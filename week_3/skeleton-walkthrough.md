###### Skeleton Walkthrough

This video is useful for AWS deploy:
https://www.udemy.com/course/deploy-java-spring-apps-online/


This video is useful for Jenkins and Continuous Integration:
https://www.udemy.com/course/jenkins-learn-continuous-integration/



This is the link for Walking Skeleton:
https://github.com/codurance/apprenticeship/wiki/Walking-Skeleton


##### This is the description of Walking Skeleton:

Walking Skeleton
Jorge Gueorguiev Garcia edited this page on 4 Aug 2018 · 5 revisions
Do you validate chosen architecture and technologies some time during definition?
If you don’t, why?
If you do, why?
If you do, when?
If you do, how?
In a nutshell
“A Walking Skeleton is a tiny implementation of the system that performs a small end-to-end function. It need not use the final architecture, but it should link together the main architectural components. The architecture and the functionality can then evolve in parallel.”

Alistair Cockburn in “Crystal Clear : A Human Powered Methodology for Small Teams”

A vertical slice through all the architecture layers
Minimal implementation that proves the architecture through some end-to-end functionality
From heavy architectural design to validating chosen architecture early on
Production code, with production level quality and testing required.
Automatically buildable
Automatically deployable
Automaticaly testable
No functionality required
Initial architecture
Why to put the bones together?
Get early feedback if chosen architecture and technology is suitable.
Build quality in, by defining the main architectural structure early on and making it be consistent. (but at the same time, not doing whole bottom layers first).
Allows to set up required development environments, such as CI systems, required load testing tools and so on.
Improve knowledge and work estimations
Give a jumpstart to development by setting the baseline onto which build. (skeleton where to hang the flesh, so to speak).
Synchronizer
Walking skeleton can also work as synchronizer of the work if multiple teams are working on same product. The walking skeleton provides a basic structure and proven way to do things in product where independent teams can then build on.
Exercise
The exercise for the day and probably the following day is to start doing the Shopping Basket kata with a walking skeleton.

The most basic thing to achieve for the walking skeleton will be this:

Continuous Integration with the tool of your choice (Circle CI, TravisCI, Jenkins, ...)
Web frontend with a single button that fetches from a datastore (of your choice) the items that can be added to the basket (two or three will be more than enough)
Display on the screen the data fetched
Deployment of the system into a machine (could be your own, AWS, Azure, ...)
Be able to run and end to end tests that uses the button on the frontend and asserts that the information has come back and is being displayed (remember, it has to come back from the datastore)
After the above is completed, you can continue with the kata as usual.

##### This was the Install and Configure for Tomcat:
https://www.youtube.com/watch?reload=9&v=4Zp5suN1b1E

These were the instructions for defining a server when creating a run/debug configuration:
https://www.jetbrains.com/help/idea/configuring-and-managing-application-server-integration.html
```
Defining a server when creating a run/debug configuration﻿
Open the Run/Debug Configurations dialog (for example, Run | Edit Configurations).

Click icons general add ⌘N, select the server of interest (for example, Tomcat Server) and, if available, select Local or Remote.

In the right-hand part of the dialog, on the Server tab, click Configure to the right of the Application server list.

In the dialog that opens specify the server settings and click OK.

Specify other run/debug configuration settings as necessary and click OK.
```

This was my solution for the Walking Skeleton:
https://github.com/TomSpencerLondon/My-Walking-Skeleton

we used Arnaud's ready build Spark project for this task.

https://dashboard.heroku.com/apps/ancient-tundra-33358/activity

I connected the application here:
https://dashboard.heroku.com/apps/ancient-tundra-33358/deploy/github

This is the site online:

https://ancient-tundra-33358.herokuapp.com/jose

We used this because linking the application through the travis cli failed on this command:
```
travis encrypt $(heroku auth:token) --add deploy.api_key
```
The error was:
```
 ›   Warning: token will expire 03/19/2021
 ›   Use heroku authorizations:create to generate a long-term token
Ignoring executable-hooks-1.6.0 because its extensions are not built. Try: gem pristine executable-hooks --version 1.6.0
Ignoring gem-wrappers-1.4.0 because its extensions are not built. Try: gem pristine gem-wrappers --version 1.4.0
Ignoring libv8-3.16.14.3 because its extensions are not built. Try: gem pristine libv8 --version 3.16.14.3
Ignoring mysql2-0.3.21 because its extensions are not built. Try: gem pristine mysql2 --version 0.3.21
Ignoring nokogiri-1.10.7 because its extensions are not built. Try: gem pristine nokogiri --version 1.10.7
/usr/local/Cellar/travis/1.8.11/libexec/gems/faraday-0.9.2/lib/faraday/request/retry.rb:30: warning: constant ::Fixnum is deprecated
undefined method `try' for #<Faraday::Env:0x00007f8e070acdb8>
for a full error report, run travis report
```

I used this command to kill a heroku app that I had wrongly created:
```
heroku apps:destroy -a sleepy-chamber-3162
```

This was the Travis site for the Walking Skeleton:
https://travis-ci.com/github/TomSpencerLondon/My-Walking-Skeleton


##### Spark project IntelliJIdea:
This video explained Spark with IntelliJ but was for gradle:
https://www.youtube.com/watch?v=KDoZ6TsQHEg

###### This is very useful for a Spark project with Maven:
https://sparktutorials.github.io/2015/04/02/setting-up-a-spark-project-with-maven.html#intellij

IDE Guides
- Instructions for IntelliJ IDEA
- Instructions for Eclipse

About Maven
Maven is a build automation tool used primarily for Java projects. It addresses two aspects of building software: First, it describes how software is built, and second, it describes its dependencies.

Maven projects are configured using a Project Object Model, which is stored in a pom.xml-file.
Here’s a minimal example:
```
<project>
    <!-- model version - always 4.0.0 for Maven 2.x POMs -->
    <modelVersion>4.0.0</modelVersion>

    <!-- project coordinates - values which uniquely identify this project -->
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0</version>

    <!-- library dependencies -->
    <dependencies>
        <dependency>
            <groupId>com.sparkjava</groupId>
            <artifactId>spark-core</artifactId>
            <version>2.5</version>
        </dependency>
    </dependencies>
