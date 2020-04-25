##### Andrzej made some interesting points regarding BDD:
```
But BDD isn't about testing... It's about establishing a "shared understanding of behaviour"... 
It's like higher-level "extension" of TDD to include everyone involved, using "human readable" specifications that are understood and worked on by POs/customers, 
testers and developers together in the phases of:
* Discovery
* Formulation
* Automation

We do these over and over again to build up "slices" (hence the name Cucumber!) of 
business value, with valuable working software at the end of each cycle.
```
He also added:
```
Just had a look at my BDD notes...

Basically the three stages are:
1. DISCOVERY - "3 Amigos" workshops where at least one of each tester, PO and developer take a user story and walk through some concrete examples of wanted behaviour to discover:
- "What we know"
- "What we want to find out"

2. FORMULATION - testers and developers pair to document as many examples as we need as "scenarios" written in a more "formal syntax" like Gherkin, as used by Cucumber... "Given, When, Then...". This becomes the "executable specification for the software"... and a "living documentation"

3. AUTOMATION - Developers, with or without testers write the code that executes the formulated steps in the scenarios

Then we work to make them pass! Theoretically, if they pass then the software is doing what we all agreed it should do (and we can see the progress, as failing scenario steps... and then whole scenarios begin to pass...) and if they start to fail we know that either we have introduced unexpected behaviour or the business requirements have changed so we fix one or the other.
But yeah, this does not replace TDD, but complements it :-)
```
This is a very clear way of working and seems to signal a healthy company environment. Here PO stands for Product Owner.

I was able to show some points regarding transformers:
```
Great! This kata is quite interesting for a starting point also:
https://github.com/sandromancuso/salaryslipkata

Also transformers are quite interesting for use with tables as we tried here:
https://github.com/TomSpencerLondon/Cucumber-EMVS/tree/master/src/test/java/acceptance

We can definitely start on these after the classical TDD revision. Looking forward to it!
```

I also started to see the point about Cucumber testing:
```
Dope. Great! See you at 4.00. Yes. I am starting to see the point here. Cucumber 
tests help to summarise and inform business decisions and the user experience of the 
applications we build. Thank you!
```