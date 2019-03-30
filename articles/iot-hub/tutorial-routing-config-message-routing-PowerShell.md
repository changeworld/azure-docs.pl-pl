---
title: Skonfiguruj routing komunikatów usługi Azure IoT Hub przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Skonfiguruj routing komunikatów usługi Azure IoT Hub przy użyciu programu Azure PowerShell
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 51e9bc85c2ee843aa096674a25a1f634bd08b838
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660942"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Samouczek: Skonfiguruj routing komunikatów usługi IoT Hub przy użyciu programu Azure PowerShell

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Pobierz skrypt (opcjonalnie)

Do drugiej części tego samouczka pobieranie i uruchamianie aplikacji programu Visual Studio, aby wysyłać komunikaty do usługi IoT Hub. Brak folderu do pobrania, który zawiera szablon usługi Azure Resource Manager i pliku parametrów, a także skrypty programu PowerShell i wiersza polecenia platformy Azure. 

Jeśli chcesz wyświetlić skryptu Zakończono pobieranie [usługi Azure IoT C# przykłady](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozpakuj plik master.zip. Skrypt interfejsu wiersza polecenia platformy Azure jest w /iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Tworzenie zasobów

Rozpocznij od utworzenia zasobów przy użyciu programu PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Aby utworzyć podstawowy zasobów przy użyciu programu PowerShell

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowa, takie jak nazwa usługi IoT Hub i nazwy konta magazynu. Aby to ułatwić, te nazwy zasobu są dołączane za pomocą losowych wartości alfanumeryczne nazywanej *randomValue*. RandomValue jest generowane raz w górnej części skryptu i dołączany do nazwy zasobów, zgodnie z potrzebami w całym skrypcie. Jeśli nie chcesz, aby była losowych, można ustawić na ciąg pusty lub określoną wartość. 

> [!IMPORTANT]
> Zestaw zmiennych w początkowej skryptu są również używane przez skrypt routingu, więc uruchomić wszystkie skryptu w tej samej sesji usługi Cloud Shell. Po otwarciu nowej sesji, aby uruchomić skrypt do konfigurowania routingu, kilka zmiennych będzie brakuje wartości. 
>

Skopiuj i wklej poniższy skrypt w usłudze Cloud Shell, a następnie naciśnij klawisz Enter. Jeden wiersz skryptu jest uruchamiane w danym momencie. Ta pierwsza sekcja skrypt utworzy zasoby podstawowe na potrzeby tego samouczka, w tym konto magazynu, usługi IoT Hub, Usługa Service Bus Namespace i kolejki usługi Service Bus. Podczas wykonywania kroków samouczka, skopiuj każdy blok skryptu i wklej go w usłudze Cloud Shell, aby go uruchomić.

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Tworzenie symulowanego urządzenia

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Teraz, gdy zasoby podstawowe są skonfigurowane, możesz skonfigurować routing komunikatów.

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Aby utworzyć punkt końcowy routingu, użyj [AzIotHubRoutingEndpoint Dodaj](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Aby utworzyć trasę obsługi komunikatów dla punktu końcowego, należy użyć [AzIotHubRoute Dodaj](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Kierować do konta magazynu 

Najpierw skonfiguruj punkt końcowy dla konta magazynu, a następnie utwórz trasy wiadomości.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Te zmienne są ustawiane:

**resourceGroup**: Istnieją dwa wystąpienia w tym polu — ustawienie w obu z nich do grupy zasobów.

**name**: To pole jest nazwą Centrum IoT Hub, do którego będzie stosowana routingu.

**Nazwapunktukoncowego**: To pole jest nazwa identyfikująca punktu końcowego. 

**endpointType**: To pole jest typ punktu końcowego. Ta wartość musi być równa `azurestoragecontainer`, `eventhub`, `servicebusqueue`, lub `servicebustopic`. Do własnych celów w tym miejscu, ustaw ją na `azurestoragecontainer`.

**subscriptionID**: To pole jest ustawione na identyfikator subscriptionID dla Twojego konta platformy Azure.

**storageConnectionString**: Ta wartość jest pobierana z konta magazynu w poprzednim skrypcie. Używane przez routingu na dostęp do konta magazynu.

**containerName**: To pole jest nazwa kontenera na koncie magazynu, do którego będą zapisywane dane.

**kodowanie**: Wartość tego pola `AVRO` lub `JSON`. Określa format przechowywanych danych. Wartość domyślna to AVRO.

**routeName**: To pole jest nazwa trasy, które konfigurujesz. 

**Warunek**: To pole jest zapytania używane do filtrowania komunikatów wysłanych do tego punktu końcowego. Warunek kwerendy komunikaty przesyłane do magazynu jest `level="storage"`.

**Włączone**: Domyślnie to pole `true`, wskazujący, że trasy wiadomości powinien być włączony, po utworzeniu.

Ten skrypt skopiuj i wklej go w oknie usługi Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Następnym krokiem jest utworzyć routingu punkt końcowy dla konta magazynu. Należy również określić kontener, w którym przechowywane będą wyniki. Kontener został utworzony podczas tworzenia konta magazynu.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Następnie utwórz trasy wiadomości dla punktu końcowego magazynu. Trasy wiadomości wskazuje, gdzie wysyłać komunikaty, które spełniają specyfikacji zapytania.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Kierowanie do kolejki usługi Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Aby pobrać parametry połączenia dla kolejki usługi Service Bus, należy utworzyć regułę autoryzacji, które ma odpowiednie prawa, które są zdefiniowane. Poniższy skrypt tworzy regułę autoryzacji dla kolejki usługi Service Bus, o nazwie `sbauthrule`i ustawia uprawnienia `Listen Manage Send`. Po skonfigurowaniu tej reguły autoryzacji służy do pobrania parametrów połączenia dla kolejki.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Teraz należy używać reguły autoryzacji próby pobrania klucza kolejki usługi Service Bus. Ta reguła autoryzacji będzie służyć do pobrania parametrów połączenia w dalszej części skryptu.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Teraz Konfigurowanie routingu punkt końcowy i trasy wiadomości do kolejki usługi Service Bus. Te zmienne są ustawiane:

**Nazwapunktukoncowego**: To pole jest nazwa identyfikująca punktu końcowego. 

**endpointType**: To pole jest typ punktu końcowego. Ta wartość musi być równa `azurestoragecontainer`, `eventhub`, `servicebusqueue`, lub `servicebustopic`. Do własnych celów w tym miejscu, ustaw ją na `servicebusqueue`.

**routeName**: To pole jest nazwa trasy, które konfigurujesz. 

**Warunek**: To pole jest zapytania używane do filtrowania komunikatów wysłanych do tego punktu końcowego. Warunek kwerendy dla wiadomości jest kierowany do kolejki usługi Service Bus jest `level="critical"`.

Oto programu Azure PowerShell do rozsyłania wiadomości do kolejki usługi Service Bus.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Wyświetl routing komunikatów w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz zasoby, konfigurowanie i skonfigurowane trasy wiadomości, przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać komunikaty do usługi IoT hub i wyświetlać je można kierować do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 — wyświetlanie wyników routing wiadomości](tutorial-routing-view-message-routing-results.md)