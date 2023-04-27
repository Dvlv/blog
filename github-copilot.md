Title: My Experience with Githut Copilot, and Why I Cancelled The Trial
Date: 2023-03-12
Tags: Linux
Category: Linux

AI is all the rage at the moment, and my manager at work encouraged us to try out some of the new tooling which is doing the rounds; one of which being Github Copilot.

I activated the 60 day trial and installed the Neovim plugin, and have been using it for somewhere around 50 days. As the title suggests, however, I will be
cancelling the subscription before the free trial expires, as I'm not entirely happy with the product in its current form.

In this post I'll go over my main reasons, roughly in _ascending_ order of annoyance, and make a suggestion which, if possible, would make the experience immeasurably better.

## Taking over Tab

The very first thing I noticed after installing the plugin was that it was arrogant enough to bind itself to Tab by default. Almost every editor and IDE I've used has
the Tab key bound to completing the current word, meaning I'd be willing to bet almost all developers have muscle memory for typing a few letters and pressing Tab to complete
things. I certainly do.

Let's say for example I have a variable with a long name, `usersWithNoSubscription`, muscle memory would have me type `usersw` and press Tab to have the whole variable auto-completed for me. Often, Copilot would be able to predict a line in almost exactly the time I spent typing `usersw`, and so I would press Tab just as the predicted line appeared, and I would accidentally accept a whole line I hadn't even read. This got very annoying, very quickly.

Luckily it's simple enough to re-bind the complete key (with the Neovim plugin, at least) so this was addressable, but put me off to a rocky start.

## Assuming SQLAlchemy

Probably a minor gripe, but due to the popularity of SQLAlchemy in the python world, Copilot assumes all ORMs are SQLAlchemy's syntax (or possibly Django's).
My workplace uses Peewee as its ORM, and thus Copilot is worse-than-useless for generating the queries.

## Hiding What's Typed

This is another one that could just be a _me_ thing, but when writing `if` or `for` statements in a C-style language, I'll usually write out all of the punctuation first, then move the cursor back into the brackets, e.g:

```
if (|) {
}

// or

for (|) {
}
```

The pipe above represents the cursor.

When I do this, Copilot will then decide it knows what both the condition and the body are going to be, and writes it in light-grey after my cursor.

The problem with this is that it hides everything already-typed after my cursor. This means, if I _don't_ take its suggestion, I then can't see that there's already `) {` at the end of my cursor position, and tend to re-write them, causing invalid syntax when I then move down a line. If I _do_ take Copilot's suggestion, it doesn't recognise that I already put a closing brace at the end, so I will end up with two. If I'm lucky enough to only be one-nest-deep, this just gets underlined red, but if I'm nested at all it will cause me to end the previous block early, causing annoying bugs if I don't notice.

## Auto-completing Typos

Unlike a normal language-server, Copilot will auto-complete typos. Say I was intending to type `PaymentReceived` but instead my fingers made `Pamye`, Copilot would often complete this to `PamyentReceived` for me, and I would often not even notice for a few seconds. It may be personal preference, but I find it more annoying to auto-complete a word then have to go back to the middle to fix it, rather than to not complete the word and make me fix the typo before I can get completion.

I'm also pretty sure it once auto-completed `"recieved"` (entirely by itself) inside a JSON payload, causing me to have a few looks as to why the data wasn't saving to the database.


## Confidently Wrong

This seems to be most people's main criticism of ChatGPT, and it _definitely_ applies to Copilot. Copilot often throws methods or variables that just don't exist anywhere into its predictions.

This is annoying-enough on single line predictions, but Copilot will often decide it knows that goes into an `if` statement before you've even begun typing the condition, and often it will _look_ like exactly what you want when it's all in the light-grey preview, but once you accept it you'll see entire lines of red-underlining as it's comparing the result of a function that doesn't exist to an undefined variable. I'd argue this is actually worse than offering no prediction at all, as I then have to throw away everything in the `if` statement and start again.

The same is true with imports. If I'm in a project which uses pluralised table names (ewww, I know) then the correct import would be `from models.users import Users`. Copilot doesn't realise this, and will happily complete `from models.us` to `from models.user import User`. As I work on projects which do both, I don't always notice that this is incorrect, and sometimes will jump down away from the imports before I've noticed it has been underlined.

This is definitely the most annoying part of Copilot. It _looks correct_ on first glance, but is sometimes just _subtly_ wrong, and it can cost more time than would
be saved had it been correct.

## How to Improve Copilot (maybe)

Now, I'm not sure if this is even possible technically, as I've never worked on anything like this, _buuuut_ if it is, _this_ is the way to improve Copilot:

**Have it communicate with the Language Server!**

If the predictions could be sent to the Language Server for validation before appearing, the Confidently Wrong-ness of Copilot would likely reduce dramatically.

The Language Server knows that there is no file at `modules.users`, but there _is_ at `modules.user`, and Copilot could be fed this information back and change its prediction - much like how users can tell ChatGPT it made a mistake, and it can correct itself in response.

The Language Server could also stop it from producing methods and functions that don't exist, and possibly suggest close alternatives, such as when it would try to complete `get_user_by_id` when the actual method is `get_user_by_account_id`, the LS could nudge it toward the latter. 

Again, I've got no idea if there's even a way for this to be done, or even if it _was_ possible whether it would be fast enough. Until _something_ like this is implemented, however, I just feel like Copilot is _at best_ a net-neutral, it costs me as much, if not _more_, time as it saves me.
