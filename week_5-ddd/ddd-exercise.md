##### DDD Exercise_Acme Library
https://github.com/codurance/apprenticeship/wiki/DDD-Exercise_Acme-Library
```
Simion Iulian Belea edited this page on 14 Nov 2018 · 2 revisions
This is an exercise in building a software model a for an imaginary library. The focus should be only on the core domain, which in this case is a system for lending books. These are the rules according to which the library operates:

  Rules:
  - In order to borrow a book one needs to be registered with the library
  - Library has a catalog which says how many of each book is in stock
  - You can only borrow a book that is in stock
  - Only 2 books at a time
  - Books can be borrowed for 3 days
  - returning a book up to a week late incurs £5 fine
  - returning a book more than a week late uncurs £10 fine
  - books can be checked out or renewed at the Circulation Desk
  - a book can only be renewed once
  - Borrower can reserve a book that's not in stock
  in which case a hold is placed on the book and as soon
  as it is returned the borrower is notified and no one else can
  borrow the book.
From these rules - write out scenarios covering examples of the application of each of the rules. The scenarios are written in Given/When/Then format, where Given is the state the system is in before the action is taken, When is the action that is taken (action we want to test), Then is the state of the system (or at least the relevant part of the system) after the action is taken. For example, we have a rule that says: "You can only borrow a book that is in stock"

  Scenario: Borrower is lent a book that's in stock
    Given there is 1 copy of "TDD for Dummies" in stock
    When Jon borrows the book "TDD for Dummies"
    Then Jon should have been lent "TDD for Dummies"
    And there should be no copies of "TDD for Dummies" in stock
The scenarios should be explicit, concrete and should use the ubiquitous business domain language. For example, if the system is being built with librarians in mind and librarians tend to say I've placed a hold on this book rather than I've reserved this book, then the term hold should be used in this context.

By writing scenarios an understanding of the business logic and domain language evolves. The end product is a collection of scenarios that model a particular aspect of the business domain.

By the time we have 5 or 6 scenarios it is time to start trying to translate them into actual code. With a tools such as Cucumber or SpecFlow we can automate these scenarios, keeping in mind the language used in the scenarios and carrying over that language into code.

The end result should be a software model (a collection of classes) of that aspect of the business that is described in the scenarios/examples. This will be a stand-alone model using interfaces at all the points where it talks to infrastructure and/or framework. The model should be usable inside any framework (or without a framework)
```
