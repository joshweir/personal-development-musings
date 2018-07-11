# Express Notes

`app.use` calls middleware, middleware is function that takes `req`, `res`, `next`, function must call `next()` when done (or not if middleware pipeline should be halted), can modify the request (`req`) or response (`res`). 

`views/` has the view templates, a view can be rendered through `res.render('templatename', { js: passedVariables })`.

View template brief: 

```
h1= title //this means render a <h1> and it's content should be based on the title js variable passed.
h1 Show this content: #{title} //render <h1> it's content should be the string including title variable rendered.
block content is placeholder for the content to yield, so additional pages can "extend" a view, inserting into the "block content":

//myview.jade
extends layout //layout is a view that has "block content" embedded

block content
	h2 This is h2 tag
```
