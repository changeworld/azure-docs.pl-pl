---
title: Konfigurowanie Apache Kafka w usłudze HDInsight przy użyciu Azure Portal — Szybki Start
description: W tym przewodniku szybki start dowiesz się, jak utworzyć klaster Apache Kafka w usłudze Azure HDInsight przy użyciu Azure Portal. Zapoznaj się również z tematami Kafka, subskrybentami i użytkownikami.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: f11cbdab59548906f751116a2ca7b9c545b25d91
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677883"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki Start: Tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu Azure Portal

Apache Kafka to platforma przesyłania strumieniowego Open Source. Jest on często używany jako Broker komunikatów, ponieważ zapewnia funkcje podobne do kolejki komunikatów publikowania/subskrybowania.

W tym przewodniku szybki start dowiesz się, jak utworzyć klaster [Apache Kafka](https://kafka.apache.org) przy użyciu Azure Portal. Dowiesz się również, jak używać dołączonych narzędzi do wysyłania i odbierania komunikatów przy użyciu Apache Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Dostęp do interfejsu API Apache Kafka można uzyskać tylko dla zasobów znajdujących się w tej samej sieci wirtualnej. W tym przewodniku szybki start dostęp do klastra jest uzyskiwany bezpośrednio przy użyciu protokołu SSH. Aby połączyć inne usługi, sieci lub maszyny wirtualne w Apache Kafka, należy najpierw utworzyć sieć wirtualną, a następnie utworzyć zasoby w sieci. Aby uzyskać więcej informacji, zobacz temat [nawiązywanie połączenia z Apache Kafka przy użyciu dokumentu sieci wirtualnej](apache-kafka-connect-vpn-gateway.md) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Klient SSH. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Tworzenie klastra Apache Kafka

Aby utworzyć Apache Kafka w klastrze usługi HDInsight, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie przejdź do **+ Utwórz zasób** > **Analytics** > **HDInsight**.

    ![Azure Portal utworzyć usługi HDInsight zasobów](./media/apache-kafka-get-started/create-hdinsight-cluster.png)

1. W obszarze **podstawowe**wpisz lub wybierz następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Ramach    |  Wybierz swoją subskrypcję platformy Azure. |
    |Grupa zasobów     | Utwórz grupę zasobów lub wybierz istniejącą grupę zasobów.  Grupa zasobów jest kontenerem składników platformy Azure.  W takim przypadku Grupa zasobów zawiera klaster HDInsight i zależne konto usługi Azure Storage. |
    |Nazwa klastra   | Wprowadź nazwę klastra Hadoop. Ponieważ wszystkie klastry w usłudze HDInsight współużytkują tę samą przestrzeń nazw DNS, ta nazwa musi być unikatowa. Nazwa może składać się z maksymalnie 59 znaków, w tym liter, cyfr i łączników. Pierwsze i ostatnie znaki nazwy nie mogą być łącznikami. |
    |Lokalizacja    | Wybierz lokalizację platformy Azure, w której chcesz utworzyć klaster.  Aby uzyskać lepszą wydajność, wybierz lokalizację bliższą. |
    |Typ klastra| Wybierz pozycję **Wybierz typ klastra**. Następnie wybierz pozycję **Kafka** jako typ klastra.|
    |Wersja|Zostanie określona wersja domyślna dla typu klastra. Wybierz z listy rozwijanej, jeśli chcesz określić inną wersję.|
    |Nazwa użytkownika i hasło logowania klastra    | Domyślna nazwa logowania to **admin**. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką i jedną małą literę, jeden znak inny niż alfanumeryczny (z wyjątkiem znaków ' "' \). Upewnij się, że **nie są** używane typowe hasła, takie jak "Pass@word1".|
    |Nazwa użytkownika Secure Shell (SSH) | Domyślna nazwa użytkownika to **sshuser**.  Dla nazwy użytkownika SSH można podać inną nazwę. |
    |Użyj hasła logowania do klastra dla protokołu SSH| Zaznacz to pole wyboru, aby użyć tego samego hasła dla użytkownika SSH, który został podany dla użytkownika logowania klastra.|

   ![Podstawowe informacje Azure Portal tworzenia klastra](./media/apache-kafka-get-started/azure-portal-cluster-basics-blank.png)

    Każdy region platformy Azure (lokalizacja) zawiera _domeny błędów_. Domena błędów to logiczne grupowanie sprzętu w centrum danych platformy Azure. Każda domena błędów udostępnia wspólne źródło napięcia i przełącznik sieciowy. Maszyny wirtualne i dyski zarządzane implementujące węzły w klastrze usługi HDInsight są dystrybuowane w tych domenach błędów. Ta architektura ogranicza potencjalny wpływ awarii sprzętu fizycznego.

    Aby zapewnić wysoką dostępność danych, wybierz region (lokalizacja), który zawiera __trzy domeny błędów__. Aby uzyskać informacje o liczbie domen błędów w regionie, zobacz [dostępność maszyn wirtualnych z systemem Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) .

    Wybierz pozycję **Dalej: magazyn > >** kartę, aby przejść do ustawień magazynu.

1. Na karcie **Magazyn** podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wyboru|Użyj wartości domyślnej **Wybierz z listy**.|
    |Podstawowe konto magazynu|Użyj listy rozwijanej, aby wybrać istniejące konto magazynu, lub wybierz pozycję **Utwórz nowe**. Jeśli utworzysz nowe konto, nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery|
    |Wbudowane|Użyj autowypełnianej wartości.|

    Wprowadzenie do usługi ![HDInsight Linux zapewnia wartości magazynu klastra](./media/apache-kafka-get-started/azure-portal-cluster-storage-blank.png "zapewniają wartości magazynu do tworzenia klastra usługi HDInsight")

    Wybierz kartę **zabezpieczenia i sieć** .

1. W tym przewodniku szybki start Pozostaw domyślne ustawienia zabezpieczeń. Aby dowiedzieć się więcej na temat pakietu zabezpieczeń przedsiębiorstwa, odwiedź stronę [Konfigurowanie klastra usługi HDInsight z pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Aby dowiedzieć się, jak używać własnego klucza do szyfrowania dysków Apache Kafka, odwiedź stronę tworzenie [własnego klucza w celu Apache Kafka w usłudze Azure HDInsight](apache-kafka-byok.md)

   Jeśli chcesz połączyć klaster z siecią wirtualną, wybierz sieć wirtualną z listy rozwijanej **Sieć wirtualna** .

   ![Dodawanie klastra do sieci wirtualnej](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vn.png)

    Wybierz kartę **Konfiguracja i Cennik** .

1. Aby zagwarantować dostępność Apache Kafka w usłudze HDInsight, __Liczba wpisów węzłów__ dla **węzła procesu roboczego** musi być równa 3 lub większa. Wartość domyślna to 4.

    Pozycja **dyski standardowe dla każdego węzła procesu roboczego** konfiguruje skalowalność Apache Kafka w usłudze HDInsight. Apache Kafka w usłudze HDInsight używa dysku lokalnego maszyn wirtualnych w klastrze do przechowywania danych. W Apache Kafka występuje duże użycie operacji we/wy, więc [usługa Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md) służy do zapewnienia dużej przepływności i większej liczby magazynów na węzeł. Typem dysku zarządzanego może być __Standardowy__ (dysk twardy) lub __Premium__ (SSD). Typ dysku zależy od rozmiaru maszyny wirtualnej używanej przez węzły procesu roboczego (Apache Kafka brokerów). Dyski w warstwie Premium są używane automatycznie z maszynami wirtualnymi z serii DS i GS. Wszystkie inne typy maszyn wirtualnych używają standardu.

   ![Ustawianie rozmiaru klastra Apache Kafka](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Wybierz kartę **Recenzja + tworzenie** .

1. Zapoznaj się z konfiguracją klastra. Zmień ustawienia, które są nieprawidłowe. Na koniec wybierz pozycję **Utwórz** , aby utworzyć klaster.

    ![Podsumowanie konfiguracji klastra Kafka](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Utworzenie klastra może potrwać do 20 minut.

## <a name="connect-to-the-cluster"></a>Nawiązywanie połączenia z klastrem

1. Aby nawiązać połączenie z podstawowym węzłem głównym klastra Apache Kafka, użyj następującego polecenia. Zastąp `sshuser` nazwą użytkownika SSH. Zastąp `mykafka` nazwą Twojego elementu Apache Kafkacluster.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Po pierwszym nawiązaniu połączenia z klastrem klient SSH może wyświetlić ostrzeżenie informujące o tym, że nie można ustalić autentyczności hosta. Po wyświetleniu monitu wpisz __tak__, a następnie naciśnij klawisz __Enter__ , aby dodać hosta do listy zaufanych serwerów klienta SSH.

3. Po wyświetleniu monitu wprowadź hasło użytkownika SSH.

    Po nawiązaniu połączenia zobaczysz informacje podobne do następującego tekstu:
    
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

## <a id="getkafkainfo"></a>Pobierz informacje o hoście Apache dozorcy i brokera

Podczas pracy z usługą Kafka należy znać hosty *Apache dozorcy* i *brokera* . Te hosty są używane z interfejsem API Apache Kafka i wieloma narzędziami dostarczanymi z Kafka.

W tej sekcji uzyskasz informacje o hoście z interfejsu API REST usługi Apache Ambari w klastrze.

1. Zainstaluj [JQ](https://stedolan.github.io/jq/), procesor JSON w wierszu polecenia. To narzędzie służy do analizy dokumentów JSON i jest przydatne podczas analizowania informacji o hoście. Z otwartego połączenia SSH wprowadź następujące polecenie, aby zainstalować `jq`:

    ```bash
    sudo apt -y install jq
    ```

2. Skonfiguruj zmienne środowiskowe. Zastąp wartości `PASSWORD` i `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

3. Wyodrębnij poprawnie wielkość liter w nazwach klastra. Rzeczywista wielkość liter nazwy klastra może być inna niż oczekiwano, w zależności od sposobu utworzenia klastra. To polecenie uzyskuje rzeczywistą wielkość liter, zapisuje je w zmiennej, a następnie wyświetla poprawną wielkość liter i nazwę podaną wcześniej. Wprowadź następujące polecenie:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

4. Aby ustawić zmienną środowiskową z informacjami o hoście dozorcy, użyj poniższego polecenia. Polecenie pobiera wszystkie hosty dozorcy, a następnie zwraca tylko pierwsze dwa wpisy. Wynika to z faktu, że w przypadku niedostępności jednego hosta jeden host jest nieosiągalny.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    To polecenie bezpośrednio wysyła zapytanie do usługi Ambari w węźle głównym klastra. Dostęp do usługi Ambari można również uzyskać przy użyciu adresu publicznego `https://$CLUSTERNAME.azurehdinsight.net:80/`. Niektóre konfiguracje sieci mogą uniemożliwiać dostęp do publicznego adresu. Na przykład użycie sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu do usługi HDInsight w sieci wirtualnej.

5. Aby sprawdzić, czy zmienna środowiskowa jest ustawiona poprawnie, użyj następującego polecenia:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

6. Aby ustawić zmienną środowiskową z informacjami hosta Apache Kafka brokera, użyj następującego polecenia:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

7. Aby sprawdzić, czy zmienna środowiskowa jest ustawiona poprawnie, użyj następującego polecenia:

    ```bash   
    echo $KAFKABROKERS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Zarządzanie Apache Kafka tematami

Kafka przechowuje strumienie danych w *tematach*. Aby zarządzać tematami, można użyć narzędzia `kafka-topics.sh`.

* **Aby utworzyć temat**, użyj następującego polecenia w połączeniu SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie nawiązuje połączenie z usługą dozorcy przy użyciu informacji o hoście przechowywanych w `$KAFKAZKHOSTS`. Następnie tworzy temat Apache Kafka o nazwie **test**.

    * Dane przechowywane w tym temacie są partycjonowane na osiem partycji.

    * Każda partycja jest replikowana w trzech węzłach procesu roboczego w klastrze.

        Jeśli klaster został utworzony w regionie świadczenia usługi Azure, w którym znajdują się trzy domeny błędów, użyj współczynnika replikacji o wartości 3. W przeciwnym razie użyj współczynnika replikacji wynoszącego 4.
        
        W regionach z trzema domenami błędów współczynnik replikacji równy 3 umożliwia rozproszenie replik w domenach błędów. W regionach z dwiema domenami błędów współczynnik replikacji czterech rozdziela repliki równomiernie między domenami.
        
        Aby uzyskać informacje o liczbie domen błędów w regionie, zobacz [dostępność maszyn wirtualnych z systemem Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) .

        Apache Kafka nie wie o domenach błędów platformy Azure. W przypadku tworzenia replik partycji dla tematów nie można prawidłowo dystrybuować replik w celu zapewnienia wysokiej dostępności.

        Aby zapewnić wysoką dostępność, użyj [Narzędzia do ponownego równoważenia partycji Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). To narzędzie musi być uruchamiane z połączenia SSH z węzłem głównym klastra Apache Kafka.

        Aby zapewnić najwyższą dostępność danych Apache Kafka, należy ponownie Zrównoważ repliki partycji dla tematu, gdy:

        * Tworzysz nowy temat lub partycję

        * Skalowanie klastra w górę

* **Aby wyświetlić listę tematów**, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie wyświetla listę tematów dostępnych w klastrze Apache Kafka.

* **Aby usunąć temat**, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie usuwa temat o nazwie `topicname`.

    > [!WARNING]  
    > W przypadku usunięcia utworzonego wcześniej tematu `test` należy go odtworzyć. Jest ona używana w dalszej części tego dokumentu.

Aby uzyskać więcej informacji na temat poleceń dostępnych w narzędziu `kafka-topics.sh`, użyj następującego polecenia:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Tworzenie i używanie rekordów

Kafka przechowuje *rekordy* w tematach. Rekordy są wytwarzane przez *producentów*i zużywane przez *konsumentów*. Producenci i konsumenci komunikują się z usługą *brokera Kafka* . Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest hostem brokera Apache Kafka.

Aby przechowywać rekordy w utworzonym wcześniej temacie testowym, a następnie odczytywać je przy użyciu konsumenta, wykonaj następujące czynności:

1. Aby zapisać rekordy w temacie, użyj narzędzia `kafka-console-producer.sh` z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Po wykonaniu tego polecenia zostanie wyświetlony pusty wiersz.

2. Wpisz wiadomość tekstową w pustym wierszu i naciśnij klawisz ENTER. Wprowadź w ten sposób kilka komunikatów, a następnie użyj **kombinacji klawiszy CTRL + C** , aby powrócić do normalnego monitu. Każdy wiersz jest wysyłany jako oddzielny rekord do tematu Apache Kafka.

3. Aby odczytać rekordy z tematu, użyj narzędzia `kafka-console-consumer.sh` z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    To polecenie umożliwia pobranie rekordów z tematu i wyświetlenie ich. Użycie `--from-beginning` informuje odbiorcę o konieczności rozpoczęcia od początku strumienia, więc pobierane są wszystkie rekordy.

    Jeśli używasz starszej wersji programu Kafka, Zastąp `--bootstrap-server $KAFKABROKERS` z `--zookeeper $KAFKAZKHOSTS`.

4. Użyj __klawiszy CTRL + C__ , aby zatrzymać konsumenta.

Możesz również programistycznie utworzyć producentów i konsumentów. Aby zapoznać się z przykładem użycia tego interfejsu API, zobacz temat [Apache Kafka producenta i klienta interfejsu API przy użyciu](apache-kafka-producer-consumer-api.md) dokumentu usługi HDInsight.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w ramach tego przewodnika Szybki Start, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów przy użyciu Azure Portal:

1. W Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __grupy zasobów__ , aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __więcej__ (...) po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__, a następnie potwierdź.

> [!WARNING]  
> Rozliczanie klastra usługi HDInsight zaczyna się od momentu utworzenia klastra i zostaje zatrzymane po usunięciu klastra. Opłaty są naliczane proporcjonalnie do liczby minut, więc należy usunąć klaster, gdy nie jest już używany.
>
> Usunięcie Apache Kafka w klastrze usługi HDInsight spowoduje usunięcie wszystkich danych przechowywanych w Kafka.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Użyj Apache Spark z Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
