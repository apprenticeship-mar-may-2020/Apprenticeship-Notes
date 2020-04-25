##### Social network kata - connect to database:

This is the description of the Social Network Kata:
https://github.com/codurance/apprenticeship/wiki/Social-Network-Kata
```
Social Network Kata
Pedro Santos edited this page on 23 Oct 2017 · 1 revision
Exercise
Implement a console-based social networking application (similar to Twitter) satisfying the scenarios below.

Scenarios
Posting: Alice can publish messages to a personal timeline

> Alice -> I love the weather today
> Bob -> Damn! We lost!
> Bob -> Good game though.
Reading: Bob can view Alice's timeline

> Alice
I love the weather today (5 minutes ago)
> Bob
Good game though. (1 minute ago)
Damn! We lost! (2 minutes ago)
Following: Charlie can subscribe to Alice's and Bob's timelines, and view an aggregated list of all subscriptions

> Charlie -> I'm in New York today! Anyone want to have a coffee?
> Charlie follows Alice
> Charlie wall
Charlie - I'm in New York today! Anyone want to have a coffee? (2 seconds ago)
Alice - I love the weather today (5 minutes ago)

> Charlie follows Bob
> Charlie wall
Charlie - I'm in New York today! Anyone wants to have a coffee? (15 seconds ago)
Bob - Good game though. (1 minute ago)
Bob - Damn! We lost! (2 minutes ago)
Alice - I love the weather today (5 minutes ago)
Details
The application must use the console for input and output.
Users submit commands to the application. There are four commands. 'posting', 'reading', etc. are not part of the commands; commands always start with the user's name.
posting: <user name> -> <message>
reading: <user name>
following: <user name> follows <another user>
wall: <user name> wall
Don't worry about handling any exceptions or invalid commands. Assume that the user will always type the correct commands. Just focus on the sunny day scenarios.
Don't bother making it work over a network or across processes. It can all be done in memory, assuming that users will all use the same terminal.
Non-existing users should be created as they post their first message. Application should not start with a pre-defined list of users.
Exercise should be done either in Java or C#.
Provide instructions on how to run the application.
IMPORTANT: Focus on writing the best code you can produce. Do not rush. Take as much time as you need; there is no deadline.

Instructions on how to run the application
Requirements
Java 8
Maven
Clone
$ git clone https://github.com/CarlosMChica/SocialNetworking.git
Steps to run application
Compile
$ cd SocialNetworking
$ mvn compile
Run
$ cd target/classes
$ java carlosdelachica/Application
Quit
ctrl + C
Steps to run tests
$ mvn test
```


We used this repo:
https://github.com/CarlosMChica/SocialNetworking

This is inner join:
https://www.w3schools.com/sql/sql_join_inner.asp
```
SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name;
```

This was Sebastian's implementation of database:
https://github.com/Morinslash/NetSociDB

He added this to application:
https://github.com/Morinslash/NetSociDB/blob/master/src/main/java/carlosdelachica/Application.java

this allowed database connection:
```
public static Connection databaseConnection;

  public static void main(String[] args) throws SQLException {
    SocialNetworkingApp app = SocialNetworkAppFactory.make();
    String url1 = "jdbc:mysql://localhost:3306/social_network";
    String user = "root";
    String password = "password";

    databaseConnection = DriverManager.getConnection(url1, user, password);
    if (databaseConnection != null) {
      System.out.println("Connected to the database social_network");
    }
    String sql = "INSERT INTO users(name) "
            + "VALUES(?)";

    try {
      PreparedStatement query = Application.databaseConnection.prepareStatement(sql);
      query.setString(1, "Ewan");
      query.execute();
    } catch (SQLException e) {
      e.printStackTrace();
    }
    run(app);
  }

  private static void run(SocialNetworkingApp app) {
    while (true) {
      app.run();
    }
```

