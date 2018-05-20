---
title: Java — funkcja zdefiniowana przez użytkownika (UDF) przy użyciu Hive w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia opartych na języku Java — funkcja zdefiniowana przez użytkownika (UDF) współpracujące z gałęzi. W tym przykładzie UDF Konwertuje tabelę ciągów tekst na małe litery.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 00af8ca67af6ba3242c0fee6c50640944768ec4c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Użyj Java funkcji zdefiniowanej przez użytkownika przy użyciu Hive w usłudze HDInsight

Informacje o sposobie tworzenia opartych na języku Java — funkcja zdefiniowana przez użytkownika (UDF) współpracujące z gałęzi. UDF języka Java w tym przykładzie konwertuje wszystkie małe znaki tabeli ciągów tekstowych.

## <a name="requirements"></a>Wymagania

* Klaster usługi HDInsight 

    > [!IMPORTANT]
    > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

    Większość kroków w tym dokumencie działają w obu klastrach z systemem Windows i Linux. Jednak kroki do przekazywania skompilowanych UDF w klastrze i uruchom go są specyficzne dla opartych na systemie Linux klastrów. Zostały podane linki do informacji, które mogą być używane z klastrów z systemem Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 lub nowszy (lub odpowiednik, takie jak OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Edytora tekstu lub IDE języka Java

    > [!IMPORTANT]
    > Jeśli tworzysz pliki języka Python w kliencie systemu Windows, musisz użyć edytora, używającą LF jako zakończenia linii. Jeśli nie masz pewności, czy edytor używa LF lub CRLF, zobacz [Rozwiązywanie problemów](#troubleshooting) sekcji instrukcje dotyczące usuwania znak CR.

## <a name="create-an-example-java-udf"></a>Utwórz przykład UDF języka Java 

1. Z wiersza polecenia aby utworzyć nowy projekt Maven skorzystaj z następujących:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Jeśli używasz programu PowerShell, możesz umieścić stawiać cudzysłów wokół parametrów. Na przykład `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    To polecenie tworzy katalog o nazwie **exampleudf**, który zawiera projekt Maven.

2. Po utworzeniu projektu, należy usunąć **exampleudf/src/testowanie** katalogu, który został utworzony jako część projektu.

3. Otwórz **exampleudf/pom.xml**i Zastąp istniejące `<dependencies>` wpis o następujących XML:

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

    Te wpisy Określ wersję usług Hadoop i Hive dołączonego 3,6 HDInsight. Można znaleźć informacje o wersjach usług Hadoop i Hive wyposażone w usłudze HDInsight z [przechowywanie wersji składnika usługi HDInsight](../hdinsight-component-versioning.md) dokumentu.

    Dodaj `<build>` sekcji przed `</project>` wiersz na końcu pliku. W tej sekcji, powinny zawierać następujące XML:

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

    Te wpisy definiują sposób tworzenia projektu. W szczególności wersja języka Java, która używa projektu i sposobu tworzenia uberjar wdrożenia do klastra.

    Zapisz plik po dokonaniu zmian.

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

    Ten kod implementuje UDF, akceptuje wartości ciągu, która zwraca wersję małe litery w ciągu.

## <a name="build-and-install-the-udf"></a>Tworzenie i zainstalować funkcję zdefiniowaną przez użytkownika

1. Użyj następującego polecenia, aby skompilować i pakiet funkcji zdefiniowanej przez użytkownika:

    ```bash
    mvn compile package
    ```

    To polecenie tworzy, a następnie umieszcza UDF w `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` pliku.

2. Użyj `scp` polecenie, aby skopiować plik do klastra usługi HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Zastąp `myuser` przy użyciu konta użytkownika SSH dla klastra. Zastąp `mycluster` z nazwą klastra. Aby zabezpieczyć konto SSH użyto hasła, monit o wprowadzenie hasła. Jeśli używasz certyfikatu może być konieczne użycie `-i` parametr, aby określić plik klucza prywatnego.

3. Połącz z klastrem przy użyciu protokołu SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. W sesji SSH skopiuj plik jar w magazynie usługi HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Użyj funkcji zdefiniowanej przez użytkownika z gałęzi

1. Aby uruchomić klienta Beeline w sesji SSH, należy użyć następującego.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Tym poleceniu założono, że używana jest domyślna **admin** konta logowania dla klastra.

2. Po przyjeździe `jdbc:hive2://localhost:10001/>` monit, wprowadź następujące polecenie, aby dodać funkcję zdefiniowaną przez użytkownika do gałęzi i pozostawić ją jako funkcję.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > W tym przykładzie zakłada, że magazyn Azure domyślny magazyn dla klastra. Jeśli klaster używa zamiast tego Data Lake Store, zmień `wasb:///` do wartości `adl:///`.

3. Do konwersji wartości pobierane z tabeli ciągów małe litery, należy użyć funkcji zdefiniowanej przez użytkownika.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    To zapytanie wybiera platformy urządzenia (Android, Windows, iOS, itp.) z tabeli, należy przekonwertować ciąg do niższych wielkość liter, a następnie wyświetlić je. Wynik jest podobny do następującego tekstu:

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

Aby uzyskać inne sposoby pracy z Hive, zobacz [używanie Hive z usługą HDInsight](hdinsight-use-hive.md).

Aby uzyskać więcej informacji na temat funkcji Hive User-Defined, zobacz [operatorów Hive i funkcje zdefiniowane przez użytkownika](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) sekcji Wiki gałęzi w serwisie apache.org.
