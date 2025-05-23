---
aliases: ["An Introduction to React"]
---

## Creating a React Project

## Browser Based

In your browser enter `react.new`.

This will create a new React project in `codesandbox.io`.

This gives you an in browser development environment to experiment with.

## Locally

### Prerequisites

You will need to first install [Node.js](https://nodejs.org/en). Typically I install `Node` using the [Node Version Manager](https://github.com/nvm-sh/nvm) since it allows you to run multiple versions of `Node` on your machine.

You will utilize [Vite](https://vite.dev/) to scaffold a new react environment so make sure to install that after installing `Node`.

#### Scaffolding your First Vite Project

```shell
npm create vite@latest
```

The follow the prompts.

The [React + Vite](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react) template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Since most of the time you will be creating a `React` application using [TypeScript](https://www.typescriptlang.org/) I would recommend using the [React + TypeScript + Vite](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts) template.

**Let Vite walk you through the creation process interactively:**

```shell
npm create vite@latest <project-name>
npm install
```

**Using the template explicitly:**

```shell
npm create vite@latest <project-name> -- --template react-ts
npm install
```

Note: You can use `.` for the project name to scaffold in the current directory.

Note: After scaffolding a new react application with `vite` you will need to run `npm install` ti