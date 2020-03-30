---
title: 'Szybki start: konfigurowanie platformy Apache Kafka w usłudze HDInsight przy użyciu witryny Azure portal'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster platformy Apache Kafka w usłudze Azure HDInsight przy użyciu witryny Azure Portal. Zdobędziesz także informacje o tematach, subskrybentach i odbiorcach platformy Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/24/2020
ms.openlocfilehash: 90f7010970f70379c8adecc4214c44d896a1beaf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130262"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki start: tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu witryny Azure portal

[Apache Kafka](./apache-kafka-introduction.md) to platforma streamingowa typu open source, rozproszona. Jest ona często używana jako broker komunikatów, ponieważ oferuje funkcje podobne do kolejki komunikatów dotyczących publikowania i subskrybowania.

W tym samouczku szybkiego startu dowiesz się, jak utworzyć klaster platformy Apache Kafka przy użyciu witryny Azure Portal. Dowiesz się także, jak używać dołączonych narzędzi do wysyłania i odbierania komunikatów na platformie Apache Kafka. Aby uzyskać szczegółowe objaśnienia dostępnych konfiguracji, zobacz [Konfigurowanie klastrów w programie HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać dodatkowe informacje dotyczące używania portalu do tworzenia klastrów, zobacz [Tworzenie klastrów w portalu](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Dostęp do interfejsu API platformy Apache Kafka mogą uzyskać tylko zasoby będące w tej samej sieci wirtualnej. W tym przewodniku Szybki start uzyskasz dostęp do klastra bezpośrednio przy użyciu protokołu SSH. Aby do platformy Apache Kafka podłączyć inne usługi, sieci lub maszyny wirtualne, należy najpierw utworzyć sieć wirtualną, a następnie utworzyć zasoby w obrębie tej sieci. Aby uzyskać więcej informacji, zobacz dokument [Connect to Apache Kafka using a virtual network (Nawiązywanie połączenia z platformą Apache Kafka za pomocą sieci wirtualnej)](apache-kafka-connect-vpn-gateway.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Tworzenie klastra platformy Apache Kafka

Aby utworzyć klaster platformy Apache Kafka w programie HDInsight, należy wykonać następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Z górnego menu wybierz pozycję **+ Utwórz zasób**.

    ![Portal Azure tworzenie zasobu HDInsight](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Wybierz **Analytics** > **Azure HDInsight,** aby przejść do strony **Tworzenie klastra HDInsight.**

1. Na karcie **Podstawy** podaj następujące informacje:

    |Właściwość  |Opis  |
    |---------|---------|
    |Subskrypcja    |  Z listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra. |
    |Grupa zasobów     | Utwórz grupę zasobów lub wybierz istniejącą grupę zasobów.  Grupa zasobów jest kontenerem składników platformy Azure.  W tym przypadku grupa zasobów zawiera klaster usługi HDInsight i zależne konto usługi Azure Storage. |
    |Nazwa klastra   | Podaj globalnie unikatową nazwę. Nazwa może składać się z maksymalnie 59 znaków, w tym liter, cyfr i łączników. Łącznik nie może być pierwszym ani ostatnim znakiem nazwy. |
    |Region    | Z listy rozwijanej wybierz region, w którym jest tworzony klaster.  Wybierz region bliżej Ciebie, aby uzyskać lepszą wydajność. |
    |Typ klastra| Wybierz **wybierz wybierz typ klastra,** aby otworzyć listę. Z listy wybierz pozycję **Kafka** jako typ klastra.|
    |Wersja|Zostanie określona wersja domyślna dla typu klastra. Wybierz z listy rozwijanej, jeśli chcesz określić inną wersję.|
    |Nazwa użytkownika i hasło logowania do klastra    | Domyślną nazwą logowania jest **admin**. Hasło musi mieć co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką literę i jedną wielką literę, jedną znak niealtalumeryczny (z wyjątkiem znaków " " \). Upewnij się, że **nie zostało podane** typowe hasło, takie jak „Pass@word1”.|
    |Nazwa użytkownika protokołu SSH (Secure Shell) | Domyślna nazwa użytkownika to **sshuser**.  Możesz podać inną nazwę użytkownika protokołu SSH. |
    |Używanie hasła logowania klastra dla SSH| Zaznacz to pole wyboru, aby używać tego samego hasła dla użytkownika SSH, które zostało podane dla użytkownika logowania do klastra.|

   ![Tworzenie podstaw klastra w portalu Azure](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Każdy region (lokalizacja) świadczenia usługi Azure udostępnia _domeny błędów_. Domena błędów to logiczna grupa bazowego sprzętu w centrum danych platformy Azure. Wszystkie domeny błędów korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Maszyny wirtualne i dyski zarządzane, które implementują węzły w klastrze usługi HDInsight są rozdzielone między te domeny błędów. Taka architektura ogranicza wpływ potencjalnych awarii sprzętu fizycznego.

    Aby zapewnić wysoką dostępność danych, wybierz region (lokalizację), który zawiera __trzy domeny błędów__. Aby uzyskać informacje dotyczące liczby domen błędów w regionie, zobacz dokument [Availability of Linux virtual machines (Dostępność maszyn wirtualnych z systemem Linux)](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

    Wybierz kartę **Dalej: >>magazynowania,** aby przejść do ustawień magazynu.

1. Na karcie **Magazyn** podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wybierania|Użyj wartości domyślnej **Wybierz z listy**.|
    |Konto magazynu podstawowego|Użyj listy rozwijanej, aby wybrać istniejące konto magazynu, lub wybierz pozycję **Utwórz nowe**. Jeśli utworzysz nowe konto, nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.|
    |Kontener|Użyj wartości autopopulowanej.|

    ![Wprowadzenie systemu HDInsight Linux zapewnia wartości pamięci masowej klastra](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Podaj wartości pamięci masowej do tworzenia klastra HDInsight")

    Wybierz kartę **Zabezpieczenia + sieć.**

1. Na potrzeby tego przewodnika Szybki start pozostaw domyślne ustawienia zabezpieczeń. Aby dowiedzieć się więcej o pakiecie Enterprise Security, odwiedź stronę [Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services (Konfigurowanie klastra usługi HDInsight z pakietem Enterprise Security przy użyciu usług Azure Active Directory Domain Services)](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Aby dowiedzieć się, jak używać własnego klucza do szyfrowania dysku Apache Kafka, odwiedź stronę [Szyfrowanie dysków kluczy zarządzanych przez klienta](../disk-encryption.md)

   Jeśli chcesz połączyć klaster z siecią wirtualną, wybierz sieć wirtualną z listy rozwijanej **Sieć wirtualna**.

   ![Dodawanie klastra do sieci wirtualnej](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Wybierz kartę **Konfiguracja + cennik.**

1. Aby zagwarantować dostępność apache kafka w programie HDInsight, __liczba węzłów__ wpis dla **węzła worker** musi być ustawiona na 3 lub więcej. Wartość domyślna to 4.

    Wpis **węzła Standardowe dyski na proces roboczy** konfiguruje skalowalność programu Apache Kafka w programie HDInsight. Do przechowywania danych platforma Apache Kafka w usłudze HDInsight używa dysku lokalnego maszyn wirtualnych w klastrze. Ze względu na duże obciążenie we/wy platformy Apache Kafka używana jest funkcja [Dyski zarządzane platformy Azure](../../virtual-machines/windows/managed-disks-overview.md), która zapewnia wysoką przepływność i więcej miejsca do magazynowania w każdym węźle. Można wybrać typ dysku zarządzanego __Standardowy__ (HDD) lub __Premium__ (SSD). Typ dysku zależy od rozmiaru maszyny wirtualnej używanej przez węzły procesu roboczego (brokery platformy Apache Kafka). Dyski w warstwie Premium są używane automatycznie przez maszyny wirtualne serii DS i GS. Wszystkie pozostałe typy maszyn wirtualnych korzystają z dysków standardowych.

   ![Ustawianie rozmiaru klastra platformy Apache Kafka](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Wybierz kartę **Recenzja + utwórz.**

1. Przejrzyj konfigurację klastra. Zmień wszystkie nieprawidłowe ustawienia. Na koniec wybierz pozycję **Utwórz,** aby utworzyć klaster.

    ![podsumowanie konfiguracji klastra platformy kafka](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Tworzenie klastra może potrwać do 20 minut.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po wyświetleniu monitu wprowadź hasło użytkownika SSH.

    Po nawiązaniu połączenia zostanie wyświetlona informacja podobna do następującej:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Pobierz informacje dotyczących hostów Apache Zookeeper i Broker

Podczas pracy z platformą Kafka musisz znać hosty *Apache Zookeeper* i *Broker*. Te hosty są używane z interfejsem API platformy Apache Kafka i wieloma narzędziami oferowanymi z platformą Kafka.

W tej sekcji uzyskasz informacje o hoście z interfejsu API REST Apache Ambari w klastrze.

1. Zainstaluj [jq](https://stedolan.github.io/jq/), procesor JSON wiersza polecenia. To narzędzie służy do analizowania dokumentów JSON i jest przydatne podczas analizowania informacji o hoście. Z otwartego połączenia SSH wprowadź `jq`następujące polecenie, aby zainstalować:

    ```bash
    sudo apt -y install jq
    ```

1. Skonfiguruj zmienną hasła. Zamień `PASSWORD` hasło logowania klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'
    ```

1. Wyodrębnij poprawnie zmienioną nazwę klastra. Rzeczywista wielkość liter nazwy klastra może być inna niż oczekiwano, w zależności od sposobu utworzenia klastra. To polecenie uzyska rzeczywistą wielkość liter, a następnie zapisze ją w zmiennej. Wprowadź następujące polecenie:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Jeśli wykonujesz ten proces spoza klastra, istnieje inna procedura przechowywania nazwy klastra. Pobierz nazwę klastra w małe litery z witryny Azure portal. Następnie zastąp `<clustername>` nazwę klastra w następującym poleceniu i wykonaj ją: `export clusterName='<clustername>'`.


1. Aby ustawić zmienną środowiskową z informacjami o hoście Zookeeper, użyj poniższego polecenia. Polecenie pobiera wszystkie hosty Zookeeper, a następnie zwraca tylko dwa pierwsze wpisy. Taka nadmiarowość jest wymagana, jeśli jeden z hostów będzie nieosiągalny.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > To polecenie wymaga dostępu Ambari. Jeśli klaster znajduje się za sieciowej grupy sieciowej, uruchom to polecenie z komputera, który może uzyskać dostęp do ambari. 

1. Aby sprawdzić, czy zmienna środowiskowa jest poprawnie ustawiona, użyj następującego polecenia:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Aby ustawić zmienną środowiskową na informacje hosta brokera platformy Apache Kafka, użyj następującego polecenia:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > To polecenie wymaga dostępu Ambari. Jeśli klaster znajduje się za sieciowej grupy sieciowej, uruchom to polecenie z komputera, który może uzyskać dostęp do ambari. 

1. Aby sprawdzić, czy zmienna środowiskowa jest poprawnie ustawiona, użyj następującego polecenia:

    ```bash
    echo $KAFKABROKERS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Zarządzanie tematami platformy Apache Kafka

Platforma Kafka przechowuje strumienie danych w *tematach*. Tematami można zarządzać za pomocą narzędzia `kafka-topics.sh`.

* **Aby utworzyć temat**, użyj następującego polecenia, korzystając z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie umożliwia nawiązanie połączenia z hostem Zookeeper przy użyciu informacji o hoście przechowywanych w elemencie `$KAFKAZKHOSTS`. Następnie tworzy ono temat platformy Apache Kafka o nazwie **test**.

    * Dane przechowywane w tym temacie są podzielone między osiem partycji.

    * Każda partycja jest replikowana w trzech węzłach procesu roboczego w klastrze.

        Jeśli klaster został utworzony w regionie świadczenia usługi Azure, który udostępnia trzy domeny błędów, użyj współczynnika replikacji o wartości 3. W przeciwnym razie użyj współczynnika replikacji o wartości 4.
        
        W regionach z trzema domenami błędów współczynnik replikacji o wartości 3 umożliwia rozmieszczenie replik w różnych domenach błędów. W regionach z dwoma domenami błędów współczynnik replikacji o wartości cztery umożliwia równomierne rozmieszczenie replik między domenami.
        
        Aby uzyskać informacje dotyczące liczby domen błędów w regionie, zobacz dokument [Availability of Linux virtual machines (Dostępność maszyn wirtualnych z systemem Linux)](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        Platforma Apache Kafka nie uwzględnia domen błędów platformy Azure. Utworzone repliki partycji tematów mogą nie zostać prawidłowo rozpowszechnione w celu zapewnienia wysokiej dostępności.

        Aby zapewnić wysoką dostępność, użyj [narzędzia do ponownego równoważenia partycji platformy Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). To narzędzie należy uruchomić, korzystając z połączenia SSH z węzłem głównym klastra platformy Apache Kafka.

        Aby zapewnić najwyższą dostępność danych na platformie Apache Kafka, należy stosować ponowne równoważenie replik partycji dla tematu, gdy:

        * Tworzysz nowy temat lub partycję

        * Skalujesz klaster w górę

* **Aby wyświetlić listę tematów**, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie wyświetla listę dostępnych tematów w klastrze platformy Apache Kafka.

* **Aby usunąć temat**, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie usuwa temat o nazwie `topicname`.

    > [!WARNING]  
    > W przypadku usunięcia utworzonego wcześniej tematu `test` konieczne będzie jego ponowne utworzenie. Jest on używany w czynnościach opisanych w dalszej części tego dokumentu.

Aby uzyskać więcej informacji na temat poleceń dostępnych w narzędziu `kafka-topics.sh`, użyj następującego polecenia:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Tworzenie i używanie rekordów

Platforma Kafka przechowuje *rekordy* w tematach. Rekordy są tworzone przez *producentów* i używane przez *odbiorców*. Producenci i odbiorcy komunikują się z usługą *brokera platformy Kafka*. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest hostem brokera platformy Apache Kafka.

Poniżej przedstawiono procedurę zapisywania rekordów w utworzonym wcześniej temacie testowym i odczytywania ich za pomocą odbiorcy:

1. Aby zapisać rekordy w temacie, użyj narzędzia `kafka-console-producer.sh`, korzystając z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Po wykonaniu tego polecenia przejdziesz do pustego wiersza.

2. Wprowadź wiadomość tekstową do pustego wiersza, a następnie naciśnij klawisz Enter. Wprowadź w ten sposób kilka wiadomości, a następnie użyj kombinacji klawiszy **Ctrl + C**, aby powrócić do normalnego monitu. Każdy wiersz jest wysyłany do tematu platformy Apache Kafka jako oddzielny rekord.

3. Aby odczytać rekordy z tematu, użyj narzędzia `kafka-console-consumer.sh`, korzystając z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    To polecenie umożliwia pobranie rekordów z tematu i ich wyświetlenie. Polecenie `--from-beginning` informuje odbiorcę, aby rozpocząć od początku strumienia w celu pobrania wszystkich rekordów.

    Jeśli korzystasz ze starszej wersji platformy Kafka, zastąp element `--bootstrap-server $KAFKABROKERS` elementem `--zookeeper $KAFKAZKHOSTS`.

4. Użyj klawiszy __Ctrl+C__, aby zatrzymać odbiorcę.

Producentów i odbiorców można również utworzyć programowo. Przykład korzystania z tego interfejsu API znajduje się w dokumencie [Apache Kafka Producer and Consumer API with HDInsight](apache-kafka-producer-consumer-api.md) (Interfejs API producenta i odbiorcy platformy Apache Kafka w usłudze HDInsight).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku szybkiego startu, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

> [!WARNING]  
> Usunięcie klastra Platformy Kafek apache w programie HDInsight powoduje usunięcie wszystkich danych przechowywanych w programie Kafka.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Używanie systemu Apache Spark z platformą Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
