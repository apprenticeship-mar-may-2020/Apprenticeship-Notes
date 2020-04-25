##### Endurance challenge:
https://github.com/apavlidi/EnduranceSQL/blob/master/pom.xml
```
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>11</java.version>
    <maven.compiler.source>${java.version}</maven.compiler.source>
    <maven.compiler.target>${java.version}</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-lang3</artifactId>
      <version>3.7</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
    </dependency>
    <dependency>
      <groupId>org.easytesting</groupId>
      <artifactId>fest-assert</artifactId>
      <version>1.4</version>
    </dependency>
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-core</artifactId>
      <version>3.3.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-junit-jupiter</artifactId>
      <version>3.3.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.19</version>
    </dependency>
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-all</artifactId>
      <version>1.9.5</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>pl.pragmatists</groupId>
      <artifactId>JUnitParams</artifactId>
      <version>1.1.0</version>
    </dependency>
  </dependencies>
```

This is the acceptance test:
```

@ExtendWith(MockitoExtension.class)
public class ProjectFeature {

  @Test
  public void create_project_with_tasks() throws SQLException {
    String url1 = "jdbc:mysql://localhost:3306/endurance";
    String user = "root";
    String password = "Buster";
    Connection connection = DriverManager.getConnection(url1, user, password);

    ProjectRepository projectRepository = new SQLProjectRepository(connection);
    ProjectService projectService = new ProjectService(projectRepository);
    UUID taskId = UUID.randomUUID();
    UUID projectId = UUID.randomUUID();

    Task task = new Task(taskId, "task 1", "This is a description 1", LocalDate.of(2020, 1, 1), ComplexityEnum.MINIMUM);
    Project project = new Project(projectId, "project 1", "project description 1", LocalDate.of(2020, 1, 1),
        Collections.singletonList(task));

    projectService.save(project);
    Project retrievedProject = projectService.retrieveById(projectId);

    assertEquals(project, retrievedProject);
  }




}
```
Here we test for the connection to a database and that it returns the correct Project. This is still failing.

This is the domain test:
```

@ExtendWith(MockitoExtension.class)
public class ProjectServiceTest {

  @Mock
  private ProjectRepository sqlProjectRepository;

  @Test
  public void saves_a_project() {
    UUID taskId = UUID.randomUUID();
    UUID projectId = UUID.randomUUID();

    Task task = new Task(taskId, "task 1", "This is a description 1", LocalDate.of(2020, 1, 1), ComplexityEnum.MINIMUM);
    Project project = new Project(projectId, "project 1", "project description 1", LocalDate.of(2020, 1, 1),
        Collections.singletonList(task));

    ProjectService projectService = new ProjectService(sqlProjectRepository);
    projectService.save(project);

    verify(sqlProjectRepository).save(project);
  }


}
```
Here we check that the ProjectService calls the projectRepository.

Here we check that hte SQLProjectRepository saves the project to the database:
```

class SQLProjectRepositoryTest {


  @Test
  public void store_project_to_sql_without_any_errors() throws SQLException {
    String url1 = "jdbc:mysql://localhost:3306/endurance";
    String user = "root";
    String password = "Buster";
    Connection connection = DriverManager.getConnection(url1, user, password);

    SQLProjectRepository sqlProjectRepository = new SQLProjectRepository(connection);
    UUID taskId = UUID.randomUUID();
    UUID projectId = UUID.randomUUID();

    Task task = new Task(taskId, "task 1", "This is a description 1", LocalDate.of(2020, 1, 1), ComplexityEnum.MINIMUM);
    Project project = new Project(projectId, "project 1", "project description 1", LocalDate.of(2020, 1, 1),
        Collections.singletonList(task));

    sqlProjectRepository.save(project);

    String sql = "SELECT * from Project prooject where prooject.id = ?";
    PreparedStatement query = connection.prepareStatement(sql);
    query.setString(1, projectId.toString());
    ResultSet resultSet = query.executeQuery();
    assertTrue(resultSet.first());
  }

}
```
We are effectively testing that an entry is added to the database.

This is the Main.java which creates the connection:
```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class Main {

  private static Connection conn1;

  public static void main(String[] args) throws SQLException {
    String url1 = "jdbc:mysql://localhost:3306/endurance";
    String user = "root";
    String password = "Buster";

    conn1 = DriverManager.getConnection(url1, user, password);
    if (conn1 != null) {
      System.out.println("Connected to the database test1");
    }


  }

}
```
In the Domain folder we have a ProjectSErvice and a ProjectRepository:
```
package domain;

import entity.Project;
import java.util.UUID;

public class ProjectService {

  private ProjectRepository projectRepository;

  public ProjectService(ProjectRepository projectRepository) {
    this.projectRepository = projectRepository;
  }

  public void save(Project project) {
    projectRepository.save(project);
  }

  public Project retrieveById(UUID projectId) {
    throw new UnsupportedOperationException("Implement me!");
  }
}
```
This is the ProjectRepository:
```
package domain;

import entity.Project;
import java.util.UUID;

public interface ProjectRepository {

  void save(Project project);

  Project findById(UUID projectId);
}
```

