##### These were the notes from the call:
```
Edward Rixon09:14
https://dbeaver.io/
Edward Rixon09:36
https://github.com/CarlosMChica/SocialNetworking.git
Sebastian Kulinski09:45
https://phoenixnap.com/kb/mysql-docker-container
Sebastian Kulinski09:55
https://www.techrepublic.com/article/how-to-deploy-and-use-a-mysql-docker-container/
Sebastian Kulinski09:58
ALTER USER 'root'@'localhost' IDENTIFIED BY 'newpassword';
Sebastian Kulinski10:09
$  docker run -p 3306:3306 --name hb-mysql-example -e MYSQL_ROOT_PASSWORD=Buster -d mysql
Sebastian Kulinski10:19
https://springframework.guru/using-docker-to-run-mysql-server-in-your-development-environment/
```
```
Alexandros Pavlidis10:24
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.19</version>
</dependency>
Alexandros Pavlidis10:26
            String url1 = "jdbc:mysql://localhost:3306/test1";
            String user = "root";
            String password = "secret";
 
            conn1 = DriverManager.getConnection(url1, user, password);
           if (conn1 != null) {
                System.out.println("Connected to the database test1");
            }
 
Alexandros Pavlidis10:30
show databases;
```

Alexandros Pavlidis10:42
```
String sql = "INSERT INTO candidates(first_name,last_name,dob,phone,email) "
            + "VALUES(?,?,?,?,?)";
 
PreparedStatement pstmt = conn.prepareStatement(sql,
                              Statement.RETURN_GENERATED_KEYS);
```

This was one of the queries for our creation of the databases with DBeaver with local database connection:
```
CREATE TABLE endurance.Project (
	id INT UNSIGNED auto_increment NOT NULL,
	name varchar(100) NULL,
	description varchar(100) NULL,
	deadline varchar(100) NULL,
	CONSTRAINT Project_PK PRIMARY KEY (id)
)
ENGINE=InnoDB
DEFAULT CHARSET=utf8mb4
COLLATE=utf8mb4_0900_ai_ci;
```
##### Alexis suggested this for database dump:
mysqldump --databases test > dump.sql

It didn't work

##### This is oracle which I signed up for:

https://www.oracle.com/index.html

This was the download:
https://dbeaver.io/download/

###### This was for setting up a mysql docker container:
https://phoenixnap.com/kb/mysql-docker-container
```
Step 3: Connect to the MySQL Docker Container
1. Before you can connect the MySQL server container with the host, you need to make sure the MySQL client package is installed:

apt-get install mysql-client
2. Then, start a MySQL client inside the container by typing:

docker exec -it [container_name] mysql -uroot -p
3. Provide the root password, when prompted. With that, you have connected the MySQL client to the server.

4. Finally, change the server root password to protect your information:

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '[newpassword]';
Replace [newpassword] with a strong password of your choice.
```

#### This is the set up for sql:
https://stackoverflow.com/questions/15450091/error-2002-hy000-cant-connect-to-local-mysql-server-through-socket-tmp-mys

```
You'll need to start MySQL before you can use the mysql command on your terminal. To do this, run brew services start mysql. By default, brew installs the MySQL database without a root password. To secure it run: mysql_secure_installation.

To connect run: mysql -uroot. root is the username name here.
```

##### This was docker desktop for mac:
https://hub.docker.com/editions/community/docker-ce-desktop-mac/

##### This is Jeff Bezos:
https://en.wikipedia.org/wiki/Jeff_Bezos
```
Jeffrey Preston Bezos (/ˈbeɪzoʊs/;[a][2] né Jorgensen; born January 12, 1964) is an American businessman, media proprietor, and investor. He is best known as the founder, CEO, and president of the online retail company Amazon. The first centi-billionaire on the Forbes wealth index, Bezos has been the world's richest person since 2017 and was named the "richest man in modern history" after his net worth increased to $150 billion in July 2018.[3] In September 2018, Forbes described him as "far richer than anyone else on the planet" as he added $1.8 billion to his net worth when Amazon became the second company in history to reach a market cap of $1 trillion.

Born in Albuquerque and raised in Houston and later Miami, Bezos graduated from Princeton University in 1986 with a degree in electrical engineering and computer science. He worked on Wall Street in a variety of related fields from 1986 to early 1994. He founded Amazon in late 1994 on a cross-country road trip from New York City to Seattle. The company began as an online bookstore and has since expanded to a wide variety of other e-commerce products and services, including video and audio streaming, cloud computing, and AI. It is currently the world's largest online sales company, the largest Internet company by revenue, and the world's largest provider of virtual assistants[4] and cloud infrastructure services through its Amazon Web Services branch.

Bezos founded the aerospace manufacturer and sub-orbital spaceflight services company Blue Origin in 2000. A Blue Origin test flight successfully first reached space in 2015, and the company has upcoming plans to begin commercial suborbital human spaceflight.[5] He also purchased the major American newspaper The Washington Post in 2013 for $250 million in cash, and manages many other investments through his Bezos Expeditions venture capital firm.
```

