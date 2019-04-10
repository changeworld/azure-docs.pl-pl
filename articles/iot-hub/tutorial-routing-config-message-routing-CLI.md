---
title: Skonfiguruj routing komunikatów usługi Azure IoT Hub przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Skonfiguruj routing komunikatów usługi Azure IoT Hub przy użyciu wiersza polecenia platformy Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6faa585f1ad38eb981e0bbffffef603c4aab0bc8
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360296"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Samouczek: Użyj wiersza polecenia platformy Azure, aby skonfigurować routing komunikatów usługi IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Pobierz skrypt (opcjonalnie)

Do drugiej części tego samouczka pobieranie i uruchamianie aplikacji programu Visual Studio, aby wysyłać komunikaty do usługi IoT Hub. Brak folderu do pobrania, który zawiera szablon usługi Azure Resource Manager i pliku parametrów, a także skrypty programu PowerShell i wiersza polecenia platformy Azure.

Jeśli chcesz wyświetlić skryptu Zakończono pobieranie [usługi Azure IoT C# przykłady](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozpakuj plik master.zip. Skrypt interfejsu wiersza polecenia platformy Azure jest w /iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Użyj wiersza polecenia platformy Azure, aby utworzyć zasoby

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowa, takie jak nazwa usługi IoT Hub i nazwy konta magazynu. Aby to ułatwić, te nazwy zasobu są dołączane za pomocą losowych wartości alfanumeryczne nazywanej *randomValue*. RandomValue jest generowane raz w górnej części skryptu i dołączany do nazwy zasobów, zgodnie z potrzebami w całym skrypcie. Jeśli nie chcesz, aby była losowych, można ustawić na ciąg pusty lub określoną wartość. 

> [!IMPORTANT]
> Zestaw zmiennych w początkowej skryptu są również używane przez skrypt routingu, więc uruchomić wszystkie skryptu w tej samej sesji usługi Cloud Shell. Po otwarciu nowej sesji, aby uruchomić skrypt do konfigurowania routingu, kilka zmiennych będzie brakuje wartości.
>

Skopiuj i wklej poniższy skrypt w usłudze Cloud Shell, a następnie naciśnij klawisz Enter. Jeden wiersz skryptu jest uruchamiane w danym momencie. Ta pierwsza sekcja skrypt utworzy zasoby podstawowe na potrzeby tego samouczka, w tym konto magazynu, usługi IoT Hub, Usługa Service Bus Namespace i kolejki usługi Service Bus. Podczas wykonywania kroków pozostałej części tego samouczka, skopiuj każdy blok skryptu i wklej go w usłudze Cloud Shell, aby go uruchomić.

> [!TIP]
> Porady dotyczące debugowania: w tym skrypcie użyto symbol kontynuacji (ukośnik odwrotny `\`) aby skrypt był bardziej czytelny. Jeśli masz problem podczas uruchamiania skryptu, upewnij się, że istnieją spacji po każdym ukośników odwrotnych.
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

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

Teraz, gdy zasoby podstawowe są skonfigurowane, możesz skonfigurować routing komunikatów.

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Aby utworzyć punkt końcowy routingu, użyj [tworzenie az iot hub routingu punkt końcowy](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Aby utworzyć trasy wiadomości dla punktu końcowego, należy użyć [tworzenie az iot hub trasy](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Kierować do konta magazynu

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Najpierw należy skonfigurować punkt końcowy dla konta magazynu, a następnie skonfiguruj trasy. 

Te zmienne są ustawiane:

**storageConnectionString**: Ta wartość jest pobierana z konta magazynu w poprzednim skrypcie. Jest on używany przez wiadomości routingu w celu uzyskania dostępu do konta magazynu.

  **resourceGroup**: Istnieją dwa wystąpienia grupy zasobów — ustaw je na grupy zasobów.

**punkt końcowy subscriptionID**: To pole jest ustawione na identyfikator subskrypcji platformy Azure dla punktu końcowego. 

**endpointType**: To pole jest typ punktu końcowego. Ta wartość musi być równa `azurestoragecontainer`, `eventhub`, `servicebusqueue`, lub `servicebustopic`. Do własnych celów w tym miejscu, ustaw ją na `azurestoragecontainer`.

**iotHubName**: To pole jest nazwę koncentratora, który będzie wykonywał routingu.

**containerName**: To pole jest nazwa kontenera na koncie magazynu, do którego będą zapisywane dane.

**kodowanie**: To pole będzie `avro` lub `json`. To wskazuje, że format przechowywanych danych.

**routeName**: To pole jest nazwa trasy, które konfigurujesz. 

**Nazwapunktukoncowego**: To pole jest nazwa identyfikująca punktu końcowego. 

**Włączone**: Domyślnie to pole `true`, wskazujący, że trasy wiadomości powinien być włączony, po utworzeniu.

**Warunek**: To pole jest zapytania używane do filtrowania komunikatów wysłanych do tego punktu końcowego. Warunek kwerendy komunikaty przesyłane do magazynu jest `level="storage"`.

Ten skrypt skopiuj i wklej go w oknie usługi Cloud Shell i uruchom go.

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

Następnym krokiem jest utworzyć routingu punkt końcowy dla konta magazynu. Należy również określić kontener, w którym przechowywane będą wyniki. Kontener został utworzony wcześniej podczas tworzenia konta magazynu.

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

Następnie należy utworzyć trasę dla punktu końcowego magazynu. Trasy wiadomości wskazuje, gdzie wysyłać komunikaty, które spełniają specyfikacji zapytania. 

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

### <a name="route-to-a-service-bus-queue"></a>Kierowanie do kolejki usługi Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Aby pobrać parametry połączenia dla kolejki usługi Service Bus, należy utworzyć regułę autoryzacji, które ma odpowiednie prawa, które są zdefiniowane. Poniższy skrypt tworzy regułę autoryzacji dla kolejki usługi Service Bus, o nazwie `sbauthrule`i ustawia uprawnienia `Listen Manage Send`. Po zdefiniowaniu ta reguła autoryzacji służy do pobrania parametrów połączenia dla kolejki.

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

Teraz użyć reguły autoryzacji, aby pobrać parametry połączenia do kolejki usługi Service Bus.

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

Teraz Konfigurowanie routingu punkt końcowy i trasy wiadomości do kolejki usługi Service Bus. Te zmienne są ustawiane:

**Nazwapunktukoncowego**: To pole jest nazwa identyfikująca punktu końcowego. 

**endpointType**: To pole jest typ punktu końcowego. Ta wartość musi być równa `azurestoragecontainer`, `eventhub`, `servicebusqueue`, lub `servicebustopic`. Do własnych celów w tym miejscu, ustaw ją na `servicebusqueue`.

**routeName**: To pole jest nazwa trasy, które konfigurujesz. 

**Warunek**: To pole jest zapytania używane do filtrowania komunikatów wysłanych do tego punktu końcowego. Warunek kwerendy dla wiadomości jest kierowany do kolejki usługi Service Bus jest `level="critical"`.

Oto wiersza polecenia platformy Azure dla punktu końcowego routingu i trasy wiadomości do kolejki usługi Service Bus.

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

### <a name="view-message-routing-in-the-portal"></a>Wyświetl routing komunikatów w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz zasoby, konfigurowanie i skonfigurowane trasy wiadomości, przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać komunikaty do usługi IoT hub i wyświetlać je można kierować do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 — wyświetlanie wyników routing wiadomości](tutorial-routing-view-message-routing-results.md)