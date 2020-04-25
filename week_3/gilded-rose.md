##### Gilded Rose Kata
https://katalyst.codurance.com/gilded-rose

https://medium.com/@gabriellamedas/the-gilded-rose-refactoring-kata-2dd2d6f52601
###### This is quite an interesting further refactoring of the Gilded Rose Kata using the Factory Pattern:
```
class GildedRose {

    private static final int LOWEST_QUALITY_VALUE_POSSIBLE = 0;
    Item[] items;

    public GildedRose(Item[] items) {
        this.items = items;
    }

    public void updateQuality() {
        for (Item item : items) {
            customisedItem(item).updateState();
            if (hasReachedLowestQualityValue(item)) {
                item.quality = LOWEST_QUALITY_VALUE_POSSIBLE;
            } else if (hasReachedHighestQualityValue(item)) {
                item.quality = 
                   QualityValues.highestValuePossible(item);
            }
        }
    }

    private CustomisedItem customisedItem(Item item) {
        return new CustomisedItemFactory(item).customiseItem(item);
    }

    private boolean hasReachedLowestQualityValue(Item item) {
        return item.quality < LOWEST_QUALITY_VALUE_POSSIBLE;
    }

    private boolean hasReachedHighestQualityValue(Item item) {
        return item.quality >
            QualityValues.highestValuePossible(item);
    }
}
```
Here the method customisedItem creates the Item depending on the type (expressed as Item). We then call updateItem() on the Item. This then allows us to write the following:
```
public class CustomisedItemFactory {

    private final static Map<String, CustomisedItem>
         ITEM_TYPES_LIST = new HashMap<>();
    public final static String SULFURAS = 
         "Sulfuras, Hand of Ragnaros";
    public final static String BRIE = "Aged Brie";
    public final static String BACKSTAGE_PASSES_ITEM = 
        "Backstage passes to a TAFKAL80ETC concert";
    public final static String CONJURED_ITEM = "Conjured";

    public CustomisedItemFactory(Item item) {
        ITEM_TYPES_LIST.put(SULFURAS, new Sulfuras());
        ITEM_TYPES_LIST.put(BRIE, new AgedBrie(item));
        ITEM_TYPES_LIST.put(BACKSTAGE_PASSES_ITEM, new
           BackstagePassesItem(item));
        ITEM_TYPES_LIST.put(CONJURED_ITEM, new ConjuredItem(item));
    }

    public CustomisedItem customiseItem(Item item) {
        if (isStandardItem(item)) {
            return new StandardItem(item);
        }
        return ITEM_TYPES_LIST.get(item.name);
    }

    private boolean isStandardItem(Item item) {
        return !ITEM_TYPES_LIST.keySet().contains(item.name);
    }
}
```

We can then check the className and then wrap this into a method using the name:
https://stackoverflow.com/questions/6271417/java-get-the-current-class-name
```
jshell> g.getClass().getName();
$9 ==> "Goodbye"
```
We can then use this method to check the className within this method:
```
for (Item item : items) {
            customisedItem(item).updateState();
            if (hasReachedLowestQualityValue(item)) {
                item.quality = LOWEST_QUALITY_VALUE_POSSIBLE;
            } else if (hasReachedHighestQualityValue(item)) {
                item.quality = 
                   QualityValues.highestValuePossible(item);
            }
        }
```

This is Emily Bache's video:
https://www.youtube.com/watch?v=NADVhSjeyJA






This is World of Warcraft:
https://worldofwarcraft.com/en-gb/


This is an interesting article claiming that we should be able to use C# gildedRose Kata:
http://iamnotmyself.com/2012/12/08/why-most-solutions-to-gilded-rose-miss-the-bigger-picture/

This is a nice article by Michael Feathers on characterisation testing:
https://michaelfeathers.silvrback.com/characterization-testing


This is Arnaud Claudel's gilded rose kata:
https://github.com/aclaudel/gilded-rose-kata/tree/master/Java

