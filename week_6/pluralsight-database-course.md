###### Pluralsight database course:
https://app.pluralsight.com/course-player?clipId=7ffe7849-847d-40f2-ab2d-5470835ccabe

JDBC - Java Core Libraries

This is JDBC Data Type conversions:
<img width="1091" alt="Screenshot 2020-04-09 at 10 09 06" src="https://user-images.githubusercontent.com/27693622/78970612-9f899880-7b01-11ea-85ab-9508b1167e81.png">
This does not include DateTime.

#### These the JDBC Types:
<img width="1110" alt="Screenshot 2020-04-09 at 10 09 18" src="https://user-images.githubusercontent.com/27693622/78971069-a82e9e80-7b02-11ea-996f-e42e3e812218.png">


##### This was a summary of the points from the first lessons of JDBC:
<img width="1414" alt="Screenshot 2020-04-09 at 10 13 29" src="https://user-images.githubusercontent.com/27693622/78971129-c7c5c700-7b02-11ea-92fa-24149c2dad5d.png">
- Here we are advised to use the try-with-resources clause.
- Also we can use ResultSet which is a flexible interface that supports several navigation methods. Not all JDBC drivers support all the navigation methods.
- SQL types map loosely to Java types but not always


These are the operations you can use with resultSet to navigate results: 
<img width="603" alt="Screenshot 2020-04-09 at 10 10 12" src="https://user-images.githubusercontent.com/27693622/78971637-f2fce600-7b03-11ea-9af0-7c45b41e0a5f.png">

This is the varying number of characters in VARCHAR for different types of database:
<img width="1431" alt="Screenshot 2020-04-09 at 10 08 27" src="https://user-images.githubusercontent.com/27693622/78975540-38251600-7b0c-11ea-8fbe-6e3fb5a8f9d3.png">

This is the different types of SQLException types:

<img width="1287" alt="Screenshot 2020-04-09 at 09 53 35" src="https://user-images.githubusercontent.com/27693622/78975602-5428b780-7b0c-11ea-85d4-c49540253c01.png">

This is an overview of the JDBC Datasource:
<img width="948" alt="Screenshot 2020-04-09 at 09 47 34" src="https://user-images.githubusercontent.com/27693622/78975708-918d4500-7b0c-11ea-84eb-f100ac93ac62.png">

First we have the Datasource which comes through a connection. We then have the option of statement or PreparedStatement in order to get a ResultSet.

This is where Driver Managers are kept:

<img width="1248" alt="Screenshot 2020-04-09 at 09 43 15" src="https://user-images.githubusercontent.com/27693622/78975797-d1ecc300-7b0c-11ea-85bd-9508854fa674.png">

This is the Facade pattern with JDBC:
<img width="1361" alt="Screenshot 2020-04-09 at 09 40 48" src="https://user-images.githubusercontent.com/27693622/78975887-02346180-7b0d-11ea-8d87-c4f58a3d865d.png">

Here we have different drivers that connect the JDBC to the database and the JDBC acts as a facade for these connections in order to translate Java.sql language to database queries.

This is an overview of connection strategies:

![Screenshot 2020-04-09 at 09 40 39](https://user-images.githubusercontent.com/27693622/78976006-3d369500-7b0d-11ea-8c19-57d8207df044.png)