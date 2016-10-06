# Codeception

![](http://codeception.com/images/logo.svg)

---

## Codeception

* **full stack** testing framework
* modules to solve **90% of common tasks**
* **unified APIs** for all modules
* **~3M** installations on Packagist

---

## It fits for

* Browser Tests (WebDriver, PHPBrowser over Guzzle)
* Framework Request/Response tests (Symfony/Laravel/etc)
* API tests (SOAP, REST)
* Database Tests (Db, MongoDb, Doctrine, Eloquent)

---

## Acceptance Tests

* Check the real user experience (UI/UX)
* Work using browser through Selenium
* Require database, webserver
* JavaScript included

---

![](img/codeception.gif)

---

## Example

```php
$I->amOnPage('/login');
// filling form element by label
$I->fillField('Username', 'notauser');
$I->fillField('Password', 'kitten');
// clickin button by text
$I->click('Sign in');
// wait ajax-loading indicator to hide
$I->waitForElementNotVisible('#loading');
// do assertion
$I->see('Invalid credentials', '.alert');
```

---

## Functional tests

* Tests HTTP requests/HTML responses
* Perform multiple requests in a row
* Require corresponding framework module
* Use framework's ORM, DI, Auth
* Written in scenario driven manner

---

#### Yii2 Example

```php
// logging in as admin
$I->amLoggedInAs(User::findByUsername('admin'));
// using internal route of Yii2
$I->amOnRoute('tasks/new');
// sending form
$I->submitForm($this->formId, [
    'TaskForm[title]' => 'Fix bug',
    'TaskForm[priroity]' => 'urgent',
]);
// checking values in database
$I->seeRecord('common\models\Task', [
    'task' => 'Fix bug'
]);
```

---

## Unit Tests

* Are the same as in PHPUnit
* To test components in isolation
* Codeception is built on top of PHPUnit
* Just copy your tests to `tests/unit` directory

---

### Integration Tests

* PHPUnit tests enhanced with Codeception modules
* Tests integration between components and external services
* You can use DI and ORM inside of them

---

### Laravel Example

```php
class UserTest extends \Codeception\TestCase\Test
{
    /**
     * @var \UnitTester
     */
    protected $tester;
    public function testRegister()
    {
        $email = 'johndoe@example.com';
        $password = Hash::make('password');
        User::register(['email' => $email, 'password' => $password]);
        $this->tester->seeRecord('users', ['email' => $email, 'password' => $password]);
    }
}
```

---

## API Tests

* Can be executed inside a framework or over HTTP
* REST/SOAP/XML-RPC/Facebook modules
* Match JSON/XML responses **by data and structure**

---

GET /api/tickets/3

```json
{
  "ticket": {
    "id": 3,
    "from": "web",
    "description": "Lorem ipsum...",
    "priority": "important",
    "priority_value": 1,
    "report": {
      "user_agent": "Mozilla...",
      "url": "/tasks",      
      "window": "1280x525",
      "resolution": "1600x1200"
    },
    "reporter_info": {
      "name": "davert",
      "email": "davert@codeception.com",
    }
    "created_at": "2016-08-21T20:16:37Z",
    "updated_at": "2016-09-11T15:13:47Z"    
  }
}
```

---

```php
$I->sendGET('/api/tickets/3');
$I->seeResponseCodeIs(HttpCode::OK); // 200
$I->seeResponseIsJson();
// check data in response
$I->seeResponseContainsJson([
'ticket' =>
  'id' => 3,
  'from' => 'web'
  'report' => [
    'url' => '/tasks'
  ]
]);

```

---


```php
// check the structure of response
$I->seeResponseMatchesJsonType([
'ticket' => [
  'id' => 'integer',
  'description' => 'string|null',
  'priority' => 'string',
  'created_at' => 'string:date',
  'reporter_info' => [
    'email' => 'string:email'
  ]
]]);

```

---

```php
// check existence of element
$I->seeResponseJsonMatchesJsonPath(
  '$ticket.report.url'
);
// via XPath too
$I->seeResponseJsonMatchesXpath(
  '//ticket/report/url'
);

```

---

## BDD Features

* Business specifications written by examples
* Example scenarios in plain English (Cucumber, Behat)
* Scenarios can be executed as tests

---

### Example

```gherkin
Feature: content
  In order to manage content article in the web
  As an owner
  I need to create modify trash publish and Unpublish content article

  Background:
    Given Joomla CMS is installed
    When Login into Joomla administrator with username "admin" and password "admin"
    Then I see administrator dashboard

  Scenario: Create an Article
    Given There is a add content link
    When I create new content with field title as "My_Article" and content as a "This is my first article"
    And I save an article
    Then I should see the "Article successfully saved." message
```

---

## Tests != Features

* Features written for business-team communication
* Features are happy path tests
* Features ⊂ Tests