#### This is Sandro Mancuso's version of the Golden Master:
```
package org.craftedsw.gildedrose;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;

import org.approvaltests.Approvals;
import org.junit.Before;
import org.junit.Test;

public class GildedRoseTest {

    private static final int FIXED_SEED = 100;
    private static final int NUMBER_OF_RANDOM_ITEMS = 2000;
    private static final int MINIMUM = -50;
    private static final int MAXIMUN = 101;

    private String[] itemNames = {"+5 Dexterity Vest",
                      "Aged Brie",
                      "Elixir of the Mongoose",
                      "Sulfuras, Hand of Ragnaros",
                      "Backstage passes to a TAFKAL80ETC concert",
                      "Conjured Mana Cake"};

    private Random random = new Random(FIXED_SEED);
    private GildedRose gildedRose;

    @Before
    public void initialise() {
        gildedRose = new GildedRose();
    }

    @Test public void
    should_generate_update_quality_output() throws Exception {
        List<Item> items = generateRandomItems(NUMBER_OF_RANDOM_ITEMS);

        gildedRose.updateQuality(items);

        Approvals.verify(getStringRepresentationFor(items));
    }

    private List<Item> generateRandomItems(int totalNumberOfRandomItems) {
        List<Item> items = new ArrayList<Item>();
        for (int cnt = 0; cnt < totalNumberOfRandomItems; cnt++) {
            items.add(new Item(itemName(), sellIn(), quality()));
        }
        return items;
    }

    private String itemName() {
        return itemNames[0 + random.nextInt(itemNames.length)];
    }

    private int sellIn() {
        return randomNumberBetween(MINIMUM, MAXIMUN);
    }

    private int quality() {
        return randomNumberBetween(MINIMUM, MAXIMUN);
    }

    private int randomNumberBetween(int minimum, int maximum) {
        return minimum + random.nextInt(maximum);
    }

    private String getStringRepresentationFor(List<Item> items) {
        StringBuilder builder = new StringBuilder();
        for (Item item : items) {
            builder.append(item).append("\r");
        }
        return builder.toString();
    }

}
```
This includes the following code:
```
 private static final int FIXED_SEED = 100;
 private static final int NUMBER_OF_RANDOM_ITEMS = 2000;
 private static final int MINIMUM = -50;
 private static final int MAXIMUN = 101;

 private String[] itemNames = {"+5 Dexterity Vest",
                   "Aged Brie",
                   "Elixir of the Mongoose",
                   "Sulfuras, Hand of Ragnaros",
                   "Backstage passes to a TAFKAL80ETC concert",
                   "Conjured Mana Cake"};
```
We have a FIXED_SEED constant, NUMBER_OF_RANDOM_ITEMS, MINIMUM (set at -50) and MAXIMUM (set at 101). We then have a String array of the Items. Next we have the SetUp:
```
    private Random random = new Random(FIXED_SEED);
    private GildedRose gildedRose;

    @Before
    public void initialise() {
        gildedRose = new GildedRose();
    }
s
```
We then use the nextInt() method to get the next Random integer:
```
jshell> random.nextInt(1000);
$9 ==> 988

```
We also initialize the GildedRose in the Test. These were the imports for the test:
```
package org.craftedsw.gildedrose;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;

import org.approvaltests.Approvals;
import org.junit.Before;
import org.junit.Test;
```
These included org.approvaltests.Approvals. This was the test and the generateRandomItems() method:
```
@Test public void
    should_generate_update_quality_output() throws Exception {
        List<Item> items = generateRandomItems(NUMBER_OF_RANDOM_ITEMS);

        gildedRose.updateQuality(items);

        Approvals.verify(getStringRepresentationFor(items));
    }

    private List<Item> generateRandomItems(int totalNumberOfRandomItems) {
        List<Item> items = new ArrayList<Item>();
        for (int cnt = 0; cnt < totalNumberOfRandomItems; cnt++) {
            items.add(new Item(itemName(), sellIn(), quality()));
        }
        return items;
    }
```
The test should_generate_update_quality_output() thows Exception() creates a List of items through the generateRandomItems(NUMBER_OF_RANDOM_ITEMS) method. The generateRandomItems(int totalNumberOfRandomItems). This method includes a List of items. It then iterates over the items with less iterations than the totalNumberOfRandomItems. For each of these we add a new Item with itemName, SellIn and quality for each item in the array. The method then calls gildedRose.updateQuality(items) and uses Approvals.verify(getStringVerificationFor(List)):
```
private String getStringRepresentationFor(List<Item> items) {
    StringBuilder builder = new StringBuilder();
    for (Item item : items) {
        builder.append(item).append("\r");
    }
    return builder.toString();
}
```
This method makes a StringBuilder and we then iterate over the items and append the item to the list. Each addition then has "\r" added. The "\r" means carriage return.

