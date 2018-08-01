# hive-udf

1. mvn archetype:generate -DgroupId=aws -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

2. Open the exampleudf/pom.xml, and replace the existing <dependencies> entry with the following XML:

<dependencies>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hive</groupId>
        <artifactId>hive-exec</artifactId>
        <version>1.2.1</version>
        <scope>provided</scope>
    </dependency>
</dependencies>

3. Add a <build> section before the </project> line at the end of the file. This section should contain the following XML:

<build>
    <plugins>
        <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
                <!-- Keep us from getting a can't overwrite file error -->
                <transformers>
                    <transformer
                            implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>



4. Rename exampleudf/src/main/java/com/microsoft/examples/App.java to ExampleUDF.java, and then open the file in your editor.

5. Replace the contents of the ExampleUDF.java file with the following, then save the file.

package com.microsoft.examples;

import org.apache.hadoop.hive.ql.exec.Description;
import org.apache.hadoop.hive.ql.exec.UDF;
import org.apache.hadoop.io.*;

// Description of the UDF
@Description(
    name="ExampleUDF",
    value="returns a lower case version of the input string.",
    extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
)
public class ExampleUDF extends UDF {
    // Accept a string input
    public String evaluate(String input) {
        // If the value is null, return a null
        if(input == null)
            return null;
        // Lowercase the input string and return it
        return input.toLowerCase();
    }
}

This code implements a UDF that accepts a string value, and returns a lowercase version of the string.

6. Use the following command to compile and package the UDF:

mvn compile package

This command builds and packages the UDF into the exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar file.

