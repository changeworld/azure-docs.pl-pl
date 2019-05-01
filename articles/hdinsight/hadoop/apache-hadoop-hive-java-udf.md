---
title: Java funkcji zdefiniowanej przez użytkownika (UDF) przy użyciu technologii Hive w HDInsight — Azure
description: Informacje o sposobie tworzenia opartych na języku Java — funkcja zdefiniowana przez użytkownika (UDF) współdziałająca z usługą Apache Hive. W tym przykładzie UDF Konwertuje tabelę ciągów tekstowych na małe litery.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 24c2e8b9600b3d622d3d6b42b3bc3615a87ff853
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686630"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Korzystanie z języka Java funkcji zdefiniowanej przez użytkownika przy użyciu Apache Hive w HDInsight

Informacje o sposobie tworzenia opartych na języku Java — funkcja zdefiniowana przez użytkownika (UDF) współdziałająca z usługą Apache Hive. UDF języka Java, w tym przykładzie konwertuje wszystkie małe znaki tabeli ciągów tekstowych.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks)
* [Narzędzia Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowane](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to projekt system kompilacji dla projektów Java.
* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do obsługi klastrów magazynu podstawowego. Takie rozwiązanie byłoby wasb: / / dla usługi Azure Storage, abfs: / / dla usługi Azure Data Lake Storage Gen2 lub systemu plików adl: / / dla usługi Azure Data Lake Storage Gen1. Bezpieczny transfer jest włączona dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI będzie mieć wasbs: / / lub abfss: / / odpowiednio Zobacz też [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

* Edytor tekstu lub środowiska IDE Java

    > [!IMPORTANT]  
    > Jeśli tworzysz pliki języka Python na kliencie Windows, należy użyć edytora używającej LF, jako koniec wiersza. Jeśli nie masz pewności, czy edytor używa wysuwu wiersza i CRLF, zobacz [Rozwiązywanie problemów](#troubleshooting) sekcji, aby uzyskać instrukcje dotyczące usuwania znaków CR.

## <a name="test-environment"></a>Środowisko testowe
Środowiska używanego na potrzeby tego artykułu jest komputer z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki zostały zmienione w Notatniku. Zmodyfikuj odpowiednio dla danego środowiska.

W wierszu polecenia wpisz poniższe polecenia, aby utworzyć środowiska pracy:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Utwórz przykład UDF języka Java

1. Utwórz nowy projekt narzędzia Maven, wprowadzając następujące polecenie:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    To polecenie tworzy katalog o nazwie `exampleudf`, która zawiera projekt narzędzia Maven.

2. Po utworzeniu projektu, usunąć `exampleudf/src/test` katalogu, który został utworzony w ramach projektu, wprowadzając następujące polecenie:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Otwórz `pom.xml` , wprowadzając poniższe polecenie:

    ```cmd
    notepad pom.xml
    ```

    Następnie Zastąp istniejące `<dependencies>` wpis o następujący kod XML:

    ```xml
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
    ```

    Te wpisy Określ wersję usług Hadoop i Hive dołączone HDInsight 3.6. Można znaleźć informacje o wersji usługi Hadoop i Hive, wyposażone w HDInsight z [przechowywanie wersji składnika HDInsight](../hdinsight-component-versioning.md) dokumentu.

    Dodaj `<build>` sekcji przed `</project>` wiersza na końcu pliku. W tej sekcji może zawierać następujący kod XML:

    ```xml
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
                <version>3.2.1</version>
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
    ```

    Te wpisy definiują sposób skompilować projekt. W szczególności wersja języka Java, która używa projektu oraz sposób kompilowania uberjar we wdrożeniach w klastrze.

    Zapisz plik, gdy zostały zmienione.

4. Wprowadź polecenie, aby utworzyć i otworzyć nowy plik `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Następnie skopiuj i wklej poniższy kod języka java do nowego pliku. Następnie zamknij plik.

    ```java
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
    ```

    Ten kod implementuje funkcji zdefiniowanej przez użytkownika, która przyjmuje wartość ciągu i zwraca małe wersję ciągu.

## <a name="build-and-install-the-udf"></a>Tworzenie i instalowanie funkcji zdefiniowanej przez użytkownika

W poniższych poleceń Zastąp `sshuser` rzeczywiste nazwy użytkownika, jeśli jest inna. Zastąp `mycluster` nazwą rzeczywistego klastra.

1. Skompilować i utworzyć pakiet funkcji zdefiniowanej przez użytkownika, wprowadzając następujące polecenie:

    ```cmd
    mvn compile package
    ```

    To polecenie tworzy, a następnie umieszcza funkcji zdefiniowanej przez użytkownika do `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` pliku.

2. Użyj `scp` polecenie, aby skopiować plik z klastrem HDInsight, wprowadzając następujące polecenie:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Połącz się z klastrem przy użyciu protokołu SSH, wprowadzając następujące polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Otwieranie sesji SSH należy skopiować plik jar do magazynu HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Użyj funkcji zdefiniowanej przez użytkownika z programu Hive

1. Uruchom klienta z usługi Beeline w sesji SSH, wprowadzając następujące polecenie:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tego polecenia przyjęto założenie, że używany jest domyślny **administratora** dla konta logowania dla klastra.

2. Po przyjeździe do biura `jdbc:hive2://localhost:10001/>` monit, wprowadź następujące polecenie, aby dodać funkcji zdefiniowanej przez użytkownika do gałęzi i udostępnić ją jako funkcję.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Aby przekonwertować wartościami pobranymi z tabeli na ciągi małymi literami, należy użyć funkcji zdefiniowanej przez użytkownika.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    To zapytanie wybiera stan z tabeli, konwertowania ciągu w niższych zamierzone, Zapisz i wyświetl je wraz z nazwą zostały zmodyfikowane. Dane wyjściowe wyglądają podobnie do następującego tekstu:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas uruchamiania zadania hive, może wystąpić błąd podobny do następującego tekstu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ten problem może być spowodowane końce wierszy w pliku języka Python. Wiele Windows edytory domyślną wartość zwykle sygnalizowany znakiem CRLF jako koniec wiersza, ale aplikacje dla systemu Linux oczekiwać LF.

Następujące instrukcje programu PowerShell służy do usuwania znaków CR przed przekazaniem go do usługi HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Kolejne kroki

Inne sposoby pracy z programu Hive, zobacz [używanie programu Apache Hive z HDInsight](hdinsight-use-hive.md).

Aby uzyskać więcej informacji na temat funkcji Hive User-Defined, zobacz [Apache Hive operatory i funkcje zdefiniowane przez użytkownika](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) sekcji wiki gałęzi w serwisie apache.org.
