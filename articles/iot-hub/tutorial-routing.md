---
title: Konfigurowanie routingu wiadomości dla usługi Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure
description: Konfigurowanie routingu wiadomości dla usługi Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure i witryny Azure portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 38a40d628b883c0e7ada824d47d3fdf3d29caf93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084387"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Samouczek: konfigurowanie routingu komunikatów usługi IoT Hub za pomocą interfejsu wiersza polecenia platformy Azure i witryny Azure Portal

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Tworzenie zasobów podstawowych za pomocą interfejsu wiersza polecenia platformy Azure

W tym samouczku użyto interfejsu wiersza polecenia platformy Azure do utworzenia zasobów podstawowych, a następnie używa [portalu Azure](https://portal.azure.com) portal, aby pokazać, jak skonfigurować routing wiadomości i skonfigurować urządzenie wirtualne do testowania.

Skopiuj i wklej poniższy skrypt do usługi Cloud Shell i naciśnij klawisz Enter. Uruchamia skrypt po jednym wierszu naraz. Spowoduje to utworzenie zasobów podstawowych dla tego samouczka, w tym konta magazynu, Centrum IoT, obszaru nazw magistrali usług i kolejki usługi Service Bus.

Istnieje kilka nazw zasobów, które muszą być unikatowe globalnie, takich jak nazwa Centrum IoT i nazwa konta magazynu. Aby to ułatwić, te nazwy zasobów są dołączane z losową wartością alfanumeryczną o nazwie *randomValue*. RandomValue jest generowany raz w górnej części skryptu i dołączane do nazw zasobów w razie potrzeby w całym skrypcie. Jeśli nie chcesz, aby był losowy, możesz ustawić go na pusty ciąg lub określoną wartość.

> [!TIP]
> Wskazówka dotycząca debugowania: ten skrypt używa symbolu `\`kontynuacji (ukośnika odwrotnego), aby skrypt był bardziej czytelny. Jeśli masz problem z uruchomieniem skryptu, upewnij `bash` się, że sesja cloud shell jest uruchomiona i że nie ma spacji po każdym z ukośników odwrotnych.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
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

Teraz, gdy zasoby podstawowe są skonfigurowane, można skonfigurować routing wiadomości w [witrynie Azure portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Kierowanie do konta magazynu

Teraz należy skonfigurować routing dla konta magazynu. Przejdź do okienka Kierowanie komunikatów, a następnie dodaj trasę. Podczas dodawania trasy zdefiniuj nowy punkt końcowy trasy. Po skonfigurowaniu tego routingu wiadomości, w których właściwość **level** jest ustawiona na **magazyn,** są automatycznie zapisywane na koncie magazynu. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

2. Wybierz centrum IoT w obszarze listy zasobów. W tym samouczku jest używane centrum **ContosoTestHub**.

3. Wybierz **pozycję Routing wiadomości**. W okienku **Routing wiadomości** wybierz pozycję +**Dodaj**. W **okienku Dodawanie trasy** wybierz pozycję +**Dodaj** obok pola Punkt końcowy, aby wyświetlić obsługiwane punkty końcowe, jak pokazano na poniższym obrazie:

   ![Rozpoczynanie dodawania punktu końcowego dla trasy](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Wybierz pozycję **Blob storage**. Zobaczysz okienko **Dodaj punkt końcowy magazynu.**

   ![Dodawanie punktu końcowego](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Wprowadź nazwę punktu końcowego. W tym samouczku użyto programu **ContosoStorageEndpoint**.

6. Wybierz **wybierz wybierz wybierz kontener**. Spowoduje to przejście do listy kont magazynu. Wybierz konto skonfigurowane podczas wykonywania kroków przygotowywania. W tym samouczku jest używana kolejka **contosostorage**. Przedstawia ona listę kontenerów na danym koncie magazynu. **Wybierz** kontener skonfigurowany w krokach przygotowania. W tym samouczku jest używany kontener **contosoresults**. Powrót do okienka **Dodaj punkt końcowy magazynu** i zobacz dokonane wybory.

7. Ustaw kodowanie na AVRO lub JSON. Na potrzeby tego samouczka użyj wartości domyślnych dla pozostałych pól. To pole będzie wyszarzone, jeśli wybrany region nie obsługuje kodowania JSON.,

   > [!NOTE]
   > Możesz określić format nazwy obiektu blob przy użyciu **formatu nazwy pliku obiektu blob**. Wartość domyślna to `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Format musi zawierać pola {centrum IoT}, {partycja}, {RRRR}, {MM}, {DD}, {GG} i {mm} w dowolnej kolejności.
   >
   > Na przykład przy użyciu domyślnego formatu nazwy pliku obiektu blob, jeśli nazwa centrum to ContosoTestHub, a data/godzina to 30 października 2018 r., 10:56:00, nazwa obiektu blob będzie wyglądać następująco: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Obiekty blob są zapisywane w formacie Avro.
   >

8. Wybierz **pozycję Utwórz,** aby utworzyć punkt końcowy magazynu i dodać go do trasy. Spowoduje to powrót do okienka **Dodawanie trasy**.

9. Teraz podaj pozostałe informacje zapytania dotyczącego routingu. To zapytanie określa kryteria dotyczące wysyłania komunikatów do kontenera magazynu, który właśnie został dodany jako punkt końcowy. Wypełnij pola na ekranie.

   **Nazwa**: wprowadź nazwę zapytania dotyczącego routingu. W tym samouczku użyto **programu ContosoStorageRoute**.

   **Punkt końcowy**: właśnie skonfigurowany punkt końcowy.

   **Źródło danych**: z listy rozwijanej wybierz pozycję **Komunikaty telemetrii urządzenia**.

   **Włącz trasę:** Upewnij się, `enabled`że to pole jest ustawione na .
   
   **Zapytanie dotyczące routingu**: wprowadź `level="storage"` jako ciąg zapytania.

   ![Tworzenie kwerendy routingu dla konta magazynu](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Wybierz **pozycję Zapisz**. Po zakończeniu nastąpi powrót do okienka Kierowanie komunikatów, w którym można zobaczyć nowe zapytanie dotyczące routingu dla magazynu. Zamknij okienko Trasy, co spowoduje powrót na stronę grupy zasobów.

### <a name="route-to-a-service-bus-queue"></a>Trasa do kolejki usługi Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Przejdź do okienka Kierowanie komunikatów, a następnie dodaj trasę. Podczas dodawania trasy zdefiniuj nowy punkt końcowy trasy. Po skonfigurowaniu tej trasy wiadomości, w których właściwość **level** jest ustawiona na **krytyczną,** są zapisywane w kolejce usługi Service Bus, która wyzwala aplikację logiki, która następnie wysyła wiadomość e-mail z informacjami.

1. Na stronie Grupa zasobów wybierz centrum IoT, a następnie wybierz pozycję **Routing wiadomości**.

2. W okienku **Routing wiadomości** wybierz pozycję +**Dodaj**.

3. W **okienku Dodawanie trasy** wybierz pozycję +**Dodaj** obok pola Punkt końcowy. Wybierz pozycję **Kolejka usługi Service Bus**. Zostanie wyświetlone okienko **Dodawanie punktu końcowego usługi Service Bus**.

   ![Dodawanie punktu końcowego magistrali usług](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Wypełnij następujące pola:

   **Nazwa punktu końcowego**: wprowadź nazwę punktu końcowego. W tym samouczku użyto programu **ContosoSBQueueEndpoint**.
   
   **Obszar nazw magistrali usług:** Użyj listy rozwijanej, aby wybrać obszar nazw magistrali usług skonfigurowany w krokach przygotowania. W tym samouczku jest używana nazwa **ContosoSBNamespace**.

   **Kolejka usługi Service Bus:** Użyj listy rozwijanej, aby wybrać kolejkę usługi Service Bus. W tym samouczku jest używana kolejka **contososbqueue**.

5. Wybierz **pozycję Utwórz,** aby dodać punkt końcowy kolejki usługi Service Bus. Spowoduje to powrót do okienka **Dodawanie trasy**.

6. Teraz podaj pozostałe informacje zapytania dotyczącego routingu. To zapytanie określa kryteria dotyczące wysyłania komunikatów do kolejki usługi Service Bus, która właśnie została dodana jako punkt końcowy. Wypełnij pola na ekranie. 

   **Nazwa**: wprowadź nazwę zapytania dotyczącego routingu. W tym samouczku **użyto funkcji ContosoSBQueueRoute**. 

   **Punkt końcowy**: właśnie skonfigurowany punkt końcowy.

   **Źródło danych**: z listy rozwijanej wybierz pozycję **Komunikaty telemetrii urządzenia**.

   **Zapytanie dotyczące routingu**: wprowadź `level="critical"` jako ciąg zapytania. 

   ![Tworzenie kwerendy routingu dla kolejki usługi Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Wybierz **pozycję Zapisz**. Po powrocie do okienka Trasy zobaczysz obydwie nowe trasy wyświetlone w poniższy sposób.

   ![Trasy, które właśnie ustawiłeś](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Niestandardowe punkty końcowe można wyświetlić, wybierając kartę **Niestandardowe punkty końcowe.**

   ![Niestandardowy punkt końcowy, który właśnie skonfigurowałeś](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zamknij okienko Kierowanie komunikatów, co spowoduje powrót do okienka grupy zasobów.

## <a name="create-a-simulated-device"></a>Tworzenie symulowanego urządzenia

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz skonfigurowane zasoby i trasy wiadomości skonfigurowane, przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać wiadomości do centrum IoT hub i zobaczyć, że są kierowane do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 - Wyświetlanie wyników routingu wiadomości](tutorial-routing-view-message-routing-results.md)
