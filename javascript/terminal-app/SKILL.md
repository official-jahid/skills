---
name: terminal-app
description: Terminal application patterns - CLI parsing, prompts, colors, and cross-platform terminal interaction.
license: MIT
metadata:
  author: javascript-skills
  version: "1.0.0"
---

# Terminal Application (JavaScript)

Build CLI applications with Node.js.

## Argument Parsing with Commander

```javascript
import { Command } from 'commander';
const program = new Command();

program
  .name('myapp')
  .description('CLI tool')
  .version('1.0.0')
  .requiredOption('-f, --file <path>', 'file to process')
  .option('-v, --verbose', 'verbose output');

program.parse();
```

## Interactive Prompts

```javascript
import prompts from 'prompts';

async function ask() {
  const response = await prompts([
    { type: 'text', name: 'name', message: 'Your name' },
    { type: 'password', name: 'password', message: 'Password' },
    { type: 'select', name: 'color', message: 'Pick color', choices: [
      { title: 'Red', value: 'red' },
      { title: 'Blue', value: 'blue' }
    ]},
    { type: 'confirm', name: 'confirm', message: 'Continue?' }
  ]);
  return response;
}
```

## Colors and Formatting

```javascript
import chalk from 'chalk';

console.log(chalk.blue('Info message'));
console.log(chalk.red.bold('Error!'));
console.log(chalk.green(`Success: ${item}`));
console.log(chalk.yellow.bgRed('Warning!'));

// Custom themes
const success = chalk.bold.green;
const error = chalk.bold.red;
```

## Progress Bars

```javascript
import cliProgress from 'cli-progress';

const bar = new cliProgress.SingleBar({}, cliProgress.Chalk2);
bar.start(100, 0);

for (let i = 0; i <= 100; i++) {
  bar.update(i);
  await processItem(i);
}
bar.stop();
```

## Tables

```javascript
import Table from 'cli-table';

const table = new Table({ head: ['Name', 'Age', 'City'] });
table.push(['John', 25, 'NYC'], ['Jane', 30, 'LA']);
console.log(table.toString());
```

## Keyboard Input

```javascript
import readline from 'readline';

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.question('Enter value: ', (answer) => {
  console.log(`You entered: ${answer}`);
  rl.close();
});
```

## Packaging

```json
{
  "bin": {
    "myapp": "./index.js"
  },
  "files": ["dist/**/*"]
}
```