Title: Front End Frameworks 2 - React
Date: 2020-09-05
Tags: Web Development
Category: Web

The second framework I decided to rewrite my timekeeping page in was React. It seems to be the most popular framework at the present time, so I would expect it to
have a lot of support from documentation, stackoverflow questions, and the like.

To my disappointment, it seems like it has recently gone through a redesign, and it turns out a lot of what I learned initially became effectively useless.

## Classes vs Functions

If you search youtube for tutorials on react right now, a lot of what you will find is going to teach you to make components by subclassing `React.Component`.

Inside your subclass of `Component` you have access to various hooks, such as `componentDidMount`, which is called when the component is first added to the DOM.

I got about half way through my rewrite, when I began to struggle with how data sharing is supposed to work (I think because I was expecting it to be as easy
as in Mithril) but made a last-ditch attempt to solve my problem by viewing a course over on [frontendmasters](https://frontendmasters.com/), which I had watched
before but didn't remember much of.

Upon viewing this course, I realised that they were actually using functions to make components. After a chat with a colleague about the differences between the
two ways, I started my rewrite again using the function components, and after a lesson on what `useEffect` is all about, I managed to finish the rewrite quite
easily.


## The Rewrite

As a quick reminder, here's an image of the page I am rewriting. There is a form to enter details of your timekeeping, and underneath a table which displays all
of your entries for the selected day.

<img src="{filename}/images/timekeeping.png" alt="Timekeeping system" width="98%">

The backend changes are the same as for the Mithril rewrite, I simply adjusted the old API endpoints to return JSON instead of rendering a separate template.
In this post I will just cover the new frontend, which is all contained in a single typescript file.

As said previously, I don't want 20 gigabytes of nonsense like `Parcel` or `Webpack`, so this rewrite will not include things like `JSX` which necessitate a build step.


### The Table

We'll start with the table, as it needs to exist inside of the form component to get its data. Unlike Mithril, React keeps tighter control over what data
is accessible to components, so no more global `TimeLog` object.

    :::typescript
    const r = React.createElement;
    const ipChoices = undefined;
    
    const LogList = ({logs, total}) => {
        return r("table", {className: "time-log-table"}, [
            r("thead", null, [
                r("tr", null, [
                    r("td", null, "IP"),
                    r("td", null, "Time"),
                    r("td", null, "Message"),
                    r("td", null, "Delete"),
                    r("td", null, "Clone"),
                ]),
            ]),
            r("tbody", null, [
                logs.map((log) => (
                    r("tr",{},[
                        r("td", {}, log.ip_name),
                        r("td", {}, log.duration_formatted),
                        r("td", {}, log.message),
                        r("td", {}, r("i", {className: "fa fa-trash"})),
                        r("td", {}, r("i", {className: "fa fa-copy"})),
                    ])
                )),
               r("tr", null, [
                    r("td"),
                    r("td", {"id": "total"}, total),
                    r("td"),
                    r("td"),
                    r("td"),
                ])
            ])
        ])

    }


Starting off, I assign `React.createElement` to a variable `r` to make each line much shorter. I could have assigned it to `m`, which would then let me copy-paste
a large amount of the template code from the Mithril branch, but I decided to to.

The `LogList` component needs a reference to the user's logs for the chosen day, as well as a string of the total amount of time they have logged. Since there are only
two things it really needs, I have chosen to unpack the usual `props` argument.

Otherwise, this should hopefully be fairly self-explanitory. Just a table with some headings, a `map` function to display information about each provided `log`, and a
final row for showing the total.

### The Form

The form component is much longer, and is responsible for passing the correct ``logs`` to the table based on the date selected.

    :::typescript
    function loadUserLogs(dt?: string) {
        var url = "/admin/logs/xhr_fetch_user_logs?";
        if (dt) {
            url += "date=" + dt
        }
        return fetch(url).then(res => res.json())
    }

    const LogForm = () => {
        const [dt, setDt] = React.useState(getFormattedDate(new Date()));
        const [logs, setLogs] = React.useState([]);
        const [total, setTotal] = React.useState(0);

        React.useEffect(() => {
            loadUserLogs()
            .then((result) => {
                setLogs(result.data)
                setTotal(result.total_duration)
            })

            document.getElementById("date_input").addEventListener("change", changeDate)

            var form = document.forms["log_time_form"];
            form.addEventListener("submit", function(e) { onFormSubmit.bind(this, e)()});
        }, []);

        const onDateChange = (e) => {
            setDt(e.target.value);

            loadUserLogs(e.target.value)
            .then((result) => {
                setLogs(result.data)
                setTotal(result.total_duration)
            });
        }

        const onFormSubmit = function(e) {
            e.preventDefault();
            var formData = new FormData(this);

            fetch("/admin/logs/save", {
                method: "POST",
                body: formData,
            })
            .then((res) => res.json())
            .then((result) => {
                setLogs(result.data);
                setTotal(result.total_duration);
            })
        }

        return r("div", {}, [
            r("form", {className: "edit-form", name: "log_time_form"}, 
               r("div", {className: "form-piece"},
                   r("label", {}, "Date"),
                   r("input", {type: "text", id: "date_input", name: "date", value: dt})
               ),

               r("div", {className: "form-piece"},
                   r("label", {}, "IP"),
                   r(IpDropdown)
               ),

               r("div", {className: "form-piece"},
                   r("label", {}, "Duration"),
                   r("input", {type: "text", id: "duration", name: "duration"})
               ),

               r("div", {className: "form-piece"},
                   r("label", {}, "Message"),
                   r("input", {type: "text", id: "message", name: "message"})
               ),

               r("input", {className: "btn btn-blue", value: "Save", type: "submit"})
            ),
            logs.length ?
            r("div", {id: "user_entries_area"}, r(LogList, {logs: logs, total: total}))
            : undefined
        ])
    }


The `LogForm` component needs to keep track of three things - the selected date, the user's logs for that date, and the total duration logged. The latter two are to be passed
to the table, and the former is just used to fetch the others.

To hold these, we use the `useState` function so that we can let the page re-render every time one of them changes.

A `useEffect` with an empty array as the second argument is used so that we can call a function when the component first mounts (this would be `componentDidMount` in
a class-based component). The frist thing the function does is load the user's logs for the current date, since this is the default date displayed when the page
first loads. These logs are what will initially be shown in the table.

The function is also responsible for binding an event to the Date input changing, as the datepicker library I use would overwrite anything
passed as an `onClick` property. The form submitting is also bound to a custom function.

The `onDateChange` function first uses `setDt` to update the date input with the new chosen value, then fires this date off to the API to get the user's logs for the chosen date,
which are then set using the `setLogs` and `setTotal` state functions, which causes a re-render of the table below with the new data.

`onFormSubmit` is responsible for posting the form data to the API, then updating the logs with the newly returned data (which will add the newly-logged entry to the table).

Below all of these functions is the HTML returned to display the form. Underneath the form's markup is a ternary which will render the table (`LogList`) if there are any
logs in the current state.

You may notice that, like before, the React version of this form also contains a separate component for the IP (Project) dropdown.

### The IP Dropdown

This dropdown exists as a separate component so that it can fetch the IPs from the API on creation, then instantiate `choices.js` over itself once they are loaded.

    :::typescript
    const IpDropdown = () => {
        const [ips, setIps] = React.useState([]);

        React.useEffect(() => {
            fetch("/admin/logs/xhr_get_ips")
            .then(res=>res.json())
            .then((result) => {
                setIps(result.data);
                ipChoices = new Choices(document.getElementById("ip_id"), {itemSelectText: '',})
            })
        }, []);

        return r("select", {name: "ip_id", id: "ip_id"},
            ips.map((ip) => (
                r("option", {value: ip.ip_id, key: ip.ip_id}, ip.long_name)
            ))
        )
    }

With all of that done, we just need to use `ReactDOM` to render it. There's no need for a parent component this time, as the form already contains the table.

    :::typescript
    ReactDOM.render(r(LogForm), document.getElementById("form_area"));


## Thoughts on React vs Mithril

In the beginning, I found Mithril much simpler to use compared to the Class-based version of React. However, once I switched to the function components and learned about
`useState` and `useEffect` it seemed to make a lot more sense, and I think I clicked with React a bit more.

The inconsistency with documentation is not great, and it is very hard to find answers to questions which don't assume I am using `JSX`, but overall the react experience
has been "good enough". Due to its immense popularity, I think I am probably more likely to undertake a serious project in React than in Mithril.


