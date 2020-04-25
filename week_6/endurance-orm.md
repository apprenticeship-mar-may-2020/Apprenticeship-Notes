###### Endurance ORM

This was the extraction of the Endurance to use Springboot API and Hibernate. This was the main method which created a connection to the database:
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

This was the POM file which included the following:
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>project</artifactId>
  <version>1.0-SNAPSHOT</version>

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

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.2.5.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
      <exclusions>
        <exclusion>
          <groupId>org.junit.vintage</groupId>
          <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>

</project>
```
This included the following springBoot dependencies:
```
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.2.5.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
      <exclusions>
        <exclusion>
          <groupId>org.junit.vintage</groupId>
          <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
```
Also we had this plugin:
```
   <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
```
This was the Acceptance test:
```
@ExtendWith(MockitoExtension.class)
public class ProjectFeature {

  @Test
  public void create_project_with_tasks() throws SQLException, NoTasksException {
    String url1 = "jdbc:mysql://localhost:3306/endurance";
    String user = "root";
    String password = "Buster";
    Connection connection = DriverManager.getConnection(url1, user, password);

    ProjectRepository projectRepository = new SQLProjectRepository(connection);
    ProjectService projectService = new ProjectService(projectRepository);
    int taskId = 1;
    int projectId = 1;

    Task task = new Task(taskId, "task 1", "This is a description 1", LocalDate.of(2020, 1, 1), ComplexityEnum.MINIMUM);
    Project project = new Project(projectId, "project 1", "project description 1", LocalDate.of(2020, 1, 1),
        Collections.singletonList(task));

    projectService.save(project);
    Project retrievedProject = projectService.retrieveById(projectId);

    assertEquals(project, retrievedProject);
  }
}
```
This checked for the connection and also created a Task and ensured that it was created and retrieved from the Repository. The ProjectService test looked like this:
```
@ExtendWith(MockitoExtension.class)
public class ProjectServiceTest {

  @Mock
  private ProjectRepository sqlProjectRepository;

  @Test
  public void saves_a_project() throws NoTasksException {
    int taskId = 1;
    int projectId = 1;

    Task task = new Task(taskId, "task 1", "This is a description 1", LocalDate.of(2020, 1, 1), ComplexityEnum.MINIMUM);
    Project project = new Project(projectId, "project 1", "project description 1", LocalDate.of(2020, 1, 1),
        Collections.singletonList(task));

    ProjectService projectService = new ProjectService(sqlProjectRepository);
    projectService.save(project);

    verify(sqlProjectRepository).save(project);
  }


}
```
Here we also ensured that the Repository had saved the project. Our SQLRepository test was the following:
```
class SQLProjectRepositoryTest {

  private static Connection connection;

  @BeforeAll
  static void beforeAll() throws SQLException {
    String url1 = "jdbc:mysql://localhost:3306/endurance";
    String user = "root";
    String password = "password";
    connection = DriverManager.getConnection(url1, user, password);
  }

  @Test
  public void store_project_to_sql_without_any_errors() throws SQLException, NoTasksException, InvalidKeyException {

    SQLProjectRepository sqlProjectRepository = new SQLProjectRepository(connection);
    int taskId = 1;
    int projectId = 1;

    Task task = new Task(taskId, "task 1", "This is a description 1", LocalDate.of(2020, 1, 1), ComplexityEnum.MINIMUM);
    Project project = new Project(projectId, "project 1", "project description 1", LocalDate.of(2020, 1, 1),
        Collections.singletonList(task));

    sqlProjectRepository.save(project);

    String sql = "SELECT * from Project project where project.id = ?";
    PreparedStatement query = connection.prepareStatement(sql);
    query.setInt(1, projectId);
    ResultSet resultSet = query.executeQuery();
    assertTrue(resultSet.first());
    Project projectFound = sqlProjectRepository.findById(project.id);
  }


  @AfterEach
  void tearDown() throws SQLException {
    String deleteTask = "DELETE FROM Task";
    String deleteProject = "DELETE FROM Project";
    PreparedStatement deleteTaskQuery = connection.prepareStatement(deleteTask);
    PreparedStatement deleteProjectQuery = connection.prepareStatement(deleteProject);
    deleteTaskQuery.execute();
    deleteProjectQuery.execute();
  }
}
```
Here we had a teardown and ensured that we could find the project by ID from the repository.

Our production code included the following folder structure:
```
domain
entity
exceptions
infrastructure
```
The repository looked like this:
```
  
package domain;

import entity.Project;

import java.security.InvalidKeyException;
import java.sql.SQLException;
import java.util.UUID;

public interface ProjectRepository {

  void save(Project project);

