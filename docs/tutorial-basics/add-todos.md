---
sidebar_position: 3
---

# Add todos

Let's add a new tile that'll let us add a new todo item.

Here's the tile we'll add

```javascript
// We won't use this variable name. It's just here to make this code snippet valid javascript. We'll only add the object to our project.
const addNewTile = {
  id: "add-new",
  template: [
    {
      el: "div",
      styles: "m-2",
      in: [
        {
          ctx: "{{#justAdded}}",
          el: "div",
          styles: "mb-2",
          in: ["You've successfully added the todo: {{.}}"],
        },
        "Add a new todo",
        {
          el: "lable",
          _for: "title",
          in: ["Title"],
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
          el: "lable",
          _for: "description",
          in: ["Title"],
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
          in: [
            {
              el: "button",
              action: "cancel",
              styles: "mr-2",
              in: ["cancel"],
            },
            {
              el: "button",
              action: "done",
              in: ["Add todo"],
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

We've just introduced you to a few things here, let's talk about those:

- Notice the `label`, `input` and `textarea` elements. These are Appish form elements. They look and act like HTML form elements. The key thing to note about Appish form elements is that their values are automatically submitted when the tile is submitted. This brings us to:
- The `on` tile field. This is an array of submit-reactors. When the user submits the tile using a submitting element(a button or any other element defined with an `action` field), an "action" value is also passed along. The submit-reactor with a matching `action` field is chosen, and its `call` function to called.
  - Submit-reactor functions are called with
    1. the user data of the user who submitted the tile, and
    2. an object that holds the parameters that are being submitted with the tile. By default these parameters are taken from each form element on the tile. They are passed as an object whose keys are taken from `name` and the values from the value of each form element.
  - Submit-reactors return an object that determines which tile should be sent to the user next. The config of that tile is selected, then the `hash` field function is called to determine the hash values that will be used to render the tile on the user's device.
- Notice how some of the fields for elements start with an underscore, like `_for`, `_type`, `_id`, `_placeholder`. All fields that start with an underscore directly match to HTML attributes with the same name.

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
    in: [
     {
      ctx: "{{^todos}}",
      el: "div",
      in: ["No todos"],
     },
     {
      el: "button",
      action: "add",
      styles: "focus:outline-none text-white bg-green-700 hover:bg-green-800 focus:ring-4 focus:ring-green-300 font-medium rounded-lg text-sm px-5 py-2.5 mr-2 mb-2 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-green-800",
      in: ["Add new"],
     },
     {
      el: "div",
      styles: "mt-2",
      in: [{
        ctx: "{{#todos}}",
        el: "div",
        in: ["- {{title}}: {{description}}"],
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
    in: [
     {
      ctx: "{{#justAdded}}",
      el: "div",
      styles: "mb-2",
      in: ["You've successfully added the todo: {{.}}"],
     },
     "Add a new todo",
     {
      el: "lable",
      _for: "title",
      in: [
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
      el: "lable",
      _for: "description",
      in: [
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
      in: [
        {
         el: "button",
         action: "cancel",
         styles: "mr-2",
         in: ["cancel"]
        },
        {
         el: "button",
         action: "done",
         in: ["Add todo"]
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
