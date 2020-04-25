###### Shopping Basket
https://github.com/codurance/apprenticeship/wiki/Shopping-Basket-Kata

https://github.com/emilybache/GildedRose-Refactoring-Kata/blob/master/GildedRoseRequirements.txt

This is the description:
```
Shopping Basket Kata
threenary edited this page on 12 Sep 2019 · 9 revisions
Shopping Cart Kata
Scenario 1 - Add items to shopping basket
Given I add 2 units of "The Hobbit" to my shopping basket And I add 5 units of "Breaking Bad" When I check the content of my shopping basket Then it should contain the following infomation:

Creation date (of the shopping basket)
2 x The Hobbit // 2 x 5.00 = £10.00
5 x Breaking Bad // 5 x 7.00 = £35.00
Total: £45.00
Acceptance criteria:

Shopping basket should be created when the first product is added.
Shopping basket should be persisted (In-memory, DB later)
Each user should have her own shopping basket.
Products available (in-memory repository):

Books
10001: Lord of the Rings - £10.00
10002: The Hobbit - £5.00
DVDs
20001: Game of Thrones - £9.00
20110: Breaking Bad - £7.00
    public class ShoppingBasketService {
    
        public void addItem(UserID userId, ProductID productId, int quantity) { }

        public <?> basketFor(UserID userId) { }
    
    }    
Scenario 2 - Logging
Log items added to shopping cart on the console:
[BASKET CREATED]: Created[<"YYYY-07-12">], User[]
[ITEM ADDED TO SHOPPING CART]: Added[<"YYYY-07-12">], User[], Product[], Quantity[], Price[<£12.00>]

```

This is the video on Shopping Basket:
https://www.youtube.com/watch?v=kBNThogwWYw

This is the shopping basket kata:
https://github.com/codurance/apprenticeship/wiki/Shopping-Basket-Kata

This is the description of the kata from the video:
http://codekata.com/kata/kata09-back-to-the-checkout/

This is the rules of Object Calisthenics:

We ran an Object Calisthenics variation of Coding Dojo on Wednesday night as part of ThoughtWorks Geek Night in Sydney.

Object Calisthenics is an idea suggest by Jeff Bay in The ThoughtWorks Anthology , and lists 9 rules to writing better Object Oriented code. For those who haven’t seen the book, the 9 rules are:

Use only one level of indentation per method
Don't use the else keyword
Wrap all primitives and strings
Use only one dot per line
Don't abbreviate
Keep all entities small
Don't use any classes with more than two instance variables
Use first-class collections
Don't use any getters/setters/properties


##### Deploying code online:
This video covers how to deploy the Java code online:
https://www.udemy.com/course/deploy-java-spring-apps-online/

This is the website:
http://www.mysupercoolspringapp.com/customer/showFormForAdd

This is the website for deploying to AWS:
http://codersuk.com/docs/shared/introduction-to-continuous-delivery.html

Sebastian added:
for the dependencies.
```
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.3.1</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>2.21.0</version>
    <scope>test</scope>
</dependency>
<plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.19.1</version>
    <dependencies>
        <dependency>
             <groupId>org.junit.platform</groupId>
             <artifactId>junit-platform-surefire-provider</artifactId>
             <version>1.0.1</version>
         </dependency>
     </dependencies>
</plugin>
<dependency>
    <groupId>org.junit.platform</groupId>
    <artifactId>junit-platform-runner</artifactId>
    <version>1.2.0</version>
    <scope>test</scope>
</dependency>
<dependency>
     <groupId>org.junit.vintage</groupId>
     <artifactId>junit-vintage-engine</artifactId>
     <version>5.2.0</version>
     <scope>test</scope>
</dependency>
```

This is the slightly different checkout kata:
http://codekata.com/kata/kata09-back-to-the-checkout/
https://www.youtube.com/watch?v=c8jKUXcTDc8



###### Finally I have found Sandro Mancuso's version:
https://github.com/sandromancuso/shopping-basket

This includes a basket, discount and product folder structure.

