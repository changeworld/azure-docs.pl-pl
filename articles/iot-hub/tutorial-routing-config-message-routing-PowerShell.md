---
title: Konfigurowanie routingu wiadomości dla usługi Azure IoT Hub za pomocą usługi Azure PowerShell
description: Konfigurowanie routingu wiadomości dla usługi Azure IoT Hub przy użyciu programu Azure PowerShell. W zależności od właściwości w wiadomości, trasy do konta magazynu lub kolejki usługi Service Bus.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084434"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Samouczek: konfigurowanie routingu komunikatów usługi IoT Hub za pomocą programu Azure PowerShell

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Pobierz skrypt (opcjonalnie)

W drugiej części tego samouczka można pobrać i uruchomić aplikację programu Visual Studio do wysyłania wiadomości do Centrum IoT Hub. Istnieje folder w pobieranie, który zawiera szablon usługi Azure Resource Manager i plik parametrów, a także skrypty interfejsu wiersza polecenia platformy Azure i programu PowerShell. 

Jeśli chcesz wyświetlić gotowy skrypt, pobierz [przykłady usługi Azure IoT C#.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Rozpaj plik master.zip. Skrypt interfejsu wiersza polecenia platformy Azure jest w /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ jako **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Tworzenie zasobów

Zacznij od utworzenia zasobów za pomocą programu PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Tworzenie zasobów podstawowych za pomocą programu PowerShell

Skopiuj i wklej poniższy skrypt do usługi Cloud Shell i naciśnij klawisz Enter. Uruchamia skrypt po jednym wierszu naraz. W tej pierwszej sekcji skryptu utworzy się zasoby podstawowe dla tego samouczka, w tym konto magazynu, Centrum IoT Hub, obszar nazw magistrali usług i kolejka usługi Service Bus. Podczas przechodzenia przez samouczek skopiuj każdy blok skryptu i wklej go do usługi Cloud Shell, aby go uruchomić.

Istnieje kilka nazw zasobów, które muszą być unikatowe globalnie, takich jak nazwa Centrum IoT i nazwa konta magazynu. Aby to ułatwić, te nazwy zasobów są dołączane z losową wartością alfanumeryczną o nazwie *randomValue*. RandomValue jest generowany raz w górnej części skryptu i dołączane do nazw zasobów w razie potrzeby w całym skrypcie. Jeśli nie chcesz, aby był losowy, możesz ustawić go na pusty ciąg lub określoną wartość. 

> [!IMPORTANT]
> Zmienne ustawione w skrypcie początkowym są również używane przez skrypt routingu, więc uruchom cały skrypt w tej samej sesji cloud shell. Jeśli otworzysz nową sesję, aby uruchomić skrypt do konfigurowania routingu, kilka zmiennych będzie brakować wartości. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
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

Teraz, gdy zasoby podstawowe są skonfigurowane, można skonfigurować routing wiadomości.

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Aby utworzyć punkt końcowy routingu, należy użyć [add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Aby utworzyć trasę obsługi wiadomości dla punktu końcowego, należy użyć programu [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Kierowanie do konta magazynu 

Najpierw skonfiguruj punkt końcowy dla konta magazynu, a następnie utwórz marszrutę wiadomości.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Są to zmienne używane przez skrypt, który musi być ustawiony w ramach sesji Cloud Shell:

**resourceGroup**: Istnieją dwa wystąpienia tego pola — ustaw oba z nich na grupę zasobów.

**nazwa**: To pole jest nazwą Centrum IoT, do którego będzie stosowana routing.

**endpointName**: To pole jest nazwą identyfikującą punkt końcowy. 

**typ punktu końcowego:** To pole jest typem punktu końcowego. Wartość ta musi `azurestoragecontainer`być `eventhub` `servicebusqueue`ustawiona `servicebustopic`na , , lub . Dla twoich celów tutaj `azurestoragecontainer`ustaw go na .

**identyfikator subskrypcji:** to pole jest ustawione na identyfikator subskrypcji dla twojego konta platformy Azure.

**storageConnectionString**: Ta wartość jest pobierana z konta magazynu skonfigurowanego w poprzednim skrypcie. Jest on używany przez routingu, aby uzyskać dostęp do konta magazynu.

**containerName:** To pole jest nazwą kontenera na koncie magazynu, do którego będą zapisywane dane.

**Kodowanie:** Ustaw to pole `AVRO` `JSON`na jeden lub . Oznacza to format przechowywanych danych. Wartość domyślna to AVRO.

**routeName**: To pole jest nazwą trasy, którą konfigurujesz. 

**warunek:** To pole jest kwerendą używaną do filtrowania wiadomości wysyłanych do tego punktu końcowego. Warunkiem kwerendy dla wiadomości kierowanych `level="storage"`do magazynu jest .

**włączone:** To pole domyślnie wskazuje, `true`że trasa wiadomości powinna być włączona po utworzeniu.

Skopiuj ten skrypt i wklej go do okna Powłoki chmury.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Następnym krokiem jest utworzenie punktu końcowego routingu dla konta magazynu. Należy również określić kontener, w którym będą przechowywane wyniki. Kontener został utworzony podczas tworzenia konta magazynu.

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

Następnie utwórz trasę wiadomości dla punktu końcowego magazynu. Trasa wiadomości określa, gdzie mają być wysyłane wiadomości, które spełniają specyfikację kwerendy.

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

### <a name="route-to-a-service-bus-queue"></a>Trasa do kolejki usługi Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Aby pobrać parametry połączenia dla kolejki usługi Service Bus, należy utworzyć regułę autoryzacji, która ma zdefiniowane poprawne prawa. Poniższy skrypt tworzy regułę autoryzacji `sbauthrule`dla kolejki usługi `Listen Manage Send`Service Bus o nazwie , i ustawia prawa do . Po skonfigurowaniu tej reguły autoryzacji można jej użyć do pobrania ciągu połączenia dla kolejki.

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

Teraz użyj reguły autoryzacji, aby pobrać klucz kolejki usługi Service Bus. Ta reguła autoryzacji będzie używana do pobierania ciągu połączenia w dalszej części skryptu.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Teraz skonfiguruj punkt końcowy routingu i trasę komunikatów dla kolejki usługi Service Bus. Są to zmienne używane przez skrypt, który musi być ustawiony w ramach sesji Cloud Shell:

**endpointName**: To pole jest nazwą identyfikującą punkt końcowy. 

**typ punktu końcowego:** To pole jest typem punktu końcowego. Wartość ta musi `azurestoragecontainer`być `eventhub` `servicebusqueue`ustawiona `servicebustopic`na , , lub . Dla twoich celów tutaj `servicebusqueue`ustaw go na .

**routeName**: To pole jest nazwą trasy, którą konfigurujesz. 

**warunek:** To pole jest kwerendą używaną do filtrowania wiadomości wysyłanych do tego punktu końcowego. Warunkiem kwerendy dla wiadomości kierowanych do kolejki `level="critical"`usługi Service Bus jest .

Oto program Azure PowerShell dla routingu komunikatów dla kolejki usługi Service Bus.

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

### <a name="view-message-routing-in-the-portal"></a>Wyświetlanie routingu wiadomości w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz skonfigurowane zasoby i trasy wiadomości skonfigurowane, przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać wiadomości do centrum IoT hub i zobaczyć, że są kierowane do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 - Wyświetlanie wyników routingu wiadomości](tutorial-routing-view-message-routing-results.md)