---
name: discord-bot
description: Discord bot development with discord.js - slash commands, events, buttons, and deployment.
license: MIT
metadata:
  author: javascript-skills
  version: "1.0.0"
---

# Discord Bot (JavaScript)

Build Discord bots with discord.js.

## Bot Setup

```javascript
import { Client, Collection, GatewayIntentBits } from 'discord.js';
import { config } from 'dotenv';
config();

const client = new Client({
  intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMessages]
});

client.on('ready', () => {
  console.log(`Logged in as ${client.user.tag}`);
});

client.on('interactionCreate', async interaction => {
  if (!interaction.isChatInputCommand()) return;
  
  if (interaction.commandName === 'ping') {
    await interaction.reply(`Pong! ${client.ws.ping}ms`);
  }
});

client.login(process.env.DISCORD_TOKEN);
```

## Slash Commands

```javascript
import { SlashCommandBuilder } from 'discord.js';

const command = new SlashCommandBuilder()
  .setName('hello')
  .setDescription('Say hello')
  .addStringOption(option =>
    option.setName('name')
      .setDescription('Your name')
      .setRequired(true)
  );

// Register command
const rest = new REST({ version: '10' }).setToken(TOKEN);
await rest.put(
  Routes.applicationCommands(CLIENT_ID),
  { body: [command.toJSON()] }
);
```

## Event Handling

```javascript
client.on('guildMemberAdd', async member => {
  const channel = member.guild.channels.cache.find(c => c.name === 'welcome');
  if (channel) {
    channel.send(`Welcome ${member}!`);
  }
});

client.on('messageCreate', message => {
  if (message.author.bot) return;
  
  if (message.content.includes('hello')) {
    message.reply('Hello there!');
  }
});
```

## Buttons and Interactivity

```javascript
import { ActionRowBuilder, ButtonBuilder, ButtonStyle } from 'discord.js';

const button = new ButtonBuilder()
  .setCustomId('confirm')
  .setLabel('Confirm')
  .setStyle(ButtonStyle.Success);

const row = new ActionRowBuilder().addComponents(button);

await interaction.reply({
  content: 'Do you agree?',
  components: [row]
});

// Handle button click
client.on('interactionCreate', async interaction => {
  if (interaction.isButton() && interaction.customId === 'confirm') {
    await interaction.update({ content: 'Confirmed!', components: [] });
  }
});
```

## Deployment

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
CMD ["node", "index.js"]
```