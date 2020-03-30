---
title: Korzystanie z aplikacji Apache Kafka w usłudze HDInsight w centrum Usługi Azure IoT Hub
description: Dowiedz się, jak używać aplikacji Apache Kafka w programie HDInsight w usłudze Azure IoT Hub. Projekt Usługi Azure IoT Hub platformy Kafka Connect udostępnia łącznik źródłowy i ujście dla platformy Kafka. Łącznik źródłowy może odczytywać dane z usługi IoT Hub, a łącznik ujścia zapisuje w centrum IoT Hub.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 48a72b5ba3819712b9e1d2536ae2dd3a06eaf3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238814"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Korzystanie z aplikacji Apache Kafka w usłudze HDInsight w centrum Usługi Azure IoT Hub

Dowiedz się, jak korzystać ze łącznika [Usługi Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) do przenoszenia danych między apache kafka w usłudze HDInsight i usłudze Azure IoT Hub. W tym dokumencie dowiesz się, jak uruchomić łącznik Centrum IoT z węzła brzegowego w klastrze.

Interfejs API platformy Kafka Connect umożliwia implementowanie łączników, które stale ściągają dane do platformy Kafka lub wypychają dane z platformy Kafka do innego systemu. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) to łącznik, który pobiera dane z usługi Azure IoT Hub do platformy Kafka. Może również wypychać dane z platformy Kafka do centrum IoT Hub.

Podczas ściągania z usługi IoT Hub, należy użyć łącznika __źródłowego.__ Podczas wypychania do centrum IoT Hub, należy użyć łącznika __ujścia.__ Łącznik IoT Hub zawiera zarówno złącza źródłowe, jak i umiejcze.

Na poniższym diagramie przedstawiono przepływ danych między usługi Azure IoT Hub i Platformy Kafka na hdinsight podczas korzystania z łącznika.

