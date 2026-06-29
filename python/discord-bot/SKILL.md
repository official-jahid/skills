---
name: discord-bot
description: Discord bot development with discord.py - commands, events, cogs, and bot deployment.
license: MIT
metadata:
  author: python-skills
  version: "1.0.0"
---

# Discord Bot (Python)

Discord bot patterns using discord.py.

## Bot Setup

```python
import discord
from discord.ext import commands

bot = commands.Bot(command_prefix='!', intents=discord.Intents.all())

@bot.event
async def on_ready():
    print(f'{bot.user} has logged in!')

@bot.command()
async def ping(ctx):
    await ctx.send(f'Pong! {round(bot.latency * 1000)}ms')

bot.run('TOKEN')
```

## Slash Commands

```python
from discord import app_commands

@bot.tree.command(name='hello', description='Say hello')
@app_commands.describe(name='Your name')
async def hello(interaction: discord.Interaction, name: str):
    await interaction.response.send_message(f'Hello {name}!')

@bot.event
async def on_ready():
    await bot.tree.sync()
```

## Cogs

```python
# cogs/moderation.py
from discord.ext import commands

class Moderation(commands.Cog):
    def __init__(self, bot):
        self.bot = bot
    
    @commands.command()
    @commands.has_permissions(ban_members=True)
    async def ban(self, ctx, member: discord.Member, reason=None):
        await member.ban(reason=reason)
        await ctx.send(f'Banned {member}')

async def setup(bot):
    await bot.add_cog(Moderation(bot))
```

## Event Handling

```python
@bot.event
async def on_message(message):
    if message.author == bot.user:
        return
    
    if 'hello' in message.content.lower():
        await message.channel.send('Hello!')
    
    await bot.process_commands(message)

@bot.event
async def on_member_join(member):
    channel = discord.utils.get(member.guild.channels, name='welcome')
    if channel:
        await channel.send(f'Welcome {member}!')
```

## Buttons and Views

```python
from discord.ui import Button, View

class ConfirmView(View):
    def __init__(self):
        super().__init__()
        self.value = None
    
    @Button(label='Confirm', style=discord.ButtonStyle.green)
    async def confirm(self, interaction: discord.Interaction, button: Button):
        self.value = True
        await interaction.response.send_message('Confirmed!', ephemeral=True)
        self.stop()
```

## Deployment

```bash
# Docker
FROM python:3.11-slim
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
CMD ["python", "bot.py"]
```