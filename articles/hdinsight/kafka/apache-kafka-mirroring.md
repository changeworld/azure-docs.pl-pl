---
title: Takie same jak stosowane tematów platformy Apache Kafka — Azure HDInsight
description: Dowiedz się, jak za pomocą funkcji dublowania platformy Apache Kafka Obsługa repliki platformy Kafka w klastrze HDInsight Dublując tematy, aby pomocniczy klastra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: ba04ed7c95cbf00d5996ef237d3ac65053da0662
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727392"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Replikowanie tematów platformy Apache Kafka z platformą Kafka w HDInsight przy użyciu narzędzia MirrorMaker

Dowiedz się, jak za pomocą funkcji dublowania platformy Apache Kafka replikacja tematy pomocniczy klastra. Dublowanie mogą być uruchomione jako ciągły proces lub sporadycznie używane jako metodę migracji danych z jednego klastra do innego.

W tym przykładzie dublowania umożliwia replikowanie tematów między dwoma klastrami HDInsight. Oba klastry znajdują się w sieci wirtualnej platformy Azure, w tym samym regionie.

> [!WARNING]  
> Dublowanie nie być traktowane jako oznacza, że uzyskanie odporności na uszkodzenia. Przesunięcie do elementów w ramach tematu różnią się między klastrami źródłowym i docelowym, dzięki czemu klienci nie mogą używać dwóch zamiennie.
>
> Jeśli jesteś zajmującym się ochroną odporności na uszkodzenia, należy ustawić replikacji dla tematów w klastrze. Aby uzyskać więcej informacji, zobacz [wprowadzenie do platformy Apache Kafka w HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak działa platforma Apache Kafka dublowania

Za pomocą funkcji dublowania działa [narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) narzędzia (część platformy Apache Kafka), aby używać rekordów z tematów w klastrze źródłowym, a następnie utworzyć lokalną kopię w klastrze docelowym. Narzędzia MirrorMaker wykorzystuje jedną (lub więcej) *konsumentów* odczytanej z klastra źródłowego i *producentów* zapisuje klastra lokalnego (docelowy).

Poniższy diagram ilustruje proces dublowania:

![Diagram procesu dublowania](./media/apache-kafka-mirroring/kafka-mirroring.png)

Platforma Apache Kafka w HDInsight nie zapewnia dostępu do usługi Kafka za pośrednictwem publicznej sieci internet. Platforma Kafka producentów i konsumentów, musi być w tej samej sieci wirtualnej platformy Azure jako węzły w klastrze Kafka. W tym przykładzie obu klastrów platformy Kafka źródłowy i docelowy znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono przepływ komunikacji między klastrami:

![Diagram przedstawiający źródłowe i docelowe platformy Kafka klastrów w sieci wirtualnej platformy Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Klastrów źródłowych i docelowych może różnić się w liczbie węzłów i partycji i przesunięcia w tematach różnią się także. Dublowanie zachowuje wartość klucza, który służy do partycjonowania, więc kolejność rekordów jest zachowywany na podstawie-key.

### <a name="mirroring-across-network-boundaries"></a>Funkcja dublowania w granicach sieci

Jeśli zachodzi potrzeba utworzenia duplikatów między klastrami Kafka w różnych sieciach, istnieją dodatkowe następujące zagadnienia:

* **Bramy**: Sieci musi mieć możliwość komunikacji na poziomie protokołu TCP/IP.

* **Rozpoznawanie nazw**: Klastry platformy Kafka w każdej sieci musi mieć możliwość łączyć się ze sobą przy użyciu nazw hostów. Może to wymagać serwera systemu nazw domen (DNS, Domain Name System), w każdej sieci, który jest skonfigurowany do przesyłania żądań z innymi sieciami.

    Podczas tworzenia usługi Azure Virtual Network, zamiast używania automatycznej DNS, wyposażone w sieci, należy określić niestandardowego serwera DNS i adres IP serwera. Po utworzeniu sieci wirtualnej, użytkownik musi następnie utwórz maszynę wirtualną platformy Azure, która używa tego adresu IP, a następnie zainstaluj i skonfiguruj oprogramowania DNS na nim.

    > [!WARNING]  
    > Utwórz i skonfiguruj niestandardowego serwera DNS przed zainstalowaniem HDInsight w sieci wirtualnej. Brak konieczności dodatkowej konfiguracji dla HDInsight służące serwer DNS skonfigurowany w ramach sieci wirtualnej.

Aby uzyskać więcej informacji na temat łączenia dwóch sieci wirtualnych platformy Azure, zobacz [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-apache-kafka-clusters"></a>Tworzenie klastrów platformy Apache Kafka

Podczas tworzenia sieci wirtualnej platformy Azure, a następnie ręcznie klastry platformy Kafka, łatwiej jest używać szablonu usługi Azure Resource Manager. Wykonaj następujące kroki, aby wdrożyć siecią wirtualną platformy Azure i dwa klastry platformy Kafka z subskrypcją platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Szablon usługi Azure Resource Manager znajduje się tutaj: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]  
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia utworzenie klastra Kafka zawierającego trzy węzły procesu roboczego.

2. Użyj poniższych informacji, aby wypełnić wpisy na **wdrożenie niestandardowe** bloku:
    
    ![HDInsight wdrożenia niestandardowego](./media/apache-kafka-mirroring/parameters.png)
    
    * **Grupa zasobów**: Utwórz grupę lub wybierz istniejącą. Ta grupa zawiera klastra HDInsight.

    * **Lokalizacja**: Wybierz lokalizację lokalizacji geograficznej blisko.
     
    * **Podstawowa nazwa klastra**: Ta wartość jest używana jako nazwa podstawowa w przypadku klastrów Kafka. Na przykład wprowadzenie **hdi** służąca do tworzenia klastrów, o nazwie **hdi źródła** i **dest hdi**.

    * **Nazwa użytkownika logowania klastra**: Nazwa użytkownika administratora dla źródłowego i docelowego klastry platformy Kafka.

    * **Hasło logowania klastra**: Hasło użytkownika będącego administratorem źródłowe i docelowe klastry platformy Kafka.

    * **Nazwa użytkownika SSH**: Użytkownika SSH na potrzeby tworzenia źródła i przeznaczenia platformy Kafka klastrów.

    * **SSH hasła**: Hasło dla użytkownika SSH źródłowe i docelowe klastry platformy Kafka.

3. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

4. Na koniec zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Kup**. Tworzenie klastrów trwa około 20 minut.

> [!IMPORTANT]  
> Nazwy klastrów HDInsight są **BASENAME źródła** i **dest BASENAME**, gdzie BASENAME jest nazwa podana w szablonie. W kolejnych krokach będą używać tych nazw, łącząc się z klastrami.

## <a name="create-topics"></a>Twórz tematy

1. Połączyć się z **źródła** klastra przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** z nazwą użytkownika protokołu SSH, które są używane podczas tworzenia klastra. Zastąp **BASENAME** o nazwie podstawowej używane podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Aby znaleźć hosty Apache Zookeeper dla źródłowego klastra, użyj następujących poleceń:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra źródłowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

3. Aby utworzyć temat o nazwie `testtopic`, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Aby sprawdzić, czy temat został utworzony, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    Odpowiedź zawiera `testtopic`.

4. Aby wyświetlić informacje hosta Zookeeper, to należy użyć następującego ( **źródła**) klastra:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    To zwraca informacje podobne do następującego tekstu:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Zapisz te informacje. Jest on używany w następnej sekcji.

## <a name="configure-mirroring"></a>Konfigurowanie funkcji dublowania

1. Połączyć się z **docelowy** klastrem za pomocą innej sesji protokołu SSH:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** z nazwą użytkownika protokołu SSH, które są używane podczas tworzenia klastra. Zastąp **BASENAME** o nazwie podstawowej używane podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` plik jest używany do konfigurowania komunikacji z **źródła** klastra. Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano consumer.properties
    ```

    Skorzystaj z poniższego tekstu jako zawartość `consumer.properties` pliku:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Zastąp **SOURCE_ZKHOSTS** przy użyciu informacji o hostach Zookeeper z **źródła** klastra.

    Ten plik zawiera opis informacji klientów do użycia podczas odczytu ze źródła klastra Kafka. Aby uzyskać więcej informacji o konfiguracji konsumenta, zobacz [Configs konsumenta](https://kafka.apache.org/documentation#consumerconfigs) na stronie kafka.apache.org.

    Aby zapisać plik, użyj **Ctrl + X**, **Y**, a następnie **Enter**.

3. Przed skonfigurowaniem producent, który komunikuje się z docelowym klastrze, należy znaleźć brokera hostów **docelowy** klastra. Aby pobrać te informacje, użyj następujących poleceń:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra docelowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

    `echo` Polecenie zwraca informacje podobne do następującego tekstu:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. A `producer.properties` plik jest używany do komunikowania się __docelowy__ klastra. Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano producer.properties
    ```

    Skorzystaj z poniższego tekstu jako zawartość `producer.properties` pliku:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Zastąp **DEST_BROKERS** brokera informacje z poprzedniego kroku.

    Aby uzyskać więcej informacji o konfiguracji producenta, zobacz [Configs producentów](https://kafka.apache.org/documentation#producerconfigs) na stronie kafka.apache.org.

5. Aby znaleźć hosty dozorcy dla klastra docelowego, użyj następujących poleceń:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra docelowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

7. W domyślnej konfiguracji platformy Kafka w HDInsight nie zezwala na automatyczne tworzenie tematów. Przed rozpoczęciem procesu dublowania, należy użyć jednej z następujących opcji:

    * **Tworzenie tematów w docelowym klastrze**: Ta opcja umożliwia ustawienie liczby partycji i współczynnik replikacji.

        Tematy wcześniej, można utworzyć za pomocą następującego polecenia:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Zastąp `testtopic` o nazwie temacie, aby utworzyć.

    * **Konfigurowanie klastra na potrzeby tworzenia automatycznych tematu**: Ta opcja umożliwia narzędzia MirrorMaker automatycznie utworzyć tematy, jednak może utworzyć je przy użyciu innej liczby partycji lub współczynnika replikacji niż tematu źródła.

        Aby skonfigurować klaster docelowy do automatycznego tworzenia tematów, wykonaj następujące kroki:

        1. Z [witryny Azure portal](https://portal.azure.com), wybierz miejsce docelowe klastra Kafka.
        2. Omówienie klastra, zaznacz __pulpit nawigacyjny klastra__. Następnie wybierz pozycję __pulpit nawigacyjny klastra HDInsight__. Po wyświetleniu monitu uwierzytelniania przy użyciu poświadczeń logowania (administratora) dla klastra.
        3. Wybierz __Kafka__ usługi na liście po lewej części strony.
        4. Wybierz __Configs__ pośrodku strony.
        5. W __filtru__ wprowadź wartość `auto.create`. Filtruje listę właściwości i wyświetla `auto.create.topics.enable` ustawienie.
        6. Zmień wartość właściwości `auto.create.topics.enable` wartość PRAWDA, a następnie wybierz pozycję __Zapisz__. Dodanie uwagi, a następnie wybierz __Zapisz__ ponownie.
        7. Wybierz __Kafka__ usługi, wybierz opcję __ponowne uruchomienie__, a następnie wybierz pozycję __ponownego uruchomienia, wszystkie objęte__. Po wyświetleniu monitu wybierz __Potwierdź ponowne uruchomienie wszystkich__.

## <a name="start-mirrormaker"></a>Uruchom narzędzia MirrorMaker

1. Z poziomu połączenia SSH **docelowy** klastra, użyj następującego polecenia, aby rozpocząć proces narzędzia MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry używane w tym przykładzie są następujące:

    * **--consumer.config**: Określa plik, który zawiera właściwości odbiorcy. Te właściwości są używane do tworzenia konsumenta, która odczytuje z *źródła* klastra Kafka.

    * **--producer.config**: Określa plik, który zawiera właściwości producenta. Te właściwości są używane do tworzenia producenta, która zapisuje do *docelowy* klastra Kafka.

    * **Lista dozwolonych —**: Lista tematów, które narzędzia MirrorMaker są replikowane z klastra źródłowego do miejsca docelowego.

    * **--num.streams**: Liczba wątków odbiorców do utworzenia.

   Uruchamianie narzędzia MirrorMaker zwraca informacje podobne do następującego tekstu:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Z poziomu połączenia SSH **źródła** klastra, użyj następującego polecenia, aby uruchomić producenta i wysyłanie komunikatów do tematu:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra źródłowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

     Po przyjeździe do pustego wiersza z kursorem, wpisz kilka komunikatów tekstowych. Komunikaty są wysyłane do tematu na **źródła** klastra. Gdy skończysz, użyj **klawisze Ctrl + C** aby zakończyć proces producenta.

3. Z poziomu połączenia SSH **docelowy** klastra, użyj **klawisze Ctrl + C** aby zakończyć proces narzędzia MirrorMaker. Może potrwać kilka sekund, aby zakończyć proces. Aby sprawdzić, czy komunikaty zostały zreplikowane do miejsca docelowego, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra docelowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

    Na liście tematów obejmuje teraz `testtopic`, który jest tworzony podczas MirrorMaster odzwierciedla tematu z klastra źródłowego do miejsca docelowego. Komunikaty pobrane z tematu są takie same, tak jak zostały wprowadzone w klastrze źródłowym.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Ponieważ kroki opisane w tym dokumencie utworzyć obu klastrach w tej samej grupie zasobów platformy Azure, możesz usunąć grupę zasobów w witrynie Azure portal. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów utworzonych, postępując zgodnie z tego dokumentu, Azure Virtual Network i konto magazynu używane przez klaster.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób użycia [narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) utworzyć replikę [platformy Apache Kafka](https://kafka.apache.org/) klastra. Użyj następujących linków, aby poznać inne sposoby pracy z platformą Kafka:

* [Dokumentacja usługi Apache Kafka z narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.apache.org.
* [Wprowadzenie do platformy Apache Kafka w HDInsight](apache-kafka-get-started.md)
* [Korzystanie z platformy Apache Spark z platformą Apache Kafka w HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Używanie systemu Apache Storm z platformą Apache Kafka w HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Łączenie do platformy Apache Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
