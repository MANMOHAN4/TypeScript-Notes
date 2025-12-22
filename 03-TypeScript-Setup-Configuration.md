# Chapter 3: Setting Up TypeScript - Project Configuration

## Initializing a TypeScript Project

### Step 1: Create a New Project Directory

```bash
mkdir my-typescript-project
cd my-typescript-project
```

### Step 2: Initialize Node Project

```bash
npm init -y
```

This creates `package.json` with default settings. The `-y` flag skips interactive questions.

## Installing TypeScript

### Project-Level Installation (Recommended)

Install TypeScript as a **project dependency**, not globally:

```bash
npm install --save-dev typescript
```

Or shorter:
```bash
npm install -D typescript
```

**Why project-level?**
- Different projects may use different TypeScript versions
- Makes the project portable and reproducible
- Ensures team members use the same version
- Prevents version conflicts

### Global Installation (Not Recommended)

If you insist on global installation:
```bash
npm install -g typescript
```

## Initializing TypeScript Configuration

### Create TypeScript Config

```bash
npx tsc --init
```

This creates `tsconfig.json` with default configuration options.

**Important:** `npx` (Node Package eXecute) allows you to run locally installed packages.

### Understanding tsconfig.json

The `tsconfig.json` file contains all TypeScript compilation options.

#### Key Configuration Properties

```json
{
  "compilerOptions": {
    "target": "ES2020",                    // JavaScript version to compile to
    "module": "commonjs",                  // Module system (commonjs, es6, etc)
    "lib": ["ES2020"],                     // Library definitions to include
    "outDir": "./dist",                    // Where compiled JS goes
    "rootDir": "./src",                    // Where TS source files are
    "strict": true,                        // Enable all strict type checking
    "esModuleInterop": true,               // CommonJS/ES module compatibility
    "skipLibCheck": true,                  // Skip type checking of declaration files
    "forceConsistentCasingInFileNames": true, // Force case-sensitive file names
    "jsx": "react-jsx",                    // JSX handling mode
    "declaration": true,                   // Generate .d.ts files
    "sourceMap": true                      // Generate source maps
  },
  "include": ["src"],                      // Files to compile
  "exclude": ["node_modules", "dist"]     // Files to exclude
}
```

## Important Configuration Options Explained

### 1. **Target** (`target`)

Specifies which JavaScript version to compile to:
- `ES5` - Older JavaScript, wide browser support
- `ES2015` - ES6, widely supported
- `ES2020` - Modern JavaScript
- `ESNext` - Latest features

**Example Impact:**
```typescript
// Modern TypeScript
const message = `Hello ${name}`;

// Compiled to ES5
var message = "Hello " + name;
```

### 2. **Module System** (`module`)

Specifies how modules are handled:
- `commonjs` - Node.js style (default for Node)
- `es6`/`es2015` - ES modules
- `umd` - Universal Module Definition

### 3. **Root Directory** (`rootDir`)

Where TypeScript looks for `.ts` files:
```json
"rootDir": "./src"
```

This tells TypeScript: "All my TypeScript files are in the `src` folder."

### 4. **Output Directory** (`outDir`)

Where compiled `.js` files are written:
```json
"outDir": "./dist"
```

This says: "Put all generated JavaScript files in the `dist` folder."

### 5. **Strict Mode** (`strict`)

The most important option! Enables all strict type checking:
```json
"strict": true
```

This is equivalent to enabling:
- `noImplicitAny` - Disallow implicit any type
- `strictNullChecks` - Strict null checking
- `strictFunctionTypes` - Strict function types
- `strictPropertyInitialization` - Strict property initialization
- And more...

**Recommendation:** Always use `"strict": true` for new projects

### 6. **JSX Handling** (`jsx`)

Options for JSX transformation:
- `"react"` - For React without automatic import
- `"react-jsx"` - For React 17+ with automatic import
- `"preserve"` - Keep JSX as-is

### 7. **Declaration Files** (`declaration`)

Generate `.d.ts` files:
```json
"declaration": true
```

This creates type definition files for library usage.

### 8. **Source Maps** (`sourceMap`)

Generate source maps for debugging:
```json
"sourceMap": true
```

