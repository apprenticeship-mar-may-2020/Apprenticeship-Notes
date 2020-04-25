###### This was the description of the kata:
https://gist.github.com/edtheshed/ae4984fd253986343babb597344c4772

```
Corn, Chicken, Fox Kata

A farmer is trying to transport his stuff to the other side of a river. He has a boat to do this, but only space for one 
thing at a time.

If he took the corn over first, the fox would eat the chicken.
If he took the fox over first the chicken would eat the corn.

He needs to be there or take the right items in the right order to prevent these things from happening.

Find the order in which he should take the items!

Harder version:

You now have 10 bags of corn, 10 chickens and 10 foxes. Your boat can transport 2 items at once. If the number of 
chickens is 2 or more than the number of foxes, the chickens don’t get eaten, as they band together to defend themselves. 
If the number of foxes is not less than half the number of chickens, the chickens are too afraid to eat the corn

```


##### This was Alexis and Sebastian's code for Ed's Kata:
Alexis may clean this out over the weekend 
https://github.com/apavlidi/Eds-Kata/blob/master/src/test/java/Acceptance.java
This was their Acceptance test:
```
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.ArrayList;
import java.util.List;
import org.junit.jupiter.api.Test;

public class Acceptance {

  @Test
  public void move_in_correct_order() {
    List<Item>  items = new ArrayList<>();
    items.add(new Item("corn"));
    items.add(new Item("chicken"));
    items.add(new Item("fox"));

    RiverSide leftSide = new RiverSide(items);
    RiverSide rightSide = new RiverSide();
    RiverService service = new RiverService(leftSide, rightSide);

    service.transportFromLeft(new Item("chicken"));
    service.transportFromLeft(new Item("fox"));
    service.transportFromRight(new Item("chicken"));
    service.transportFromLeft(new Item("corn"));
    service.transportFromLeft(new Item("chicken"));

    assertTrue(service.checkStock());
  }

}
```
In this test Alexis and Sebastian check that the RiverService service (instantiated with Left and right River sides) can transport from left and from right for the corn, chicken and fox. The RiverService test checks the RiverService itself:
```
  
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTrue;

import exceptions.InvalidOperation;
import java.awt.dnd.InvalidDnDOperationException;
import java.util.ArrayList;
import java.util.List;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class RiverServiceTest {

  private List<Item> items;
  private RiverSide leftSide;
  private RiverSide rightSide;
  private RiverService service;

  @BeforeEach
  void setUp() {
    createItems();
    leftSide = new RiverSide(items);
    rightSide = new RiverSide();
    service = new RiverService(leftSide, rightSide);
  }

  private void createItems() {
    items = new ArrayList<>();
    items.add(new Item("corn"));
    items.add(new Item("chicken"));
    items.add(new Item("fox"));
  }

  @Test
  public void state_is_valid_when_move_chicken_from_left_side_to_right_side() {
    service.transportFromLeft(new Item("chicken"));

    assertTrue(service.checkStock());
  }

  @Test
  public void state_is_valid_when_move_chicken_and_fox_to_right_side_and_then_chicken_back() {
    service.transportFromLeft(new Item("chicken"));
    service.transportFromLeft(new Item("fox"));
    service.transportFromRight(new Item("chicken"));

    assertTrue(service.checkStock());
  }

  @Test
  public void state_is_valid_when_move_chicken_and_fox_to_right_side_and_farmer_is_present() {
    service.transportFromLeft(new Item("chicken"));
    service.transportFromLeft(new Item("fox"));

    assertTrue(service.checkStock());
  }

  @Test
  public void state_is_invalid_when_move_fox_to_right_side_and_farmer_is_present() {
    assertThrows(InvalidOperation.class, () -> service.transportFromLeft(new Item("fox")));
  }

  @Test
  public void state_is_invalid_when_move_chicken_and_fox_to_right_side_and_farmer_not_is_present() {
    assertThrows(InvalidOperation.class, () -> {
      service.transportFromLeft(new Item("chicken"));
      service.transportFromLeft(new Item("fox"));
    });
  }

}
```
This is the set up section of this test:
```
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTrue;

import exceptions.InvalidOperation;
import java.awt.dnd.InvalidDnDOperationException;
import java.util.ArrayList;
import java.util.List;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class RiverServiceTest {

  private List<Item> items;
  private RiverSide leftSide;
  private RiverSide rightSide;
  private RiverService service;

  @BeforeEach
  void setUp() {
    createItems();
    leftSide = new RiverSide(items);
    rightSide = new RiverSide();
    service = new RiverService(leftSide, rightSide);
  }

  private void createItems() {
    items = new ArrayList<>();
    items.add(new Item("corn"));
    items.add(new Item("chicken"));
    items.add(new Item("fox"));
  }
```
Here we have private fields for the List of Items, the Riverside (leftside and rightside) and the RiverService. In our BeforeEach method we call createItems(). This method creates an ArrayList for the items and then adds a new corn, chicken and fox item to the ArrayList. We then instantiate a new Riverside(items) with items. We then ensure that checkStock() method returns true for each of these tests:
```
@Test
  public void state_is_valid_when_move_chicken_from_left_side_to_right_side() {
    service.transportFromLeft(new Item("chicken"));

    assertTrue(service.checkStock());
  }

  @Test
  public void state_is_valid_when_move_chicken_and_fox_to_right_side_and_then_chicken_back() {
    service.transportFromLeft(new Item("chicken"));
    service.transportFromLeft(new Item("fox"));
    service.transportFromRight(new Item("chicken"));

    assertTrue(service.checkStock());
  }

  @Test
  public void state_is_valid_when_move_chicken_and_fox_to_right_side_and_farmer_is_present() {
    service.transportFromLeft(new Item("chicken"));
    service.transportFromLeft(new Item("fox"));

    assertTrue(service.checkStock());
  }

```
These all ensure that the moves of Chicken fox chicken are correct:
```
  @Test
  public void state_is_invalid_when_move_fox_to_right_side_and_farmer_is_present() {
    assertThrows(InvalidOperation.class, () -> service.transportFromLeft(new Item("fox")));
  }

  @Test
  public void state_is_invalid_when_move_chicken_and_fox_to_right_side_and_farmer_not_is_present() {
    assertThrows(InvalidOperation.class, () -> {
      service.transportFromLeft(new Item("chicken"));
      service.transportFromLeft(new Item("fox"));
    });
  }
```
The next tests (above) ensure that the state is invalid when the fox is moved to the right side and the farmer is present. The state is invalid when we move the chicken and fox to the right sie and the farmer is not present.

