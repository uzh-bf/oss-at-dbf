---
title: Testing Web Applications
date: 2022-12-30
author: Jonas Gebel
---

# Testing Web Applications (Automated UI Testing, Integration Tests, Github Actions)

https://api.cast.switch.ch/p/106/sp/10600/embedIframeJs/uiconf_id/23448425/partner_id/106?iframeembed=true&playerId=kaltura_player&entry_id=0_3qfyc54d&flashvars[streamerType]=auto&amp;flashvars[localizationCode]=de&amp;flashvars[leadWithHTML5]=true&amp;flashvars[sideBarContainer.plugin]=true&amp;flashvars[sideBarContainer.position]=left&amp;flashvars[sideBarContainer.clickToClose]=true&amp;flashvars[chapters.plugin]=true&amp;flashvars[chapters.layout]=vertical&amp;flashvars[chapters.thumbnailRotator]=false&amp;flashvars[streamSelector.plugin]=true&amp;flashvars[EmbedPlayer.SpinnerTarget]=videoHolder&amp;flashvars[dualScreen.plugin]=true&amp;flashvars[hotspots.plugin]=1&amp;flashvars[Kaltura.addCrossoriginToIframe]=true&amp;&wid=0_4t7a7k9w

# Motivation and Goals

End-to-end testing is a technique that tests the entire software product from beginning to end to ensure the application flow behaves as expected. It defines the product’s system dependencies and ensures all integrated pieces work together as expected.

The main purpose of End-to-end (E2E) testing is to test from the end user’s experience by simulating the real user scenario and validating the system under test and its components for integration and data integrity.

Software systems nowadays are complex and interconnected with numerous subsystems. If any of the subsystems fails, the whole software system could crash. This is a major risk and can be avoided by end-to-end testing.

In our case, E2E testing should help to simplify the whole testing process of the applications and automate it. That way, once the process is implemented, developers can focus on building without having to pay too much attention on testing.

Cypress offers a simple and understandable way of implementing an automation process into any application. The process can be integrated into Github, which makes the whole process automated.

## Github Actions

The automated testing process can be integrated into Github via Github actions. These are `.yml` files which contain all the requirements for the application to run and to be tested by Cypress. The challenge is to configure the `.yml` file in a way that all necessary parts of the application are defined and built if necessary (for example seeding the database) so that Cypress can then start the test suite.

The goal is that with every commit to a specified branch, Github actions triggers the whole test suite and runs it. This way, developers can continue building their application, while the tests run in the background.

# Setup and usage

## Setup test environment

