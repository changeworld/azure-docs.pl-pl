---
title: Tworzenie mapy JavaReduce dla Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak użyć Apache Maven do utworzenia aplikacji MapReduce opartej na języku Java, a następnie uruchomić ją za pomocą usługi Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311958"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w usłudze HDInsight

Dowiedz się, jak użyć Apache Maven do utworzenia aplikacji MapReduce opartej na języku Java, a następnie uruchomić ją za pomocą apache Hadoop na platformie Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to system budowania projektów java.

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

W tym artykule użyto komputera z systemem Windows 10. Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane za pomocą Notatnika. Odpowiednio zmodyfikuj dla swojego środowiska.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko pracy:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

1. Wprowadź następujące polecenie, aby utworzyć projekt Maven o nazwie **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    To polecenie tworzy katalog o nazwie `artifactID` określonej przez parametr (**wordcountjava** w tym przykładzie.) Ten katalog zawiera następujące elementy:

    * `pom.xml`- [Model obiektu projektu (POM),](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) który zawiera informacje i szczegóły konfiguracji używane do tworzenia projektu.
    * src\main\java\org\apache\hadoop\examples: zawiera kod aplikacji.
    * src\test\java\org\apache\hadoop\examples: zawiera testy dla aplikacji.

1. Usuń wygenerowany przykładowy kod. Usuń wygenerowane pliki `AppTest.java`testowe `App.java` i aplikacyjne oraz wprowadzając poniższe polecenia:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizowanie modelu obiektu projektu

Aby uzyskać pełne odniesienie do pliku pom.xml, zobacz https://maven.apache.org/pom.html. Otwórz, `pom.xml` wpisując poniższe polecenie:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Dodawanie zależności

W `pom.xml`sekcji dodaj następujący `<dependencies>` tekst:

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

Definiuje wymagane biblioteki (wymienione &lt;w\>artifactId) z określoną &lt;\>wersją (wymienioną w wersji ). W czasie kompilacji te zależności są pobierane z domyślnego repozytorium Maven. Możesz użyć [wyszukiwania repozytorium Maven,](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) aby wyświetlić więcej.

Informuje `<scope>provided</scope>` Maven, że te zależności nie powinny być pakowane z aplikacją, ponieważ są one dostarczane przez klaster HDInsight w czasie wykonywania.

> [!IMPORTANT]
> Używana wersja powinna być zgodna z wersją programu Hadoop obecną w klastrze. Aby uzyskać więcej informacji na temat wersji, zobacz dokument [przechowywania wersji składnika HDInsight.](../hdinsight-component-versioning.md)

### <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki Maven umożliwiają dostosowanie etapów budowy projektu. Ta sekcja służy do dodawania wtyczek, zasobów i innych opcji konfiguracji kompilacji.

Dodaj następujący kod `pom.xml` do pliku, a następnie zapisz i zamknij plik. Ten tekst musi `<project>...</project>` znajdować się wewnątrz znaczników w `</dependencies>` `</project>`pliku, na przykład między i .

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

W tej sekcji skonfigurowano wtyczkę Apache Maven Compiler i wtyczkę Apache Maven Shade. Wtyczka kompilatora służy do kompilowania topologii. Wtyczka cienia służy do zapobiegania powielaniu licencji w pakiecie JAR, który jest zbudowany przez Maven. Ta wtyczka jest używana, aby zapobiec "zduplikowane pliki licencji" błąd w czasie wykonywania w klastrze HDInsight. Korzystanie z maven-shade-plugin z `ApacheLicenseResourceTransformer` implementacją zapobiega błędowi.

Maven-shade-plugin produkuje również uber jar, który zawiera wszystkie zależności wymagane przez aplikację.

Zapisz plik `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Tworzenie aplikacji MapReduce

1. Wprowadź poniższe polecenie, aby utworzyć `WordCount.java`i otworzyć nowy plik . Wybierz **pozycję Tak** w wierszu polecenia, aby utworzyć nowy plik.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Następnie skopiuj i wklej poniższy kod java do nowego pliku. Następnie zamknij plik.

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

    Zwróć uwagę, `org.apache.hadoop.examples` że nazwa pakietu `WordCount`jest i nazwa klasy jest . Nazwy te są używane podczas przesyłania zadania MapReduce.

## <a name="build-and-package-the-application"></a>Tworzenie i pakowanie aplikacji

Z `wordcountjava` katalogu użyj następującego polecenia, aby utworzyć plik JAR zawierający aplikację:

```cmd
mvn clean package
```

To polecenie czyści wszystkie poprzednie artefakty kompilacji, pobiera wszystkie zależności, które nie zostały jeszcze zainstalowane, a następnie tworzy i pakuje aplikację.

Po zakończeniu polecenia `wordcountjava/target` katalog zawiera plik o `wordcountjava-1.0-SNAPSHOT.jar`nazwie .

> [!NOTE]
> Plik `wordcountjava-1.0-SNAPSHOT.jar` jest uberjar, który zawiera nie tylko zadanie WordCount, ale także zależności, które zadanie wymaga w czasie wykonywania.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Prześlij jar i uruchom zadania (SSH)

Poniższe kroki `scp` można wykonać, aby skopiować JAR do głównego węzła głównego bazy danych Apache HBase w klastrze HDInsight. Polecenie `ssh` jest następnie używane do łączenia się z klastrem i uruchamiania przykładu bezpośrednio w węźle głównym.

1. Przekaż słoik do klastra. Zamień `CLUSTERNAME` na nazwę klastra USŁUGI HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Połącz się z klastrem. Zamień `CLUSTERNAME` na nazwę klastra USŁUGI HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH użyj następującego polecenia, aby uruchomić aplikację MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    To polecenie uruchamia aplikację WordCount MapReduce. Plik wejściowy `/example/data/gutenberg/davinci.txt`jest , a `/example/data/wordcountout`katalog wyjściowy jest . Zarówno plik wejściowy, jak i dane wyjściowe są przechowywane w domyślnym magazynie klastra.

1. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić wyniki:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Powinna pojawić się lista słów i zliczeń, z wartościami podobnymi do następującego tekstu:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak opracować zadanie Mapy JavaReduce. Zobacz następujące dokumenty, aby uzyskać inne sposoby pracy z hdinsight.

* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z mapReduce z HDInsight](hdinsight-use-mapreduce.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)
