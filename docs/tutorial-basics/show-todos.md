---
sidebar_position: 2
---

# Show todos

The way Appish bots render the UI in tiles is heavily inspired by [moustache templating](http://mustache.github.io/mustache.5.html).

## A quick intro to Moustache

Moustache is usually used in web development to render HTML according to a javascript object literal.

A typical Mustache **template**:

```html
<h1>Hello {{name}}</h1>

{{^todos}}
<p>No todos (yayy)</p>
{{/todos}}

<ul>
{{#todos}}
<li>{{title}}: {{description}}</li>
{{/todos}}
</ul>
```

Given the following **hash**:

```javascript
const hash = {
 name: "Chris",
 todos: [
  {
   title: "go shopping",
   description: "I need bread, eggs and milk"
  },
  {
   title: "Wash the car",
   description: "Remember the interior"
  },
 ],
}
```

Will produce the following:

```html
<h1>Hello Chris</h1>


<ul>
<li>go shopping: I need bread, eggs and milk</li>
<li>Wash the car: Remember the interior</li>
</ul>
```

We have 2 types of *tags* used in this example:

1. **variable tags** like `{{name}}` and `{{title}}`. These are pretty straight forward: the key is replaced with the value from the hash.
2. **section tags** like `{{^todos}}...{{/todos}}` and `{{#todos}}...{{/todos}}`. These render the blocks they surround 0, 1 or many times. In Appish there's two kinds, which you can spot by whether the opening tag has a "^" or a "#":

- \# : Acts like a FOR statement if the **tag key** turns out to be a non-empty array. Otherwise, it acts like a IF statement that renders the block only if the **tag key** exists and is not an empty array or `false`
- ^ : basically acts like a NOT operator

Check out [Moustache JS on Github](https://github.com/janl/mustache.js) for a deeper dive.

## Moustache in Appish

Here's the same template example, but in a tile's template config:

```js
const template = [
{
 el: "h2",
 inner: ["Hello {{name}}"]
},
{
 ctx: "{{^todos}}", // ctx stands for context, this is the equivelent of {{#todos}}<p>...</p>{{/todos}}
 el: "p",
 inner: ["No todos (yayy)"]
},
{
 el: "ul",
 inner: [
  {
   ctx: "{{#todos}}",
   el: "li",
   inner: ["{{title}}: {{description}}"],
  }
 ],
}
];
```

Notice how we've used a new field called `ctx`. It stands for "context" and it's the Appish way to wrap an element in **section tags**.

In case you're curious, the reason we use templating in Appish is to prevent bulky UIs from slowing down the time the user has to wait for a tile to be fetched from the server:

- template: downloaded when the user installs the bot
- hash: fetched just when the tile must be shown to the user

## Show todos (finally)

Finally, let's update our code to show the current user's todos on the home tile.

```javascript
import Appish from 'appish';

const db = ... // a made up database is defined here

const appish = new Appish(process.env.API_KEY);

appish.setup({
 startTile: "home",
 tiles: [
  {
   id: "home",
   template: [
    {
    el: "div",
    styles: "flex flex-col items-center m-2",
    inner: [
     {
      ctx: "{{^todos}}",
      el: "div",
      inner: ["No todos"],
     },
     {
      el: "button",
      action: "add",
      styles: "focus:outline-none text-white bg-green-700 hover:bg-green-800 focus:ring-4 focus:ring-green-300 font-medium rounded-lg text-sm px-5 py-2.5 mr-2 mb-2 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-green-800",
      inner: ["Add new"],
     },
     {
      el: "div",
      styles: "mt-2",
      inner: [{
        ctx: "{{#todos}}",
        el: "div",
        inner: ["- {{title}}: {{description}}"],
      }]
     }
    ],
   }],
   hash: async function (user,params){
       const todos = await db.todos.getAllWhere({userId: user.id});
       return todos.map(t=>({
        id: t.id,
        title: t.title,
        description: t.description,
        isDone: t.isDone,
       }));
   },
  }
 ],
}).then(()=>console.log('Appish bot successfully set up'))
.catch(err=>{
 console.log('Setup failed. Something went wrong: ',err);
});
```

- The `template` and `hash` fields represent the template and hash that are used to render the UI using templating.
- Note that `hash` is not just a javascript object literal, it's an async function. This function runs just when the user requests the tile, and returns the hash that will be used by the user's device to render the tile. It takes the parameters:
  - `user`: Appish provides this object to expose the user's information to your bot. This is useful for identifying the user and customizing their experience.
  - `params`: contains the data that was sent from the user's device in the request for this tile, or `null` if there's none.

So far so good. Now [let's give our users a way to add a new todo.](./add-todos)
