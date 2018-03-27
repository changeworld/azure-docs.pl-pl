---
title: Wprowadzenie do platformy Apache Kafka — usługa Azure HDInsight | Microsoft Docs
description: Dowiedz się, jak utworzyć klaster Apache Kafka w usłudze Azure HDInsight. Dowiedz się, jak utworzyć tematy, subskrybentów i użytkowników.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 27e6472480dac104de799ebf0e7579a7987f6c4c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Wprowadzenie do platformy Apache Kafka w usłudze HDInsight

Dowiedz się, jak utworzyć klaster [Apache Kafka](https://kafka.apache.org) w usłudze Azure HDInsight i używać go. Kafka to rozproszona platforma przesyłania strumieniowego typu „open source”, dostępna z usługą HDInsight. Jest ona często używana jako broker komunikatów, ponieważ oferuje funkcje podobne do kolejki komunikatów dotyczących publikowania i subskrybowania. Platforma Kafka jest często używana z platformami Apache Spark i Apache Storm do obsługi wiadomości, śledzenia działań, agregacji strumienia lub transformacji danych.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Tworzenie klastra platformy Kafka

Aby utworzyć klaster platformy Kafka w usłudze HDInsight, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **+ Utwórz zasób** i pozycję **Dane + analiza**, a następnie pozycję **HDInsight**.
   
    ![Tworzenie klastra usługi HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. W bloku **Podstawowe** wprowadź następujące informacje:

    * **Nazwa klastra**: nazwa klastra usługi HDInsight. Ta nazwa musi być unikatowa.
    * **Subskrypcja**: wybierz subskrypcję, której chcesz użyć.
    * **Typ klastra**: wybierz tę pozycję, a następnie ustaw następujące wartości z bloku **Konfiguracja klastra**:

        * **Typ klastra**: Kafka
        * **Wersja**: Kafka 0.10.0 (HDI 3.6)

        Użyj przycisku **Wybierz**, aby zapisać ustawienia typu klastra.

        ![Wybieranie typu klastra](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

    * **Nazwa użytkownika logowania klastra** i **Hasło logowania klastra**: dane logowania podczas uzyskiwania dostępu do klastra przy użyciu protokołu HTTPS. Te poświadczenia umożliwiają dostęp do takich usług jak interfejs użytkownika sieci Web Ambari lub interfejs API REST.
    * **Nazwa użytkownika SSH (Secure Shell)**: nazwa logowania używana podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH. Domyślnie hasło jest takie samo jak hasło logowania klastra.
    * **Grupa zasobów**: grupa zasobów, w której ma zostać utworzony klaster.
    * **Lokalizacja**: region platformy Azure, w którym ma zostać utworzony klaster.

        > [!IMPORTANT]
        > Aby zapewnić wysoką dostępność danych, zalecamy wybranie lokalizacji (regionu), który zawiera __trzy domeny błędów__. Aby uzyskać więcej informacji, zobacz sekcję [Wysoka dostępność danych](#data-high-availability).
   
 ![Wybieranie subskrypcji](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Zakończ konfigurację podstawową za pomocą przycisku __Dalej__.

4. W bloku **Magazyn** wybierz lub utwórz konto magazynu. Na potrzeby procedury w tym dokumencie pozostaw wartości domyślne w pozostałych polach. Zapisz konfigurację magazynu za pomocą przycisku __Dalej__.

    ![Konfigurowanie ustawień konta magazynu dla usługi HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

5. Opcjonalnie w bloku __Aplikacje__ wybierz przycisk __Dalej__, aby kontynuować. W tym przykładzie nie są wymagane żadne aplikacje.

6. W bloku __Rozmiar klastra__ wybierz przycisk __Dalej__, aby kontynuować.

    > [!WARNING]
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Aby uzyskać więcej informacji, zobacz sekcję [Wysoka dostępność danych](#data-high-availability).

    ![Ustawianie rozmiaru klastra Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > Parametr **liczby dysków na węzeł procesu roboczego** pozwala konfigurować skalowalność platformy Kafka w usłudze HDInsight. Platforma Kafka w usłudze HDInsight używa dysku lokalnego maszyn wirtualnych w klastrze. Ze względu na duże obciążenie we/wy platformy Kafka używana jest usługa [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md), która zapewnia wysoką przepływność i więcej miejsca do magazynowania w każdym węźle. Można wybrać typ dysku zarządzanego __Standardowy__ (HDD) lub __Premium__ (SSD). Dyski w warstwie Premium są używane przez maszyny wirtualne serii DS i GS. Wszystkie pozostałe typy maszyn wirtualnych korzystają z dysków standardowych.

7. W bloku __Ustawienia zaawansowane__ wybierz przycisk __Dalej__, aby kontynuować.

8. W bloku **Podsumowanie** przejrzyj konfigurację klastra. Zmień niepoprawne ustawienia przy użyciu linków __Edytuj__. Utwórz klaster przy użyciu przycisku __Utwórz__.
   
    ![Podsumowanie konfiguracji klastra](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Tworzenie klastra może potrwać do 20 minut.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

> [!IMPORTANT]
> Wykonując poniższe kroki, musisz użyć klienta SSH. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Aby nawiązać połączenie z klastrem przy użyciu protokołu SSH, musisz podać nazwę konta użytkownika protokołu SSH i nazwę swojego klastra. W poniższym przykładzie zastąp wartości `sshuser` i `clustername` nazwą swojego konta i klastra:

```ssh sshuser@clustername-ssh.azurehdinsight.net```

Po wyświetleniu monitu wprowadź hasło, którego użyto dla konta SSH.

Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Uzyskiwanie informacji dotyczących hosta dozorcy i brokera

Podczas pracy z platformą Kafka musisz znać hosty *Zookeeper* i *Broker*. Te hosty są używane z interfejsem API platformy Kafka i wieloma narzędziami oferowanymi z platformą Kafka.

Poniżej przedstawiono procedurę tworzenia zmiennych środowiskowych z informacjami o hoście:

1. Z poziomu połączenia SSH z klastrem zainstaluj narzędzie `jq` za pomocą poniższego polecenia. To narzędzie służy do analizowania dokumentów JSON i jest przydatne podczas pobierania informacji o hoście brokera:
   
    ```bash
    sudo apt -y install jq
    ```

2. Aby ustawić zmienną środowiskową na nazwę klastra, użyj następującego polecenia:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

3. Aby ustawić zmienną środowiskową na informacje hosta Zookeeper, użyj następującego polecenia:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

4. Aby sprawdzić, czy zmienna środowiskowa jest poprawnie ustawiona, użyj następującego polecenia:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Aby ustawić zmienną środowiskową na informacje hosta brokera platformy Kafka, użyj następującego polecenia:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

6. Aby sprawdzić, czy zmienna środowiskowa jest poprawnie ustawiona, użyj następującego polecenia:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
> [!WARNING]
> Informacje zwracane z tej sesji nie zawsze są dokładne. Podczas skalowania klastra następuje dodanie lub usunięcie nowych brokerów. W przypadku niepowodzenia lub zamiany węzła nazwa hosta węzła może ulec zmianie.
>
> Pobierz informacje o hostach Zookeeper i Broker na krótko przed sprawdzeniem, czy masz prawidłowe informacje.

## <a name="create-a-topic"></a>Tworzenie tematu

Platforma Kafka przechowuje strumienie danych w kategoriach nazywanych *tematami*. Z poziomu połączenia SSH z węzłem głównym klastra utwórz temat za pomocą skryptu dostarczonego z platformą Kafka:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

To polecenie umożliwia nawiązanie połączenia z hostem Zookeeper przy użyciu informacji o hoście przechowywanych w elemencie `$KAFKAZKHOSTS`. Następnie tworzy ono temat platformy Kafka o nazwie **test**. Aby sprawdzić, czy temat został utworzony, użyj następującego skryptu w celu wyświetlenia listy tematów:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

Dane wyjściowe tego polecenia zawierają listę tematów platformy Kafka w klastrze.

## <a name="produce-and-consume-records"></a>Tworzenie i używanie rekordów

Platforma Kafka przechowuje *rekordy* w tematach. Rekordy są tworzone przez *producentów* i używane przez *odbiorców*. Producenci tworzą rekordy dla *brokerów* platformy Kafka. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest brokerem platformy Kafka.

Poniżej przedstawiono procedurę zapisywania rekordów w utworzonym wcześniej temacie testowym i odczytywania ich za pomocą odbiorcy:

1. Z poziomu sesji SSH zapisz rekordy w temacie przy użyciu skryptu dostarczonego z platformą Kafka:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Po wykonaniu tego polecenia przejdziesz do pustego wiersza.

2. Wprowadź wiadomość tekstową do pustego wiersza, a następnie naciśnij klawisz Enter. Wprowadź w ten sposób kilka wiadomości, a następnie użyj kombinacji klawiszy **Ctrl + C**, aby powrócić do normalnego monitu. Każdy wiersz jest wysyłany do tematu platformy Kafka jako oddzielny rekord.

3. Odczytaj rekordy z tematu przy użyciu skryptu dostarczonego z platformą Kafka:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    To polecenie umożliwia pobranie rekordów z tematu i ich wyświetlenie. Polecenie `--from-beginning` informuje odbiorcę, aby rozpocząć od początku strumienia w celu pobrania wszystkich rekordów.

    > [!NOTE]
    > Jeśli korzystasz ze starszej wersji platformy Kafka, zastąp element `--bootstrap-server $KAFKABROKERS` elementem `--zookeeper $KAFKAZKHOSTS`.

4. Użyj klawiszy __Ctrl+C__, aby zatrzymać odbiorcę.

Producentów i odbiorców można również utworzyć programowo. Przykład korzystania z tego interfejsu API znajduje się w dokumencie [Kafka Producer and Consumer API with HDInsight](apache-kafka-producer-consumer-api.md) (Interfejs API producenta i odbiorcy platformy Kafka w usłudze HDInsight).

## <a name="data-high-availability"></a>Wysoka dostępność danych

Każdy region (lokalizacja) świadczenia usługi Azure udostępnia _domeny błędów_. Domena błędów to logiczna grupa bazowego sprzętu w centrum danych platformy Azure. Wszystkie domeny błędów korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Maszyny wirtualne i dyski zarządzane, które implementują węzły w klastrze usługi HDInsight są rozdzielone między te domeny błędów. Taka architektura ogranicza wpływ potencjalnych awarii sprzętu fizycznego.

Aby uzyskać informacje dotyczące liczby domen błędów w regionie, zobacz dokument [Availability of Linux virtual machines (Dostępność maszyn wirtualnych z systemem Linux)](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> W miarę możliwości używaj regionu świadczenia usługi Azure zawierającego trzy domeny błędów oraz twórz tematy mające współczynnik replikacji równy 3.

Jeśli używasz regionu, który zawiera tylko dwie domeny błędów, użyj współczynnika replikacji o wartości 4, aby równomiernie rozłożyć repliki na dwie domeny błędów.

### <a name="kafka-and-fault-domains"></a>Platforma Kafka i domeny błędów

Platforma Kafka nie uwzględnia domen błędów. Utworzone repliki partycji tematów mogą nie zostać prawidłowo rozpowszechnione w celu zapewnienia wysokiej dostępności. Aby zapewnić wysoką dostępność, użyj [narzędzia do ponownego równoważenia partycji platformy Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Narzędzie to należy uruchomić w sesji połączenia SSH z węzłem głównym klastra Kafka.

Aby zapewnić najwyższą dostępność danych na platformie Kafka, należy stosować ponowne równoważenie replik partycji dla tematu, gdy:

* Tworzysz nowy temat lub partycję

* Skalujesz klaster w górę

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono podstawowe informacje dotyczące pracy z platformą Apache Kafka w usłudze HDInsight. Dowiedz się więcej o pracy z platformą Kafka, korzystając z następujących zasobów:

* [Analizowanie dzienników Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicate data between Kafka clusters (Replikowanie danych między klastrami Kafka)](apache-kafka-mirroring.md)
* [Kafka Producer and Consumer API with HDInsight](apache-kafka-producer-consumer-api.md) (Interfejs API producenta i odbiorcy platformy Kafka w usłudze HDInsight).
* [Kafka Streams API with HDInsight](apache-kafka-streams-api.md) (Interfejs API strumieni platformy Kafka w usłudze HDInsight)
* [Use Apache Spark streaming (DStream) with Kafka on HDInsight (Używanie strumieni (DStream) platformy Apache Spark z platformą Kafka w usłudze HDInsight)](../hdinsight-apache-spark-with-kafka.md)
* [Use Apache Spark Structured Streaming with Kafka on HDInsight (Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight)](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Use Apache Spark Structured Streaming to move data from Kafka on HDInsight to Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md) (Korzystanie z przesyłania strumieniowego ze strukturą w systemie Apache Spark w celu przenoszenia danych z platformy Kafka w usłudze HDInsight do usługi Cosmos DB)
* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Nawiązywanie połączenia z platformą Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
* [Używanie usługi Kafka z usługą Azure Container Service](apache-kafka-azure-container-services.md)
* [Używanie usługi Kafka z aplikacjami funkcji platformy Azure](apache-kafka-azure-functions.md)
