---
title: Korzystanie z platformy Apache Kafka w HDInsight z usługą Azure IoT Hub
description: Dowiedz się, jak używać platformy Apache Kafka w HDInsight z usługą Azure IoT Hub. Projekt platformy Kafka łączenie usługi Azure IoT Hub udostępnia łącznik źródła i ujścia dla platformy Kafka. Łącznik źródła może odczytywać dane z usługi IoT Hub, a connector ujścia zapisuje do usługi IoT Hub.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 420800e718c8f98bfd3d5d7383829d5aa5472828
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698163"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Korzystanie z platformy Apache Kafka w HDInsight z usługą Azure IoT Hub

Dowiedz się, jak używać [Apache Kafka łączenie usługi Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) łącznika w celu przenoszenia danych między platformy Apache Kafka w HDInsight i Azure IoT Hub. W tym dokumencie dowiesz się, jak uruchomić łącznik usługi IoT Hub z węzłem krawędzi klastra.

Interfejsem API połączenia platformy Kafka umożliwia Implementowanie łączników, które stale ściąganie danych do platformy Kafka, lub wypchnąć dane z usługi Kafka do innego systemu. [Apache Kafka łączenie usługi Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) łącznik, który ściąga dane z usługi Azure IoT Hub do platformy Kafka. Jego wypychania danych z usługi Kafka do usługi IoT Hub. 

Ściąganie z usługi IoT Hub, za pomocą funkcji __źródła__ łącznika. Wypychanie do usługi IoT Hub, za pomocą funkcji __ujścia__ łącznika. Łącznik usługi IoT Hub udostępnia łączniki źródła i ujścia.

Na poniższym diagramie przedstawiono przepływ danych między Azure IoT Hub i Kafka na HDInsight przy użyciu łącznika.