1. If not already in dependencies, install cypress: `npm install cypress -E -D` . Detailed descriptions [here](https://github.com/cypress-io/cypress).
2. If not already in dependencies, install testing library: `npm install -E -D @testing-library/cypress` . Detailed descriptions [here](https://github.com/testing-library/cypress-testing-library).

## How to run tests via Terminal

With Cypress Desktop: `npx cypress open`
In headless mode: `npx cypress run`
Default browser is Electron. To change that: `npx cypress run --browser firefox/chrome`
Run only one file in headless mode: `npx cypress run --spec "path/to/file"`

# Cypress Cloud

_Cypress_ _Cloud is our enterprise-ready, web-based companion to the Cypress app. It gives you online access to your recorded test results, orchestrates test runs across multiple machines, provides rich analytics and diagnostics, and integrates those insights with your favourite tools._

There is a subscription plan (3 users and 500 monthly test results) that you need to use this. It adds some benefits in comparison to the free Cypress app where you can track test results live.

First of all your test results are stored and saved, so you don't have to remember any test outcomes from last week. The tests are saved as recordings, screenshots and full stack traces (For those that need it, myself included: _Full stack tracing is tracing on the full software stack, from the operating system to the application. By collecting profiling information (timing, process, caller, API, and other info) from the kernel, drivers, software frameworks, application, and JavaScript environments, you’re able to see exactly how the individual components of a system are interacting._).

Second, it offers a lot of analytical tricks and tools that can help to discover flaky tests (➝ Test Retries), you can group tests together depending on OS or browser, results are shared with the free Cypress app so developers can see the latest results across the team. You can run tests in parallel (time optimisation) and on multiple different browsers.

### Test Retries

Once test retries are enabled, tests can be configured to have X number of retry attempts. For example, if test retries has been configured with 2 retry attempts, Cypress will retry tests up to 2 additional times (for a total of 3 attempts) before potentially being marked as a failed test.

You can configure the amount of retries in the cypress.config file for the run mode and the open mode respectively or for both together. You can also configure retry attempts on a specific test or test suite.

Screenshots will be taken for each failed attempt. For videos you can delete videos for specs that had no retry attempts or failures when using Cypress test retries to save resource space on the machine as well as skip the time used to process, compress, and upload the video to Cypress Cloud.

If you are using Cypress Cloud, information related to test retries is displayed on the Test Results tab for a run. Selecting the Flaky filter will show tests that retried and then passed during the run.

Find the well written documentation here: [https://docs.cypress.io/guides/guides/test-retries#What-you-ll-learn](https://docs.cypress.io/guides/guides/test-retries#What-you-ll-learn

# Cypress Plugins

### Cypress testing library

An originally extern library that got verified by the Cypress team. Offers additional commands for your tests. Mostly used to find elements in different ways, for example via text.

Link: [https://github.com/testing-library/cypress-testing-library](https://github.com/testing-library/cypress-testing-library)

# Add data-cy attribute

Before writing tests, so-called `data-cy` attributes need to be added to your components in order for them to be usable by Cypress. Why using this attribute is recommended and other options is described [here](https://docs.cypress.io/guides/references/best-practices#Selecting-Elements).

Example of how to add data-cy to your components:
![](https://t4648007.p.clickup-attachments.com/t4648007/9042afe8-889b-4238-aa18-104f24ad9ab2/Screenshot%202022-12-27%20at%2012.00.21.png)![](https://t4648007.p.clickup-attachments.com/t4648007/dd314fb8-2247-4758-b73c-92d3453280a3/Screenshot%202022-12-27%20at%2011.59.56.png)

Since the component library got some chances to further boost the use of the `data-cy` attribute there is an important difference to make. All components that got changes now use the `data={{ cy: '`_`name`_`' }}` attribute instead of `data-cy`.

Elements which use `data-cy`: Image, Field, div, FastField, FormikTextField, textarea

Elements which use `data={{ cy: '`_`name`_`' }}`: Button,

Neither ➝ needs data_cy as Prop (manually added to the component): ContentInput

# Write Tests

Every test should be placed in a file called _`descriptive-name`_`.cy.ts` . These files need to be placed in the folder structure, which is automatically generated when installing Cypress. When running the tests, all files with the ending `.cy.ts` will be run automatically, as long as no specific files are targeted.

### General Structure

Each test file should have the following basic structure:

![](https://t4648007.p.clickup-attachments.com/t4648007/5a5a9b35-80a4-41a8-8ab9-1d5009140bbd/Screenshot%202022-12-28%20at%2014.29.48.png)

- `describe` marks the beginning of the test suite in that file and the outer function containing each single test. It is possible to have more than one `describe` function per file, but since it serves as one test suite it is recommended to only use one of them per file.

### BeforeEach Hook

This hook can be used to store some general commands that should be run before each single test in that file. For example if we are targeting the lecturers website of the klicker in a certain test suite, we could run the following beforeEach hook:

![](https://t4648007.p.clickup-attachments.com/t4648007/11eb7a21-ae97-4278-a0e9-2c0725320b75/Screenshot%202022-12-28%20at%2014.38.04.png)

- This allows us to login into the account of the lecturer before each single test, which makes the code in each single test a little bit smaller.

### Single Test

Each test should only target a single workflow and not depend on other tests. This is important for the test to not fail just because a previous test failed. You can list the chain of commands needed for a test in a `it` function and Cypress will automatically run one `it` function after the other.

This is how a single test could look like:![](https://t4648007.p.clickup-attachments.com/t4648007/ab8cd131-22e7-403c-8640-b847b69f2195/Screenshot%202022-12-28%20at%2014.41.17.png)

- The test is broken up into two parts. First the workflow we need to perform (First two commands) and then some checks (Last commands) to evaluate if we reached the wanted screen or output after executing the workflow.

A list of the most important commands used in the tests can be found in `Cypress Commands`.

All shown examples are form the Klicker code.

# General commands

The following commands are generally used to find elements in your DOM.

## cy.get()

Detailed description [here](https://docs.cypress.io/api/commands/get).

This is the most commonly used command and allows you to find any element in your DOM that contains a `data-cy` , `data={{ cy: '`_`name`_`' }}` or `data_cy` attribute. For further reference I will use `data:cy` in case I mean both variants of the attribute.

`cy.get('[data-cy="`_`data-cy-attribute-name`_`"]');`

After the element is found you can perform actions on it by chaining other commands, for example `click()` or `type()` (Further details below).

If this command is able to find multiple elements (which it can do!) you can further select them by chaining `eq()`(Further details below).

## cy.findByText()

Detailed description [here](https://testing-library.com/docs/queries/bytext).

Sometimes it is useful to select an element via the text that is written in it. This should only be used if that text is no subject to change!

Be mindful of the structure of the elements! It is often the case that the text element is not exactly the element you want to perform an action on but a child of that element. In that case you have to chain further commands (like parents(), children(), siblings(); further details below) to findByText() to access the desired element. Despite this being very useful, it is a very fragile construct since one single added div can change the DOM structure in a way that the test doesn't work anymore.

The following is an example, where we want to write text into an input field. Since we can't find the element via their `data:cy` attribute we need to find it via text. But the element containing the text is not exactly the input field, but a child of it. That is why we have to chain some parent() commands to find the desired input field in the DOM.

`cy.findByText('The text you want to find').parent().parent().parent().type('100%');`

Be wary that this function only finds the **exact** text in the DOM. So if you are searching for the text "The text you want to find" with this query: `cy.findByText('The text you')`

Cypress won't find any elements.

## cy.findAllByText()

Detailed description [here](https://testing-library.com/docs/queries/bytext).

In case there are multiple texts of the same kind you have to use this function instead of using `cy.findByText()` which would give you the following error: _Timed out retrying after 4000ms: Found multiple elements with the text_.

Apart from that all the rules from `cy.findByText()` apply also to this function. Additionally, you get not a single elements but an array of elements back. This means you can't directly perform a `click()` or `type()` action on this array, but must select one element from the array first with the `eq()` command (Further details below).

`cy.findAllByText('The text you want to find').eq(1).click();`

## .children() / .parents() / .siblings()

Detailed description [here](https://docs.cypress.io/api/commands/children), [here](https://docs.cypress.io/api/commands/parents) and [here](https://docs.cypress.io/api/commands/siblings).

These commands can be chained to one of the three commands above used for finding elements. Although they are very useful to "navigate" through the DOM to find child-, parent- or sibling-elements they are rather weak whenever changes to the code are made. Try to avoid them generally.

_Important_:

`children()`, `parents()` and `siblings()` can yield multiple elements. In that case an additional `eq()` is necessary to select one of the found elements for further chaining.

`cy.findByText(questionTitle).parent().parent().children().eq(1).click();`

`parent()` only yields one element (and only works if one parent element exists). Thus, no eq() chain is necessary.

`cy.findByText(questionTitle).parent().click();`

## .eq()

Detailed description [here](https://docs.cypress.io/api/commands/eq).

Whenever multiple elements are yielded by `get()`, `findAllByText()`, `parents()`, `children()` or `siblings()` you can select the desired element from that array with eq(). Just insert the correct index and you can chain further commands as you like.

`cy.get('[data-cy="select-question-type-div"]').children().eq(1).click();`

The components are counted in a top-down manner, which is why, although `eq()` is really powerful, it should be avoided if possible. Any changes to the DOM that swap elements around will lead to a failing test, unless the index numbers for `eq()` are also changed.

# Workflow commands

Almost every line in your code needs one of this commands, since most actions are performed using a click or typing in fields.

## .click()

Detailed description [here](https://docs.cypress.io/api/commands/click).

`click()` can be performed on any clickable elements in the DOM. There exist other variants of this command, for example `dblclick()` or `rightclick()`.

`cy.get('[data-cy="`_`data-cy-attribute-name`_`"]').click();`

Remember that buttons don't take `data-cy`, but `data={{ cy: '`_`name`_`' }}` as an attribute.

## .type()

Detailed description [here](https://docs.cypress.io/api/commands/type).

Any text fields can be filled with `type()`.

`cy.get('[data-cy="`_`data-cy-attribute-name`_`"]').type('Any text?);`

# Check commands

These commands are generally used when checking for the right state of the DOM at the end of a test. However, it can happen that these commands need to be used during the workflow part of a test to access certain components.

## .should()

Detailed description [here](https://docs.cypress.io/api/commands/should).

`.should()` creates an assertion. Assertions are automatically retried until they pass or time out. The command is always chained to a `get()` or `findBy...()` command and checks if the targeted element meets the criteria that it _should_ have.

There are numerous criteria that can be checked for. [Here](https://docs.cypress.io/guides/references/assertions#Chai) is some documentation on the possibilities and below three examples of how the criteria should be used in the code.

`cy.get('[data-cy="`_`data-cy-attribute-name`_`"]').should('exist');`

`cy.get('[data-cy="`_`data-cy-attribute-name`_`"]').should('have.length', 1);`

`cy.get('[data-cy="`_`data-cy-attribute-name`_`"]').should('have.text', 'Some text');`

The first example asserts that the element is present in the DOM. The second one asserts that the number of elements in the DOM that have the `data-cy` attribute _`data-cy-attribute-name`_ assigned, is only one. The third one asserts that an element has exactly the text `Some text`. Any additional text in the element will lead to a failed assertion.

## .contains()

Detailed description [here](https://docs.cypress.io/api/commands/contains).

.contains() gets the DOM element containing the text specified in the brackets. DOM elements can contain *more* than the desired text and still match.

`cy.get('[data-cy="`_`data-cy-attribute-name`_`"]').contains('Some text');`

# Further Resources

- [https://www.youtube.com/watch?v=5ajwAkZDbwo](https://www.youtube.com/watch?v=5ajwAkZDbwo)
