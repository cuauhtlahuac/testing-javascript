# install, configure, and script Cypress for JavaScript web applications

1. Install and run Cypress

   Once Cypress is installed in a project, we can run it with:

   ```bash
   $ npx cypress open
   ```

   This generates a number of tests and files in `./cypress` folder, including
   a folder for integration tests, fixtures, plugins, and support.

2. Write the first Cypress Test

   ```bash
   $ npm run dev & npx cypress open
   ```

   [`calculator.js`](./cypress/e2e/calculator.js)

   Tests in Cypress need to start with `cy.visit()` in order for Cypress to know
   what to render for integration tests to be written.

   We configure Cypress to test our app using `cy.visit('http://localhost:[port]')`
   once our server is running.

   By default, Cypress creates an integration folder in the `cypress` folder
   created when first run.

   To make it clear that we are writing end-to-end tests, we'll change it to
   `e2e`. Cypress won't know about this change until we specify to use the
   folder. We can do so by adding and `integrationFolder` property to our
   `cypress.json`.

   Once Cypress visits our app in the browser, we can select elements to
   interact with.

   To get an element, we use `.get('.my-selector')`. We can trigger a click on
   it with `.click()`. We can run assertions on elements using
   `.should('[assertion.command]', 'value-to-compare')`.

3. Configure Cypress in cypress.json

   Cypress can be configured via `cypress.json`.

   Instead of passing `http://localhost:8080` to all of our tests in
   `cy.visit()`, we can configure a `baseUrl` property that will be used in our
   tests, for which we can provide only paths.

   This solves another problem - by specifying a full URL, Cypress will reload
   the app every time the test is rerun. Using `baseUrl` overcomes this.

   We can move our `calculator.js` test in `cypress/integration` into
   `cypress/e2e` to better describe the intent of our tests.

   Cypress runs integration tests by default from `cypress/integration`, so
   we'll need to configure the `integrationFolder`

   By default Cypress runs apps at 1000x660. We can use the `viewportWidth` and
   `viewportHeight` properties in the config to specify our own dimensions.

   In Cypress' UI we can view the entire config for the project, including
   environment variables, plugin overrides, and CLI flags.

4. Installing cypress-testing-library

   Currently the `.get()` commands in our calculator test are pretty nasty. We
   can leverage `cypress-testing-library` to benefit from commands similar to
   those in `react-testing-library`.

   Once `cypress-testing-library` is installed, we need to extend `cy`'s
   commands. This is done by importing commands into
   `cypress/support/index.js`. We import `cypress-testing-library/add-commands` here.

   Now we can change `.get()` to `.getByText`, `.getByTestId` etc.

5. Scripting Cypress for local development and Continuous Integration

   In order to run Cypress we need to first run our server, and then start
   Cypress once that's running. It'd be convenient to be able to have the
   server start and Cypress then run once the server is receiving responses.

   This is also required for CI, because we need some way to start the server,
   run Cypress, and then kill the server once Cypress is done.

   We can use `start-server-and-test` to start our server, wait for the app to
   be accepting requests, run Cypress, and then kill our server.

   When we run Cypress on CI we don't want/need the UI. `cypress run` runs tests
   on Cypress without the UI.

   Using `is-ci`, `start-server-and-run`, `npm-run-all`, and separating our
   scripts we can simplify running our application and tests at the same time.

6. Debug a test with Cypress

   Because Cypress uses Chrome you can use dev tools to debug your tests.

   To debug tests in Cypress, chain a `.then` that accepts a subject and
   returns it. Inside this statement you can place a `debugger` statement.

   Cypress adds a `Cypress` object to the global object which can be used in
   tests to determine whether a file is being executed in the context of
   Cypress or not.

7. Use Cypress to test user registration

   [`register.js`](./cypress/e2e/register.js)

   We can automate registration by navigating to the sign up page, entering in
   details, and submitting the form.

   Using `test-data-bot` we can generate credentials for users using `faker`s
   API.

   To have Cypress simulate a user typing out text, we use the `.type('text to type')`
   command once we have a subject (an element).

   We can evaluate the current URL using `.url().should('eq', myUrl)`

   Cypress provides access to config values inside tests via `Cypress.config()`

   We can evaluate the `window` object using `.window()` to make it the
   subject.

   To evaluate properties on a subject, we use the `.its('property[name]')`
   command:

   ```javascript
   ...
     .window()
     .its('document.body')
     .should('have.class', 'my-class')
   ...
   ```

   Type assertions can be done using `.should('be.a', expectedType)`