</project>
```
We then import this in the src/main/java folder inside a class:
```
import static spark.Spark.*;

public class Main {
    public static void main(String[] args) {
        get("/hello", (req, res) -> "Hello World");
    }
}
```

This is what the Spark file looks like for Arnaud inside My-Walking-Skeleton/src/main/java/codurance/com/walking_skeleton/App.java:
```
package aclaudel.codurance;

import spark.Spark;

import static spark.Spark.get;

public class App {

    public static final int DEFAULT_PORT = 80;

    public static void main(String[] args) {
        Spark.port(getPort());
        get("/hello", (req, res) -> "Hello World");
        get("/", (req, res) -> "Welcome");
        get("/jose", (req, res) -> "Hola Jose");
    }

    private static int getPort() {
        String port = System.getenv("PORT");
        if (port != null)
            return Integer.parseInt(port);
        return DEFAULT_PORT;
    }

}
```
This is what the app looks like:
https://ancient-tundra-33358.herokuapp.com/


This is a list of some of the commands we used for the Skeleton:
```
λ history                                                                                                          Desktop
history
git clone git@github.com:Morinslash/SocialNetworkKata.git
bash --login
brew install travis
git push
git commit -m "Edit AppTest and .travis.yml"
git add .
git status
git push
git commit -m "Add .travis.yml"
git add .
git status
mvn test
git push
git commit -m  "Correct AppShould"
code .
git push heroku master
git commit -m "Correct Procfile"
git commit -m "Add Procfile"
touch Procfile
heroku logs --tail
heroku open
heroku ps:scale web=1
heroku create
heroku apps:destroy -a limitless-mesa-77626
limitless-mesa-77626
git commit -m "Add files"
echo "# My-Walking-Skeleton" >> README.md
                  git init
                  git add README.md
                  git commit -m "first commit"
                  git remote add origin git@github.com:TomSpencerLondon/My-Walking-Skeleton.git
                  git push -u origin master
ls
rm -rf .git
ls -la
heroku login
java --version
mvn --version
heroku
cd my-walking-skeleton/
git clone https://github.com/aclaudel/my-walking-skeleton
cd ..
mvn compile
cd SocialNetworking/
git clone https://github.com/CarlosMChica/SocialNetworking.git
docker ps
brew install apache-spark
brew install scala
cd Sedgewick-Algorithms/
git clone git@github.com:TomSpencerLondon/Sedgewick-Algorithms.git
cd algorithms/
mkdir algorithms
cd Desktop/
history
docker image push tomspencer/bulletinboard:1.0
docker image tag bulletinboard:1.0 tomspencer/bulletinboard:1.0
docker ls --all
docker ls -all
docker image
docker push tomspencer/bulletinboard:tagname
docker push tomspencer/bulletinboard:1.0
docker container rm --force bb
npm start
docker container run --publish 8000:8080 --detach --name bb bulletinboard:1.0
docker image build -t bulletinboard:1.0 .
git clone https://github.com/dockersamples/node-bulletin-board
cd node-bulletin-board/bulletin-board-app
docker container ls --all
docker run hello-world
docker system prune
docker system --prune
docker --version
```

This was the skeleton walkthrough from Arnaud Claudel:
https://github.com/aclaudel/my-walking-skeleton/tree/master/src/main/java/aclaudel/codurance

This is another tutorial on Apache Spark without IntelliJ:
https://www.tutorialkart.com/apache-spark/create-java-project-with-apache-spark/

https://www.tutorialkart.com/apache-spark/how-to-install-spark-on-mac-os/


This was the Walking Skeleton that I deployed on Travis:
https://travis-ci.com/

This is a handout on how to build a Java project on Travis CI:
https://docs.travis-ci.com/user/languages/java/


This is the heroku deployment information for Travis:
https://docs.travis-ci.com/user/deployment/heroku/

https://github.com/travis-ci/travis.rb#readme
