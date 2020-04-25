##### Mars Rover Command

This was the Mars Rover Command pattern implementation that Alexis and I made:
```
package com.codurance.mars_rover;

public class Rover {

    public static final String DELIMITER = ":";
    private final Position position;
    private CommandFactory commandFactory;

    public Rover(int x, int y, String direction) {
        this.position = new Position(x, y, direction);
        commandFactory = new CommandFactory();
    }

    public String execute(String commands) {
        String[] commandsArray = commands.split("");
        for (String c : commandsArray) {
            commandFactory.create(c).execute(position);
        }

        return lastPosition();
    }

    private String lastPosition() {
        return position.getX() + DELIMITER + position.getY() + DELIMITER + position.getDirection();
    }

}
```
In the execute method we call the commandFactory we initialized in the constructor. We create the Command in the Factory:
```
commandFactory.create(c)
```
and then call:
```
.execute(position)
```
This is what the create method in the Factory looks like:
```
package com.codurance.mars_rover;

public class CommandFactory {

    public Command create(String type) {
        if (type.equals("L")) {
            return new RotateLeftCommand();
        }
        if (type.equals("R")) {
            return new RotateRightCommand();
        }
        return new NullCommand();
    }
}
```
Here the Factory encapsulates the if statement for the create depending on the character that it takes. This is useful because it encapsulates the if statement for reuse. We then have a command interface from which the TurnLeftCommand and RotateRightCommands inherit:
```
package com.codurance.mars_rover;

public interface Command {

    void execute(Position position);
}
```
The RotateLeftCommand then calls the rotateLeft on the position class:
```
package com.codurance.mars_rover;

public class RotateLeftCommand implements Command {

    public void execute(Position position) {
        position.rotateLeft();
    }
}
```
The Position then changes the direction with which it is initialized:
```
package com.codurance.mars_rover;

public class Position {

    private int x;
    private int y;
    private String direction;

    public Position(int x, int y, String direction) {
        this.x = x;
        this.y = y;
        this.direction = direction;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }


    public String getDirection() {
        return direction;
    }

    public void setDirection(String direction) {
        this.direction = direction;
    }

    public void rotateLeft() {
        direction = Direction.valueOf(direction).getLeft();
    }

    public void rotateRight() {
        direction = Direction.valueOf(direction).getRight();
    }
}
```
The Position is originally passed into the constructor of the Rover in order to be used in the execute(position) called on the Command:
```
    public Rover(int x, int y, String direction) {
        this.position = new Position(x, y, direction);
        commandFactory = new CommandFactory();
    }
```