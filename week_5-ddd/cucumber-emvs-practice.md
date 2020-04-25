###### This is the start of our cucumber solution:
https://github.com/TomSpencerLondon/Cucumber-EMVS

https://github.com/TomSpencerLondon/Cucumber-EMVS/tree/master/src/main/java/entities/product

https://cucumber.io/docs/installation/


#### I did try to refer to this EmployeeBuilder:
```
package com.codurance;

import java.math.BigDecimal;

public class EmployeeBuilder {

  private int id = 12345;
  private String name = "John J Doe";
  private BigDecimal annualGrossSalary = BigDecimal.valueOf(5000);

  public static EmployeeBuilder anEmployee() {
    return new EmployeeBuilder();
  }


  public EmployeeBuilder withID(int id) {
    this.id = id;
    return this;
  }

  public EmployeeBuilder withName(String name) {
    this.name = name;
    return this;
  }

  public EmployeeBuilder withAnnualGrossSalary(BigDecimal annualGrossSalary) {
    this.annualGrossSalary = annualGrossSalary;
    return this;
  }

  public EmployeeBuilder withAnnualGrossSalary(double annualGrossSalary) {
    this.annualGrossSalary = BigDecimal.valueOf(annualGrossSalary);
    return this;
  }

  public Employee build() {
    return new Employee(id, name, annualGrossSalary);
  }
}
```
##### We used the cucumber tutorial here:
https://cucumber.io/docs/installation/

and this one here:
https://cucumber.io/docs/guides/10-minute-tutorial/

This is a handout on context in Java:
https://www.toolsqa.com/selenium-cucumber-framework/share-data-between-steps-in-cucumber-using-scenario-context/
```
Share data between steps in Cucumber using Scenario Context
Let’s get back to our Test Scenario and put a validation. So far we have just written an End 2 End test Case but we have not validated anything in the test. Let’s say we want to validate the Name of the Product at the end of the test. In that case, we need to store the name of the product in Scenario Context object while adding product in the bag. And retrieve the product from the ScenarioContext and validate the name on the final confirmation page, after the order placement is done.
```

This is the EMVS board from our work on Miro:
https://miro.com/app/board/o9J_kuAzdTo=/