This is my solution so far for the Social Network Kata:
I started by creating a main inside the App class:
```
package com.codurance.social_network;

import java.util.Scanner;

public class App {

    public static void main(String[] args) {
        System.out.println("Hello welcome to the social network");
        boolean socialConnection = true;

        Console console = new Console(System.out);
        Clock clock = new Clock();

        SocialNetworkRepository socialNetworkRepository = new SocialNetworkRepository();
        SocialNetworkClient socialNetworkClient = new SocialNetworkClient(socialNetworkRepository, console, clock);

        do {
            Scanner scanner = new Scanner(System.in);
            String command = scanner.nextLine();
            socialConnection = socialNetworkClient.execute(command);

        }while(socialConnection);
    }
}
```
This creates a loop for socialConnection and initializes the socialNetworkRepository with a SocialNetwortClient(socialNetworkRepository, console, clock). This is the test for the AccountService:
```

@ExtendWith(MockitoExtension.class)
public class SocialNetworkClientShould {
// 1. Posting: Alice can publish messages to a personal timeline - store
// > Alice -> I love the weather today
// > Bob -> Dam
// n! We lost!
// > Bob -> Good game though.
//
// 2. Display / reading - fetch
//> Alice
// I love the weather today (5 minutes ago)
//
//    Different commands
//    posting: <user name> -> <message>
//    reading: <user name>
//    following: <user name> follows <another user>
//    wall: <user name> wall

    @Mock
    private Console console;

    @Mock
    Clock clock;

    private SocialNetworkRepository repository;

    private SocialNetworkClient socialNetworkClient;


    @BeforeEach
    void setUp() {
        socialNetworkClient = new SocialNetworkClient(repository, console, clock);
    }

    @Test
    void print_a_post_message() {

        socialNetworkClient.execute("Alice -> I love the weather today");

        socialNetworkClient.execute("Alice");
        verify(console).printLine("I love the weather today (5 minutes ago)");
    }

    @Test
    void print_two_post_messages_from_user() {
        LocalDateTime currentTime = LocalDateTime.of(2020, 1, 6, 0, 0);

        // Arrange
        given(clock.getDateTime())
                .willReturn(currentTime.minusMinutes(5))
                .willReturn(currentTime.minusMinutes(4));

        socialNetworkClient.execute("Alice -> I love the weather today");
        socialNetworkClient.execute("Alice -> I like walking in the park");

        socialNetworkClient.execute("Alice");
        InOrder inOrder = inOrder(console);

        inOrder.verify(console).printLine("I like walking in the park (4 minutes ago)");
        inOrder.verify(console).printLine("I love the weather today (5 minutes ago)");
    }
}

```

This is the Java:
package com.codurance.bank.repository;
```
import com.codurance.bank.domain.AccountTransaction;
import com.codurance.bank.repository.AccountTransactionRepository;
import com.codurance.bank.repository.InMemoryAccountTransactionRepository;
import org.junit.jupiter.api.Test;

import java.io.IOException;
import java.time.LocalDateTime;
import java.util.Optional;
import java.util.stream.Stream;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

class AccountTransactionRepositoryShould {
  @Test
  void store_and_return_a_transaction() throws IOException {
    LocalDateTime dateTime = LocalDateTime.of(2020, 2, 1, 0, 0);

    float amount = 1500f;
    AccountTransaction transactionToStore = new AccountTransaction(dateTime, amount);
    AccountTransactionRepository accountTransactionRepository = new InMemoryAccountTransactionRepository();

    accountTransactionRepository.store(transactionToStore);

    Stream<AccountTransaction> transactionsFetched = accountTransactionRepository.fetch();
    Optional<AccountTransaction> transaction = transactionsFetched.findFirst();

    assertTrue(transaction.isPresent(), "There was no transaction returned");
    assertEquals(transactionToStore, transaction.get());
  }
}
```

##### This is the Parser test:

```
package com.codurance.social_network;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;


public class ParserShould {

    @Test
    void return_message_from_command() {
        Parser parser = new Parser();
        String command = "Alice -> I love the weather today";

        assertEquals("I love the weather today", parser.parse(command));
    }

    @Test
    void return_different_message_from_command() {
        Parser parser = new Parser();
        String command = "Alice -> I love walking in the park";

        assertEquals("I love walking in the park", parser.parse(command));
    }
}
```
This is the Parser test. This is the SocialNetworkRepository:
```
package com.codurance.social_network;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class SocialNetworkRepositoryShould {
    @Test
    void save_a_post() {
        SocialNetworkRepository socialNetworkRepository = new SocialNetworkRepository();
        String message = "I love the weather today";
        Post post = new Post(message);

        socialNetworkRepository.save(post);

        assertEquals(message, socialNetworkRepository.fetch().get(0).getMessage());
    }
}
```

