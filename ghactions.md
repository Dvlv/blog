Title: How we use Github Actions and Ansible to automate Flask / Postgres applications
Date: 2021-01-23
Tags: Web Development
Category: Web

I've previously written about how we use CircleCI and Ansible for continuous integration and delivery (CI/CD), and now we have a couple of projects using Github Actions instead of CircleCI, so I thought I'd write another post detailing how one of these projects handles its CI/CD.

## The Github Actions file

Our github action needs to do the following:

* Checkout the Repo
* Initialise Postgres
* Install our dependencies
* Run the unit tests
* Call Ansible to deploy to live if the unit tests pass

Here is the yaml file in our `.github` folder:

    :::yaml
    name: Deploy

    on:
      push:
        paths-ignore:
          - "notes.md"
        branches:
          - master

    jobs:
      build:
        runs-on: ubuntu-latest
        services:
          postgres:
            image: postgres:10.8
            env:
              POSTGRES_USER: test
              POSTGRES_PASSWORD: test
              POSTGRES_DB: test
            ports:
              - 5432:5432
            # needed because the postgres container does not provide a healthcheck
            options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5

        env:
          ANSIBLE_HOST_KEY_CHECKING: False
        steps:
          - uses: actions/checkout@v1
          - name: Setup Python
            uses: actions/setup-python@v2
            with:
              python-version: 3.9
          - name: Cache
            uses: actions/cache@v1
            id: cache
            with:
              path: ./env
              key: ${{ runner.os }}-env-v2-${{ hashFiles('**/requirements.txt') }}
          - name: Install Dependencies
            if: steps.cache.outputs.cache-hit != 'true'
            run: |
              python3 -m venv env
              source env/bin/activate
              python3 -m pip install --upgrade pip
              python3 -m pip install -r requirements.txt
          - name: Run Tests
            run: |
              source env/bin/activate
              pytest -sx -n 2
          - name: Output SSH key to file
            run: |
              mkdir ~/.ssh
              echo "${{secrets.SSH_KEY}}" > ~/.ssh/id_rsa
              chmod 600 ~/.ssh/id_rsa
          - name: Deploy via ansible
            working-directory: ./ansible
            run: ansible-playbook web_deploy.yml -i hosts_github


First we specify that we need a Postgres container to go along with our build. We use environment variables to set up the database which will be used to run our unit tests.

Then onto our main container, we set another environment variable for Ansible which allows it to run uninterrupted. 

For the steps, we start off with `checkout@v1`, which simply adds the code from our github repo to this container. 

Once our repo is over, we want to install python 3.9, so we use the `setup-python` action.

To prevent having to wait for our dependencies to install on every single run, we use a `cache` action to save a copy of the `env` folder (our virtualenv) against a hash of our `requirements.txt` file. This way, dependencies are pulled from disk on most runs, and only re-downloaded when we change our requirements.

The next step is to install our requirements into the env folder if we did not hit our cache. If we did, this is skipped.

Now that we have our dependencies, we can run `pytest` to launch our unit tests. If any tests fail, the deploy process stops here, and Github will mark the commit with a red cross to indicate failure.

If our tests all passes, we then copy an SSH key onto the container so that we can ssh safely into our live server with ansible.

Finally, `ansible-playbook` calls our deployment instructions, which copies our code onto the live server and restarts necessary processes.

Speaking of which, let's take a look at that file now

