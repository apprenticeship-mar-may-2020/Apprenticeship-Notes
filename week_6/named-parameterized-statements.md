##### This is useful for named Parameterized statements:
https://www.codemeright.com/blog/post/named-parameterized-query-java#NamedPreparedStatement

https://www.codemeright.com/blog/post/named-parameterized-query-java#PreparedStatementImpl
```
/*
 * Here, we inserted static values in query. In your case these values might come from user input.
 * Here, we are directly concatinating the values in the query. Hence, It's not SQL injection safe.  
 */ 
String sqlQuery = " INSERT INTO employee (id, name, designation, salary)"
                + " VALUES('1', 'Dwayne Johnson', 'Project Manager', '250000')";
Statement stmt = conn.createStatement();
stmt.executeUpdate(sqlQuery);
stmt.close();
```

#### This is prepared statements in Java:

https://www.daniweb.com/programming/software-development/threads/200302/using-prepared-statements-in-java

```
import java.io.*;
import java.sql.*;
public class SingleUser
{
	public static void main(String[] args)throws Exception
	{
		Class.forName("sun.jdbc.odbc.JdbcOdbcDriver");
		String url = "jdbc:odbc:StockTracker";
		Connection con = DriverManager.getConnection(url);
		PreparedStatement pStmt = con.createStatement("SELECT userID, symbol FROM UserStocks");
		PreparedStatement pStmt1 = con.createStatement("SELECT name FROM Stocks");
		System.out.println("Stock holdings for User: " + pStmt);
		System.out.println("Stock - Description");
		System.out.println("-------------------------------------------");
		BufferedReader dataIn = new BufferedReader(new InputStreamReader(System.in));
		for(int i = 0; 1 > 0; i++)
		{
			System.out.print("Enter User ID: ");
			String usrID = dataIn.readline();
			pStmt.setString(1, userID);
			pStmt1.setString(1, symbol);
			pStmt1.setString(2, name);
			pStmt.executeUpdate();
			pStmt1.executeUpdate();
		}
		pStmt.close();
	}
}
```