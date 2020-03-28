---
title: Konfigurowanie routingu wiadomości dla usługi Azure IoT Hub przy użyciu szablonu usługi Azure Resource Manager
description: Konfigurowanie routingu wiadomości dla usługi Azure IoT Hub przy użyciu szablonu usługi Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084403"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Samouczek: konfigurowanie routingu wiadomości usługi IoT Hub za pomocą szablonu usługi Azure Resource Manager

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Routing komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Pobierz plik szablonu i parametrów

W drugiej części tego samouczka można pobrać i uruchomić aplikację programu Visual Studio do wysyłania wiadomości do Centrum IoT Hub. Istnieje folder w tym pobieranie, który zawiera szablon usługi Azure Resource Manager i plik parametrów, a także skrypty interfejsu wiersza polecenia platformy Azure i programu PowerShell.

Śmiało i pobierz [próbki usługi Azure IoT C#.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Rozpaj plik master.zip. Szablon Menedżera zasobów i plik parametrów znajdują się w pliku /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ jako **template_iothub.json** i **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Tworzenie zasobów

Zamierzasz użyć szablonu usługi Azure Resource Manager (RM), aby utworzyć wszystkie zasoby. Skrypty interfejsu wiersza polecenia platformy Azure i programu PowerShell można uruchomić kilka wierszy naraz. Szablon RM jest wdrażany w jednym kroku. W tym artykule przedstawiono sekcje oddzielnie, aby ułatwić zrozumienie każdego z nich. Następnie pokaże, jak wdrożyć szablon i utworzyć urządzenie wirtualne do testowania. Po wdrożeniu szablonu można wyświetlić konfigurację routingu wiadomości w portalu.

Istnieje kilka nazw zasobów, które muszą być unikatowe globalnie, takich jak nazwa Centrum IoT i nazwa konta magazynu. Aby ułatwić nazywanie zasobów, te nazwy zasobów są skonfigurowane do dołączania losowej wartości alfanumerycznej wygenerowanej od bieżącej daty/godziny. 

Jeśli spojrzysz na szablon, zobaczysz, gdzie są skonfigurowane zmienne dla tych zasobów, które przyjmują parametr przekazany i łączyć *randomValue do parametru.* 

W poniższej sekcji opisano parametry używane.

### <a name="parameters"></a>Parametry

Większość z tych parametrów ma wartości domyślne. Te kończące się **na _in** są łączone *z randomValue,* aby uczynić je globalnie unikatowymi. 

**randomValue:** Ta wartość jest generowana od bieżącej daty/godziny podczas wdrażania szablonu. To pole nie znajduje się w pliku parametrów, ponieważ jest generowane w samym szablonie.

**identyfikator subskrypcji:** To pole jest ustawione dla subskrypcji, w której wdrażasz szablon. To pole nie znajduje się w pliku parametrów, ponieważ jest ustawione dla Ciebie.

**IoTHubName_in:** To pole jest podstawową nazwą centrum IoT Hub, która jest łączona z randomValue jako unikatowa globalnie.

**lokalizacja:** To pole jest regionem platformy Azure, w którym wdrażasz, takim jak "westus".

**consumer_group:** To pole jest grupą odbiorców ustawioną dla wiadomości przechodzących przez punkt końcowy routingu. Służy do filtrowania wyników w usłudze Azure Stream Analytics. Na przykład istnieje cały strumień, w którym można uzyskać wszystko lub jeśli masz dane pochodzące z consumer_group ustawiona na **Contoso**, następnie można skonfigurować strumień usługi Azure Stream Analytics (i raport usługi Power BI), aby wyświetlić tylko te wpisy. To pole jest używane w części 2 tego samouczka.

**sku_name:** To pole jest skalowaniem centrum IoT Hub. Wartość ta musi być S1 lub wyższa; bezpłatna warstwa nie działa dla tego samouczka, ponieważ nie zezwala na wiele punktów końcowych.

**sku_units**: To pole jest powiązane z **sku_name**i jest liczbą jednostek IoT Hub, które mogą być używane.

**d2c_partitions**: To pole jest liczbą partycji używanych do strumienia zdarzeń.

**storageAccountName_in**: To pole jest nazwą konta magazynu, które ma zostać utworzone. Wiadomości są kierowane do kontenera na koncie magazynu. To pole jest łączone z randomValue, aby uczynić go globalnie unikatowym.

**storageContainerName**: To pole jest nazwą kontenera, w którym są przechowywane wiadomości kierowane do konta magazynu.

**storage_endpoint**: To pole jest nazwą punktu końcowego konta magazynu używanego przez routing wiadomości.

**service_bus_namespace_in:** To pole jest nazwą obszaru nazw usługi Service Bus, który ma zostać utworzony. Ta wartość jest łączona z randomValue, aby uczynić ją globalnie unikatową.

**service_bus_queue_in:** To pole jest nazwą kolejki usługi Service Bus używanej do routingu wiadomości. Ta wartość jest łączona z randomValue, aby uczynić ją globalnie unikatową.

**AuthRules_sb_queue:** To pole jest regułami autoryzacji dla kolejki magistrali usług, używane do pobierania ciągu połączenia dla kolejki.

### <a name="variables"></a>Zmienne

Wartości te są używane w szablonie i są najczęściej pochodną parametrów.

**queueAuthorizationRuleResourceId:** To pole jest identyfikatorem zasobu dla reguły autoryzacji dla kolejki usługi Service Bus. ResourceId jest z kolei używany do pobierania ciągu połączenia dla kolejki.

**iotHubName:** To pole jest nazwą Centrum IoT po randomValue łączenia. 

**storageAccountName:** To pole jest nazwą konta magazynu po losowymwścięcie łączenia. 

**service_bus_namespace:** To pole jest obszarem nazw po randomValue łączenia.

**service_bus_queue:** To pole jest nazwą kolejki usługi Service Bus po losowymkonkułowanie.

**sbVersion**: THe wersja interfejsu API usługi Service Bus do użycia. W tym przypadku jest to "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Zasoby: konto magazynu i kontener

Pierwszym utworzonym zasobem jest konto magazynu wraz z kontenerem, do którego są kierowane wiadomości. Kontener jest zasobem w ramach konta magazynu. Ma klauzulę `dependsOn` dla konta magazynu, wymagające konta magazynu, które mają zostać utworzone przed kontenerem.

Oto jak wygląda ta sekcja:

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

### <a name="resources-service-bus-namespace-and-queue"></a>Zasoby: obszar nazw usługi Service Bus i kolejka

Drugi utworzony zasób to obszar nazw usługi Service Bus wraz z kolejką usługi Service Bus, do której są kierowane wiadomości. Jednostka SKU jest ustawiona na standard. Wersja interfejsu API jest pobierana ze zmiennych. Jest również ustawiona, aby aktywować obszar nazw usługi Service Bus podczas wdrażania tej sekcji (stan:Aktywny). 

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

W tej sekcji utworzy się kolejkę usługi Service Bus. Ta część skryptu `dependsOn` ma klauzulę, która zapewnia, że obszar nazw jest tworzony przed kolejką.

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

### <a name="resources-iot-hub-and-message-routing"></a>Zasoby: Centrum Iot i routing wiadomości

Teraz, gdy konto magazynu i kolejka usługi Service Bus zostały utworzone, należy utworzyć Centrum IoT Hub, który kieruje wiadomości do nich. Szablon RM używa `dependsOn` klauzul, więc nie próbuje utworzyć centrum przed utworzeniem zasobów usługi Service Bus i konta magazynu. 

Oto pierwsza część sekcji Centrum IoT. Ta część szablonu konfiguruje zależności i rozpoczyna się od właściwości.

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

Następna sekcja jest sekcją konfiguracji routingu wiadomości dla Centrum Iot. Pierwszy to sekcja dla punktów końcowych. Ta część szablonu konfiguruje punkty końcowe routingu dla kolejki usługi Service Bus i konta magazynu, w tym parametry połączenia.

Aby utworzyć parametry połączenia dla kolejki, potrzebujesz queueAuthorizationRulesResourcedId, który jest pobierany w linii. Aby utworzyć parametry połączenia dla konta magazynu, należy pobrać podstawowy klucz magazynu, a następnie użyć go w formacie dla ciągu połączenia.

Konfiguracja punktu końcowego jest również w `AVRO` miejscu, w którym można ustawić format obiektu blob na lub . `JSON`

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

Ta następna sekcja dotyczy tras wiadomości do punktów końcowych. Istnieje jeden skonfigurowany dla każdego punktu końcowego, więc jest jeden dla kolejki usługi Service Bus i jeden dla kontenera konta magazynu.

Należy pamiętać, że warunkiem kwerendy dla `level="storage"`wiadomości kierowanych do magazynu jest , a warunkiem `level="critical"`kwerendy dla wiadomości kierowanych do kolejki usługi Service Bus jest .

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

Ten json pokazuje pozostałą część sekcji Centrum IoT, która zawiera informacje domyślne i jednostkę SKU dla koncentratora.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Zasoby: reguły autoryzacji kolejek usługi Service Bus

Reguła autoryzacji kolejki usługi Service Bus służy do pobierania ciągu połączenia dla kolejki usługi Service Bus. Używa klauzuli, `dependsOn` aby upewnić się, że nie jest tworzony przed obszarem nazw usługi Service Bus i kolejki usługi Service Bus.

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

W tej sekcji utworzysz grupę odbiorców dla danych usługi IoT Hub, które mają być używane przez usługę Azure Stream Analytics w drugiej części tego samouczka.

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

### <a name="resources-outputs"></a>Zasoby: Produkty

Jeśli chcesz wysłać wartość z powrotem do skryptu wdrażania, który ma być wyświetlany, należy użyć sekcji danych wyjściowych. Ta część szablonu zwraca parametry połączenia dla kolejki usługi Service Bus. Zwracanie wartości nie jest wymagane, jest zawiera jako przykład sposobu zwracania wyników do skryptu wywołującego.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Wdrażanie szablonu rm

Aby wdrożyć szablon na platformie Azure, przekaż szablon i plik parametrów do usługi Azure Cloud Shell, a następnie wykonaj skrypt, aby wdrożyć szablon. Otwórz usługę Azure Cloud Shell i zaloguj się. W tym przykładzie użyto programu PowerShell.

Aby przesłać pliki, wybierz ikonę **Przekaż/Pobierz pliki** na pasku menu, a następnie wybierz pozycję Przekaż.

![Pasek menu Powłoki chmury z wyróżnionymi plikami przesyłania/pobierania](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Użyj wyskakuje Eksploratora plików, aby znaleźć pliki na dysku lokalnym i wybrać je, a następnie wybrać **otwórz**.

Po przekazaniu plików okno dialogowe wyników pokazuje coś podobnego do poniższego obrazu.

![Pasek menu Powłoki chmury z wyróżnionymi plikami przesyłania/pobierania](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Pliki są przekazywane do udziału używanego przez wystąpienie usługi Cloud Shell. 

Uruchom skrypt, aby wykonać wdrożenie. Ostatni wiersz tego skryptu pobiera zmienną, która została skonfigurowana do zwracania — ciąg połączenia kolejki usługi Service Bus.

Skrypt ustawia i używa tych zmiennych:

**$RGName** jest nazwą grupy zasobów, do której można wdrożyć szablon. To pole jest tworzone przed wdrożeniem szablonu.

**$location** jest lokalizacją platformy Azure, która ma być używana dla szablonu, na przykład "westus".

**nazwa deploymentname** to nazwa przypisana do wdrożenia w celu pobrania zwracanej wartości zmiennej.

Oto skrypt programu PowerShell. Skopiuj ten skrypt programu PowerShell i wklej go do okna Powłoki chmury, a następnie naciśnij klawisz Enter, aby go uruchomić.

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

Jeśli masz błędy skryptu, możesz edytować skrypt lokalnie, przekazać go ponownie do powłoki chmury i ponownie uruchomić skrypt. Po pomyślnym uruchomieniu skryptu przejdź do następnego kroku.

## <a name="create-simulated-device"></a>Tworzenie symulowanego urządzenia

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Wyświetlanie routingu wiadomości w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz skonfigurowane wszystkie zasoby i trasy wiadomości są skonfigurowane, przejdź do następnego samouczka, aby dowiedzieć się, jak przetwarzać i wyświetlać informacje o kierowanych wiadomościach.

> [!div class="nextstepaction"]
> [Część 2 - Wyświetlanie wyników routingu wiadomości](tutorial-routing-view-message-routing-results.md)
