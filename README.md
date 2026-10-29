# Secrets — Authentication Level 2

A learning exercise in building email and password authentication with Node.js, Express, PostgreSQL, and bcrypt. Users register or log in to view a predefined secret rendered with EJS.

The focus is storing salted password hashes and comparing passwords during login. The app does not yet support submitting secrets or maintaining login sessions.

## Learning objectives

- Handle registration and login form submissions with Express.
- Store and retrieve users in PostgreSQL using parameterized queries.
- Hash passwords with bcrypt using 10 salt rounds.
- Verify a submitted password with `bcrypt.compare()`.
- Render server-side pages with EJS and serve static CSS.

## Technology

- Node.js with ES modules
- Express and body-parser
- PostgreSQL and the `pg` client
- bcrypt
- EJS, Bootstrap, and Font Awesome

## Getting started

You need Node.js and npm, plus a running PostgreSQL server. Bootstrap and Font Awesome load from external CDNs, so their styling requires internet access.

### 1. Install dependencies

From the project directory:

```sh
npm install
```

### 2. Create the database and table

Using pgAdmin or `psql`, create a database named `secrets`:

```sql
CREATE DATABASE secrets;
```

Connect to that database, then run:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL
);
```

The `password` column stores the bcrypt hash, not the original password.

### 3. Configure the connection

Update the `pg.Client` configuration in the file you intend to run (`index.js` or `solution.js`) to match your local PostgreSQL credentials:

```js
const db = new pg.Client({
  user: "your_postgres_user",
  host: "localhost",
  database: "secrets",
  password: "your_postgres_password",
  port: 5432,
});
```

The exercise currently uses inline configuration; it does not load a `.env` file. Keep actual credentials out of shared commits.

### 4. Run the exercise

```sh
node index.js
```

Open **http://localhost:3000**.

**Current exercise issue:** `index.js` contains a second registration insert that references an undefined `hashedPassword` variable after starting the bcrypt callback. Remove that duplicate insert and its following `console.log(result)` and `res.render(...)` statements so registration inserts the user and renders the page only inside the successful hash callback. Use `solution.js` as a reference.

To run the reference implementation instead, configure its database connection and run:

```sh
node solution.js
```

Run only one entry point at a time because both use port 3000. There is no `npm start` script.

## Routes

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/` | Display the home page |
| GET | `/register` | Display the registration form |
| POST | `/register` | Check for an existing email, hash the password, and create a user |
| GET | `/login` | Display the login form |
| POST | `/login` | Find the user and compare the password with the stored hash |

Both forms submit `username` (the email address) and `password`. Successful registration or login renders `secrets.ejs` directly; there is no `/secrets` route.

## Project structure

```text
index.js              Exercise implementation
solution.js           Reference implementation
views/                EJS pages
  partials/           Shared page header and footer
public/css/styles.css Served application styles
package.json          Dependencies and package configuration
```

The root-level `partials/` and `css/` directories are additional copies; the app uses `views/partials/` and `public/css/`.

## Manual practice checks

After addressing the registration issue above:

1. Register with a new email and confirm the secret page appears.
2. Inspect the database and confirm the stored password is a hash rather than the submitted text.
3. Register with the same email again and confirm the existing-email message appears.
4. Log in with the correct password and confirm the secret page appears.
5. Try an incorrect password and an unknown email to inspect the failure responses.

No automated tests are configured. The existing `npm test` script is a placeholder that exits with an error.

## Exercise limitations and next steps

This is an educational starting point. It has no persistent sessions, logout, password reset, or secret submission feature. Some error paths only log errors and can leave requests without a response. The registration template also includes the header at the bottom where the footer would normally appear.

Useful follow-up exercises include moving credentials into environment variables, removing hash logging, adding input validation, handling asynchronous errors consistently, and implementing sessions and access checks for protected pages.