![Obraz przedstawiający dane płynące z usługi IoT Hub do platformy Kafka przez łącznik](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Aby uzyskać więcej informacji na [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)temat interfejsu API connect, zobacz .

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Platformy Kafek Apache w programie HDInsight. Aby uzyskać więcej informacji, zobacz dokument [Przewodnik Szybki start dla platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md).

* Węzeł krawędzi w klastrze platformy Kafka. Aby uzyskać więcej informacji, zobacz [Użyj węzłów krawędzi z dokumentem HDInsight.](../hdinsight-apps-use-edge-node.md)

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Centrum IoT usługi Azure i urządzenia. W tym artykule należy rozważyć użycie [trybu online Connect Raspberry Pi w usłudze Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started)

* [Scala budować narzędzie](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Zbuduj łącznik

1. Pobierz źródło łącznika [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) z lokalnego środowiska.

2. W wierszu polecenia przejdź `toketi-kafka-connect-iothub-master` do katalogu. Następnie użyj następującego polecenia, aby skompilować i spakować projekt:

    ```cmd
    sbt assembly
    ```

    Kompilacja potrwa kilka minut. Polecenie tworzy plik `kafka-connect-iothub-assembly_2.11-0.7.0.jar` nazwany `toketi-kafka-connect-iothub-master\target\scala-2.11` w katalogu dla projektu.

## <a name="install-the-connector"></a>Zainstaluj złącze

1. Przekaż plik .jar do węzła brzegowego platformy Kafka w klastrze HDInsight. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` rzeczywistą nazwą klastra. Wartości domyślne dla konta użytkownika SSH i nazwa [węzła krawędzi](../hdinsight-apps-use-edge-node.md#access-an-edge-node) są używane poniżej, zmodyfikować w razie potrzeby.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po zakończeniu kopiowania pliku połącz się z węzłem brzegowym za pomocą funkcji SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby zainstalować łącznik w `libs` katalogu platformy Kafka, użyj następującego polecenia:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Utrzymuj aktywne połączenie SSH dla pozostałych kroków.

## <a name="configure-apache-kafka"></a>Konfigurowanie apache kafki

Z połączenia SSH do węzła brzegowego należy wykonać następujące czynności, aby skonfigurować program Kafka do uruchamiania łącznika w trybie autonomicznym:

1. Skonfiguruj zmienną hasła. Zamień hasło na hasło logowania klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'
    ```

1. Zainstaluj narzędzie [jq.](https://stedolan.github.io/jq/) jq ułatwia przetwarzanie dokumentów JSON zwróconych z zapytań Ambari. Wprowadź następujące polecenie:

    ```bash
    sudo apt -y install jq
    ```

1. Uzyskaj adres brokerów Kafka. Może istnieć wielu brokerów w klastrze, ale trzeba odwołać się tylko jeden lub dwa. Aby uzyskać adres dwóch hostów brokera, użyj następującego polecenia:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Skopiuj wartości do późniejszego użycia. Zwrócona wartość będzie podobna do następującego tekstu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Uzyskaj adres węzłów Apache Zookeeper. Istnieje kilka węzłów Zookeeper w klastrze, ale trzeba odwołać się tylko jeden lub dwa. Użyj następującego polecenia, aby zapisać `KAFKAZKHOSTS`adresy w zmiennej:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Podczas uruchamiania łącznika w trybie autonomicznym `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` plik jest używany do komunikowania się z brokerami platformy Kafka. Aby edytować `connect-standalone.properties` plik, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Wprowadzać następujące zmiany:

    |Bieżąca wartość |Nowa wartość | Komentarz |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Zastąp `localhost:9092` wartość hostami brokera z poprzedniego kroku|Konfiguruje konfigurację autonomiczną dla węzła brzegowego, aby znaleźć brokerów platformy Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Ta zmiana pozwala przetestować przy użyciu producenta konsoli dołączonego do platformy Kafka. Możesz potrzebować różnych konwerterów dla innych producentów i konsumentów. Aby uzyskać informacje na temat [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)używania innych wartości konwertera, zobacz .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Tak samo jak powyżej.|
    |Nie dotyczy|`consumer.max.poll.records=10`|Dodaj na końcu pliku. Ta zmiana ma na celu zapobieganie przekroczeniu limitu czasu w łączniku ujścia, ograniczając go do 10 rekordów naraz. Aby uzyskać więcej [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)informacji, zobacz .|

1. Aby zapisać plik, użyj __klawiszy Ctrl + X__, __Y__, a następnie __wprowadź__.

1. Aby utworzyć tematy używane przez łącznik, należy użyć następujących poleceń:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Aby sprawdzić, `iotin` `iotout` czy istnieją tematy, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Temat `iotin` jest używany do odbierania wiadomości z Usługi IoT Hub. Temat `iotout` jest używany do wysyłania wiadomości do usługi IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Uzyskaj informacje o połączeniu usługi IoT Hub

Aby pobrać informacje o koncentratorze IoT używane przez łącznik, należy wykonać następujące kroki:

1. Pobierz punkt końcowy zgodny z Centrum zdarzeń i nazwę punktu końcowego zgodnego z Centrum zdarzeń dla centrum IoT hub. Aby uzyskać te informacje, należy użyć jednej z następujących metod:

   * __Z [witryny Azure portal](https://portal.azure.com/)__ należy wykonać następujące czynności:

     1. Przejdź do centrum IoT Hub i wybierz pozycję __Punkty końcowe__.
     2. Z __wbudowanych punktów końcowych__wybierz pozycję __Zdarzenia__.
     3. Z __właściwości__, skopiuj wartość następujących pól:

         * __Nazwa zgodna z Centrum zdarzeń__
         * __Punkt końcowy zgodny z Centrum zdarzeń__
         * __Partycje__

        > [!IMPORTANT]  
        > Wartość punktu końcowego z portalu może zawierać dodatkowy tekst, który nie jest potrzebny w tym przykładzie. Wyodrębnij tekst zgodny `sb://<randomnamespace>.servicebus.windows.net/`z tym wzorcem .

   * __Z [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ użyj następującego polecenia:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Zamień `myhubname` na nazwę centrum IoT Hub. Odpowiedź jest podobna do następującego tekstu:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Pobierz __zasady dostępu współdzielonego__ i __klucz__. W tym przykładzie użyj klucza __usługi.__ Aby uzyskać te informacje, należy użyć jednej z następujących metod:

    * __Z [witryny Azure portal](https://portal.azure.com/)__ należy wykonać następujące czynności:

        1. Wybierz pozycję __Zasady dostępu współdzielonego,__ a następnie wybierz __pozycję Usługa__.
        2. Skopiuj wartość __klucza podstawowego.__
        3. Skopiuj ciąg połączenia — wartość __klucza podstawowego.__

    * __Z [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ użyj następującego polecenia:

        1. Aby uzyskać wartość klucza podstawowego, użyj następującego polecenia:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Zamień `myhubname` na nazwę centrum IoT Hub. Odpowiedź jest kluczem podstawowym `service` do zasad dla tego centrum.

        2. Aby uzyskać parametry połączenia `service` dla zasad, użyj następującego polecenia:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Zamień `myhubname` na nazwę centrum IoT Hub. Odpowiedź jest ciągiem połączenia `service` dla zasad.

## <a name="configure-the-source-connection"></a>Konfigurowanie połączenia źródłowego

Aby skonfigurować źródło do pracy z centrum IoT Hub, wykonaj następujące akcje z połączenia SSH do węzła brzegowego:

1. Utwórz kopię `connect-iot-source.properties` pliku w `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-kafka-connect-iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Aby edytować `connect-iot-source.properties` plik i dodać informacje o centrum IoT, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. W edytorze znajdź i zmień następujące wpisy:

    |Bieżąca wartość |Edytuj|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Zastąp element `PLACEHOLDER` pytaniem `iotin`. Wiadomości odebrane z centrum IoT hub są umieszczane w `iotin` temacie.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Zamień `PLACEHOLDER` na nazwę zgodną z Centrum zdarzeń.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Zamień `PLACEHOLDER` na punkt końcowy zgodny z Centrum zdarzeń.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Zastąp element `PLACEHOLDER` pytaniem `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Zamień `PLACEHOLDER` na klucz `service` podstawowy zasad.|
    |`IotHub.Partitions=PLACEHOLDER`|Zamień `PLACEHOLDER` na liczbę partycji z poprzednich kroków.|
    |`IotHub.StartTime=PLACEHOLDER`|Zamień `PLACEHOLDER` datę UTC. Ta data jest, gdy łącznik rozpoczyna sprawdzanie wiadomości. Format daty `yyyy-mm-ddThh:mm:ssZ`to .|
    |`BatchSize=100`|Zastąp element `100` pytaniem `5`. Ta zmiana powoduje łącznika do odczytu wiadomości do platformy Kafka, gdy istnieje pięć nowych wiadomości w centrum IoT hub.|

    Aby uzyskać przykładową konfigurację, zobacz [Łącznik źródłowy łączyć platformę Kafka dla usługi Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Aby zapisać zmiany, użyj __klawiszy Ctrl + X__, __Y__, a następnie __wprowadź__.

Aby uzyskać więcej informacji na temat [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)konfigurowania źródła łącznika, zobacz .

## <a name="configure-the-sink-connection"></a>Konfigurowanie połączenia ujścia

Aby skonfigurować połączenie ujścia do pracy z centrum IoT Hub, wykonaj następujące akcje z połączenia SSH do węzła krawędzi:

1. Utwórz kopię `connect-iothub-sink.properties` pliku w `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-kafka-connect-iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Aby edytować `connect-iothub-sink.properties` plik i dodać informacje o centrum IoT, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. W edytorze znajdź i zmień następujące wpisy:

    |Bieżąca wartość |Edytuj|
    |---|---|
    |`topics=PLACEHOLDER`|Zastąp element `PLACEHOLDER` pytaniem `iotout`. Wiadomości zapisane `iotout` w temacie są przekazywane do centrum IoT Hub.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Zamień `PLACEHOLDER` na ciąg `service` połączenia dla zasady.|

    Aby uzyskać przykładową konfigurację, zobacz [Łącznik odjęcia łączącego platformę Kafka dla usługi Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Aby zapisać zmiany, użyj __klawiszy Ctrl + X__, __Y__, a następnie __wprowadź__.

Aby uzyskać więcej informacji na temat [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)konfigurowania zlewu złącza, zobacz .

## <a name="start-the-source-connector"></a>Uruchamianie łącznika źródłowego

1. Aby uruchomić łącznik źródłowy, użyj następującego polecenia z połączenia SSH do węzła krawędziowego:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Po uruchomieniu łącznika wysyłaj wiadomości do centrum IoT hub z urządzeń. Gdy łącznik odczytuje komunikaty z centrum IoT i przechowuje je w temacie platformy Kafka, rejestruje informacje do konsoli:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Podczas uruchamiania łącznika może pojawić się kilka ostrzeżeń. Te ostrzeżenia nie powodują problemów z odbieraniem wiadomości z centrum IoT Hub.

1. Zatrzymaj złącze po kilku minutach za pomocą **ctrl + C** dwa razy. Zatrzymanie złącza zajmie kilka minut.

## <a name="start-the-sink-connector"></a>Uruchamianie złącza zlewu

Z połączenia SSH do węzła krawędzi użyj następującego polecenia, aby uruchomić złącze ujścia w trybie autonomicznym:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Po uruchomieniu łącznika wyświetlane są informacje podobne do następującego tekstu:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Podczas uruchamiania łącznika można zauważyć kilka ostrzeżeń. Możesz je bezpiecznie zignorować.

## <a name="send-messages"></a>Wysyłanie komunikatów

Aby wysyłać wiadomości za pośrednictwem łącznika, należy wykonać następujące czynności:

1. Otwórz *drugą* sesję SSH w klastrze platformy Kafka:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uzyskaj adres brokerów Kafka na nową sesję SSH. Zamień hasło na hasło logowania klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Aby wysłać `iotout` wiadomości do tematu, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    To polecenie nie powoduje powrotu do normalnego wiersza Bash. Zamiast tego wysyła dane wejściowe klawiatury do tematu. `iotout`

1. Aby wysłać wiadomość do urządzenia, wklej dokument JSON do `kafka-console-producer`sesji SSH dla pliku .

    > [!IMPORTANT]  
    > Należy ustawić wartość wpisu `"deviceId"` na identyfikator urządzenia. W poniższym przykładzie urządzenie `myDeviceId`nosi nazwę:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Schemat dla tego dokumentu JSON jest opisany [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)bardziej szczegółowo w .

    Jeśli używasz symulowanego urządzenia Raspberry Pi i jest ono uruchomione, urządzenie zarejestrowało następujący komunikat:

    ```output
    Receive message: Turn On
    ```

    Ponownie wysuń dokument JSON, `"message"` ale zmień wartość wpisu. Nowa wartość jest rejestrowana przez urządzenie.

Aby uzyskać więcej informacji na [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)temat korzystania ze złącza zlewu, zobacz .

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak używać interfejsu API Apache Kafka Connect do uruchamiania łącznika platformy IoT Platformy Kafka w programie HDInsight. Skorzystaj z następujących linków, aby poznać inne sposoby pracy z platformą Kafka:

* [Użyj Apache Spark z Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Użyj Apache Storm z Apache Kafka na HDInsight](../hdinsight-apache-storm-with-kafka.md)