  Project findById(int projectId) throws SQLException, InvalidKeyException;
}
```
The projectService was as yet unimplemented:
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

  public Project retrieveById(int projectId) {
    throw new UnsupportedOperationException("Implement me!");
  }
}
```
The complexity enum was quite simple:
```
package entity;

public enum ComplexityEnum {
  MINIMUM(1),
  MEDIUM(2),
  MAXIMUM(3);

  public final int level;

  ComplexityEnum(int level) {
    this.level = level;
  }

  public static ComplexityEnum getByLevel(int number) {
    for (ComplexityEnum c : ComplexityEnum.values()){
      if (c.level == number){
        return c;
      }
    }
    throw new IllegalArgumentException();
  }
}
```
The Project looked like this:
```
package entity;

import exceptions.NoTasksException;

import java.time.LocalDate;
import java.util.List;
import java.util.UUID;

public class Project {

  public final int id;
  public final String name;
  public final String description;
  public final LocalDate deadline;
  public final List<Task> tasks;

  public Project(int id, String name, String description, LocalDate deadline, List<Task> tasks) throws NoTasksException {
    if (tasks.isEmpty()){
      throw new NoTasksException();
    }

    this.id = id;
    this.name = name;
    this.description = description;
    this.deadline = deadline;
    this.tasks = tasks;
  }
}
```
Here I added a tasks.isEmpty() check with help from Sebastian:
```
    if (tasks.isEmpty()){
      throw new NoTasksException();
    }

```
This was the Task:
```
    if (tasks.isEmpty()){
      throw new NoTasksException();
    }

```

This is the SQLProjectRepository file that we made to connect to the JDBC:
```
package infrastructure;

import domain.ProjectRepository;
import entity.ComplexityEnum;
import entity.Project;
import entity.Task;
import exceptions.NoTasksException;

import java.security.InvalidKeyException;
import java.sql.*;
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.UUID;

public class SQLProjectRepository implements ProjectRepository {

  private Connection connection;

  public SQLProjectRepository(Connection connection) {
    this.connection = connection;
  }

  @Override
  public void save(Project project) {
    String projectSql = "INSERT INTO Project (id, name, description, deadline)\n" +
            "VALUES (?, ?, ?, ?);";

    String[] taskAttributes = {
            "id",
            "name",
            "description",
            "deadline",
            "project_id",
            "complexity_id"
    };

    String taskSql = "INSERT INTO Task (" +  String.join(", ", taskAttributes)  + ")\n" +
            "VALUES (?, ?, ?, ?, ?, ?)";

    try {
      connection.setAutoCommit(false);
      PreparedStatement insertProject = connection.prepareStatement(projectSql);
      PreparedStatement insertTask = connection.prepareStatement(taskSql);

      insertProject.setInt(1, project.id);
      insertProject.setString(2, project.name);
      insertProject.setString(3, project.description);
      insertProject.setString(4, project.deadline.toString());

      Task task = project.tasks.get(0);
      insertTask.setInt(1, task.id);
      insertTask.setString(2, task.name);
      insertTask.setString(3, task.description);
      insertTask.setDate(4, Date.valueOf(task.deadline));
      insertTask.setInt(5, project.id);
      insertTask.setInt(6, task.complexity.level);

      insertProject.execute();
      insertTask.execute();

      connection.commit();

    }catch(Exception e){
      System.out.println(e);
    }finally {

      try {
        connection.setAutoCommit(true);
      } catch (SQLException e) {
        e.printStackTrace();
      }
    }
  }

  @Override
  public Project findById(int projectId) throws SQLException, InvalidKeyException {
    String findProjectSql = "SELECT * FROM Project WHERE id = ?";
    PreparedStatement projectQuery = connection.prepareStatement(findProjectSql);
    projectQuery.setInt(1, projectId);
    ResultSet projectResult = projectQuery.executeQuery();
    projectResult.next();

    String findTaskSql = "SELECT * FROM Task where project_id = ?";
    PreparedStatement taskQuery = connection.prepareStatement(findTaskSql);
    taskQuery.setInt(1, projectResult.getInt("id"));
    ResultSet taskResult = taskQuery.executeQuery();

    List<Task> listOfTasks = new ArrayList<>();
    while(taskResult.next()){
      listOfTasks.add(new Task(taskResult.getInt("id"),
              taskResult.getString("name"),
              taskResult.getString("description"),
              taskResult.getDate("deadline").toLocalDate(),
              ComplexityEnum.getByLevel(taskResult.getInt("complexity_id"))));
    }

    try {
      Project project = new Project(projectResult.getInt("id"),
              projectResult.getString("name"),
              projectResult.getString("description"),
              projectResult.getDate("deadline").toLocalDate(),
              listOfTasks);
      return project;
    } catch (NoTasksException e) {
      e.printStackTrace();
    }
    throw new InvalidKeyException();
  }
}
```