![Screenshot 2020-04-01 at 13 39 55](https://user-images.githubusercontent.com/27693622/78167287-24135180-7446-11ea-80a0-7d36bb1585cf.png)

![Screenshot 2020-04-01 at 09 50 37](https://user-images.githubusercontent.com/27693622/78167296-2a093280-7446-11ea-9a39-9f39b1516661.png)


###### This is a fully summary of the work we did on the Cucumber prototype for EMVS:
```
Feature: verify Pack exists
  verifies that Pack exists

  Background:
    Given "active" Product exists "GTIN" "12345678901231" "Ibuprofen"
    And "active" Batch exists "123" for product "GTIN" "12345678901231" with "active" Pack "456"

  Scenario: If Pack exists when Dispenser verify it returns Pack state
    When Dispenser asks for verification
    Then NBS responds with Pack state "active"
```
Here we wrote our first Cucumber prototype based on the designs we made in our event storming on Miro:
https://miro.com/app/board/o9J_kuAzdTo=/

In this Feature we are focused on verfiying if the Pack exists. For our background we write:
##### Given
An active Products exists with GTIN productScheme, UUID batchID with pack with ID 456 and status active we have a Scenario:
If the Pack exists when the dispenser calls verify we return the Pack state. When the Dispenser asks for verification then the National Blueprint responds with the Pack state active.

This was how I worked out how to use breakpoints:
![Screenshot 2020-04-02 at 21 36 13](https://user-images.githubusercontent.com/27693622/78298506-57c7a780-752a-11ea-8249-26368b05f2a3.png)

This was the verificationAPI test:
```
  
package actions;


import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.BDDMockito.given;

import domain.VerificationService;
import entities.pack.PackState;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class VerificationAPITest {

  @Mock
  private VerificationService service;

  @Mock
  private VerificationRequestObject requestObject;

  @Test
  public void returns_pack_state() {
    PackState actualState = PackState.ACTIVE;

    given(service.verify(requestObject)).willReturn(actualState);

    VerificationAPI verificationAPI = new VerificationAPI(service);
    VerificationResponseObject response = verificationAPI.verify(
        requestObject);

    assertEquals(actualState.value, response.state);
  }


}
```
Here we mocked the VerificationService and the VerificationRequestObject and tested that the VerficationAPI returned the correct state in its response.

We had this test for the packService:
```
package domain;

import entities.batch.Batch;
import entities.batch.BatchPackRepository;
import entities.product.ProductCode;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.BDDMockito.given;

@ExtendWith(MockitoExtension.class)
class PackServiceTest {
    @Mock private BatchPackRepository batchPackRepository;
    @Mock private Batch batch;

    @Test
    void should_retrieve_batch_by_batch_id_and_product_code() {
        PackService packService = new PackService(batchPackRepository);
        ProductCode productCode = new ProductCode("12345678901231", "GTIN");
        String batchId = "123";
        given(batchPackRepository.findByBatchIdAndProductCode(batchId, productCode)).willReturn(batch);

        Batch actualBatch = packService.retrieve(batchId, productCode);
        assertEquals(batch, actualBatch);
    }
}
```
This test created a BatchPackRepository and a mock Batch. Next we created a test with @Mock private BatchRepository and @Mock private. We then initialize the PackService with the batchPacRepository and create a productCode object to mock the response of the BatchAndPackRepository to findByBatch to return batch. Next we create a variable (actualBatch) against which to check the batch (ie expected batch) through packService.retrieve(batchId, productCode).

We also have a test for the ProductService:
```
package domain;

import entities.product.Product;
import entities.product.ProductCode;
import entities.product.ProductRepository;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.BDDMockito.given;

@ExtendWith(MockitoExtension.class)
class ProductServiceTest {
    @Mock private ProductRepository productRepository;
    @Mock private Product product;

    @Test
    void retrieve_product_by_product_code() {
        ProductService productService = new ProductService(productRepository);
        ProductCode productCode = new ProductCode("12345678901231", "GTIN");
        given(productRepository.findByProductCode(productCode)).willReturn(product);

        Product actualProduct = productService.retrieve(productCode);
        assertEquals(product, actualProduct);
    }
}
```
Here we create a productService with a productRepository and create a productCode which is an actual object. We retrieve a product through productService.retrieve(productCode). This retrieves a product through mocking the productRepository.

Next we have a test for the VerificationServiceTest:
```
package domain;

import actions.VerificationRequestObject;
import entities.batch.Batch;
import entities.pack.Pack;
import entities.pack.PackState;
import entities.product.Product;
import entities.product.ProductCode;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.time.LocalDate;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.BDDMockito.given;

@ExtendWith(MockitoExtension.class)
class VerificationServiceTest {
    @Mock private ProductService productService;
    @Mock private PackService packService;
    @Mock private Product product;
    @Mock private Batch batch;
    @Mock private Pack pack;

    @Test
    void should_retrieve_product_and_pack_and_return_pack_state() {
        ProductCode productCode = new ProductCode("12345678901231", "GTIN");
        String batchId = "123";
        String packSerial = "456";
        VerificationRequestObject requestObject = new VerificationRequestObject(productCode, batchId,
                LocalDate.of(2020, 1, 1), packSerial, "Ibuprofen");

        given(productService.retrieve(productCode)).willReturn(product);
        given(product.getProductCode()).willReturn(productCode);
        given(packService.retrieve(batchId, productCode)).willReturn(batch);
        given(batch.getPackBySerial(packSerial)).willReturn(pack);
        given(pack.getState()).willReturn(PackState.ACTIVE);

        VerificationService verificationService = new VerificationService(productService, packService);
        PackState state = verificationService.verify(requestObject);

        assertEquals(PackState.ACTIVE, state);
    }
}
```
Here we create a productCode. We also have several mocks including the ProductService, PackService, Product, Batch and Pack. We assign the batchId, packSerial and assign the VerificationRequestObject(productCode, batchId, LocalDate.of(2020, 1, 1), packSerial, "Ibuprofen"). Here we create the VerificationRequestObject with the following information: productCode, batchId, date, packSerial and name.

We use a series of givens in order to mock aspects of the system. These mock the following operations:
```
productService to retrieve product
product getProductCode()
packService.retrieve(batchId, productCode) to return batch
batch.getPackBySerial(packSerial) to return pack
pack.getState() to return PackState.ACTIVE
```
We then instantiate the VerificationService(productService, packService) and return a PackState by calling verificationService.verify(requestObject). We then check the state against the PackState.ACTIVE expectation.

The production code splits the EMVS into 3 folders:
```
actions
domain
entities
```
The actions folder includes:
```
VerificationAPI.java
VerificationRequestObject.java
VerificationResponseObject.java
```
The domain folder includes:
```
PackService.java
ProductService.java
VerificationService.java
```
The entities folder includes:
```
batch
pack
product
```
The batch folder is the following:
```
Batch.java
BatchPackRepository.java
BatchState.java
InMemoryBatchPackRepository.java
```
The Pack folder is the following:
```
Pack.java
PackState.java
```
The Product folder is the following:
```
InMemoryProductRepository.java
Product.java
ProductCode.java
ProductRepository.java
ProductState.java
```

I referred to this github account from Sandro Mancuso for cucumber:
https://github.com/sandromancuso/salaryslipkata



#### FINAL VERSION FOR EMVS:
These are the test files:
Test folders include:
```
acceptance
actions
domain
entities/batch
```
Acceptance is interesting since it involves Cucumber tests:
There are lots of set up files for the acceptance test:
We start with the following feature test file:
https://github.com/TomSpencerLondon/Cucumber-EMVS/blob/pack-verification-exceptions/src/test/java/acceptance/verifyPack.feature
```
Feature: verify Pack exists
  verifies that Pack exists

  Background:
    Given Product exists in system
      | productCode    | productSchema | productName | productState |
      | 12345678901231 | GTIN          | Ibuprofen   | active       |
      | 12345678901232 | GTIN          | Ibuprofen   | withdrawn    |
    And Packs exists in system
      | productCode    | productSchema | batchID | serialNumber | state |
      | 12345678901231 | GTIN          | 123     | 456          | active    |
      | 12345678901231 | GTIN          | 123     | 457          | supplied  |
    And Batch exists with pack in system
      | productCode    | productSchema | batchState | batchID | expiryDate |
      | 12345678901231 | GTIN          | active     | 123     | 02/02/2020 |

  Scenario: If Pack active when Dispenser verify it returns Pack state
    When Dispenser asks for verification
      | productCode    | productSchema | batchID | expiryDate | packSerialNumber |
      | 12345678901231 | GTIN          | 123     | 02/02/2020 | 456              |
    Then NBS responds with Pack state "active"

  Scenario: If Pack supplied when Dispenser verify it returns Pack state
    When Dispenser asks for verification
      | productCode    | productSchema | batchID | expiryDate | packSerialNumber |
      | 12345678901231 | GTIN          | 123     | 02/02/2020 | 457              |
    Then NBS responds with Pack state "supplied"
```
The first Given includes tests for the Products existing on the system. The second given includes information for the Packs on the system and the third involves batches in the system with BatchID, batchState and expiryDate. The state appears to be used on the Pack.

We then have a transformer registry which is used to transfer the tables to the Transformers:
```
package acceptance;

import acceptance.updatePackState.UpdateStatePackRequestObjectTransformer;
import actions.update_pack_state.UpdateStatePackRequestObject;
import actions.VerificationRequestObject;
import entities.batch.Batch;
import entities.pack.Pack;
import entities.product.Product;
import io.cucumber.core.api.TypeRegistry;
import io.cucumber.core.api.TypeRegistryConfigurer;
import io.cucumber.datatable.DataTableType;

import java.util.Locale;

public class TransformerRegistry implements TypeRegistryConfigurer {
    @Override
    public Locale locale() {
        return Locale.ENGLISH;
    }

    @Override
    public void configureTypeRegistry(TypeRegistry typeRegistry) {
        typeRegistry.defineDataTableType(
                new DataTableType(Product.class, new ProductTransformer())
        );
        typeRegistry.defineDataTableType(
                new DataTableType(Batch.class, new BatchTransformer())
        );
        typeRegistry.defineDataTableType(
                new DataTableType(VerificationRequestObject.class, new VerificationRequestObjectTransformer())
        );
        typeRegistry.defineDataTableType(
                new DataTableType(UpdateStatePackRequestObject.class, new UpdateStatePackRequestObjectTransformer())
        );
    }
}
```
Here we instantiate a new DataTableType(Product.class, new ProductTransformer()). We can now look in more detail at each of the transformers including ProductTransformer(), BatchTransformer(), VerificationRequestObjectTransformer(), updateStatePackRequestObjectTransformer():
```
package acceptance;

import entities.product.Product;
import entities.product.ProductCode;
import entities.product.ProductState;
import io.cucumber.datatable.DataTable;
import io.cucumber.datatable.TableEntryTransformer;
import io.cucumber.datatable.TableTransformer;

import java.util.Map;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class ProductTransformer implements TableEntryTransformer<Product> {
    @Override
    public Product transform(Map<String, String> map) throws Throwable {
        ProductCode productCode = new ProductCode(map.get("productCode"), map.get("productSchema"));
        String productName = map.get("productName");
        ProductState state = ProductState.fromString(map.get("productState"));
        return new Product(state, productCode, productName);
    }
}
```
Here we override the transform method from the TableEntryTransformer interface to return a Product using Map<String, String> map. We then create a productCode with map.get("productCode") and map.get("productSchema") and then assign productName to map.get("productName"). We then add productState with ProductState.fromString(map.get("productState")). Finally, we use new Product(state, productCode, productName) to instantiate our product. In the correct section of the Steps file we then implicitly feed through the parameter of the @Given test set up:
```
    @Given("Product exists in system")
    public void productExistsInSystem(List<Product> products) {
        products.forEach(product -> ScenarioContext.productRepository.save(product));
    }
```
This takes a list of products and then iterates over each product with forEach and calls ScenarioContext.productRepository.save(product);

In the production code we have the following folders:
```
actions
domain
entities
```
This is the VerficationAPI:
```
package actions;

import domain.VerificationService;
import entities.pack.PackState;

public class VerificationAPI {

    private VerificationService service;

    public VerificationAPI(VerificationService service) {
        this.service = service;
    }

    public VerificationResponseObject verify(VerificationRequestObject verificationRequestObject) {
        PackState state = service.verify(verificationRequestObject);
        return new VerificationResponseObject(state.value);
    }

}
```
This is the RequestObject:
```
package actions;

import entities.product.Product;
import entities.product.ProductCode;

import java.time.LocalDate;

public class VerificationRequestObject {

  public final ProductCode productCode;
  public final String batchId;
  public final LocalDate expiryDate;
  public final String packSerialNumber;
  public final String productName;

  public VerificationRequestObject(ProductCode productCode, String batchId, LocalDate expiryDate,
                                   String packSerialNumber, String productName) {
    this.productCode = productCode;
    this.batchId = batchId;
    this.expiryDate = expiryDate;
    this.packSerialNumber = packSerialNumber;
    this.productName = productName;
  }

}
```
This is the ResponseObject:
```
package actions;

public class VerificationResponseObject {

  public final String state;

  public VerificationResponseObject(String state) {
    this.state = state;
  }
}
```

This is the updatePackStateAPI:
```
  
package actions.update_pack_state;

public class UpdatePackStateAPI {
    public UpdateStatePackResponseObject update(UpdateStatePackRequestObject requestObject) {
        throw new UnsupportedOperationException("implement me!");
    }
}
```
This is the RequestObject:
```
package actions.update_pack_state;

import entities.pack.PackState;
import entities.product.ProductCode;

import java.time.LocalDate;

public class UpdateStatePackRequestObject {

    public final ProductCode productCode;
    public final String batchID;
    public final LocalDate expiryDate;
    public final String packSerialNumber;
    public final PackState desiredState;

    public UpdateStatePackRequestObject(ProductCode productCode, String batchID, LocalDate expiryDate, String packSerialNumber, PackState desiredState) {
        this.productCode = productCode;
        this.batchID = batchID;
        this.expiryDate = expiryDate;
        this.packSerialNumber = packSerialNumber;
        this.desiredState = desiredState;
    }
}
```
This is the ResponseObject:
```
package actions.update_pack_state;

import entities.pack.PackState;

public class UpdateStatePackResponseObject {
    public PackState state;
}
```
This is the PackService:
```
package domain;

import entities.batch.Batch;
import entities.batch.BatchPackRepository;
import entities.product.ProductCode;

public class PackService {
    private BatchPackRepository batchPackRepository;

    public PackService(BatchPackRepository batchPackRepository) {
        this.batchPackRepository = batchPackRepository;
    }

    public Batch retrieve(String batchId, ProductCode productCode) {
        return batchPackRepository.findByBatchIdAndProductCode(batchId, productCode);
    }
}
```
and the ProductService:
```
package domain;

import entities.product.Product;
import entities.product.ProductCode;
import entities.product.ProductRepository;

public class ProductService {
    private ProductRepository productRepository;

    public ProductService(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }

    public Product retrieve(ProductCode productCode) {
        return productRepository.findByProductCode(productCode);
    }
}
```
This is the VerificationRequestObject:
```
package domain;

import actions.VerificationRequestObject;
import entities.batch.Batch;
import entities.pack.Pack;
import entities.pack.PackState;
import entities.product.Product;

public class VerificationService {

  private final ProductService productService;
  private final PackService packService;

  public VerificationService(ProductService productService, PackService packService) {
    this.productService = productService;
    this.packService = packService;
  }

  public PackState verify(VerificationRequestObject requestObject) {
    Product product = productService.retrieve(requestObject.productCode);
    Batch batch = packService.retrieve(requestObject.batchId, product.getProductCode());
    Pack pack = batch.getPackBySerial(requestObject.packSerialNumber);
    return pack.getState();
  }
}
```
This is the Batch:
```
  
package entities.batch;

import entities.pack.Pack;
import entities.product.ProductCode;

import java.time.LocalDate;
import java.util.List;
import java.util.Optional;

public class Batch {

    public final String id;
    private final LocalDate expiryDate;
    private BatchState state;
    public final ProductCode productCode;
    private List<Pack> packs;

    public Batch(BatchState state, String id, ProductCode productCode, List<Pack> packs, LocalDate expiryDate) {
        this.state = state;
        this.productCode = productCode;
        this.packs = packs;
        this.id = id;
        this.expiryDate = expiryDate;
    }

    public BatchState getState() {
        return state;
    }

    public Pack getPackBySerial(String serial) {
        Optional<Pack> pack = packs.stream().filter(p ->
                p.serialNumber.equals(serial)
        ).findFirst();

        if (pack.isPresent()) {
            return pack.get();
        }
        throw new UnsupportedOperationException();
    }

}
```
BatchPackRepository:
```
  
package entities.batch;

import entities.product.ProductCode;

public interface BatchPackRepository {
    Batch findByBatchIdAndProductCode(String batchId, ProductCode productCode);

    void save(Batch batch);
}
```
BatchState:
```
package entities.batch;

public enum BatchState {
    ACTIVE("active");

    public final String value;

    BatchState(String value) {
        this.value = value;
    }

    public static BatchState fromString(String value) {
        return BatchState.valueOf(value.toUpperCase());
    }
}
```
InBatchMemoryRepository:
```
package entities.batch;

import entities.product.ProductCode;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.function.Predicate;

public class InMemoryBatchPackRepository implements BatchPackRepository {
    private List<Batch> batches;

    public InMemoryBatchPackRepository() {
        batches = new ArrayList<>();
    }

    public Batch findByBatchIdAndProductCode(String batchId, ProductCode productCode) {
        Optional<Batch> batch = batches.stream().filter(byBatchIdAndProductCode(batchId, productCode)).findAny();
        if (batch.isPresent()) {
            return batch.get();
        }

        throw new UnsupportedOperationException();
    }

    private Predicate<Batch> byBatchIdAndProductCode(String batchId, ProductCode productCode) {
        return b -> b.id.equals(batchId) &&
                b.productCode.isEquals(productCode);
    }

    public void save(Batch batch) {
        batches.add(batch);
    }
}
```
This is the Pack:
```
package entities.pack;

public class Pack {

  public final String serialNumber;
  private PackState state;

  public Pack(String serialNumber, PackState state) {
    this.serialNumber = serialNumber;
    this.state = state;
  }

  public PackState getState() {
    return state;
  }
}
```
and PackState:
```
package entities.pack;

public enum PackState {
  ACTIVE("active"),
  SUPPLIED("supplied");

  public final String value;

  PackState(String value) {
    this.value = value;
  }

  public static PackState fromString(String value) {
    return PackState.valueOf(value.toUpperCase());
  }
}
```
Finally we have the ProductRepository:
```
  
package entities.product;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.function.Predicate;

public class InMemoryProductRepository implements ProductRepository {
    private List<Product> products;

    public InMemoryProductRepository() {
        products = new ArrayList<>();
    }

    public Product findByProductCode(ProductCode productCode) {
        Optional<Product> product = products.stream().filter(byProductCode(productCode)).findAny();
        if (product.isPresent()) {
            return product.get();
        }

        throw new UnsupportedOperationException();
    }

    private Predicate<Product> byProductCode(ProductCode productCode) {
        return p -> p.getProductCode().isEquals(productCode);
    }

    public void save(Product product) {
        products.add(product);
    }
}
```
This is the Product:
```
package entities.product;

public class Product {
    private ProductCode productCode;
    private ProductState state;
    private String name;

    public Product(ProductState state, ProductCode productCode, String name) {
        this.state = state;
        this.productCode = productCode;
        this.name = name;
    }

    public ProductCode getProductCode() {
        return productCode;
    }

    public String getName() {
        return name;
    }

    public ProductState getState() {
        return state;
    }
}

```
ProductCode:

```
package entities.product;

public class ProductCode {
    public final String code;
    public final String scheme;

    public ProductCode(String code, String scheme) {
        this.code = code;
        this.scheme = scheme;
    }

    public boolean isEquals(ProductCode productCode) {
        return code.equals(productCode.code) &&
                scheme.equals(productCode.scheme);
    }
}
```
ProductRepository:
```
  
package entities.product;

public interface ProductRepository {
    Product findByProductCode(ProductCode productCode);

    void save(Product product);
}
```
Finally we have ProductState:
```
package entities.product;

public enum ProductState {
    ACTIVE("active");

    public final String value;

    ProductState(String value) {
        this.value = value;
    }

    public static ProductState fromString(String value) {
        return ProductState.valueOf(value.toUpperCase());
    }
}
```