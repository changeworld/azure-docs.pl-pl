---
title: Funkcja zdefiniowana przez użytkownika języka Java (UDF) z usługą Apache Hive Azure HDInsight
description: Dowiedz się, jak utworzyć funkcję zdefiniowaną przez użytkownika w języku Java (UDF), która współpracuje z gałęzią Apache. W tym przykładzie UDF konwertuje tabelę ciągów tekstowych na małe litery.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327208"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Używanie dysku Java UDF z gałęzią Apache w funkcji HDInsight

Dowiedz się, jak utworzyć funkcję zdefiniowaną przez użytkownika w języku Java (UDF), która współpracuje z gałęzią Apache. Java UDF w tym przykładzie konwertuje tabelę ciągów tekstowych na wszystkie małe litery.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop na HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Zestaw Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to system budowania projektów java.
* [Schemat identyfikatorów URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) dla magazynu podstawowego klastrów. Byłoby to wasb:// dla usługi Azure Storage, abfs:// dla usługi Azure Data Lake Storage Gen2 lub adl:// dla usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi Azure `wasbs://`Storage, identyfikator URI będzie .  Zobacz też [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

* Edytor tekstu lub środowisko Java IDE

    > [!IMPORTANT]  
    > Jeśli tworzysz pliki Języka Python na kliencie systemu Windows, należy użyć edytora, który używa LF jako zakończenia wiersza. Jeśli nie masz pewności, czy edytor używa LF lub CRLF, zobacz sekcję [Rozwiązywanie problemów,](#troubleshooting) aby uzyskać instrukcje dotyczące usuwania znaku CR.

## <a name="test-environment"></a>Środowisko testowe

W tym artykule użyto komputera z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane za pomocą Notatnika. Odpowiednio zmodyfikuj dla swojego środowiska.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko pracy:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Tworzenie przykładowego UDF języka Java

1. Utwórz nowy projekt Maven, wprowadzając następujące polecenie:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    To polecenie tworzy katalog `exampleudf`o nazwie , który zawiera projekt Maven.

2. Po utworzeniu projektu usuń `exampleudf/src/test` katalog utworzony w ramach projektu, wprowadzając następujące polecenie:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Otwórz, `pom.xml` wpisując poniższe polecenie:

    ```cmd
    notepad pom.xml
    ```

    Następnie zastąp istniejący `<dependencies>` wpis następującym kodem XML:

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

    Te wpisy określają wersję Hadoop i Hive dołączone do HDInsight 3.6. Informacje na temat wersji programu Hadoop i Hive dostarczonych z programem HDInsight można znaleźć w dokumencie [przechowywania wersji składników HDInsight.](../hdinsight-component-versioning.md)

    Dodaj `<build>` sekcję `</project>` przed wierszem na końcu pliku. Ta sekcja powinna zawierać następujący kod XML:

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

    Te wpisy definiują sposób tworzenia projektu. W szczególności wersja języka Java, który używa projektu i jak utworzyć uberjar do wdrożenia w klastrze.

    Zapisz plik po wprowadzeniu zmian.

4. Wprowadź poniższe polecenie, aby utworzyć `ExampleUDF.java`i otworzyć nowy plik:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Następnie skopiuj i wklej poniższy kod java do nowego pliku. Następnie zamknij plik.

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

    Ten kod implementuje UDF, który akceptuje wartość ciągu i zwraca wersję małych liter ciągu.

## <a name="build-and-install-the-udf"></a>Zbuduj i zainstaluj UDF

W poniższych poleceniach `sshuser` zamień na rzeczywistą nazwę użytkownika, jeśli jest inna. Zamień `mycluster` na rzeczywistą nazwę klastra.

1. Skompiluj i zapakuj UDF, wprowadzając następujące polecenie:

    ```cmd
    mvn compile package
    ```

    To polecenie tworzy i pakuje `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` UDF do pliku.

2. Użyj `scp` polecenia, aby skopiować plik do klastra HDInsight, wprowadzając następujące polecenie:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Połącz się z klastrem za pomocą SSH, wprowadzając następujące polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Z otwartej sesji SSH skopiuj plik jar do magazynu HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Korzystanie z UDF z hive

1. Uruchom klienta Beeline z sesji SSH, wprowadzając następujące polecenie:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    To polecenie zakłada, że użyto domyślnego **administratora** dla konta logowania dla klastra.

2. Po dojściu do monitu `jdbc:hive2://localhost:10001/>` wprowadź następujące elementy, aby dodać UDF do gałęzi i udostępnić go jako funkcję.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Użyj UDF do konwersji wartości pobranych z tabeli na małe ciągi przypadków.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Ta kwerenda wybiera stan z tabeli, konwertuje ciąg na małe litery, a następnie wyświetla je wraz z niezmodyfikowaną nazwą. Dane wyjściowe są podobne do następującego tekstu:

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

Podczas uruchamiania zadania gałęzi można natknąć się na błąd podobny do następującego tekstu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ten problem może być spowodowany przez zakończenia wierszy w pliku Python. Wiele edytorów systemu Windows domyślnie używa CRLF jako zakończenia linii, ale aplikacje Linux zwykle oczekują LF.

Następujące instrukcje programu PowerShell można użyć do usunięcia znaków CR przed przekazaniem pliku do programu HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać inne sposoby pracy z gałęzi, zobacz [Korzystanie z gałęzi Apache z hdinsight](hdinsight-use-hive.md).

Aby uzyskać więcej informacji na temat funkcji zdefiniowanych przez użytkownika [hive, zobacz Operatory gałęzi Apache i funkcje zdefiniowane przez użytkownika](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) w wiki hive w apache.org.
