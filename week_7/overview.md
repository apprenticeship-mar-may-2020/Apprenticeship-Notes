###### Week 7 overview:
https://github.com/codurance/apprenticeship/wiki/Week-7

```
Week 7: Web development
Dependencies:

Week 1 - Test Driven Development
Topic to Cover
HTML5 - semantic HTML
Styling (CSS3)
Javascript (Ecosystem): a simple setup (i.e., webpack)
Modern web development: reactjs (or similar)
REST APIs
Awareness of authentication, security, CDN, logs, etc. Running a backend service in production.
Exit Criteria
“Develop and architect a front-end web application” or “Develop and architect a backend web application”

What is essential to learn from front end?
This is a working draft, should be filled out with links to reading resources / videos / exercises

Assumed knowledge:
no exposure to UX/UI work
TDD
basic familiarity with HTML
We have one week to build foundations of front end, this week will happen towards the end of the apprenticeship. The skills touched on here will be practised in a project at the end of the apprenticeship.

Basics of UX and user journeys
To work effectively in UI development, it is important that we have a common understanding with designers.

How do we model and design user experiences?
"Don't make me think" principles
Wireframing and prototyping
graphic design theory
Mobile First
Accessibility
HTTP and networking (further reading https://hpbn.co/)
IP, TCP, HTTP, TLS etc.
request / response flow
typical architecture and CDN / edge cloud networks e.g. fastly, load balancers e.g. alohas
HTML5 - semantic HTML
DOM and how it works - web browser internals, page lifecycle
most commonly used HTML5 elements and how to use them (semantic html)
A look at the document head and now resources are fetched / styles are applied / scripts are executed
The HTML5 specification and living standard - how things become a standard
Differences between browsers and how to find information on feature support
Styling -
render lifecycle
CSS selectors
box model
simple properties && values / units (background, color, font-family, font-size, margin, padding, border, display, line-height, text-align, text-decoration)
how to layout a page (display: flex) (flex-box frogger!)
Some summarisation of the following:

CSS normalise / resets
browser differences
SCSS | less preprocessors
overview of how they work in modern web frameworks (CSS in JS frameworks)
responsiveness and media queries, css grid
exercise - build a static html & css clone of well known website: e.g. twitter

For css have a look at: http://www.csszengarden.com

Javascript -
walkthrough ecosystem setup: nvm, npm, node modules & package
introduction to some test library (jest? ava? jest is used by create-react-app...)
Start functional - don't jump straight to classes
TDD a kata
Modern web development -
polyfilling / transpiling
react (create-react-app) *pick the best tutorial we can find
can we teach some of this from the devTools? - get people familiar with using them. (can we find a debugging task?)
There is also.... react-native, electron
day 1:
writing good html (https://www.w3schools.com/tags/) https://websitesetup.org/html5-cheat-sheet/ :
- semantics
- accessibility (a11y)
- what goes in <head/>
- including stylesheets
- including script tags - position in page, async
- separation of styling and markup
introduction to css
layouts with flexbox (http://flexboxfroggy.com/)
exercise: build a "circle ci dashboard" in html and css.
##  testing css backstop.js

day 2:
Extreme learning: TCP/IP - HTTP (SSL)
- HTTP VERBS
- HEADERS
- Local State: Cookies, Local Storage, GDPR concerns
- look at what happens with a request in wireshark
- can you write a simple HTTP request by hand
Extreme learning: performance - further reading HPBN (https://hpbn.co/)
- cover meaningful metrics for user experience of performance
- cover technical basis (latency, bandwidth, typical request patterns of an html page
- how do caching and CDN's fit into this (e.g. cache control headers)
- can you look at a website in devtools and understand how to determine critical paths and what is relevant to user experience
React.js and js/web ecosystems (node, webpack, testing) introduction to React.js and ecosystem use create-react-app to bypass as much possible javascript ecosystem faff. introduce package.json for dependencies and scripts test driving a react application with jest and enzyme (default un CRA) exercise - recreate "build a "circle ci dashboard" from yesterday with react.js using json file for source data

Deploying to Heroku:

Create an account on Heroku
Create an app in Heroku
Download the Heroku CLI
Add a Procfile to the git repo containing: web: npm start
heroku login
heroku git:remote --app APP_NAME_HERE
git push --set-upstream heroku master
Day 3
Test driving react: https://medium.com/codeclan/testing-react-with-jest-and-enzyme-20505fec4675
Control State: https://www.valentinog.com/blog/react-redux-tutorial-beginners/
Testing React/Redux: https://alligator.io/react/testing-react-redux-with-jest-enzyme/
Build a dashboard for Circle CI: https://circleci.com/docs/api/v1-reference/
Also have a look at:

https://testdriven.io/tdd-with-react-jest-and-enzyme-part-one
day 4 - 5
security - owasp game? https://github.com/StaymanHou/Hacking-the-Pentest-Tutor-Game
Using a cdn - experiment locally - Varnish cache - https://varnish-cache.org/intro/
basic server in node (we used express)
node backend
- serve up an application and REST endpoints
build a build a "circle ci dashboard" using the circle API.
should have a backend server handling authentication, frontend written in react, styling
to consider - essential parts of dom API document.getElementBy... document.querySelectorAll document.createElement document.XMLHttpRequest etc.
Express webserver: https://expressjs.com/en/starter/installing.html https://expressjs.com/en/guide/writing-middleware.html

REST calls from react: https://hackernoon.com/tutorial-how-to-make-http-requests-in-react-part-1-f7afa3cd0cc8

DOM API https://www.w3schools.com/js/js_htmldom.asp

If you want to know more about javascript: https://github.com/getify/You-Dont-Know-JS
```