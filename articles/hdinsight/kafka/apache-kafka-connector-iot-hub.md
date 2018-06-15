---
title: Użyj Apache Kafka na HDInsight z Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Apache Kafka na HDInsight z Centrum IoT Azure. Projekt Centrum IoT Azure połączyć Kafka zapewnia łącznika źródłowy i odbiorczy Kafka. Łącznik źródła można odczytać danych z Centrum IoT i Centrum IoT zapisuje łącznika odbioru.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2018
ms.author: larryfr
ms.openlocfilehash: 33fdb5b099efc40fec94a860b21cda75ced44fe9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267591"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Użyj Apache Kafka na HDInsight z Centrum IoT Azure

Dowiedz się, jak używać [Kafka połączyć Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) łącznika do przenoszenia danych między Kafka Apache na HDInsight i Azure IoT Hub. W tym dokumencie Dowiedz się jak uruchomić łącznik Centrum IoT z węzłem krawędzi w klastrze.

API połączyć Kafka umożliwia wdrożenie łączników, które stale ściąganie danych do Kafka lub wypychać dane z Kafka do innego systemu. [Centrum IoT Azure połączyć Kafka](https://github.com/Azure/toketi-kafka-connect-iothub) jest łącznik, który pobiera dane z Centrum IoT Azure do Kafka. On również wypychać dane z Kafka w Centrum IoT. 

Ściąganie z Centrum IoT, za pomocą __źródła__ łącznika. Wypychanie do Centrum IoT, za pomocą __zbiornika__ łącznika. Łącznik Centrum IoT zapewnia źródło i ujście łączników.

Na poniższym diagramie przedstawiono przepływ danych między centrum IoT Azure i Kafka w usłudze HDInsight przy użyciu łącznika.

![Obraz przedstawiający dane przepływające z Centrum IoT na Kafka za pośrednictwem łącznika](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Aby uzyskać więcej informacji na połączenia interfejsu API, zobacz [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Kafka w klastrze usługi HDInsight. Aby uzyskać więcej informacji, zobacz dokument [Przewodnik Szybki start dla platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md).

* Krawędź węzeł w klastrze Kafka. Aby uzyskać więcej informacji, zobacz [krawędzi węzłów za pomocą usługi HDInsight](../hdinsight-apps-use-edge-node.md) dokumentu.

* Centrum IoT Azure. W tym samouczku, najlepiej [symulator online Połącz Pi malina Centrum IoT Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentu.

* Klient SSH. W krokach opisanych w tym dokumencie połączenie z klastrem jest nawiązywane za pomocą protokołu SSH. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Instalowanie łącznika

1. Pobieranie najnowszej wersji programu Kafka Connect dla Centrum IoT Azure z [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Aby przekazać plik JAR do węzła krawędzi Kafka sieci w klastrze usługi HDInsight, użyj następującego polecenia:

    > [!NOTE]
    > Zastąp `sshuser` przy użyciu konta użytkownika SSH dla klastra usługi HDInsight. Zastąp `new-edgenode` o nazwie węzła krawędzi. Zastąp `clustername` z nazwą klastra. Aby uzyskać więcej informacji na punkt końcowy SSH dla węzła krawędzi, zobacz [używane z usługą HDInsight węzłów krawędzi](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentu.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Po zakończeniu kopiowania plików, połączyć się z węzłem krawędzi przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Aby zainstalować łącznik na Kafka `libs` katalogu, należy użyć następującego polecenia:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Jeśli wystąpiły problemy z pozostałych kroków w tym dokumencie podczas korzystania z pliku JAR wbudowanych, spróbuj kompilowania pakietu ze źródła.
>
> Aby utworzyć łącznik, musi mieć Środowisko deweloperskie Scala z [narzędzie kompilacji Scala](http://www.scala-sbt.org/).
>
> 1. Pobierz źródła dla łącznika z [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) w środowisku deweloperskim.
>
> 2. Z wiersza polecenia w katalogu projektu użyj następującego polecenia, aby skompilować i pakiet projektu:
>
>    ```bash
>    sbt assembly
>    ```
>
>    To polecenie tworzy plik o nazwie `kafka-connect-iothub-assembly_2.11-0.6.jar` w `target/scala-2.11` katalogu projektu.

## <a name="configure-kafka"></a>Skonfiguruj Kafka

Z połączenia SSH z węzłem krawędzi wykonaj następujące kroki, aby skonfigurować Kafka w celu uruchamiania konektora w trybie autonomicznym:

1. Zapisz nazwę klastra w zmiennej. Za pomocą zmiennej ułatwia kopiowanie/wklejanie w innych poleceń w tej sekcji:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Zainstaluj `jq` narzędzia. To narzędzie ułatwia przetwarzania dokumentów JSON zwracane z zapytań Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Pobierz adres Kafka brokerzy. Może istnieć wiele brokerzy w klastrze, ale należy odwoływać się do jednego lub dwóch. Aby uzyskać adres dwa hosty brokera, użyj następującego polecenia:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora). Zwrócona wartość będzie podobna do następującego tekstu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Pobierz adres węzły dozorcy. Istnieje kilka dozorcy węzłów w klastrze, ale należy odwoływać się do jednego lub dwóch. Aby uzyskać adres dwa węzły dozorcy, użyj następującego polecenia:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Podczas uruchamiania w trybie autonomicznym, łącznik `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` plik jest używany do komunikacji z brokerzy Kafka. Aby edytować `connect-standalone.properties` plików, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Aby skonfigurować w konfiguracji autonomicznej dla węzła krawędzi można znaleźć Kafka brokerów, wyszukaj wiersz rozpoczynający się od `bootstrap.servers=`. Zastąp `localhost:9092` wartości z hostami broker z poprzedniego kroku.

    * Zmień `key.converter=` i `value.converter=` wierszy na następujące wartości:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Ta zmiana umożliwia testowanie za pomocą dołączonego Kafka producenta konsoli. Mogą być potrzebne konwertery różnych innych producentów i konsumentów. Uzyskać informacji na temat używania innych konwerter wartości, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Dodaj wiersz na końcu pliku, który zawiera następujący tekst:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Ta zmiana jest aby zapobiec limitów czasu w łączniku zbiornika ograniczając go do 10 rekordów jednocześnie. Aby uzyskać więcej informacji, zobacz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

7. Aby utworzyć tematy używane przez łącznik, użyj następujących poleceń:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Aby sprawdzić, czy `iotin` i `iotout` tematy istnieje, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` Tematu jest używany do odbierania wiadomości z Centrum IoT. `iotout` Tematu służy do wysyłania komunikatów do Centrum IoT.

## <a name="get-iot-hub-connection-information"></a>Pobierz informacje o połączeniu Centrum IoT

Aby uzyskać informacje Centrum IoT używane przez łącznik, wykonaj następujące czynności:

1. Pobieranie punktu końcowego zgodnego Centrum zdarzeń i Centrum zdarzeń zgodnych Nazwa punktu końcowego Centrum IoT. Aby uzyskać informacje o tym, użyj jednej z następujących metod:

    * __Z [portalu Azure](https://portal.azure.com/)__, wykonaj następujące kroki:

        1. Przejdź do Centrum IoT i wybierz __punkty końcowe__.
        2. Z __wbudowane punkty końcowe__, wybierz pozycję __zdarzenia__.
        3. Z __właściwości__, skopiuj wartość pola:

            * __Nazwa zgodnych z Centrum zdarzeń__
            * __Punkt końcowy zgodnych z Centrum zdarzeń__
            * __Partycje__

        > [!IMPORTANT]
        > Wartość punktu końcowego z portalu może zawierać dodatkowy tekst, który nie jest potrzebne w tym przykładzie. Wyodrębnij tekst, który pasuje do tego wzorca `sb://<randomnamespace>.servicebus.windows.net/`.

    * __Z [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, użyj następującego polecenia:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Zastąp `myhubname` nazwą Centrum IoT. Odpowiedź jest podobny do następującego tekstu:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Pobierz __udostępnionych zasad dostępu__ i __klucza__. Na przykład użyć __usługi__ klucza. Aby uzyskać informacje o tym, użyj jednej z następujących metod:

    * __Z [portalu Azure](https://portal.azure.com/)__, wykonaj następujące kroki:

        1. Wybierz __zasady dostępu współużytkowanego__, a następnie wybierz __usługi__.
        2. Kopiuj __klucz podstawowy__ wartość.
        3. Kopiuj __parametry połączenia — klucz podstawowy__ wartość.

    * __Z [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, użyj następującego polecenia:

        1. Aby uzyskać wartość klucza podstawowego, użyj następującego polecenia:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Zastąp `myhubname` nazwą Centrum IoT. Odpowiedź jest klucz podstawowy na `service` zasady dla tego koncentratora.

        2. Aby uzyskać ciąg połączenia dla `service` zasad, użyj następującego polecenia:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Zastąp `myhubname` nazwą Centrum IoT. Odpowiedź jest ciąg połączenia dla `service` zasad.

## <a name="configure-the-source-connection"></a>Skonfiguruj połączenie ze źródłem

Aby skonfigurować źródła do pracy z Centrum IoT, wykonaj następujące czynności z połączenia SSH z węzłem krawędzi:

1. Utwórz kopię `connect-iot-source.properties` w pliku `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-kafka-connect-Centrum iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Aby edytować `connect-iot-source.properties` plik i dodać informacje o Centrum IoT, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    W edytorze Znajdź i Zmień następujące wpisy:

    * `Kafka.Topic=PLACEHOLDER`: Zastąp `PLACEHOLDER` z `iotin`. Wiadomości odebrane z Centrum IoT są umieszczane w `iotin` tematu.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Zastąp `PLACEHOLDER` o nazwie zgodnej Centrum zdarzeń.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Zastąp `PLACEHOLDER` z punktem końcowym zgodnego Centrum zdarzeń.
    * `IotHub.Partitions=PLACEHOLDER`: Zastąp `PLACEHOLDER` o liczbie partycji z poprzedniego kroku.
    * `IotHub.AccessKeyName=PLACEHOLDER`: Zastąp `PLACEHOLDER` z `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: Zastąp `PLACEHOLDER` z kluczem podstawowym `service` zasad.
    * `IotHub.StartType=PLACEHOLDER`: Zastąp `PLACEHOLDER` z daty UTC. Ta data jest uruchomienia łącznika sprawdzanie komunikatów. Format daty jest `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: Zastąp `100` z `5`. Ta zmiana powoduje, że łącznik, aby odczytywać wiadomości w Kafka po pięć nowych komunikatów w Centrum IoT.

    Konfigurację przykładową przedstawiono w temacie [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Aby zapisać zmiany, należy użyć __Ctrl + X__, __Y__, a następnie __Enter__.

Aby uzyskać więcej informacji na temat konfigurowania źródła łącznika, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Konfigurowanie połączenia odbioru

Aby skonfigurować połączenia odbioru do pracy z Centrum IoT, wykonaj następujące czynności z połączenia SSH z węzłem krawędzi:

1. Utwórz kopię `connect-iothub-sink.properties` w pliku `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-kafka-connect-Centrum iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Aby edytować `connect-iothub-sink.properties` plik i dodać informacje o Centrum IoT, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    W edytorze Znajdź i Zmień następujące wpisy:

    * `topics=PLACEHOLDER`: Zastąp `PLACEHOLDER` z `iotout`. Komunikatów zapisywanych `iotout` tematu są przekazywane do Centrum IoT.
    * `IotHub.ConnectionString=PLACEHOLDER`: Zastąp `PLACEHOLDER` ciągu połączenia dla `service` zasad.

    Konfigurację przykładową przedstawiono w temacie [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Aby zapisać zmiany, należy użyć __Ctrl + X__, __Y__, a następnie __Enter__.

Aby uzyskać więcej informacji na temat konfigurowania zbiornika łącznika, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Początek łącznika źródła

Aby uruchomić łącznik źródła, użyj następującego polecenia z połączenia SSH z węzłem krawędzi:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Po uruchomieniu łącznik wysyłania komunikatów do Centrum IoT z Twojego urządzenia. Łącznik odczytuje wiadomości z Centrum IoT i przechowuje je w temacie Kafka, rejestruje informacje do konsoli:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Kilka ostrzeżeń może zostać wyświetlony po uruchomieniu łącznika. Te ostrzeżenia, nie powodują problemy z odbieranie komunikatów z Centrum IoT.

Aby wyłączyć łącznik, należy użyć __klawisze Ctrl + C__.

## <a name="start-the-sink-connector"></a>Początek łącznika odbioru

Z połączenia SSH z węzłem krawędzi Użyj następującego polecenia, aby uruchomić łącznik zbiornika w trybie autonomicznym:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Jak działa łącznik, wyświetlane są informacje podobne do następującego tekstu:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Po uruchomieniu łącznika można zauważyć kilka ostrzeżeń. Możesz je bezpiecznie zignorować.

Do wysyłania wiadomości przez łącznik, wykonaj następujące kroki:

1. Otwórz innej sesji SSH z klastrem Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Aby wysyłać komunikaty do `iotout` tematu, należy użyć następującego polecenia:

    > [!WARNING]
    > Ponieważ jest to nowe połączenie SSH `$KAFKABROKERS` zmiennej nie zawiera żadnych informacji. Aby ustawić ją, użyj jednej z następujących metod:
    >
    > * Użyj pierwsze trzy kroki w [skonfigurować Kafka](#configure-kafka) sekcji.
    > * Użyj `echo $KAFKABROKERS` poprzednie połączenie SSH pobiera wartości, a następnie zastąp `$KAFKABROKERS` w poniższym poleceniu rzeczywiste wartości.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    To polecenie nie powrót do normalnej wiersza Bash. Zamiast tego, wysyła wprowadzanie z klawiatury do `iotout` tematu.

3. Aby wysłać komunikat do urządzenia, Wklej dokument JSON w sesji SSH dla `kafka-console-producer`.

    > [!IMPORTANT]
    > Należy ustawić wartość `"deviceId"` wpis dla identyfikatora urządzenia. W poniższym przykładzie urządzenia o nazwie `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Schemat dla tego dokumentu JSON jest opisany bardziej szczegółowo na [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Jeśli używasz symulowane urządzenie Pi malina i jest uruchomiona, następujące zdarzenie jest rejestrowane przez urządzenie:

    ```text
    Receive message: Turn On
    ```

    Wyślij ponownie dokument JSON, ale Zmień wartość `"message"` wpisu. Nowa wartość jest rejestrowane przez urządzenie.

Aby uzyskać więcej informacji o używaniu łącznika odbioru, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób Użyj interfejsu API połączyć Kafka, aby uruchomić łącznik Kafka IoT w usłudze HDInsight. Aby dowiedzieć się, inne metody pracy z Kafka, użyj następujących łączy:

* [Używanie platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