The entity folder includes the Project:
```
package entity;

import java.time.LocalDate;
import java.util.List;
import java.util.UUID;

public class Project {

  private final UUID id;
  private final String name;
  private final String description;
  private final LocalDate deadline;
  private final List<Task> tasks;

  public Project(UUID id, String name, String description, LocalDate deadline, List<Task> tasks) {
    this.id = id;
    this.name = name;
    this.description = description;
    this.deadline = deadline;
    this.tasks = tasks;
  }
}
```
a complexity enum:
```
package entity;

public enum ComplexityEnum {
  MINIMUM
}
```
and a Task POJO:
```
package entity;

import java.time.LocalDate;
import java.util.UUID;

public class Task {

  public final UUID id;
  public final String name;
  public final String description;
  public final LocalDate deadline;
  public final ComplexityEnum complexity;

  public Task(UUID id, String name, String description, LocalDate deadline, ComplexityEnum complexity) {
    this.id = id;
    this.name = name;
    this.description = description;
    this.deadline = deadline;
    this.complexity = complexity;
  }

}
```
the infrastructure repository includes the SQLProjectRepository:
```
  
package infrastructure;

import domain.ProjectRepository;
import entity.Project;
import java.sql.Connection;
import java.util.UUID;

public class SQLProjectRepository implements ProjectRepository {

  public SQLProjectRepository(Connection connection) {
  }

  @Override
  public void save(Project project) {
  }

  @Override
  public Project findById(UUID projectId) {
    throw new UnsupportedOperationException();
  }

}
```
this is the picture of the ERD:

![Screenshot 2020-04-06 at 23 51 33](https://user-images.githubusercontent.com/27693622/78612570-92e71500-7861-11ea-8892-055b194c1110.png)

This is an example of database connection:
https://stackoverflow.com/questions/13950496/what-is-java-io-eofexception-message-can-not-read-response-from-server-expect
```
I had this problem but I it wasn't possible for me to make changes in MySQL database configuration. Therefore I ensured that in my sql connector class the connection is always closed before it's initiated again. Something like:

public static Connection getConnection() {

    if (DatabaseConnnector.conn == null) {
        initConn();
    } else {
        try {
            DatabaseConnnector.conn.close();          
        } catch (SQLException e) {
            e.printStackTrace();
        }
          initConn();
    }
    return DatabaseConnnector.conn;
}
```
This is a repeat of the excellent article on docker and mysql connection:
https://medium.com/@rauf.rahmancz/connect-docker-and-mysql-in-right-way-95602f833cb0

These were some of the commands I used for the docker database image and connection:
```
brew services stop mysql
mysql -u root
brew services start mysql
docker ps -a
docker rm ea62156a4d9b
docker stop ea62156a4d9b
docker ps
docker exec -it mysql mysql -uroot -p
docker run -p 3306:3307 --name mysql -e MYSQL_ROOT_PASSWORD=password -d mysql
docker start mysql
mysql
docker rm 2ff715323161
docer ps
docker stop 2ff715323161
docker exec -it mysql mysql -uroot -p password
docker exec -it mysql mysql -uroot -p AjMOqg@vANk@J@SK@kdOxYSUqli
docker logs mysql
brew install mysql-client
apt-get install mysql-client
docker docker exec -it mysql mysql -uroot
docker docker exec -it mysql mysql -uroot -p
docker run --name=mysql -d mysql/mysql-server:latest
docker images
docker pull mysql/mysql-server:latest
mysql -uroot
brew install mysql
```

These commands were excellent:
```
λ docker run -d -p 3306:3306 — name=Home-Server-1  — env=MYSQL_ROOT_PASSWORD="password" mysql
docker: invalid reference format.
See 'docker run --help'.
λ docker run -d -p 3306:3306 --name=Home-Server-1  --env=MYSQL_ROOT_PASSWORD="password" mysql
865ee40d6287d01f956b1f7afe1be0bec8c41fa4873333a0dde65b3430da9a3f
λ docker ps                                                                                  ~
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
865ee40d6287        mysql               "docker-entrypoint.s…"   4 seconds ago       Up 3 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   Home-Server-1
λ docker exec -it Home-Server-1 bash
```

and allowed me to connect to the database with:
```
mysql -h 127.0.0.1 -P 3306 -u root -p
```

This worked:
```
λ docker run -d -p 3306:3306 --name=Home-Server-1  --env=MYSQL_ROOT_PASSWORD="password" mysql
d3e509d5ce5471615425802ef0c6bba60bea031252bc4d2d9723798c4528b911
λ docker exec -it Home-Server-1 bash                                                   ~
root@d3e509d5ce54:/#
```
then I could do:
```
λ mysql -h 127.0.0.1 -P 3306 -u root -p
```