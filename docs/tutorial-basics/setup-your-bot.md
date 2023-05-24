---
sidebar_position: 1
---

# Setup your bot

In this tutorial we're building the classic todo-list app as an Appish bot. Note that this is all just a concept since Appish is still under development. In fact this is more of a walkthrough than a tutorial since none of the code can actually run yet.

Here's a short clip showing how the completed bot will work (TODO: add the clip here).

Let's start by creating a "Hello world" bot to get started.

1. Setup a basic javascript project. This single codebase will control your bot, serving your users across all their favourite platforms.
2. In your entry javascript file (eg. `index.js`, `main.js` ) paste the following code to get started:

```javascript
import Appish from "appish";

const appish = new Appish(process.env.API_KEY);

appish
  .setup({
    startTile: "home",
    tiles: [
      {
        id: "home",
        template: [
          {
            el: "div",
            styles: "flex flex-col items-center m-2",
            nest: [
              {
                el: "div",
                nest: ["Hello world"],
              },
            ],
          },
        ],
      },
    ],
  })
  .then(() => console.log("Appish bot successfully set up"))
  .catch((err) => {
    console.log("Setup failed. Something went wrong: ", err);
  });
```

Notes:

- `process.env.API_KEY`: This is the API key you'll get from the official @appish bot. This API key has to be kept secret since anyone who has it can control your Appish bot.
- `appish.setup(...)`: The map paseed into this function defines the behaviour of your bot. It's called the bot config.
  Now let's talk about the `tiles`:
- Appish bots speak in tiles: the bot sends the user a "message" in the form of a tile. Tiles are awesome because they let the bot present information in all sorts of ways, from text to video to a live map. If a response is expected, then the tile will have some interactive elements in which the user submits their response- which will prompt the bot to respond by sending another tile and so on.
- The UI of a tile is heavily inspired by HTML and uses an adaptation of [Tailwind CSS](https://tailwindcss.com/) (in the `styles` field) for styling:
  - `el`: the HTML element tag name
  - `in`: this is how elements are nested
  - `styles`: tailwind styles. (there's no classes)