###### carriage return
\r (carriage return) is what it sounds like if you're familiar with old-fashioned manual typewriters: It moves the "carriage" (the roll the paper is fed through) back to the beginning of the line.26 Sep 2013

The other methods in the test are the following:
```
private String itemName() {
    return itemNames[0 + random.nextInt(itemNames.length)];
}

private int sellIn() {
    return randomNumberBetween(MINIMUM, MAXIMUN);
}

private int quality() {
    return randomNumberBetween(MINIMUM, MAXIMUN);
}

private int randomNumberBetween(int minimum, int maximum) {
    return minimum + random.nextInt(maximum);
}
```
The most interesting here is the randomNumberBetween. This returns a number between minimum and maximum numbers. The other methods: itemName(), sellIn() and quality() all return the itemName() from the names array and sellIn and quality() respectively.

The Item class was slightly changed:
```
public class Item {
    private String name;
    private int sellIn;
    private int quality;

    public Item(String name, int sellIn, int quality) {
        this.setName(name);
        this.setSellIn(sellIn);
        this.setQuality(quality);
    }

        // all getters and setters here

    @Override
    public String toString() {
        return "Item [name=" + name +
                              ", sellIn=" + sellIn +
                              ", quality=" + quality + "]";
    }
}
```
The class now includes the toString() method which is an automatically created method.

The first time the test method is executed, the line:
```
Approvals.verify(getStringRepresentationFor(items));
```
will generate a text file, in the same folder where the test class is, called: GildedRoseTest.should_generate_update_quality_output.received.txt. That mean, ..received.txt

