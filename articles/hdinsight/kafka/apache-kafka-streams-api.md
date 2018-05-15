---
title: 'Samouczek: korzystanie z interfejsu API strumieni platformy Apache Kafka — Azure HDInsight | Microsoft Docs'
description: Informacje o sposobie korzystania z interfejsu API strumieni platformy Apache Kafka w usłudze HDInsight. Ten interfejs API umożliwia przetwarzanie strumieni między tematami na platformie Kafka.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2018
ms.author: larryfr
ms.openlocfilehash: 8aff28079a0aaa7c02d8a187cb379ecdbedcd854
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-apache-kafka-streams-api"></a>Samouczek: interfejs API strumieni platformy Apache Kafka

Dowiedz się, jak utworzyć aplikację, która używa interfejsu API strumieni platformy Kafka, i uruchomić ją na platformie Kafka w usłudze HDInsight. 

Aplikacja przedstawiona w tym samouczku zlicza przesyłane strumieniowo wyrazy. Odczytuje ona dane tekstowe z tematu platformy Kafka, wyodrębnia poszczególne wyrazy, a następnie zapisuje liczbę wyrazów w innym temacie platformy Kafka.

> [!NOTE]
> Przetwarzanie strumienia platformy Kafka jest często wykonywane przy użyciu platformy Apache Spark lub systemu Storm. Interfejs API strumieni platformy Kafka został wprowadzony na platformie Kafka w wersji 0.10.0 (w usłudze HDInsight 3.5 i 3.6). Ten interfejs API umożliwia przekształcanie strumieni danych między tematami wejściowymi i wyjściowymi. W niektórych przypadkach może to być alternatywą dla tworzenia rozwiązań przesyłania strumieniowego platformy Spark lub systemu Storm. 
>
> Aby uzyskać więcej informacji o strumieniach platformy Kafka, zobacz dokumentację [Intro to Streams](https://kafka.apache.org/10/documentation/streams/) (Wprowadzenie do strumieni) w serwisie Apache.org.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska projektowego
> * Zrozumienie kodu
> * Kompilowanie i wdrażanie aplikacji
> * Konfigurowanie tematów platformy Kafka
> * Uruchamianie kodu

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Kafka w usłudze HDInsight 3.6. Aby dowiedzieć się, jak utworzyć platformę Kafka w klastrze usługi HDInsight, zobacz dokument [Wprowadzenie do platformy Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md).

* Wykonaj kroki opisane w dokumencie [Kafka Consumer and Producer API](apache-kafka-producer-consumer-api.md) (Interfejs API odbiorcy i producenta na platformie Kafka). Czynności opisane w tym dokumencie bazują na przykładowej aplikacji i tematach utworzonych w tym samouczku.

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Następujące składniki muszą być zainstalowane w środowisku deweloperskim:

* [Zestaw Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) lub równoważny, taki jak OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH i polecenie `scp`. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Zrozumienie kodu

Przykładowa aplikacja znajduje się pod adresem [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) w podkatalogu `Streaming`. Aplikacja składa się z dwóch plików:

* `pom.xml`: w tym pliku są definiowane zależności projektu, wersja języka Java i metody pakowania.
* `Stream.java`: ten plik implementuje logikę przesyłania strumieniowego.

### <a name="pomxml"></a>Pom.xml

Należy zrozumieć następujące ważne kwestie dotyczące pliku `pom.xml`:

* Zależności: ten projekt bazuje na interfejsie API strumieni platformy Kafka, który jest udostępniany w pakiecie `kafka-clients`. Ta zależność jest definiowana przez następujący kod XML:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > Wpis `${kafka.version}` jest zadeklarowany w sekcji `<properties>..</properties>` pliku `pom.xml` i jest skonfigurowany zgodnie z wersją platformy Kafka znajdującą się w klastrze usługi HDInsight.

* Wtyczki: wtyczki Maven zapewniają różne możliwości. W tym projekcie są używane następujące wtyczki:

    * `maven-compiler-plugin`: służy do ustawiania wersji 8 języka Java używanej przez projekt. Język Java 8 jest wymagany przez usługę HDInsight 3.6.
    * `maven-shade-plugin`: służy do generowania pełnego pliku jar zawierającego tę aplikację, a także wszelkie zależności. Jest ona również używana do ustawiania punktu wejścia aplikacji, dzięki czemu można bezpośrednio uruchamiać plik Jar bez konieczności określania klasy głównej.

### <a name="streamjava"></a>Stream.java

Plik `Stream.java` używa interfejsu API strumienia do zaimplementowania aplikacji liczącej wyrazy. Odczytuje on dane z tematu Kafka o nazwie `test` i zapisuje liczbę wyrazów w temacie o nazwie `wordcounts`.

Poniższy kod definiuje aplikację do zliczania wyrazów:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>Kompilowanie i wdrażanie przykładu

Aby skompilować i wdrożyć projekt na platformie Kafka w klastrze usługi HDInsight, wykonaj następujące kroki:

1. Pobierz przykłady z witryny [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Przejdź do katalogu `Streaming` i użyj następującego polecenia, aby utworzyć pakiet jar:

    ```bash
    mvn clean package
    ```

    To polecenie tworzy pakiet w lokalizacji `target/kafka-streaming-1.0-SNAPSHOT.jar`.

3. Aby skopiować plik `kafka-streaming-1.0-SNAPSHOT.jar` do klastra usługi HDInsight, użyj następującego polecenia:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `clustername` na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH. Aby uzyskać więcej informacji na temat używania polecenia `scp` w usłudze HDInsight, zobacz [Korzystanie z protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-kafka-topics"></a>Tworzenie tematów platformy Kafka

1. Aby otworzyć połączenie SSH do klastra, użyj następującego polecenia:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `clustername` na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH. Aby uzyskać więcej informacji na temat używania polecenia `scp` w usłudze HDInsight, zobacz [Korzystanie z protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Aby zapisać nazwę klastra w zmiennej i zainstalować narzędzie do analizy JSON (`jq`), użyj następujących poleceń. Po wyświetleniu monitu wprowadź nazwę klastra platformy Kafka:

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. Aby uzyskać hosty brokera platformy Kafka i hosty usługi ZooKeeper, użyj następujących poleceń. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora). Monit o hasło zostanie wyświetlony dwa razy.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. Aby utworzyć tematy używane przez operację przesyłania strumieniowego, użyj następujących poleceń:

    > [!NOTE]
    > Może zostać wyświetlony błąd z informacją, że temat `test` już istnieje. Nie stanowi to problemu, ponieważ ten temat mógł zostać utworzony w samouczku dotyczącym interfejsu API producenta i odbiorcy.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Tematy są używane do następujących celów:

    * `test`: w tym temacie są odbierane rekordy. Aplikacja do przesyłania strumieniowego odczytuje dane z tego tematu.
    * `wordcounts`: w tym temacie aplikacja do przesyłania strumieniowego przechowuje swoje dane wyjściowe.
    * `RekeyedIntermediateTopic`: w tym temacie zachodzi ponowne dzielenie danych, ponieważ liczba wyrazów jest aktualizowana za pomocą operatora `countByKey`.
    * `wordcount-example-Counts-changelog`: ten temat jest magazynem stanów używanym przez operację `countByKey`

    > [!IMPORTANT]
    > Platformę Kafka w usłudze HDInsight można również skonfigurować w taki sposób, aby automatycznie tworzyła tematy. Aby uzyskać więcej informacji, zobacz dokument [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Konfigurowanie automatycznego tworzenia tematów).

## <a name="run-the-code"></a>Uruchamianie kodu

1. Aby uruchomić aplikację do przesyłania strumieniowego jako proces w tle, użyj następującego polecenia:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]
    > Może zostać wyświetlone ostrzeżenie dotyczące mechanizmu log4j. Możne je zignorować.

2. Aby wysyłać rekordy do tematu `test`, użyj następującego polecenia w celu uruchomienia aplikacji producenta:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

3. Po zakończeniu działania producenta użyj następującego polecenia, aby wyświetlić informacje przechowywane w temacie `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]
    > Dzięki użyciu parametrów `--property` odbiorca konsoli drukuje zarówno klucz (wyraz), jak i liczbę (wartość). Ten parametr konfiguruje również deserializatora do użycia podczas odczytu tych wartości z platformy Kafka.

    Dane wyjściowe będą podobne do następującego tekstu:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > Parametr `--from-beggining` konfiguruje odbiorcę, aby rozpoczął przetwarzanie od początku rekordów przechowywanych w temacie. Liczba wystąpień zwiększa się każdorazowo po napotkaniu wyrazu, dlatego temat zawiera wiele pozycji dla każdego wyrazu ze zwiększającą się liczbą wystąpień.

7. Użyj klawiszy __Ctrl + C__, aby zakończyć działanie producenta. Podobnie użyj klawiszy __Ctrl + C__, aby zakończyć działanie aplikacji i odbiorcy.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie zawarto informacje o sposobie korzystania z interfejsu API strumieni platformy Kafka w usłudze HDInsight. Dowiedz się więcej o pracy z platformą Kafka, korzystając z następujących zasobów:

* [Analizowanie dzienników Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicate data between Kafka clusters (Replikowanie danych między klastrami Kafka)](apache-kafka-mirroring.md)
