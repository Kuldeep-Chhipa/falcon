# How to write automation scenario in Node.js

This is a step by step guide to writing automation scenario in Node.js. The document covers **Browser Automation** to perform end-to-end testing for Web Applications.

```
In this help file, Nightwatch has been used for Browser Automation. Nightwatch is written in Node.js and uses the W3C WebDriver API for interacting with various browsers.
```

For Browser and API automation, the process can be divided into the following:

* Installation: This covers the installation requirements.
* Configuration: This covers the configuration requirements.
* Execution: This covers the execution steps.

## Browser Automation

To cover the browser automation scenarios, you are required to perform following sequential steps:

### Installation
You are required to install the following:

**Step-1** Install Node.js
Please refer [Node.js](https://nodejs.org/en/) website to install Node.js for your Operating systems. 

```
Remember to install the npm tool, which is the node package manager and is distributed with the Node.js installer.
```
**Step-2** Install Nightwatch

To install the latest version of Nightwatch using the npm command-line tool, run the following:

```
$npm install nightwatch
```

* add -g option to make nightwatch runner available globally in your system.
* add --save-dev option to save nightwatch as a devDependency in your package.json.

**Step-3** Install WebDriver

You need to use a specific WebDriver package for the target browser.  In this document, we are going to install WebDriver for Chrome browser.

Install the [ChromeDriver](https://nightwatchjs.org/gettingstarted/installation/#install-chromedriver) using the following:

```
$npm install chromedriver --save-dev
```

**Step-4** Install Selenium Server

```
npm install selenium-server --save-dev
```
### Configuration

**Step-1** Configure nightwatch.json

The nightwatch test runner binary expects a configuration file, using by default a nightwatch.json file from the current working directory. A nightwatch.conf.js file will also be loaded by default, if found.

**Step-2** Create the nightwatch.json in the project's root folder. Assuming you have downloaded or installed the ChromeDriver service, the simplest nightwatch.json file will look like this, where node_modules/.bin/chromedriver is the path where ChromeDriver is installed:

```
{
  "src_folders" : ["tests"],

  "webdriver" : {
    "start_process": true,
    "server_path": "node_modules/.bin/chromedriver",
    "port": 9515
  },

  "test_settings" : {
    "default" : {
      "desiredCapabilities": {
        "browserName": "chrome"
      }
    }
  }
}

```

### Execution


**Writing Tests**

Create a separate folder for tests in your project, e.g.: tests. Each file inside it will be loaded as a test suite by the Nightwatch test runner.

**Basic Example**
Here's a basic test suite example which opens the search engine Ecosia.org, searches for the term "nightwatch", then verifies if the term first result is the Nightwatch.js website.

```
module.exports = { 'Demo test ecosia.org' : function (browser) { browser .url('https://www.ecosia.org/') .waitForElementVisible('body') .assert.titleContains('Ecosia') .assert.visible('input[type=search]') .setValue('input[type=search]', 'nightwatch') .assert.visible('button[type=submit]') .click('button[type=submit]') .assert.containsText('.mainline-results', 'Nightwatch.js') .end(); } };
```

A test can have multiple steps, if needed:

```
module.exports = { 'step one: navigate to ecosia.org': function (browser) { browser .url('https://www.ecosia.org') .waitForElementVisible('body') .assert.titleContains('Ecosia') .assert.visible('input[type=search]') .setValue('input[type=search]', 'nightwatch') .assert.visible('button[type=submit]'); }, 'step two: click submit' : function (browser) { browser .click('button[type=submit]') .assert.containsText('.mainline-results', 'Nightwatch.js') .end(); } };
```

**Finding & Interacting with Elements**

Finding elements on a page is by far one of the most common functions during an end-to-end test. Nightwatch provides several techniques of locating elements and also an extensible assertion framework to perform verifications on them.

Elements are searched for from the document root, using either a CSS selector or an Xpath selector. It is possible to use other locator strategies as well, refer to the Webdriver docs for details.

Elements are internally identified using a web element reference id. When interacting with elements, this step happens behind the scenes and Nightwatch has automatic retry mechanisms for locating the element before interacting with it or performing assertions.

In the example above, the command setValue is first performing internally the element lookup and then calling the element set value command. The test can be simplified like so:

```
module.exports = { 'Demo test ecosia.org': function (browser) { browser .url('https://www.ecosia.org/') .setValue('input[type=search]', 'nightwatch') .click('button[type=submit]') .assert.containsText('.mainline-results', 'Nightwatch.js') .end(); } };
```

For more info on element related commands, see the [API Reference](https://nightwatchjs.org/api/commands/#elements-headline) pages.

**Writing Assertions**
Assertions determine if the test passes or not. Assertions are different than commands in the sense that assertions may use one or more commands internally.
Assertions are also automatically retried using the same mechanism as for implicit waiting on finding elements.
In the example above there are four assertions: waitForElementVisible, assert.atitleContains, assert.visible on two separate elements, and assert.containsText.

Assertions and their status (pass/fail) are displayed in the terminal. Commands (such as setValue or url) are only shown in the verbose output.










