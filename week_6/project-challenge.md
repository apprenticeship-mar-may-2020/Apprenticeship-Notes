##### This was the project challenge:
https://github.com/TomSpencerLondon/Codurance-Apprenticeship-Wiki/blob/master/Week-6.md

https://docs.google.com/document/d/1tV_b_xsaAkHW0QklbWSE8slQGOSpWCQh8DgyKljhkXc/edit#


https://drive.google.com/file/d/1yoehFGKtlhjtgrROxF0h0vbTjPXG5sQo/view

This was the description for Entity Relationship Diagrams:
https://docs.google.com/presentation/d/1qw-TRm0zrKkC722WqhBUCcgcYgK2-UxkLxVjatxmeO8/edit#slide=id.p


This was what the challenge looked like:
```
Week 6 - ERD - Practise

Problem
Tools for this purpose
Conceptual Diagram
Logical Diagram
Physical Diagram


Problem
Endurance is a company that works delivering projects, and is willing to expand its capacity to different offices and locations in the world. For that reason they are asking us to create a tool to help them manage the projects around the company.

Projects are composed for different tasks, there is no project without a task. Tasks can be simple tasks or more complex in which case they are composed of subtasks.

Endurance methodology establishes an estimated cost in time for each task in the project, that later uses for invoicing. The tasks have a complexity factor that is established as follows:
Minimum complexity
Medium complexity
Maximum complexity 

Additionally to the cost in time of the tasks, there are taxes that are applied to the task, those taxes vary from task to task depending on the regulation of each country.
Tools for this purpose
Draw.io (references on ERD https://about.draw.io/entity-relationship-diagrams-with-draw-io/)
ERDPlus (create an account for free and work online)
OnlineVisualParadigm
Conceptual Diagram
Implement a first approach to the solution in a Conceptual Diagram:
At this point we just need to identify the entities in the domain
Trace a basic relation between them
Remember is a conceptual vision 

Logical Diagram
We need to go a step further, now we can model or design a logical ER diagram, where entities and relationships are better shaped.

Physical Diagram
At this point in time we should have decided which type of RDBMS we are going to use, to know the specific details of the implementations and limitations in the real database.

This is the most detailed aspect of the relationship, were we are going to implement the result directly to the RDBMS.

In general, is quite difficult and even impractical to define the Physical Diagram from scratch, and might be helpful to use reverse engineering to grab an existing schema and visualize it in a detailed DER, this depends on the SQL capabilities of the person trying to design the diagram.

Solutions
Draw.io solution


```

This was our solution:

