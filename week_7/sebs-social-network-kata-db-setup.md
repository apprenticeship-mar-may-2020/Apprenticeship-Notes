###### This is Sebs multi DB Social Network set up:
```
package com.codurance.apperntice.clients;

import com.codurance.apperntice.commands.Command;
import com.codurance.apperntice.commands.CommandFactory;
import com.codurance.apperntice.service.SocialService;

public class SocialNetworkClient {
    private CommandFactory commandFactory;
    private SocialService socialService;

    public SocialNetworkClient(CommandFactory commandFactory, SocialService socialService) {
        this.commandFactory = commandFactory;
        this.socialService = socialService;
    }

    public void processUserInput(String userInput) {
        Command command = commandFactory.getCommand(userInput);
        socialService.execute(command);
    }
}
```

#### This is his CommandFactory:
```
package com.codurance.apperntice.commands;

import com.codurance.apperntice.entities.User;
import com.codurance.apperntice.utils.Parser;
import com.codurance.apperntice.service.UserService;

public class CommandFactory {
    public static final String POST_COMMAND = "->";
    public static final String FOLLOW_COMMAND = "follows";
    public static final String WALL_COMMAND = "wall";
    private Parser parser;
    private UserService userService;

    public CommandFactory(Parser parser, UserService userService) {
        this.parser = parser;
        this.userService = userService;
    }

    public Command getCommand(String userInput) {
        String[] parsedInput = parser.parseInput(userInput);
        User user = new User(parsedInput[0]);
//TODO think about other solution for parsedInput
        if (parsedInput[1] == null) {
            return new ReadCommand(user, userService);
        }
        if (parsedInput[1].equals(POST_COMMAND)) {
            return new PostCommand(user, parsedInput[2], userService);
        }
        if (parsedInput[1].equals(FOLLOW_COMMAND)) {
            return new FollowCommand(user, parsedInput[2], userService);
        }
        if (parsedInput[1].equals(WALL_COMMAND)) {
            return new WallCommand(user, userService);
        }

        throw new UnsupportedOperationException("Unknown command");
    }
}
```
This is still a series of if statements. We could make this into a list over which we would iterate using ideas from POODR:
<img width="464" alt="Screenshot 2020-04-15 at 09 49 46" src="https://user-images.githubusercontent.com/27693622/79318110-7f414b80-7efe-11ea-961c-75546e573325.png">
This is the reference I was thinking about for duck typing over a loop:
<img width="1455" alt="Screenshot 2020-04-15 at 09 51 51" src="https://user-images.githubusercontent.com/27693622/79318489-f70f7600-7efe-11ea-8e2f-e119886697a5.png">

<img width="1459" alt="Screenshot 2020-04-15 at 09 52 02" src="https://user-images.githubusercontent.com/27693622/79318553-11495400-7eff-11ea-94fe-c6e07a84fa78.png">