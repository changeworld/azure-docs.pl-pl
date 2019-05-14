---
title: Skonfiguruj routing komunikatów dla usługi Azure IoT Hub przy użyciu wiersza polecenia platformy Azure i witryny Azure portal | Dokumentacja firmy Microsoft
description: Skonfiguruj routing komunikatów dla usługi Azure IoT Hub przy użyciu wiersza polecenia platformy Azure i witryny Azure portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: d81b01992bd3bdd49a48a873281d1be1e795497a
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556015"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Samouczek: Skonfiguruj routing komunikatów usługi IoT Hub za pomocą wiersza polecenia platformy Azure i witryny Azure portal

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Użyj wiersza polecenia platformy Azure, aby utworzyć zasoby podstawowe

W tym samouczku korzysta z wiersza polecenia platformy Azure do tworzenia podstawowych zasobów, a następnie używa [witryny Azure portal](https://portal.azure.com) i pokazuje, jak skonfigurować routing komunikatów i konfigurowanie urządzenia wirtualnego do testowania.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowa, takie jak nazwa usługi IoT Hub i nazwy konta magazynu. Aby to ułatwić, te nazwy zasobu są dołączane za pomocą losowych wartości alfanumeryczne nazywanej *randomValue*. RandomValue jest generowane raz w górnej części skryptu i dołączany do nazwy zasobów, zgodnie z potrzebami w całym skrypcie. Jeśli nie chcesz, aby była losowych, można ustawić na ciąg pusty lub określoną wartość.

Skopiuj i wklej poniższy skrypt w usłudze Cloud Shell, a następnie naciśnij klawisz Enter. Jeden wiersz skryptu jest uruchamiane w danym momencie. Spowoduje to utworzenie zasoby podstawowe na potrzeby tego samouczka, w tym konto magazynu, usługi IoT Hub, Usługa Service Bus Namespace i kolejki usługi Service Bus.

Pamiętaj o debugowaniu: w tym skrypcie użyto symbol kontynuacji (ukośnik odwrotny `\`) aby skrypt był bardziej czytelny. Jeśli masz problem podczas uruchamiania skryptu, upewnij się, że istnieją spacji po każdym ukośników odwrotnych.

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Teraz, gdy zasoby podstawowe są skonfigurowane, można skonfigurować routing komunikatów w [witryny Azure portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Kierować do konta magazynu

Teraz należy skonfigurować routing dla konta magazynu. Przejdź do okienka Kierowanie komunikatów, a następnie dodaj trasę. Podczas dodawania trasy zdefiniuj nowy punkt końcowy trasy. Po skonfigurowaniu tego routing komunikatów gdzie **poziom** właściwość jest ustawiona na **magazynu** są automatycznie zapisywane na koncie magazynu. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **grup zasobów**, następnie wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

2. Wybierz Centrum IoT na liście zasobów. W tym samouczku jest używane centrum **ContosoTestHub**.

3. Wybierz **Routing komunikatów**. W **Routing komunikatów** okienku wybierz pozycję +**Dodaj**. Na **Dodaj trasę** okienku wybierz pozycję +**Dodaj** obok pola punktu końcowego, aby wyświetlić obsługiwane punkty końcowe, ponieważ wyświetlany na poniższej ilustracji:

   ![Rozpocznij dodawanie punktu końcowego dla trasy](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Wybierz pozycję **Blob storage**. Zostanie wyświetlony **Dodawanie punktu końcowego magazynu** okienka.

   ![Dodawanie punktu końcowego](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Wprowadź nazwę punktu końcowego. W tym samouczku **ContosoStorageEndpoint**.

6. Wybierz **Wybierz kontener**. Spowoduje to przejście do listy kont magazynu. Wybierz konto skonfigurowane podczas wykonywania kroków przygotowywania. W tym samouczku jest używana kolejka **contosostorage**. Przedstawia ona listę kontenerów na danym koncie magazynu. **Wybierz** kontener został skonfigurowany w procedury przygotowywania. W tym samouczku jest używany kontener **contosoresults**. Wróć do **Dodawanie punktu końcowego magazynu** okienka i sprawdź wybory dokonane.

7. Ustaw kodowanie AVRO lub JSON. Na potrzeby tego samouczka użyj wartości domyślnych dla pozostałych pól. To pole będą wyszarzone, jeśli region wybrany nie obsługuje kodowania w formacie JSON.,

   > [!NOTE]
   > Możesz określić format nazwy obiektu blob przy użyciu **formatu nazwy pliku obiektu blob**. Wartość domyślna to `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Format musi zawierać pola {centrum IoT}, {partycja}, {RRRR}, {MM}, {DD}, {GG} i {mm} w dowolnej kolejności.
   >
   > Na przykład przy użyciu domyślnego formatu nazwy pliku obiektu blob, jeśli nazwa centrum to ContosoTestHub, a data/godzina to 30 października 2018 r., 10:56:00, nazwa obiektu blob będzie wyglądać następująco: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Obiekty blob są zapisywane w formacie Avro.
   >

8. Wybierz **Utwórz** można utworzyć punktu końcowego magazynu i dodać go do trasy. Spowoduje to powrót do okienka **Dodawanie trasy**.

9. Teraz podaj pozostałe informacje zapytania dotyczącego routingu. To zapytanie określa kryteria dotyczące wysyłania komunikatów do kontenera magazynu, który właśnie został dodany jako punkt końcowy. Wypełnij pola na ekranie.

   **Nazwa**: Wprowadź nazwę zapytania dotyczącego routingu. W tym samouczku **ContosoStorageRoute**.

   **Punkt końcowy**: Skonfigurowany w poprzednim kroku punkt końcowy.

   **Źródło danych**: Z listy rozwijanej wybierz pozycję **Komunikaty telemetrii urządzenia**.

   **Włącz trasę**: Pamiętaj, że to pole jest ustawione `enabled`.
   
   **Zapytanie dotyczące routingu**: Wprowadź `level="storage"` jako ciąg zapytania.

   ![Tworzenie routingu zapytania dla konta magazynu](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Wybierz pozycję **Zapisz**. Po zakończeniu nastąpi powrót do okienka Kierowanie komunikatów, w którym można zobaczyć nowe zapytanie dotyczące routingu dla magazynu. Zamknij okienko Trasy, co spowoduje powrót na stronę grupy zasobów.

### <a name="route-to-a-service-bus-queue"></a>Kierowanie do kolejki usługi Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Przejdź do okienka Kierowanie komunikatów, a następnie dodaj trasę. Podczas dodawania trasy zdefiniuj nowy punkt końcowy trasy. Po skonfigurowaniu tej trasy wiadomości gdzie **poziom** właściwość jest ustawiona na **krytyczne** są zapisywane do kolejki usługi Service Bus, co powoduje wyzwolenie aplikacji logiki, a następnie wysyła wiadomość e-mail z informacjami.

1. Na stronie grupy zasobów wybierz Centrum IoT hub, a następnie wybierz **Routing komunikatów**.

2. W **Routing komunikatów** okienku wybierz pozycję +**Dodaj**.

3. Na **Dodaj trasę** okienku wybierz +**Dodaj** obok pola punktu końcowego. Wybierz pozycję **Kolejka usługi Service Bus**. Zostanie wyświetlone okienko **Dodawanie punktu końcowego usługi Service Bus**.

   ![Dodawanie punktu końcowego usługi Service bus](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Wypełnij następujące pola:

   **Nazwa punktu końcowego**: Wprowadź nazwę punktu końcowego. W tym samouczku **ContosoSBQueueEndpoint**.
   
   **Przestrzeń nazw usługi Service Bus**: Użyj listy rozwijanej, aby wybrać ustawionych w krokach przygotowanie przestrzeni nazw magistrali usług. W tym samouczku jest używana nazwa **ContosoSBNamespace**.

   **Kolejka usługi Service Bus**: Użyj listy rozwijanej, aby wybrać kolejki usługi Service Bus. W tym samouczku jest używana kolejka **contososbqueue**.

5. Wybierz **Utwórz** można dodać punkt końcowy kolejki usługi Service Bus. Spowoduje to powrót do okienka **Dodawanie trasy**.

6. Teraz podaj pozostałe informacje zapytania dotyczącego routingu. To zapytanie określa kryteria dotyczące wysyłania komunikatów do kolejki usługi Service Bus, która właśnie została dodana jako punkt końcowy. Wypełnij pola na ekranie. 

   **Nazwa**: Wprowadź nazwę zapytania dotyczącego routingu. W tym samouczku **ContosoSBQueueRoute**. 

   **Punkt końcowy**: Skonfigurowany w poprzednim kroku punkt końcowy.

   **Źródło danych**: Z listy rozwijanej wybierz pozycję **Komunikaty telemetrii urządzenia**.

   **Zapytanie dotyczące routingu**: Wprowadź `level="critical"` jako ciąg zapytania. 

   ![Utwórz kwerendę routingu dla kolejki usługi Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Wybierz pozycję **Zapisz**. Po powrocie do okienka Trasy zobaczysz obydwie nowe trasy wyświetlone w poniższy sposób.

   ![Trasy, które właśnie skonfigurowanym](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Możesz zobaczyć niestandardowe punkty końcowe, konfigurowanie, wybierając **niestandardowe punkty końcowe** kartę.

   ![Niestandardowy punkt końcowy, który po prostu skonfigurować](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zamknij okienko Kierowanie komunikatów, co spowoduje powrót do okienka grupy zasobów.

## <a name="create-a-simulated-device"></a>Tworzenie symulowanego urządzenia

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz zasoby, konfigurowanie i skonfigurowane trasy wiadomości, przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać komunikaty do usługi IoT hub i wyświetlać je można kierować do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 — wyświetlanie wyników routing wiadomości](tutorial-routing-view-message-routing-results.md)