This is what the ShoppingBasketService looks like:
```
package com.codurance.shoppingbasket.basket;

import com.codurance.shoppingbasket.UserID;
import com.codurance.shoppingbasket.discount.Discount;
import com.codurance.shoppingbasket.discount.DiscountCalculator;
import com.codurance.shoppingbasket.product.Product;
import com.codurance.shoppingbasket.product.ProductID;
import com.codurance.shoppingbasket.product.ProductService;

public class ShoppingBasketService {

    private ProductService productService;
    private ShoppingBasketRepository shoppingBasketRepository;
	private DiscountCalculator discountCalculator;

	public ShoppingBasketService(ProductService productService,
	                             ShoppingBasketRepository shoppingBasketRepository,
	                             DiscountCalculator discountCalculator) {
        this.productService = productService;
        this.shoppingBasketRepository = shoppingBasketRepository;
		this.discountCalculator = discountCalculator;
	}

    public void addItem(UserID userID, ProductID productID, int quantity) {
    	abortIfNotEnoughItemsInStock(productID, quantity);
	    addProductToBasket(productID, quantity, basketFor(userID));
    }

	public ShoppingBasket basketFor(UserID userID) {
		return shoppingBasketRepository.basketFor(userID);
	}

	public Discount basketDiscount(UserID userID) {
		ShoppingBasket shoppingBasket = shoppingBasketRepository.basketFor(userID);
		return discountCalculator.discountFor(shoppingBasket);
	}

	private void addProductToBasket(ProductID productID, int quantity, ShoppingBasket basket) {
		Product product = productService.productFor(productID);
		basket.add(new ShoppingBasketItem(product, quantity));
		shoppingBasketRepository.save(basket);
	}

	private void abortIfNotEnoughItemsInStock(ProductID productID, int quantity) {
		if (!productService.hasEnoughItemsInStock(productID, quantity)) {
			throw new NotEnoughItemsInStockException();
		}
	}
}

```
This class includes addItem(id, productid, quantity) and basketFor method:
```
package com.codurance.shoppingbasket.basket;

import com.codurance.shoppingbasket.UserID;

import java.util.HashMap;
import java.util.Map;

public class ShoppingBasketRepository {

    private Clock clock;
    private Map<UserID, ShoppingBasket> baskets = new HashMap<>();

    public ShoppingBasketRepository(Clock clock) {
        this.clock = clock;
    }

    ShoppingBasket basketFor(UserID userId) {
        return baskets.getOrDefault(userId, createBasket(userId));
    }

    void save(ShoppingBasket basket) {
        baskets.put(basket.userId(), basket);
    }

    private ShoppingBasket createBasket(UserID userID) {
        return new ShoppingBasket(userID, clock.today());
    }
}
```

#### We also saw the generics symbol:
https://stackoverflow.com/questions/11392380/generics-what-does-actually-mean
```
Its a wildcard type. It is short for ? extends Object

If you get it, all you know is its an Object. If you try to set, you can't because it could be any sub class of Object.
```
From Java documentation:

In generic code, the question mark (?), called the wildcard, represents an unknown type. The wildcard can be used in a variety of situations: as the type of a parameter, field, or local variable; sometimes as a return type (though it is better programming practice to be more specific). The wildcard is never used as a type argument for a generic method invocation, a generic class instance creation, or a supertype.

So, for instance, a List<?> is a list containing objects from unknown type.


##### These were the diagrams that Alexis made to help the design of the Shopping Basket

