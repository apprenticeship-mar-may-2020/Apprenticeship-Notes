#### Social Networking:
I copied this code - it seems we will do the social networking kata in a couple of weeks:

https://github.com/joantolos/kata-social-networking/commit/c05679ee708e9092ab3b034f9a6b4927565e3201

This may be better:
https://github.com/andretorres-codurance/social-network-kata/blob/master/src/test/java/socialnetwork/SocialNetworkShould.java

###### This is what the exercise looks like:
Exercise
Implement a console-based social networking application (similar to Twitter) satisfying the scenarios below.
```
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

```

Details
```
The application must use the console for input and output.
Users submit commands to the application. There are four commands.
'posting', 'reading', etc. are not part of the commands; 
commands always start with the user's name.
```
This is one part:
```
posting: <user name> -> <message>
reading: <user name>
following: <user name> follows <another user>
wall: <user name> wall
```
Don't worry about handling any exceptions or invalid commands. Assume that the user will always type the correct commands. Just focus on the sunny day scenarios.
Don't bother making it work over a network or across processes. It can all be done in memory, assuming that users will all use the same terminal.
Non-existing users should be created as they post their first message. Application should not start with a pre-defined list of users.
Exercise should be done either in Java or C#.
Provide instructions on how to run the application.
IMPORTANT: Focus on writing the best code you can produce. Do not rush. Take as much time as you need; there is no deadline.

##### Refactoring

I did have a look at this for Refactoring:
Martin Fowler Refactoring the Design of Existing Code:

https://www.csie.ntu.edu.tw/~r95004/Refactoring_improving_the_design_of_existing_code.pdf

This book is clearly the inspiration for the Refactoring guru website (many of the chapters are exactly the same):
https://refactoring.guru/


This is the Social Networking Kata:
https://github.com/andretorres-codurance/social-network-kata/blob/master/src/test/java/acceptance/SocialNetworkAcceptance.java

This is the acceptance test:
```
package acceptance;

import org.junit.jupiter.api.BeforeEach;
import org.mockito.InOrder;
import socialnetwork.SocialNetwork;
import socialnetwork.commands.CommandFactory;
import socialnetwork.domain.message.MessageRepository;
import socialnetwork.domain.user.UserRepository;
import socialnetwork.environment.Clock;
import socialnetwork.environment.Console;
import org.junit.jupiter.api.Test;
import socialnetwork.formatters.MessageFormatter;
import socialnetwork.inputs.InputParser;

import java.time.LocalDateTime;

import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.inOrder;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;

public class SocialNetworkAcceptance {

    private static final LocalDateTime TEN_MINUTES_AGO = LocalDateTime.of(2018, 11, 5, 16, 55);;
    private static final LocalDateTime FIVE_MINUTES_AGO = LocalDateTime.of(2018, 11, 5, 17, 00);
    private static final LocalDateTime NOW = LocalDateTime.of(2018, 11, 5, 17, 05);
    private SocialNetwork socialNetwork;
    private Console console;
    private Clock clock;

    @BeforeEach
    void setUp() {
        console = mock(Console.class);
        clock = mock(Clock.class);
        var userRepository = new UserRepository();
        var messageRepository = new MessageRepository();
        var messageFormatter = new MessageFormatter(clock);
        var commandFactory = new CommandFactory(clock, messageRepository, userRepository, messageFormatter, console);
        var inputParser = new InputParser();
        socialNetwork = new SocialNetwork(console, commandFactory, inputParser);
    }

    @Test
    void user_can_see_timeline() {
        given(console.readLine()).willReturn(
                "Alice -> I love the weather today",
                "Alice",
                "quit"
        );
        given(clock.now()).willReturn(FIVE_MINUTES_AGO, NOW);


        socialNetwork.start();


        verify(console).printLine("I love the weather today (5 minutes ago)");
    }

    @Test
    void user_can_follow_other_users() {
        given(console.readLine()).willReturn(
                "Alice -> I love the weather today",
                "Bob -> Its really hot today",
                "Alice follows Bob",
                "Alice wall",
                "quit"
        );
        given(clock.now()).willReturn(TEN_MINUTES_AGO, FIVE_MINUTES_AGO, NOW, NOW);


        socialNetwork.start();


        InOrder inOrder = inOrder(console);
        inOrder.verify(console).printLine("Bob - Its really hot today (5 minutes ago)");
        inOrder.verify(console).printLine("Alice - I love the weather today (10 minutes ago)");
    }
}
```

This refers the classes in the application including SocialNetwork, commandFactory, MessageRepository, UserRepository, Clock, Console, MessageFormatter and InputParser.

