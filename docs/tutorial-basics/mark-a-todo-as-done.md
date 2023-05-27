---
sidebar_position: 4
---

# Mark a todo as done

We've come so far. With this last step, we'll have a usable cross-platform todo-list!

We're going to update the home tile so that the user can mark todos as done and undone.

```javascript
// We won't use this variable name. It's just here to make this code snippet valid javascript. We'll only add the object to our project.
const homeTile = {
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
      // TEMPLATE UPDATED FROM HERE...
      styles: "flex",
      inner: [
     {
       el: "input",
       _type: "checkbox",
       _id: "toggle-todo-{{id}}",
       action: "toggle-todo",
       actionParams: ["{{id}}"],
       _checked: "{{isDone}}"
     },
     {
      el: "label",
      _for: "toggle-todo-{{id}}",
      inner: ["- {{title}}: {{description}}"],
     }
      ],
      // TO HERE
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
 on: [
  {
   action: "toggle-todo",
   call: async function (user,p){
    const [id] = p;
    const targetTodo = await db.todos.getOne({userId: user.id, id});
    await targetTodo.update({isDone: !targetTodo.isDone});
   
    const todos = await db.todos.getAllWhere({userId: user.id});
    return todos.map(t=>({
     id: t.id,
     title: t.title,
     description: t.description,
     isDone: t.isDone,
    }));
   },
  }
 ]
};
```

- The `action` field turns the checkbox element into a submitting element. This means that when the user clicks on the checkbox, it will be toggled as usual, and then it will trigger the submit of the the tile.
- Normally, whenever the home tile is submitted, the parameters that would be passed to the submission-handler would be null (since there are no form elements). That's why we defined `actionParams` to override this default parameter whenever the tile is submitted through the `toggle-todo` action.
