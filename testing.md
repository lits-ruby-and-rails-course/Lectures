Testing
====================

***Man, I just love unit tests, I've just been able to make a bunch of changes to the way something works, and then was able to confirm I hadn't broken anything by running the test over it again.***

> **Types:**

>- Unit tests;
>- Acceptance tests;



Unit tests
----------------

The lowest and most important level. Confirm the correct behavior of isolated units of functionality:

- Classes
- methods
- functions

***and run extremely fast (milliseconds).***


Tools: 

- RSpec
- MiniTest
- Jasmine(JavaScript)

Unit tests
----------------

Acceptance tests — The high level (typically user-level) tests.

Since they run a lot more code than unit tests and often depend on external services they are much slower (seconds or minutes).

Tools:

- RSpec and Capybara
- Cucumber
- Selenium

----------------
----------------

RSpec
====================

> **Topics:**

>- Install Rspec;
>- Basic Structure;
>- Configuration & Matchers;
>- DRY Specs;
>- Hooks & Tags;
>- Mocks & Stubs;
>- Custom Matchers;
