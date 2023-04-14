## TypeScript Compiler

### Basic commands

    tsc app.ts // compile file one-time
    tsc app.ts -w // compile file in watch mode

    tsc --init // compile all js files in project (current directory) - creates tsconfig.json

After running 'tsc --init' we only need to run 'tsc' or 'tsc -w' later.

### Including and excluding files

In the tsconfig.json:

    "exclude": ["foo.ts", "bar.ts", "*.dev.ts", "node_modules"]
    "include": ["app.ts"]