The set up for this acceptance test looks like this:
```
    @BeforeEach
    void setUp() {
        console = mock(Console.class);
        clock = mock(Clock.class);
        var userRepository = new UserRepository();
        var messageRepository = new MessageRepository();
        var messageFormatter = new MessageFormatter(clock);
        var commandFactory = new CommandFactory(clock, messageRepository, userRepository, messageFormatter, console);
        var inputParser = new InputParser();
        socialNetwork = new SocialNetwork(console, commandFactory, inputParser);
    }

```
Here we use a mock for the console and the clock. the other classes we refer to directly. The first test looks like this:
```
    @Test
    void user_can_see_timeline() {
        given(console.readLine()).willReturn(
                "Alice -> I love the weather today",
                "Alice",
                "quit"
        );
        given(clock.now()).willReturn(FIVE_MINUTES_AGO, NOW);


        socialNetwork.start();


        verify(console).printLine("I love the weather today (5 minutes ago)");
    }
```
This means that the user can see a timeline. The mock set up for the console means that console.readLine() returns:
```
"Alice -> I love the weather today",
"Alice",
"quit"
```
This all depends on socialNetwork.start(). The other test is:
```
user_can_follow_other_users()
```

This is what socialNetwork looks like:
```
package socialnetwork;

import socialnetwork.commands.Command;
import socialnetwork.commands.CommandFactory;
import socialnetwork.domain.message.MessageRepository;
import socialnetwork.domain.user.UserRepository;
import socialnetwork.environment.Clock;
import socialnetwork.environment.Console;
import socialnetwork.exceptions.UserInterruptionException;
import socialnetwork.formatters.MessageFormatter;
import socialnetwork.inputs.InputParser;

public class SocialNetwork {
    private final Console console;
    private final CommandFactory commandFactory;
    private InputParser inputParser;

    public SocialNetwork(Console console, CommandFactory commandFactory, InputParser inputParser) {
        this.console = console;
        this.commandFactory = commandFactory;
        this.inputParser = inputParser;
    }

    public void start() {
        while (true) {
            Command command = commandFactory.create(inputParser.parse(console.readLine()));
            try {
                command.execute();
            } catch (UserInterruptionException e) {
                break;
            }
        }
    }

    public static void main(String[] args) {
        Clock clock = new Clock();
        MessageRepository messageRepository = new MessageRepository();
        UserRepository userRepository = new UserRepository();
        MessageFormatter messageFormatter = new MessageFormatter(clock);
        Console console =new Console();
        CommandFactory commandFactory = new CommandFactory(clock, messageRepository, userRepository, messageFormatter, console);
        SocialNetwork socialNetwork = new SocialNetwork(console, commandFactory, new InputParser());
        socialNetwork.start();
    }
}
```
The socialNetwork includes a console and CommandFactory.

##### Sandro Mancuso's solution also included a isFrom and isFromOneOf for the Post in the Social Networking Kata:
```
  
package com.codurance.socialnetworking.domain

import java.time.LocalDateTime

case class Post(user_name: String,
                message: String,
                date: LocalDateTime = LocalDateTime.now()) {


	def isFrom(user: User): Boolean = user_name == user.user_name

	def isFromOneOf(users: User*): Boolean = users.exists(_.user_name == user_name)

}
```
The isFromOneOf is then used in the Users class:
```
	private def isWallPost(user: User, post: Post): Boolean = {
		post.isFrom(user) || post.isFromOneOf(user.followedUsers():_*)
	}
```
This method retrieves the followedUsers from the User:
```
package com.codurance.socialnetworking.domain

import scala.collection.mutable.MutableList

case class User(user_name: String,
                followed_users: MutableList[User] = new MutableList[User]) {

	def follows(another_user: User) = {
		followed_users += another_user
	}

	def followedUsers() = {
		followed_users
	}
}
```
and iterates over them to decide if the post is from one of user.followedUsers().


###### Andre Torres' solution for the Social Network Kata was quite good:

