---
title: Apache Storm przykładową topologię Java — Azure HDInsight
description: Dowiedz się, jak utworzyć topologii Apache Storm w języku Java, tworząc topologii zliczania wyrazów w przykładzie.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
keywords: System Apache storm, apache storm przykład storm w języku java, przykład topologii systemu storm
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 10ea0eb3907abc0a90e589e57970c0e4622a4d56
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43043933"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Tworzenie topologii Apache Storm w języku Java

Dowiedz się, jak utworzyć topologię, oparte na języku Java dla platformy Apache Storm. Możesz utworzyć topologię Storm, który implementuje aplikacji zliczania wyrazów. Używasz narzędzia Maven do tworzenia i pakiet z projektem. Następnie dowiesz się, jak zdefiniować topologii przy użyciu framework strumienia.

Po wykonaniu tych kroków w tym dokumencie, można wdrożyć topologię Storm Apache na HDInsight.

> [!NOTE]
> Pełną wersję przykłady topologii systemu Storm, utworzone w tym dokumencie znajduje się w temacie [ https://github.com/Azure-Samples/hdinsight-java-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Wymagania wstępne

* [Java Developer Kit (JDK) w wersji 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

* [Narzędzie maven (https://maven.apache.org/download.cgi)](https://maven.apache.org/download.cgi): Maven to system kompilacji projektu dla projektów języka Java.

* Edytor tekstu lub w środowisku IDE.

## <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Następujące zmienne środowiskowe można ustawić podczas instalacji języka Java i zestawu JDK. Należy jednak sprawdzić, czy te zmienne istnieją i czy zawierają poprawne wartości dla systemu.

* **JAVA_HOME** -powinien wskazywać katalog, w którym zainstalowano środowiska wykonawczego języka Java (JRE). Na przykład w dystrybucji systemu Unix lub Linux, powinien mieć wartość podobną do `/usr/lib/jvm/java-8-oracle`. W Windows będzie miała wartość podobne do `c:\Program Files (x86)\Java\jre1.8`

* **ŚCIEŻKA** -może zawierać następujących ścieżek:

  * **JAVA_HOME** (lub równoważne ścieżki)

  * **JAVA_HOME\bin** (lub równoważne ścieżki)

  * Katalog, w którym zainstalowano narzędzia Maven

## <a name="create-a-maven-project"></a>Utwórz projekt narzędzia Maven

W wierszu polecenia, użyj następującego polecenia, aby utworzyć projekt narzędzia Maven o nazwie **WordCount**:

```bash
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false
```

> [!NOTE]
> Jeśli używasz programu PowerShell, należy otoczyć`-D` parametrów z podwójnymi cudzysłowami.
>
> `mvn archetype:generate "-DarchetypeArtifactId=maven-archetype-quickstart" "-DgroupId=com.microsoft.example" "-DartifactId=WordCount" "-DinteractiveMode=false"`

To polecenie tworzy katalog o nazwie `WordCount` w bieżącej lokalizacji, która zawiera podstawowe Projekt narzędzia Maven. `WordCount` Katalog zawiera następujące elementy:

* `pom.xml`: Zawiera ustawienia dla Projekt narzędzia Maven.
* `src\main\java\com\microsoft\example`Zawiera kod aplikacji.
* `src\test\java\com\microsoft\example`: Zawiera testy dla aplikacji. 

### <a name="remove-the-generated-example-code"></a>Usuń wygenerowane przykładowy kod

Usuń wygenerowany test i pliki aplikacji:

* **src\test\java\com\microsoft\example\AppTest.java**
* **src\main\java\com\microsoft\example\App.java**

## <a name="add-maven-repositories"></a>Dodawanie repozytoriów narzędzia Maven

HDInsight jest oparta na platformie danych Hortonworks (HDP), tak więc zaleca się przy użyciu repozytorium Hortonworks, aby pobrać zależności dla projektów systemu Apache Storm. W __pom.xml__ pliku i Dodaj następujący kod XML po `<url>http://maven.apache.org</url>` wiersza:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>http://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>http://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Dodawanie właściwości

Maven umożliwia zdefiniowanie wartości na poziomie projektu o nazwie właściwości. W __pom.xml__, Dodaj następujący tekst po `</repositories>` wiersza:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Teraz możesz użyć tej wartości w innych częściach `pom.xml`. Na przykład, określając wersję składników systemu Storm, można użyć `${storm.version}` zamiast twardych kodowania wartości.

## <a name="add-dependencies"></a>Dodaj zależności

Dodawanie zależności składników systemu Storm. Otwórz `pom.xml` pliku i Dodaj następujący kod w `<dependencies>` sekcji:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

W czasie kompilacji narzędzia Maven używa tych informacji w celu wyszukania `storm-core` w repozytorium narzędzia Maven. Najpierw szuka w repozytorium na komputerze lokalnym. Jeśli pliki nie istnieje, narzędzia Maven pobiera je z publicznego repozytorium Maven i przechowuje je w repozytorium lokalnym.

> [!NOTE]
> Zwróć uwagę `<scope>provided</scope>` wiersza w tej sekcji. To ustawienie określa Maven, aby wykluczyć **storm core** z wszystkie pliki JAR, które są tworzone, ponieważ jest ona udostępniana przez system.

## <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki maven umożliwiają dostosowanie etapy kompilacji generują projektu. Na przykład jak projekt jest skompilowany lub jak spakujesz ją do pliku JAR. Otwórz `pom.xml` pliku i Dodaj następujący kod bezpośrednio powyżej `</project>` wiersza.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Ta sekcja umożliwia dodawanie dodatków plug-in, zasoby i inne opcje konfiguracji kompilacji. Aby uzyskać pełną dokumentację z **pom.xml** plików, zobacz [ http://maven.apache.org/pom.html ](http://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Dodawanie wtyczek

Dla topologii Apache Storm zaimplementowane w języku Java [wtyczki Maven Exec](http://www.mojohaus.org/exec-maven-plugin/) jest przydatne, ponieważ pozwala na łatwe uruchamianie topologii lokalnie w środowisku programistycznym. Dodaj następujące polecenie, aby `<plugins>` części `pom.xml` pliku, aby uwzględnić wtyczki Exec Maven:

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.5.0</version>
    <executions>
        <execution>
        <goals>
            <goal>exec</goal>
        </goals>
        </execution>
    </executions>
    <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
        <cleanupDaemonThreads>false</cleanupDaemonThreads> 
    </configuration>
</plugin>
```

Inny, które są przydatne, wtyczka jest [wtyczki programu Apache Maven kompilatora](http://maven.apache.org/plugins/maven-compiler-plugin/), umożliwiający umożliwia zmianę opcji kompilacji. Zmiany języka Java wersję, która korzysta z narzędzia Maven dla źródłowego i docelowego dla aplikacji.

* Dla HDInsight __3.4 lub starszym__, ustaw źródło i docelowa wersja środowiska Java do __1.7__.

* Aby uzyskać HDInsight __3.5__, ustaw źródło i docelowa wersja środowiska Java do __1.8__.

Dodaj następujący tekst w `<plugins>` części `pom.xml` pliku, aby uwzględnić wtyczki Apache Maven kompilatora. W tym przykładzie określa 1.8, więc docelowa wersja HDInsight 3.5.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.3</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

### <a name="configure-resources"></a>Konfigurowanie zasobów

Sekcja zasobów umożliwia zostaną umieszczone zasoby bez kodu, takich jak pliki konfiguracyjne wymagane przez składniki w topologii. W tym przykładzie należy dodać następujący tekst w `<resources>` części "plik pom.xml.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Ten przykład dodaje katalog zasobów w katalogu głównym projektu (`${basedir}`) jako lokalizacji, która zawiera zasoby i zawiera plik o nazwie `log4j2.xml`. Ten plik jest używany do konfigurowania, jakie informacje są rejestrowane przez topologię.

## <a name="create-the-topology"></a>Tworzenie topologii

Topologii opartych na języku Java Apache Storm składa się z trzech składników, które należy utworzyć (lub odwołania) jako zależność.

* **Spouts**: odczytuje dane z zewnętrznych źródeł i emituje strumieni danych do topologii.

* **Bolts**: przetwarza strumienie emitowane przez elementy spout lub inne elementy bolt i emituje jeden lub więcej strumieni.

* **Topologia**: Określa jak elementy spout i bolt są uporządkowane i zapewnia punkt wejścia dla topologii.

### <a name="create-the-spout"></a>Utwórz spout

Aby zmniejszyć wymagania dotyczące konfigurowania zewnętrznych źródeł danych, spout następujące po prostu generuje losowe zdania. To zmodyfikowana wersja spout, która jest dostarczana z [przykładów z projektu Storm Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [!NOTE]
> Na przykład spout, która odczytuje z zewnętrznego źródła danych zobacz jeden z poniższych przykładach:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): spout przykład, która odczytuje z serwisu Twitter
> * [Platforma Kafka STORM](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): spout, która odczytuje z platformy Kafka

Dla spout, Utwórz plik o nazwie `RandomSentenceSpout.java` w `src\main\java\com\microsoft\example` katalogu i użyj następujących Java kodu jako zawartość:

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]
> Chociaż ta topologia jest używany tylko jeden spout, inne mogą zawierać kilka innych, które źródła danych z różnych źródeł do topologii.

### <a name="create-the-bolts"></a>Tworzenie elementów bolt

Elementy bolt obsługi przetwarzania danych. Ta topologia używa dwóch elementów bolt:

* **SplitSentence**: dzieli zdania wyemitowane przez **RandomSentenceSpout** do poszczególnych wyrazów.

* **WordCount**: zlicza liczbę wystąpień każdego wyrazu.

> [!NOTE]
> Można to zrobić wszystko, na przykład obliczeń, trwałości lub mówić do składników zewnętrznych elementów bolt.

Utwórz dwa nowe pliki `SplitSentence.java` i `WordCount.java` w `src\main\java\com\microsoft\example` katalogu. Skorzystaj z poniższego tekstu jako zawartość dla plików:

#### <a name="splitsentence"></a>SplitSentence

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Zdefiniuj topologię

Topologia łączy elementów spout i bolts razem do wykresu, który definiuje sposób przepływu danych między składnikami. Umożliwia także równoległości wskazówek dotyczących serwerów, które system Storm używa podczas tworzenia wystąpienia składników w klastrze.

Poniższa ilustracja jest podstawowy diagram wykresu składników tej topologii.

![Diagram przedstawiający rozmieszczenie elementów spout i bolt](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Aby zaimplementować topologii, Utwórz plik o nazwie `WordCountTopology.java` w `src\main\java\com\microsoft\example` katalogu. Użyj poniższego kodu języka Java jako zawartość pliku:

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

System Storm używa mechanizmu Apache Log4j do rejestrowania informacji. Jeśli rejestrowanie nie jest skonfigurowane, topologia emituje informacje diagnostyczne. Aby kontrolować, co jest rejestrowane, Utwórz plik o nazwie `log4j2.xml` w `resources` katalogu. Użyj następujący kod XML jako zawartość pliku.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

Poniższy kod XML umożliwia skonfigurowanie nowego rejestratora dla `com.microsoft.example` klasy, która obejmuje składniki w tym przykładzie topologii. Ustawiono poziom śledzenia dla tego rejestratora, który będzie przechwytywał wszelkie informacje rejestrowania wyemitowane przez składniki w tej topologii.

`<Root level="error">` Sekcji konfiguruje poziom główny rejestrowania (wszystko, czego nie `com.microsoft.example`) Aby rejestrować jedynie informacje o błędzie.

Aby uzyskać więcej informacji na temat konfigurowania rejestrowania Log4j, zobacz [ http://logging.apache.org/log4j/2.x/manual/configuration.html ](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]
> System STORM w wersji 0.10.0 i wyższe użycie mechanizmu Log4j 2.x. Starsze wersje programu storm używane Log4j 1.x, używany inny format dziennika konfiguracji. Instrukcje dotyczące konfiguracji starsze, zobacz [ http://wiki.apache.org/logging-log4j/Log4jXmlFormat ](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testowanie topologii lokalnie

Po zapisaniu plików, użyj następującego polecenia, aby przetestować topologii lokalnie.

```bash
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Po jej uruchomieniu topologii Wyświetla informacje o uruchamianiu. Następujący tekst jest przykład danych wyjściowych liczba word:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Ten przykładowy dziennik wskazuje, że wyraz "i" został wyemitowany 113 razy. Liczba w dalszym ciągu wzrosną, tak długo, jak topologia działa, ponieważ spout stale emituje tego samego zdań.

Istnieje 5-sekundowego interwału między emisji wyrazy i liczby. **WordCount** składnik jest skonfigurowany tylko emitowanie informacji po nadejściu krotki. Żąda ona, że znaczników, które krotek są dostarczane tylko co pięć sekund.

## <a name="convert-the-topology-to-flux"></a>Konwertuj topologii na strumień

Strumień jest nowej struktury dostępne z systemem Storm 0.10.0 lub nowszy, dzięki czemu można oddzielić konfiguracji z wdrożenia. Składniki nadal są zdefiniowane w języku Java, ale topologii definiuje się przy użyciu pliku YAML. Pakiet definicji topologii domyślnej z projektem lub używać autonomicznego pliku podczas przesyłania topologii. Podczas przesyłania topologii systemu Storm, można użyć zmiennych środowiskowych lub pliki konfiguracji do wypełniania wartości w definicji topologii YAML.

Plik YAML definiuje składników na potrzeby topologii oraz dane przepływ między nimi. Można dołączyć plik YAML, jako część pliku jar, lub można użyć zewnętrznego pliku YAML.

Aby uzyskać więcej informacji na temat strumienia, zobacz [framework strumienia (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html).

> [!WARNING]
> Ze względu na [usterki (https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) z systemem Storm 1.0.1, użytkownik może być konieczne zainstalowanie [Środowisko deweloperskie systemu Storm](http://storm.apache.org/releases/current/Setting-up-development-environment.html) do uruchomienia topologii strumienia lokalnie.

1. Przenieś `WordCountTopology.java` plik z projektu. Wcześniej ten plik zdefiniowany topologii, ale nie jest wymagane przy użyciu strumienia.

2. W `resources` katalogu Utwórz plik o nazwie `topology.yaml`. Skorzystaj z poniższego tekstu jako zawartość tego pliku.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. Wprowadź następujące zmiany do `pom.xml` pliku.
   
   * Dodaj następującą zależność nowy w `<dependencies>` sekcji:
     
        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```
   * Dodaj następujące wtyczki do `<plugins>` sekcji. Ta wtyczka obsługuje tworzenia pakietu (pliku jar) dla projektu i ma zastosowanie niektórych określonych przekształceń do strumienia, podczas tworzenia pakietu.
     
        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
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
        ```

   * W **wtyczki exec-maven** `<configuration>` sekcji, zmień wartość `<mainClass>` do `org.apache.storm.flux.Flux`. To ustawienie umożliwia strumień do obsługi uruchomiona topologia lokalnie w trakcie opracowywania.

   * W `<resources>` Dodaj następujące polecenie, aby `<includes>`. Plik XML zawiera plik YAML, który definiuje topologii w ramach projektu.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testowanie topologii strumienia lokalnie

1. Aby skompilować i wykonać topologii strumień przy użyciu narzędzia Maven, należy użyć następujących:

    ```bash
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    Jeśli używasz programu PowerShell, użyj następującego polecenia:

    ```bash
    mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"
    ```

    > [!WARNING]
    > Topologia używa on usługi bits Storm 1.0.1, to polecenie nie powiedzie się. Ten błąd jest spowodowany przez [ https://issues.apache.org/jira/browse/STORM-2055 ](https://issues.apache.org/jira/browse/STORM-2055). Zamiast tego [zainstalować system Storm w środowisku projektowym](http://storm.apache.org/releases/current/Setting-up-development-environment.html) i wykonaj następujące czynności:
    >
    > Jeśli masz [zainstalowany system Storm w środowisku projektowym](http://storm.apache.org/releases/current/Setting-up-development-environment.html), zamiast tego użyj następujących poleceń:
    >
    > ```bash
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local` Parametr działa topologii w trybie lokalnym w środowisku deweloperskim. `-R /topology.yaml` Używa parametru `topology.yaml` plik zasobów z pliku jar, aby określić topologię.

    Po jej uruchomieniu topologii Wyświetla informacje o uruchamianiu. Następujący tekst jest przykładem dane wyjściowe:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Ma 10 sekund opóźnienia między partie zarejestrowane informacje.

2. Utwórz kopię `topology.yaml` pliku z projektem. Nadaj nowemu plikowi `newtopology.yaml`. W `newtopology.yaml` pliku Znajdź następującą sekcję i zmień wartość właściwości `10` do `5`. Ta modyfikacja zmienia odstęp między emitowanie partie liczba znaków z zakresu od 10 do 5.

    ```yaml
    - id: "counter-bolt"
    className: "com.microsoft.example.WordCount"
    constructorArgs:
    - 5
    parallelism: 1
    ```yaml

3. To run the topology, use the following command:

    ```bash
    mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
    ```

    Lub, jeśli masz system Storm w środowisku deweloperskim:

    ```bash
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
    ```

    Zmiana `/path/to/newtopology.yaml` na ścieżkę do pliku newtopology.yaml utworzonego w poprzednim kroku. To polecenie używa newtopology.yaml jako definicji topologii. Ponieważ firma Microsoft nie obejmuje `compile` parametru Maven korzysta z wersji programu project, utworzone w poprzednich krokach.

    Po uruchomieniu topologii, można zauważyć, że czas między emitowany partie została zmieniona na odzwierciedlać wartości w newtopology.yaml. Aby było widać, które możesz zmieniać konfiguracji przy użyciu pliku YAML, bez konieczności ponownego kompilowania topologii.

Aby uzyskać więcej informacji na temat tych i innych funkcji w ramach strumienia, zobacz [strumienia (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html).

## <a name="trident"></a>Trident

Trident to Abstrakcja wysokiego poziomu, dostarczone przez system Storm. Obsługuje ona stanowych przetwarzania. Główną zaletą Trident to, że mógł zagwarantować, że każdy komunikat, który wprowadzi topologia jest przetwarzany tylko raz. Bez korzystania z języka Trident, topologii można tylko gwarantuje, że komunikaty są przetwarzane co najmniej raz. Istnieją także inne różnice, takie jak wbudowane składniki, których można użyć zamiast tworzenia elementów bolt. W rzeczywistości elementy bolt są zastępowane przez składniki mniej ogólnych, takich jak funkcje, projekcje i filtry.

Trident aplikacje można tworzyć przy użyciu narzędzia Maven projektów. Użyj te same czynności podstawowe, jak przedstawiono wcześniej w tym artykule — tylko do kodu jest inny. Trident również nie (obecnie) można używać z architekturą strumienia.

Aby uzyskać więcej informacji na temat języka Trident, zobacz [omówienie interfejsu API Trident](http://storm.apache.org/documentation/Trident-API-Overview.html).

## <a name="next-steps"></a>Następne kroki

Masz przedstawiono sposób tworzenia topologii systemu Storm przy użyciu platformy Java. Teraz Dowiedz się, jak:

* [Wdrażanie i zarządzanie topologiami Apache Storm na HDInsight](apache-storm-deploy-monitor-topology.md)

* [Opracowywanie topologii języka C# dla Storm Apache na HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Możesz znaleźć więcej przykład topologii systemu Storm, odwiedzając [przykładowe topologie dla systemu Storm w HDInsight](apache-storm-example-topology.md).

