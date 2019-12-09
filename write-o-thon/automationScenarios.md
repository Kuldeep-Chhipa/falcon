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

**Assertion Failures**

When an assertion fails, an AssertionError is thrown and the Nightwatch test runner detects it and marks the entire test as failed.
A single test can have multiple assertions and, by default, if an assertion happens to fail the rest of the test is aborted. If the test suite has multiple steps or multiple testcases, the rest of steps/testcases will also be skipped.

**Controlling Behaviour on Failure**

Sometimes it is desirable for the rest of the assertions to continue, even if an assertion happens to fail mid-way. This can be achieved in several ways:

1. Using .verify instead of .assert - the assertion failure will be logged but the test execution will continue; the test is still marked as failed at the end;

2. Using element selector objects and set abortOnFailure to false, e.g.:
browser.setValue({selector: 'input[type=search]', abortOnFailure: false}, 'nightwatch')

Running the remaining test steps/test cases is possible by setting "skip_testcases_on_fail" to false in your configuration file. Refer to the Configuration section for details.

**Ending the Session on Failure**

By default, when an assertion fails, the test runner ends the session and closes the browser window so it can continue with the remaining test suites.

However, if you wish to keep the browser window open whenever an assertion failure occurs, you can set "end_session_on_fail" to false. This can be useful for performing debugging.

Refer to the Configuration section for details.

**Using BDD describe** [beta]

Version 1.3 brings native support in Nightwatch for using the popular BDD interface for writing tests. No further configuration is necessary.

It is possible to run tests written in BDD describe and Exports interfaces together. Prior to this version, users had to use the Mocha test runner but now it is possible without additional plugins or libraries. The BDD interface in Nightwatch provides the common describe(), context(), test(), it(), specify(), before(), after(), beforeEach(), and afterEach() functions.

At this point Nightwatch doesn't support nested describe/context declarations. Only the top-level one is supported, which defines the name for the test suite.

**Basic Example**

```
describe('Ecosia', function() { test('demo test', function(browser) { browser .url('https://www.ecosia.org/') .setValue('input[type=search]', 'nightwatch') .click('button[type=submit]') .assert.containsText('.mainline-results', 'Nightwatch.js') .end(); }); });
```

In addition to the usual BDD syntax, Nightwatch provides a few ways for defining own behaviour.

- **Test suite specific capabilities**

```

describe('homepage test with describe', function() {
  // testsuite specific capabilities
  this.desiredCapabilities = {
    browserName: 'firefox'
  };

  test('...', function() {...});
});

```

- **Test suite specific retries**

```

describe('homepage test with describe', function() {
  // how many time to retry a failed testcase inside this test suite
   this.retries(3);

   // how many times to retry the current test suite in case of an assertion failure or error
   this.suiteRetries(2);

   test('...', function() {...});
});
```

- **Complete BDD Syntax**

```
describe('homepage test with describe', function() {
  // testsuite specific capabilities
  // this.desiredCapabilities = {};

  // Enable this if the current test is a unit/integration test (i.e. no Webdriver session will be created)
  // this.unitTest = false

  // Set this to false if you'd like the browser window to be kept open in case of a failure or error (useful for debugging)
  // this.endSessionOnFail = true

  // Set this to false if you'd like the rest of the test cases/test steps to be executed in the event of an assertion failure/error
  // this.skipTestcasesOnFail = true

  // Set this to true if you'd like this test suite to be skipped by the test runner
  // this.disabled = false

  // this.retries(3);
  // this.suiteRetries(2);

  // Control the assertion and element commands timeout until when an element should be located or assertion passed
  // this.timeout(1000)

  // Controll the polling interval between re-tries for assertions or element commands
  // this.retryInterval(100);

  before(function(browser) {
    this.homepage = browser.page.home();
  });

  test('startHomepage', () => {
    this.homepage.navigate();
    this.homepage.expect.section('@indexContainer').to.be.not.visible;
  });


  // Run only this testcase
  /
  test.only('startHomepage', () => {
    this.homepage.navigate();
  });
  / 

  // skipped testcase: equivalent to: test.skip(), it.skip(), and xit()
  xtest('async testcase', async browser => {
    const result = await browser.getText('#navigation');
    console.log('result', result.value)
  });

  test('version dropdown is enabled', browser => {
    const navigation = this.homepage.section.navigation;
    const navbarHeader = navigation.section.navbarHeader;

    navbarHeader.expect.element('@versionDropdown').to.be.enabled;
  });

  after(browser => browser.end());
});
```
**Using ES6 async/await** [beta]