```
    private User getUser() {
        var userOptional = userRepository.find(username);

        if (userOptional.isEmpty()) {
            var user = new User(username);
            userRepository.save(user);
            return user;
        }

        return userOptional.get();
    }
```
It included reference to an optional for finding the user:
```
public class UserRepository {

    private Set<User> users = new HashSet<>();

    public Optional<User> find(String username) {
        return users.stream()
                .filter(user -> user.username().equals(username))
                .findFirst();
    }

    public void save(User user) {
        users.add(user);
    }
}
```
This is quite a good pattern although the getOrDefault() is simpler. Andre's solution also uses the following:
```
package socialnetwork.domain.message;

import socialnetwork.domain.user.User;

import java.util.ArrayList;
import java.util.List;

import static java.util.stream.Collectors.toUnmodifiableList;

public class MessageRepository {

    private List<Message> messages = new ArrayList<>();

    public void save(Message message) {
        messages.add(message);
    }

    public List<Message> findBy(User user) {
        return messages.stream()
                .filter(message -> message.user().equals(user))
                .collect(toUnmodifiableList());
    }
}
```
the findBy method uses equals to check if the User from the message is the same as the user. We also add HashCode and Equals on the User:
```
package socialnetwork.domain.user;

import java.util.Objects;

public class User {
    private String username;

    public User(String username) {
        this.username = username;
    }

    public String username() {
        return username;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        User user1 = (User) o;
        return Objects.equals(username, user1.username);
    }

    @Override
    public int hashCode() {
        return Objects.hash(username);
    }
}
```
This part of the app is where it is run:
```
package socialnetwork;

import socialnetwork.commands.Command;
import socialnetwork.commands.CommandFactory;
import socialnetwork.domain.message.MessageRepository;
import socialnetwork.domain.user.UserRepository;
import socialnetwork.environment.Clock;
import socialnetwork.environment.Console;
import socialnetwork.exceptions.UserInterruptionException;
import socialnetwork.formatters.MessageFormatter;
import socialnetwork.inputs.InputParser;

public class SocialNetwork {
    private final Console console;
    private final CommandFactory commandFactory;
    private InputParser inputParser;

    public SocialNetwork(Console console, CommandFactory commandFactory, InputParser inputParser) {
        this.console = console;
        this.commandFactory = commandFactory;
        this.inputParser = inputParser;
    }

    public void start() {
        while (true) {
            Command command = commandFactory.create(inputParser.parse(console.readLine()));
            try {
                command.execute();
            } catch (UserInterruptionException e) {
                break;
            }
        }
    }

    public static void main(String[] args) {
        Clock clock = new Clock();
        MessageRepository messageRepository = new MessageRepository();
        UserRepository userRepository = new UserRepository();
        MessageFormatter messageFormatter = new MessageFormatter(clock);
        Console console =new Console();
        CommandFactory commandFactory = new CommandFactory(clock, messageRepository, userRepository, messageFormatter, console);
        SocialNetwork socialNetwork = new SocialNetwork(console, commandFactory, new InputParser());
        socialNetwork.start();
    }
}
```

#### command pattern:
This was the testing for the commandFactory in the Mars Rover:
```
package com.codurance.mars_rover;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;

import org.junit.jupiter.api.Test;

public class CommandFactoryShould {

    @Test
    public void create_rotate_left_instance() {
        CommandFactory commandFactory = new CommandFactory();
        Command command = commandFactory.create("L");
        assertEquals(RotateLeftCommand.class, command.getClass());
    }

    @Test
    public void create_rotate_right_instance() {
        CommandFactory commandFactory = new CommandFactory();
        Command command = commandFactory.create("R");
        assertEquals(RotateRightCommand.class, command.getClass());
    }
```
We used the same method in the Social Network Kata.

#### This link was useful for deploying a Java app on Heroku:
https://devcenter.heroku.com/articles/getting-started-with-java#deploy-the-app



This was the main method on SocialNetwork in Andrew Torres' example:

```

    public static void main(String[] args) {
        Clock clock = new Clock();
        MessageRepository messageRepository = new MessageRepository();
        UserRepository userRepository = new UserRepository();
        MessageFormatter messageFormatter = new MessageFormatter(clock);
        Console console =new Console();
        CommandFactory commandFactory = new CommandFactory(clock, messageRepository, userRepository, messageFormatter, console);
        SocialNetwork socialNetwork = new SocialNetwork(console, commandFactory, new InputParser());
        socialNetwork.start();
    }
}
```
Here we instantiate a clock and a MessageRepository. We also instantiate a UserRepository and MessageFormater with a clock in the initialize. Next we instantiate the console. The next Object we create is the commandFactory. The commandFactory is instantiated with a clock, messageRepository, userRepository, messageFormatter and console. Next we instantiate the socialNetwork with the console, commandFactory and an inline new InputParser. We then use .start() on the socialNetwork.


##### This is Joan Tolos' social networking kata:
http://www.joantolos.com/blog/kata/

It is built with Gradle:
https://github.com/joantolos/kata-social-networking

so I didn't look too far into it.


