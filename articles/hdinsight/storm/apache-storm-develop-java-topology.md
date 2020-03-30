---
title: Apache Storm przykład topologii Java - Azure HDInsight
description: Dowiedz się, jak utworzyć topologie Apache Storm w języku Java, tworząc przykładową topologię liczby słów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 75100b47ddf8f36ed9a22ff3073c439f8ad9040b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083295"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Tworzenie topologii burzy Apache w języku Java

Dowiedz się, jak utworzyć topologię opartą na języku Java dla [Apache Storm](https://storm.apache.org/). W tym miejscu należy utworzyć topologię Storm, która implementuje aplikację zliczania wyrazów. [Apache Maven](https://maven.apache.org/) służy do tworzenia i pakowania projektu. Następnie dowiesz się, jak zdefiniować topologię przy użyciu struktury [Apache Storm Flux.](https://storm.apache.org/releases/2.0.0/flux.html)

Po wykonaniu kroków w tym dokumencie można wdrożyć topologię w programie Apache Storm w programie HDInsight.

> [!NOTE]  
> Ukończona wersja przykładów topologii burzy utworzona w [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)tym dokumencie jest dostępna pod adresem .

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to system budowania projektów java.

## <a name="test-environment"></a>Środowisko testowe

W tym artykule użyto komputera z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane za pomocą Notatnika.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko pracy:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

Wprowadź następujące polecenie, aby utworzyć projekt Maven o nazwie **WordCount:**

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

To polecenie tworzy katalog `WordCount` o nazwie w bieżącej lokalizacji, który zawiera podstawowy projekt Maven. Drugie polecenie zmienia obecny katalog `WordCount`roboczy na . Trzecie polecenie tworzy nowy katalog, `resources`który będzie używany później.  Katalog `WordCount` zawiera następujące elementy:

* `pom.xml`: Zawiera ustawienia projektu Maven.
* `src\main\java\com\microsoft\example`: Zawiera kod aplikacji.
* `src\test\java\com\microsoft\example`: Zawiera testy dla aplikacji.  

### <a name="remove-the-generated-example-code"></a>Usuwanie wygenerowanego przykładowego kodu

Usuń wygenerowane pliki `AppTest.java`testowe `App.java` i aplikacyjne oraz wprowadzając poniższe polecenia:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Dodawanie repozytoriów Maven

HDInsight jest oparty na Hortonworks Data Platform (HDP), dlatego zalecamy użycie repozytorium Hortonworks do pobierania zależności dla projektów Apache Storm.  

Otwórz, `pom.xml` wpisując poniższe polecenie:

```cmd
notepad pom.xml
```

Następnie dodaj następujący kod `<url>https://maven.apache.org</url>` XML za wierszem:

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
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
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
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Dodawanie właściwości

Maven umożliwia definiowanie wartości na poziomie projektu o nazwie właściwości. W `pom.xml`, dodaj następujący `</repositories>` tekst po wierszu:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Teraz można użyć tej wartości w `pom.xml`innych sekcjach . Na przykład podczas określania wersji składników Storm, `${storm.version}` można użyć zamiast twardego kodowania wartości.

## <a name="add-dependencies"></a>Dodawanie zależności

Dodaj zależność dla składników Storm. W `pom.xml`sekcji dodaj następujący `<dependencies>` tekst:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

W czasie kompilacji Maven używa tych `storm-core` informacji do wyszukiwania w repozytorium Maven. Najpierw wygląda w repozytorium na komputerze lokalnym. Jeśli plików nie ma, Maven pobiera je z publicznego repozytorium Maven i przechowuje je w lokalnym repozytorium.

> [!NOTE]  
> Zwróć `<scope>provided</scope>` uwagę na wiersz w tej sekcji. To ustawienie informuje Maven, aby wykluczyć **storm-core** z wszystkich plików JAR, które są tworzone, ponieważ jest dostarczany przez system.

## <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki Maven umożliwiają dostosowanie etapów budowy projektu. Na przykład sposób kompilowania projektu lub jak spakować go do pliku JAR. W `pom.xml`, dodaj następujący tekst `</project>` bezpośrednio nad wierszem.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Ta sekcja służy do dodawania wtyczek, zasobów i innych opcji konfiguracji kompilacji. Aby uzyskać pełne `pom.xml` odniesienie do [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html)pliku, zobacz .

### <a name="add-plug-ins"></a>Dodawanie wtyczek

* **Wtyczka Exec Maven**

    Dla topologii Apache Storm zaimplementowanych w Javie, [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/) jest przydatna, ponieważ pozwala łatwo uruchomić topologię lokalnie w środowisku programistycznym. Dodaj następujące `<plugins>` elementy do `pom.xml` sekcji pliku, aby dołączyć wtyczkę Exec Maven:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
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

* **Wtyczka kompilatora Apache Maven**

    Innym przydatnym plug-in jest [Apache Maven Kompilator Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/), który jest używany do zmiany opcji kompilacji. Zmień wersję języka Java, której Maven używa dla źródła i celu aplikacji.

  * W przypadku programu HDInsight __3.4 lub wcześniejszego__ustaw wersję źródłową i docelową w wersji Java na __1.7__.

  * Dla HDInsight __3.5__, ustawić źródło i docelową wersję Java na __1.8__.

  Dodaj następujący tekst `<plugins>` w sekcji `pom.xml` pliku, aby uwzględnić wtyczkę Apache Maven Compiler. W tym przykładzie określa 1.8, więc docelowa wersja HDInsight jest 3.5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Konfigurowanie zasobów

Sekcja zasobów umożliwia dołączenie zasobów innych niż kod, takich jak pliki konfiguracyjne wymagane przez składniki w topologii. W tym przykładzie dodaj następujący `<resources>` tekst `pom.xml` w sekcji pliku. Następnie zapisz i zamknij plik.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

W tym przykładzie dodano katalog zasobów w`${basedir}`katalogu głównym projektu ( ) jako `log4j2.xml`lokalizację zawierającą zasoby i zawierającą plik o nazwie . Ten plik służy do konfigurowania, jakie informacje są rejestrowane przez topologię.

## <a name="create-the-topology"></a>Tworzenie topologii

Topologia apache storm oparta na języku Java składa się z trzech składników, które należy autor (lub odwołanie) jako zależność.

* **Wylewki:** Odczytuje dane ze źródeł zewnętrznych i emituje strumienie danych do topologii.

* **Śruby:** Wykonuje przetwarzanie na strumieniach emitowanych przez wylewki lub inne śruby i emituje jeden lub więcej strumieni.

* **Topologia**: Definiuje sposób rozmieszczenia wylewek i śrub oraz zapewnia punkt wejścia dla topologii.

### <a name="create-the-spout"></a>Tworzenie dziobka

Aby zmniejszyć wymagania dotyczące konfigurowania zewnętrznych źródeł danych, następujący wylewka po prostu emituje losowe zdania. Jest to zmodyfikowana wersja dziobka, która jest dostarczana z [przykładami Storm-Starter.](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)  Mimo że ta topologia używa tylko jednego dziobka, inne mogą mieć kilka, które są źródłowe danych z różnych źródeł do topologii.

Wprowadź poniższe polecenie, aby utworzyć `RandomSentenceSpout.java`i otworzyć nowy plik:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Następnie skopiuj i wklej poniższy kod java do nowego pliku.  Następnie zamknij plik.

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
> Na przykład wylewka, która odczytuje z zewnętrznego źródła danych, zobacz jeden z następujących przykładów:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): przykład wylewka, która brzmi z Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): wylewka, która brzmi z Kafki.

