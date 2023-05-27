---
sidebar_position: 3
---

# Build a tile to add todos

Let's build a new tile that'll let users add new todos.

## How to navigate between tiles

Here's an example of two tiles, `tileA` and `tileB`. `tileA` has a button that navigates the user to `tileB`:

```javascript
import Appish from 'appish';

const db = ... // a made up database is defined here

const appish = new Appish(process.env.API_KEY);

appish.setup({
 startTile: "tileA",
 tiles: [
  {
   id: "tileA",
   template: [
    {
      el: "button",
      action: "goto-b",
      inner: ["Go to B"]
    }
   ],
   on: [
    {
      action: "goto-b",
      call: (user,params)=>({tile: "tileB", hashParams: null}),
    }
   ]
  },
  {
   id: "tileB",
   template: [
    "Welcome to tileB, {{username}}!",
   ],
   hash: async function (user,params){
    return {username: user.name};
   },
  },
 ],
}).then(()=>console.log('Appish bot successfully set up'))
.catch(err=>{
 console.log('Setup failed. Something went wrong: ',err);
});
```

There are two **tile configs** involved in navigating between tiles: `hash` and `on`.

It starts with the `on` field. This is an array of **submit-reactors**. Submit reactors "take the user from one tile to another". Here's how:

1. It starts with a submission. A submission is triggered by clicking on a **submitting element** (like a button). When the user submits the tile, the submit-reactor with a matching `action` field is chosen, and its `call` function is called.
2. Submit-reactor functions are called with two parameters. The first one is `user`, which exposes the information of the user who triggered the submission (the "current" user). The second one allows passing data along with the submission.
2. Submit-reactor functions return an object that determines which tile should be sent to the user next. The `hashParams` specified will be passed to the **hash function** of that next tile.

Finally the **hash function**(`hash`) of the next tile takes over. You're already familiar with the **hash function** because we just used it to [show the user's todos](./show-todos).

## Now, lets build the tile to add todos

Now were ready to build the tile that the user will use to add new todos.

```javascript
// We won't use this variable name. It's just here to make this code snippet valid javascript. We'll only add the object to our project.
const addNewTile = {
  id: "add-new",
  template: [
    {
      el: "div",
      styles: "m-2",
      inner: [
        {
          ctx: "{{#justAdded}}",
          el: "div",
          styles: "mb-2",
          inner: ["You've successfully added the todo: {{.}}"],
        },
        "Add a new todo",
        {
          el: "label",
          _for: "title",
          inner: ["Title"],
        },
        {
          el: "input",
          _type: "text",
          _id: "title",
          _name: "title",
          _placeholder: "Do something",
          styles: "mb-2",
        },
        {
          el: "label",
          _for: "description",
          inner: ["Title"],
        },
        {
          el: "textarea",
          _rows: "2",
          _cols: "50",
          _id: "description",
          _name: "description",
          styles: "mb-2",
        },
        {
          el: "div",
          styles: "flex",
          inner: [
            {
              el: "button",
              action: "cancel",
              styles: "mr-2",
              inner: ["cancel"],
            },
            {
              el: "button",
              action: "done",
              inner: ["Add todo"],
            },
          ],
        },
      ],
    },
  ],
  hash: (params) => null,
  on: [
    {
      action: "cancel",
      call: (user, p) => ({ tile: "home", hashParams: null }),
    },
    {
      action: "done",
      call: async function (user, { title, description }) {
        const newTodo = { userId: user.id, title, description };
        const result = await db.todos.add(newTodo);

        // resend this tile with the title of the item just added
        return { tile: "add-new", hashParams: { justAdded: newTodo.title } };
      },
    },
  ],
};
```

We've just introduced a few things here, let's talk about those:

Notice the `label`, `input` and `textarea` elements. These are Appish **form elements**. They look and act like HTML form elements. The values of form elements are automatically passed as a single object literal to the second parameter of the chosen **submit-reactor** function.

Notice how some of the fields for elements start with an underscore, like `_for`, `_type`, `_id`, `_placeholder`. All fields that start with an underscore directly match to HTML attributes with the same name.

Here's what our project looks like so far

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
  },
  {
   id: "add-new",
   template: [{
    el: "div",
    styles: "m-2",
    inner: [
     {
      ctx: "{{#justAdded}}",
      el: "div",
      styles: "mb-2",
      inner: ["You've successfully added the todo: {{.}}"],
     },
     "Add a new todo",
     {
      el: "label",
      _for: "title",
      inner: [
        "Title"
      ],
     },
     {
      el: "input",
      _type: "text",
      _id: "title",
      _name: "title",
      _placeholder: "Do something",
      styles: "mb-2"
     },
     {
      el: "label",
      _for: "description",
      inner: [
        "Title"
      ],
     },
     {
      el: "textarea",
      _rows: "2",
      _cols: "50",
      _id: "description",
      _name: "description",
      styles: "mb-2"
     },
     {
      el: "div",
      styles: "flex",
      inner: [
        {
         el: "button",
         action: "cancel",
         styles: "mr-2",
         inner: ["cancel"]
        },
        {
         el: "button",
         action: "done",
         inner: ["Add todo"]
        },
      ]
     }
    ]
   }],
   hash: (params)=>null,
   on: [
     {
    action: "cancel",
    call: (user,p)=>({tile:"home", hashParams: null}),
     },
     {
    action: "done",
    call: async function (user,{title, description}){
      const newTodo = { userId: user.id, title, description };
      const result = await db.todos.add(newTodo)

      // resend this tile with the title of the item just added
      return {tile:"add-new", hashParams: {justAdded: newTodo.title}};
    }
     }
   ]
   }
 ],
}).then(()=>console.log('Appish bot successfully set up'))
.catch(err=>{
 console.log('Setup failed. Something went wrong: ',err);
});
```

Our todo-list bot is almost ready for the spotlight, but what good is a todo-list if you can't indicate which todos you've completed? [Let's add that feature next.](./mark-a-todo-as-done)