Allows debugging original TypeScript code in browser/debugger.

## Project Structure Setup

### Creating the Source Directory

```bash
mkdir src
```

### Creating Your First TypeScript File

```bash
touch src/index.ts
```

Write some code:
```typescript
function greet(name: string): string {
  return `Hello ${name}, Welcome to Chai and Code`;
}

const username = "Hitesh";
console.log(greet(username));
```

## Compilation Process

### Compile TypeScript to JavaScript

```bash
npx tsc
```

This command:
- Reads `tsconfig.json`
- Compiles all files in `src` directory
- Outputs JavaScript to `dist` directory
- Creates source maps and declaration files if configured

### Generated Files

After compilation, you'll have in `dist/`:
- `index.js` - Compiled JavaScript
- `index.js.map` - Source map
- `index.d.ts` - Type definitions

### Examine Generated JavaScript

The generated `index.js` will look like:
```javascript
"use strict";
function greet(name) {
    return `Hello ${name}, Welcome to Chai and Code`;
}
const username = "Hitesh";
console.log(greet(username));
```

Notice:
- Type annotations (`: string`) are removed
- Return type (`: string`) is removed
- Code structure remains identical
- `"use strict"` is added for safety

## Running Compiled JavaScript

### Using Node.js

```bash
node dist/index.js
```

This executes the compiled JavaScript.

## Setting Up NPM Scripts

### Edit package.json

Add scripts to `package.json`:

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsc && node dist/index.js",
    "watch": "tsc --watch"
  }
}
```

### Using the Scripts

```bash
# Build TypeScript
npm run build

# Run the compiled code
npm start

# Build and run together
npm run dev

# Watch mode - recompiles on file change
npm run watch
```

## TypeScript Watch Mode

```bash
npx tsc --watch
```

Or use the npm script:
```bash
npm run watch
```

**What watch mode does:**
- Monitors all TypeScript files
- Automatically recompiles when you make changes
- Very useful for development
- No need to manually run `tsc` every time

## Using ts-node for Development

For faster development, you can use **ts-node** which runs TypeScript directly:

```bash
npm install -D ts-node
```

Then run TypeScript files directly:
```bash
npx ts-node src/index.ts
```

**Advantages:**
- No compilation step needed
- TypeScript runs directly
- Faster feedback loop
- Perfect for development

**Note:** ts-node strips types and executes JavaScript in memory.

## Modern Runtimes

### Bun

Bun can run TypeScript files directly:
```bash
bun run src/index.ts
```

### Deno

Deno also supports TypeScript natively:
```bash
deno run src/index.ts
```

## Configuration for Different Scenarios

### For React Projects

Additional packages needed:
```bash
npm install --save-dev @types/react @types/react-dom
```

Update `tsconfig.json`:
```json
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "lib": ["ES2020", "DOM", "DOM.Iterable"]
  }
}
```

### For Node.js Projects

```bash
npm install --save-dev @types/node
```

Update `tsconfig.json`:
```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "ES2020"
  }
}
```

## Recommended Configuration for Beginners

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "sourceMap": true,
    "noImplicitAny": true,
    "strictNullChecks": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

## Key Takeaways

1. **Always install TypeScript project-level** - Use `npm install -D typescript`
2. **Initialize with `tsconfig.json`** - Use `npx tsc --init`
3. **Configure `rootDir` and `outDir`** - Tell TypeScript where to find and output files
4. **Use `strict: true`** - Enables strict type checking
5. **Set up npm scripts** - Makes development easier
6. **Use watch mode during development** - `tsc --watch`
7. **Consider ts-node for faster feedback** - Skips compilation step

## Troubleshooting

### "tsc" command not found
- Make sure you installed TypeScript: `npm install -D typescript`
- Use `npx tsc` instead of just `tsc`

### Files not compiling
- Check that source files are in `rootDir` (default: `./src`)
- Verify file extensions are `.ts` for TypeScript

### Wrong JavaScript output
- Check your `target` in `tsconfig.json`
- Check `module` system is correct
- View generated `.js` files to see what was produced

---

**Next:** We'll explore type annotations, type inference, and basic data types.
