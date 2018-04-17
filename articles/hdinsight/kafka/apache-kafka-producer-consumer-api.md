---
title: Użyj Apache Kafka producentów i interfejsów API klienta — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z Kafka w usłudze HDInsight Apache Kafka producentów i interfejsów API klienta. Te interfejsy API umożliwiają tworzenie aplikacji, które zapisywania i odczytywania z Apache Kafka.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Producent Kafka Apache i interfejsów API klienta

Dowiedz się, jak utworzyć aplikację, która używa Kafka producentów i interfejsów API klienta z Kafka w usłudze HDInsight.

Dokumentacja interfejsów API, zobacz [API producent](https://kafka.apache.org/documentation/#producerapi) i [konsumenta interfejsu API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Następujące składniki muszą być zainstalowane w środowisku deweloperskim:

* [Zestaw Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) lub równoważny, taki jak OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH i polecenie `scp`. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Konfigurowanie środowiska wdrażania

W tym przykładzie wymaga Kafka na 3,6 HDInsight. Aby dowiedzieć się, jak utworzyć Kafka w klastrze usługi HDInsight, zobacz [rozpoczynać Kafka w usłudze HDInsight](apache-kafka-get-started.md) dokumentu.

## <a name="build-and-deploy-the-example"></a>Tworzenie i wdrażanie przykładu

1. Pobierz przykłady z [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Zmień katalogi na lokalizację `Producer-Consumer` katalogu i użyj następującego polecenia:

    ```
    mvn clean package
    ```

    To polecenie tworzy katalog o nazwie `target`, który zawiera plik o nazwie `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Aby skopiować plik `kafka-producer-consumer-1.0-SNAPSHOT.jar` do klastra usługi HDInsight, użyj następujących poleceń:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Zamień ciąg **SSHUSER** na nazwę użytkownika SSH klastra i zamień ciąg **CLUSTERNAME** na nazwę klastra. Po wyświetleniu monitu wprowadź hasło użytkownika SSH.

## <a id="run"></a> Uruchomić przykład

1. Aby otworzyć połączenie SSH do klastra, użyj następującego polecenia:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zamień ciąg **SSHUSER** na nazwę użytkownika SSH klastra i zamień ciąg **CLUSTERNAME** na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło dla konta użytkownika SSH. Aby uzyskać więcej informacji na temat używania `scp` z usługą HDInsight, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Aby utworzyć tematy, które są używane w tym przykładzie Kafka, użyj następujących poleceń:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Zastąp __nazwa klastra__ o nazwie z klastrem usługi HDInsight. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra usługi HDInsight.

    > [!NOTE]
    > Jeśli logowania do klastra jest inna niż domyślna wartość `admin`, Zastąp `admin` wartości w powyższych poleceń nazwy logowania klastra.

3. Aby uruchomić producenta i zapisywania danych do tematu, użyj następującego polecenia:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Po zakończeniu producenta, użyj następującego polecenia do odczytu z tematu:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Zostanie wyświetlona liczba odczytanych rekordów wraz z liczbą rekordów.

5. Użyj klawiszy __Ctrl+C__, aby zakończyć działanie odbiorcy.

### <a name="multiple-consumers"></a>Wielu odbiorców

Odbiorcy platformy Kafka używają grupy odbiorców podczas odczytywania rekordów. Korzystanie z tej samej grupy przez wielu odbiorców umożliwia równoważenie obciążenia podczas przeprowadzania odczytu z tematu. Każdy odbiorca w grupie odbiera część rekordów.

Aplikacja użytkownika akceptuje parametr, który jest używany jako identyfikatora grupy. Na przykład następujące polecenie uruchamia konsumenta przy użyciu Identyfikatora grupy `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Aby wyświetlić ten proces w akcji, wykonaj następujące czynności

1. Powtórz kroki 1 i 2 w [uruchomić przykład](#run) sekcji, aby otworzyć nową sesję SSH.

2. W obu sesji SSH wprowadź następujące polecenie:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Wprowadź oba polecenia równocześnie, tak, aby obie działają w tym samym czasie.

    Zwróć uwagę, że aktualna liczba wiadomości do odczytu nie jest taka sama jak w poprzedniej sekcji, gdy miały tylko jednego użytkownika. Zamiast tego odczytu wiadomości jest podzielony między wystąpieniami.

Użycie przez klientów w tej samej grupie jest obsługiwane przez partycje tematu. Utworzony wcześniej temat `test` ma osiem partycji. Jeśli otworzysz osiem sesji SSH i uruchomisz odbiorcę we wszystkich sesjach, każdy odbiorca odczyta rekordy z jednej partycji tematu.

> [!IMPORTANT]
> Grupa odbiorców nie może zawierać więcej wystąpień odbiorców niż partycji. W tym przykładzie jedna grupa odbiorców może zawierać maksymalnie ośmiu odbiorców, ponieważ tyle partycji znajduje się w temacie. Może też istnieć wiele grup odbiorców — każda z nich może zawierać maksymalnie ośmiu odbiorców.

Rekordy na platformie Kafka są przechowywane w kolejności, w której zostały odebrane na partycji. Aby dostarczać rekordy *na partycji* w określonej kolejności, utwórz grupę odbiorców, w której liczba wystąpień odbiorców jest zgodna z liczbą partycji. Aby dostarczać rekordy *w temacie* w określonej kolejności, utwórz grupę odbiorców z jednym wystąpieniem odbiorcy.

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób korzystania z Kafka w usłudze HDInsight Kafka producentów i interfejsu API klienta. Dowiedz się więcej o pracy z platformą Kafka, korzystając z następujących zasobów:

* [Analizowanie dzienników Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicate data between Kafka clusters (Replikowanie danych między klastrami Kafka)](apache-kafka-mirroring.md)
* [Kafka Streams API with HDInsight](apache-kafka-streams-api.md) (Interfejs API strumieni platformy Kafka w usłudze HDInsight)
* [Use Apache Spark streaming (DStream) with Kafka on HDInsight (Używanie strumieni (DStream) platformy Apache Spark z platformą Kafka w usłudze HDInsight)](../hdinsight-apache-spark-with-kafka.md)
* [Use Apache Spark Structured Streaming with Kafka on HDInsight (Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight)](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Use Apache Spark Structured Streaming to move data from Kafka on HDInsight to Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md) (Korzystanie z przesyłania strumieniowego ze strukturą w systemie Apache Spark w celu przenoszenia danych z platformy Kafka w usłudze HDInsight do usługi Cosmos DB)
* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Nawiązywanie połączenia z platformą Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)