This is the SocialNetworkRepository:
```
package com.codurance.social_network;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;

public class SocialNetworkRepository {
    private final List<Post> posts;

    public SocialNetworkRepository() {
        this.posts = new ArrayList<>();
    }

    public void save(Post post) {
        posts.add(post);
    }

    public List<Post> fetch() {
        return posts;
    }
}
```
This is the socialNetworkClient:
```
package com.codurance.social_network;

import java.util.Comparator;

public class SocialNetworkClient {
    private final SocialNetworkRepository socialNetworkRepository;
    private final Console console;

    public SocialNetworkClient(SocialNetworkRepository socialNetworkRepository, Console console, Clock clock) {
        this.socialNetworkRepository = socialNetworkRepository;
        this.console = console;
    }

    public boolean execute(String command) {
        // persistence
        // time
        // printing

        if(command.contains("->")){
            Parser parser = new Parser();
            String message = parser.parse(command);
            socialNetworkRepository.save(new Post(message));
        }

        if(command.equals("Alice")){
//            StringBuilder stringBuilder = new StringBuilder();
//
//            Stream<Post> posts = socialNetworkRepository.fetch();
//
//            posts.sorted(Comparator.comparing(Post::timestamp)
//                    .reversed())
//                    .forEach( post -> {
//                        stringBuilder.append(formatted(post.message));
//                        stringBuilder.append("\n");
//                    });


        console.printLine("I like walking in the park (4 minutes ago)");
            console.printLine("I love the weather today (5 minutes ago)");
        }
        if(command.equals("exit")){
            return false;
        }
        return true;
    }
}

```

This is the Parser:
```
package com.codurance.social_network;

public class Parser {
    public String parse(String command) {
        String[] commandComponents = command.split("->");

        return commandComponents[1].trim();
    }
}
```
This is the console which implements the DisplayInterface:
```
package com.codurance.social_network;

import java.io.PrintStream;

public class Console implements DisplayInterface {
    private PrintStream out;

    public Console(PrintStream out) {
        this.out = out;
    }

    @Override
    public void printLine(String message) {
        out.println(message);
    }
}
```

This is Sherlocks link for great fitness regime:

https://www.youtube.com/watch?v=vc1E5CfRfos

#### This is a gif recorder:
```
Today i learnt a great tool for recording screen into a gif  (works only in linux) https://github.com/phw/peek  the simplicity of its design makes me happy ! (edited) 
phw/peek
Simple animated GIF screen recorder with an easy to use interface
Stars
5848
Language
```
and
```
I found one of these recently too! https://gifcap.dev/
:+1:
1

5:08
though this one doesn't need you to download anything, it just uses webrtc, which I thought was very clever
```

These were some of the commands for docker mongo:
In inverted order: 
```
mongo
brew install mongodb-community@4.2
brew tap mongodb/brew
brew doctor
brew help
brew install mongodb
brew install node
node -v
node --v
node --version
brew upgrade mongodb
brew update mongodb
brew update
bash --login
export PATH=$PATH:/usr/local/mongodb/bin
history
brew install mongoose
monog
brew install mongo-c-driver
brew install mongo
git checkout -b mongo-database-branch
docker ps -a
docker run -d -p 27017-27019:27017-27019 --name mongodb mongo:4.0.4
which mongo
```

#### I forked Sindoudou's solution to the Social Network kata:
https://github.com/sindoudou/social-network-app

https://github.com/TomSpencerLondon/social-network-app

We looked again at this kata:
https://github.com/Morinslash/SocialNetworkKata/blob/master/src/main/java/com/codurance/social_network/SocialClient.java

This was the execute method:
```
package com.codurance.social_network;

import com.codurance.social_network.commands.Command;

public class SocialClient {
    private CommandRepository commandRepo;

    public SocialClient(CommandRepository commandRepo) {
        this.commandRepo = commandRepo;
    }

    public void execute(String input) {
        Command command = commandRepo.getCommand(input);
        command.execute();
    }
}
```

#### We also looked at this for the CommandParser:
https://github.com/TomSpencerLondon/social-network-app/blob/master/src/main/java/com/sg/social/app/command/CommandParser.java
```
package com.sg.social.app.command;

import java.util.ArrayList;
import java.util.List;

import com.sg.social.app.repository.Repository;
import com.sg.social.app.util.AppPrinter;

public class CommandParser {
	private List<Command> commands = new ArrayList<>();
	private Command invalidCommand;

	public CommandParser(Repository repository, AppPrinter appPrinter) {
		commands.add(new ExitCommand(appPrinter));
		commands.add(new PostCommand(repository));
		commands.add(new ReadCommand(repository, appPrinter));
		commands.add(new FollowCommand(repository));
		commands.add(new WallCommand(repository, appPrinter));

		invalidCommand = new InvalidCommand(appPrinter);
	}

	public void executeCommand(String commandLine) {
		Command command = getCommand(commandLine);
		command.execute(commandLine);
	}

	public Command getCommand(String commandLine) {
		for (Command command : commands) {
			if (command.matches(commandLine))
				return command;
		}
		return invalidCommand;
	}

}
```