## Ansible

 
    :::yaml
    - name: "Register Build Time"
      command: date +%Y%m%d%H%M%S
      run_once: true
      register: build_time

    - set_fact:
        build_dir: "{{ builds_dir }}/{{build_time.stdout}}"

    - name: "Create build directory"
      file:
        path: "{{ build_dir }}"
        state: directory
        mode: 0755

    - name: "Rsync repo content to build directory"
      synchronize:
        src: "{{ project_source }}"
        dest: "{{ build_dir }}"
        rsync_opts:
          - "--exclude-from={{ excludes_file }}"

    - stat: path={{ current_release_dir }}
      register: link

    - debug: msg="Current release symlink exists"
      when: link.stat.islnk is defined and link.stat.islnk

    - name: "Create checksum from current requirements.txt file"
      shell: md5sum {{ current_release_dir }}/requirements.txt | awk '{print $1}' > {{ project_root }}/old_requirements.checksum
      when: link.stat.islnk is defined and link.stat.islnk

    - name: "Create checksum from new build requirements.txt file"
      shell: md5sum {{ build_dir }}/requirements.txt | awk '{print $1}' > {{ project_root }}/new_requirements.checksum
      when: link.stat.islnk is defined and link.stat.islnk

    - name: "Check for checksum changes"
      shell: diff {{ project_root }}/old_requirements.checksum {{ project_root }}/new_requirements.checksum
      register: req_diff
      when: link.stat.islnk is defined and link.stat.islnk
      ignore_errors: yes

    - debug: msg="Changes detected"
      when: req_diff.rc is defined and req_diff.rc == 1

    - name: "Create build virtualenv"
      command: /home/uwsgi/.pyenv/versions/{{ python_version }}/bin/python -m venv {{ build_dir }}/env

    - name: "Pip wheel requirements"
      command: /home/uwsgi/.pyenv/versions/{{ python_version }}/bin/python -m pip wheel --wheel-dir={{ project_root }}/wheels -r {{ build_dir }}/requirements.txt
      when: req_diff.rc is not defined or (req_diff.rc is defined and req_diff.rc == 1)

    - name: "Install requirements on new build virtualenv"
      pip:
        requirements: "{{ build_dir }}/requirements.txt"
        virtualenv: "{{ build_dir }}/env"
        extra_args: "--no-index --find-links={{project_root}}/wheels"

    - name: "Decrypt vaulted files"
      command: /home/uwsgi/.pyenv/versions/{{ python_version }}/bin/ansible-vault decrypt --vault-id {{ project_root }}/ansible_pass.txt {{ build_dir }}/config.ini

    # Full path had to be specified here. Ansible does not seem to load up PATH by default.
    - name: "Apply migrations"
      command: /usr/local/bin/flyway migrate
      when: inventory_hostname in groups['migrations']
      args:
        chdir: "{{ build_dir }}"

    - name: "Update current release symlink"
      file:
        state: link
        force: yes
        path: "{{ current_release_dir }}"
        src: "{{ build_dir }}"

    - name: "Reload celery worker"
      when: inventory_hostname in groups['web']
      command: /usr/local/bin/supervisorctl restart celery

    - name: "Restart celery beat"
      when: inventory_hostname in groups['proc']
      command: /usr/local/bin/supervisorctl restart celery_beat

    - name: "(Gracefully) Reload UWSGI"
      file:
        path: "{{ project_root }}/uwsgi.pid"
        state: touch

    - name: "Cleanup old releases"
      shell: ls -1dt {{ builds_dir }}/* | tail -n +{{ keep_releases | int + 2 }} | xargs rm -rf
      when: keep_releases > 0


We begin by creating a new folder in the project root's build directiory named after the current timestamp. This is to allow us to quickly revert to an older build if a particular deployment breaks badly.

Next we `rsync` the content of the github repo into this directory to act as the new codebase.

Once this has happened, check for the presense of a symlink at `/project/path/current`, as mentioned earlier.

The next 7 sections detect whether or not our `requirements.txt` file has changed in this push. If it has we must rebuild our dependencies as wheel files in the `wheels` folder. Otherwise, we can just install the wheel files already there.

With dependencies taken care of, we next use `ansible-vault` to decrypt any encrypted files in the repo using a password stored on the web server.

We then use `flyway` to run database migrations against `.sql` files which live in the repo. 

With code and database taken care of, all that's left to do is restart things. We update `current` to symlink to the new folder, then restart our services which are running via `supervisor`.

The final step is to delete older builds so that the server doesn't fill up. The amount of old builds to keep varies by project, so we use a variable `keep_releases` to hold it.

And that's all there is to it! For more detail about some of our processes, check out the older post about [CircleCI here.](https://www.dvlv.co.uk/how-we-use-circleci-and-ansible-to-automate-deploying-flask-applications.html)
