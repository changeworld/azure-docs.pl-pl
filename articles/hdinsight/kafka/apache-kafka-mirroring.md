---
title: Lustrzane tematy Apache Kafka — Azure HDInsight
description: Dowiedz się, jak używać funkcji dublowania platformy Apache Kafka do obsługi repliki platformy Kafka w klastrze USŁUGI HDInsight przez dublowanie tematów w klastrze pomocniczym.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425888"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Replikowanie tematów platformy Apache Kafka przy użyciu platformy Kafka w usłudze HDInsight za pomocą narzędzia MirrorMaker

Dowiedz się, jak używać funkcji dublowania apache Kafka do replikowania tematów do klastra pomocniczego. Dublowanie można przeprowadzić jako proces ciągły lub być używane sporadycznie jako metoda migracji danych z jednego klastra do drugiego.

W tym przykładzie dublowanie jest używane do replikowania tematów między dwoma klastrami HDInsight. Oba klastry znajdują się w różnych sieciach wirtualnych w różnych centrach danych.

> [!WARNING]  
> Dublowanie nie powinno być traktowane jako środek do osiągnięcia odporności na uszkodzenia. Przesunięcie do elementów w temacie różnią się między klastrów podstawowych i pomocniczych, więc klienci nie mogą używać dwóch zamiennie.
>
> Jeśli obawiasz się odporności na uszkodzenia, należy ustawić replikację dla tematów w klastrze. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do apache kafka w programie HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak działa mirroring Apache Kafka

