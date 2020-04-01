---
title: Konfigurowanie routingu wiadomości dla usługi Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure
description: Skonfiguruj routing wiadomości dla usługi Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. W zależności od właściwości w wiadomości, trasy do konta magazynu lub kolejki usługi Service Bus.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 056dac7977115f97892d8dbfde0710e00237804e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78674343"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Samouczek: konfigurowanie routingu komunikatów usługi IoT Hub za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Pobierz skrypt (opcjonalnie)

W drugiej części tego samouczka można pobrać i uruchomić aplikację programu Visual Studio do wysyłania wiadomości do Centrum IoT Hub. Istnieje folder w pobieranie, który zawiera szablon usługi Azure Resource Manager i plik parametrów, a także skrypty interfejsu wiersza polecenia platformy Azure i programu PowerShell.

Jeśli chcesz wyświetlić gotowy skrypt, pobierz [przykłady usługi Azure IoT C#.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Rozpaj plik master.zip. Skrypt interfejsu wiersza polecenia platformy Azure jest w /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ jako **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Tworzenie zasobów za pomocą interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej poniższy skrypt do usługi Cloud Shell i naciśnij klawisz Enter. Uruchamia skrypt po jednym wierszu naraz. W tej pierwszej sekcji skryptu utworzy się zasoby podstawowe dla tego samouczka, w tym konto magazynu, Centrum IoT Hub, obszar nazw magistrali usług i kolejka usługi Service Bus. W trakcie przechodzenia przez resztę samouczka skopiuj każdy blok skryptu i wklej go do usługi Cloud Shell, aby go uruchomić.

> [!TIP]
> Wskazówka dotycząca debugowania: ten skrypt używa symbolu `\`kontynuacji (ukośnika odwrotnego), aby skrypt był bardziej czytelny. Jeśli masz problem z uruchomieniem skryptu, upewnij `bash` się, że sesja cloud shell jest uruchomiona i że nie ma spacji po każdym z ukośników odwrotnych.
> 

Istnieje kilka nazw zasobów, które muszą być unikatowe globalnie, takich jak nazwa Centrum IoT i nazwa konta magazynu. Aby to ułatwić, te nazwy zasobów są dołączane z losową wartością alfanumeryczną o nazwie *randomValue*. RandomValue jest generowany raz w górnej części skryptu i dołączane do nazw zasobów w razie potrzeby w całym skrypcie. Jeśli nie chcesz, aby był losowy, możesz ustawić go na pusty ciąg lub określoną wartość. 

> [!IMPORTANT]
> Zmienne ustawione w skrypcie początkowym są również używane przez skrypt routingu, więc uruchom cały skrypt w tej samej sesji cloud shell. Jeśli otworzysz nową sesję, aby uruchomić skrypt do konfigurowania routingu, kilka zmiennych będzie brakować wartości.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Teraz, gdy zasoby podstawowe są skonfigurowane, można skonfigurować routing wiadomości.

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Aby utworzyć punkt końcowy routingu, należy użyć [programu AZ iot Hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Aby utworzyć trasę wiadomości dla punktu końcowego, użyj [programu AZ iot Hub route create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Kierowanie do konta magazynu

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Najpierw skonfiguruj punkt końcowy dla konta magazynu, a następnie skonfiguruj marszrutę. 

Są to zmienne używane przez skrypt, który musi być ustawiony w ramach sesji Cloud Shell:

**storageConnectionString**: Ta wartość jest pobierana z konta magazynu skonfigurowanego w poprzednim skrypcie. Jest on używany przez routing wiadomości, aby uzyskać dostęp do konta magazynu.

  **resourceGroup**: Istnieją dwa wystąpienia grupy zasobów — ustaw je na grupę zasobów.

**identyfikator subskrypcji punktu końcowego:** to pole jest ustawione na identyfikator subskrypcji platformy Azure dla punktu końcowego. 

**typ punktu końcowego:** To pole jest typem punktu końcowego. Wartość ta musi `azurestoragecontainer`być `eventhub` `servicebusqueue`ustawiona `servicebustopic`na , , lub . Dla twoich celów tutaj `azurestoragecontainer`ustaw go na .

**iotHubName:** To pole jest nazwą koncentratora, który wykona marszrutę.

**containerName:** To pole jest nazwą kontenera na koncie magazynu, do którego będą zapisywane dane.

**kodowanie:** To pole będzie `avro` `json`albo lub . Oznacza to format przechowywanych danych.

**routeName**: To pole jest nazwą trasy, którą konfigurujesz. 

**endpointName**: To pole jest nazwą identyfikującą punkt końcowy. 

**włączone:** To pole domyślnie wskazuje, `true`że trasa wiadomości powinna być włączona po utworzeniu.

**warunek:** To pole jest kwerendą używaną do filtrowania wiadomości wysyłanych do tego punktu końcowego. Warunkiem kwerendy dla wiadomości kierowanych `level="storage"`do magazynu jest .

Skopiuj ten skrypt i wklej go do okna Cloud Shell i uruchom go.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Następnym krokiem jest utworzenie punktu końcowego routingu dla konta magazynu. Należy również określić kontener, w którym będą przechowywane wyniki. Kontener został utworzony wcześniej podczas tworzenia konta magazynu.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Następnie utwórz trasę dla punktu końcowego magazynu. Trasa wiadomości określa, gdzie mają być wysyłane wiadomości, które spełniają specyfikację kwerendy. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Trasa do kolejki usługi Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Aby pobrać parametry połączenia dla kolejki usługi Service Bus, należy utworzyć regułę autoryzacji, która ma zdefiniowane poprawne prawa. Poniższy skrypt tworzy regułę autoryzacji `sbauthrule`dla kolejki usługi `Listen Manage Send`Service Bus o nazwie , i ustawia prawa do . Po zdefiniowaniu tej reguły autoryzacji można jej użyć do pobrania ciągu połączenia dla kolejki.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Teraz użyj reguły autoryzacji, aby pobrać parametry połączenia z kolejką usługi Service Bus.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Teraz skonfiguruj punkt końcowy routingu i trasę komunikatów dla kolejki usługi Service Bus. Są to zmienne używane przez skrypt, który musi być ustawiony w ramach sesji Cloud Shell:

**endpointName**: To pole jest nazwą identyfikującą punkt końcowy. 

**typ punktu końcowego:** To pole jest typem punktu końcowego. Wartość ta musi `azurestoragecontainer`być `eventhub` `servicebusqueue`ustawiona `servicebustopic`na , , lub . Dla twoich celów tutaj `servicebusqueue`ustaw go na .

**routeName**: To pole jest nazwą trasy, którą konfigurujesz. 

**warunek:** To pole jest kwerendą używaną do filtrowania wiadomości wysyłanych do tego punktu końcowego. Warunkiem kwerendy dla wiadomości kierowanych do kolejki `level="critical"`usługi Service Bus jest .

Oto narzędzie wiersza polecenia platformy Azure dla punktu końcowego routingu i trasa komunikatu dla kolejki usługi Service Bus.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Wyświetlanie routingu wiadomości w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz skonfigurowane zasoby i trasy wiadomości skonfigurowane, przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać wiadomości do centrum IoT hub i zobaczyć, że są kierowane do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 - Wyświetlanie wyników routingu wiadomości](tutorial-routing-view-message-routing-results.md)
