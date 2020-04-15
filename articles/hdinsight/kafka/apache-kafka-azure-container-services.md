---
title: Korzystanie z usługi Azure Kubernetes z platformą Kafka w programie HDInsight
description: Dowiedz się, jak używać platformy Kafka w programie HDInsight z obrazów kontenerów hostowanych w usłudze Azure Kubernetes Service (AKS).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 55373f71c78b6d45b9c78c52dea61a37b89b4a00
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383059"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Korzystanie z usługi Azure Kubernetes z apache kafka w programie HDInsight

Dowiedz się, jak korzystać z usługi Azure Kubernetes Service (AKS) z [apache kafka](https://kafka.apache.org/) w klastrze USŁUGI HDInsight. Kroki opisane w tym dokumencie używają aplikacji Node.js hostowanej w udziale usługi AKS w celu zweryfikowania łączności z platformą Kafka. Ta aplikacja używa pakietu [węzła kafka](https://www.npmjs.com/package/kafka-node) do komunikowania się z platformą Kafka. Używa [Socket.io](https://socket.io/) do obsługi wiadomości sterowanych zdarzeniami między klientem przeglądarki a zapleczem hostowanym w u.

[Apache Kafka](https://kafka.apache.org) to rozproszona platforma przesyłania strumieniowego typu „open source”, która umożliwia tworzenie aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym. Usługa Azure Kubernetes service zarządza hostowanym środowiskiem Kubernetes i umożliwia szybkie i łatwe wdrażanie aplikacji konteneryzowanych. Za pomocą sieci wirtualnej platformy Azure można połączyć dwie usługi.

> [!NOTE]  
> Ten dokument koncentruje się na krokach wymaganych do włączenia usługi Azure Kubernetes do komunikowania się z platformą Kafka w usłudze HDInsight. Sam przykład jest tylko podstawowym klientem platformy Kafka, aby wykazać, że konfiguracja działa.

## <a name="prerequisites"></a>Wymagania wstępne

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Subskrypcja platformy Azure

W tym dokumencie przyjęto założenie, że użytkownik jest zaznajomiony z tworzeniem i używaniem następujących usług platformy Azure:

* Usługa Kafka w usłudze HDInsight
* Azure Kubernetes Service
* Sieci wirtualne platformy Azure

W tym dokumencie przyjęto również założenie, że przeszedłeś przez [samouczek usługi Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md). Ten artykuł tworzy usługę kontenera, tworzy klaster Kubernetes, `kubectl` rejestr kontenerów i konfiguruje narzędzie.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Topologia sieci

Zarówno HDInsight, jak i AKS używają sieci wirtualnej platformy Azure jako kontenera dla zasobów obliczeniowych. Aby włączyć komunikację między hdinsight i AKS, należy włączyć komunikację między ich sieciami. Kroki opisane w tym dokumencie używają komunikacji równorzędnej sieci wirtualnej do sieci. Inne połączenia, takie jak VPN, również powinny działać. Aby uzyskać więcej informacji na temat komunikacji równorzędnej, zobacz dokument [komunikacji równorzędnej sieci wirtualnej.](../../virtual-network/virtual-network-peering-overview.md)

Na poniższym diagramie przedstawiono topologię sieci używaną w tym dokumencie:

![HDInsight w jednej sieci wirtualnej, AKS w innej, przy użyciu komunikacji równorzędnej](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Rozpoznawanie nazw nie jest włączone między sieciami równorzędnym, dlatego używane jest adresowanie IP. Domyślnie program Kafka w programie HDInsight jest skonfigurowany do zwracania nazw hostów zamiast adresów IP podczas łączenia się klientów. Kroki opisane w tym dokumencie modyfikują platformę Kafka, aby zamiast tego używać reklam IP.

## <a name="create-an-azure-kubernetes-service-aks"></a>Tworzenie usługi Azure Kubernetes (AKS)

Jeśli nie masz jeszcze klastra usługi AKS, użyj jednego z następujących dokumentów, aby dowiedzieć się, jak go utworzyć:

* [Wdrażanie klastra usługi Kubernetes platformy Azure (AKS) — portal](../../aks/kubernetes-walkthrough-portal.md)
* [Wdrażanie klastra usługi Kubernetes platformy Azure (AKS) — wiersz polecenia](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> Usługa AKS tworzy sieć wirtualną podczas instalacji w **dodatkowej** grupie zasobów. Dodatkowa grupa zasobów jest zgodna z konwencją nazewnictwa **MC_resourceGroup_AKSclusterName_location**.  
> Ta sieć jest równorzędna z siecią utworzoną dla usługi HDInsight w następnej sekcji.

## <a name="configure-virtual-network-peering"></a>Konfigurowanie komunikacji równorzędnej w sieci wirtualnej

### <a name="identify-preliminary-information"></a>Identyfikacja informacji wstępnych

1. W [witrynie Azure portal](https://portal.azure.com)znajdź dodatkową **grupę zasobów** zawierającą sieć wirtualną dla klastra AKS.

2. Z grupy zasobów wybierz zasób __sieci wirtualnej.__ Zapisz tę nazwę do późniejszego użycia.

3. W obszarze **Ustawienia**wybierz pozycję __Przestrzeń adresowa__. Zanotuj wymienioną przestrzeń adresową.

### <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

1. Aby utworzyć sieć wirtualną dla usługi HDInsight, przejdź do __+ Utwórz zasób__ > __Sieć wirtualna__ > __Virtual network__.

1. Utwórz sieć, korzystając z następujących wskazówek dotyczących niektórych właściwości:

    |Właściwość | Wartość |
    |---|---|
    |Przestrzeń adresowa|Należy użyć przestrzeni adresowej, która nie nakłada się na przestrzeń używaną przez sieć klastra AKS.|
    |Lokalizacja|Użyj tej samej __lokalizacji__ dla sieci wirtualnej, która została użyta w klastrze AKS.|

1. Poczekaj, aż sieć wirtualna została utworzona przed przejściem do następnego kroku.

### <a name="configure-peering"></a>Konfigurowanie komunikacji równorzędnej

1. Aby skonfigurować komunikację równorzędną między siecią HDInsight a siecią klastra AKS, wybierz sieć wirtualną, a następnie wybierz pozycję __Komunikacja równorzędna__.

1. Zaznacz __+ Dodaj__ i użyj następujących wartości, aby wypełnić formularz:

    |Właściwość |Wartość |
    |---|---|
    |Nazwa komunikacji równorzędnej z \<tej sieci VN> do zdalnej sieci wirtualnej|Wprowadź unikatową nazwę dla tej konfiguracji komunikacji równorzędnej.|
    |Sieć wirtualna|wybierz sieć wirtualną **dla klastra AKS**.|
    |Nazwa komunikacji równorzędnej z \<> AKS VN do \<tej> VN|Wprowadź unikatową nazwę.|

    Pozostaw wszystkie inne pola przy wartości domyślnej, a następnie wybierz __przycisk OK,__ aby skonfigurować komunikację równorzędną.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Tworzenie klastra Apache Kafka w programie HDInsight

Podczas tworzenia klastra Platformy Kafka w programie HDInsight należy dołączyć do sieci wirtualnej utworzonej wcześniej dla usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra platformy Kafka, zobacz Tworzenie dokumentu [klastra Apache Kafka.](apache-kafka-get-started.md)

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurowanie apache Kafka IP Reklama

Aby skonfigurować platformę Kafka, należy anonsować adresy IP zamiast nazw domen:

1. Korzystając z przeglądarki internetowej, przejdź do adresu `https://CLUSTERNAME.azurehdinsight.net`. Zamień nazwę CLUSTERNAME na nazwę kafki w klastrze HDInsight.

    Po wyświetleniu monitu użyj nazwy użytkownika i hasła HTTPS dla klastra. Zostanie wyświetlony interfejs użytkownika sieci Web Ambari dla klastra.

2. Aby wyświetlić informacje o platformie Kafka, wybierz pozycję __Kafka__ z listy po lewej stronie.

    ![Lista usług z wyróżnioną poka](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Aby wyświetlić konfigurację platformy Kafka, wybierz __configs__ z górnego środka.

    ![Konfiguracja usług Apache Ambari](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. Aby znaleźć konfigurację __kafka-env,__ wprowadź `kafka-env` w polu __Filtr__ w prawym górnym rogu.

    ![Konfiguracja platformy Kafka dla kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Aby skonfigurować platformę Kafka do anonsowania adresów IP, dodaj następujący tekst u dołu pola __szablonu kafka-env:__

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, na który nasłuchuje program Kafka, wprowadź `listeners` pole __Filtr__ w prawym górnym rogu.

7. Aby skonfigurować platformę Kafka do nasłuchiwać na wszystkich `PLAINTEXT://0.0.0.0:9092`interfejsach sieciowych, zmień wartość w polu __detektory__ na .

8. Aby zapisać zmiany konfiguracji, użyj przycisku __Zapisz.__ Wprowadź wiadomość tekstową opisującą zmiany. Po zapisaniu zmian wybierz __przycisk OK.__

    ![Apache Ambari zapisać konfigurację](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Aby zapobiec błędom podczas ponownego uruchamiania platformy Kafka, użyj przycisku __Akcje usługi__ i wybierz opcję __Włącz tryb konserwacji__. Wybierz przycisk OK, aby wykonać tę operację.

    ![Akcje serwisowe z podświetlenia włączenia konserwacji](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Aby ponownie uruchomić platformę Kafka, użyj przycisku __Uruchom ponownie__ i wybierz __pozycję Uruchom ponownie wszystkie, których dotyczy problem__. Potwierdź ponowne uruchomienie, a następnie użyj przycisku __OK__ po zakończeniu operacji.

    ![Przycisk Uruchom ponownie z podświetlenym ponownym uruchomieniem wszystkich wyróżnionych](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Aby wyłączyć tryb konserwacji, użyj przycisku __Akcje serwisu__ i wybierz __opcję Wyłącz tryb konserwacji__. Wybierz **przycisk OK,** aby wykonać tę operację.

## <a name="test-the-configuration"></a>Testowanie konfiguracji

W tym momencie platformy Kafka i usługi Azure Kubernetes są w komunikacji za pośrednictwem sieci wirtualnych równorzędnych. Aby przetestować to połączenie, należy wykonać następujące czynności:

1. Utwórz temat platformy Kafka, który jest używany przez aplikację testową. Aby uzyskać informacje na temat tworzenia tematów platformy Kafka, zobacz tworzenie dokumentu [klastra Apache Kafka.](apache-kafka-get-started.md)

2. Pobierz przykładowa [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test)aplikacja z pliku .

3. Edytuj `index.js` plik i zmień następujące wiersze:

    * `var topic = 'mytopic'`: `mytopic` Zastąp nazwą tematu platformy Kafka używanego przez tę aplikację.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Zastąp wewnętrznym adresem IP jednego z hostów brokera dla swojego klastra.

        Aby znaleźć wewnętrzny adres IP hostów brokera (workernodes) w klastrze, zobacz dokument [interfejsu API Apache Ambari REST.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) Wybierz adres IP jednego z wpisów, w `wn`którym zaczyna się nazwa domeny .

4. Z wiersza polecenia `src` w katalogu zainstaluj zależności i użyj platformy Docker do utworzenia obrazu do wdrożenia:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Pakiety wymagane przez tę aplikację są sprawdzane w repozytorium, `npm` więc nie trzeba używać narzędzia, aby je zainstalować.

5. Zaloguj się do rejestru kontenerów platformy Azure (ACR) i znajdź nazwę serwera logowania:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Jeśli nie znasz nazwy rejestru kontenerów platformy Azure lub nie znasz korzystania z interfejsu wiersza polecenia platformy Azure do pracy z usługą Azure Kubernetes, zobacz [samouczki AKS.](../../aks/tutorial-kubernetes-prepare-app.md)

6. Oznacz lokalny `kafka-aks-test` obraz za pomocą strony logowania ACR. Dodaj `:v1` również na końcu, aby wskazać wersję obrazu:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Wypchnij obraz do rejestru:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Ta operacja trwa kilka minut.

8. Edytuj plik manifestu Kubernetes`kafka-aks-test.yaml` `microsoft` ( ) i zastąp nazwą ACR loginServer pobraną w kroku 4.

9. Użyj następującego polecenia, aby wdrożyć ustawienia aplikacji z manifestu:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Użyj następującego polecenia, `EXTERNAL-IP` aby obserwować aplikację:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Po przypisaniu zewnętrznego adresu IP użyj __klawiszy CTRL + C,__ aby wyjść z

11. Otwórz przeglądarkę internetową i wprowadź zewnętrzny adres IP usługi. Dojdziesz do strony podobnej do poniższej ilustracji:

    ![Apache Kafka test strony internetowej obraz](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Wprowadź tekst w polu, a następnie wybierz przycisk __Wyślij.__ Dane są wysyłane do platformy Kafka. Następnie konsument platformy Kafka w aplikacji odczytuje komunikat i dodaje go do __sekcji Wiadomości z platformy Kafka.__

    > [!WARNING]  
    > Możesz otrzymać wiele kopii wiadomości. Ten problem zwykle występuje po odświeżeniu przeglądarki po nawiązaniu połączenia lub otwarciu wielu połączeń przeglądarki z aplikacją.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z platformy Apache Kafka w usłudze HDInsight, użyj następujących linków:

* [Wprowadzenie do apache Kafka w programie HDInsight](apache-kafka-get-started.md)

* [Użyj MirrorMaker, aby utworzyć replikę Apache Kafka na HDInsight](apache-kafka-mirroring.md)

* [Użyj Apache Storm z Apache Kafka na HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Użyj Apache Spark z Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Łączenie się z platformą Apache Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