![Obraz przedstawiający danych napływających z usługi IoT Hub do platformy Kafka za pośrednictwem łącznika](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Aby uzyskać więcej informacji na temat interfejsu API połączenia, zobacz [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Platforma Kafka w klastrze HDInsight. Aby uzyskać więcej informacji, zobacz dokument [Przewodnik Szybki start dla platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md).

* Węzeł krawędzi klastra Kafka. Aby uzyskać więcej informacji, zobacz [węzłów brzegowych za pomocą HDInsight](../hdinsight-apps-use-edge-node.md) dokumentu.

* Usługa Azure IoT Hub. Na potrzeby tego samouczka najlepiej [łączenie urządzenia Raspberry Pi symulatora w trybie online dla usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentu.

* Klient SSH. W krokach opisanych w tym dokumencie połączenie z klastrem jest nawiązywane za pomocą protokołu SSH. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Zainstaluj łącznik

1. Pobieranie najnowszej wersji programu Connect platformy Kafka dla usługi Azure IoT Hub z [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Aby przekazać plik JAR z węzłem krawędzi Twojej platformy kafka w klastrze HDInsight, użyj następującego polecenia:

    > [!NOTE]  
    > Zastąp `sshuser` przy użyciu konta użytkownika SSH dla klastra usługi HDInsight. Zastąp `new-edgenode` nazwą węzła krawędzi. Zastąp `clustername` nazwą klastra. Aby uzyskać więcej informacji na temat punktu końcowego SSH dla węzła krawędzi, zobacz [używane węzłów brzegowych za pomocą HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentu.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Po zakończeniu kopiowania plików, połączyć się z węzłem krawędzi za pomocą protokołu SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Aby zainstalować łącznik do Kafka `libs` katalogu, użyj następującego polecenia:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]  
> Jeśli napotkasz problemy z pozostałą częścią kroki opisane w tym dokumencie podczas korzystania z pliku JAR wstępnie skompilowane, spróbuj tworzenia pakietu ze źródła.
>
> Aby utworzyć łącznik, musi mieć środowiska programowania Scala, za pomocą [narzędzie kompilacji Scala](https://www.scala-sbt.org/).
>
> 1. Pobierz źródło dla łącznika z [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) do swojego środowiska projektowego.
>
> 2. Z wiersza polecenia w katalogu projektu użyj następującego polecenia, aby skompilować i pakiet z projektem:
>
>    ```bash
>    sbt assembly
>    ```
>
>    To polecenie tworzy plik o nazwie `kafka-connect-iothub-assembly_2.11-0.6.jar` w `target/scala-2.11` katalogu projektu.

## <a name="configure-apache-kafka"></a>Konfigurowanie platformy Apache Kafka

Z połączenia SSH z węzłem krawędzi wykonaj następujące kroki, aby skonfigurować platformy Kafka w celu uruchamiania w trybie autonomicznym:

1. Zapisz nazwę klastra, do zmiennej. Używanie zmiennej ułatwia kopiowanie/wklejanie w innych poleceń w tej sekcji:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Zainstaluj `jq` narzędzia. To narzędzie umożliwia przetwarzanie dokumentów JSON zwracane z zapytań systemu Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Uzyskaj adres Kafka brokerów. Może istnieć wiele brokerzy w klastrze, ale musisz odwoływać się do jednego lub dwóch. Aby uzyskać adres dwa hosty brokera, użyj następującego polecenia:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora). Zwrócona wartość będzie podobna do następującego tekstu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Uzyskaj adres węzłów Apache Zookeeper. Istnieje kilka węzłów dozorcy w klastrze, ale musisz odwoływać się do jednego lub dwóch. Aby uzyskać adres z dwóch węzłów dozorcy, użyj następującego polecenia:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Podczas uruchamiania łącznika w trybie autonomicznym `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` plik jest używany do komunikowania się z brokerów platformy Kafka. Aby edytować `connect-standalone.properties` plików, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Aby skonfigurować w konfiguracji autonomicznej do węzła krawędzi znaleźć brokerów Kafka, znajdź wiersz, który rozpoczyna się od `bootstrap.servers=`. Zastąp `localhost:9092` wartość z hostami brokera z poprzedniego kroku.

    * Zmiana `key.converter=` i `value.converter=` wierszy na następujące wartości:

        ```ini
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]  
        > Ta zmiana umożliwia testowanie za pomocą producenta konsoli dołączone do platformy Kafka. Mogą być potrzebne konwertery różnych innych producentów i konsumentów. Aby uzyskać informacje na temat korzystania z innych konwertera wartości, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Dodaj wiersz na końcu pliku który zawiera następujący tekst:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]  
        > Ta zmiana ma na celu uniknąć przekroczeń limitu czasu w łączniku ujścia poprzez ograniczenie do 10 rekordów, w czasie. Aby uzyskać więcej informacji, zobacz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

7. Aby utworzyć tematy, używane przez łącznik, użyj następujących poleceń:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Aby sprawdzić, czy `iotin` i `iotout` tematy istnieje, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` Tematu jest używany do odbierania komunikatów z usługi IoT Hub. `iotout` Tematu jest używany do wysyłania komunikatów do Centrum IoT.

## <a name="get-iot-hub-connection-information"></a>Pobieranie informacji o połączeniu usługi IoT Hub

Aby pobrać IoT hub informacje używane przez łącznik, użyj następujących kroków:

1. Pobieranie punktu końcowego zgodnego z Centrum zdarzeń i nazwę punktu końcowego zgodnego z Centrum zdarzeń usługi IoT hub. Aby uzyskać te informacje, użyj jednej z następujących metod:

   * __Z [witryny Azure portal](https://portal.azure.com/)__, wykonaj następujące czynności:

     1. Przejdź do Centrum IoT Hub i wybierz __punktów końcowych__.
     2. Z __wbudowanych punktach końcowych__, wybierz opcję __zdarzenia__.
     3. Z __właściwości__, skopiuj wartość pola:

         * __Nazwę zgodną z Centrum zdarzeń__
         * __Punkt końcowy zgodny z Centrum zdarzeń__
         * __Partycje__

        > [!IMPORTANT]  
        > Wartość punktu końcowego w portalu może zawierać dodatkowy tekst, który nie jest potrzebne w tym przykładzie. Wyodrębnij tekst, który pasuje do tego wzorca `sb://<randomnamespace>.servicebus.windows.net/`.

   * __Z [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, użyj następującego polecenia:

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Zastąp `myhubname` nazwą Centrum IoT Hub. Odpowiedź jest podobna do następującego tekstu:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Pobierz __współużytkowane zasady dostępu__ i __klucz__. W tym przykładzie użyj __usługi__ klucza. Aby uzyskać te informacje, użyj jednej z następujących metod:

    * __Z [witryny Azure portal](https://portal.azure.com/)__, wykonaj następujące czynności:

        1. Wybierz __zasady dostępu współdzielonego__, a następnie wybierz pozycję __usługi__.
        2. Kopiuj __klucz podstawowy__ wartość.
        3. Kopiuj __parametry połączenia — klucz podstawowy__ wartość.

    * __Z [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, użyj następującego polecenia:

        1. Aby uzyskać wartość klucza podstawowego, użyj następującego polecenia:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Zastąp `myhubname` nazwą Centrum IoT Hub. Odpowiedź jest klucz podstawowy, aby `service` zasad dla tego koncentratora.

        2. Aby uzyskać parametry połączenia dla `service` zasad, użyj następującego polecenia:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Zastąp `myhubname` nazwą Centrum IoT Hub. Odpowiedź jest ciąg połączenia dla `service` zasad.

## <a name="configure-the-source-connection"></a>Skonfiguruj połączenie ze źródłem

Aby skonfigurować źródła do pracy z Centrum IoT Hub, wykonaj następujące czynności połączenie SSH, z węzłem krawędzi:

1. Utwórz kopię `connect-iot-source.properties` w pliku `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-kafka-connect-iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Aby edytować `connect-iot-source.properties` pliku i Dodaj informacje o Centrum IoT, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    W edytorze należy znaleźć i zmienić następujące wpisy:

   * `Kafka.Topic=PLACEHOLDER`: Zastąp element  pytaniem `iotin`. Komunikatów odebranych z usługi IoT hub są umieszczane w `iotin` tematu.
   * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Zastąp `PLACEHOLDER` o nazwie zgodnego z Centrum zdarzeń.
   * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Zastąp `PLACEHOLDER` przy użyciu punktu końcowego zgodnego z Centrum zdarzeń.
   * `IotHub.Partitions=PLACEHOLDER`: Zastąp `PLACEHOLDER` z liczbą partycji z poprzednich kroków.
   * `IotHub.AccessKeyName=PLACEHOLDER`: Zastąp element  pytaniem `service`.
   * `IotHub.AccessKeyValue=PLACEHOLDER`: Zastąp `PLACEHOLDER` za pomocą klucza podstawowego `service` zasad.
   * `IotHub.StartType=PLACEHOLDER`: Zastąp `PLACEHOLDER` z datę w czasie UTC. Ta data jest, gdy łącznik, który rozpoczyna się sprawdzanie komunikatów. Format daty jest `yyyy-mm-ddThh:mm:ssZ`.
   * `BatchSize=100`: Zastąp element  pytaniem `5`. Ta zmiana powoduje, że łącznik Aby odczytać wiadomości platformy Kafka po pięciu nowych komunikatów w usłudze IoT hub.

     Aby uzyskać przykładową konfigurację, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Aby zapisać zmiany, należy użyć __Ctrl + X__, __Y__, a następnie __Enter__.

Aby uzyskać więcej informacji na temat konfigurowania źródło łączników, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Konfigurowanie połączenia odbioru

Do skonfigurowania połączenia odbioru do pracy z Centrum IoT Hub, wykonaj następujące akcje z poziomu połączenia SSH z węzłem krawędzi:

1. Utwórz kopię `connect-iothub-sink.properties` w pliku `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-kafka-connect-iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Aby edytować `connect-iothub-sink.properties` pliku i Dodaj informacje o Centrum IoT, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    W edytorze należy znaleźć i zmienić następujące wpisy:

   * `topics=PLACEHOLDER`: Zastąp element  pytaniem `iotout`. Komunikatów zapisywanych `iotout` tematu są przekazywane do usługi IoT hub.
   * `IotHub.ConnectionString=PLACEHOLDER`: Zastąp `PLACEHOLDER` parametrami połączenia dla `service` zasad.

     Aby uzyskać przykładową konfigurację, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Aby zapisać zmiany, należy użyć __Ctrl + X__, __Y__, a następnie __Enter__.

Aby uzyskać więcej informacji na temat konfigurowania obiektu sink łącznika, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Rozpocznij łącznik źródła

Aby uruchomić łącznik źródła, użyj następującego polecenia z poziomu połączenia SSH z węzłem krawędzi:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Po uruchomieniu programu connector, wysyłanie komunikatów do Centrum IoT hub, z tego urządzenia. Łącznik odczytuje komunikaty z usługi IoT hub i przechowuje je w temacie platformy Kafka, rejestruje informacje w konsoli:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]  
> Po uruchomieniu programu connector, może zostać wyświetlony kilka ostrzeżeń. Ostrzeżenia te nie powodują problemów z odbieranie komunikatów z usługi IoT hub.

Aby zatrzymać łącznika, użyj __klawisze Ctrl + C__.

## <a name="start-the-sink-connector"></a>Początek łącznika obiektu sink

Z połączenia SSH z węzłem krawędzi Użyj następującego polecenia, aby uruchomić łącznik ujścia w trybie autonomicznym:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Ponieważ łącznik nie zostanie uruchomiony, są wyświetlane informacje podobne do następującego tekstu:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Po uruchomieniu łącznika, możesz zauważyć kilka ostrzeżeń. Możesz je bezpiecznie zignorować.

Aby wysyłać komunikaty za pośrednictwem łącznika, wykonaj następujące kroki:

1. Otwórz inną sesję SSH z klastrem Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Aby wysyłać komunikaty do `iotout` tematu, użyj następującego polecenia:

    > [!WARNING]  
    > Ponieważ jest to nowe połączenie SSH `$KAFKABROKERS` zmiennej nie zawiera żadnych informacji. Aby ustawić go, użyj jednej z następujących metod:
    >
    > * Użyj pierwszych trzech kroków w [Konfigurowanie platformy Apache Kafka](#configure-apache-kafka) sekcji.
    > * Użyj `echo $KAFKABROKERS` z poprzedniego połączenia SSH do uzyskania wartości, a następnie zastąp `$KAFKABROKERS` w następującym poleceniu rzeczywistymi wartościami.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    To polecenie nie powrót do normalnego monitu powłoki Bash. Zamiast tego wysyła klawiatury do `iotout` tematu.

3. Aby wysłać komunikat do Twojego urządzenia, Wklej dokument JSON sesji SSH dla `kafka-console-producer`.

    > [!IMPORTANT]  
    > Należy ustawić wartość z `"deviceId"` wpis, aby identyfikator urządzenia. W poniższym przykładzie nosi nazwę urządzenia `fakepi`:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Schemat dla tego dokumentu JSON jest opisany bardziej szczegółowo w [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Jeśli używasz symulowanego urządzenia Raspberry Pi i jest uruchomiona, następujący komunikat o błędzie jest rejestrowany przez urządzenie:

    ```text
    Receive message: Turn On
    ```

    Wyślij ponownie dokument JSON, ale Zmień wartość właściwości `"message"` wpisu. Nowa wartość jest rejestrowane przez urządzenie.

Aby uzyskać więcej informacji na temat korzystania z łącznika ujścia, zobacz [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób użyć platformy Kafka połączenia interfejsu API Apache do uruchomienia łącznika Kafka IoT w HDInsight. Użyj następujących linków, aby poznać inne sposoby pracy z platformą Kafka:

* [Korzystanie z platformy Apache Spark z platformą Apache Kafka w HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Używanie systemu Apache Storm z platformą Apache Kafka w HDInsight](../hdinsight-apache-storm-with-kafka.md)