In our mob session we implemented the following characterisation tests for the Gilded Rose:
```
package com.gildedrose;

import java.util.Arrays;
import java.util.Collection;
import java.util.Collections;
import java.util.List;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class GildedRoseTest {

  private Item[] items;
  private GildedRose gildedRose;

  @Test
  void normal_item_decrease_quality_and_sell_in_by_1() {
    String itemName = "+5 Dexterity Vest";
//        items = createItem(itemName, 10, 10);
    List<ItemDecorator> items = Collections
        .singletonList((new ThisSpecificNormalItemDecorator(new Item(itemName, 10, 10))));

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 9, 9);
  }

  @Test
  void normal_item_decrease_quality_is_never_negative() {
    String itemName = "+5 Dexterity Vest";
    items = createItem(itemName, 10, 0);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 9, 0);
  }

  @Test
  void normal_item_with_sellIn_lower_or_equal_to_0_lose_quality_twice_fast() {
    String itemName = "+5 Dexterity Vest";
    items = createItem(itemName, 0, 10);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, -1, 8);
  }

  @Test
  void aged_brie_increased_in_quality_daily() {
    String itemName = "Aged Brie";
    items = createItem(itemName, 3, 0);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 2, 1);
  }

  @Test
  void aged_brie_increased_in_quality_twice_as_fast_after_sellIn() {
    String itemName = "Aged Brie";
    items = createItem(itemName, 0, 0);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, -1, 2);
  }

  @Test
  void aged_brie_max_quality_is_50() {
    String itemName = "Aged Brie";
    items = createItem(itemName, 5, 50);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 4, 50);
  }

  @Test
  void sulfuras_never_decrease_in_quality_and_sellIn() {
    String itemName = "Sulfuras, Hand of Ragnaros";
    items = createItem(itemName, 5, 5);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 5, 5);
  }

  @Test
  void backstage_pass_increase_quality_by_1_when_sell_in_above_10() {
    String itemName = "Backstage passes to a TAFKAL80ETC concert";
    items = createItem(itemName, 11, 5);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 10, 6);
  }

  @Test
  void backstage_pass_increase_quality_by_2_when_sell_in_less_then_10() {
    String itemName = "Backstage passes to a TAFKAL80ETC concert";
    items = createItem(itemName, 10, 5);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 9, 7);
  }

  @Test
  void backstage_pass_increase_quality_by_3_when_sell_in_less_then_3() {
    String itemName = "Backstage passes to a TAFKAL80ETC concert";
    items = createItem(itemName, 3, 5);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 2, 8);
  }

  @Test
  void backstage_pass_quality_is_0_when_sell_in_0() {
    String itemName = "Backstage passes to a TAFKAL80ETC concert";
    items = createItem(itemName, 0, 10);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, -1, 0);
  }

  @Test
  void conjured_should_degrade_twice_as_fast_as_normal() {
    String itemName = "Conjured Mana Cake";
    items = createItem(itemName, 10, 10);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 9, 8);
  }

  @Test
  void conjured_should_degrade_twice_as_fast_after_expiry() {
    String itemName = "Conjured Mana Cake";
    items = createItem(itemName, 0, 10);

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, -1, 6);
  }

  private Item[] createItem(String itemName, int sellIn, int quality) {
    return new Item[]{new Item(itemName, sellIn, quality)};
  }

  private void assertItemFields(String itemName, int sellIn, int quality) {
    assertEquals(itemName, gildedRose.items[0].name);
    assertEquals(sellIn, gildedRose.items[0].sellIn);
    assertEquals(quality, gildedRose.items[0].quality);
  }
}
```

This is the first characterisation test:
```
class GildedRoseTest {

  private Item[] items;
  private GildedRose gildedRose;

  @Test
  void normal_item_decrease_quality_and_sell_in_by_1() {
    String itemName = "+5 Dexterity Vest";
//        items = createItem(itemName, 10, 10);
    List<ItemDecorator> items = Collections
        .singletonList((new ThisSpecificNormalItemDecorator(new Item(itemName, 10, 10))));

    gildedRose = new GildedRose(items);
    gildedRose.updateQuality();

    assertItemFields(itemName, 9, 9);
  }

```
This creates an itemName and then attempts to add the Item from the ItemName with Collections.singletonList(newThisSpecificNormalItemDecorator(new Item(itemName, 10, 10))). We then create the GildedRose and then update the gildedRose. This method uses test methods:
```

  private Item[] createItem(String itemName, int sellIn, int quality) {
    return new Item[]{new Item(itemName, sellIn, quality)};
  }

  private void assertItemFields(String itemName, int sellIn, int quality) {
    assertEquals(itemName, gildedRose.items[0].name);
    assertEquals(sellIn, gildedRose.items[0].sellIn);
    assertEquals(quality, gildedRose.items[0].quality);
  }

```
The createItem creates an Item according to the name, sellin and quality. The assertItemFields checks the name, sellIn and quality against the gildedRose.items[0]. The Golden Master test was quite clever in that it used StringBuilder and new File() to create and write to the file:
```
package com.gildedrose;

import org.junit.jupiter.api.Test;

import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class GoldenMasterTest {

    @Test
    void golden_master() throws FileNotFoundException {
        String goldenMasterOutput = getStringBuilder("./goldenMaster.txt").toString();

        TexttestFixture.main(new String[]{});

        String sample = getStringBuilder("./sample.txt").toString();

        assertEquals(goldenMasterOutput, sample);
    }

    private StringBuilder getStringBuilder(String path) throws FileNotFoundException {
        StringBuilder file = new StringBuilder();
        File goldenMaster = new File(path);
        Scanner myReader = new Scanner(goldenMaster);
        while (myReader.hasNextLine()) {
            file.append(myReader.nextLine());

        }
        System.out.println(file.toString());
        myReader.close();
        return file;
    }
}
```

