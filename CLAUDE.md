# CLAUDE.md — Guestbook Project

This file provides guidance for AI assistants (and developers) working in this repository.

## Project Overview

A simple educational guestbook web application built with Node.js, Express, and EJS templating. Entries are stored in-memory (no database). Designed as a learning project following a tutorial by @FaztTech.

## Tech Stack

| Layer       | Technology                          |
|-------------|-------------------------------------|
| Runtime     | Node.js (v8+)                       |
| Framework   | Express 4.x                         |
| Templating  | EJS 2.x                             |
| Styling     | Bootstrap 3.3.7 (CDN)               |
| Middleware  | body-parser, morgan                 |
| Storage     | In-memory (`app.locals.entries`)    |

## Directory Structure

```
guestbook/
├── app.js              # Entry point — Express setup, middleware, server start
├── routes/
│   └── index.js        # All route handlers (GET /, GET /new-entry, POST /new-entry)
└── views/
    ├── index.ejs       # Homepage listing all entries
    ├── new-entry.ejs   # Form to create a new entry
    └── partials/
        ├── head.ejs    # HTML <head> with Bootstrap CDN
        ├── header.ejs  # Navigation bar
        └── footer.ejs  # Closing HTML tags
```

## Development Workflow

### Setup

```bash
npm install
npm start         # Starts server on http://localhost:3000
```

The port defaults to `3000` but can be overridden via the `PORT` environment variable.

### Running Tests

No test framework is currently configured. The `npm test` script exits with an error. Adding tests (e.g., with Jest or Mocha) is a potential improvement.

### No Build Step

There is no bundler, transpiler, or build process. All JavaScript runs directly via `node app.js`.

## Key Conventions

### Routing Pattern

Routes are defined in `routes/index.js` as a function that receives the Express `app` instance:

```js
module.exports = function(app) {
  app.get('/', ...);
  app.post('/new-entry', ...);
};
```

This function is called in `app.js` with `require('./routes/index')(app)`.

### Data Model

Entries are objects with the following structure:

```js
{
  title: String,     // Entry title (required)
  content: String,   // Entry body text (required)
  published: Date    // Timestamp set at creation
}
```

Entries are stored in `app.locals.entries` (an array), making them available to all EJS views via `<%= locals.entries %>`.

### EJS Partial Includes

Views use the EJS 2.x partial syntax:

```ejs
<% include partials/head %>
<% include partials/header %>
```

Note: In EJS 3.x this syntax changed to `<%- include('partials/head') %>`. If upgrading EJS, all views must be updated.

### Naming Conventions

- JavaScript: camelCase for variables and functions
- Route paths: kebab-case (e.g., `/new-entry`)
- View files: kebab-case (e.g., `new-entry.ejs`)
- The codebase mixes Spanish (UI labels, comments) and English (code identifiers)

## Known Issues / Technical Debt

### Bug in POST `/new-entry` handler (`routes/index.js:19`)

```js
// Current (broken):
res.send(400).send();

// Should be:
res.status(400).send('Title and content are required');
```

This will throw a runtime error when validation fails on form submission.

### No Persistence

All entries are lost on server restart. To add persistence, a database (e.g., SQLite, MongoDB, PostgreSQL) or file-based storage would need to be integrated.

### No Tests

No testing infrastructure exists. Adding unit tests for route handlers and integration tests for the HTTP endpoints is recommended before any significant changes.

### Outdated `.gitignore`

The `.gitignore` contains patterns for Angular/SystemJS projects that are irrelevant to this Express app. It can be cleaned up.

## Important Constraints

- **No database** — do not assume persistence between restarts
- **No authentication** — all routes are public
- **No CI/CD** — changes must be manually tested locally
- **Educational project** — keep changes simple and readable; this is not a production system

## Potential Improvements (Do Not Implement Unless Asked)

- Fix the `res.send(400)` bug in the POST handler
- Add a test framework (Jest or Mocha + Supertest)
- Persist entries to a JSON file or database
- Add input sanitization to prevent XSS in rendered entries
- Upgrade EJS partial syntax for EJS 3.x compatibility
- Add a delete entry route
