# Discora

A structured way to create discord bots effectively and efficiently with ease.

Discora simplifies the repetitive tasks of command and event handling, so you won't need to write handlers for every new bot. just run the cli and start creating commands

__Building a Discord bot can be tricky, especially for new developers. Here are some common challenges:__

  

- ⚙️ Complex Setup: Managing commands and events can involve a lot of repetitive code, leading to mistakes.

- 🔄 Unorganized Event Logic: Event handling can become hard to track, causing the bot to behave unpredictably.

- 🧠 Steep Learning Curve: Learning how to set up a bot with Node.js and Discord.js can be tough.

- 🐞 Debugging Issues: Poorly organized code can make fixing problems harder.

  
Discora, built with TypeScript and type safety in mind, provides IntelliSense support, helping you write better code with fewer errors. The framework keeps everything simple and organized, so you can focus on building great bots.

## Installation

You can install Discora using npm:


```bash
npm install  Discora
```
Alternatively, you can quickly set up a new project with Discora using npx: __(recommended)__

```bash
npx create-discora@latest
```

## Basics

The Discora framework consists of a client, a loader, and handlers. This concept is very simple and not too complicated. It is designed this way so developers can contribute and add other concepts to make Discora even better.

**DiscoraClient:** is a class that extends the Discord.js Client class adding features such as loading commands and events using a specfic type loader

**Loader**: The loader reads your commands and events files, there are currently two types of loaders availble

-  `flat` - Loads the typescript and javascript files on the top level it does not load files within folders

-  `recursive` - Loads files and files nested within folders. It will load all the files within the commands directory, regardless of how deeply they are nested

**Handlers:** Handlers are specifically used in slash commands. They help manage interactions that aren't traditional chat input commands but are still connected to a particular command.

_These are the three concepts that makes Discora, and we will explore them further in this documention._

## DiscoraClient

The DiscoraClient extends the Discord.js Client class, handling most of the heavy lifting while still giving you full access to the underlying Client.

This allows you to focus on your bot's functionality without worrying about the boilerplate. With DiscoraClient, managing commands, and events. other aspects of your bot becomes straightforward.
  
```ts

import { DiscoraClient } from  "discora";

const  client = new  DiscoraClient({

   root:  process.cwd(), // Root directory (required by Discora)

   token: "Your bot token", // Your Discord bot token (required)

   clientId: "Your bot clientId", // Your bot's client ID (required)

   guildId: "Your guild ID", // Guild where commands are registered (required)
   
   handler: { events: "/events", slash: "/commands" }, // Paths for event & command handlers

   loader: { events: "flat", slash: "recursive" },
   
   environment: "development", // Use 'production' for global command registration

  // How to load events and commands

   client: { intents: ["GuildMessages", "GuildMembers"]    }, // Bot's intents

});

client.start(); // Load commands & events, then start the bot

```
## SlashCommands

Once you setup the Discora Client creating a slash Command  is as easy as adding a file under the directory you specify and the following code.

The DiscoraClient will automatically load this file according to the specified loader type when the bot starts, storing the command information in memory for future reference.

```ts
import { SlashCommandBuilder } from "discord.js";
import { createSlashCommand } from "discora";

export default createSlashCommand({
  data: new SlashCommandBuilder()
    .setName("ping")
    .setDescription("Replies with Pong!"),

  execute: async (interaction) => {
    await interaction.reply("Pong!");
  },
});
```

   - **data**: The command data defined using the  `SlashCommandBuilder`. This includes the command name, description, and any options.
   
  - **execute**: The function executed when a user invokes the slash command. It has access to the interaction object from Discord.js, allowing you to respond to the user.

## Creating an Event

The concept for events is similar: simply create a file in the directory specified for events through the `DiscoraClientOptions`. 

The `DiscoraClient` will load these files, but instead of loading the data into memory, it will create a new event listener using the loaded data. The handler function will be called with the `DiscoraClient` as the first parameter and the event as the second.
```ts
import { Events } from "discord.js";
import { createEvent } from "discora";

export default createEvent({
  name: Events.ClientReady,
  once: true,
  handler: async (_, client) => {
    console.log(`${client.user.username} is online and ready!`);
  },
});
```

##  Interaction Handlers

Okay, so while Discora supports slash commands, we all know that Discord offers other types of interactions like `autocomplete`, `modalSubmit`, `buttonInteraction`, and a few more. So how do we handle these? The Discora client has a concept called interaction handlers, or just handlers, which makes it really easy to manage all of these interactions.

**Interaction Handlers** can only be exported from the files within the slashCommands directories


Let's take a look at an example using a button.

```ts
// Button interaction handler
export const handleButtonClick: HandleButtonClickFunction = async (
  interaction
) => {
  if (interaction.customId === "ping-hello_button") {
    await interaction.reply("Hello, world!");
  } else if (interaction.customId === "ping-hi_button") {
    await interaction.reply("Hi!");
  }
};

// Slash command logic
export default createSlashCommand({
  data: new SlashCommandBuilder()
    .setName("ping")
    .setDescription("A test command"),

  execute: async (interaction) => {
    const helloButton = new ButtonBuilder()
      .setCustomId("ping-hello_button")
      .setLabel("Say Hello")
      .setStyle(ButtonStyle.Primary);

    const hiButton = new ButtonBuilder()
      .setCustomId("ping-hi_button")
      .setLabel("Say Hi")
      .setStyle(ButtonStyle.Primary);

    const row = new ActionRowBuilder<ButtonBuilder>()
      .addComponents(helloButton, hiButton);

    await interaction.reply({
      content: "How do you want me to respond?",
      components: [row],
    });
  },
});
```

### Key Points:
- **Custom IDs**: Button custom IDs are prefixed with the command name (`ping-`) to ensure the handler logic matches the command.
-  **Separated Handlers**: The  `handleButtonClick`  function handles button interactions separately, keeping the code modular and maintainable.
-   **Usage**: The handler triggers for any button with a custom ID prefixed with  `ping-`, such as  `ping-hello_button`, specifically for that command.

# Documentation

For in-depth examples and explanations, visit the Discora docs. The Discora docs contain instructions on how to use Discora in ESM, CommonJS, and TypeScript, so be sure to check it out.
3. **Usage**: The handler triggers for any button with a custom ID prefixed with `ping-`, such as `ping-hello_button`, specifically for that command.
