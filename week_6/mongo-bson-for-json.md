##### BSON for JSON:
https://github.com/michel-kraemer/bson4jackson

Binary JSON with bson4jackson
Re­cently, JSON has be­come an ex­cel­lent al­tern­at­ive to XML. But most JSON pars­ers writ­ten in Java are still rather slow. On my search for faster lib­rar­ies I found two things: BSON and Jack­son.

BSON is bin­ary en­coded JSON. The format has been de­signed with fast ma­chine read­ab­il­ity in mind. BSON has gained prom­in­ence as the main data ex­change format for the doc­u­ment-ori­ented data­base man­age­ment sys­tem Mon­goDB. Ac­cord­ing to the JVM seri­al­izers bench­mark Jack­son is one of the fast­est JSON pro­cessors avail­able. Apart from that, Jack­son al­lows writ­ing cus­tom ex­ten­sions. This fea­ture can be used to add fur­ther data ex­change formats.
```
bson4jackson
This is the mo­ment where bson4­jack­son steps in. The lib­rary ex­tends Jack­son by the cap­ab­il­ity of read­ing and writ­ing BSON doc­u­ments. Since bson4­jack­son is fully in­teg­rated, you can use the very nice API of Jack­son to seri­al­ize simple PO­JOs. Think of the fol­low­ing class:

public class Person {
  private String _name;

  public void setName(String name) {
    _name = name;
  }

  public String getName() {
    return _name;
  }
}
You may use the ObjectMapper to quickly seri­al­ize ob­jects:

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import com.fasterxml.jackson.databind.ObjectMapper;
import de.undercouch.bson4jackson.BsonFactory;

public class ObjectMapperSample {
  public static void main(String[] args) throws Exception {
    //create dummy POJO
    Person bob = new Person();
    bob.setName("Bob");

    //serialize data
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    ObjectMapper mapper = new ObjectMapper(new BsonFactory());
    mapper.writeValue(baos, bob);

    //deserialize data
    ByteArrayInputStream bais = new ByteArrayInputStream(
      baos.toByteArray());
    Person clone_of_bob = mapper.readValue(bais, Person.class);

    assert bob.getName().equals(clone_of_bob.getName());
  }
}
Or you may use Jack­son’s stream­ing API and seri­al­ize the ob­ject manu­ally:

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.core.JsonFactory;
import com.fasterxml.jackson.core.JsonParser;
import com.fasterxml.jackson.core.JsonToken;
import de.undercouch.bson4jackson.BsonFactory;

public class ManualSample {
  public static void main(String[] args) throws Exception {
    //create dummy POJO
    Person bob = new Person();
    bob.setName("Bob");

    //create factory
    BsonFactory factory = new BsonFactory();

    //serialize data
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    JsonGenerator gen = factory.createJsonGenerator(baos);
    gen.writeStartObject();
    gen.writeFieldName("name");
    gen.writeString(bob.getName());
    gen.close();

    //deserialize data
    ByteArrayInputStream bais = new ByteArrayInputStream(
      baos.toByteArray());
    JsonParser parser = factory.createJsonParser(bais);
    Person clone_of_bob = new Person();
    parser.nextToken();
    while (parser.nextToken() != JsonToken.END_OBJECT) {
      String fieldname = parser.getCurrentName();
      parser.nextToken();
      if ("name".equals(fieldname)) {
        clone_of_bob.setName(parser.getText());
      }
    }

    assert bob.getName().equals(clone_of_bob.getName());
  }
}
```

This is MongoDB:
https://docs.mongodb.com/manual/core/capped-collections/