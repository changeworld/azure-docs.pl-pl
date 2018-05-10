---
title: Usługa Azure Kubernetes za pomocą Kafka w usłudze HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Kafka w usłudze HDInsight z obrazów kontenera hostowanych w usłudze Azure usługa Kubernetes (AKS).
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
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: f54039a0e702aa3c789363969120e000760f6ef5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="use-azure-kubernetes-service-with-kafka-on-hdinsight"></a>Usługa Azure Kubernetes za pomocą Kafka w usłudze HDInsight

Dowiedz się, jak używać usługi Kubernetes Azure (AKS) z Kafka w klastrze usługi HDInsight. Kroki opisane w tym dokumencie użyj aplikacji Node.js hostowanych w AKS, aby sprawdzić łączność z Kafka. Ta aplikacja używa [kafka węzła](https://www.npmjs.com/package/kafka-node) pakietu do komunikowania się z Kafka. Używa [użyciu biblioteki Socket.io](https://socket.io/) dla zdarzeniami wysyłanie komunikatów między klient przeglądarki i zaplecza w AKS.

[Apache Kafka](https://kafka.apache.org) to rozproszona platforma przesyłania strumieniowego typu „open source”, która umożliwia tworzenie aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym. Usługa Azure Kubernetes zarządza hostowanym środowiskiem Kubernetes i umożliwia szybkie i łatwe do wdrażania konteneryzowanych aplikacji. Przy użyciu sieci wirtualnej platformy Azure, można połączyć te dwie usługi.

> [!NOTE]
> Ten dokument koncentruje się na kroki wymagane do włączenia usługi Kubernetes Azure do komunikowania się z Kafka w usłudze HDInsight. Przykładem sam jest właśnie podstawowe Kafka klientowi pokazują, że działa w konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

* [Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Subskrypcja platformy Azure

Tym dokumencie przyjęto założenie, że czytelnik zna tworzenie i korzystanie z następujących usług platformy Azure:

* Usługa Kafka w usłudze HDInsight
* Usługa Azure Kubernetes
* Sieci wirtualne platformy Azure

Ten dokument założono również, że ma udał za pośrednictwem [samouczek usługi Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md). W tym samouczku utworzy usługi kontenera klastrze Kubernetes rejestru kontenera i konfiguruje `kubectl` narzędzia.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Topologia sieci

Zarówno HDInsight i AKS użyć sieci wirtualnej platformy Azure jako kontener dla zasobów obliczeniowych. Aby umożliwić komunikację między HDInsight i AKS, należy włączyć komunikację między swoich sieci. Kroki opisane w tym dokumencie Użyj równorzędna sieci wirtualnych do sieci. Inne połączenia, takich jak sieć VPN, również powinny działać. Aby uzyskać więcej informacji dotyczących komunikacji równorzędnej, zobacz [równorzędna sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md) dokumentu.


Na poniższym diagramie przedstawiono topologię sieci używane w tym dokumencie:

![HDInsight w jedną sieć wirtualną, AKS w innym i sieciami, połączyć za pomocą komunikacji równorzędnej](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> Rozpoznawanie nazwy nie jest włączone między sieciami peered, więc adresów IP jest używana. Domyślnie Kafka w usłudze HDInsight jest skonfigurowany do zwracania nazwy hosta zamiast adresów IP, gdy klienci łączą się. Kroki opisane w tym dokumencie zmodyfikować Kafka do używania adresu IP zamiast reklamy.

## <a name="create-an-azure-kubernetes-service-aks"></a>Tworzenie usługi Azure Kubernetes (AKS)

Jeśli nie masz już AKS klastra, użyj jednej z następujących dokumentach Aby dowiedzieć się, jak utworzyć:

* [Wdrażanie klastra usługi Kubernetes Azure (AKS) — portalu](../../aks/kubernetes-walkthrough-portal.md)
* [Wdrażanie klastra usługi Kubernetes Azure (AKS) - interfejsu wiersza polecenia](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> Podczas instalacji AKS tworzy sieć wirtualną. Ta sieć jest połączyć za pomocą utworzonym dla usługi HDInsight w następnej sekcji.

## <a name="configure-virtual-network-peering"></a>Konfigurowanie sieci wirtualnej komunikacji równorzędnej

1. Z [portalu Azure](https://portal.azure.com), wybierz pozycję __grup zasobów__, a następnie znajdź grupę zasobów, która zawiera sieć wirtualną klastra AKS. Nazwa grupy zasobów jest `MC_<resourcegroup>_<akscluster>_<location>`. `resourcegroup` i `akscluster` wpisy są nazwa grupy zasobów utworzonej w klastrze i nazwy klastra. `location` Jest klaster został utworzony w lokalizacji.

2. W tej grupie zasobów, wybierz __sieci wirtualnej__ zasobów.

3. Wybierz __przestrzeni adresów__. Uwaga przestrzeni adresowej listy.

4. Aby utworzyć sieć wirtualną dla usługi HDInsight, wybierz __+ Utwórz zasób__, __sieci__, a następnie __sieci wirtualnej__.

    > [!IMPORTANT]
    > Podczas wprowadzania wartości w nowej sieci wirtualnej, należy użyć na przestrzeń adresową, który nie nakłada się na używany przez sieć klastra AKS.

    Używać tego samego __lokalizacji__ dla sieci wirtualnej, która została użyta w przypadku klastra AKS.

    Poczekaj, aż sieć wirtualną utworzono przed przejściem do następnego kroku.

5. Aby skonfigurować komunikację równorzędną między siecią HDInsight i AKS sieć klastra, wybierz sieć wirtualną, a następnie wybierz __komunikacji równorzędnych__. Wybierz __+ Dodaj__ i wypełnij formularz przy użyciu następujących wartości:

    * __Nazwa__: wprowadź unikatową nazwę dla tej konfiguracji komunikacji równorzędnej.
    * __Sieć wirtualna__: Użyj tego pola, aby wybrać sieć wirtualną **klastra AKS**.

    Pozostaw wszystkich innych pól na wartości domyślne, a następnie wybierz __OK__ skonfigurować komunikację równorzędną.

6. Aby skonfigurować komunikację równorzędną między AKS sieci klastra i HDInsight, wybierz __AKS klastra sieci wirtualnej__, a następnie wybierz __komunikacji równorzędnych__. Wybierz __+ Dodaj__ i wypełnij formularz przy użyciu następujących wartości:

    * __Nazwa__: wprowadź unikatową nazwę dla tej konfiguracji komunikacji równorzędnej.
    * __Sieć wirtualna__: Użyj tego pola, aby wybrać sieć wirtualną __klastra usługi HDInsight__.

    Pozostaw wszystkich innych pól na wartości domyślne, a następnie wybierz __OK__ skonfigurować komunikację równorzędną.

## <a name="install-kafka-on-hdinsight"></a>Zainstaluj Kafka w usłudze HDInsight

Podczas tworzenia Kafka w klastrze usługi HDInsight, należy dołączyć sieci wirtualnej utworzony wcześniej dla usługi HDInsight. Aby uzyskać więcej informacji o tworzeniu klastra Kafka, zobacz [utworzyć klaster Kafka](apache-kafka-get-started.md) dokumentu.

> [!IMPORTANT]
> Podczas tworzenia klastra, należy użyć __Zaawansowane ustawienia__ Aby dołączyć do sieci wirtualnej, który został utworzony dla usługi HDInsight.

## <a name="configure-kafka-ip-advertising"></a>Skonfiguruj Kafka IP reklam

Aby skonfigurować Kafka anonsowanie adresów IP zamiast nazwy domeny, wykonaj następujące kroki:

1. Przy użyciu przeglądarki sieci web, przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp __CLUSTERNAME__ o nazwie Kafka w klastrze usługi HDInsight.

    Po wyświetleniu monitu użyj protokołu HTTPS, nazwę użytkownika i hasło dla klastra. Zostanie wyświetlony interfejs użytkownika sieci Web Ambari dla klastra.

2. Aby wyświetlić informacje o Kafka, wybierz __Kafka__ na liście z lewej strony.

    ![Lista usług z Kafka wyróżnione](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Zaznacz, aby wyświetlić konfigurację Kafka __Configs__ ze środka top.

    ![Łącza Configs Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Aby znaleźć __kafka env__ konfiguracji, wprowadź `kafka-env` w __filtru__ w prawym górnym rogu.

    ![Konfiguracja Kafka, kafka env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Aby skonfigurować Kafka anonsowanie adresów IP, Dodaj poniższy tekst do dołu __kafka env szablonów__ pola:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, który nasłuchuje Kafka, wprowadź `listeners` w __filtru__ w prawym górnym rogu.

7. Aby skonfigurować Kafka do nasłuchiwania na wszystkich interfejsach sieciowych, zmień wartość w __odbiorników__ do `PLAINTEXT://0.0.0.0:9092`.

8. Aby zapisać zmiany konfiguracji, użyj __zapisać__ przycisku. Wprowadź wiadomość tekstową opisujące zmiany. Wybierz __OK__ po zapisaniu zmian.

    ![Konfiguracja przyciskiem Zapisz](./media/apache-kafka-azure-container-services/save-button.png)

9. Aby zapobiec błędom podczas ponownego uruchamiania Kafka, należy użyć __akcji usługi__ i wybrać __włączyć w trybie konserwacji__. Wybierz przycisk OK, aby wykonać tę operację.

    ![Włącz funkcję obsługi wyróżnione akcje usługi](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Aby ponownie uruchomić Kafka, należy użyć __Uruchom ponownie__ i wybrać __ponowne uruchomienie wszystkich wpływ__. Upewnij się, ponowne uruchomienie, a następnie użyj __OK__ przycisku po ukończeniu tej operacji.

    ![Uruchom ponownie przycisk o ponowne uruchomienie wszystkich wpływ wyróżnione](./media/apache-kafka-azure-container-services/restart-button.png)

11. Aby wyłączyć tryb konserwacji, użyj __akcji usługi__ i wybrać __włączyć Wyłącz tryb konserwacji__. Wybierz **OK** do ukończenia tej operacji.

## <a name="test-the-configuration"></a>Przetestuj konfigurację

W tym momencie Kafka i usługę Azure Kubernetes znajdują się w komunikacji za pośrednictwem peered sieci wirtualnych. Można przetestować tego połączenia, wykonaj następujące kroki:

1. Utwórz temat Kafka, który jest używany przez aplikację testu. Informacje dotyczące tworzenia Kafka tematów, zobacz [utworzyć klaster Kafka](apache-kafka-get-started.md) dokumentu.

2. Pobieranie przykładowej aplikacji z [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Edytuj `index.js` plików i Zmień następujące wiersze:

    * `var topic = 'mytopic'`: Zastąp `mytopic` o nazwie tematu Kafka używanego przez tę aplikację.
    * `var brokerHost = '176.16.0.13:9092`: Zastąp `176.16.0.13` z wewnętrznego adresu IP jednego z hostów brokera klastra.

        Aby znaleźć adres IP wewnętrznego brokera hostów (workernodes) w klastrze, zobacz [interfejsu API REST Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentu. Wybierz jeden z wpisów, których nazwy domeny rozpoczynają się od adresu IP `wn`.

4. Z poziomu wiersza polecenia w `src` katalogu, zainstaluj zależności i użyj Docker, aby utworzyć obraz do wdrożenia:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Wymagane przez tę aplikację pakiety są sprawdzane do repozytorium, dzięki czemu nie trzeba używać `npm` narzędzie, aby je zainstalować.

5. Zaloguj się do programu Azure kontenera rejestru (ACR) i znaleźć nazwy loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Jeśli nie znasz nazwy rejestru kontenera platformy Azure lub czy znasz przy użyciu wiersza polecenia platformy Azure do pracy z usługą Kubernetes Azure, zobacz [samouczki AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Tag lokalnej `kafka-aks-test` obrazu o loginServer z rekordu ACR. Dodaj również `:v1` aby wskazywać wersję obrazu:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Wypchnij obrazu w rejestrze:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Ta operacja może zająć kilka minut.

8. Edytuj plik manifestu Kubernetes (`kafka-aks-test.yaml`) i Zastąp `microsoft` o nazwie loginServer ACR pobrany w kroku 4.

9. Aby wdrożyć ustawienia aplikacji w manifeście, wykonaj następujące polecenie:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Użyj następującego polecenia, które należy obserwować `EXTERNAL-IP` aplikacji:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Kiedy zewnętrzny adres IP zostanie przypisany, za pomocą __klawisze CTRL + C__ aby zakończyć czujki

11. Otwórz przeglądarkę sieci web, a następnie wprowadź zewnętrzny adres IP dla usługi. Przyjeździe stronę podobną do poniższej ilustracji:

    ![Obraz strony sieci web](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Wprowadź tekst w polu, a następnie wybierz __wysyłania__ przycisku. Dane są wysyłane do Kafka. Następnie konsumenta Kafka w aplikacji odczytuje komunikat i dodaje go do __komunikaty z Kafka__ sekcji.

    > [!WARNING]
    > Może pojawić się wiele kopii wiadomości. Zazwyczaj ten problem spowodowany Odświeżaj okna przeglądarki po połączeniu lub otwierać wiele połączeń przeglądarki do aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak korzystać z platformy Apache Kafka w usłudze HDInsight, użyj następujących linków:

* [Wprowadzenie do platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md)

* [Tworzenie repliki platformy Kafka w usłudze HDInsight przy użyciu narzędzia MirrorMaker](apache-kafka-mirroring.md)

* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Używanie platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Nawiązywanie połączenia z platformą Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
