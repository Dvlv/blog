Title: Front End Frameworks 3 - Vue
Date: 2020-09-12
Tags: Web Development
Category: Web

The third (and probably final) framework I'll be writing my timekeeping page in is Vue. I've worked on a project in the past
which was written in "proper" Vue (with `.vue` files and a `npm` server), but I've forgotten basically all of it by now.

Vue 3 has recently been made available, and I was also writing this page without the use of a server or bundler, so I
essentially started again from 0.

Despite this, I managed to go from no knowledge, reading tutorials to figure out how to make Vue work, to having
the page re-written in about 2 and a half hours (compared to multiple days with the previous two rewrites).

The main reason for this is the fact that Vue lets me use the HTML I had already written with just some small modifications.

## The Page

As before, this is a screenshot of the page I am rewriting. It contains a form in which the user enters information of how
they have spent their time, and a table below which shows what has been entered.

When the Date field is changed, the table underneath updates to show the time logged for that day.


<img src="{filename}/images/timekeeping.png" alt="Timekeeping system" width="98%">


## The Template Changes

Previously, the HTML for the table was in a separate template to the form, and it was rendered and injected by an
XHR request.

For the Vue rewrite, I can put the table directly below the form, and just add some `v-for` and `v-model`s to the existing
HTML.

    :::html
    <h1>Log Your Time</h1>
    
    <div id="app">

        <form name="log_time_form" class="edit-form" method="POST" @submit.prevent="onFormSubmit">

            <input type="hidden" name="user_id" value="{{user_id}}">

            <div class="form-piece">
                <label>Date</label>
                <input name="date" type="text" v-model="date" id="date_input">
            </div>

            <div class="form-piece">
                <label>IP</label>
                <select name="ip_id" v-model="ip_id">
                    {% for ip in all_ips %}
                        <option value="{{ip.ip_id}}" {{"selected" if ip_id and ip_id|int == ip.ip_id else ""}}>
                            {{ip.long_name}}
                        </option>
                    {% endfor %}
                </select>
            </div>

            <div class="form-piece">
                <label>Duration</label>
                <input type="text" name="duration" v-model="duration">
                <span class="small" style="margin-top:-20px;margin-left:23%;">e.g. 1d, 1h30m, 90m, 90</span>
            </div>

            <div class="form-piece">
                <label>Description</label>
                <input type="text" name="message" v-model="message">
            </div>

            <input type="submit" value="Save" class="btn btn-blue">

        </form>

        <div id="user_entries_area">
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
                <tbody v-if="logs.length">
                    <tr v-for="log in logs">
                        <td>[[log.ip_name]]</td>
                        <td>[[log.duration_formatted]]</td>
                        <td>[[log.message]]</td>
                        <td><i class="fa fa-trash" ></i></td>
                        <td><i class="fa fa-copy" ></i></td>
                    </tr>
                    <tr>
                        <td></td>
                        <td>[[total_duration]]</td>
                        <td></td>
                        <td></td>
                        <td></td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div> 

Looking through the elements of the form, you can see that I have added a `@submit.prevent` to run a javascript function
when the form is submitted, and I have added some `v-model`s to each input.

The IPs dropdown is still using Jinja's `{{double-brace}}` syntax to add python variables and logic to the HTML. For this to
stay as-is, I will need to change the syntax used by Vue to denote variables.

If we now look at the table, we can see a `v-if` and `v-for` which are responsible for hiding and populating the rows
in the table.

The data is placed between `[[double-square]]` brackets, as we have to differentiate Vue's replacements from Jinja's.

With the template done, we can now go over the Vue code to render this page. Due to the lack of HTML, it is much shorter
than the previous implementations.

## The Javascript

    :::typescript
    const app = Vue.createApp({
        delimiters: ['[[', ']]'],
        data() {
            return {
                date: getFormattedDate(new Date()),
                ip_id: 1,
                duration: '',
                message: '',
                total_duration: '0 Minutes',
                logs: [],
            }
        },
        mounted() {
            this.fetchLogs(this.date);

            var dateInput = document.getElementById("date_input");

            dateInput.addEventListener("change", function(e) {
                this.date = e.target.value;
                this.fetchLogs()
            }.bind(this))

            new Choices(document.querySelector('select[name="ip_id"]'), {itemSelectText: '',})
        },
        methods: {
            onFormSubmit() {
                var form = document.forms["log_time_form"];
                var formData = new FormData(form);

                fetch("/admin/logs/save", {method: "POST", body: formData})
                .then((res) => res.json())
                .then((result) => {
                    this.logs = result.data;
                    this.total_duration = result.total_duration;
                })
            },
            fetchLogs() {
                var url = "/admin/logs/xhr_fetch_user_logs";
                var date = this.date;
                if (date) {
                    url += "?date=" + date;
                }

               fetch(url)
               .then((res) => res.json())
               .then((result) => {
                    this.logs = result.data;
                    this.total_duration = result.total_duration;
                });

            }
        },
    }).mount("#app")

The first thing we do with our Vue object is set the delimeters to `[[` and `]]` as we saw in the template, to allow Vue
to coexist with jinja.

Inside the `data` function, we have a variable for each of the form inputs, one to store the total duration, and one to hold
the logs for the chosen date.

Under `mounted` we first grab the logs for display in the table, then we bind an event to the datepicker's `change` event.
This function will fetch the user's logs for the chosen date, then update the input's value to the one chosen. Finally,
`choices` is instantiated over the IP dropdown, which is already populated on the server-side by Jinja, unlike the previous
rewrites.

Two `methods` are defined - one for fetching the logs for the current date via XHR, and one which will send an XHR request
to save a new entry, then update the table with the server's response.

That's it, The page is now re-done in Vue with much less effort than my other rewrites.

## Thoughts on Vue vs Mithril and React

Compared to the other frameworks I tried, Vue was definitely the fastest to refactor into. It allowed me to continue
using the HTML I had already written.

I also like how it visibly separates the data, initialisation, and generic functions. I found this very easy to wrap
my head around. 

However, I really _don't_ like the fact that, to split a Vue project into components, the HTML has to be stored as a string
in the javascript object. This feels wrong to me, and prevents an IDE from helping you to write this HTML. I actually
find this worse than having to write it as a series of functions. 

In terms of which I would use in the future, I think if I had something already written with HTML, I would likely use Vue
so that I can keep the old templates around. For something entirely new, I think I would lean toward React - mainly for its
popularity, and the ability to split it into component objects without storing HTML as a string.
    