### <a name="create-the-bolts"></a>Tworzenie śrub

Śruby obsługują przetwarzanie danych. Śruby mogą wykonywać wszystko, na przykład obliczanie, trwałość lub rozmawiać ze składnikami zewnętrznymi. Ta topologia wykorzystuje dwie śruby:

* **SplitSentence**: Dzieli zdania emitowane przez **RandomSentenceSpout** na poszczególne słowa.

* **WordCount**: Zlicza, ile razy wystąpiło każde słowo.

#### <a name="splitsentence"></a>SplitSentence (Podzielanie)

Wprowadź poniższe polecenie, aby utworzyć `SplitSentence.java`i otworzyć nowy plik:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Następnie skopiuj i wklej poniższy kod java do nowego pliku.  Następnie zamknij plik.

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

#### <a name="wordcount"></a>Wordcount

Wprowadź poniższe polecenie, aby utworzyć `WordCount.java`i otworzyć nowy plik:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Następnie skopiuj i wklej poniższy kod java do nowego pliku.  Następnie zamknij plik.

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

### <a name="define-the-topology"></a>Definiowanie topologii

Topologia łączy wylewki i śruby razem w wykres, który określa, jak przepływy danych między składnikami. Zapewnia również wskazówki równoległości, które storm używa podczas tworzenia wystąpień składników w klastrze.