This was the test:
```
    @Test
    void golden_master() throws FileNotFoundException {
        String goldenMasterOutput = getStringBuilder("./goldenMaster.txt").toString();

        TexttestFixture.main(new String[]{});

        String sample = getStringBuilder("./sample.txt").toString();

        assertEquals(goldenMasterOutput, sample);
    }
```
The test takes the result of the goldenMaster.txt and then calls TexttestFixture.main(new String[]{}). It then gets the result of the sample.txt and then compares the contents of the two files and checks if they are the same. This is the TexttestFixture:
```
package com.gildedrose;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.PrintStream;

public class TexttestFixture {
    public static void main(String[] args) throws FileNotFoundException {
        System.out.println("OMGHAI!");

        Item[] items = new Item[] {
                new Item("+5 Dexterity Vest", 10, 20), //
                new Item("Aged Brie", 2, 0), //
                new Item("Elixir of the Mongoose", 5, 7), //
                new Item("Sulfuras, Hand of Ragnaros", 0, 80), //
                new Item("Sulfuras, Hand of Ragnaros", -1, 80),
                new Item("Backstage passes to a TAFKAL80ETC concert", 15, 20),
                new Item("Backstage passes to a TAFKAL80ETC concert", 10, 49),
                new Item("Backstage passes to a TAFKAL80ETC concert", 5, 49),
                // this conjured item does not work properly yet
                new Item("Conjured Mana Cake", 3, 6) };

        GildedRose app = new GildedRose(items);

        int days = 2;
        if (args.length > 0) {
            days = Integer.parseInt(args[0]) + 1;
        }


        File file = new File("./sample.txt");
        //Instantiating the PrintStream class
        PrintStream stream = new PrintStream(file);
        System.out.println("From now on "+file.getAbsolutePath()+" will be your console");
        System.setOut(stream);
        //Printing values to file


        for (int i = 0; i < days; i++) {
            System.out.println("-------- day " + i + " --------");
            System.out.println("name, sellIn, quality");
            for (Item item : items) {
                System.out.println(item);
            }
            System.out.println();
            app.updateQuality();
        }


    }

}
```
Here we create an array of the Items:
```
        Item[] items = new Item[] {
                new Item("+5 Dexterity Vest", 10, 20), //
                new Item("Aged Brie", 2, 0), //
                new Item("Elixir of the Mongoose", 5, 7), //
                new Item("Sulfuras, Hand of Ragnaros", 0, 80), //
                new Item("Sulfuras, Hand of Ragnaros", -1, 80),
                new Item("Backstage passes to a TAFKAL80ETC concert", 15, 20),
                new Item("Backstage passes to a TAFKAL80ETC concert", 10, 49),
                new Item("Backstage passes to a TAFKAL80ETC concert", 5, 49),
                // this conjured item does not work properly yet
                new Item("Conjured Mana Cake", 3, 6) };

```
We then instantiate the GildedRose(items):
```
GildedRose app = new GildedRose(items);

        int days = 2;
        if (args.length > 0) {
            days = Integer.parseInt(args[0]) + 1;
        }


        File file = new File("./sample.txt");
        //Instantiating the PrintStream class
        PrintStream stream = new PrintStream(file);
        System.out.println("From now on "+file.getAbsolutePath()+" will be your console");
        System.setOut(stream);
        //Printing values to file
```
The main point here is that we make a new ./sample.txt file and then create a PrintStream with the file. We then setOut(stream) from System. Next we iterate over the items and System.out.println for each of them:
```

        for (int i = 0; i < days; i++) {
            System.out.println("-------- day " + i + " --------");
            System.out.println("name, sellIn, quality");
            for (Item item : items) {
                System.out.println(item);
            }
            System.out.println();
            app.updateQuality();
        }
```
This enables us to see a printed result for the items. This is our refactoring for the updateQuality method on GildedRose:
```
package com.gildedrose;

import java.util.List;

class GildedRose {

  Item[] items;
  List<ItemDecorator> itemsDecorated;

  public GildedRose(Item[] items) {
    this.items = items;
  }

  public GildedRose(List<ItemDecorator> itemsDecorated) {
    this.itemsDecorated = itemsDecorated;
  }

  public void updateQuality() {
    ItemDecorator itemDecorator;
    for (Item item : items) {
      switch (item.name) {
        case "Sulfuras, Hand of Ragnaros":
          itemDecorator = new LegendaryDecorator(item);
          itemDecorator.update();
          break;
        case "Aged Brie":
          itemDecorator = new ThisSpecificCheeseDecorator(item);
          itemDecorator.update();
          break;
        case "Backstage passes to a TAFKAL80ETC concert":
          itemDecorator = new ThisSpecificBackStagePassToATAFKL80ETCConcert(item);
          itemDecorator.update();
          break;
        case "Conjured Mana Cake":
          itemDecorator = new ThisSpecificConjuredManCakeDecorator(item);
          itemDecorator.update();
          break;
        default:
          itemDecorator = new ThisSpecificNormalItemDecorator(item);
          itemDecorator.update();
          break;
      }
    }
  }
}
```
Here we create Decorators for each item and then call update on them. We use the Decorator as a wrapper so that we don't have to call any methods on the Item as the Goblin would not like this:
```
package com.gildedrose;

public abstract class ItemDecorator {

    protected Item item;

    public ItemDecorator(Item item) {
        this.item = item;
    }

    protected boolean isExpired(Item item) {
        return item.sellIn < 0;
    }

    protected void decreaseSellIn(Item item) {
        item.sellIn--;
    }

    abstract void update();
}
```
This enables us to make specific wrappers for each of the Items:
```
package com.gildedrose;

public class ThisSpecificBackStagePassToATAFKL80ETCConcert extends IncreasableDecorator {
    public static final int TEN_DAYS = 10;
    public static final int FIVE_DAYS = 5;

    public ThisSpecificBackStagePassToATAFKL80ETCConcert(Item item) {
        super(item);
    }

    private void updateBackstagePassesQuality(Item item) {
        increaseQuality(item);

        if (concertWithin(item, TEN_DAYS)) {
            increaseQuality(item);
        }

        if (concertWithin(item, FIVE_DAYS)) {
            increaseQuality(item);
        }
    }

    private boolean concertWithin(Item item, int i) {
        return item.sellIn <= i;
    }

    private void expiredQuality(Item item) {
        item.quality = 0;
    }

    void update() {
        updateBackstagePassesQuality(item);
        decreaseSellIn(item);
        if (isExpired(item)) {
            expiredQuality(item);
        }
    }

}
```
Next we will create a Factory in order to instantiate the ItemDecorators. This is the Decorator pattern:
https://refactoring.guru/design-patterns/decorator
![example](https://user-images.githubusercontent.com/27693622/77366512-12e48980-6d50-11ea-8c58-6aeab392faa0.png)

This was what our version of the GoldenMaster looked like:
![Screenshot 2020-03-23 at 15 23 03](https://user-images.githubusercontent.com/27693622/77366537-1f68e200-6d50-11ea-86c4-54c8f444b5f9.png)


This was Sandro's article on Testing Legacy Code with Golden Master:
https://codurance.com/2012/11/11/testing-legacy-code-with-golden-master/

This is Emily Bache's video on Gilded Rose:
https://www.youtube.com/watch?v=NADVhSjeyJA