![Screenshot 2020-03-18 at 13 07 01](https://user-images.githubusercontent.com/27693622/76998887-b21f1080-694d-11ea-8421-fa3dec2337eb.png)

##### This was the CRC card:
![Screenshot 2020-03-18 at 13 05 08](https://user-images.githubusercontent.com/27693622/77000785-d16b6d00-6950-11ea-85cc-0d9803b5ed17.png)


#### This was the shopping cart kata which actually turned out to be completely different to the Shopping Basket kata:
https://www.youtube.com/watch?v=kBNThogwWYw&list=PLBwHb8bunj7jbtKF0jrSm_50N-AXhfFVV&index=3


##### getOrDefault:
https://docs.oracle.com/javase/8/docs/api/java/util/Map.html
default V	getOrDefault(Object key, V defaultValue)
Returns the value to which the specified key is mapped, or defaultValue if this map contains no mapping for the key.

###### Shopping Basket Kata by S Mancuso:

This kata starts with an acceptance test:
```
package acceptance;

import com.codurance.shoppingbasket.UserID;
import com.codurance.shoppingbasket.basket.Clock;
import com.codurance.shoppingbasket.basket.ShoppingBasket;
import com.codurance.shoppingbasket.basket.ShoppingBasketRepository;
import com.codurance.shoppingbasket.basket.ShoppingBasketService;
import com.codurance.shoppingbasket.discount.DiscountCalculator;
import com.codurance.shoppingbasket.discount.NoDiscount;
import com.codurance.shoppingbasket.product.ProductService;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.Mock;
import org.mockito.runners.MockitoJUnitRunner;

import java.math.BigDecimal;
import java.time.LocalDate;

import static com.codurance.shoppingbasket.basket.ShoppingBasketBuilder.aShoppingBasket;
import static com.codurance.shoppingbasket.product.ProductService.BREAKING_BAD;
import static com.codurance.shoppingbasket.product.ProductService.THE_HOBBIT;
import static java.util.Collections.singletonList;
import static org.hamcrest.core.Is.is;
import static org.junit.Assert.assertThat;
import static org.mockito.BDDMockito.given;

@RunWith(MockitoJUnitRunner.class)
public class AddItemsToShoppingBasketFeature {

    private static final UserID USER_ID_1 = new UserID("1234");
    private static final LocalDate CREATION_DATE = LocalDate.of(2016, 7, 1);

    private static final int QTY_2 = 2;
    private static final int QTY_5 = 5;

    @Mock
    Clock clock;

    private ShoppingBasketService shoppingBasketService;

    @Before
    public void initialise() {
        ShoppingBasketRepository shoppingBasketRepository = new ShoppingBasketRepository(clock);
        ProductService productService = new ProductService();
	    DiscountCalculator discountCalculator = new DiscountCalculator(singletonList(new NoDiscount()));
	    shoppingBasketService = new ShoppingBasketService(productService, shoppingBasketRepository, discountCalculator);
    }

    @Test public void
    return_basket_containing_all_the_items_added_to_it() {
        given(clock.today()).willReturn(CREATION_DATE);
        shoppingBasketService.addItem(USER_ID_1, THE_HOBBIT.id(), QTY_2);
        shoppingBasketService.addItem(USER_ID_1, BREAKING_BAD.id(), QTY_5);

        ShoppingBasket shoppingBasket = shoppingBasketService.basketFor(USER_ID_1);

        assertThat(shoppingBasket, is(aShoppingBasket()
                                            .createdOn(CREATION_DATE)
                                            .ownedBy(USER_ID_1)
                                            .withItem(THE_HOBBIT, QTY_2)
                                            .withItem(BREAKING_BAD, QTY_5)
                                            .build()));
        assertThat(shoppingBasket.total(), is(BigDecimal.valueOf(45.0)));
    }

}

```
This ensures that the shoppingBasket methods are correct. Here we do not return a string.

The application is then split between:
- basket
- discount
- product

The tests for Shopping Basket return a Builder:
```
    @Test public void
    return_the_total_cost_for_a_basket_containing_items() {
        ShoppingBasket shoppingBasket = ShoppingBasketBuilder.aShoppingBasket()
                                            .withItem(PRODUCT_1, QTY_2)
                                            .withItem(PRODUCT_2, QTY_5)
                                            .build();

        BigDecimal totalCost = shoppingBasket.total();

        assertThat(totalCost, is(FORTY_FIVE));
    }
```
The Builder is a fixture through which we create different kinds of ShoppingBaskets:
```
package com.codurance.shoppingbasket.basket;

import com.codurance.shoppingbasket.UserID;
import com.codurance.shoppingbasket.product.Product;

import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

public class ShoppingBasketBuilder {

    private LocalDate creationDate = LocalDate.now();
    private UserID userID = new UserID("SOME ID");
    private List<ShoppingBasketItem> items = new ArrayList<>();

    public static ShoppingBasketBuilder aShoppingBasket() {
        return new ShoppingBasketBuilder();
    }

    public ShoppingBasketBuilder createdOn(LocalDate creationDate) {
        this.creationDate = creationDate;
        return this;
    }

    public ShoppingBasketBuilder ownedBy(UserID userID) {
        this.userID = userID;
        return this;
    }

    public ShoppingBasketBuilder withItem(Product product, int quantity) {
        this.items.add(new ShoppingBasketItem(product, quantity));
        return this;
    }

    public ShoppingBasket build() {
        ShoppingBasket shoppingBasket = new ShoppingBasket(userID, creationDate);
        items.forEach(shoppingBasket::add);
        return shoppingBasket;
    }
}

```
The Builder has static methods which return a ShoppingBasket on which we can add various attributes:
```
        ShoppingBasket basket = ShoppingBasketBuilder.aShoppingBasket()
                                    .withItem(aBook().build(), QTY_2)
                                    .withItem(aBook().build(), QTY_5)
                                    .build();
```
The test for the ShoppingBasketShould:
```

	private static final ProductID THE_HOBBIT_ID = new ProductID("10002");
	private static final Product THE_HOBBIT = aBook().withId("10002").costing(TEN).build();
    private static final int QTY_2 = 2;
	private static final Discount NO_DISCOUNT = new NoDiscount();

    private static final UserID USER_ID_1 = new UserID("1234");
	private static final Discount TWENTY_PERCENT_DISCOUNT = new AtLeastOneBookAndOneVideoDiscount(20);

	private ShoppingBasket USER_1_SHOPPING_BASKET;

	@Mock ShoppingBasketRepository shoppingBasketRepository;
    @Mock ProductService productService;
	@Mock DiscountCalculator discountCalculator;

    private ShoppingBasketService shoppingBasketService;

	@Before
    public void initialise() {
        USER_1_SHOPPING_BASKET = aShoppingBasket().ownedBy(USER_ID_1).build();
		given(shoppingBasketRepository.basketFor(USER_ID_1)).willReturn(USER_1_SHOPPING_BASKET);
        given(productService.productFor(THE_HOBBIT_ID)).willReturn(THE_HOBBIT);

        shoppingBasketService =
                new ShoppingBasketService(productService, shoppingBasketRepository, discountCalculator);
    }

    @Test public void
    add_new_item_to_shopping_basket() {
	    ShoppingBasket shoppingBasket = aShoppingBasket().ownedBy(USER_ID_1).withItem(THE_HOBBIT, QTY_2).build();
	    given(discountCalculator.discountFor(shoppingBasket)).willReturn(NO_DISCOUNT);
	    given(productService.hasEnoughItemsInStock(THE_HOBBIT_ID, QTY_2)).willReturn(true);

        shoppingBasketService.addItem(USER_ID_1, THE_HOBBIT_ID, QTY_2);

        verify(shoppingBasketRepository).save(
                aShoppingBasket().ownedBy(USER_ID_1)
		                         .withItem(THE_HOBBIT, QTY_2)
		                         .build());
    }

    @Test public void
    return_a_shopping_basket_for_a_given_user() {
        ShoppingBasket shoppingBasket = shoppingBasketService.basketFor(USER_ID_1);

        assertThat(shoppingBasket, is(USER_1_SHOPPING_BASKET));
    }

    @Test(expected = NotEnoughItemsInStockException.class) public void
    throw_exception_when_there_are_not_enough_items_in_stock() {
        given(productService.hasEnoughItemsInStock(THE_HOBBIT_ID, QTY_2)).willReturn(false);

	    shoppingBasketService.addItem(USER_ID_1, THE_HOBBIT_ID, QTY_2);
    }
```
The ShoppingBasketRepositoryShould test creates and returns Baskets from Repository:
```
@RunWith(MockitoJUnitRunner.class)
public class ShoppingBasketRepositoryShould {

    private static final UserID USER_ID = new UserID("1234");
    private static final LocalDate CURRENT_DATE = LocalDate.now();
    private static final int QTY_2 = 2;
    private static final Product PRODUCT = ProductBuilder.aBook().withId("10001").costing(TEN).build();

    @Mock
    Clock clock;

    private ShoppingBasketRepository shoppingBasketRepository;

    @Before
    public void initialise() {
        given(clock.today()).willReturn(CURRENT_DATE);
        shoppingBasketRepository = new ShoppingBasketRepository(clock);
    }

    @Test public void
    create_and_return_a_new_shopping_basket_for_a_user_when_she_does_not_have_one() {
        ShoppingBasket shoppingBasket = shoppingBasketRepository.basketFor(USER_ID);

        assertThat(shoppingBasket, Is.is(ShoppingBasketBuilder.aShoppingBasket()
                                            .createdOn(CURRENT_DATE)
                                            .ownedBy(USER_ID)
                                            .build()));
    }

    @Test public void
    save_user_basket() {
        ShoppingBasket userBasket = ShoppingBasketBuilder.aShoppingBasket()
                                            .createdOn(CURRENT_DATE)
                                            .ownedBy(USER_ID)
                                            .withItem(PRODUCT, QTY_2)
                                            .build();
        shoppingBasketRepository.save(userBasket);

        ShoppingBasket shoppingBasket = shoppingBasketRepository.basketFor(USER_ID);

        assertThat(shoppingBasket, is(userBasket));
    }

}
```
There is also a ProductServiceShould test which we didn't implement on our kata:
```
package com.codurance.shoppingbasket.product;

import org.junit.Before;
import org.junit.Test;

import static com.codurance.shoppingbasket.product.ProductService.*;
import static org.hamcrest.core.Is.is;
import static org.junit.Assert.assertThat;

public class ProductServiceShould {

	private ProductService productService;

	@Before
	public void initialise() {
		productService = new ProductService();
	}

	@Test public void
	inform_when_there_are_enough_items_in_stock() {
		assertThat(productService.hasEnoughItemsInStock(THE_HOBBIT.id(), 3), is(true));
		assertThat(productService.hasEnoughItemsInStock(THE_HOBBIT.id(), 15), is(false));
	}

	@Test public void
	return_a_product_by_its_ID() {
	    assertThat(productService.productFor(LOTR.id()), is(LOTR));
	    assertThat(productService.productFor(THE_HOBBIT.id()), is(THE_HOBBIT));
	    assertThat(productService.productFor(GAME_OF_THRONES.id()), is(GAME_OF_THRONES));
	    assertThat(productService.productFor(BREAKING_BAD.id()), is(BREAKING_BAD));
	}

}
```
One of the tests for this class ensures that the ProductService class returns a product from by its ID. The ShoppingBasketService has two main methods for our purposes - addItem() and basketFor:
```
package com.codurance.shoppingbasket.basket;

import com.codurance.shoppingbasket.UserID;
import com.codurance.shoppingbasket.discount.Discount;
import com.codurance.shoppingbasket.discount.DiscountCalculator;
import com.codurance.shoppingbasket.product.Product;
import com.codurance.shoppingbasket.product.ProductID;
import com.codurance.shoppingbasket.product.ProductService;

public class ShoppingBasketService {

    private ProductService productService;
    private ShoppingBasketRepository shoppingBasketRepository;
	private DiscountCalculator discountCalculator;

	public ShoppingBasketService(ProductService productService,
	                             ShoppingBasketRepository shoppingBasketRepository,
	                             DiscountCalculator discountCalculator) {
        this.productService = productService;
        this.shoppingBasketRepository = shoppingBasketRepository;
		this.discountCalculator = discountCalculator;
	}

    public void addItem(UserID userID, ProductID productID, int quantity) {
    	abortIfNotEnoughItemsInStock(productID, quantity);
	    addProductToBasket(productID, quantity, basketFor(userID));
    }

	public ShoppingBasket basketFor(UserID userID) {
		return shoppingBasketRepository.basketFor(userID);
	}

	public Discount basketDiscount(UserID userID) {
		ShoppingBasket shoppingBasket = shoppingBasketRepository.basketFor(userID);
		return discountCalculator.discountFor(shoppingBasket);
	}

	private void addProductToBasket(ProductID productID, int quantity, ShoppingBasket basket) {
		Product product = productService.productFor(productID);
		basket.add(new ShoppingBasketItem(product, quantity));
		shoppingBasketRepository.save(basket);
	}

	private void abortIfNotEnoughItemsInStock(ProductID productID, int quantity) {
		if (!productService.hasEnoughItemsInStock(productID, quantity)) {
			throw new NotEnoughItemsInStockException();
		}
	}
}
```
The addItem() looks like this:
```
    public void addItem(UserID userID, ProductID productID, int quantity) {
    	abortIfNotEnoughItemsInStock(productID, quantity);
	    addProductToBasket(productID, quantity, basketFor(userID));
    }
```
This calls addProductToBasket:
```

	private void addProductToBasket(ProductID productID, int quantity, ShoppingBasket basket) {
		Product product = productService.productFor(productID);
		basket.add(new ShoppingBasketItem(product, quantity));
		shoppingBasketRepository.save(basket);
	}
```
Here we use the basket in the parameter to add the ShoppingBasketItem and then save this in the shoppingBasketRepository. The basketFor method on the other hand:
```
	public ShoppingBasket basketFor(UserID userID) {
		return shoppingBasketRepository.basketFor(userID);
	}

```
returns the result of basketFor on the shoppingBasketRepository. The ShoppingBasketRepository has a HashMap which is used to store the UserID and the ShoppingBasket:
```
package com.codurance.shoppingbasket.basket;

import com.codurance.shoppingbasket.UserID;

import java.util.HashMap;
import java.util.Map;

public class ShoppingBasketRepository {

    private Clock clock;
    private Map<UserID, ShoppingBasket> baskets = new HashMap<>();

    public ShoppingBasketRepository(Clock clock) {
        this.clock = clock;
    }

    ShoppingBasket basketFor(UserID userId) {
        return baskets.getOrDefault(userId, createBasket(userId));
    }

    void save(ShoppingBasket basket) {
        baskets.put(basket.userId(), basket);
    }

    private ShoppingBasket createBasket(UserID userID) {
        return new ShoppingBasket(userID, clock.today());
    }
}
```
This uses a clock and then uses the getOrDefault HashMap method to return the correctBasket or create a new one. The ShoppingBasket on the other hand looks like this:
```
public class ShoppingBasket {
    private final UserID userID;
    private final LocalDate creationDate;
    private List<ShoppingBasketItem> items = new ArrayList<>();

	ShoppingBasket(UserID userID, LocalDate creationDate) {
        this.userID = userID;
        this.creationDate = creationDate;
	}

    void add(ShoppingBasketItem item) {
        items.add(item);
    }

    public BigDecimal total() {
        return valueOf(items.stream()
                            .map(item -> item.totalPrice().doubleValue())
                            .reduce((t, acc) -> acc + t).orElse(0.0));
    }

    UserID userId() {
        return userID;
    }

	public boolean contains(ProductType productType) {
		return items.stream()
					.anyMatch(i -> i.productType() == productType);
	}

	public int numberOf(ProductType productType) {
		return items.stream()
					.filter(i -> i.productType() == productType)
					.map(ShoppingBasketItem::quantity)
					.reduce((quantity, acc) -> acc + quantity)
					.orElse(0);
	}

    @Override
    public boolean equals(Object o) {
        return reflectionEquals(this, o);
    }

    @Override
    public int hashCode() {
        return reflectionHashCode(this);
    }

	@Override
	public String toString() {
		return "ShoppingBasket{" +
				"userID=" + userID +
				", creationDate=" + creationDate +
				", items=" + items +
				'}';
	}
}
```
The toString method is overridden to return information from the ShoppingBasket. This ShoppingBasket class includes a total method which returns an int from a stream of the items:
```
public BigDecimal total() {
        return valueOf(items.stream()
                            .map(item -> item.totalPrice().doubleValue())
                            .reduce((t, acc) -> acc + t).orElse(0.0));
    }
s
```
This is quite interesting because it maps over the items in a stream and performs reduce on the items' totalPrice and returns this number as a BigDecimal or returns 0.0. 

##### This is an example of getOrDefault() which returns the hash value or a default a bit like .fetch in Ruby:
https://www.geeksforgeeks.org/hashmap-getordefaultkey-defaultvalue-method-in-java-with-examples/
```
// Java program to demonstrate 
// getOrDefault(Object key, V defaultValue) method 
  
import java.util.*; 
  
public class GFG { 
  
    // Main method 
    public static void main(String[] args) 
    { 
  
        // Create a HashMap and add some values 
        HashMap<String, Integer> map 
            = new HashMap<>(); 
        map.put("a", 100); 
        map.put("b", 200); 
        map.put("c", 300); 
        map.put("d", 400); 
  
        // print map details 
        System.out.println("HashMap: "
                           + map.toString()); 
  
        // provide key whose value has to be obtained 
        // and default value for the key. Store the 
        // return value in k 
        int k = map.getOrDefault("b", 500); 
  
        // print the value of k returned by 
        // getOrDefault(Object key, V defaultValue) method 
        System.out.println("Returned Value: " + k); 
    } 
} 
Output:
```
##### Builder Model:
This is interesting for the Builder pattern:
https://howtodoinjava.com/design-patterns/creational/builder-pattern-in-java/
```
    public static class UserBuilder 
    {
        private final String firstName;
        private final String lastName;
        private int age;
        private String phone;
        private String address;
 
        public UserBuilder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
        public UserBuilder age(int age) {
            this.age = age;
            return this;
        }
        public UserBuilder phone(String phone) {
            this.phone = phone;
            return this;
        }
        public UserBuilder address(String address) {
            this.address = address;
            return this;
        }
        //Return the finally consrcuted User object
        public User build() {
            User user =  new User(this);
            validateUserObject(user);
            return user;
        }
        private void validateUserObject(User user) {
            //Do some basic validations to check 
            //if user object does not break any assumption of system
        }
    }
}
```
We can then use the Builder in the following manner:
```
public static void main(String[] args) {
    User user1 = new User.UserBuilder("Lokesh", "Gupta")
    .age(30)
    .phone("1234567")
    .address("Fake address 1234")
    .build();
 
    System.out.println(user1);
 
    User user2 = new User.UserBuilder("Jack", "Reacher")
    .age(40)
    .phone("5655")
    //no address
    .build();
 
    System.out.println(user2);
 
    User user3 = new User.UserBuilder("Super", "Man")
    //No age
    //No phone
    //no address
    .build();
 
    System.out.println(user3);
}
```
This way we can set the attributes we want on the User class:
```
    User user1 = new User.UserBuilder("Lokesh", "Gupta")
    .age(30)
    .phone("1234567")
    .address("Fake address 1234")
    .build();
```

##### This is Sandro Mancuso's version of the ShoppingBasketRepository for the ShoppingBasket kata:
```
package com.codurance.shoppingbasket.basket;

import com.codurance.shoppingbasket.UserID;
import com.codurance.shoppingbasket.discount.Discount;
import com.codurance.shoppingbasket.discount.DiscountCalculator;
import com.codurance.shoppingbasket.product.Product;
import com.codurance.shoppingbasket.product.ProductID;
import com.codurance.shoppingbasket.product.ProductService;

public class ShoppingBasketService {

    private ProductService productService;
    private ShoppingBasketRepository shoppingBasketRepository;
	private DiscountCalculator discountCalculator;

	public ShoppingBasketService(ProductService productService,
	                             ShoppingBasketRepository shoppingBasketRepository,
	                             DiscountCalculator discountCalculator) {
        this.productService = productService;
        this.shoppingBasketRepository = shoppingBasketRepository;
		this.discountCalculator = discountCalculator;
	}

    public void addItem(UserID userID, ProductID productID, int quantity) {
    	abortIfNotEnoughItemsInStock(productID, quantity);
	    addProductToBasket(productID, quantity, basketFor(userID));
    }

	public ShoppingBasket basketFor(UserID userID) {
		return shoppingBasketRepository.basketFor(userID);
	}

	public Discount basketDiscount(UserID userID) {
		ShoppingBasket shoppingBasket = shoppingBasketRepository.basketFor(userID);
		return discountCalculator.discountFor(shoppingBasket);
	}

	private void addProductToBasket(ProductID productID, int quantity, ShoppingBasket basket) {
		Product product = productService.productFor(productID);
		basket.add(new ShoppingBasketItem(product, quantity));
		shoppingBasketRepository.save(basket);
	}

	private void abortIfNotEnoughItemsInStock(ProductID productID, int quantity) {
		if (!productService.hasEnoughItemsInStock(productID, quantity)) {
			throw new NotEnoughItemsInStockException();
		}
	}
}
```
This includes a ProductFor method. This is called on the ProductService class:
```
package com.codurance.shoppingbasket.product;

import java.math.BigDecimal;
import java.util.HashMap;
import java.util.Map;

public class ProductService {

    private static final BigDecimal TEN_POUNDS = BigDecimal.valueOf(10.0);
    private static final BigDecimal FIVE_POUNDS = BigDecimal.valueOf(5.0);
    private static final BigDecimal NINE_POUNDS = BigDecimal.valueOf(9.0);
    private static final BigDecimal SEVEN_POUNDS = BigDecimal.valueOf(7.0);

	private static final int QTY_10 = 10;

	public static final Product LOTR = ProductBuilder.aBook().withId("10001").costing(TEN_POUNDS).withQuantity(QTY_10).build();
	public static final Product THE_HOBBIT = ProductBuilder.aBook().withId("10002").costing(FIVE_POUNDS).withQuantity(QTY_10).build();
	public static final Product GAME_OF_THRONES = ProductBuilder.aVideo().withId("20001").costing(NINE_POUNDS).withQuantity(QTY_10).build();
	public static final Product BREAKING_BAD = ProductBuilder.aVideo().withId("20110").costing(SEVEN_POUNDS).withQuantity(QTY_10).build();


	private static Map<ProductID, Product> products = new HashMap<ProductID, Product>() {
        {
            put(LOTR.id(), LOTR);
            put(THE_HOBBIT.id(), THE_HOBBIT);
            put(GAME_OF_THRONES.id(), GAME_OF_THRONES);
            put(BREAKING_BAD.id(), BREAKING_BAD);
        }
    };

	public Product productFor(ProductID productID) {
		return products.get(productID);
	}

    public boolean hasEnoughItemsInStock(ProductID productID, int quantity) {
	    return products.get(productID).quantity() >= quantity;
    }
}
```
The ProductService class has a HashSet (HashMap) of ProductID and Product. This then returns the product according to the ProductID. the Builder class is particularly interesting here:
```
package com.codurance.shoppingbasket.product;

import java.math.BigDecimal;

public class ProductBuilder {

	private ProductType productType = ProductType.BOOK;
	private ProductID productID = new ProductID("1234");
	private BigDecimal price = BigDecimal.TEN;
	private int quantity = 0;

	private ProductBuilder(ProductType productType) {
		this.productType = productType;
	}

	public static ProductBuilder aBook() {
        return new ProductBuilder(ProductType.BOOK);
    }

	public static ProductBuilder aVideo() {
		return new ProductBuilder(ProductType.VIDEO);
	}

    public ProductBuilder withId(String value) {
        this.productID = new ProductID(value);
        return this;
    }

    public ProductBuilder costing(BigDecimal price) {
        this.price = price;
	    return this;
    }

	public ProductBuilder withQuantity(int quantity) {
		this.quantity = quantity;
		return this;
	}

	public Product build() {
		return new Product(productID, productType, price, quantity);
	}
}
```
This uses theenum ProductType to select BOOK or VIDEO:
```
  
package com.codurance.shoppingbasket.product;

public enum ProductType {

	BOOK, VIDEO;

}
```
The builder is then called in the following manner:
```
	public static final Product LOTR = ProductBuilder.aBook().withId("10001").costing(TEN_POUNDS).withQuantity(QTY_10).build();
	public static final Product THE_HOBBIT = ProductBuilder.aBook().withId("10002").costing(FIVE_POUNDS).withQuantity(QTY_10).build();
	public static final Product GAME_OF_THRONES = ProductBuilder.aVideo().withId("20001").costing(NINE_POUNDS).withQuantity(QTY_10).build();
	public static final Product BREAKING_BAD = ProductBuilder.aVideo().withId("20110").costing(SEVEN_POUNDS).withQuantity(QTY_10).build();
```

I do particularly like the getOrDefault from the ShoppingBasketRepository:
```
public class ShoppingBasketRepository {

    private Clock clock;
    private Map<UserID, ShoppingBasket> baskets = new HashMap<>();

    public ShoppingBasketRepository(Clock clock) {
        this.clock = clock;
    }

    ShoppingBasket basketFor(UserID userId) {
        return baskets.getOrDefault(userId, createBasket(userId));
    }

    void save(ShoppingBasket basket) {
        baskets.put(basket.userId(), basket);
    }

    private ShoppingBasket createBasket(UserID userID) {
        return new ShoppingBasket(userID, clock.today());
    }
}
```

##### HashMap getOrDefault:
https://javatutorialhq.com/java/util/hashmap-class/getordefault-method-example/

Nice example with getOrDefault called on the HashMap:
```
package com.javatutorialhq.java.examples;

import java.util.HashMap;

/*
 * This example source code demonstrates the use of  
 * getOrDefault() method of HashMap class
 */

public class HashMapGetOrDefaultExample {

	public static void main(String[] args) throws InterruptedException {

		int idNum = 9756;
		HashMap<Integer, String> map = init();
		System.out.println("Student with id number " + idNum + " is "
				+ map.getOrDefault(idNum, "John Doe"));

	}

	private static HashMap<Integer, String> init() {
		// declare the hashmap
		HashMap<Integer, String> mapStudent = new HashMap<>();
		// put contents to our HashMap
		mapStudent.put(73654, "Shyra Travis");
		mapStudent.put(98712, "Sharon Wallace");
		return mapStudent;
	}

}
```


###### Alexis with Arnaud:
This is Alexis' work on the ShoppingBasket with Arnaud:
https://github.com/apavlidi/ShoppingBasket/blob/master/src/main/java/domain/Basket.java

```
package domain;

import java.util.HashMap;
import java.util.Map;

public class Basket {

    Map<String, Integer> productsQuantity;
    Map<String, Product> products;

    public Basket() {
        productsQuantity = new HashMap<>();
        products = new HashMap<>();
    }

    public Map<Product, Integer> getProducts() {
        throw new UnsupportedOperationException();
    }

    public int getTotal() {
        throw new UnsupportedOperationException();
    }

    public int getProductQuantity(String productId) {
        return productsQuantity.get(productId);
    }

    public int size() {
        throw new UnsupportedOperationException();
    }

    public Product getProductBy(String productId) {
        return products.get(productId);
    }

    public void addProduct(Product product, int quantity) {
        products.put(product.getId(), product);
        productsQuantity.put(product.getId(), quantity);
    }
}
```

This is the BasketRepository:
```

package repos;

import domain.Basket;
import java.util.Optional;
import java.util.UUID;

public class BasketRepositoryInMemory implements BasketRepository {

    @Override
    public Optional<Basket> get(UUID userId) {
        return null;
    }

    @Override
    public void save(Basket basket) {
        throw new UnsupportedOperationException();
    }
}
```
We can also see the UUID on the BasketRepositoryInMemory. This is the ProductService:
```
package service;

import domain.Product;

public class ProductService {

    public Product getBy(String productID) {
        throw new UnsupportedOperationException();
    }
}
```

This is the ShoppingBasketService:
```
package service;

import domain.Basket;
import java.util.UUID;
import java.util.function.Consumer;
import repos.BasketRepository;

public class ShoppingBasketService {

    private final BasketRepository basketRepo;
    private final ProductService productService;

    public ShoppingBasketService(BasketRepository basketRepo, ProductService productService) {
        this.basketRepo = basketRepo;
        this.productService = productService;
    }

    public void addItem(UUID userId, String productId, int quantity) {
        basketRepo.get(userId)
            .ifPresentOrElse(
                addProductAndSave(productId, quantity),
                createBasketAndAddProductAndSave(productId, quantity));
    }

    private Runnable createBasketAndAddProductAndSave(String productId, int quantity) {
        return () -> {
            Basket basket = new Basket();
            //        baskset = createBasket();
            basket.addProduct(productService.getBy(productId), quantity);
            basketRepo.save(basket);
        };
    }


    private Consumer<Basket> addProductAndSave(String productId, int quantity) {
        return (basket) -> {
            basket.addProduct(productService.getBy(productId), quantity);
            basketRepo.save(basket);
        };
    }

    public Basket basketFor(UUID randomUUID) {
        throw new UnsupportedOperationException();
    }
}
```
In the class we have some private methods which return Runnable and Consumer<Basket>.


This is the description of the Golden Master:

https://github.com/codurance/apprenticeship/wiki/Golden-master

```
Golden master
Pedro Santos edited this page on 20 Nov 2017 · 3 revisions
The Golden Master technique is very useful when a clear input and output is easy to obtain on the system level. There are some cases where the Golden Master technique can be applied with difficulty or where it cannot be applied at all. We will discuss these situations further as well.

For all the given situations we need to think if the system tests generated with the Golden Master are enough, or we need to start adding other types of tests like unit tests, component tests, integration tests, security tests, etc. The Golden Master technique is based on the following steps:

Find the way the system delivers its outputs

Check for clear outputs of the system: console, data layer, logger, file system, etc.
Find a way to capture the output of the system without changing the production code

Think if that output can be “stolen” without changing the production code. For example you could “steal” the console output by redirecting the stream of the console to an in-memory stream. Another example would be injecting an implementation of a data layer interface that would write to another data source than the production code would.
Find a pattern of the outputs

The output of the system could be text or a data structures tree or another type of stream. Starting from this output type you can decide if you could go on to the next step.
Generate enough random inputs and persist the tuple input/output

In the case you can persist the outputs to a simple data repository, you can think about what are the inputs for the corresponding outputs. Here the magic consists in finding a good balance of random or pseudo-random inputs. We need inputs that would cover most of the system with tests, but in the same time the test would run in seconds. For example in the case of a text generating algorithm we need to decide if we want to feed the system with 1000 or one million entry data. Maybe 10 000 input data are enough. We will anyway check the tests coverage during the last stage.
We need to persist the pair input-output. The output would be the Golden Master, the reference data we will always check our SUT against.
Write a system test to check the SUT against the previously persisted data

Now that we have a way to “steal” the outputs and we have a guess about how to generate enough input-output pairs, but not too many, we can call the system and check it against the outputs for a given input. We need to check if the test touches the SUT and if it passes. We also need to check that the test runs fast enough, in seconds.
Commit the test

Always when a green test passes we need to commit the code to a local source control system. Why? So that we can easily revert to a stable testable system.
Important: Do not forget to commit also the golden masters (files, database, etc)!
Check test behaviour and coverage

In this stage I tend to do two things:
Use a test coverage tool to see where the system tests touch the SUT
Start changing the SUT in order to see the golden master test go red.
If the test does not go red, I can understand that the code base is not covered by tests in that area and I should not touch it during the next stages, until I have a basic safety net. Always after this step I will revert to the previous commit, not matter how small the change to the SUT was.
If not enough behaviours are covered, go to 3

In the case we found during the last stage some behaviours that were not covered by the golden master test, we need to write some more tests with other inputs and outputs. We go on until all the visible behaviours needing to be covered by tests are covered.
```

This is my solution for the Gilded Rose kata:
https://github.com/TomSpencerLondon/Gilded-Rose/tree/master/lib

This is Emily Bache's answer:
https://github.com/emilybache/Tennis-Refactoring-Kata

This was an interesting part of Alexis' answer with Arnaud to Tennis kata in ShoppingBasketService:
```
    public void addItem(UUID userId, String productId, int quantity) {
        basketRepo.get(userId)
            .ifPresentOrElse(
                addProductAndSave(productId, quantity),
                createBasketAndAddProductAndSave(productId, quantity));
    }

    private Runnable createBasketAndAddProductAndSave(String productId, int quantity) {
        return () -> {
            Basket basket = new Basket();
            //        baskset = createBasket();
            basket.addProduct(productService.getBy(productId), quantity);
            basketRepo.save(basket);
        };
    }
```
This is a Runnable method. 
https://stackoverflow.com/questions/13327571/in-a-simple-to-understand-explanation-what-is-runnable-in-java

Runnable is an interface defined as so:

interface Runnable {
    public void run();
}
To make a class which uses it, just define the class as (public) class MyRunnable implements Runnable {

It can be used without even making a new Thread. It's basically your basic interface with a single method, run, that can be called.

If you make a new Thread with runnable as it's parameter, it will call the run method in a new Thread.

It should also be noted that Threads implement Runnable, and that is called when the new Thread is made (in the new thread). The default implementation just calls whatever Runnable you handed in the constructor, which is why you can just do new Thread(someRunnable) without overriding Thread's run method.


Alexis wanted to use the HotelService kata by using ArgumentCaptor forClass:
```

    HotelService service = new HotelService(repo);
    String hotelId = UUID.randomUUID().toString();

    ArgumentCaptor<Hotel> hotelArg = ArgumentCaptor.forClass(Hotel.class);
    service.addHotel(hotelId, "Tom's hotel");

    verify(repo).save(hotelArg.capture());

    assertEquals("Tom's hotel", hotelArg.getValue().name);
    assertEquals(hotelId, hotelArg.getValue().id);
```