The RiverSide test is the next one:
```
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.ArrayList;
import java.util.List;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class RiverSideTest {

  public static final Item FOX = new Item("fox");
  public static final Item CORN = new Item("corn");
  public static final Item CHICKEN = new Item("chicken");
  private List<Item> items;
  private RiverSide sideWithStock;
  private RiverSide sideWithoutStock;

  @BeforeEach
  void setUp() {
    items = new ArrayList<>();
    items.add(CORN);
    items.add(CHICKEN);
    items.add(FOX);
    sideWithStock = new RiverSide(items);
    sideWithoutStock = new RiverSide();
  }

  @Test
  public void state_is_true_when_corn_fox_and_chicken_on_list() {
    assertTrue(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_no_items() {
    assertTrue(sideWithoutStock.state());
  }

  @Test
  public void state_is_false_when_corn_and_chicken_on_list() {
    sideWithStock.transport(FOX);

    assertFalse(sideWithStock.state());
  }

  @Test
  public void state_is_false_when_chicken_and_fox_on_list() {
    sideWithStock.transport(CORN);

    assertFalse(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_corn_and_fox_on_list() {
    sideWithStock.transport(CHICKEN);

    assertTrue(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_trasport_fox_and_corn() {
    sideWithStock.transport(FOX);
    sideWithStock.transport(CORN);

    assertTrue(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_receive_chicken_only() {
    sideWithoutStock.receive(CHICKEN);

    assertTrue(sideWithoutStock.state());
  }

  @Test
  public void state_is_true_when_receive_fox_only() {
    sideWithoutStock.receive(FOX);

    assertTrue(sideWithoutStock.state());
  }

  @Test
  public void state_is_true_when_receive_corn_only() {
    sideWithoutStock.receive(CORN);

    assertTrue(sideWithoutStock.state());
  }

  @Test
  public void state_is_true_when_receive_fox_and_corn_only() {
    sideWithoutStock.receive(CORN);
    sideWithoutStock.receive(FOX);

    assertTrue(sideWithoutStock.state());
  }

}
```
This test adds the items to the items array:
```
public class RiverSideTest {

  public static final Item FOX = new Item("fox");
  public static final Item CORN = new Item("corn");
  public static final Item CHICKEN = new Item("chicken");
  private List<Item> items;
  private RiverSide sideWithStock;
  private RiverSide sideWithoutStock;

  @BeforeEach
  void setUp() {
    items = new ArrayList<>();
    items.add(CORN);
    items.add(CHICKEN);
    items.add(FOX);
    sideWithStock = new RiverSide(items);
    sideWithoutStock = new RiverSide();
  }

  @Test
  public void state_is_true_when_corn_fox_and_chicken_on_list() {
    assertTrue(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_no_items() {
    assertTrue(sideWithoutStock.state());
  }

```
Then instantiates the Riverside with the items and then checks the state is true when the corn fox and chicken are on the list. WE then have the following tests:
```

  @Test
  public void state_is_false_when_corn_and_chicken_on_list() {
    sideWithStock.transport(FOX);

    assertFalse(sideWithStock.state());
  }

  @Test
  public void state_is_false_when_chicken_and_fox_on_list() {
    sideWithStock.transport(CORN);

    assertFalse(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_corn_and_fox_on_list() {
    sideWithStock.transport(CHICKEN);

    assertTrue(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_trasport_fox_and_corn() {
    sideWithStock.transport(FOX);
    sideWithStock.transport(CORN);

    assertTrue(sideWithStock.state());
  }

  @Test
  public void state_is_true_when_receive_chicken_only() {
    sideWithoutStock.receive(CHICKEN);

    assertTrue(sideWithoutStock.state());
  }

```
These next tests all check for the True state:
```

  @Test
  public void state_is_true_when_receive_fox_only() {
    sideWithoutStock.receive(FOX);

    assertTrue(sideWithoutStock.state());
  }

  @Test
  public void state_is_true_when_receive_corn_only() {
    sideWithoutStock.receive(CORN);

    assertTrue(sideWithoutStock.state());
  }

  @Test
  public void state_is_true_when_receive_fox_and_corn_only() {
    sideWithoutStock.receive(CORN);
    sideWithoutStock.receive(FOX);

    assertTrue(sideWithoutStock.state());
  }
```
This is the exceptions class:
```
package exceptions;

public class InvalidOperation extends RuntimeException {
}
```
This is the Item which is a POJO. This is the RiverService:
```
import exceptions.InvalidOperation;

public class RiverService {

  private final RiverSide leftSide;
  private final RiverSide rightSide;
  private boolean isFarmerOnTheLeft = true;

  public RiverService(RiverSide leftSide, RiverSide rightSide) {
    this.leftSide = leftSide;
    this.rightSide = rightSide;
  }

  public boolean checkStock() {
    return isLeftSideValid() && isRightSideValid();
  }

  private boolean isRightSideValid() {
    return rightSide.state() || !isFarmerOnTheLeft;
  }

  private boolean isLeftSideValid() {
    return leftSide.state() || isFarmerOnTheLeft;
  }

  public void transportFromLeft(Item item) {
    leftSide.transport(item);
    rightSide.receive(item);
    isFarmerOnTheLeft = false;
    validateState();
  }

  public void transportFromRight(Item item) {
    rightSide.transport(item);
    leftSide.receive(item);
    isFarmerOnTheLeft = true;
    validateState();
  }

  private void validateState() {
    if (!checkStock()) {
      throw new InvalidOperation();
    }
  }

}
```
After initializing with Riversides and boolean farmer is on left:
```
  private final RiverSide leftSide;
  private final RiverSide rightSide;
  private boolean isFarmerOnTheLeft = true;

  public RiverService(RiverSide leftSide, RiverSide rightSide) {
    this.leftSide = leftSide;
    this.rightSide = rightSide;
  }

  public boolean checkStock() {
    return isLeftSideValid() && isRightSideValid();
  }
```
The checkStock method returns the result of isLeftSideValid and isRightSideValid:
```
  private boolean isRightSideValid() {
    return rightSide.state() || !isFarmerOnTheLeft;
  }

  private boolean isLeftSideValid() {
    return leftSide.state() || isFarmerOnTheLeft;
  }
```
These two methods ensure that the rightSide state is correct and the the farmer is not on the left.
This checks against the state() method on the Riverside:
```

  public boolean state() {
    return !contains("chicken") || (contains("corn") == contains("fox"));
  }

  private boolean contains(String chicken) {
    return item.stream().anyMatch(byName(chicken));
  }

  private Predicate<Item> byName(String itemName) {
    return i -> i.name.equals(itemName);
  }
```
This state ensures that the state does not contain chicken but contains corn and fox. This is clever but doesn't quite work. The Predicate byName checks the itemName is equal to the itemName parameter.

