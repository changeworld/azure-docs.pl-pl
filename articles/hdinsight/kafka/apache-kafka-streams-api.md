---
title: Za pomocą interfejsu API strumienie Kafka Apache — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z interfejsu API Apache Kafka strumieni z Kafka w usłudze HDInsight. Ten interfejs API umożliwia przeprowadzanie przetwarzania między tematami w Kafka strumieni.
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
ms.openlocfilehash: 36d67cdb99871f3948db1f6497b1a4638df4f3f1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-streams-api"></a>Strumienie Apache Kafka interfejsu API

Dowiedz się, jak utworzyć aplikację, która używa interfejsu API strumienie Kafka i uruchom go z Kafka w usłudze HDInsight.

Podczas pracy z Apache Kafka, przetwarzania strumienia często odbywa się za pomocą platformy Apache Spark lub Storm. Kafka wersji 0.10.0 (w usłudze HDInsight 3.5 i 3,6) wprowadzono interfejs API Kafka strumieni. Ten interfejs API umożliwia przekształcanie strumienie danych między wejściowymi i wyjściowymi tematów, za pomocą aplikacji, która działa na Kafka. W niektórych przypadkach może to być alternatywę dla tworzenia Spark lub Storm rozwiązań do przesyłania strumieniowego. Aby uzyskać więcej informacji na strumienie Kafka, zobacz [wprowadzenie do strumieni](https://kafka.apache.org/10/documentation/streams/) dokumentacji w serwisie Apache.org.

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Następujące składniki muszą być zainstalowane w środowisku deweloperskim:

* [Zestaw Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) lub równoważny, taki jak OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH i polecenie `scp`. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Konfigurowanie środowiska wdrażania

W tym przykładzie wymaga Kafka na 3,6 HDInsight. Aby dowiedzieć się, jak utworzyć Kafka w klastrze usługi HDInsight, zobacz [rozpoczynać Kafka w usłudze HDInsight](apache-kafka-get-started.md) dokumentu.

## <a name="build-and-deploy-the-example"></a>Tworzenie i wdrażanie przykładu

Tworzenie i wdrażanie projektu sieci Kafka w klastrze usługi HDInsight, wykonaj następujące kroki.

1. Pobierz przykłady z [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Przejdź do `Streaming` katalogu, a następnie użyć następujące polecenie, aby utworzyć pakiet jar:

    ```bash
    mvn clean package
    ```

    To polecenie tworzy katalog o nazwie `target`, który zawiera plik o nazwie `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Użyj następującego polecenia, aby skopiować `kafka-streaming-1.0-SNAPSHOT.jar` pliku do klastra usługi HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Zamień ciąg **SSHUSER** na nazwę użytkownika SSH klastra i zamień ciąg **CLUSTERNAME** na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło dla konta użytkownika SSH. Aby uzyskać więcej informacji na temat używania `scp` z usługą HDInsight, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>Uruchomić przykład

1. Aby otworzyć połączenie SSH do klastra, użyj następującego polecenia:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zamień ciąg **SSHUSER** na nazwę użytkownika SSH klastra i zamień ciąg **CLUSTERNAME** na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło dla konta użytkownika SSH. Aby uzyskać więcej informacji na temat używania `scp` z usługą HDInsight, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Aby utworzyć tematy, które są używane w tym przykładzie Kafka, użyj następujących poleceń:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Zastąp __nazwa klastra__ o nazwie z klastrem usługi HDInsight. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra usługi HDInsight.

    > [!NOTE]
    > Jeśli logowania do klastra jest inna niż domyślna wartość `admin`, Zastąp `admin` wartości w powyższych poleceń nazwy logowania klastra.

5. Aby uruchomić ten przykład, należy wykonać trzy czynności:

    * Uruchom rozwiązania strumieni zawarte w `kafka-streaming.jar`.
    * Uruchom producenta, która zapisuje `test` tematu.
    * Uruchom konsumenta, dzięki czemu można zobaczyć dane wyjściowe zapisane `wordcounts` tematu

    > [!NOTE]
    > Należy sprawdzić, czy `auto.create.topics.enable` ma ustawioną wartość właściwości `true` w pliku konfiguracyjnym Kafka brokera. Tej właściwości można wyświetlać i modyfikować w pliku zaawansowane konfiguracji brokera Kafka przy użyciu interfejsu użytkownika sieci Web Ambari. W przeciwnym razie należy utworzyć temat pośredniego `RekeyedIntermediateTopic` ręcznie, przed uruchomieniem w tym przykładzie przy użyciu następującego polecenia:
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    Te operacje można wykonywać, otwierając trzy sesji SSH. Następnie trzeba ustawić, ale `$KAFKABROKERS` i `$KAFKAZKHOSTS` dla każdego uruchamiając kroku 4 z sekcji w każdej sesji SSH. Łatwiejsze rozwiązaniem jest użycie `tmux` narzędzia, które można podzielić bieżącego ekranu SSH w wielu sekcjach. Można uruchomić strumienia, producent i konsumentów przy użyciu `tmux`, użyj następującego polecenia:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    To polecenie dzieli wyświetlania SSH na trzy części:

    * Lewej sekcji uruchamia konsumenta konsoli, które odczytuje komunikaty z `wordcounts` tematu: `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > `--property` Parametry Poinformuj użytkownika konsoli do drukowania klucza (word) wraz z count (wartość). Ten parametr konfiguruje również deserializatora do użycia podczas odczytu z Kafka tych wartości.

    * Pierwsza sekcja prawo uruchamia rozwiązania strumieni interfejsu API: `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * Prawej dolnej części uruchamia producent konsoli i oczekuje na wprowadzenie komunikatów do wysłania do `test` tematu: `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Po `tmux` polecenia dzieli wyświetlania, kursor znajduje się w prawej dolnej części. Rozpocznij wprowadzanie zdań. Po każdym zdaniu okienka po lewej stronie jest aktualizowana w celu wyświetlenia liczbę unikatowych słów. Dane wyjściowe będą podobne do następującego tekstu:
   
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
    > Liczba rośnie przy każdym napotkaniu wyrazu.

7. Użyj __klawisze Ctrl + C__ aby zakończyć producenta. Nadal używać __klawisze Ctrl + C__ aby zakończyć działanie aplikacji i konsumenta.

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób za pomocą interfejsu API strumienie Kafka z Kafka w usłudze HDInsight. Dowiedz się więcej o pracy z platformą Kafka, korzystając z następujących zasobów:

* [Analizowanie dzienników Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicate data between Kafka clusters (Replikowanie danych między klastrami Kafka)](apache-kafka-mirroring.md)
* [Kafka Producer and Consumer API with HDInsight](apache-kafka-producer-consumer-api.md) (Interfejs API producenta i odbiorcy platformy Kafka w usłudze HDInsight).
* [Use Apache Spark streaming (DStream) with Kafka on HDInsight (Używanie strumieni (DStream) platformy Apache Spark z platformą Kafka w usłudze HDInsight)](../hdinsight-apache-spark-with-kafka.md)
* [Use Apache Spark Structured Streaming with Kafka on HDInsight (Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight)](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Use Apache Spark Structured Streaming to move data from Kafka on HDInsight to Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md) (Korzystanie z przesyłania strumieniowego ze strukturą w systemie Apache Spark w celu przenoszenia danych z platformy Kafka w usłudze HDInsight do usługi Cosmos DB)
* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Nawiązywanie połączenia z platformą Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
