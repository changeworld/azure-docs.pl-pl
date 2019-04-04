---
title: 'Samouczek: Korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka — Azure HDInsight '
description: Informacje o sposobie korzystania z interfejsów API producentów i odbiorców platformy Apache Kafka w usłudze HDInsight. W tym samouczku dowiesz się, jak używać tych interfejsów API na platformie Kafka w usłudze HDInsight z poziomu aplikacji Java.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 6b77cd9939e244fd031788164cdfe391c3e2b9d5
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916397"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Samouczek: Korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka

Informacje o sposobie korzystania z interfejsów API producentów i odbiorców platformy Apache Kafka w usłudze HDInsight.

Interfejs API producenta platformy Kafka umożliwia aplikacjom wysyłanie strumieni danych do klastra Kafka. Interfejs API odbiorcy platformy Kafka umożliwia aplikacjom odczytywanie strumieni danych z klastra.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wymagania wstępne
> * Zrozumienie kodu
> * Kompilowanie i wdrażanie aplikacji
> * Uruchamianie aplikacji w klastrze

Aby uzyskać więcej informacji o tych interfejsach API, zobacz dokumentację platformy Apache dotyczącą [interfejsu API producenta](https://kafka.apache.org/documentation/#producerapi) i [interfejsu API odbiorcy](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Wymagania wstępne

* Platforma Apache Kafka w HDInsight 3.6. Aby dowiedzieć się, jak utworzyć platformy Kafka w klastrze HDInsight, zobacz [rozpoczynać platformy Apache Kafka w HDInsight](apache-kafka-get-started.md).

* [Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks) lub równoważny, taki jak OpenJDK.

* [Narzędzia Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowane](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to projekt system kompilacji dla projektów Java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Zrozumienie kodu

Przykładowa aplikacja znajduje się pod adresem [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) w podkatalogu `Producer-Consumer`. Ta aplikacja składa się zasadniczo z czterech plików:

* `pom.xml`: w tym pliku są definiowane zależności projektu, wersja języka Java i metody pakowania.
* `Producer.java`: ten plik wysyła losowe zdania do platformy Kafka przy użyciu interfejsu API producenta.
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

Producent komunikuje się z hostami brokera platformy Kafka (węzłami procesu roboczego) i wysyła dane do tematu platformy Kafka. Poniższy fragment kodu jest z [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) plik wchodzącej w skład [repozytorium GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) i przedstawiono sposób ustawiania właściwości producenta:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Odbiorca komunikuje się z hostami brokera platformy Kafka (węzłami procesu roboczego) i odczytuje rekordy w pętli. Poniższy fragment kodu z pliku [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) ustawia właściwości odbiorcy:

```java
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
```

W tym kodzie odbiorca jest skonfigurowany do odczytywania od początku tematu (właściwość `auto.offset.reset` jest ustawiona na wartość `earliest`).

### <a name="runjava"></a>Run.java

[Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) plik zawiera interfejs wiersza polecenia, która uruchamia kod producenta lub konsumenta. Jako parametr należy podać informacje o hoście brokera platformy Kafka. Możesz opcjonalnie dołączyć wartość Identyfikatora grupy, który jest używany przez proces konsumenta. Jeśli tworzysz wiele wystąpień odbiorcy, przy użyciu tego samego Identyfikatora grupy, załaduje saldo odczytu z tematu.

## <a name="build-and-deploy-the-example"></a>Kompilowanie i wdrażanie przykładu

1. Pobierz i Wyodrębnij przykłady z [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Ustaw bieżący katalog na lokalizację `hdinsight-kafka-java-get-started\Producer-Consumer` katalogu i użyj następującego polecenia:

    ```cmd
    mvn clean package
    ```

    To polecenie tworzy katalog o nazwie `target`, który zawiera plik o nazwie `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Wprowadź następujące polecenie, aby skopiować `kafka-producer-consumer-1.0-SNAPSHOT.jar` plików do klastra usługi HDInsight. Po wyświetleniu monitu wprowadź hasło użytkownika SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> Uruchamianie przykładu

1. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Otwórz połączenie SSH z klastrem, wprowadzając następujące polecenie. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Zainstaluj [jq](https://stedolan.github.io/jq/), wiersza polecenia procesora w formacie JSON. Z otwartego połączenia SSH, wprowadź następujące polecenie, aby zainstalować `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Konfigurowanie zmiennych środowiskowych. Zastąp `PASSWORD` i `CLUSTERNAME` hasło logowania klastra i klastra odpowiednio nazwę, a następnie wpisz polecenie:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Wyodrębnij nazwy klastra poprawnie z uwzględnieniem wielkości liter. Rzeczywiste wielkość liter w wyrazie nazwy klastra mogą być inne, niż jest to oczekiwane, w zależności od sposobu utworzenia klastra. To polecenie uzyskać rzeczywiste wielkość liter w wyrazie, zapisz go w zmiennej i następnie poprawnie cased nazwę wyświetlaną i nazwę, którą podano wcześniej. Wprowadź następujące polecenie:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Aby uzyskać brokera platformy Kafka, hostów i hosty Apache Zookeeper, użyj następującego polecenia:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

6. Utworzenie tematu platformy Kafka `myTest`, wprowadzając następujące polecenie:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

7. Aby uruchomić producenta i zapisać dane w temacie, użyj następującego polecenia:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

8. Po zakończeniu procesu producenta odczytaj rekordy z tematu za pomocą następującego polecenia:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    Zostanie wyświetlona liczba odczytanych rekordów wraz z liczbą rekordów.

9. Użyj klawiszy __Ctrl+C__, aby zakończyć działanie odbiorcy.

### <a name="multiple-consumers"></a>Wielu odbiorców

Odbiorcy platformy Kafka używają grupy odbiorców podczas odczytywania rekordów. Korzystanie z tej samej grupy przez wielu odbiorców umożliwia równoważenie obciążenia podczas przeprowadzania odczytu z tematu. Każdy odbiorca w grupie odbiera część rekordów.

Aplikacja odbiorcy akceptuje parametr, który jest używany jako identyfikator grupy. Na przykład następujące polecenie uruchamia odbiorcę przy użyciu identyfikatora grupy `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Użyj klawiszy __Ctrl+C__, aby zakończyć działanie odbiorcy.

Aby zobaczyć, jak działa ten proces, użyj następującego polecenia:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

To polecenie używa polecenia `tmux`, aby podzielić terminal na dwie kolumny. W każdej kolumnie jest uruchamiany odbiorca z tą samą wartością identyfikatora grupy. Kiedy odbiorcy zakończą odczytywanie, można zauważyć, że każdy z nich odczytał tylko część rekordów. Użyj __klawisze Ctrl + C__ dwa razy, aby zakończyć działanie `tmux`.

Użycie przez klientów w tej samej grupie jest obsługiwane przez partycje tematu. W tym przykładowym kodzie utworzony wcześniej temat `test` ma osiem partycji. Jeśli zostanie uruchomionych ośmiu odbiorców, każdy z nich będzie odczytywał rekordy z jednej partycji tematu.

> [!IMPORTANT]  
> Grupa odbiorców nie może zawierać więcej wystąpień odbiorców niż partycji. W tym przykładzie jedna grupa odbiorców może zawierać maksymalnie ośmiu odbiorców, ponieważ tyle partycji znajduje się w temacie. Może też istnieć wiele grup odbiorców — każda z nich może zawierać maksymalnie ośmiu odbiorców.

Rekordy na platformie Kafka są przechowywane w kolejności, w której zostały odebrane na partycji. Aby dostarczać rekordy *na partycji* w określonej kolejności, utwórz grupę odbiorców, w której liczba wystąpień odbiorców jest zgodna z liczbą partycji. Aby dostarczać rekordy *w temacie* w określonej kolejności, utwórz grupę odbiorców z jednym wystąpieniem odbiorcy.

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie zawarto informacje o sposobie korzystania z interfejsu API producenta i odbiorcy platformy Apache Kafka w usłudze HDInsight. Dowiedz się więcej o pracy z platformą Kafka, korzystając z następujących zasobów:

* [Analizowanie dzienników platformy Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikuj dane między klastrami platformy Apache Kafka](apache-kafka-mirroring.md)
* [Interfejs API za pomocą HDInsight strumieni platformy Apache Kafka](apache-kafka-streams-api.md)
