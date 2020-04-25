#### This was my bank kata - with database:

https://github.com/TomSpencerLondon/Bank-Revision

This is Alexis' bank:
https://github.com/apavlidi/Bank

##### These were some of the set ups for database session:
Here Seb reversed the statement list in order to display the transactions:
![Screenshot 2020-04-09 at 15 13 56](https://user-images.githubusercontent.com/27693622/78970020-43724480-7b00-11ea-8e68-5368836f68d7.png)

This is the singleton Datasource class for database connection that Seb used:
![Screenshot 2020-04-09 at 12 18 36](https://user-images.githubusercontent.com/27693622/78970072-63096d00-7b00-11ea-8ef6-7a0afe438bae.png)

This is the connection method for the database. I believe Alexis used a 
![Screenshot 2020-04-09 at 12 18 14](https://user-images.githubusercontent.com/27693622/78970134-8502ef80-7b00-11ea-8560-6b3178ce18f1.png)

This was Seb's SQLite implementation of the database query with PreparedStatement and setDate and executeUpdate():
![Screenshot 2020-04-09 at 12 15 14](https://user-images.githubusercontent.com/27693622/78970261-d4e1b680-7b00-11ea-9cda-814f84aa62b4.png)


I think this was Alexis' version with the close and connect methods:
![Screenshot 2020-04-09 at 12 12 32](https://user-images.githubusercontent.com/27693622/78970312-efb42b00-7b00-11ea-9d2d-37f6a25fd176.png)

I don't believe I used close for my connections - actually perhaps my connections were within the try catch block that I used and this closed the connection. I will need to check this.

This is the connection class that Alexis made I believe:
![Screenshot 2020-04-09 at 11 56 39](https://user-images.githubusercontent.com/27693622/78970525-65b89200-7b01-11ea-8193-2d673a22e762.png)

Here we have the connection and then catch an error and return the connection.


I didn't click on this window for allowing apps to download - for mongodb download:
<img width="672" alt="Screenshot 2020-04-10 at 08 11 36" src="https://user-images.githubusercontent.com/27693622/78971349-4589d280-7b03-11ea-961e-96be66f2090d.png">
