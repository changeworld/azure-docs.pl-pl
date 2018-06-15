---
title: Użyj usługi Azure Functions do wysyłania danych do Kafka w usłudze HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać funkcji platformy Azure można zapisać danych do Kafka w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: c657cda7dce0611cb357a0a2063a154f2f6f25f2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939808"
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Użyj Kafka w usłudze HDInsight z aplikacji Azure — funkcja

Dowiedz się, jak utworzyć aplikację funkcji platformy Azure, która wysyła dane do Kafka w usłudze HDInsight.

[Środowisko Azure Functions](https://docs.microsoft.com/azure/azure-functions/) jest usługą niekorzystającą obliczeń. Umożliwia uruchamianie kodu na żądanie bez konieczności jawnego udostępniania lub zarządzanie infrastrukturą.

[Apache Kafka](https://kafka.apache.org) to rozproszona platforma przesyłania strumieniowego typu „open source”, która umożliwia tworzenie aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym. Ponadto platforma Kafka oferuje funkcję brokera komunikatów, która działa podobnie do kolejki komunikatów. Pozwala ona publikować i subskrybować w nazwanych strumieniach danych. Platforma Kafka w usłudze HDInsight to zarządzana wysoce skalowalna usługa o wysokiej dostępności, która działa w chmurze platformy Microsoft Azure.

Kafka w usłudze HDInsight nie zapewnia interfejsu API w publicznej sieci internet. Aby opublikować lub wykorzystywać dane z Kafka, musi nawiązać klastra Kafka, przy użyciu sieci wirtualnej platformy Azure. Środowisko Azure Functions zapewnia wygodny sposób, aby utworzyć publiczny punkt końcowy, który wypycha dane do Kafka w usłudze HDInsight za pośrednictwem bramy sieci wirtualnej.

> [!NOTE]
> Ten dokument koncentruje się na krokach wymaganych do włączenia usługi Azure Functions do komunikowania się z Kafka w usłudze HDInsight. Przykładem sam jest właśnie podstawowe producent Kafka wykazać, że działa w konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja funkcji platformy Azure. Aby uzyskać więcej informacji, zobacz [tworzenie pierwszej funkcji](../../azure-functions/functions-create-first-azure-function.md) dokumentacji.

* Sieć wirtualną platformy Azure. Aby pracować z usługi Azure Functions, sieci wirtualnej muszą używać jednej z następujących zakresów IP:

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    Aby uzyskać więcej informacji, zobacz [integracji aplikacji z sieci wirtualnej platformy Azure](../../app-service/web-sites-integrate-with-vnet.md) dokumentu.

* Kafka w klastrze usługi HDInsight. Aby uzyskać informacje dotyczące tworzenia Kafka w klastrze usługi HDInsight, zobacz [utworzyć klaster Kafka](apache-kafka-get-started.md) dokumentu.

    > [!IMPORTANT]
    > Podczas konfiguracji klastra, należy użyć __Zaawansowane ustawienia__ krok, aby wybrać sieć wirtualna Azure i podsieć, która używa usługi HDInsight. Wybierz sieć wirtualną i podsieć utworzona w poprzednim kroku.

    Aby uzyskać więcej informacji na Kafka i sieci wirtualnych, zobacz [nawiązywanie Kafka za pośrednictwem sieci wirtualnej](apache-kafka-connect-vpn-gateway.md) dokumentu.

## <a name="architecture"></a>Architektura

Kafka w usłudze HDInsight znajduje się w sieci wirtualnej platformy Azure. Środowisko Azure Functions może komunikować się z siecią wirtualną przy użyciu bramy punkt-lokacja. Poniższa ilustracja jest diagram tej topologii sieci:

![Architektura usługi Azure Functions nawiązywania połączenia z usługi HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Skonfiguruj Kafka

Informacje przedstawione w tej sekcji przygotowuje klaster Kafka, aby akceptować dane funkcji platformy Azure. Obejmuje on następujące akcje konfiguracji:

* Reklamy przez sieć IP
* Adresy IP brokera Kafka zbieranie
* Tworzenie tematu Kafka

### <a name="configure-kafka-for-ip-advertising"></a>Skonfiguruj Kafka do celów reklamowych IP

Domyślnie dozorcy zwraca nazwę domeny brokerzy Kafka do klientów. Ta konfiguracja nie działa bez serwera DNS, zgodnie z klienta (usługi Azure Functions) nie można rozpoznać nazwy dla sieci wirtualnej. W przypadku tej konfiguracji umożliwia skonfigurowanie Kafka anonsowanie adresów IP zamiast nazwy domeny następujące czynności:

1. Przy użyciu przeglądarki sieci web, przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp __CLUSTERNAME__ o nazwie Kafka w klastrze usługi HDInsight.

    Po wyświetleniu monitu użyj protokołu HTTPS, nazwę użytkownika i hasło dla klastra. Zostanie wyświetlony interfejs użytkownika sieci Web Ambari dla klastra.

2. Aby wyświetlić informacje o Kafka, wybierz __Kafka__ na liście z lewej strony.

    ![Lista usług z Kafka wyróżnione](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Zaznacz, aby wyświetlić konfigurację Kafka __Configs__ ze środka top.

    ![Łącza Configs Kafka](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Aby znaleźć __kafka env__ konfiguracji, wprowadź `kafka-env` w __filtru__ w prawym górnym rogu.

    ![Konfiguracja Kafka, kafka env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

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

    ![Konfiguracja przyciskiem Zapisz](./media/apache-kafka-azure-functions/save-button.png)

9. Aby zapobiec błędom podczas ponownego uruchamiania Kafka, należy użyć __akcji usługi__ i wybrać __włączyć w trybie konserwacji__. Wybierz przycisk OK, aby wykonać tę operację.

    ![Włącz funkcję obsługi wyróżnione akcje usługi](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Aby ponownie uruchomić Kafka, należy użyć __Uruchom ponownie__ i wybrać __ponowne uruchomienie wszystkich wpływ__. Upewnij się, ponowne uruchomienie, a następnie użyj __OK__ przycisku po ukończeniu tej operacji.

    ![Uruchom ponownie przycisk o ponowne uruchomienie wszystkich wpływ wyróżnione](./media/apache-kafka-azure-functions/restart-button.png)

11. Aby wyłączyć tryb konserwacji, użyj __akcji usługi__ i wybrać __włączyć Wyłącz tryb konserwacji__. Wybierz **OK** do ukończenia tej operacji.

### <a name="get-the-kafka-broker-ip-address"></a>Pobierz brokera Kafka adres IP

Można pobrać w pełni kwalifikowanej nazwy domeny (FQDN) i adresy IP w węzłach klastra Kafka, użyj jednej z następujących metod:

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

To polecenie zakłada się, że `<resourcegroupname>` to nazwa grupy zasobów platformy Azure, która zawiera sieci wirtualnej.

Na liście nazw zwracane zaznacz adres IP workernode. Wewnętrzna nazwa FQDN węzła zaczyna się od litery `wn`. Ten adres IP jest używany przez funkcję do komunikowania się z Kafka.

### <a name="create-a-kafka-topic"></a>Utwórz temat Kafka

Kafka przechowuje dane w __tematy__. Przed wysłaniem danych do Kafka z funkcji platformy Azure, musisz utworzyć funkcji.

Aby utworzyć temat, wykonaj czynności w [utworzyć klaster Kafka](apache-kafka-get-started.md) dokumentu.

## <a name="create-a-function-that-sends-to-kafka"></a>Tworzy funkcję, która wysyła do Kafka

> [!NOTE]
> Kroki opisane w tej sekcji Tworzenie funkcji JavaScript, która używa [kafka węzła](https://www.npmjs.com/package/kafka-node) pakietu do publikowania danych Kafka:

1. Utwórz nową __element WebHook i interfejs API__ funkcji, a następnie wybierz __JavaScript__ jako język. Aby uzyskać więcej informacji na temat tworzenia nowych funkcji, zobacz [tworzenie pierwszej funkcji](../../azure-functions/functions-create-first-azure-function.md#create-function) dokumentu.

2. Jako treść funkcji, należy użyć poniższego kodu:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Zastąp `'test'` o nazwie tematu Kafka został utworzony w klastrze usługi HDInsight.

    Zastąp `10.1.0.7` adresem IP, który został wcześniej pobrany. Pozostaw `:9092` wartość. 9092 jest port, który Kafka nasłuchuje.

    Użyj __zapisać__ przycisk, aby zapisać zmiany.

    ![Edytor z wyjątkiem przycisku](./media/apache-kafka-azure-functions/function-editor.png)

3. Z prawej strony Edytor funkcji, wybierz __wyświetlać pliki__. Wybierz __+ Dodaj__ i Dodaj nowy plik o nazwie `package.json`. Ten plik jest używany do określania zależności na `kafka-node` pakietu.

    ![Dodawanie pliku](./media/apache-kafka-azure-functions/add-files.png)

4. Aby edytować nowy plik, wybierz `package.json` z __wyświetlać pliki__ listy. Użyj następującego tekstu jako zawartość pliku:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Użyj __zapisać__ przycisk, aby zapisać zmiany.

5. Aby zainstalować `kafka-node` pakietu, należy użyć _węzła zarządzania version i package_ sekcji [przewodnik dewelopera usługi Azure funkcji JavaScript](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > Może się pojawić kilka błędów jak kafka węzła pakiet jest zainstalowany. Można te błędy bezpiecznie zignorować.

## <a name="run-the-function"></a>Uruchom funkcję

Z prawej strony Edytor funkcji, wybierz __testu__. Pozostaw ustawienia domyślne dla testu, a następnie wybierz __Uruchom__. Podczas działania testu, przekazuje `name` parametr do funkcji. Ten parametr zawiera wartość `Azure`, która wstawia funkcję do Kafka.

![Zrzut ekranu okna dialogowego testu](./media/apache-kafka-azure-functions/function-test-dialog.png)

Aby wyświetlić informacje w zarejestrowany przez funkcję podczas wykonywania testu, wybierz __dzienniki__ w dolnej części strony. Uruchom test ponownie, aby wygenerować informacje dziennika.

![Przykład danych wyjściowych dziennika — funkcja](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Sprawdź, czy dane są Kafka

Aby sprawdzić, czy dane dostarczone w temacie Kafka, skorzystaj z informacji w _utworzyć i używać rekordów_ sekcji [utworzyć klaster Kafka](apache-kafka-get-started.md#produce-and-consume-records) dokumentu. `kafka-console-consumer` Odczytywać dane z tematu i wyświetla listę komunikatów przechowywane w tym temacie.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak korzystać z platformy Apache Kafka w usłudze HDInsight, użyj następujących linków:

* [Wprowadzenie do platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md)

* [Tworzenie repliki platformy Kafka w usłudze HDInsight przy użyciu narzędzia MirrorMaker](apache-kafka-mirroring.md)

* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Używanie platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Nawiązywanie połączenia z platformą Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)
