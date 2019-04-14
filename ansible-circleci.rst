:Title: How we use Circleci and Ansible to Automate Deploying Flask Applications
:Date: 2018-07-18
:Category: Python
:Tags: Python, Testing, Web
:Summary: At my workplace we like to use CI/CD to automatically deploy any changes to the master branch into production. This involves two main pieces of technology - Circleci and Ansible.
    <br><br> In this post I will be going over how we use these two pieces of technology to deploy a flask application.

At my workplace we like to use CI/CD to automatically deploy any changes to the master branch into production.

This involves two main pieces of technology - Circleci and Ansible.

My preferred stack for web development is:

- Flask
- Postgres
- Uwsgi
- Nginx
- Supervisor

In this post I'll provide an example of how we implement this for a python application which uses the above technologies.

Our Deployment and Rollback Process
-----------------------------------

We deploy our applications by copying over the contents of the github repository via rsync. We first set up a parent folder somewhere on the system, such as ``/var/www/project_name``. Inside this folder we maintain two more folders: ``builds`` and ``current``. Our nginx is then pointed to ``/var/www/project_name/current``. However, ``current`` is not actually a folder, it is a symlink to the latest build inside our ``builds`` folder.

This allows us to very quickly undo a deployment by just updating the ``current`` symlink to an older build and restarting our web server.

All of this is done automatically using Ansible.

Ansible
-------

The first thing to do is to set up ansible. We typically create a folder named ``ansible`` inside the project's repo. We can then begin with the ``hosts`` file, which holds information about our live server.

ansible/hosts
.............

.. code-block:: yaml

    [web]
    our.url.com

    [web:vars]
    ansible_ssh_private_key_file=~/.ssh/id_rsa
    ansible_user=circleci


With this information set up, we now need to define some variables which will be used throughout our playbook. The ``/path/to/project`` bits will be replaced with the file path at which we will store the repository on our live server.


ansible/group_vars/all
......................

.. code-block:: yaml

    ---

    project_root: "/path/to/project"
    project_source: "{{ playbook_dir }}/.."
    builds_dir: "/path/to/project/builds"
    current_release_dir: "/path/to/project/current"
    excludes_file: "{{ playbook_dir }}/rsync_excludes"
    keep_releases: 5


With the variables taken care of, we now need a playbook to execute.

ansible/deployment.yml
.......................


.. code-block:: yaml

    ---

    - name: "Deploy to production"
      any_errors_fatal: true
      hosts: web
      roles:
        - { role: deploy, become: yes }


Our playbook calls one role. This role will look as follows (large file ahead):


ansible/roles/deploy/tasks/main.yml
....................................