#### This was for IDEF1X notation for databases:
https://www.google.com/search?q=idef1x+notation&rlz=1C5CHFA_enGB894GB895&tbm=isch&source=iu&ictx=1&fir=aVjhQ5ZsdXRqAM%253A%252CtRwilNNcak0vmM%252C_&vet=1&usg=AI4_-kQtZH3T84Oz9J0nqL-nxVxFv5_NyA&sa=X&ved=2ahUKEwjsgqeLg9ToAhWSQUEAHXVtAH8Q_h0wEnoECAkQBQ#imgrc=aVjhQ5ZsdXRqAM:

![unnamed](https://user-images.githubusercontent.com/27693622/78573269-74145e80-7820-11ea-8def-c0cf7d8f908d.gif)


#### This is crows foot:
![download](https://user-images.githubusercontent.com/27693622/78573378-927a5a00-7820-11ea-9c2a-9dfdadb4d414.png)

##### This was Sebs set up:

![Screenshot 2020-04-06 at 11 21 32](https://user-images.githubusercontent.com/27693622/78573450-a756ed80-7820-11ea-81ca-417148a99109.png)

![Screenshot 2020-04-06 at 11 21 08](https://user-images.githubusercontent.com/27693622/78573518-ba69bd80-7820-11ea-9dbc-0ee8da982935.png)


![Screenshot 2020-04-06 at 11 11 11](https://user-images.githubusercontent.com/27693622/78573571-c5bce900-7820-11ea-9975-baa48359c9dd.png)


![Screenshot 2020-04-06 at 10 58 05](https://user-images.githubusercontent.com/27693622/78573613-d1a8ab00-7820-11ea-934c-9c5d24cca9a0.png)


##### This was the handout on Post-Relational databases:
![Screenshot 2020-04-06 at 09 18 52](https://user-images.githubusercontent.com/27693622/78573711-f0a73d00-7820-11ea-87bc-c5de1279bd81.png)


![Screenshot 2020-04-06 at 10 52 47](https://user-images.githubusercontent.com/27693622/78573831-146a8300-7821-11ea-81da-4b9677139c0f.png)

This was the reference to SQL in the presentation group by:

![Screenshot 2020-04-06 at 09 32 45](https://user-images.githubusercontent.com/27693622/78573861-1fbdae80-7821-11ea-8066-25b0e6dca404.png)


This was Left inner join:
![Screenshot 2020-04-06 at 09 29 33](https://user-images.githubusercontent.com/27693622/78573927-349a4200-7821-11ea-93da-835b6ad78e10.png)

![Screenshot 2020-04-06 at 09 28 06](https://user-images.githubusercontent.com/27693622/78574000-4b409900-7821-11ea-882e-877760543a3d.png)


![Screenshot 2020-04-06 at 09 28 06](https://user-images.githubusercontent.com/27693622/78574095-6dd2b200-7821-11ea-9919-45f3e08096cb.png)


###### This was the site Seb used to set up docker with mysql:
https://springframework.guru/using-docker-to-run-mysql-server-in-your-development-environment/

##### This is a link on normalization:
```
What is Normalization?
NORMALIZATION is a database design technique that organizes tables in a manner that reduces redundancy and dependency of data. Normalization divides larger tables into smaller tables and links them using relationships. The purpose of Normalization is to eliminate redundant (useless) data and ensure data is stored logically.

The inventor of the relational model Edgar Codd proposed the theory of normalization with the introduction of the First Normal Form, and he continued to extend theory with Second and Third Normal Form. Later he joined Raymond F. Boyce to develop the theory of Boyce-Codd Normal Form.

In this tutorial, you will learn-

1NF Rules
2NF Rules
3NF Rules
BCNF (Boyce-Codd Normal Form)
```
1NF (First Normal Form) Rules
Each table cell should contain a single value.
Each record needs to be unique.
The above table in 1NF-

What is Composite Key?
A composite key is a primary key composed of multiple columns used to identify a record uniquely

In our database, we have two people with the same name Robert Phil, but they live in different places.

2NF (Second Normal Form) Rules
Rule 1- Be in 1NF
Rule 2- Single Column Primary Key
It is clear that we can't move forward to make our simple database in 2nd Normalization form unless we partition the table above.

3NF (Third Normal Form) Rules
Rule 1- Be in 2NF
Rule 2- Has no transitive functional dependencies
To move our 2NF table into 3NF, we again need to again divide our table.

These tables now represent 3 NF:
![2NFTable1](https://user-images.githubusercontent.com/27693622/78574535-05d09b80-7822-11ea-9a80-0b43d45f6f0c.png)


![2NFTable2](https://user-images.githubusercontent.com/27693622/78574571-11bc5d80-7822-11ea-9b63-8c7c22c932ad.png)


![2NFTable3](https://user-images.githubusercontent.com/27693622/78574600-1aad2f00-7822-11ea-8e75-1eab348c22ed.png)

##### In contract this was 1NF:

![NormalizationTable1](https://user-images.githubusercontent.com/27693622/78574662-37496700-7822-11ea-9419-2f0dc2192343.png)