This was Ewan and my ChickenFarmerCornFox:
https://github.com/ewansheldon/corn-chicken-fox

We looked at the state pattern for this:
https://refactoring.guru/design-patterns/state/java/example

This was the State Interface from refactoring guru:
```
states/State.java: Common state interface
package refactoring_guru.state.example.states;

import refactoring_guru.state.example.ui.Player;

/**
 * Common interface for all states.
 */
public abstract class State {
    Player player;

    /**
     * Context passes itself through the state constructor. This may help a
     * state to fetch some useful context data if needed.
     */
    State(Player player) {
        this.player = player;
    }

    public abstract String onLock();
    public abstract String onPlay();
    public abstract String onNext();
    public abstract String onPrevious();
}
```
This was the LockedState:
```
package refactoring_guru.state.example.states;

import refactoring_guru.state.example.ui.Player;

/**
 * Concrete states provide the special implementation for all interface methods.
 */
public class LockedState extends State {

    LockedState(Player player) {
        super(player);
        player.setPlaying(false);
    }

    @Override
    public String onLock() {
        if (player.isPlaying()) {
            player.changeState(new ReadyState(player));
            return "Stop playing";
        } else {
            return "Locked...";
        }
    }

    @Override
    public String onPlay() {
        player.changeState(new ReadyState(player));
        return "Ready";
    }

    @Override
    public String onNext() {
        return "Locked...";
    }

    @Override
    public String onPrevious() {
        return "Locked...";
    }
}
```
which overrode the interface methods.

