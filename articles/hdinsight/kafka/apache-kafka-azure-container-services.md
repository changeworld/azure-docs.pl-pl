---
title: Usługa Azure Kubernetes Service za pomocą platformy Kafka w HDInsight
description: Dowiedz się, jak używać platformy Kafka w HDInsight z obrazów kontenerów hostowanej w usłudze Azure Kubernetes Service (AKS).
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: dc2c3e557f295bd3125c09d9b839716159dcf50b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446458"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Usługa Azure Kubernetes Service za pomocą platformy Apache Kafka w HDInsight

Dowiedz się, jak używać usługi Azure Kubernetes Service (AKS) przy użyciu [platformy Apache Kafka](https://kafka.apache.org/) w klastrze HDInsight. Kroki opisane w tym dokumencie użycia aplikacji w technologii Node.js hostowanych w usłudze AKS do weryfikowania łączności z platformą Kafka. Ta aplikacja używa [kafka węzła](https://www.npmjs.com/package/kafka-node) pakietu do komunikowania się z platformą Kafka. Używa ona [biblioteki Socket.io](https://socket.io/) dla typu EventDriven obsługi komunikatów między klient przeglądarki i zaplecza hostowanych w usłudze AKS.

[Apache Kafka](https://kafka.apache.org) to rozproszona platforma przesyłania strumieniowego typu „open source”, która umożliwia tworzenie aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym. Usługa Azure Kubernetes Service zarządza hostowanym środowiskiem Kubernetes i pozwala szybko i łatwo wdrażać konteneryzowane aplikacje. Korzystając z usługi Azure Virtual Network można połączyć te dwie usługi.

> [!NOTE]  
> Ten dokument koncentruje się na krokach wymaganych do włączenia usługi Azure Kubernetes Service do komunikowania się z platformą Kafka w HDInsight. Przykładem, sama jest tylko podstawowe Kafka klientów pokazują, że działa w konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Subskrypcja platformy Azure

W tym dokumencie przyjęto założenie, że jesteś zaznajomiony z tworzeniem i przy użyciu następujących usług platformy Azure:

* Usługa Kafka w usłudze HDInsight
* Azure Kubernetes Service
* Sieci wirtualne platformy Azure

W tym dokumencie założono, że masz tamtych [samouczek usługi Azure Kubernetes Service](../../aks/tutorial-kubernetes-prepare-app.md). W tym artykule tworzy z nich usługę kontenera, tworzony jest klaster Kubernetes, rejestr kontenerów i konfiguruje `kubectl` narzędzia.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Topologia sieci

HDInsight i AKS należy użyć sieci wirtualnej platformy Azure jako kontener za zasoby obliczeniowe. Aby umożliwić komunikację między HDInsight i AKS, należy włączyć komunikację między sieciach. Kroki opisane w tym dokumencie Użyj komunikacji równorzędnej sieci wirtualnej do sieci. Inne połączenia, np. sieć VPN również powinno zadziałać. Aby uzyskać więcej informacji na temat komunikacji równorzędnej, zobacz [komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md) dokumentu.


Na poniższym diagramie przedstawiono topologię sieci używana w tym dokumencie:

![HDInsight w jednej sieci wirtualnej, usługi AKS w innym i sieci połączonych za pomocą komunikacji równorzędnej](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Nie włączono rozpoznawanie nazw między równorzędnymi sieciami, więc adresowania IP jest używany. Domyślnie platformy Kafka w HDInsight jest skonfigurowany do zwrócenia nazw hostów, a nie do adresów IP, gdy klienci łączą. Kroki opisane w tym dokumencie zmodyfikować platformy Kafka, aby używać adresu IP zamiast reklamowych.

## <a name="create-an-azure-kubernetes-service-aks"></a>Tworzenie usługi Azure Kubernetes Service (AKS)

Jeśli nie masz już klaster AKS, użyj jednej z następujących dokumentów, aby dowiedzieć się, jak utworzyć:

* [Wdrażanie klastra usługi Azure Kubernetes Service (AKS) — Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Wdrażanie klastra usługi Azure Kubernetes Service (AKS) — interfejs wiersza polecenia](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> Podczas instalacji usługi AKS tworzy sieć wirtualną. Ta sieć jest połączona z utworzonym dla HDInsight w następnej sekcji.

## <a name="configure-virtual-network-peering"></a>Skonfiguruj komunikację równorzędną sieci wirtualnej

1. Z [witryny Azure portal](https://portal.azure.com), wybierz opcję __grup zasobów__, a następnie znajdź grupę zasobów, która zawiera sieć wirtualną dla klastra usługi AKS. Nazwa grupy zasobów jest `MC_<resourcegroup>_<akscluster>_<location>`. `resourcegroup` i `akscluster` wpisy są nazwa grupy zasobów utworzonej klaster i nazwę klastra. `location` Jest klaster został utworzony w lokalizacji.

2. W grupie zasobów, wybierz __sieć wirtualna__ zasobów.

3. Wybierz __przestrzeń adresowa__. Należy pamiętać o tym, na liście przestrzeni adresowej.

4. Aby utworzyć sieć wirtualną dla HDInsight, wybierz __+ Utwórz zasób__, __sieć__, a następnie __sieć wirtualna__.

    > [!IMPORTANT]  
    > Podczas wprowadzania wartości w nowej sieci wirtualnej, należy użyć przestrzeni adresowej, który nie nakłada się na używaną przez sieć klastra AKS.

    Użyto tych samych __lokalizacji__ dla sieci wirtualnej, która została użyta dla klastra AKS.

    Poczekaj, aż sieci wirtualnej została utworzona przed przejściem do następnego kroku.

5. Aby skonfigurować komunikację równorzędną między siecią HDInsight i sieć klastra AKS, wybierz sieć wirtualną, a następnie wybierz pozycję __komunikacje równorzędne__. Wybierz __+ Dodaj__ i użyj następujących wartości do wypełnienia formularza:

   * __Nazwa__: Wprowadź unikatową nazwę dla tej konfiguracji komunikacji równorzędnej.
   * __Sieć wirtualna__: Użyj tego pola, aby wybrać sieć wirtualną dla **klastra AKS**.

     Pozostaw inne pola na wartość domyślną, a następnie wybierz __OK__ do skonfigurowania komunikacji równorzędnej.

6. Aby skonfigurować komunikację równorzędną między siecią klastra AKS i siecią HDInsight, wybierz __AKS klastra sieć wirtualną__, a następnie wybierz pozycję __komunikacje równorzędne__. Wybierz __+ Dodaj__ i użyj następujących wartości do wypełnienia formularza:

   * __Nazwa__: Wprowadź unikatową nazwę dla tej konfiguracji komunikacji równorzędnej.
   * __Sieć wirtualna__: Użyj tego pola, aby wybrać sieć wirtualną dla __klastra HDInsight__.

     Pozostaw inne pola na wartość domyślną, a następnie wybierz __OK__ do skonfigurowania komunikacji równorzędnej.

## <a name="install-apache-kafka-on-hdinsight"></a>Instalowanie platformy Apache Kafka w HDInsight

Podczas tworzenia Kafka w klastrze HDInsight, możesz dołączyć do sieci wirtualnej utworzonej wcześniej for HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra platformy Kafka, zobacz [Tworzenie klastra platformy Apache Kafka](apache-kafka-get-started.md) dokumentu.

> [!IMPORTANT]  
> Podczas tworzenia klastra, należy użyć __Zaawansowane ustawienia__ do dołączenia do sieci wirtualnej, który został utworzony dla HDInsight.

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurowanie Apache Kafka IP reklamy

Aby skonfigurować Kafka anonsowanie adresów IP zamiast nazwy domeny, wykonaj następujące kroki:

1. Korzystając z przeglądarki internetowej przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp __CLUSTERNAME__ o nazwie platformy Kafka w klastrze HDInsight.

    Po wyświetleniu monitu użyj protokołu HTTPS, nazwę użytkownika i hasło dla klastra. Interfejs użytkownika sieci Web Ambari klastra jest wyświetlana.

2. Aby wyświetlić informacje o platformie Kafka, wybierz pozycję __Kafka__ z listy po lewej stronie.

    ![Wyróżnioną listę usług z platformą Kafka](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Zaznacz, aby wyświetlić konfigurację platformy Kafka __Configs__ z górnej środkowej.

    ![Konfiguracje łączy dla platformy Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Aby znaleźć __env platformy kafka__ konfiguracji, wprowadź `kafka-env` w __filtru__ w prawym górnym rogu.

    ![Konfiguracja platformy Kafka, środowisko platformy kafka](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Aby skonfigurować Kafka anonsowanie adresów IP, Dodaj następujący tekst do dołu __kafka env szablonów__ pola:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, który nasłuchuje platformy Kafka, wprowadź `listeners` w __filtru__ w prawym górnym rogu.

7. Aby skonfigurować Kafka do nasłuchiwania na wszystkich interfejsach sieciowych, zmień wartość w __odbiorników__ pole `PLAINTEXT://0.0.0.0:9092`.

8. Aby zapisać zmiany konfiguracji, należy użyć __Zapisz__ przycisku. Wprowadź wiadomość tekstową, opisująca zmiany. Wybierz __OK__ po zapisaniu zmian.

    ![Konfiguracja przycisk Zapisz](./media/apache-kafka-azure-container-services/save-button.png)

9. Aby zapobiec błędom przy ponownym uruchamianiu platformy Kafka, użyj __działania usługi__ i wybrać __włączyć w trybie konserwacji__. Wybierz przycisk OK, aby ukończyć tę operację.

    ![Włączanie obsługi wyróżnione akcje usługi](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Ponowne uruchomienie platformy Kafka, użyj __ponowne uruchomienie__ i wybrać __ponowne uruchomienie wszystkich wpływ__. Potwierdź ponowne uruchomienie, a następnie użyj __OK__ przycisku po ukończeniu tej operacji.

    ![Uruchom ponownie wyróżnionym z ponownym uruchomieniem komputera wszystkie których to dotyczy](./media/apache-kafka-azure-container-services/restart-button.png)

11. Aby wyłączyć tryb konserwacji, użyj __działania usługi__ i wybrać __Włącz Wyłącz tryb konserwacji__. Wybierz **OK** do ukończenia tej operacji.

## <a name="test-the-configuration"></a>Testowanie konfiguracji

W tym momencie usługi Kafka i usłudze Azure Kubernetes Service komunikuje się za pośrednictwem wirtualnych sieciach równorzędnych. Aby przetestować tego połączenia, wykonaj następujące kroki:

1. Tworzenie tematu platformy Kafka, który jest używany przez aplikację testu. Aby uzyskać informacje na temat tworzenia tematów platformy Kafka, zobacz [Tworzenie klastra platformy Apache Kafka](apache-kafka-get-started.md) dokumentu.

2. Pobieranie przykładowej aplikacji z [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Edytuj `index.js` pliku i Zmień następujące wiersze:

    * `var topic = 'mytopic'`: Zastąp `mytopic` o nazwie tematu platformy Kafka, używanego przez tę aplikację.
    * `var brokerHost = '176.16.0.13:9092`: Zastąp `176.16.0.13` wewnętrzny adres IP jednego z hostów brokera klastra.

        Wewnętrzny adres IP brokera hostów (workernodes) w klastrze, możesz znaleźć [Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentu. Wybierz jeden z wpisów, gdzie rozpoczyna się od nazwy domeny adresu IP `wn`.

4. Z poziomu wiersza polecenia w `src` katalogu, zainstaluj zależności i korzystać z aparatu Docker w celu skompilowania obrazu do wdrożenia:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Pakiety wymagane przez tę aplikację są sprawdzane w repozytorium, więc nie trzeba używać `npm` narzędzie, aby je zainstalować.

5. Zaloguj się do usługi Azure Container Registry (ACR) i Znajdź nazwę loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Jeśli nie znasz nazwy usługi Azure Container Registry lub wiesz, przy użyciu wiersza polecenia platformy Azure do pracy z usługą Azure Kubernetes Service, zobacz [samouczków usługi AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Tag lokalnej `kafka-aks-test` obrazu, wartością loginServer usługi ACR. Również dodać `:v1` -to-end w celu wskazania, wersja obrazu:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Wypchnij obraz do rejestru:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Ta operacja trwa kilka minut.

8. Edytuj plik manifestu rozwiązania Kubernetes (`kafka-aks-test.yaml`) i Zastąp `microsoft` nazwą loginServer usługi ACR pobranego w kroku 4.

9. Użyj następującego polecenia, aby wdrożyć ustawienia aplikacji z manifestu:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Użyj następującego polecenia do obserwacji `EXTERNAL-IP` aplikacji:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Po przypisaniu zewnętrzny adres IP, użyj __klawisze CTRL + C__ aby zakończyć pracę w oknach wyrażenie kontrolne

11. Otwórz przeglądarkę internetową i wprowadzić zewnętrzny adres IP dla usługi. Zostanie wyświetlony stronę podobną do poniższej ilustracji:

    ![Obraz strony sieci web](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Wprowadź tekst w polu, a następnie wybierz pozycję __wysyłania__ przycisku. Dane są wysyłane do platformy Kafka. Następnie odbiorcy platformy Kafka w aplikacji odczytuje komunikat i dodaje go do __wiadomości z usługi Kafka__ sekcji.

    > [!WARNING]  
    > Możesz otrzymać wiele kopii wiadomości. Ten problem zazwyczaj się dzieje, gdy Odśwież przeglądarkę po nawiązaniu połączenia lub otwierać wiele połączeń w przeglądarce do aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak korzystać z platformy Apache Kafka w usłudze HDInsight, użyj następujących linków:

* [Wprowadzenie do platformy Apache Kafka w HDInsight](apache-kafka-get-started.md)

* [Tworzenie repliki platformy Kafka Apache na HDInsight przy użyciu narzędzia MirrorMaker](apache-kafka-mirroring.md)

* [Używanie systemu Apache Storm z platformą Apache Kafka w HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Korzystanie z platformy Apache Spark z platformą Apache Kafka w HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Łączenie do platformy Apache Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
