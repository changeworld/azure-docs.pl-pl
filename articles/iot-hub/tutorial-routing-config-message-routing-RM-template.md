---
title: Skonfiguruj routing komunikatów usługi Azure IoT Hub przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Skonfiguruj routing komunikatów usługi Azure IoT Hub przy użyciu szablonu usługi Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: d7b8c0685cf92341241575d3e67c09a759f5c190
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543762"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Samouczek: Użyj szablonu usługi Azure Resource Manager, aby skonfigurować routing komunikatów usługi IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Kierowanie komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Pobierz plik szablon i parametry

Do drugiej części tego samouczka pobieranie i uruchamianie aplikacji programu Visual Studio, aby wysyłać komunikaty do usługi IoT Hub. Istnieje folder, w tym do pobrania, który zawiera szablon usługi Azure Resource Manager i pliku parametrów, a także skrypty programu PowerShell i wiersza polecenia platformy Azure.

Przejdź dalej i Pobierz [usługi Azure IoT C# przykłady](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) teraz. Rozpakuj plik master.zip. Szablon usługi Resource Manager i pliku parametrów znajdują się w /iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **template_iothub.json** i **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Tworzenie zasobów

Zamierzasz używać szablonu usługi Azure Resource Manager (RM) do tworzenia wszystkich zasobów. Skrypty programu PowerShell i wiersza polecenia platformy Azure mogą być uruchamiane kilka wierszy w danym momencie. Szablon Menedżera zasobów jest wdrażany w jednym kroku. Ten artykuł zawiera sekcje oddzielnie, aby lepiej zrozumieć każdej z nich. Następnie go będzie pokazują, jak wdrożyć szablon i utworzyć urządzenie wirtualne do testowania. Po wdrożeniu szablonu konfiguracji routingu wiadomości można wyświetlić w portalu.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowa, takie jak nazwa usługi IoT Hub i nazwy konta magazynu. Aby nadawania nazw zasobów łatwiejsze, te nazwy zasobu są konfigurowane do dołączenia losowych wartości alfanumeryczne wygenerowany na podstawie bieżącej daty/godziny. 

Jeśli przyjrzymy się szablon, zostanie wyświetlony gdy zmienne są skonfigurowane dla tych zasobów, które przyjmują z przekazanego parametru i łączenie *randomValue* do parametru. 

Poniższa sekcja zawiera parametry używane.

### <a name="parameters"></a>Parametry

Większość z tych parametrów mają przypisane wartości domyślne. Te kończąc **_W** są łączone za pomocą *randomValue* aby były unikatowe globalnie. 

**randomValue**: Ta wartość jest generowana z bieżącą datą/godziną podczas wdrażania szablonu. To pole nie jest w pliku parametrów wygenerowane w samego szablonu.

**subscriptionId**: To pole jest ustawione dla Ciebie do subskrypcji, w której wdrażasz szablon. To pole nie jest w pliku parametrów, ponieważ jest ona ustawiona dla Ciebie.

**IoTHubName_in**: To pole jest podstawowej nazwy Centrum IoT Hub, która jest połączona z randomValue być globalnie unikatowa.

**Lokalizacja**: To pole jest region platformy Azure, w którym jest wdrażany, takie jak "westus".

**consumer_group**: To pole jest ustawione dla wiadomości przychodzących za pośrednictwem routingu punktu końcowego grupy odbiorców. Służy do filtrowania wyników w usłudze Azure Stream Analytics. Na przykład istnieje cały strumienia, w przypadku, gdy wszystkie elementy Pobierz lub z danych przesyłanych za pomocą consumer_group równa **Contoso**, a następnie skonfiguruj strumienia usługi Azure Stream Analytics (i raportu usługi Power BI) aby wyświetlić tylko te wpisy. To pole jest używane w części 2 samouczka.

**sku_name**: To pole jest skalowanie dla usługi IoT Hub. Ta wartość musi być S1 lub nowszy; w warstwie bezpłatna nie działa w ramach tego samouczka, ponieważ nie zezwala na wiele punktów końcowych.

**sku_units**: W tym polu łączy się z **sku_name**, a liczba jednostek usługi IoT Hub, których można użyć.

**d2c_partitions**: To pole jest liczba partycji, używany do strumienia zdarzeń.

**storageAccountName_in**: To pole jest nazwa konta magazynu, który ma zostać utworzony. Wiadomości są przesyłane do kontenera na koncie magazynu. To pole jest połączone z randomValue się globalnie unikatowa.

**storageContainerName**: To pole jest nazwa kontenera, w której są przechowywane wiadomości kierowane do konta magazynu.

**storage_endpoint**: To pole jest nazwa punktu końcowego konta magazynu używany przez routing komunikatów.

**service_bus_namespace_in**: To pole jest nazwa przestrzeni nazw usługi Service Bus ma zostać utworzony. Ta wartość jest połączona z randomValue się globalnie unikatowa.

**service_bus_queue_in**: To pole jest nazwa kolejki usługi Service Bus, używane do przesyłania wiadomości. Ta wartość jest połączona z randomValue się globalnie unikatowa.

**AuthRules_sb_queue**: To pole jest reguł autoryzacji dla kolejki magistrali usług służy do pobierania parametrów połączenia dla kolejki.

### <a name="variables"></a>Zmienne

Te wartości są używane w szablonie, a przede wszystkim są uzyskiwane z parametrami.

**queueAuthorizationRuleResourceId**: To pole jest ResourceId dla reguły autoryzacji dla kolejki usługi Service Bus. Identyfikator zasobu z kolei jest używany do pobrania parametrów połączenia dla kolejki.

**iotHubName**: To pole jest nazwę Centrum IoT Hub po randomValue połączonych. 

**storageAccountName**: To pole jest nazwą konta magazynu po randomValue połączonych. 

**service_bus_namespace**: To pole jest przestrzeń nazw po randomValue połączonych.

**service_bus_queue**: To pole jest nazwa kolejki usługi Service Bus po randomValue połączonych.

**sbVersion**: Wersja interfejsu API usługi Service Bus do użycia. W tym przypadku jest to "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Zasoby: Konto magazynu i kontener

Pierwszy zasób, które są tworzone jest konto magazynu, wraz z kontenera, do której są kierowane komunikaty. Kontener jest zasobami dostępnymi w ramach konta magazynu. Ma ona `dependsOn` klauzula dla konta magazynu, wymagać konta magazynu można utworzyć przed kontenera.

Oto jak wygląda w tej sekcji:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Zasoby: Przestrzeń nazw magistrali usług i kolejek

Drugi utworzonego zasobu jest przestrzeń nazw usługi Service Bus, wraz z kolejki usługi Service Bus, do którego są przesyłane komunikaty. Jednostka SKU jest ustawiana na standardowa. Wersja interfejsu API są pobierane z zmienne. Zostanie ona również ustawiona do aktywowania przestrzeni nazw usługi Service Bus, podczas wdrażania w tej sekcji (stan: Active). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

W tej sekcji tworzy kolejki usługi Service Bus. Ta część skrypt ma `dependsOn` klauzula, która zapewnia przestrzeń nazw jest tworzony przed kolejki.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Zasoby: Usługa iot Hub i routingu wiadomości

Utworzono konto magazynu i kolejki usługi Service Bus, można utworzyć Centrum IoT, które kieruje komunikaty do nich. Szablon Menedżera zasobów używa `dependsOn` klauzul, dzięki czemu nie próbuje utworzyć Centrum, przed zasobami usługi Service Bus i konta magazynu zostały utworzone. 

Poniżej przedstawiono pierwszą część sekcji usługi IoT Hub. Ta część szablonu konfiguruje zależności i rozpoczyna się od właściwości.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

Kolejna sekcja nazywa się w sekcji konfiguracji routingu komunikatów usługi Iot Hub. Najpierw jest sekcji dla punktów końcowych. Ta część szablonu konfiguruje routingu punktów końcowych dla kolejki usługi Service Bus i konta magazynu, w tym parametry połączenia.

Aby utworzyć parametry połączenia dla kolejki, należy queueAuthorizationRulesResourcedId, która jest wbudowana pobrane. Aby utworzyć parametry połączenia dla konta magazynu, możesz pobrać klucz podstawowy magazyn i używania go w formacie ciągu połączenia.

Konfiguracja punktu końcowego jest również, gdzie ustawiasz format obiektów blob `AVRO` lub `JSON`.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

To kolejna sekcja nazywa się dla trasy wiadomości do punktów końcowych. Istnieje jeden zestaw się dla każdego punktu końcowego, więc ma jeden dla kolejki usługi Service Bus i jeden dla kontenera konta magazynu.

Należy pamiętać, że warunek kwerendy komunikaty przesyłane do magazynu jest `level="storage"`, i warunek kwerendy dla wiadomości jest kierowany do kolejki usługi Service Bus jest `level="critical"`.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Ten kod json zawiera pozostała część sekcji usługi IoT Hub, który zawiera domyślne informacje i jednostki SKU dla koncentratora.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Zasoby: Reguły autoryzacji kolejki usługi Service Bus

Reguły autoryzacji do kolejki usługi Service Bus jest używany do pobierania parametrów połączenia dla kolejki usługi Service Bus. Używa ona `dependsOn` klauzuli, aby upewnić się, że nie został utworzony przed przestrzeni nazw usługi Service Bus i kolejki usługi Service Bus.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Zasoby: Grupa konsumentów

W tej sekcji utworzysz grupy odbiorców dla danych Centrum IoT Hub do użycia przez usługę Azure Stream Analytics w drugiej części tego samouczka.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Zasoby: Dane wyjściowe

Jeśli chcesz wysyłać wartość do skryptu wdrażania, które mają być wyświetlane, możesz użyć sekcji danych wyjściowych. Ta część szablonu zwraca ciąg połączenia dla kolejki usługi Service Bus. Zwraca wartość nie jest wymagane, jest on zawarty na przykład sposób zwracania wyników do wywoływania skryptu.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Wdrażanie szablonu Menedżera zasobów

Aby wdrożyć szablon na platformie Azure, przekazywanie szablonu i pliku parametrów do usługi Azure Cloud Shell, a następnie uruchomienia skryptu w celu wdrożenia szablonu. Otwórz usługę Azure Cloud Shell, a następnie zaloguj się. W tym przykładzie użyto programu PowerShell.

Aby przekazać pliki, wybierz **przekazywanych/pobieranych plików** ikonę na pasku menu, następnie wybierz pozycję Przekaż.

![Pasek menu powłoki w chmurze z plikami przekazywanie/pobieranie wyróżniony](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Użyj Eksploratora plików, które pojawia się, aby znaleźć pliki na dysku lokalnym i zaznacz je, a następnie wybierz **Otwórz**.

Po przekazaniu plików wyniki okna dialogowego pokazuje podobną do poniższej ilustracji.

![Pasek menu powłoki w chmurze z plikami przekazywanie/pobieranie wyróżniony](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Pliki są przekazywane do udziału, używane przez wystąpienie usługi Cloud Shell. 

Uruchom skrypt, aby wykonać wdrożenie. Ostatni wiersz ten skrypt pobiera zmienną, który został skonfigurowany do zwrócenia — parametry połączenia kolejki usługi Service Bus.

Te zmienne są ustawiane w tym skrypcie.

**$RGName** to nazwa grupy zasobów, dla której chcesz wdrożyć szablon. W tym polu jest tworzony przed przystąpieniem do wdrażania szablonu.

**$location** jest lokalizacja platformy Azure, która ma być używany dla szablonu, na przykład "westus".

**deploymentname** jest nazwą Przypisz do wdrożenia, aby pobrać zwracanych wartości zmiennej.

Oto skrypt programu PowerShell. Ten skrypt programu PowerShell skopiuj i wklej go w oknie usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby go uruchomić.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Jeśli błędy skryptu, zmodyfikuj skrypt lokalnie, ponownie przekazać go do usługi Cloud Shell, a następnie ponownie uruchom skrypt. Po zakończeniu działania skryptu pomyślnemu kontynuować do następnego kroku.

## <a name="create-simulated-device"></a>Tworzenie symulowanego urządzenia

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Wyświetl routing komunikatów w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

Skoro masz wszystkie zasoby, konfigurowanie i trasy wiadomości są skonfigurowane, przejdź do następnego samouczka, aby dowiedzieć się, jak przetwarzać i wyświetlać informacje na temat routingu komunikatów.

> [!div class="nextstepaction"]
> [Część 2 — wyświetlanie wyników routing wiadomości](tutorial-routing-view-message-routing-results.md)
