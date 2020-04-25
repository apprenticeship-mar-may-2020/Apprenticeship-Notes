##### AWSProfileCLI
##### Spec- AWS Profile CLI Tool

Some devs have a number of AWS credentials they use. Luckily, AWS provides the "profiles" feature that enables one to store a number of credentials and switch between them.

Specifically, these details are stored in two files- ~/.aws/config and ~/.aws/credentials.

The format of config is like so (where profile_name is a placeholder for the name of the profile):
```
[profile profile_name]
output = json
region = eu-west-1
```
And the format of credentials is like so:
```
[profile_name]
aws_access_key_id = access_key
aws_secret_access_key = secret_access_key
```
A few notes:

- The [ and ] characters are a part of the file format that identifies the name of the profile. Strangely, config uses [profile profile_name] while credentials uses just [profile_name]. This is just AWS being daft.

- The output is almost always going to be json unless the reader is from the year 3000, using some fourth-dimensional quantum markup language not yet invented.

- The region is often the same too. The credentials are much more likely to change.

- The spacing before and after the = is, AFAIK, required. The end of the value is denoted by end of line or end of file.

- One can change one's AWS profile by setting the value of the environmental variable AWS_PROFILE.

- While AWS_PROFILE is set, all subsequent AWS CLI calls will use that profile for its configuration and authentication.

- However, the interface for changing AWS profile is awkward.

- If you don't recall what profiles you have available you need to read your configuration files, switching them requires setting an environment variable, and it isn't easy to add them either.

- Thus, we're going to write a simple CLI tool called aps (AWS Profile Switcher) with the following commands:

- aps list - lists available AWS profiles (newline separated).

- aps switch // Added (Tom Spencer) <profile> - switches to the specified profile (sets AWS_PROFILE)

- aps add <access_key> <secret_access_key> - adds a new profile (edited)

```
package com.codurance.aws_profile;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;

import java.io.FileNotFoundException;

import static org.mockito.Mockito.verify;

@ExtendWith(MockitoExtension.class)
public class Acceptance {

  @Mock
  private Printer printer;

  @Test
  void lists__profiles() throws FileNotFoundException {
    String filePath = System.getProperty("user.dir") + "/src/test/java/com/codurance/aws_profile/aws-test" + "/credentials";

    AWSProfileSwitcher awsProfileSwitcher = new AWSProfileSwitcher(printer, filePath);

    awsProfileSwitcher.list();

    verify(printer).print("toms_profile\n" +
                          "harrys_profile");
  }
}
```
Here I create a mock from the Printer class and then put a file path into the AWSProfileSwitcher. I then call awsProfileSwitcher.list(). Next I check that the class then calls a printer with toms_profile and harrys_profile.

This is the acceptance test with looks for the file and finds the profile. This is the App itself:
```
package com.codurance.aws_profile;

import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class AWSProfileSwitcher {
  private final Printer printer;
  private final String pathConfig;


  public AWSProfileSwitcher(Printer printer, String pathConfig) {
    this.printer = printer;
    this.pathConfig = pathConfig;
  }

  public void list() throws FileNotFoundException {
    StringBuilder profile = new StringBuilder();

    File file = new File(pathConfig);

    Scanner reader = new Scanner(file);

    while(reader.hasNextLine()){
      String line = reader.nextLine();
      if (line.charAt(0) == '['){
        profile.append(line.substring(1,line.length() - 1));
        profile.append("\n");
      }
    }

    String result = profile.toString().trim();

    printer.print(result);
  }
}
```
Here we add the printer and pathConfig as dependencies. We then have the list() method which creates a StringBuilder and finds the file using the pathConfig. We then create a Scanner using the file. Next we loop over each line of the file and append the line to the StringBuilder() if it contains the '[]'. We then call print on the printer using the StringBuilder result. I did make an AWSFileReader class but I haven't yet implemented the methods here.


This was the AcceptanceTest for the AWSConsole:
```
package com.codurance.aws_profile;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.io.IOException;

import static org.mockito.Mockito.verify;

@ExtendWith(MockitoExtension.class)
public class AcceptanceTest {

  @Mock
  private Printer printer;

  @Test
  void apps_list_shows_profiles() throws IOException {
    String[] path = {"/src/test/",
            "java/com/codurance/",
            "aws_profile/aws-test",
            "/credentials"};
    String filePath = System.getProperty("user.dir") + String.join("", path);
    AWSProfileSwitcher awsProfileSwitcher = new AWSProfileSwitcher(printer, filePath);

    awsProfileSwitcher.execute("aps list");

    verify(printer).print("toms_profile\n" +
                          "harrys_profile\n" +
                          "jerrys_profile");
  }

  @Test
  void aps_switch_changes_specified_profile_environment_variable() throws IOException {
    AWSProfileSwitcher awsProfileSwitcher = new AWSProfileSwitcher(printer, "path");
    awsProfileSwitcher.execute("aps switch toms_profile");
  }
}

```
This included two tests: the list() method and an unfinished test for the switch functionality. The Code so far looks like the following:
```
package com.codurance.aws_profile;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.util.Scanner;

public class AWSProfileSwitcher {
  private final Printer printer;
  private final String pathConfig;


  public AWSProfileSwitcher(Printer printer, String pathConfig) {
    this.printer = printer;
    this.pathConfig = pathConfig;
  }

  public void execute(String command) throws IOException {
    if (command.equals("aps list")) {
      list();
    }
    String[] switchCommands = command.split(" ");
    System.out.println("This is execute");
    if (command.contains("switch")){
    System.out.println("This is execute 2...");

      String profile = switchCommands[2];
      switchProfile(profile);
    }
  }

  public void switchProfile(String profile) throws IOException{
    String[] command = {"sh", "-c", "export", "AWS_PROFILE=" + profile};

    ProcessBuilder processBuilder = new ProcessBuilder(command);
    processBuilder.directory(new File(System.getProperty("user.home")));


    try {
      System.out.println("This is the switch");
      processBuilder.start();
      System.out.println(System.getenv("AWS_PROFILE"));

    } catch (IOException e) {
      System.out.println(e);
      e.printStackTrace();
    }
  }



  public void list() throws FileNotFoundException {
    StringBuilder profile = new StringBuilder();
    Scanner reader = new Scanner(new File(pathConfig));

    while(reader.hasNextLine()){
      String line = reader.nextLine();
      if (line.charAt(0) == '['){
        profile.append(line.substring(1,line.length() - 1));
        profile.append("\n");
      }
    }

    String result = profile.toString().trim();
    printer.print(result);
  }
}

```
#### I added the following notes to my feedback:
https://docs.google.com/document/d/10PW19nw5zUS-5BpJFII5O9BW68BOhWi-hTCN47yLOWE/edit?ts=5e8221a8
```
This is useful feedback. I have continued to work on the kata here:
https://github.com/TomSpencerLondon/AWSProfileCLI
 
www 
Improved intelliJ shortcuts
Some improved algorithm work
ebi
Not being able to run the mocks for the tests. I realised afterwards I had left out mockito-junit (although I had included mockito-core)
More work on algorithms. I will continue practice on these here:
https://github.com/TomSpencerLondon/algorithms-practice
```
