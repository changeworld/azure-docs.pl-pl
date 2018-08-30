---
title: 'Samouczek: korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka — Azure HDInsight '
description: Informacje o sposobie korzystania z interfejsów API producentów i odbiorców platformy Apache Kafka w usłudze HDInsight. W tym samouczku dowiesz się, jak używać tych interfejsów API na platformie Kafka w usłudze HDInsight z poziomu aplikacji Java.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/16/2018
ms.openlocfilehash: c52f64c2508870bf061e144229cf26ab269c343b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049240"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Samouczek: korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka

Informacje o sposobie korzystania z interfejsów API producentów i odbiorców platformy Kafka w usłudze HDInsight.

Interfejs API producenta platformy Kafka umożliwia aplikacjom wysyłanie strumieni danych do klastra Kafka. Interfejs API odbiorcy platformy Kafka umożliwia aplikacjom odczytywanie strumieni danych z klastra.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska projektowego
> * Konfigurowanie środowiska wdrażania
> * Zrozumienie kodu
> * Kompilowanie i wdrażanie aplikacji
> * Uruchamianie aplikacji w klastrze

Aby uzyskać więcej informacji o tych interfejsach API, zobacz dokumentację platformy Apache dotyczącą [interfejsu API producenta](https://kafka.apache.org/documentation/#producerapi) i [interfejsu API odbiorcy](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Następujące składniki muszą być zainstalowane w środowisku deweloperskim:

* [Zestaw Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) lub równoważny, taki jak OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH i polecenie `scp`. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Edytor tekstu lub środowisko projektowe Java.

Po zainstalowaniu środowiska Java i zestawu JDK na deweloperskiej stacji roboczej mogą zostać ustawione następujące zmienne środowiskowe. Należy jednak sprawdzić, czy te zmienne istnieją i czy zawierają poprawne wartości dla systemu.

* `JAVA_HOME` — powinna wskazywać katalog, w którym jest zainstalowany zestaw JDK.
* `PATH` — powinna zawierać następujące ścieżki:
  
    * `JAVA_HOME` (lub równoważną ścieżkę).
    * `JAVA_HOME\bin` (lub równoważną ścieżkę).
    * Katalog, w którym zainstalowano narzędzie Maven.

## <a name="set-up-your-deployment-environment"></a>Konfigurowanie środowiska wdrażania

Ten samouczek wymaga platformy Kafka w usłudze HDInsight 3.6. Aby dowiedzieć się, jak utworzyć platformę Kafka w klastrze usługi HDInsight, zobacz dokument [Wprowadzenie do platformy Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Zrozumienie kodu

Przykładowa aplikacja znajduje się pod adresem [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) w podkatalogu `Producer-Consumer`. Ta aplikacja składa się zasadniczo z czterech plików:

* `pom.xml`: w tym pliku są definiowane zależności projektu, wersja języka Java i metody pakowania.
* `Producer.java`: ten plik wysyła 1 milion (1 000 000) losowych zdań do platformy Kafka przy użyciu interfejsu API producenta.
* `Consumer.java`: ten plik korzysta z interfejsu API odbiorcy do odczytywania danych z platformy Kafka i przekazywania ich do wyjścia STDOUT.
* `Run.java`: interfejs wiersza polecenia używany do uruchamiania kodu producenta i odbiorcy.

### <a name="pomxml"></a>Pom.xml

Należy zrozumieć następujące ważne kwestie dotyczące pliku `pom.xml`:

* Zależności: ten projekt bazuje na interfejsach API producenta i odbiorcy platformy Kafka, które są udostępniane w pakiecie `kafka-clients`. Ta zależność jest definiowana przez następujący kod XML:

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

    * `maven-compiler-plugin`: służy do ustawiania wersji 8 języka Java używanej przez projekt. Jest to wersja języka Java używana przez usługę HDInsight 3.6.
    * `maven-shade-plugin`: służy do generowania pełnego pliku jar zawierającego tę aplikację, a także wszelkie zależności. Jest ona również używana do ustawiania punktu wejścia aplikacji, dzięki czemu można bezpośrednio uruchamiać plik Jar bez konieczności określania klasy głównej.

### <a name="producerjava"></a>Producer.java

Producent komunikuje się z hostami brokera platformy Kafka (węzłami procesu roboczego) na potrzeby przechowywania danych w temacie platformy Kafka. Poniższy fragment kodu pochodzi z pliku `Producer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

Ten kod łączy się z hostami brokera platformy Kafka (węzłami procesu roboczego), a następnie wysyła 1 000 000 zdań na platformę Kafka przy użyciu interfejsu API producenta.

### <a name="consumerjava"></a>Consumer.java

Odbiorca komunikuje się z hostami brokera platformy Kafka (węzłami procesu roboczego) i odczytuje rekordy w pętli. Poniższy fragment kodu pochodzi z pliku `Consumer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
        KafkaConsumer<String, String> consumer;
        // Configure the consumer
        Properties properties = new Properties();
        // Point it to the brokers
        properties.setProperty("bootstrap.servers", brokers);
        // Set the consumer group (all consumers must belong to a group).
        properties.setProperty("group.id", groupId);
        // Set how to serialize key/value pairs
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        // When a group is first created, it has no offset stored to start reading from. This tells it to start
        // with the earliest record in the stream.
        properties.setProperty("auto.offset.reset","earliest");
        consumer = new KafkaConsumer<>(properties);

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

W tym kodzie odbiorca jest skonfigurowany do odczytywania od początku tematu (właściwość `auto.offset.reset` jest ustawiona na wartość `earliest`).

### <a name="runjava"></a>Run.java

Plik `Run.java` udostępnia interfejs wiersza polecenia uruchamiający kod producenta lub odbiorcy. Jako parametr należy podać informacje o hoście brokera platformy Kafka. Opcjonalnie można dołączyć wartość identyfikatora grupy, który jest używany przez proces odbiorcy. Jeśli tworzysz wiele wystąpień odbiorcy za pomocą tego samego identyfikatora grupy, obciążenie operacjami odczytu z tematu będzie równoważone.

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

## <a name="build-and-deploy-the-example"></a>Kompilowanie i wdrażanie przykładu

1. Pobierz przykłady z witryny [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Przejdź do katalogu `Producer-Consumer` i użyj następującego polecenia:

    ```
    mvn clean package
    ```

    To polecenie tworzy katalog o nazwie `target`, który zawiera plik o nazwie `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Aby skopiować plik `kafka-producer-consumer-1.0-SNAPSHOT.jar` do klastra usługi HDInsight, użyj następujących poleceń:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Zamień ciąg **SSHUSER** na nazwę użytkownika SSH klastra i zamień ciąg **CLUSTERNAME** na nazwę klastra. Po wyświetleniu monitu wprowadź hasło użytkownika SSH.

## <a id="run"></a> Uruchamianie przykładu

1. Aby otworzyć połączenie SSH do klastra, użyj następującego polecenia:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zamień ciąg **SSHUSER** na nazwę użytkownika SSH klastra i zamień ciąg **CLUSTERNAME** na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH. Aby uzyskać więcej informacji na temat używania polecenia `scp` w usłudze HDInsight, zobacz [Korzystanie z protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Wykonaj następujące czynności, aby utworzyć tematy platformy Kafka, które są używane w tym przykładzie:

    1. Aby zapisać nazwę klastra w zmiennej i zainstalować narzędzie do analizy JSON (`jq`), użyj następujących poleceń. Po wyświetleniu monitu wprowadź nazwę klastra platformy Kafka:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Aby uzyskać hosty brokera platformy Kafka i hosty usługi ZooKeeper, użyj następujących poleceń. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Aby utworzyć temat `test`, użyj następującego polecenia:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```
    4. W celu utworzenia tematu można również użyć pliku jar. Na przykład, aby utworzyć temat `test2`, użyj następującego polecenia:

        ```bash
        java -jar kafka-producer-consumer.jar create test2 $KAFKABROKERS
        ```

3. Aby uruchomić producenta i zapisać dane w temacie, użyj następującego polecenia:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. Po zakończeniu procesu producenta odczytaj rekordy z tematu za pomocą następującego polecenia:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```
   
    Zostanie wyświetlona liczba odczytanych rekordów wraz z liczbą rekordów.

5. Użyj klawiszy __Ctrl+C__, aby zakończyć działanie odbiorcy.

### <a name="multiple-consumers"></a>Wielu odbiorców

Odbiorcy platformy Kafka używają grupy odbiorców podczas odczytywania rekordów. Korzystanie z tej samej grupy przez wielu odbiorców umożliwia równoważenie obciążenia podczas przeprowadzania odczytu z tematu. Każdy odbiorca w grupie odbiera część rekordów.

Aplikacja odbiorcy akceptuje parametr, który jest używany jako identyfikator grupy. Na przykład następujące polecenie uruchamia odbiorcę przy użyciu identyfikatora grupy `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Aby zobaczyć, jak działa ten proces, użyj następującego polecenia:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

To polecenie używa polecenia `tmux`, aby podzielić terminal na dwie kolumny. W każdej kolumnie jest uruchamiany odbiorca z tą samą wartością identyfikatora grupy. Kiedy odbiorcy zakończą odczytywanie, można zauważyć, że każdy z nich odczytał tylko część rekordów. Użyj klawiszy __Ctrl + C __ dwa razy, aby zakończyć polecenie `tmux`.

Użycie przez klientów w tej samej grupie jest obsługiwane przez partycje tematu. Utworzony wcześniej temat `test` ma osiem partycji. Jeśli zostanie uruchomionych ośmiu odbiorców, każdy z nich będzie odczytywał rekordy z jednej partycji tematu.

> [!IMPORTANT]
> Grupa odbiorców nie może zawierać więcej wystąpień odbiorców niż partycji. W tym przykładzie jedna grupa odbiorców może zawierać maksymalnie ośmiu odbiorców, ponieważ tyle partycji znajduje się w temacie. Może też istnieć wiele grup odbiorców — każda z nich może zawierać maksymalnie ośmiu odbiorców.

Rekordy na platformie Kafka są przechowywane w kolejności, w której zostały odebrane na partycji. Aby dostarczać rekordy *na partycji* w określonej kolejności, utwórz grupę odbiorców, w której liczba wystąpień odbiorców jest zgodna z liczbą partycji. Aby dostarczać rekordy *w temacie* w określonej kolejności, utwórz grupę odbiorców z jednym wystąpieniem odbiorcy.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie zawarto informacje o sposobie korzystania z interfejsu API producenta i odbiorcy platformy Kafka w usłudze HDInsight. Dowiedz się więcej o pracy z platformą Kafka, korzystając z następujących zasobów:

* [Analizowanie dzienników Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicate data between Kafka clusters (Replikowanie danych między klastrami Kafka)](apache-kafka-mirroring.md)
* [Kafka Streams API with HDInsight](apache-kafka-streams-api.md) (Interfejs API strumieni platformy Kafka w usłudze HDInsight)