![Screenshot 2020-04-06 at 21 15 35](https://user-images.githubusercontent.com/27693622/78604359-9f637180-7851-11ea-86c4-8cd2090640a3.png)


<img width="526" alt="Screenshot 2020-04-06 at 22 00 44" src="https://user-images.githubusercontent.com/27693622/78604679-1b5db980-7852-11ea-9130-b5be310af8ce.png">


https://app.diagrams.net/#G1stAzRUug8cd90ur2UYK1DN2Ju6R9oDMJ
<img width="646" alt="Screenshot 2020-04-06 at 21 57 10" src="https://user-images.githubusercontent.com/27693622/78604737-2a446c00-7852-11ea-8d19-a530c4602b6d.png">


Db diagram.io was a version of draw.io:
https://dbdiagram.io/d

it is useful for ERD for databases

This was the set up for the database:
![Screenshot 2020-04-06 at 23 14 26](https://user-images.githubusercontent.com/27693622/78610289-71cff580-785c-11ea-975c-3eb923592c47.png)


![Screenshot 2020-04-06 at 23 14 16](https://user-images.githubusercontent.com/27693622/78610333-84e2c580-785c-11ea-95db-9ae1ef8ada45.png)


##### This article was extremely useful for connecting to the docker instance of the mysql database:
https://medium.com/@rauf.rahmancz/connect-docker-and-mysql-in-right-way-95602f833cb0

```
Take a quick glance at MySQL and Docker
MySQL is a freely available open-source relational database management system that uses SQL.
Docker is an virtualization software that performs operating-system-level virtualization.
Unlike traditional VM, docker doesn’t take up a lot of space, memory and processing power.
Docker provides a light-weight container environment which can be used to host any application of your choice. Because it’s light-weight, it can be easily shipped to other machines and be executed on those machines, then you can get the exact same environment in dev and product system. Or you can clone your environment for any new team members easily and quickly, without painful setups.
With Docker, you can easily launch containers based on images that contain different versions of the software.
You could create as many container as you want on the host machine, and each of the container are isolated from one another.
Also, each docker images are version controlled.
1.Install Docker
Download the (free) Docker Community Edition for Mac (unless you’ve already got it installed on your system). This will enable you to run SQL Server from within a Docker container.
To download, visit the Docker download page and click Get Docker.
After download complete and successful installed lunch docker.
2.Increase the Memory
By default, Docker will have 2GB of memory allocated to it. SQL Server needs at least 3.25GB. To be safe, increase it to 4GB if you can.
To do this:
Select Preferences from the little Docker icon in the top menu
Slide the memory slider up to at least 4GB
Click Apply & Restart

```

This was to start docker mysql:
```
3.Download and run mysql server
Open Terminal
Verify docker installation via running “docker version”
A successful docker should give you following output:

```
and this:
```
3. Run below command to create a docker container with mysql image.
docker run -d -p 3306:3306 — name=Home-Server-1  — env=”MYSQL_ROOT_PASSWORD=”Your Password" mysql
//Your password = choose a any password
It should give you following output in terminal

```
This command was key:
```
6. You may encounter with following “Public Key Retrieval is not allowed” error. Do not worry it is a common error which is occurring from MySQL 8, the authentication plugin is changed to “caching_sha2_password”.
7. For solving this error open terminal and copy and paste following command
docker exec -it Home-Server-1 bash
It will give you bash access for your container.
8. Then login into mysql
mysql -u root -p

````

#### Alexis mentioned datagrip for database management:
https://www.jetbrains.com/datagrip/quick-start/

##### This is a good site for gifs:
https://media2.giphy.com/media/Mp4hQy51LjY6A/giphy.gif?cid=6104955ed139049cb4b63132e5d4acc27ffde316515f3938&rid=giphy.gif

##### This is good sudoku problem:
```
Good morning!

Here's a solution to yesterday's problem.

This is your coding interview problem for today.

This problem was asked by Dropbox.

Sudoku is a puzzle where you're given a partially-filled 9 by 9 grid with digits. The objective is to fill the grid with the constraint that every row, column, and box (3 by 3 subgrid) must contain all of the digits from 1 to 9.

Implement an efficient sudoku solver.

We will be sending the solution tomorrow, along with tomorrow's question. As always, feel free to shoot us an email if there's anything we can help with.

Have a great day!
```

##### This is the Gatsby bootcamp:
https://www.youtube.com/watch?v=8t0vNu2fCCM


##### This was tail recursion for Roman Numeral calculator:
```
package com.codurance.roman_numeral_converter;

public class RomanNumeralConverter {
  private final StringBuilder stringBuilder;

  public RomanNumeralConverter() {
    this.stringBuilder = new StringBuilder();
  }

  public String call(int number) {
    if (number <= 0){
      return stringBuilder.toString();
    }

    ArabicToRoman highestValue = findHighestValueFor(number);

    stringBuilder.append(highestValue.roman);
    number -= highestValue.decimal;
    return call(number);
  }

  private ArabicToRoman findHighestValueFor(int number) {
    for (ArabicToRoman arabic : ArabicToRoman.values()){
      if (arabic.decimal <= number){
        return arabic;
      }
    }
    return null;
  }

  public enum ArabicToRoman{
    TEN(10, "X"),
    NINE(9, "IX"),
    FIVE(5, "V"),
    FOUR(4, "IV"),
    ONE(1, "I");

    public final int decimal;
    public final String roman;

    ArabicToRoman(int decimal, String roman) {
      this.decimal = decimal;
      this.roman = roman;
    }
  }

}
```
The keys here are extracting the stringBuilder to an instance variable:
```
  private final StringBuilder stringBuilder;

  public RomanNumeralConverter() {
    this.stringBuilder = new StringBuilder();
  }
```

Also creating a guard clause for the base case:
```
    if (number <= 0){
      return stringBuilder.toString();
    }
```
Creating a separate method for calculating the highestValue for adding to the stringBuilder:
```
  private ArabicToRoman findHighestValueFor(int number) {
    for (ArabicToRoman arabic : ArabicToRoman.values()){
      if (arabic.decimal <= number){
        return arabic;
      }
    }
    return null;
  }
```
Finally we have the recursive tail recursive call at the end of the convert method:
```

    ArabicToRoman highestValue = findHighestValueFor(number);

    stringBuilder.append(highestValue.roman);
    number -= highestValue.decimal;
    return call(number);

```