Starting with version v1.1 it is also possible to write tests as an ES6 async function. Simply by doing so, it will enable the API commands to return a promise and thus making it possible to use the await operator to retrieve the result, instead of the callback, as it is now.

This greatly improves the readability and ease of writing of tests. However, please note that it will no longer be possible to chain the API commands when using an async function.

```
module.exports = { 'demo test async': async function (browser) { // get the available window handles const result = await browser.windowHandles(); console.log('result', result); // switch to the second window // await is not necessary here since we're not interested in the result browser.switchWindow(result.value1); } };
```

**Callbacks and async testcases**

Callbacks can still be used as before and if the callback returns a Promise, the result of the promise will be the result of the command. See below for an example:

```
module.exports = { 'demo test async': async function (browser) { // get the available window handles const value = await browser.windowHandles(function(result) { // we only want the value, not the entire result object return Promise.resolve(result.value); }); console.log('value', value); // switch to the second window browser.switchWindow(value1); } };
```

**Using XPath selectors**

Nightwatch supports xpath selectors also. To switch to xpath instead of css selectors as the locate strategy, in your test call the method useXpath(), as seen in the example below. To switch back to CSS, call useCss().

To always use xpath by default set the property "use_xpath": true in your Configuration.

```
module.exports = { demoTest: function (browser) { browser .useXpath() // every selector now must be xpath .click("//tr[@data-recordid]/span[text()='Search Text']") .useCss() // we're back to CSS now .setValue('input[type=text]', 'nightwatch') } };
```
You can also use xpath directly on a single command or assertion, by passing an element selector object, like so:

```
module.exports = { demoTest: function (browser) { browser .click({ selector: '//tr[@data-recordid]/span[text()='Search Text']', locateStrategy: 'xpath' }) .setValue('input[type=text]', 'nightwatch'); } };
```

**Expect Assertions**

The Nightwatch API supports out of the box a BDD-style expect assertion library which greatly improves the flexibility as well as readability of the assertions.

The expect assertions use a subset of the Expect api from the Chai framework and are available for elements only at this point. Here's an example:

```
module.exports = { 'Demo test Google' : function (browser) { browser .url('https://google.no') .pause(1000); // expect element <body> to be present in 1000ms browser.expect.element('body').to.be.present.before(1000); // expect element <#lst-ib> to have css property 'display' browser.expect.element('#lst-ib').to.have.css('display'); // expect element <body> to have attribute 'class' which contains text 'vasq' browser.expect.element('body').to.have.attribute('class').which.contains('vasq'); // expect element <#lst-ib> to be an input tag browser.expect.element('#lst-ib').to.be.an('input'); // expect element <#lst-ib> to be visible browser.expect.element('#lst-ib').to.be.visible; browser.end(); } };
```

The expect interface provides a much more flexible and fluid language for defining assertions, significantly improved over the existing assert interface. The only downside is that it's not possible to chain assertions anymore.
 
For a complete list of available expect assertions, refer to the API docs.

**Using before[Each] and after[Each] hooks**
Nightwatch provides the standard before/after and beforeEach/afterEach hooks to be used in the tests.
The before and after will run before and after the execution of the test suite respectively, while beforeEach and afterEach are ran before and after each testcase (test step).
All methods have the Nightwatch instance passed as argument.

**Example**

```
module.exports = { before : function(browser) { console.log('Setting up...'); }, after : function(browser) { console.log('Closing down...'); }, beforeEach : function(browser) { }, afterEach : function(browser) { }, 'step one' : function (browser) { browser // ... }, 'step two' : function (browser) { browser // ... .end(); } };

```

**Thanks a lot**






