Poniższa ilustracja przedstawia podstawowy diagram wykresu komponentów dla tej topologii.

![schemat przedstawiający rozstaw wylewek i śrub](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Aby zaimplementować topologię, wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik: `WordCountTopology.java`

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Następnie skopiuj i wklej poniższy kod java do nowego pliku.  Następnie zamknij plik.

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

Storm używa [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) do rejestrowania informacji. Jeśli rejestrowanie nie zostanie skonfigurowane, topologia emituje informacje diagnostyczne. Aby kontrolować to, co jest `log4j2.xml` rejestrowane, `resources` utwórz plik o nazwie w katalogu, wprowadzając poniższe polecenie:

```cmd
notepad resources\log4j2.xml
```

Następnie skopiuj i wklej tekst XML poniżej do nowego pliku.  Następnie zamknij plik.

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

Ten xml konfiguruje nowy `com.microsoft.example` rejestrator dla klasy, który zawiera składniki w tym przykładzie topologii. Poziom jest ustawiony na śledzenie dla tego rejestratora, który przechwytuje wszystkie informacje rejestrowania emitowane przez składniki w tej topologii.

Sekcja `<Root level="error">` konfiguruje poziom główny rejestrowania (wszystko nie w `com.microsoft.example`) tylko rejestrować informacje o błędzie.

Aby uzyskać więcej informacji na temat konfigurowania rejestrowania dla log4j 2, zobacz [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Storm w wersji 0.10.0 i nowszego zastosowania Log4j 2.x. Starsze wersje storm używane Log4j 1.x, który używany inny format konfiguracji dziennika. Aby uzyskać informacje na [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)temat starszej konfiguracji, zobacz .

## <a name="test-the-topology-locally"></a>Przetestuj topologię lokalnie

Po zapisaniu plików użyj następującego polecenia, aby przetestować topologię lokalnie.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Po uruchomieniu topologia wyświetla informacje o starcie. Poniższy tekst jest przykładem danych wyjściowych liczby wyrazów:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Ten przykładowy dziennik wskazuje, że słowo "i" zostało wyemitowane 113 razy. Liczba nadal iść w górę tak długo, jak topologia działa, ponieważ dziobek stale emituje te same zdania.

Istnieje 5-sekundowy odstęp między emisją słów i liczby. Składnik **WordCount** jest skonfigurowany do emitowania informacji tylko po nadejściu krotki zaznaczenia. Żąda, aby krotek zaznaczyć są dostarczane tylko co pięć sekund.

## <a name="convert-the-topology-to-flux"></a>Konwertowanie topologii na flux

[Flux](https://storm.apache.org/releases/2.0.0/flux.html) to nowa struktura dostępna z storm 0.10.0 lub nowszym, która pozwala na oddzielenie konfiguracji od implementacji. Składniki są nadal zdefiniowane w języku Java, ale topologia jest definiowana za pomocą pliku YAML. Domyślną definicję topologii można spakować z projektem lub użyć pliku autonomicznego podczas przesyłania topologii. Podczas przesyłania topologii do storm, można użyć zmiennych środowiskowych lub plików konfiguracyjnych do wypełniania wartości w definicji topologii YAML.

Plik YAML definiuje składniki, które mają być używane dla topologii i przepływu danych między nimi. Możesz dołączyć plik YAML jako część pliku jar lub użyć zewnętrznego pliku YAML.

Aby uzyskać więcej informacji na temat flux, zobacz [Ramy flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Z powodu [błęduhttps://issues.apache.org/jira/browse/STORM-2055) (](https://issues.apache.org/jira/browse/STORM-2055) z Storm 1.0.1 może być konieczne zainstalowanie [środowiska programistycznego Storm,](https://storm.apache.org/releases/current/Setting-up-development-environment.html) aby uruchomić topologie Flux lokalnie.

1. Wcześniej `WordCountTopology.java` zdefiniowano topologię, ale nie jest to potrzebne w u technologii Flux. Usuń plik za pomocą następującego polecenia:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Wprowadź poniższe polecenie, aby utworzyć `topology.yaml`i otworzyć nowy plik:

    ```cmd
    notepad resources\topology.yaml
    ```

    Następnie skopiuj i wklej poniższy tekst do nowego pliku.  Następnie zamknij plik.

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

1. Wprowadź poniższe polecenie, aby otworzyć, `pom.xml` aby wykonać opisane poprawki poniżej:

    ```cmd
    notepad pom.xml
    ```

   1. Dodaj następującą nową zależność w `<dependencies>` sekcji:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Dodaj następującą wtyczkę `<plugins>` do sekcji. Ta wtyczka obsługuje tworzenie pakietu (plik jar) dla projektu i stosuje pewne przekształcenia specyficzne dla Flux podczas tworzenia pakietu.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
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

   1. W sekcji Wtyczka Exec Maven `${storm.topology}` przejdź `org.apache.storm.flux.Flux`do `<configuration>`  >  `<mainClass>` . To ustawienie umożliwia flux do obsługi uruchamiania topologii lokalnie w rozwoju.

   1. W `<resources>` sekcji dodaj następujące `<includes>`elementy do . Ten kod XML zawiera plik YAML, który definiuje topologię jako część projektu.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Przetestuj topologię topologii strumienia lokalnie

1. Wprowadź następujące polecenie, aby skompilować i wykonać topologię fluxu za pomocą maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Jeśli topologia używa bitów Storm 1.0.1, to polecenie nie powiedzie się. Ta awaria [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)jest spowodowana przez . Zamiast tego [zainstaluj storm w środowisku deweloperskim](https://storm.apache.org/releases/current/Setting-up-development-environment.html) i wykonać następujące czynności:
    >
    > Jeśli [w środowisku programistycznym zainstalowano program Storm,](https://storm.apache.org/releases/current/Setting-up-development-environment.html)można użyć następujących poleceń:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    Parametr `--local` uruchamia topologię w trybie lokalnym w środowisku programistycznym. Parametr `-R /topology.yaml` używa zasobu `topology.yaml` pliku z pliku jar do zdefiniowania topologii.

    Po uruchomieniu topologia wyświetla informacje o starcie. Poniższy tekst jest przykładem danych wyjściowych:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Istnieje 10-sekundowe opóźnienie między partiami zarejestrowanych informacji.

2. Utwórz nową topologię yaml z projektu.

    1. Wprowadź poniższe polecenie, aby otworzyć: `topology.xml`

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Znajdź następującą sekcję i `10` `5`zmień wartość na . Ta modyfikacja zmienia interwał między emituje partie zliczeń wyrazów z 10 sekund do 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Zapisz plik `newtopology.yaml`jako .

3. Aby uruchomić topologię, wprowadź następujące polecenie:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Lub, jeśli masz Storm w środowisku programistycznym:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    To polecenie używa `newtopology.yaml` jako definicji topologii. Ponieważ nie uwzględniliśmy `compile` parametru, Maven używa wersji projektu zbudowanej w poprzednich krokach.

    Po uruchomieniu topologii należy zauważyć, że czas między emitowanymi partiami uległ `newtopology.yaml`zmianie, aby odzwierciedlić wartość w . Możesz więc zobaczyć, że możesz zmienić konfigurację za pomocą pliku YAML bez konieczności ponownej kompilowanie topologii.

Aby uzyskać więcej informacji na temat tych i innych funkcji struktury Flux, zobacz [Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) to abstrakcja wysokiego poziomu, która jest dostarczana przez Storm. Obsługuje przetwarzanie stanowe. Główną zaletą Trident jest to, że może zagwarantować, że każda wiadomość, która wchodzi do topologii jest przetwarzany tylko raz. Bez użycia Trident, topologia może tylko zagwarantować, że wiadomości są przetwarzane co najmniej raz. Istnieją również inne różnice, takie jak wbudowane komponenty, które mogą być używane zamiast tworzenia śrub. W rzeczywistości śruby są zastępowane przez mniej ogólne składniki, takie jak filtry, rzuty i funkcje.

Aplikacje Trident można tworzyć przy użyciu projektów Maven. Używasz tych samych podstawowych kroków, które zostały przedstawione wcześniej w tym artykule — tylko kod jest inny. Trident również nie może (obecnie) być używane z ramą Flux.

Aby uzyskać więcej informacji na temat trident, zobacz [Trident API Overview](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Następne kroki

Nauczyłeś się, jak utworzyć topologię Apache Storm za pomocą oprogramowania Java. Teraz dowiedz się, jak:

* [Wdrażanie topologii Apache Storm i zarządzanie nimi w programie HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Tworzenie topologii języka C# dla usługi Apache Storm w programie HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Możesz znaleźć więcej przykładów Apache Storm topologie odwiedzając [przykład topologie apache Storm na HDInsight](apache-storm-example-topology.md).