.. code-block:: yaml

    ---

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
      command: python3.6 -m venv {{ build_dir }}/env

    - name: "Pip wheel requirements"
      command: python3.6 -m pip wheel --wheel-dir={{ project_root }}/wheels -r {{ build_dir }}/requirements.txt
      when: req_diff.rc is not defined or (req_diff.rc is defined and req_diff.rc == 1)

    - name: "Install requirements on new build virtualenv"
      pip:
        requirements: "{{ build_dir }}/requirements.txt"
        virtualenv: "{{ build_dir }}/env"
        extra_args: "--use-wheel --no-index --find-links={{project_root}}/wheels"

    - name: "Update current release symlink"
      file:
        state: link
        force: yes
        path: "{{ current_release_dir }}"
        src: "{{ build_dir }}"

    - name: "Reload UWSGI"
      supervisorctl:
        name: "uwsgi"
        state: restarted

    - name: "Cleanup old releases"
      shell: ls -1dt {{ builds_dir }}/* | tail -n +{{ keep_releases | int + 2 }} | xargs rm -rf
      when: keep_releases > 0

That's a lot to take in. Let's go over it step by step.

- **Register build time** - Gets a string representing the current date and time. This is used as the folder name. (More on this later).

- **Create build directory** - Creates a folder under ``builds`` named after our build time.

- **Rsync content to build directory** - self explanitory.

- **Create checksums from requirements.txt files** - If we have an old release, we grab its ``requirements.txt`` file and generate an md5 hash. We then do the same for our current ``requirements.txt``. This allows us to check if our requirements have changed since the last deploy.

- **Check for checksum changes** - Check if the two md5 hashes are different. This is then stored in ``req_diff``.

- **Create build virtualenv** - Self explanitory

- **Pip wheel requirements** - If our checksums differ, our requirements have updated. We need to download them as wheel files into our ``wheels`` directory.

- **Install requirements on new build virtualenv** - Installs everything in our wheels directory into our virtualenv.

- **Update current release symlink** - Symlinks the ``current`` folder in the project root to point to our latest build folder.

- **Reload Uwsgi** - Calls upon supervisor to restart our uwsgi process.

- **Cleanup old releases** - Deletes old copies of the repo from the ``builds`` directory, leaving us with only the most recent 5.


Circleci
--------

Now that we have a playbook which will deploy our website to its live server, we need to use Circleci to make this happen automatically whenever someone pushes to the master branch on Github.

After telling Circleci about our project, we add the following file to the ``.circleci`` folder inside our repo.


.circleci/config.yml
.....................

.. code-block:: yaml

    version: 2
    jobs:
    build:
        docker:
          - image: circleci/python:3.6.2
          - image: circleci/postgres:9.6.2
            environment:
              POSTGRES_USER: <user>
              POSTGRES_DB: <db>
              PGPASSWORD: <pw>
        environment:
          - ANSIBLE_HOST_KEY_CHECKING: False
          - ANSIBLE_LOCAL_TEMP: /home/circleci/.ansible/tmp
          - ANSIBLE_REMOTE_TEMP: /home/circleci/.ansible/tmp
        steps:
          - checkout
        - restore_cache:
            keys:
            - deps-{{ checksum "requirements.txt" }}
        - run:
            name: Install Dependencies
            command: |
                python3 -m venv env
                . env/bin/activate
                pip install ansible
                pip install -r requirements.txt
                sudo apt update
                sudo apt install rsync
        - save_cache:
            key: deps-{{ checksum "requirements.txt" }}
            paths:
                - "env"
        - run:
            name: Run Tests
            command: |
                . env/bin/activate
                export PYTHONPATH=.
                export TEST_MODE=1
                pytest -vs
        - store_artifacts:
            path: test-reports
            destination: test-reports

        - run:
          name: Deploy to production
          command: |
            . env/bin/activate
            ansible-playbook -i ansible/hosts ansible/deployment.yml
    
    workflows:
      version: 2
      build:
        jobs:
          - build:
              filters:
                branches:
                  only: master


This configuration will cause Circleci to spawn two containers - one for python and one for postgres. We set some enviroment variables to allow our postgres database to function, and let ansible play nicely with circleci.

We leverage the ability to store our ``env`` folder against the checksum of ``requirements.txt``, preventing the need to reinstall all of our external packages on each deploy (unless the requirements are updated).

After installing dependencies, we run our unit tests via ``pytest`` then call our ansible playbook to deploy.


Rolling Back
------------

If a deployment passes all of its unit tests but is somehow catastrophically broken, we can use Ansible to roll back to a previous release.


ansible/rollback.yml
....................

.. code-block:: yaml

  - name: "Revert the build"
    any_errors_fatal: true
    hosts: web
    roles:
      - { role: revert_web_build, become: true }

Another playbook which calls a single role.

ansible/roles/revert_web_build/tasks/main.yml
.............................................

.. code-block:: yaml

    ---

  - name: "Determine penultimate build"
    shell: ls -d /path/to/project/builds/* | tail -n +{{ keep_releases }} | head -1
    register: penultimate_build

  - name: "Update current release symlink"
    file:
        state: link
        force: yes
        path: "{{ current_web_release_dir }}"
        src: "{{ penultimate_build.stdout }}"

  - name: "Reload supervisord"
    service:
        name: "supervisord"
        state: "reloaded"

  - name: "Restart uwsgi"
    command: "supervisorctl restart uwsgi"


This role finds the previous build in our ``builds`` folder then updates the ``current`` symlink to point at it. It finishes off by restarting uwsgi to update the web server.

Now if a panic ensues, a developer simply needs to run ``ansible-playbook -i ansible/hosts ansible/rollback.yml`` and a deployment will be reversed while a fix is worked on.