Dublowanie działa przy użyciu narzędzia [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (część Apache Kafka) do korzystania z rekordów z tematów w klastrze podstawowym, a następnie utworzyć kopię lokalną w klastrze pomocniczym. MirrorMaker używa jednego (lub więcej) *konsumentów,* którzy czytają z klastra podstawowego i *producenta,* który zapisuje do lokalnego (pomocniczego) klastra.

Najbardziej przydatne dublowanie konfiguracji odzyskiwania po awarii wykorzystuje klastry platformy Kafka w różnych regionach platformy Azure. Aby to osiągnąć, sieci wirtualne, w których znajdują się klastry są równorzędne.

Na poniższym diagramie przedstawiono proces dublowania i sposób przepływu komunikacji między klastrami:

![Diagram procesu dublowania](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Klastry podstawowe i pomocnicze mogą być różne pod względem liczby węzłów i partycji, a przesunięcia w obrębie tematów są również różne. Dublowanie zachowuje wartość klucza, który jest używany do partycjonowania, więc kolejność rekordów jest zachowywany na podstawie na klucz.

### <a name="mirroring-across-network-boundaries"></a>Dublowanie między granicami sieci

Jeśli konieczne jest tworzenie kopii lustrzanych między klastrami platformy Kafka w różnych sieciach, istnieją następujące dodatkowe zagadnienia:

* **Bramy:** Sieci muszą być w stanie komunikować się na poziomie TCP/IP.

* **Adresowanie serwera:** Można zaa addressować węzły klastra przy użyciu ich adresów IP lub w pełni kwalifikowanych nazw domen.

    * **Adresy IP:** Jeśli klastry platformy Kafka skonfigurują do używania reklam adresów IP, możesz kontynuować konfigurację dublowania przy użyciu adresów IP węzłów brokera i węzłów zookeeper.
    
    * **Nazwy domen:** Jeśli klastry platformy Kafka nie są skonfigurowane pod kątem reklam adresów IP, klastry muszą mieć możliwość łączenia się ze sobą przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Wymaga to serwera DNS (Domain Name System) w każdej sieci skonfigurowanego do przesyłania dalej żądań do innych sieci. Podczas tworzenia sieci wirtualnej platformy Azure, zamiast automatycznego dns dostarczanego z siecią, należy określić niestandardowy serwer DNS i adres IP serwera. Po utworzeniu sieci wirtualnej należy utworzyć maszynę wirtualną platformy Azure, która używa tego adresu IP, a następnie zainstalować i skonfigurować oprogramowanie DNS na nim.

    > [!WARNING]  
    > Utwórz i skonfiguruj niestandardowy serwer DNS przed zainstalowaniem usługi HDInsight w sieci wirtualnej. Nie ma potrzeby korzystania z serwera DNS skonfigurowanym dla sieci wirtualnej.

Aby uzyskać więcej informacji na temat łączenia dwóch sieci wirtualnych platformy Azure, zobacz [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architektura lustrzana

Ta architektura zawiera dwa klastry w różnych grupach zasobów i sieci wirtualne: **podstawowy** i **pomocniczy**.

### <a name="creation-steps"></a>Kroki tworzenia

1. Utwórz dwie nowe grupy zasobów:

    |Grupa zasobów | Lokalizacja |
    |---|---|
    | kafka-primary-rg | Środkowe stany USA |
    | kafka-secondary-rg | Północno-środkowe stany USA |

1. Utwórz nową sieć wirtualną **kafka-primary-vnet** w **kafka-primary-rg**. Pozostaw ustawienia domyślne.
1. Utwórz nową sieć wirtualną **kafka-secondary-vnet** w **kafka-secondary-rg**, również z ustawieniami domyślnymi.

1. Utwórz dwa nowe klastry platformy Kafka:

    | Nazwa klastra | Grupa zasobów | Virtual Network | Konto magazynu |
    |---|---|---|---|
    | kafka-podstawowy klaster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-pomocniczy-klaster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Tworzenie komunikacji równorzędnej sieci wirtualnej. W tym kroku utworzy się dwie komunikacji równorzędnej: jedną z **kafka-primary-vnet** do **kafka-secondary-vnet** i jedną z powrotem z **kafka-secondary-vnet** do **kafka-primary-vnet**.
    1. Wybierz sieć wirtualną **kafka-primary-vnet.**
    1. Wybierz **pozycję Komunikacja równorzędna** w **obszarze Ustawienia**.
    1. Wybierz pozycję **Dodaj**.
    1. Na ekranie **Dodaj komunikację równorzędną** wprowadź szczegóły, jak pokazano na poniższym zrzucie ekranu.

        ![HdInsight Kafka dodać komunikację równorzędnych sieci wirtualnych](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Konfigurowanie reklam IP

Skonfiguruj reklamy IP, aby umożliwić klientowi łączenie się przy użyciu adresów IP brokera zamiast nazw domen.

1. Przejdź do pulpitu nawigacyjnego `https://PRIMARYCLUSTERNAME.azurehdinsight.net`Ambari dla klastra podstawowego: .
1. Wybierz **pozycję Usługi** > **Kafka**. CliSelectck kartę **Configs.**
1. Dodaj następujące wiersze konfiguracji do dolnej sekcji **szablonu kafka-env.** Wybierz **pozycję Zapisz**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Wprowadź notatkę na ekranie **Zapisz konfigurację** i kliknij przycisk **Zapisz**.
1. Jeśli zostanie wyświetlony monit z ostrzeżeniem o konfiguracji, kliknij przycisk **Kontynuuj mimo wszystko**.
1. Wybierz **pozycję Ok** w obszarze Zapisz zmiany **konfiguracji**.
1. Wybierz **pozycję Uruchom ponownie** > **wszystkie, których dotyczy problem,** w powiadomieniu **Wymagane ponowne uruchomienie.** Wybierz **pozycję Potwierdź ponownie wszystkie**.

    ![Apache Ambari ponownie uruchomić wszystkie dotknięte](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Skonfiguruj platformę Kafka do nasłuchiwać na wszystkich interfejsach sieciowych.
    
1. Pozostań na karcie **Configs** w obszarze **Usługi** > **Kafka**. W sekcji **Kafka Broker** ustaw właściwość **słuchaczy** na `PLAINTEXT://0.0.0.0:9092`.
1. Wybierz **pozycję Zapisz**.
1. Wybierz pozycję **Uruchom ponownie**i **potwierdź ponowne uruchomienie wszystkich**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Adresy IP brokera rekordów i adresy Zookeeper dla klastra podstawowego.

1. Wybierz **pozycję Hosty** na pulpicie nawigacyjnym Ambari.
1. Zanotuj adresy IP dla brokerów i zookeeperów. Węzły brokera mają **wn** jako pierwsze dwie litery nazwy hosta, a węzły zookeeper mają **zk** jako pierwsze dwie litery nazwy hosta.

    ![Adresy IP węzła widoku Apache Ambari](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Powtórz poprzednie trzy kroki dla drugiego **klastra kafka-secondary-cluster:** skonfiguruj reklamy IP, ustaw detektory i zanotuj adresy IP brokera i zookeepera.

## <a name="create-topics"></a>Tworzenie tematów

1. Połącz się z klasterem **podstawowym** przy użyciu funkcji SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** nazwą użytkownika SSH używaną podczas tworzenia klastra. Zastąp **PRIMARYCLUSTER** nazwą podstawową używaną podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Użyj następującego polecenia, aby utworzyć zmienną z hostami Apache Zookeeper dla klastra podstawowego. Ciągi takie `ZOOKEEPER_IP_ADDRESS1` jak muszą być zastąpione rzeczywistymi `10.23.0.11` adresami IP zarejestrowanymi wcześniej, takimi jak i `10.23.0.7`. Jeśli używasz rozpoznawania FQDN z niestandardowym serwerem DNS, wykonaj [następujące kroki,](apache-kafka-get-started.md#getkafkainfo) aby uzyskać nazwy brokera i zookeepera.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Aby utworzyć temat `testtopic`o nazwie , użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Aby sprawdzić, czy temat został utworzony, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Odpowiedź zawiera `testtopic`.

1. Aby wyświetlić informacje o hoście Zookeeper, służy do wyświetlania następujących informacji o tym **(podstawowym)** klastrze:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    To zwraca informacje podobne do następującego tekstu:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Zapisz te informacje. Jest on używany w następnej sekcji.

## <a name="configure-mirroring"></a>Konfigurowanie dublowania

1. Połącz się z klasterem **pomocniczym** przy użyciu innej sesji SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** nazwą użytkownika SSH używaną podczas tworzenia klastra. Zamień **SECONDARYCLUSTER** na nazwę używaną podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Plik `consumer.properties` służy do konfigurowania komunikacji z klastrem **podstawowym.** Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano consumer.properties
    ```

    Użyj następującego tekstu jako `consumer.properties` zawartości pliku:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Zastąp **PRIMARY_ZKHOSTS** adresami IP zookeeper z klastra **podstawowego.**

    Ten plik opisuje informacje o konsumentach, których należy użyć podczas odczytywania z podstawowego klastra platformy Kafka. Aby uzyskać więcej informacji konfiguracji konsumenta, zobacz [Konfiguracje konsumentów](https://kafka.apache.org/documentation#consumerconfigs) w kafka.apache.org.

    Aby zapisać plik, użyj **klawiszy Ctrl + X**, **Y**, a następnie **wprowadź**.

1. Przed skonfigurowaniem producenta, który komunikuje się z klastrem pomocniczym, należy skonfigurować zmienną dla adresów IP brokera klastra **pomocniczego.** Aby utworzyć tę zmienną, użyj następujących poleceń:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Polecenie `echo $SECONDARY_BROKERHOSTS` powinno zwracać informacje podobne do następującego tekstu:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Plik `producer.properties` jest używany do komunikowania klastra **pomocniczego.** Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano producer.properties
    ```

    Użyj następującego tekstu jako `producer.properties` zawartości pliku:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Zastąp **SECONDARY_BROKERHOSTS** adresami IP brokera używanymi w poprzednim kroku.

    Aby uzyskać więcej informacji o konfiguracji producenta, zobacz [Konfiguracje producentów](https://kafka.apache.org/documentation#producerconfigs) w kafka.apache.org.

1. Użyj następujących poleceń, aby utworzyć zmienną środowiskową z adresami IP hostów Zookeeper dla klastra pomocniczego:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Domyślna konfiguracja platformy Kafka w programie HDInsight nie zezwala na automatyczne tworzenie tematów. Przed rozpoczęciem procesu dublowania należy użyć jednej z następujących opcji:

    * **Tworzenie tematów w klastrze pomocniczym:** Ta opcja umożliwia również ustawienie liczby partycji i współczynnika replikacji.

        Tematy można tworzyć z wyprzedzeniem za pomocą następującego polecenia:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Zamień `testtopic` nazwę tematu do utworzenia.

    * **Skonfiguruj klaster do automatycznego tworzenia tematów:** Ta opcja umożliwia programowi MirrorMaker automatyczne tworzenie tematów, jednak może tworzyć je z inną liczbą partycji lub współczynnikiem replikacji niż temat podstawowy.

        Aby skonfigurować klaster pomocniczy do automatycznego tworzenia tematów, wykonaj następujące czynności:

        1. Przejdź do pulpitu nawigacyjnego `https://SECONDARYCLUSTERNAME.azurehdinsight.net`Ambari dla klastra pomocniczego: .
        1. Kliknij **pozycję Usługi** > **Kafka**. Kliknij kartę **Konfiguracje.**
        1. W polu __Filtr__ wprowadź wartość `auto.create`. Spowoduje to filtrowanie listy właściwości `auto.create.topics.enable` i wyświetlenie tego ustawienia.
        1. Zmień wartość `auto.create.topics.enable` true, a następnie wybierz pozycję __Zapisz__. Dodaj notatkę, a następnie wybierz pozycję __Zapisz__ ponownie.
        1. Wybierz usługę __Kafka,__ wybierz pozycję __Uruchom ponownie__, a następnie wybierz pozycję __Uruchom ponownie wszystkie, których dotyczy problem.__ Po wyświetleniu monitu wybierz pozycję __Potwierdź ponownie wszystkie__.

        ![kafka włączyć automatyczne tworzenie tematów](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Uruchamianie mirrormaker

1. Z połączenia SSH do klastra **pomocniczego** użyj następującego polecenia, aby rozpocząć proces MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry użyte w tym przykładzie to:

    |Parametr |Opis |
    |---|---|
    |--consumer.config|Określa plik zawierający właściwości konsumenta. Te właściwości są używane do tworzenia konsumenta, który odczytuje z *podstawowego* klastra platformy Kafka.|
    |--producer.config|Określa plik zawierający właściwości producenta. Te właściwości są używane do tworzenia producenta, który zapisuje do *pomocniczego* klastra platformy Kafka.|
    |--biała lista|Lista tematów, które MirrorMaker replikuje z klastra podstawowego do pomocniczego.|
    |--num.streams|Liczba wątków konsumenta do utworzenia.|

    Konsument w węźle pomocniczym oczekuje teraz na odebranie wiadomości.

2. Z połączenia SSH do klastra **podstawowego** użyj następującego polecenia, aby uruchomić producenta i wysłać wiadomości do tematu:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Po dotarciu do pustego wiersza z kursorem wpisz kilka wiadomości tekstowych. Wiadomości są wysyłane do tematu w klastrze **podstawowym.** Po zakończeniu użyj **klawiszy Ctrl + C,** aby zakończyć proces produkcji.

3. Z połączenia SSH do klastra **pomocniczego** użyj **ctrl + C,** aby zakończyć proces MirrorMaker. Może upłynąć kilka sekund, aby zakończyć proces. Aby sprawdzić, czy wiadomości zostały zreplikowane do pomocniczego, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Lista tematów zawiera `testtopic`teraz , który jest tworzony, gdy MirrorMaster dubluje temat z klastra podstawowego do pomocniczego. Wiadomości pobrane z tematu są takie same, jak te wprowadzone w klastrze podstawowym.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Kroki w tym dokumencie utworzone klastry w różnych grupach zasobów platformy Azure. Aby usunąć wszystkie utworzone zasoby, można usunąć dwie utworzone grupy zasobów: **kafka-primary-rg** i **kafka-secondary_rg**. Usunięcie grup zasobów powoduje usunięcie wszystkich zasobów utworzonych przez obserwowanie tego dokumentu, w tym klastrów, sieci wirtualnych i kont magazynu.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie opisano, jak używać [mirrormaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do tworzenia repliki klastra [Apache Kafka.](https://kafka.apache.org/) Skorzystaj z następujących linków, aby poznać inne sposoby pracy z platformą Kafka:

* [Apache Kafka MirrorMaker dokumentacji](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) w cwiki.apache.org.
* [Kafka Mirror Maker – najważniejsze wskazówki](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Wprowadzenie do apache Kafka w programie HDInsight](apache-kafka-get-started.md)
* [Użyj Apache Spark z Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Łączenie się z platformą Apache Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
