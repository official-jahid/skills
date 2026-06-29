---
name: terminal-app
description: Terminal application patterns - CLI argument parsing, interactive prompts, progress bars, and output formatting.
license: MIT
metadata:
  author: typescript-skills
  version: "1.0.0"
---

# Terminal Application (TypeScript)

Build CLI applications with Node.js.

## Argument Parsing

```typescript
import { Command } from 'commander';

const program = new Command();
program
  .name('myapp')
  .description('CLI tool')
  .version('1.0.0')
  .option('-v, --verbose', 'verbose output')
  .argument('<file>', 'file to process')
  .action((file: string, options: { verbose: boolean }) => {
    console.log(`Processing ${file}`);
  });

program.parse();
```

## Interactive Prompts

```typescript
import prompts from 'prompts';

async function askQuestions() {
  const response = await prompts([
    { type: 'text', name: 'name', message: 'Your name' },
    { type: 'password', name: 'password', message: 'Password' },
    { type: 'select', name: 'color', message: 'Pick a color', choices: [
      { title: 'Red', value: '#ff0000' },
      { title: 'Green', value: '#00ff00' },
    ]},
  ]);
  return response;
}
```

## Progress Bars

```typescript
import cliProgress from 'cli-progress';

const bar = new cliProgress.SingleBar({}, cliProgress.Chalk2);
bar.start(100, 0);
for (let i = 0; i <= 100; i++) {
  bar.update(i);
  await processItem(i);
}
bar.stop();
```

## Colors and Formatting

```typescript
import chalk from 'chalk';

console.log(chalk.blue('Info message'));
console.log(chalk.red.bold('Error!'));
console.log(chalk.green(`Success: ${item}`));
```

## Tables

```typescript
import Table from 'cli-table';

const table = new Table({ head: ['Name', 'Age', 'City'] });
table.push(['John', 25, 'NYC'], ['Jane', 30, 'LA']);
console.log(table.toString());
```