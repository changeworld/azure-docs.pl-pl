---
title: Utwórz Java MapReduce dla platformy Hadoop — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Apache Maven do tworzenia aplikacji opartych na języku Java MapReduce, a następnie uruchom go z platformą Hadoop w usłudze Azure HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: ''
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: Java
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: c245b30a48be73f189dcf48502134cf36e93ba20
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169544"
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Tworzenie programów Java MapReduce dla platformy Hadoop w usłudze HDInsight

Dowiedz się, jak używać Apache Maven do tworzenia aplikacji opartych na języku Java MapReduce, a następnie uruchom go z platformą Hadoop w usłudze Azure HDInsight.

> [!NOTE]
> W tym przykładzie ostatnio zbadano 3,6 HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 lub nowszy (lub odpowiednik, takie jak OpenJDK).
    
    > [!NOTE]
    > HDInsight w wersji 3.4 i wcześniejszych Użyj Java 7. HDInsight w wersji 3.5 lub nowszej używa języka Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Po zainstalowaniu Java i zestaw JDK, który można konfigurować następujące zmienne środowiskowe. Jednak należy sprawdzić, czy istnieją i że zawierają one poprawne wartości dla systemu.

* `JAVA_HOME` -powinny wskazywać na katalog, w którym zainstalowano środowiska wykonawczego języka Java (JRE). Na przykład w systemie OS X, Unix lub Linux, powinien on wartość podobną do `/usr/lib/jvm/java-7-oracle`. W systemie Windows będzie mieć wartość podobną do `c:\Program Files (x86)\Java\jre1.7`

* `PATH` — powinna zawierać następujących ścieżkach:
  
  * `JAVA_HOME` (lub równoważne ścieżki)

  * `JAVA_HOME\bin` (lub równoważne ścieżki)

  * Katalogu, w którym zainstalowano Maven

## <a name="create-a-maven-project"></a>Utwórz projekt Maven

1. W sesji terminalowej lub wiersza polecenia w środowisku projektowania przejdź do lokalizacji przechowywania tego projektu.

2. Użyj `mvn` polecenia, które jest instalowany z Maven, aby wygenerować funkcją szkieletów dla projektu.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Jeśli używasz programu PowerShell, należy ująć `-D` parametrów w podwójny cudzysłów.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    To polecenie tworzy katalog o nazwie określonej przez `artifactID` parametr (**wordcountjava** w tym przykładzie.) Ten katalog zawiera następujące elementy:

   * `pom.xml` - [Projektu Object Model (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) zawiera informacje i szczegóły konfiguracji używany do tworzenia projektu.

   * `src` -Katalog zawierający aplikację.

3. Usuń `src/test/java/org/apache/hadoop/examples/apptest.java` pliku. Nie jest on używany w tym przykładzie.

## <a name="add-dependencies"></a>Dodaj zależności

1. Edytuj `pom.xml` i Dodaj następujący tekst wewnątrz `<dependencies>` sekcji:
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    Definiuje wymaganych bibliotek (wymienione w &lt;artifactId\>) z określoną wersją (wymienione w &lt;wersji\>). W czasie kompilacji te zależności są pobierane z repozytorium Maven domyślne. Można użyć [Maven repozytorium wyszukiwania](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) Aby wyświetlić więcej.
   
    `<scope>provided</scope>` Informuje Maven te zależności nie powinny być pakowane z aplikacją, jak są one udostępniane przez klaster usługi HDInsight w czasie wykonywania.

    > [!IMPORTANT]
    > Wersja użyta powinna być zgodna wersja platformy Hadoop w klastrze. Aby uzyskać więcej informacji o wersji, zobacz [przechowywanie wersji składnika usługi HDInsight](../hdinsight-component-versioning.md) dokumentu.

2. Dodaj następujący kod do `pom.xml` pliku. Ten tekst musi znajdować się wewnątrz `<project>...</project>` tagów w pliku, na przykład między `</dependencies>` i `</project>`.

   ```xml
    <build>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
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
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    Konfiguruje pierwszy wtyczki [Maven cień wtyczki](http://maven.apache.org/plugins/maven-shade-plugin/), który jest używany do tworzenia uberjar (nazywane również fatjar), która zawiera zależności wymagane przez aplikację. Uniemożliwia także dublowania licencji w pakiecie jar, co może spowodować problemy w niektórych systemach.

    Druga wtyczka konfiguruje docelową wersję języka Java.

    > [!NOTE]
    > HDInsight 3.4 i używanie wcześniejszych Java 7. HDInsight w wersji 3.5 lub nowszej używa języka Java 8.

3. Zapisz plik `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Tworzenie aplikacji MapReduce

1. Przejdź do `wordcountjava/src/main/java/org/apache/hadoop/examples` katalogu i Zmień nazwę `App.java` pliku `WordCount.java`.

2. Otwórz `WordCount.java` plik w edytorze tekstów i Zastąp zawartość następującym tekstem:
   
    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```
   
    Zwróć uwagę, nazwa pakietu jest `org.apache.hadoop.examples` i nazwa klasy jest `WordCount`. Za pomocą tych nazw można przesłać zadania MapReduce.

3. Zapisz plik.

## <a name="build-the-application"></a>Kompilowanie aplikacji

1. Zmień na `wordcountjava` katalogu, jeśli nie masz już istnieje.

2. Aby utworzyć plik JAR zawierający aplikację, użyj następującego polecenia:

   ```
   mvn clean package
   ```

    To polecenie czyści wszystkie poprzednie artefaktów kompilacji, pobierze wszelkie zależności, które nie został jeszcze zainstalowany, a następnie kompilacji i pakietu aplikacji.

3. Po zakończeniu działania polecenia `wordcountjava/target` katalog zawiera plik o nazwie `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` Plik jest uberjar, która zawiera nie tylko zadania WordCount, ale także zależności, które wymaga zadanie w czasie wykonywania.

## <a id="upload"></a>Przekaż jar

Użyj następującego polecenia, aby przekazać plik jar do HDInsight headnode:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

To polecenie kopiuje pliki z systemu lokalnego do węzła głównego. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Uruchom zadanie MapReduce na platformie Hadoop

1. Połączenie do usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. W sesji SSH wpisz następujące polecenie do uruchomienia aplikacji MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    To polecenie uruchamia aplikację WordCount MapReduce. Plik wejściowy jest `/example/data/gutenberg/davinci.txt`, i katalog wyjściowy jest `/example/data/wordcountout`. Plik wejściowy i wyjściowy są przechowywane do magazynu domyślnego dla klastra.

3. Po zakończeniu zadania, użyj następującego polecenia, aby wyświetlić wyniki:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Powinien zostać wyświetlony listę słów i liczb, wartości podobnie do następującego tekstu:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Następne kroki

W tym dokumencie mają przedstawiono sposób rozwijać zadania Java MapReduce. Można znaleźć w następujących dokumentach inne sposoby pracy z usługą HDInsight.

* [Korzystanie z programu Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

