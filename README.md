# Vtiger CRM – Playwright Automation Framework

A UI test automation project for the Vtiger CRM application, built with **Playwright**, **JavaScript**, and the **Page Object Model (POM)**. The suite covers common CRM record-creation workflows across 12 modules, along with authentication setup and logout.

> **Project status:** This repository contains the automation framework and its CI pipeline definitions. The test suite has not been independently executed as part of this repository review. Actual test results depend on a reachable Vtiger CRM instance, suitable CRM test data, and correctly configured credentials.

## Table of Contents

- [Project Overview](#project-overview)
- [Technology Stack](#technology-stack)
- [Automated Workflows](#automated-workflows)
- [Framework Design](#framework-design)
- [Repository Structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Setup and Configuration](#setup-and-configuration)
- [Running the Tests](#running-the-tests)
- [Authentication and Storage State](#authentication-and-storage-state)
- [Reports and Debugging](#reports-and-debugging)
- [CI/CD Integration](#cicd-integration)
- [Test Data and Environment Notes](#test-data-and-environment-notes)
- [Known Limitations and Next Improvements](#known-limitations-and-next-improvements)
- [Author](#author)

## Project Overview

This project automates selected Vtiger CRM user workflows through browser-based tests. It is organized around reusable page-object classes, a shared Playwright fixture, external JSON test data, and a dedicated authentication setup.

The current test suite contains 13 test definitions: 12 record-creation workflows and one logout workflow. The record-creation workflows cover Leads, Organizations, Contacts, Opportunities, Products, Quotes, Sales Orders, Invoices, Vendors, Price Books, Purchase Orders, and Trouble Tickets.

The goal is to demonstrate practical UI automation concepts, including reusable locators, page-object separation, Playwright fixtures, authentication state reuse, assertions, popup handling, test-data separation, HTML reporting, and CI pipeline configuration.

## Technology Stack

| Technology | Usage |
|---|---|
| JavaScript | Test implementation |
| Node.js | JavaScript runtime |
| Playwright Test | Browser automation and test runner |
| Page Object Model | Separation of page locators from test workflows |
| JSON | External test data |
| dotenv | Environment variable loading |
| GitHub Actions | CI workflow definition |
| Jenkins | Pipeline definition and HTML report publishing |

The project currently configures Chromium as the active browser project. Firefox and WebKit configuration examples are present as commented-out examples and are not active test projects.

## Automated Workflows

| No. | CRM module | Automated workflow |
|---:|---|---|
| 1 | Leads | Create a lead |
| 2 | Organizations | Create an organization |
| 3 | Contacts | Create a contact and associate an organization |
| 4 | Opportunities | Create an opportunity and associate an organization |
| 5 | Products | Create a product |
| 6 | Quotes | Create a quote and associate related CRM records |
| 7 | Sales Orders | Create a sales order and associate related records |
| 8 | Invoices | Create an invoice and associate related records |
| 9 | Vendors | Create a vendor |
| 10 | Price Books | Create a price book |
| 11 | Purchase Orders | Create a purchase order and associate related records |
| 12 | Trouble Tickets | Create a support ticket and associate related records |
| 13 | Authentication | Log out of the CRM application |

The suite uses browser interactions such as navigation, form entry, dropdown selection, popup-window handling, and form submission. It also performs input-value assertions before saving records.

**Important:** Input-value assertions confirm that data was entered into the form. They do not, by themselves, prove that the CRM persisted the record successfully. Post-save record verification should be added to each creation workflow.

## Framework Design

### 1. Page Object Model

The `PageObjectModel/` directory contains classes that encapsulate page-specific locators. The main test file uses these page objects instead of defining all locators inline.

This separation makes it easier to update locators and reuse page interactions when the application UI changes.

### 2. Custom Playwright fixtures

The `tests/fixtures.js` file extends the Playwright test object and provides page-object instances such as `homePage`, `leadsPage`, `organizationPage`, `contactsPage`, and the other module page objects.

Tests receive these objects through Playwright's fixture mechanism.

### 3. Authentication setup and storage state

The `tests/auth.setup.js` file performs the login flow, checks that the CRM Leads link becomes visible, and saves the authenticated browser context to:

`playwright/.auth/user.json`

The Chromium project depends on the setup project and uses this saved storage state. The authentication file is excluded from version control through `.gitignore`.

### 4. External test data

The `testData/` directory contains JSON files for the module workflows. Test data is imported by `tests/CRM5.spec.js`, keeping much of the form data separate from the test implementation.

### 5. Reporting and failure artifacts

The Playwright configuration uses the HTML reporter, captures screenshots on failure, retains video on failure, and records a trace on the first retry. These artifacts help investigate test failures.

## Repository Structure

```text
PLAYWRIGHT_PROJECT_CRM_APPLICATION/
│
├── .github/
│   └── workflows/
│       └── playwright.yml
│
├── PageObjectModel/
│   ├── login.page.js
│   ├── signin.page.js
│   ├── home.page.js
│   ├── 01_createLeads.page.js
│   ├── 02_createOrg.page.js
│   ├── 03_createContacts.page.js
│   ├── 04_createOppor.page.js
│   ├── 05_Product.page.js
│   ├── 06_createQuotes.page.js
│   ├── 07_createSalesOrder.page.js
│   ├── 08_createInvoice.page.js
│   ├── 09_createVendor.page.js
│   ├── 10_createPriceBook.page.js
│   ├── 11_createPurchaseOrder.page.js
│   └── 12_createTicket.page.js
│
├── tests/
│   ├── CRM5.spec.js
│   ├── auth.setup.js
│   └── fixtures.js
│
├── testData/
│   ├── 01_Leads.json
│   ├── 02_organization.json
│   ├── 03_contacts.json
│   ├── 04_opportunities.json
│   ├── 05_Product.json
│   ├── 06_Quotes.json
│   ├── 07_SalesOrder.json
│   ├── 08_Invoice.json
│   ├── 09_Vendor.json
│   ├── 10_PriceBook.json
│   ├── 11_PurchaseOrder.json
│   ├── 12_Ticket.json
│   └── login.json
│
├── screenshot/
├── Jenkinsfile
├── package.json
├── package-lock.json
├── playwright.config.js
└── .gitignore
```

## Prerequisites

Before running the tests, make sure the following are available:

- Node.js and npm.
- Git.
- A running and reachable Vtiger CRM instance.
- A valid CRM user account with permission to create the records covered by the tests.
- Network access from the machine running Playwright to the CRM application.

The current repository configuration uses environment variables for the CRM URL and login credentials. The CRM must be available at the configured URL; this project does not provision or start a CRM server.

## Setup and Configuration

### 1. Clone the repository

```bash
git clone https://github.com/nandagopalm01-byte/PLAYWRIGHT_PROJECT_CRM_APPLICATION.git
cd PLAYWRIGHT_PROJECT_CRM_APPLICATION
```

### 2. Install dependencies

```bash
npm ci
```

### 3. Install the Chromium browser

```bash
npx playwright install chromium
```

On Linux CI machines, install the browser and its operating-system dependencies with:

```bash
npx playwright install --with-deps chromium
```

### 4. Configure environment variables

Create a local `.env` file in the repository root:

```env
Base_URL=http://your-vtiger-host/index.php
USER_NAME=your_crm_username
USER_PASSWORD=your_crm_password
```

Replace the example URL and credentials with values for your own CRM test environment. The exact login URL must be compatible with your Vtiger installation.

Do not commit the `.env` file or real credentials. The repository's `.gitignore` already excludes `.env` files.

### 5. Check the CRM test data

Review the JSON files in `testData/` and ensure that any referenced related records already exist in the CRM instance. Examples used by the current tests include organization, contact, vendor, product, quote, and shipping-carrier records.

The current tests use fixed values and references to existing CRM data. A clean CRM installation may not contain these records.

## Running the Tests

The current `package.json` does not define npm test scripts. Use the Playwright CLI commands below.

Run the complete configured test suite:

```bash
npx playwright test
```

Run the suite in headed mode:

```bash
npx playwright test --headed
```

Run the Chromium project explicitly:

```bash
npx playwright test --project=chromium
```

Run a particular test file:

```bash
npx playwright test tests/CRM5.spec.js
```

Run a test by its title:

```bash
npx playwright test -g "01 - Leads"
```

Run with Playwright Inspector:

```bash
npx playwright test --debug
```

The authentication setup is configured as a project dependency. When the Chromium project runs, Playwright executes the setup project first and then uses its saved storage state.

## Authentication and Storage State

The authentication setup is implemented in `tests/auth.setup.js`. It reads `Base_URL`, `USER_NAME`, and `USER_PASSWORD` from the environment, performs login, checks for the CRM Leads link, and writes the authenticated storage state to `playwright/.auth/user.json`.

The storage-state directory is ignored by Git because it can contain session credentials. Do not publish storage-state files, cookies, authentication tokens, or other session data.

If authentication fails, check that the CRM URL is reachable, credentials are correct, the account can access the CRM, and the environment variables are available to the process running Playwright.

## Reports and Debugging

The project configures the Playwright HTML reporter. After a test run, open the report with:

```bash
npx playwright show-report
```

The configuration also sets:

| Artifact | Current configuration |
|---|---|
| HTML report | `reporter: 'html'` |
| Screenshot | `only-on-failure` |
| Video | `retain-on-failure` |
| Trace | `on-first-retry` |
| CI retries | 2 retries when `CI` is truthy |
| Local retries | 0 |

The report and failure artifacts are useful for investigating failed locators, navigation problems, application errors, and timing issues.

## CI/CD Integration

### GitHub Actions

The workflow definition is located at:

`/.github/workflows/playwright.yml`

It is configured to run on pushes and pull requests targeting `main` or `master`. The workflow checks out the repository, sets up Node.js, installs dependencies, installs Playwright browsers, runs the test suite, and uploads the Playwright report directory as an artifact.

**Environment configuration is required before this workflow can execute successfully.** Configure the CRM URL and credentials as GitHub Actions repository or environment secrets, then map them to the process environment for the test step. The workflow runner must also be able to reach the CRM host. A CRM instance running at `localhost` on a developer's computer is not automatically accessible from a GitHub-hosted runner.

### Jenkins

The repository root contains a `Jenkinsfile` with stages for source checkout, dependency installation, Chromium installation, and Playwright test execution. Its post-build section is configured to publish the Playwright HTML report.

The pipeline expects Jenkins credentials with the IDs `CRM_BASE_URL`, `USER_NAME`, and `USER_PASSWORD`. Create these credentials in Jenkins and use an agent compatible with the pipeline's Windows `bat` commands. The Jenkins agent must be able to access the CRM application.

The HTML publishing stage also requires the Jenkins HTML Publisher plugin.

## Test Data and Environment Notes

The current tests use static JSON data. Some workflows select related records by visible names in popup windows. Consequently:

- The CRM database must contain the required related records before the tests run.
- Re-running record-creation tests may create duplicate records unless the CRM permits duplicates or data is cleaned up.
- Tests that select records by fixed names can fail if those records are missing, renamed, duplicated, or inaccessible to the test user.
- The current suite should be treated as an environment-dependent demonstration until test data setup and cleanup are automated.

Use a dedicated non-production CRM instance and test account. Do not point record-creation automation at a production CRM environment.

## Known Limitations and Next Improvements

The following improvements are recommended to make the framework more reliable and maintainable:

1. **Verify records after saving.** Add assertions against the saved record's detail or listing page, including unique fields such as email or record name. Current pre-save input assertions do not establish persistence.
2. **Make tests independent.** Avoid relying on records created manually or by another test. Generate unique test data and establish related records through controlled setup.
3. **Add test-data cleanup.** Remove records created by a run where the application and test environment permit safe cleanup.
4. **Configure CI secrets and CRM reachability.** Ensure GitHub Actions and Jenkins receive the required URL and credential variables and can access the CRM environment.
5. **Improve locator resilience.** Prefer accessible role, label, and placeholder locators where possible; avoid brittle positional selectors and unnecessarily broad XPath expressions.
6. **Improve screenshot naming and evidence collection.** The current test file uses a repeated screenshot path in multiple workflows, which can overwrite earlier images. Prefer Playwright failure artifacts and uniquely named evidence where needed.
7. **Expand functional coverage.** Add negative login tests, required-field validations, invalid input cases, duplicate-record checks, and edit/delete workflows where supported.
8. **Introduce test tags.** Separate smoke and regression coverage so relevant subsets can be run independently.
9. **Enable additional browsers.** Add Firefox and WebKit after the core suite is stable and the CRM application is verified against those browsers.
10. **Add API-assisted setup.** Use API calls for reliable creation and cleanup of prerequisite records while retaining UI automation for the user-facing workflows.

These are improvement opportunities, not claims that the features are already implemented.

## Author

**Nandagopal M**

GitHub: [nandagopalm01-byte](https://github.com/nandagopalm01-byte)

Project repository: [PLAYWRIGHT_PROJECT_CRM_APPLICATION](https://github.com/nandagopalm01-byte/PLAYWRIGHT_PROJECT_CRM_APPLICATION)
