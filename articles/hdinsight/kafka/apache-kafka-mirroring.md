---
title: Takie same jak stosowane tematów platformy Apache Kafka — Azure HDInsight
description: Dowiedz się, jak za pomocą funkcji dublowania platformy Apache Kafka Obsługa repliki platformy Kafka w klastrze HDInsight Dublując tematy, aby pomocniczy klastra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657138"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Replikowanie tematów platformy Apache Kafka z platformą Kafka w HDInsight przy użyciu narzędzia MirrorMaker

Dowiedz się, jak za pomocą funkcji dublowania platformy Apache Kafka replikacja tematy pomocniczy klastra. Dublowanie mogą być uruchomione jako ciągły proces lub sporadycznie używane jako metodę migracji danych z jednego klastra do innego.

W tym przykładzie dublowania umożliwia replikowanie tematów między dwoma klastrami HDInsight. Oba klastry znajdują się w różnych sieciach wirtualnych w różnych centrach danych.

> [!WARNING]  
> Dublowanie nie być traktowane jako oznacza, że uzyskanie odporności na uszkodzenia. Przesunięcie do elementów w ramach tematu różnią się między klastrami podstawowego i pomocniczego, dzięki czemu klienci nie mogą używać dwóch zamiennie.
>
> Jeśli jesteś zajmującym się ochroną odporności na uszkodzenia, należy ustawić replikacji dla tematów w klastrze. Aby uzyskać więcej informacji, zobacz [wprowadzenie do platformy Apache Kafka w HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak działa platforma Apache Kafka dublowania

Za pomocą funkcji dublowania działa [narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) narzędzia (część platformy Apache Kafka), aby używać rekordów z tematów dotyczących klastra podstawowego, a następnie utworzyć lokalną kopię na pomocniczy klastra. Narzędzia MirrorMaker wykorzystuje jedną (lub więcej) *konsumentów* odczytanej z klastra podstawowego i *producentów* , zapisuje w lokalnym klastrze (pomocniczy).

Najbardziej przydatne dublowania instalację na potrzeby odzyskiwania po awarii korzysta z klastrów platformy Kafka w różnych regionach platformy Azure. Aby to osiągnąć, sieci wirtualnych, gdzie znajdują się te klastry równorzędnym połączeniu ze sobą.

Na poniższym diagramie przedstawiono proces dublowania i przepływ komunikacji między klastrami:

![Diagram procesu dublowania](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Podstawowe i pomocnicze klastrów może różnić się w liczbie partycji i węzłów i przesunięcia w tematach różnią się także. Dublowanie zachowuje wartość klucza, który służy do partycjonowania, więc kolejność rekordów jest zachowywany na podstawie-key.

### <a name="mirroring-across-network-boundaries"></a>Funkcja dublowania w granicach sieci

Jeśli zachodzi potrzeba utworzenia duplikatów między klastrami Kafka w różnych sieciach, istnieją dodatkowe następujące zagadnienia:

* **Bramy**: Sieci musi mieć możliwość komunikacji na poziomie protokołu TCP/IP.

* **Adresy serwera**: Można rozwiązać za pomocą ich adresów IP lub w pełni kwalifikowanych nazw domen węzłów klastra.

    * **Adresy IP**: Jeśli skonfigurujesz klastrów platformy Kafka używać anonsowanie adresów IP, możesz przejść z konfiguracją dublowania przy użyciu adresów IP węzłów brokera i węzły dozorcy.
    
    * **Nazwy domen**: Jeśli nie skonfigurujesz klastrów platformy Kafka do celów reklamowych adresu IP, klaster musi mieć możliwość łączyć się ze sobą przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN). Wymaga to serwer systemu nazw domen (DNS, Domain Name System) w każdej sieci, który jest skonfigurowany do przesyłania żądań z innymi sieciami. Podczas tworzenia usługi Azure Virtual Network, zamiast używania automatycznej DNS, wyposażone w sieci, należy określić niestandardowego serwera DNS i adres IP serwera. Po utworzeniu sieci wirtualnej, użytkownik musi następnie utwórz maszynę wirtualną platformy Azure, która używa tego adresu IP, a następnie zainstaluj i skonfiguruj oprogramowania DNS na nim.

    > [!WARNING]  
    > Utwórz i skonfiguruj niestandardowego serwera DNS przed zainstalowaniem HDInsight w sieci wirtualnej. Brak konieczności dodatkowej konfiguracji dla HDInsight służące serwer DNS skonfigurowany w ramach sieci wirtualnej.

Aby uzyskać więcej informacji na temat łączenia dwóch sieci wirtualnych platformy Azure, zobacz [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architektura dublowania

Ta architektura funkcji dwa klastry w różnych grupach zasobów i sieci wirtualnych: **podstawowego** i **dodatkowej**.

### <a name="creation-steps"></a>Kroki tworzenia

1. Utworzyć dwie nowe grupy zasobów:

    |Grupa zasobów | Location |
    |---|---|
    | kafka-primary-rg | Środkowe stany USA |
    | kafka-secondary-rg | Środkowo-północne stany USA |

1. Utwórz nową sieć wirtualną **kafka podstawowej sieci** w **kafka podstawowe rg**. Pozostaw ustawienia domyślne.
1. Utwórz nową sieć wirtualną **kafka pomocniczy sieci** w **kafka pomocnicza rg**, również przy użyciu ustawień domyślnych.

1. Tworzenie dwóch nowych klastrów Kafka:

    | Nazwa klastra | Grupa zasobów | Sieć wirtualna | Konto magazynu |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Utwórz komunikacja równorzędna sieci wirtualnych. Ten krok spowoduje utworzenie dwóch połączeń komunikacji równorzędnej: jeden z **kafka podstawowej sieci** do **kafka pomocniczy sieci** tył jeden z **kafka pomocniczy sieci** do  **Platforma kafka podstawowej sieci**.
    1. Wybierz **kafka podstawowej sieci** sieci wirtualnej.
    1. Kliknij przycisk **komunikacje równorzędne** w obszarze **ustawienia**.
    1. Kliknij przycisk **Dodaj**.
    1. Na **dodać komunikacji równorzędnej** ekranu, należy wprowadzić szczegółowe informacje, jak pokazano na poniższym zrzucie ekranu.

        ![Dodawanie komunikacji równorzędnej sieci wirtualnych](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. Skonfiguruj anonsowanie adresów IP:
    1. Przejdź do pulpitu nawigacyjnego Ambari dla klastra podstawowego: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Kliknij przycisk **usług** > **Kafka**. Kliknij przycisk **Configs** kartę.
    1. Dodaj następujące wiersze konfiguracji do dołu **szablonu kafka env** sekcji. Kliknij polecenie **Zapisz**.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Wprowadź notatki na **Zapisz konfigurację** ekranu, a następnie kliknij przycisk **Zapisz**.
    1. Jeśli zostanie wyświetlony monit z ostrzeżeniem konfiguracji, kliknij przycisk **kontynuować mimo to**.
    1. Kliknij przycisk **Ok** na **zapisać zmian konfiguracji**.
    1. Kliknij przycisk **ponowne uruchomienie** > **ponowne uruchomienie wszystkich wpływ** w **wymagane jest ponowne uruchomienie** powiadomień. Kliknij przycisk **Potwierdź ponowne uruchomienie wszystkich**.

        ![Uruchom ponownie węzły platformy kafka](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Skonfiguruj platformę Kafka do nasłuchiwania na wszystkich interfejsach sieciowych.
    1. Pozostań na **Configs** karcie **usług** > **Kafka**. W **brokera platformy Kafka** sekcji zestaw **odbiorników** właściwość `PLAINTEXT://0.0.0.0:9092`.
    1. Kliknij polecenie **Zapisz**.
    1. Kliknij przycisk **ponowne uruchomienie**, i **Potwierdź ponowne uruchomienie wszystkich**.

1. Zapisz brokera adresów i adresy dozorcy dla klastra podstawowego.
    1. Kliknij przycisk **hosty** na pulpicie nawigacyjnym systemu Ambari.
    1. Zanotuj adresów IP dla brokerów i dozorcy. Węzły brokera mają **wn** jako dwa pierwsze litery nazwy hosta i dozorcy węzły mają **zk** jako dwa pierwsze litery nazwy hosta.

        ![adresy ip w widoku](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Powtórz poprzednie trzy kroki dla drugi klaster **klastra platformy kafka — pomocniczy**: Konfigurowanie adresów IP reklamy, ustaw detektory i zwróć uwagę na adresy IP dozorcy i brokera.

## <a name="create-topics"></a>Twórz tematy

1. Połączyć się z **głównej** klastra przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** z nazwą użytkownika protokołu SSH, które są używane podczas tworzenia klastra. Zastąp **BASENAME** o nazwie podstawowej używane podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby utworzyć zmienną z hostami Apache Zookeeper dla klastra podstawowego. Ciągi, takich jak `ZOOKEEPER_IP_ADDRESS1` muszą zostać zastąpione rzeczywiste adresy IP zapisaną wcześniej, takich jak `10.23.0.11` i `10.23.0.7`. Jeśli używasz rozpoznawania nazwy FQDN przy użyciu niestandardowego serwera DNS, postępuj zgodnie z [te kroki](apache-kafka-get-started.md#getkafkainfo) można pobrać nazw brokera i dozorcy.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Aby utworzyć temat o nazwie `testtopic`, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Aby sprawdzić, czy temat został utworzony, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Odpowiedź zawiera `testtopic`.

4. Aby wyświetlić informacje hosta Zookeeper, to należy użyć następującego ( **głównej**) klastra:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    To zwraca informacje podobne do następującego tekstu:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Zapisz te informacje. Jest on używany w następnej sekcji.

## <a name="configure-mirroring"></a>Konfigurowanie funkcji dublowania

1. Połączyć się z **dodatkowej** klastrem za pomocą innej sesji protokołu SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** z nazwą użytkownika protokołu SSH, które są używane podczas tworzenia klastra. Zastąp **SECONDARYCLUSTER** z nazwę użytą podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` plik jest używany do konfigurowania komunikacji z **głównej** klastra. Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano consumer.properties
    ```

    Skorzystaj z poniższego tekstu jako zawartość `consumer.properties` pliku:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Zastąp **PRIMARY_ZKHOSTS** z adresami IP dozorcy z **głównej** klastra.

    Ten plik zawiera opis informacji klientów do użycia podczas odczytu z podstawowego klastra Kafka. Aby uzyskać więcej informacji o konfiguracji konsumenta, zobacz [Configs konsumenta](https://kafka.apache.org/documentation#consumerconfigs) na stronie kafka.apache.org.

    Aby zapisać plik, użyj **Ctrl + X**, **Y**, a następnie **Enter**.

3. Przed skonfigurowaniem producent, który komunikuje się za pomocą pomocniczy klastra, należy skonfigurować zmienną brokera adresy IP **dodatkowej** klastra. Użyj następujących poleceń do utworzenia tej zmiennej:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Polecenie `echo $SECONDARY_BROKERHOSTS` powinny zostać zwrócone informacje podobne do następującego tekstu:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. A `producer.properties` plik jest używany do komunikowania się **dodatkowej** klastra. Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano producer.properties
    ```

    Skorzystaj z poniższego tekstu jako zawartość `producer.properties` pliku:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Zastąp **SECONDARY_BROKERHOSTS** przy użyciu brokera adresów IP, który jest używany w poprzednim kroku.

    Aby uzyskać więcej informacji o konfiguracji producenta, zobacz [Configs producentów](https://kafka.apache.org/documentation#producerconfigs) na stronie kafka.apache.org.

5. Aby utworzyć zmienną środowiskową z adresami IP o hostach dozorcy pomocniczy klastra, użyj następujących poleceń:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. W domyślnej konfiguracji platformy Kafka w HDInsight nie zezwala na automatyczne tworzenie tematów. Przed rozpoczęciem procesu dublowania, należy użyć jednej z następujących opcji:

    * **Tworzenie tematów na pomocniczy klastra**: Ta opcja umożliwia ustawienie liczby partycji i współczynnik replikacji.

        Tematy wcześniej, można utworzyć za pomocą następującego polecenia:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Zastąp `testtopic` o nazwie temacie, aby utworzyć.

    * **Konfigurowanie klastra na potrzeby tworzenia automatycznych tematu**: Ta opcja umożliwia narzędzia MirrorMaker automatycznie utworzyć tematy, jednak może utworzyć je przy użyciu innej liczby partycji lub współczynnika replikacji niż podstawowy tematu.

        Aby skonfigurować pomocniczy klastra w celu automatycznego tworzenia tematów, wykonaj następujące kroki:

        1. Przejdź do pulpitu nawigacyjnego Ambari pomocniczy klastra: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Kliknij przycisk **usług** > **Kafka**. Kliknij przycisk **Configs** kartę.
        5. W __filtru__ wprowadź wartość `auto.create`. Filtruje listę właściwości i wyświetla `auto.create.topics.enable` ustawienie.
        6. Zmień wartość właściwości `auto.create.topics.enable` wartość PRAWDA, a następnie wybierz pozycję __Zapisz__. Dodanie uwagi, a następnie wybierz __Zapisz__ ponownie.
        7. Wybierz __Kafka__ usługi, wybierz opcję __ponowne uruchomienie__, a następnie wybierz pozycję __ponownego uruchomienia, wszystkie objęte__. Po wyświetleniu monitu wybierz __Potwierdź ponowne uruchomienie wszystkich__.

        ![Skonfiguruj Tworzenie automatycznego tematu](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Uruchom narzędzia MirrorMaker

1. Z poziomu połączenia SSH **dodatkowej** klastra, użyj następującego polecenia, aby rozpocząć proces narzędzia MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry używane w tym przykładzie są następujące:

    * **--consumer.config**: Określa plik, który zawiera właściwości odbiorcy. Te właściwości są używane do tworzenia konsumenta, która odczytuje z *głównej* klastra Kafka.

    * **--producer.config**: Określa plik, który zawiera właściwości producenta. Te właściwości są używane do tworzenia producenta, która zapisuje do *dodatkowej* klastra Kafka.

    * **Lista dozwolonych —** : Lista tematów, które narzędzia MirrorMaker replikuje z klastra podstawowego do regionu pomocniczego.

    * **--num.streams**: Liczba wątków odbiorców do utworzenia.

    Konsument w węźle pomocniczym oczekuje się teraz do odbierania komunikatów.

2. Z poziomu połączenia SSH **głównej** klastra, użyj następującego polecenia, aby uruchomić producenta i wysyłanie komunikatów do tematu:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Po przyjeździe do pustego wiersza z kursorem, wpisz kilka komunikatów tekstowych. Komunikaty są wysyłane do tematu na **głównej** klastra. Gdy skończysz, użyj **klawisze Ctrl + C** aby zakończyć proces producenta.

3. Z poziomu połączenia SSH **dodatkowej** klastra, użyj **klawisze Ctrl + C** aby zakończyć proces narzędzia MirrorMaker. Może potrwać kilka sekund, aby zakończyć proces. Aby sprawdzić, czy komunikaty zostały zreplikowane do regionu pomocniczego, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Na liście tematów obejmuje teraz `testtopic`, który jest tworzony podczas MirrorMaster odzwierciedla tematu z klastra podstawowego do regionu pomocniczego. Komunikaty pobrane z tematu są takie same jak te, które zostały wprowadzone na podstawowym klastrze.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Kroki opisane w tym dokumencie utworzony klastry w grupach różnych zasobów platformy Azure. Aby usunąć wszystkie zasoby utworzone, możesz usunąć utworzone grupy zasobu dwa: **kafka podstawowe rg** i **kafka secondary_rg**. Usuwanie grupy zasobów powoduje usunięcie wszystkich zasobów utworzonych, postępując zgodnie z tego dokumentu, w tym klastrami, sieci wirtualnych i kont magazynu.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób użycia [narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) utworzyć replikę [platformy Apache Kafka](https://kafka.apache.org/) klastra. Użyj następujących linków, aby poznać inne sposoby pracy z platformą Kafka:

* [Dokumentacja usługi Apache Kafka z narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.apache.org.
* [Wprowadzenie do platformy Apache Kafka w HDInsight](apache-kafka-get-started.md)
* [Korzystanie z platformy Apache Spark z platformą Apache Kafka w HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Używanie systemu Apache Storm z platformą Apache Kafka w HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Łączenie do platformy Apache Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
