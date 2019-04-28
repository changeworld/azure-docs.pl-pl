---
title: Utwórz MapReduce w języku Java dla usługi Apache Hadoop — Azure HDInsight
description: Informacje o sposobie tworzenia aplikacji opartych na języku Java MapReduce, a następnie uruchom go z usługą Hadoop w usłudze Azure HDInsight przy użyciu narzędzia Apache Maven.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: e4f567fe727a2ec308cef6f48e33728f7f31c217
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122000"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w HDInsight

Informacje o sposobie tworzenia aplikacji opartych na języku Java MapReduce, a następnie uruchom go przy użyciu technologii Apache Hadoop w usłudze Azure HDInsight przy użyciu narzędzia Apache Maven.

> [!NOTE]
> W tym przykładzie ostatnio został przetestowany w HDInsight 3.6.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/) 8 lub nowszy (lub równoważny, taki jak OpenJDK).
    
    > [!NOTE]
    > HDInsight w wersji 3.4 i starszych korzystanie z języka Java 7. HDInsight 3.5 i nowszych używa Java 8.

* [Apache Maven](https://maven.apache.org/)

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Następujące zmienne środowiskowe można ustawić podczas instalacji języka Java i zestawu JDK. Należy jednak sprawdzić, czy te zmienne istnieją i czy zawierają poprawne wartości dla systemu.

* `JAVA_HOME` -powinien wskazywać katalog, w którym zainstalowano środowiska wykonawczego języka Java (JRE). Na przykład w systemie OS X, Unix lub Linux, powinien mieć wartość podobną do `/usr/lib/jvm/java-7-oracle`. W Windows będzie miała wartość podobne do `c:\Program Files (x86)\Java\jre1.7`

* `PATH` — powinna zawierać następujące ścieżki:
  
  * `JAVA_HOME` (lub równoważne ścieżki)

  * `JAVA_HOME\bin` (lub równoważne ścieżki)

  * Katalog, w którym zainstalowano narzędzia Maven

## <a name="create-a-maven-project"></a>Utwórz projekt narzędzia Maven

1. W sesji terminalowej lub wiersza polecenia w środowisku deweloperskim Zmień katalogi na lokalizację przechowywania tego projektu.

2. Użyj `mvn` polecenia, który jest instalowany za pomocą narzędzia Maven, można wygenerować funkcją szkieletów dla projektu.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Jeśli używasz programu PowerShell, należy ująć `-D` parametrów w cudzysłów.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    To polecenie tworzy katalog o nazwie określonej przez `artifactID` parametru (**wordcountjava** w tym przykładzie.) Ten katalog zawiera następujące elementy:

   * `pom.xml` [Modelu obiektu projektu (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) zawierający informacje i szczegóły konfiguracji używane do tworzenia projektu.

   * `src` Katalog, który zawiera aplikację.

3. Usuń `src/test/java/org/apache/hadoop/examples/apptest.java` pliku. Nie jest używany w tym przykładzie.

## <a name="add-dependencies"></a>Dodaj zależności

1. Edytuj `pom.xml` pliku i Dodaj następujący tekst wewnątrz `<dependencies>` sekcji:
   
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

    Definiuje wymaganych bibliotek (wymienione w ramach &lt;artifactId\>) z określoną wersją (wymienione w ramach &lt;wersji\>). W czasie kompilacji te zależności są pobierane z repozytorium Maven domyślne. Możesz użyć [funkcji wyszukiwania narzędzia Maven repozytorium](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) Aby wyświetlić więcej.
   
    `<scope>provided</scope>` Informuje o narzędzia Maven, nie można spakować te zależności z aplikacją, ponieważ są one udostępniane przez klaster usługi HDInsight w czasie wykonywania.

    > [!IMPORTANT]
    > Wersja użyta powinien być zgodny z wersją obecne w klastrze usługi Hadoop. Aby uzyskać więcej informacji na temat wersji, zobacz [przechowywanie wersji składnika HDInsight](../hdinsight-component-versioning.md) dokumentu.

2. Dodaj następującą zawartość do pliku `pom.xml`. Ten tekst musi znajdować się wewnątrz `<project>...</project>` tagów w pliku; na przykład między `</dependencies>` i `</project>`.

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

    Konfiguruje pierwszy wtyczki [wtyczki odcień Maven](https://maven.apache.org/plugins/maven-shade-plugin/), który jest używany do tworzenia uberjar (nazywane czasem fatjar), która zawiera zależności wymagane przez aplikację. Uniemożliwia ona dublowania licencji w ramach pakiet jar, co może powodować problemy w niektórych systemach.

    Druga wtyczka konfiguruje docelowej wersji języka Java.

    > [!NOTE]
    > HDInsight 3.4 i użycie wcześniejszych Java 7. HDInsight 3.5 i nowszych używa Java 8.

3. Zapisz plik `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Tworzenie aplikacji MapReduce

1. Przejdź do `wordcountjava/src/main/java/org/apache/hadoop/examples` katalogu i Zmień nazwę `App.java` plik `WordCount.java`.

2. Otwórz `WordCount.java` plik w edytorze tekstów i zastąp jego zawartość następującym tekstem:
   
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
   
    Zwróć uwagę, pakiet nazywa `org.apache.hadoop.examples` i nazwa klasy jest `WordCount`. Te nazwy są używane podczas przesyłania zadania MapReduce.

3. Zapisz plik.

## <a name="build-the-application"></a>Kompilowanie aplikacji

1. Zmień `wordcountjava` katalogu, jeśli nie jesteś już istnieje.

2. Aby utworzyć plik JAR zawierający aplikację, użyj następującego polecenia:

   ```
   mvn clean package
   ```

    To polecenie czyści wszystkie poprzednie artefaktów kompilacji, pobiera wszystkie zależności, które nie zostały jeszcze zainstalowany, i następnie kompilacje i pakietu aplikacji.

3. Po zakończeniu działania polecenia `wordcountjava/target` katalog zawiera plik o nazwie `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` Plik jest uberjar, który zawiera nie tylko zadania WordCount, ale także zależności wymagane przez zadanie w czasie wykonywania.

## <a id="upload"></a>Przekaż plik jar

Użyj następującego polecenia, aby przekazać plik jar do węzła głównego HDInsight:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

Zastąp __USERNAME__ z Twoją nazwą użytkownika protokołu SSH dla klastra. Zastąp __CLUSTERNAME__ o nazwie klastra HDInsight.

To polecenie kopiuje pliki z systemu lokalnego do węzła głównego. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Uruchom zadanie MapReduce w usłudze Hadoop

1. Nawiązać HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. W sesji SSH wpisz następujące polecenie do uruchomienia aplikacji MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    To polecenie uruchamia aplikację WordCount MapReduce. Plik wejściowy jest `/example/data/gutenberg/davinci.txt`, a katalog wyjściowy jest `/example/data/wordcountout`. Plik wejściowy i wyjściowy są przechowywane do domyślnego magazynu klastra.

3. Po ukończeniu zadania, użyj następującego polecenia, aby wyświetlić wyniki:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Powinien zostać wyświetlony listę wyrazy i liczby wartościami, podobny do następującego tekstu:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Następne kroki

W tym dokumencie mają przedstawiono sposób tworzenia zadania MapReduce w języku Java. Zobacz następujące dokumenty, aby inne sposoby pracy z HDInsight.

* [Use Apache Hive z HDInsight](hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: https://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

