:Title: Front End Frameworks 1 - Mithril
:Date: 2020-08-29
:Tags: Web Development
:Category: Web

Despite my general dislike for the overcomplicated front-end frameworks, I have found myself on about 3 occasions now thinking they could possibly be an easier
way of tackling a particular problem.

During a small window of not-much-going-on at work, I decided to finally give some of them a try. After much online research, the one I found which caught
my eye the most was `mithriljs <https://mithril.js.org/>`_.

The best part about Mithril is that there is, by default, no build step, and no need for running ANOTHER web server, something which really annoyed
me about the popular three (although I've now found out, at least React and Vue can also be run without a server).

I decided to take a page in a recent project of mine - a timekeeping system - and rewrite this page using Mithril. The reason for choosing this page in
particular was because it currently operates by making some XHR requests to a flask endpoint, then using ``innerHTML`` to inject the result, which isn't very
graceful.

This is just an internal tool, which I doubt we would ever be able to offer as a service, so I feel okay sharing small bits of the code here.

The Page
========

The page is a fairly simple one, at the top half there is a form where a user chooses a date, an IP (which in a normal company would be a "project"), an amount of time,
and writes a small description of what they worked on.

Once they submit this form, a small table underneath the form updates with a row containing the information they just submitted.

.. image:: {filename}/images/timekeeping.png
    :width: 98 %
    :align: center
    :alt: timekeeping system

Currently, the table is rendered by an XHR request which renders a Jinja template, and this is injected with ``innerHTML(res.responseText)``.

The Old Way
===========

The current way this page works is by rendering templates with Jinja, as per a normal flask application.

The template rendered contains the form and a div with a particular ID, which is where the table is injected. 


.. code-block:: django

    {% extends "base.html" %}
    {% block main %}

        <h1>Log Your Time</h1>

        <form name="log_time_form" class="edit-form" method="POST" action="{{url_for('admin.save_log')}}">

            <input type="hidden" name="user_id" value="{{user_id}}">

            <div class="form-piece">
                <label>Date</label>
                <input name="date" type="text" value="{{date if date else today}}">
            </div>

            <div class="form-piece">
                <label>IP</label>
                <select name="ip_id">
                    {% for ip in all_ips %}
                        <option value="{{ip.ip_id}}" {{"selected" if ip_id and ip_id|int == ip.ip_id else ""}}>
                            {{ip.long_name}}
                        </option>
                    {% endfor %}
                </select>
            </div>

            <div class="form-piece">
                <label>Duration</label>
                <input type="text" name="duration">
                <span class="small" style="margin-top:-20px;margin-left:23%;">e.g. 1d, 1h30m, 90m, 90</span>
            </div>

            <div class="form-piece">
                <label>Description</label>
                <input type="text" name="message">
            </div>

            <input type="submit" value="Save" class="btn btn-blue">

        </form>

        <div id="user_entries_area"></div>

    {% endblock %}

    {% block js %}
        <script src="{{url_for('static', filename="js/common.js")}}"></script>
        <script src="{{url_for('static', filename="js/logs/log_time_form.js")}}"></script>
        <script>

            window.addEventListener('DOMContentLoaded', function() {

               // do some JS stuff here
            });

        </script>
    {% endblock %}


I've cut out some bits for brevity, but as you can see it's a fairly normal Jinja template. The form is built with HTML, then there's a
div with the id ``user_entries_area`` which is where the table will be injected.

Speaking of the table:

.. code-block:: jinja

    <table class="time-log-table">
        <thead>
            <tr>
                <td>IP</td>
                <td>Time</td>
                <td>Message</td>
                <td>Delete</td>
                <td>Clone</td>
            </tr>
        </thead>
        <tbody>
            {% for log in logs %}
                <tr>
                    <td>{{log["ip_name"]}}</td>
                    <td>{{log["duration_formatted"]}}</td>
                    <td>{{log["message"]}}</td>
                    <td><i class="fa fa-trash" onclick='deleteLog({{log["log_id"]}});'></i></td>
                    <td><i class="fa fa-copy" onclick='copyLog({{log["ip_id"]}}, "{{log['ip_name']}}", {{log["duration"]}}, "{{log['message']}}");'></i></td>
                </tr>
            {% endfor %}
            <tr>
                <td></td>
                <td>{{total_duration}}</td>
                <td></td>
                <td></td>
                <td></td>
            </tr>
        </tbody>
    </table> 


This table contains a row per entry logged, and displays the information entered, along with some buttons for deleting or cloning an entry (which we will ignore in this post).

The View
--------

.. code-block:: python

   @admin.route("/logs/xhr_fetch_user_logs")
      def xhr_fetch_user_logs():
        """
        Fetches the user's logs for display in the table
        underneath the log time form.
        """
        data = request.args

        if "user_id" not in session:
            return {"error": "user not logged in"}, 500
        if "date" not in data:
            return {"error": "no date supplied"}, 500

        logs = TimeLog.get_user_logs_for_date(session["user_id"], data["date"])

        total_duration = sum(l["duration"] for l in logs)
        total_duration = TimeLog.format_duration(total_duration)

        return render_template(
            "logs/partial_log_table.html", logs=logs, total_duration=total_duration
        )


This view is what renders the above template. Hopefully nothing really needs explaining, the query and formatting logic are taken care of by the ``TimeLog`` model class.


The Mithril Way
===============

To do this the Mithril way, we first need to change that view to return JSON instead of rendering a template. Not a lot needs to change here, we just need
the TimeLog records as dictionaries instead of iterables, since they won't be consumed by a template anymore.

.. code-block:: python

    @admin.route("/logs/xhr_fetch_user_logs")
        def xhr_fetch_user_logs():
            """
            Fetches the user's logs for display in the table
            underneath the log time form.
            """
            # ... SNIP ...

            d_logs = [l for l in logs]

            return jsonify({"data": d_logs, "total_duration": total_duration})

With that taken care of, we can now start writing some Mithril. I'll be using typescript, but the javascript version will be almost the same, just without
type hints.

First off, we need an object which handles keeping a hold of the user's log entries.

.. code-block:: typescript

   var TimeLog = {
        records: [],
        totalDuration: 0,
        loadRecords: function(p_date?: string) {
            return m.request({
                method: "GET",
                url: "/admin/logs/xhr_fetch_user_logs",
                data: {"date": p_date},
            }).then(function (result: {}) {
                TimeLog.records = result.data;
                TimeLog.totalDuration = result.total_duration;
            })
        }
    }

A fairly simple object which has fields for the records and total, and a ``loadRecords`` function for grabbing them with an XHR request to our modified endpoint.

Now we can translate the Jinja partial template into Mithril's format and use Mithril to inject into the same ``user_entries_area`` div.

.. code-block:: typescript

    var LogList = {
            oninit: function() {
                TimeLog.loadRecords();
            },
            view: function() {
                return m("table.time-log-table", [
                    m("thead", [
                        m("tr", [
                            m("td", "IP",),
                            m("td", "Time",),
                            m("td", "Message",),
                            m("td", "Delete",),
                            m("td", "Clone",),
                        ])
                    ]),
                    m("tbody", [
                        TimeLog.records.map(function (log: {}) {
                            return m("tr", [
                                m("td", log.ip_name),
                                m("td", log.duration_formatted),
                                m("td", log.message),
                                m("td", m(`i.fa.fa-trash`, {
                                            onclick: function() {
                                                deleteLog(log.log_id)
                                            }
                                          }
                                        )
                                 ),
                                m("td", m(`i.fa.fa-copy`, {
                                            onclick: function() {
                                                  copyLog(log.ip_id, log.duration, log.message);
                                            }
                                          }
                                        )
                                ),
                            ])
                        }),
                        m("tr", [
                            m("td"),
                            m("td", TimeLog.totalDuration),
                            m("td"),
                            m("td"),
                            m("td"),
                        ])
                    ])
                ])
            }
        }

We create an object called ``LogList`` to be our table. When initialised, it tells the ``TimeLog`` object to fetch its records via XHR, then uses ``map`` to iterate over
them and create a new table row for each, filling in the cell with information from that record. Once it's done looping, it adds the final row with the total duration.

Now to use Mithril to render the table, it's as easy as adding ``m.mount(document.getElementById('user_entries_area'), LogList);``. 

Replacing the form
------------------

With proof-of-concept in place that we can render data from XHR requests into HTML, it's time to go all-in and replace the whole page.

We'll start by translating the main template's form HTML to Mithril, then replacing the form with just another div.

.. code-block:: typescript

     var LogForm = {
            view: function() {
                return m("form.edit-form", {name: "log_time_form"}, [
                    m(".form-piece", [
                        m("label", "Date"),
                        m("input", {name: "date", id: "date_input", type: "text"}),
                    ]),

                    m(".form-piece", [
                        m("label", "Ip"),
                        m(IpDropdown),
                    ]),

                    m(".form-piece", [
                        m("label", "Duration"),
                        m("input", {name: "duration"}),
                    ]),

                    m(".form-piece", [
                        m("label", "Description"),
                        m("input", {name: "message"}),
                    ]),

                    m("input.btn.btn-blue", {value: "Save", type: "submit"})
                ])
            }
        }


The form is now in Mithril syntax, but the IP dropdown was giving me problems (mainly due to use of ``choices.js`` to make it fancy). I made this into its own component
to get choices to behave.

.. code-block:: typescript

    var IpDropdown = {
            choices: undefined,
            ips: [],
            oncreate: function() {
                return m.request({
                    method: "GET",
                    url: "/admin/logs/xhr_get_ips",
                }).then(function (result: {}) {
                    IpDropdown.ips = result.data;
                })
            },
            onupdate: function() {
                var dd = document.getElementById("ip_id")

                if (dd.children.length > 0 && !IpDropdown.choices) {
                    IpDropdown.choices = new Choices(document.querySelector('select[name="ip_id"]'), {itemSelectText: '',})
                }
            },
            view: function() {
                return m("select", {name: "ip_id", id: "ip_id"}, IpDropdown.ips.map(function (ip) {
                    return m("option", {value: ip.ip_id}, ip.long_name)
                }));
            }
        }

The IP Dropdown fetches the IPs (projects) from a simple XHR request which returns them all as json, and uses ``onupdate`` to ensure that ``choices.js`` only initialises
when the IPs have loaded, otherwise it will not work.

Now the form is rewritten I can cut down the template.

.. code-block:: jinja

    {% extends "base.html" %}
    {% block main %}

        <h1>Log Your Time</h1>

        <div id="form-target"> </div>

    {% endblock %}

    {% block js %}
        <script src="{{url_for('static', filename='js/choices.min.js')}}"></script>
        <script src="{{url_for('static', filename="js/pikday.js")}}"></script>
        <script src="{{url_for('static', filename="js/common.js")}}"></script>
        <script src="{{url_for('static', filename='js/mithril.min.js')}}"></script>
        <script src="{{url_for('static', filename="js/logs/log_time_form.js")}}"></script>
        </script>
    {% endblock %}

Since the form is no longer actually "submitting", but instead calling some javascript function, I had to wrap everything up in a new component and use a hook to
bind the submit event of the form.

.. code-block:: typescript

     var LogPage = {
        oncreate: onPageLoad,
        view: function() {
            return m("wrapper", [m(LogForm), m(LogList)])
        }
     }

    function onPageLoad() {
        var todayDate = getFormattedDate(new Date());
        var dateInput = document.getElementById("date_input");
        dateInput.value = todayDate;

        dateInput.addEventListener("change", function () {
            TimeLog.loadRecords(this.value);
        });

        var form = document.forms["log_time_form"];
        form.addEventListener("submit", function(event) {
            event.preventDefault();
            var formData = new FormData(this);
            m.request({
                url: "/admin/logs/save",
                method: "POST",
                data: formData,
            }).then(function(result) {
                TimeLog.records = result.data;
                TimeLog.totalDuration = result.total_duration;
            })
        })

    };

    m.mount(document.getElementById("form-target"), LogPage)


And with that, the rewrite is complete! When the user changes the date, or adds a new log entry, the table underneath the form will update. 

Overall, I would say I have grown to like Mithril quite a bit. At first I was put off by their template syntax, and I thought it would make everything difficult to read,
but actually it's not as bad as I thought. Obviously using JSX would be nicer, but I didn't want the hassle of installing some 20GB packager/builder from ``npm`` just
to get a page to render (e.g. Parcel or Webpack).

This rewrite has sparked a little bit of an interest in frontend frameworks, and I shall use this particular page to explore others in the future.
