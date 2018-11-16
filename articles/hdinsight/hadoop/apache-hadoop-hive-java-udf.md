---
title: Java funkcji zdefiniowanej przez użytkownika (UDF) przy użyciu technologii Hive w HDInsight — Azure
description: Informacje o sposobie tworzenia opartych na języku Java — funkcja zdefiniowana przez użytkownika (UDF) współdziałająca z usługą Apache Hive. W tym przykładzie UDF Konwertuje tabelę ciągów tekstowych na małe litery.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 562320b49f92d9ebb0c74c06e27fab63681955be
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634077"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Korzystanie z języka Java funkcji zdefiniowanej przez użytkownika przy użyciu Apache Hive w HDInsight

Informacje o sposobie tworzenia opartych na języku Java — funkcja zdefiniowana przez użytkownika (UDF) współdziałająca z usługą Apache Hive. UDF języka Java, w tym przykładzie konwertuje wszystkie małe znaki tabeli ciągów tekstowych.

## <a name="requirements"></a>Wymagania

* Klaster usługi HDInsight 

    > [!IMPORTANT]
    > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

    Większość czynności w tym dokumencie działać w obu klastrach z systemem Windows i Linux. Jednak są kroki używane do przekazywania skompilowanych UDF w klastrze, a następnie uruchom go, są specyficzne dla klastrów opartych na systemie Linux. Podano linki do informacji, które mogą być używane z klastrami z systemem Windows.

* [Zestaw Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 lub nowszy (lub równoważny, taki jak OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Edytor tekstu lub środowiska IDE Java

    > [!IMPORTANT]
    > Jeśli tworzysz pliki języka Python na kliencie Windows, należy użyć edytora używającej LF, jako koniec wiersza. Jeśli nie masz pewności, czy edytor używa wysuwu wiersza i CRLF, zobacz [Rozwiązywanie problemów](#troubleshooting) sekcji, aby uzyskać instrukcje dotyczące usuwania znaków CR.

## <a name="create-an-example-java-udf"></a>Utwórz przykład UDF języka Java 

1. Z poziomu wiersza polecenia należy wykonać następujące kroki i Utwórz nowy projekt narzędzia Maven:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Jeśli używasz programu PowerShell, należy umieścić w cudzysłowie parametry. Na przykład `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    To polecenie tworzy katalog o nazwie **exampleudf**, która zawiera projekt narzędzia Maven.

2. Po utworzeniu projektu, usunąć **exampleudf/src/testowania** katalogu, który został utworzony w ramach projektu.

3. Otwórz **exampleudf/pom.xml**i Zastąp istniejące `<dependencies>` wpis o następujący kod XML:

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
    ```

    Te wpisy definiują sposób skompilować projekt. W szczególności wersja języka Java, która używa projektu oraz sposób kompilowania uberjar we wdrożeniach w klastrze.

    Zapisz plik, gdy zostały zmienione.

4. Zmień nazwę **exampleudf/src/main/java/com/microsoft/examples/App.java** do **ExampleUDF.java**, a następnie otwórz plik w edytorze.

5. Zastąp zawartość **ExampleUDF.java** pliku następującym kodem, a następnie zapisz plik.

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

1. Aby skompilować i utworzyć pakiet funkcji zdefiniowanej przez użytkownika, użyj następującego polecenia:

    ```bash
    mvn compile package
    ```

    To polecenie tworzy, a następnie umieszcza funkcji zdefiniowanej przez użytkownika do `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` pliku.

2. Użyj `scp` polecenie, aby skopiować plik z klastrem HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Zastąp `myuser` przy użyciu konta użytkownika SSH dla klastra. Zastąp `mycluster` nazwą klastra. Aby zabezpieczyć konto SSH użyto hasła, monit o wprowadzenie hasła. Jeśli używany jest certyfikat może być konieczne użycie `-i` parametru, aby określić plik klucza prywatnego.

3. Połącz się z klastrem przy użyciu protokołu SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. W sesji SSH należy skopiować plik jar do magazynu HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Użyj funkcji zdefiniowanej przez użytkownika z programu Hive

1. Użyj następującego polecenia można uruchomić klienta z usługi Beeline w sesji SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tego polecenia przyjęto założenie, że używany jest domyślny **administratora** dla konta logowania dla klastra.

2. Po przyjeździe do biura `jdbc:hive2://localhost:10001/>` monit, wprowadź następujące polecenie, aby dodać funkcji zdefiniowanej przez użytkownika do gałęzi i udostępnić ją jako funkcję.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > W tym przykładzie przyjęto założenie, że usługi Azure Storage jest magazynem domyślnym dla klastra. Jeśli w klastrze jest używany zamiast tego Data Lake Store, zmień `wasb:///` wartość `adl:///`.

3. Aby przekonwertować wartościami pobranymi z tabeli na ciągi małymi literami, należy użyć funkcji zdefiniowanej przez użytkownika.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    To zapytanie wybiera platformę urządzenia (Android, Windows, iOS, itp.) z tabeli, należy przekonwertować ciąg do niższych zamierzone, Zapisz i wyświetl je. Dane wyjściowe wyglądają podobnie do następującego tekstu:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Kolejne kroki

Inne sposoby pracy z programu Hive, zobacz [korzystanie z programu Hive z HDInsight](hdinsight-use-hive.md).

Aby uzyskać więcej informacji na temat funkcji Hive User-Defined, zobacz [Hive operatory i funkcje zdefiniowane przez użytkownika](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) sekcji wiki gałęzi w serwisie apache.org.
