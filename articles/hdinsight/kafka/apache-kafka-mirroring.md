---
title: Duplikuj tematy Apache Kafka - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą funkcji dublowania Apache Kafka Obsługa repliki Kafka w klastrze usługi HDInsight Dublując tematów do dodatkowej klastra.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 9fbf4364e22c0b25d224ee0961f7e7ee13ddcef8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Użyj MirrorMaker w celu zreplikowania Apache Kafka tematy z Kafka w usłudze HDInsight

Dowiedz się, jak za pomocą funkcji dublowania Apache Kafka replikować tematów do dodatkowej klastra. Dublowanie mogą być uruchomione jako ciągły proces wykonywany lub sporadycznie używane jako metody migracji danych z jednego klastra do innego.

W tym przykładzie dublowania służy do replikowania tematy między dwoma klastrami usługi HDInsight. Obu klastrach znajdują się w sieci wirtualnej platformy Azure, w tym samym regionie.

> [!WARNING]
> Dublowanie nie należy traktować jako środek do osiągnięcia odporności na uszkodzenia. Przesunięcie do elementów w temacie różnią się między klastrami źródłowym i docelowym, więc klienci nie mogą używać dwa zamiennie.
>
> Jeśli masz obawy odporność na uszkodzenia, należy ustawić replikacji tematami w ramach klastra. Aby uzyskać więcej informacji, zobacz [wprowadzenie Kafka w usłudze HDInsight](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Jak działa Kafka dublowania

Dublowania działa przy użyciu narzędzia MirrorMaker (część Apache Kafka) do używać rekordów z tematów w klastrze źródłowym, a następnie utwórz kopię lokalną w klastrze docelowym. MirrorMaker używa jednej (lub więcej) *konsumentów* odczytanej z klastra źródłowego i *producent* zapisuje klastra lokalnego (docelowy).

Na poniższym diagramie przedstawiono proces lustrzane:

![Diagram procesu dublowania](./media/apache-kafka-mirroring/kafka-mirroring.png)

Kafka Apache na HDInsight nie zapewniają dostęp do usługi Kafka za pośrednictwem publicznej sieci internet. Producenci Kafka lub klienci, którzy muszą być w tej samej sieci wirtualnej platformy Azure jako węzły w klastrze Kafka. W tym przykładzie zarówno Kafka źródłowego i docelowego klastrów znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono sposób komunikacji przepływa między klastrami:

![Diagram źródłowy i docelowy Kafka klastrów w sieci wirtualnej platformy Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Klastry źródłowy i docelowy mogą być różne liczby węzłów i partycji, różni się od przesunięcia w tematach również. Dublowania przechowuje wartości klucza, który jest używany do partycjonowania, więc na podstawie na klucz jest zachowana kolejność rekordów.

### <a name="mirroring-across-network-boundaries"></a>Dublowanie bariery sieciowe

Jeśli potrzebujesz duplikatów między klastrami Kafka w różnych sieciach, istnieją następujące dodatkowe zagadnienia:

* **Bram**: sieci musi mieć możliwość komunikacji na poziomie protokołu TCP/IP.

* **Rozpoznawanie nazw**: Kafka klastrów w każdej sieci musi być w stanie nawiązać połączenia ze sobą przy użyciu nazwy hostów. Może to wymagać serwer systemu nazw domen (DNS) w każdej sieci, która jest skonfigurowana do przesyłania żądań do innej sieci.

    Podczas tworzenia sieci wirtualnej platformy Azure, zamiast używać automatycznego DNS podany w sieci, należy określić niestandardowy serwer DNS i adres IP serwera. Po utworzeniu sieci wirtualnej można następnie utwórz maszynę wirtualną platformy Azure, który korzysta z tego adresu IP, a następnie zainstalować i skonfigurować oprogramowanie DNS na nim.

    > [!WARNING]
    > Tworzenie i konfigurowanie niestandardowych serwera DNS przed zainstalowaniem usługi HDInsight w sieci wirtualnej. Brak konieczności dodatkowej konfiguracji dla usługi HDInsight użyć serwera DNS skonfigurowanego dla sieci wirtualnej.

Aby uzyskać więcej informacji na łącząca dwie sieci wirtualne platformy Azure, zobacz [skonfigurować połączenia do wirtualnymi](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Tworzenie Kafka klastrów

Podczas tworzenia sieci wirtualnej platformy Azure i Kafka klastrów ręcznie, łatwiej jest użyć szablonu usługi Azure Resource Manager. Wykonaj następujące kroki, aby wdrożyć sieci wirtualnej platformy Azure i dwa klastry Kafka do subskrypcji platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Szablon usługi Azure Resource Manager znajduje się tutaj: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia tworzenie klastra Kafka, który zawiera trzy węzłów procesu roboczego.

2. Użyj poniższych informacji, aby wypełnić wpisy na **wdrożenie niestandardowe** bloku:
    
    ![Niestandardowe wdrożenie usługi HDInsight](./media/apache-kafka-mirroring/parameters.png)
    
    * **Grupa zasobów**: Utwórz grupę lub wybierz istniejący. Ta grupa zawiera klastra usługi HDInsight.

    * **Lokalizacja**: Wybierz lokalizację lokalizacji geograficznej blisko.
     
    * **Podstawowa nazwa klastra**: Ta wartość jest używana jako nazwa podstawowa Kafka klastrów. Na przykład wprowadzenie **hdi** tworzy klastry o nazwie **hdi źródła** i **dest hdi**.

    * **Nazwa użytkownika logowania klastra**: nazwa użytkownika administratora na serwerze źródłowym i docelowym Kafka klastrów.

    * **Hasło logowania klastra**: klastrów Kafka hasła użytkownika administratora na serwerze źródłowym i docelowym.

    * **Nazwa użytkownika SSH**: użytkownika SSH do utworzenia na serwerze źródłowym i docelowym Kafka klastrów.

    * **Hasło SSH**: klastrów Kafka hasło użytkownika SSH na serwerze źródłowym i docelowym.

3. Odczyt **warunków i postanowień**, a następnie wybierz **akceptuję warunki i postanowienia, o których wspomniano**.

4. Na koniec zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Kup**. Tworzenie klastrów trwa około 20 minut.

> [!IMPORTANT]
> Nazwy klastrów usługi HDInsight są **nazwę BAZOWĄ źródła** i **nazwę BAZOWĄ dest**, gdzie nazwę BAZOWĄ jest podana nazwa do szablonu. Te nazwy można użyć w kolejnych krokach, podczas nawiązywania połączenia z klastrów.

## <a name="create-topics"></a>Tworzenie tematów

1. Połączyć się z **źródła** klastra przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** z nazwą użytkownika SSH używany podczas tworzenia klastra. Zastąp **nazwę BAZOWĄ** o nazwie podstawowej używany podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Aby znaleźć hosty dozorcy dla źródłowego klastra, użyj następujących poleceń:

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

4. Umożliwia wyświetlanie informacji o hoście dozorcy tego następujące ( **źródła**) klastra:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Zwróci ono informacje podobne do następującego tekstu:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Zapisz te informacje. Jest on używany w następnej sekcji.

## <a name="configure-mirroring"></a>Konfigurowanie funkcji dublowania

1. Połączyć się z **docelowego** klastra przy użyciu innej sesji SSH:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** z nazwą użytkownika SSH używany podczas tworzenia klastra. Zastąp **nazwę BAZOWĄ** o nazwie podstawowej używany podczas tworzenia klastra.

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

    Zastąp **SOURCE_ZKHOSTS** dozorcy informacjami hostów z **źródła** klastra.

    Ten plik zawiera opis informacji konsumentów do użycia podczas odczytu ze źródłowej Kafka klastra. Aby uzyskać więcej informacji o konfiguracji konsumenta, zobacz [Configs konsumenta](https://kafka.apache.org/documentation#consumerconfigs) na kafka.apache.org.

    Aby zapisać plik, użyj **Ctrl + X**, **Y**, a następnie **Enter**.

3. Przed skonfigurowaniem producenta, która komunikuje się z klastrem docelowym, należy znaleźć brokera hosty dla **docelowego** klastra. Aby pobrać te informacje, użyj następujących poleceń:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra docelowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

    `echo` Polecenie zwraca informacje podobne do następującego tekstu:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. A `producer.properties` plik jest używany do komunikacji __docelowego__ klastra. Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano producer.properties
    ```

    Skorzystaj z poniższego tekstu jako zawartość `producer.properties` pliku:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Zastąp **DEST_BROKERS** brokera informacje z poprzedniego kroku.

    Aby uzyskać więcej informacji o konfiguracji producenta, zobacz [Configs producent](https://kafka.apache.org/documentation#producerconfigs) na kafka.apache.org.

5. Użyj następujących poleceń, aby znaleźć hosty dozorcy klastra docelowego:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra docelowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

7. Konfigurację domyślną dla Kafka w usłudze HDInsight nie zezwalaj na automatyczne tworzenie tematów. Przed rozpoczęciem procesu funkcja dublowania, muszą używać jednej z następujących opcji:

    * **Tworzenie tematów w docelowym klastrze**: Ta opcja umożliwia określenie liczby partycji i współczynnik replikacji.

        Tematy wcześniej, można utworzyć za pomocą następującego polecenia:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Zastąp `testtopic` o nazwie tematu, aby utworzyć.

    * **Konfigurowanie klastra na temat automatycznego tworzenia**: Ta opcja umożliwia MirrorMaker automatycznie utworzyć tematy, jednak może je utworzyć za innej liczby partycji lub współczynnik replikacji niż tematu źródła.

        Aby skonfigurować klaster docelowy do automatycznego tworzenia tematów, wykonaj następujące kroki:

        1. Z [portalu Azure](https://portal.azure.com), wybierz miejsce docelowe Kafka klastra.
        2. Na stronie przeglądu klastra, wybierz __pulpit nawigacyjny klastra__. Następnie wybierz __pulpit nawigacyjny klastra usługi HDInsight__. Po wyświetleniu monitu uwierzytelniania przy użyciu poświadczeń logowania (Administrator) dla klastra.
        3. Wybierz __Kafka__ usługi na liście po lewej stronie.
        4. Wybierz __Configs__ środku strony.
        5. W __filtru__ wprowadź wartość `auto.create`. To filtruje listę właściwości i wyświetla `auto.create.topics.enable` ustawienie.
        6. Zmień wartość `auto.create.topics.enable` na wartość PRAWDA, a następnie wybierz __zapisać__. Dodanie uwagi, a następnie wybierz __zapisać__ ponownie.
        7. Wybierz __Kafka__ usługi, wybierz opcję __Uruchom ponownie__, a następnie wybierz __ponowne uruchomienie wszystkich odpowiednich__. Po wyświetleniu monitu wybierz __Potwierdź Uruchom ponownie wszystkie__.

## <a name="start-mirrormaker"></a>Uruchom MirrorMaker

1. Połączenie SSH **docelowego** klastra, użyj następującego polecenia, aby rozpocząć proces MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry używane w tym przykładzie są:

    * **--consumer.config**: Określa plik, który zawiera właściwości konsumenta. Te właściwości są używane do tworzenia konsumenta, która odczytuje z *źródła* Kafka klastra.

    * **--producer.config**: Określa plik, który zawiera właściwości producenta. Te właściwości są używane do tworzenia producenta, która zapisuje *docelowego* Kafka klastra.

    * **--dozwolonych**: listę tematów, które MirrorMaker replikuje dane z klastra źródłowego i docelowego.

    * **--num.streams**: liczba wątków odbiorców do utworzenia.

 Uruchamianie MirrorMaker zwraca informacje podobne do następującego tekstu:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Połączenie SSH **źródła** klastra, użyj następującego polecenia, aby uruchomić producenta i wysyłanie komunikatów do tematu:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra źródłowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

     Po przyjeździe do pustego wiersza z kursorem, wpisz w kilku wiadomości SMS. Komunikaty są wysyłane do tematu **źródła** klastra. Na koniec użyj **klawisze Ctrl + C** można zakończyć procesu producenta.

3. Połączenie SSH **docelowego** klastra, użyj **klawisze Ctrl + C** można zakończyć procesu MirrorMaker. Może potrwać kilka sekund, aby zakończyć proces. Aby sprawdzić, czy komunikaty zostały zreplikowane do miejsca docelowego, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Zastąp `$CLUSTERNAME` o nazwie klastra docelowego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

    Lista tematów zawiera teraz `testtopic`, który jest tworzony podczas MirrorMaster odzwierciedla tematu z klastra źródłowego do docelowego. Wiadomości pobierane z tematu są takie same, jak został wprowadzony w klastrze źródłowym.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Ponieważ kroki opisane w tym dokumencie utworzyć obu klastrów w tej samej grupy zasobów platformy Azure, można usunąć grupy zasobów w portalu Azure. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów utworzone przez tego dokumentu, Azure Virtual Network i konto magazynu używane przez klaster.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób umożliwiają utworzenie repliki klastra Kafka MirrorMaker. Aby dowiedzieć się, inne metody pracy z Kafka, użyj następujących łączy:

* [Dokumentację Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.apache.org.
* [Wprowadzenie do Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md)
* [Używanie platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Nawiązywanie połączenia z platformą Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
