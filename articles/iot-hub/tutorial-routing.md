---
title: Konfigurowanie routingu komunikatów przy użyciu usługi Azure IoT Hub (.NET) | Microsoft Docs
description: Konfigurowanie routingu komunikatów przy użyciu usługi Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 87d0339de117330bf6d586cd653b0d4d16a8cbca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087707"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Samouczek: Konfigurowanie routingu wiadomości przy użyciu usługi IoT Hub

[Routing komunikatów](iot-hub-devguide-messages-d2c.md) umożliwia wysyłanie danych telemetrycznych z urządzeń IoT do wbudowanych punktów końcowych zgodnych z centrum Event Hubs lub do niestandardowych punktów końcowych, takich jak usługa Blob Storage, kolejka usługi Service Bus, temat usługi Service Bus i usługa Event Hubs. Podczas konfigurowania routingu komunikatów można utworzyć [zapytania dotyczące routingu](iot-hub-devguide-routing-query-syntax.md), aby dostosować trasę spełniającą określony warunek. Po zakończeniu konfiguracji dane przychodzące są automatycznie rozsyłane do punktów końcowych przez usługę IoT Hub. 

Z tego samouczka dowiesz się, jak skonfigurować zapytania dotyczące routingu i korzystać z nich za pomocą usługi IoT Hub. Będziesz przekierowywać komunikaty z urządzenia IoT do jednej z wielu usług, w tym z magazynu obiektów blob i kolejki usługi Service Bus. Komunikaty do kolejki usługi Service Bus będą pobierane przez aplikację logiki i wysyłane pocztą e-mail. Komunikaty, które nie mają specjalnie skonfigurowanego routingu, są wysyłane do domyślnego punktu końcowego i wyświetlane w wizualizacji usługi Power BI.

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
> * Używanie interfejsu wiersza polecenia platformy Azure lub programu PowerShell do konfigurowania zasobów podstawowych — centrum IoT Hub, konta magazynu, kolejki usługi Service Bus i urządzenia symulowanego.
> * Konfigurowanie punktów końcowych i tras w centrum IoT Hub dla konta magazynu i kolejki usługi Service Bus.
> * Tworzenie aplikacji logiki, która jest wyzwalana i wysyła wiadomość e-mail po dodaniu komunikatu do kolejki usługi Service Bus.
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum przy użyciu różnych opcji routingu.
> * Tworzenie wizualizacji usługi Power BI na potrzeby danych wysyłanych do domyślnego punktu końcowego.
> * Przeglądanie wyników...
> * ...w kolejce usługi Service Bus i wiadomościach e-mail.
> * ...na koncie magazynu.
> * ...w wizualizacji usługi Power BI.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Instalacja programu [Visual Studio](https://www.visualstudio.com/). 

- Konto usługi Power BI do analizowania danych analitycznych strumienia domyślnego punktu końcowego. ([Wypróbuj bezpłatnie usługę Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)).

- Konto usługi Office 365 do wysyłania powiadomień e-mail. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Konfigurowanie zasobów

W tym samouczku potrzebujesz centrum IoT Hub, konta magazynu i kolejki usługi Service Bus. Te zasoby można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Po zakończeniu możesz usunąć wszystko w jednym kroku, usuwając grupę zasobów.

W poniższych sekcjach opisano sposób wykonywania tych wymaganych czynności. Postępuj zgodnie z instrukcjami dotyczącymi interfejsu wiersza polecenia *lub* programu PowerShell.

1. Utwórz [grupę zasobów](../azure-resource-manager/resource-group-overview.md). 

2. Utwórz centrum IoT Hub w warstwie S1. Dodaj grupę użytkowników do centrum IoT Hub. Grupa użytkowników jest używana przez usługę Azure Stream Analytics podczas pobierania danych.

   > [!NOTE]
   > Aby ukończyć ten samouczek, należy użyć usługi IoT Hub w warstwie płatnej. Warstwa bezpłatna umożliwia wyłącznie skonfigurowanie jednego punktu końcowego, a w tym samouczku należy skonfigurować ich kilka.
   > 

3. Utwórz standardowe konto magazynu w wersji 1 przy użyciu replikacji Standard_LRS.

4. Utwórz przestrzeń nazw i kolejkę usługi Service Bus. 

5. Utwórz tożsamość urządzenia symulowanego, które wysyła wiadomości do centrum. Zapisz klucz na potrzeby fazy testowania.

### <a name="set-up-your-resources-using-azure-cli"></a>Konfigurowanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej ten skrypt w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu. 

Zmienne, które muszą być globalnie unikatowe, mają dołączony element `$RANDOM`. Gdy skrypt jest uruchamiany i zmienne są ustawione, wygenerowany zostaje losowy ciąg liczbowy i dołączony na końcu stałego ciągu, dzięki czemu staje się on unikatowy.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
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
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location

# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="set-up-your-resources-using-azure-powershell"></a>Konfigurowanie zasobów za pomocą programu Azure PowerShell

Skopiuj i wklej ten skrypt w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu.

Zmienne, które muszą być globalnie unikatowe, mają dołączony element `$(Get-Random)`. Gdy skrypt jest uruchamiany i zmienne są ustawione, wygenerowany zostaje losowy ciąg liczbowy i dołączony na końcu stałego ciągu, dzięki czemu staje się on unikatowy.

```azurepowershell-interactive
# Log into Azure account.
Login-AzAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
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

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

W kolejnym kroku utwórz tożsamość urządzenia i zapisz jego klucz do późniejszego użycia. Ta tożsamość urządzenia jest używana przez aplikację symulacji do wysyłania komunikatów do centrum IoT Hub. Ta możliwość nie jest dostępna w programie PowerShell, ale można utworzyć urządzenie w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się do konta platformy Azure.

2. Kliknij pozycję **Grupy zasobów** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

3. Na liście zasobów kliknij swoje centrum IoT Hub. W tym samouczku jest używane centrum **ContosoTestHub**. Wybierz pozycję **Urządzenia IoT** w okienku centrum.

4. Kliknij pozycję **+ Dodaj**. W okienku Dodawanie urządzenia wypełnij pole identyfikatora urządzenia. W tym samouczku jest używany identyfikator **Contoso-Test-Device**. Pozostaw klucze puste i zaznacz pole **Automatycznie generuj klucze**. Upewnij się, że pozycja **Połącz urządzenie z centrum IoT Hub** została włączona. Kliknij pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający ekran dodawania urządzenia.](./media/tutorial-routing/add-device.png)

5. Po utworzeniu urządzenia kliknij je, aby wyświetlić wygenerowane klucze. Kliknij ikonę kopiowania na kluczu podstawowym i zapisz go w dowolnym miejscu, takim jak Notatnik, do użycia w fazie testowania opisanej w tym samouczku.

   ![Zrzut ekranu przedstawiający szczegóły urządzenia, w tym klucze.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

Planujesz rozsyłać komunikaty do różnych zasobów na podstawie właściwości dołączonych do komunikatu za pośrednictwem urządzenia symulowanego. Komunikaty bez tras niestandardowych są wysyłane do domyślnego punktu końcowego (wiadomości/zdarzenia). 

|wartość |Wynik|
|------|------|
|level=„storage” |Zapis w usłudze Azure Storage.|
|level=„critical” |Zapis w kolejce usługi Service Bus. Aplikacja logiki pobiera komunikat z kolejki i używa usługi Office 365 do wysłania go pocztą e-mail.|
|default |Wyświetlanie tych danych przy użyciu usługi Power BI.|

### <a name="routing-to-a-storage-account"></a>Routing do konta magazynu 

Teraz należy skonfigurować routing dla konta magazynu. Przejdź do okienka Kierowanie komunikatów, a następnie dodaj trasę. Podczas dodawania trasy zdefiniuj nowy punkt końcowy trasy. Po zakończeniu konfigurowania komunikaty, w których właściwość **level** została ustawiona na **storage**, będą automatycznie zapisywane na koncie magazynu. 

Dane są zapisywane do magazynu w formacie Avro obiektów blob domyślnie.

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. 

2. Kliknij centrum IoT Hub na liście zasobów. W tym samouczku jest używane centrum **ContosoTestHub**. 

3. Kliknij pozycję **Kierowanie komunikatów**. W okienku **Kierowanie komunikatów** kliknij pozycję +**Dodaj**. W okienku **Dodawanie trasy** kliknij pozycję +**Dodaj** obok pola punktu końcowego, jak pokazano na poniższej ilustracji:

   ![Zrzut ekranu pokazujący, jak rozpocząć dodawanie punktu końcowego do trasy.](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Wybierz pozycję **Blob storage**. Zostanie wyświetlone okienko **Dodawanie punktu końcowego magazynu**. 

   ![Zrzut ekranu przedstawiający dodawanie punktu końcowego.](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Wprowadź nazwę punktu końcowego. W tym samouczku jest używana nazwa **StorageContainer**.

6. Kliknij pozycję **Wybierz kontener**. Spowoduje to przejście do listy kont magazynu. Wybierz konto skonfigurowane podczas wykonywania kroków przygotowywania. W tym samouczku jest używana kolejka **contosostorage**. Przedstawia ona listę kontenerów na danym koncie magazynu. Wybierz kontener skonfigurowany podczas wykonywania kroków przygotowywania. W tym samouczku jest używany kontener **contosoresults**. Kliknij pozycję **Wybierz**. Spowoduje to powrót do okienka **Dodawanie punktu końcowego**. 

7. Na potrzeby tego samouczka użyj wartości domyślnych dla pozostałych pól. 

   > [!NOTE]
   > Możesz określić format nazwy obiektu blob przy użyciu **formatu nazwy pliku obiektu blob**. Wartość domyślna to `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Format musi zawierać pola {centrum IoT}, {partycja}, {RRRR}, {MM}, {DD}, {GG} i {mm} w dowolnej kolejności. 
   > 
   > Na przykład przy użyciu domyślnego formatu nazwy pliku obiektu blob, jeśli nazwa centrum to ContosoTestHub, a data/godzina to 30 października 2018 r., 10:56:00, nazwa obiektu blob będzie wyglądać następująco: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Obiekty BLOB są domyślnie zapisywane w formacie Avro. Można zapisywać pliki w formacie JSON. Funkcja do zakodowania w formacie JSON jest w wersji zapoznawczej we wszystkich regionach, w których IoT Hub jest dostępna, z wyjątkiem wschodnie stany USA, zachodnie stany USA i Europa Zachodnia. Zobacz [wskazówki dotyczące routingu w usłudze blob storage](iot-hub-devguide-messages-d2c.md#azure-blob-storage).
   > 
   > Routing do magazynu obiektów blob, firma Microsoft zaleca funkcji rejestrowanie obiektów blob, a następnie Iterowanie po nich, aby upewnić się, że wszystkie kontenery są odczytywane bez wprowadzania żadnych założeń partycji. Zakres partycji potencjalnie mogą ulec zmianie podczas [inicjowanych przez Microsoft trybu failover](iot-hub-ha-dr.md#microsoft-initiated-failover) lub usługi IoT Hub [ręczna praca awaryjna](iot-hub-ha-dr.md#manual-failover-preview). Aby dowiedzieć się, jak wyliczyć listy obiektów blob, zobacz [routingu do magazynu obiektów blob](iot-hub-devguide-messages-d2c.md#azure-blob-storage)

8. Kliknij pozycję **Utwórz**, aby utworzyć punkt końcowy magazynu i dodać go do trasy. Spowoduje to powrót do okienka **Dodawanie trasy**.

9. Teraz podaj pozostałe informacje zapytania dotyczącego routingu. To zapytanie określa kryteria dotyczące wysyłania komunikatów do kontenera magazynu, który właśnie został dodany jako punkt końcowy. Wypełnij pola na ekranie. 

   **Nazwa**: Wprowadź nazwę zapytania dotyczącego routingu. W tym samouczku jest używana nazwa **StorageRoute**.

   **Punkt końcowy**: Skonfigurowany w poprzednim kroku punkt końcowy. 

   **Źródło danych**: Z listy rozwijanej wybierz pozycję **Komunikaty telemetrii urządzenia**.

   **Włącz trasę**: Sprawdź, czy ta opcja jest włączona.

   **Zapytanie dotyczące routingu**: Wprowadź `level="storage"` jako ciąg zapytania. 

   ![Zrzut ekranu przedstawiający tworzenie zapytania dotyczącego routingu dla konta magazynu.](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Kliknij pozycję **Zapisz**. Po zakończeniu nastąpi powrót do okienka Kierowanie komunikatów, w którym można zobaczyć nowe zapytanie dotyczące routingu dla magazynu. Zamknij okienko Trasy, co spowoduje powrót na stronę grupy zasobów.

### <a name="routing-to-a-service-bus-queue"></a>Routing do kolejki usługi Service Bus 

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Przejdź do okienka Kierowanie komunikatów, a następnie dodaj trasę. Podczas dodawania trasy zdefiniuj nowy punkt końcowy trasy. Po zakończeniu konfigurowania komunikaty, w których właściwość **level** została ustawiona na **critical**, będą zapisywane do kolejki usługi Service Bus wyzwalającej aplikację logiki, która następnie wysyła wiadomość e-mail z informacjami. 

1. Na stronie grupy zasobów kliknij centrum IoT Hub, a następnie kliknij pozycję **Kierowanie komunikatów**. 

2. W okienku **Kierowanie komunikatów** kliknij pozycję +**Dodaj**. 

3. W okienku **Dodawanie trasy** kliknij pozycję +**Dodaj** obok pola punktu końcowego. Wybierz pozycję **Kolejka usługi Service Bus**. Zostanie wyświetlone okienko **Dodawanie punktu końcowego usługi Service Bus**. 

   ![Zrzut ekranu przedstawiający dodawanie punktu końcowego usługi Service Bus](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Wypełnij następujące pola:

   **Nazwa punktu końcowego**: Wprowadź nazwę punktu końcowego. W tym samouczku jest używana nazwa **CriticalQueue**.

   **Przestrzeń nazw usługi Service Bus**: Kliknij to pole, aby wyświetlić listę rozwijaną. Wybierz przestrzeń nazw usługi Service Bus, która została skonfigurowana podczas procedury przygotowywania. W tym samouczku jest używana nazwa **ContosoSBNamespace**.

   **Kolejka usługi Service Bus**: Kliknij to pole, aby wyświetlić listę rozwijaną. Z tej listy wybierz kolejkę usługi Service Bus. W tym samouczku jest używana kolejka **contososbqueue**.

5. Kliknij pozycję **Utwórz**, aby dodać punkt końcowy kolejki usługi Service Bus. Spowoduje to powrót do okienka **Dodawanie trasy**. 

6. Teraz podaj pozostałe informacje zapytania dotyczącego routingu. To zapytanie określa kryteria dotyczące wysyłania komunikatów do kolejki usługi Service Bus, która właśnie została dodana jako punkt końcowy. Wypełnij pola na ekranie. 

   **Nazwa**: Wprowadź nazwę zapytania dotyczącego routingu. W tym samouczku jest używana nazwa **SBQueueRoute**. 

   **Punkt końcowy**: Skonfigurowany w poprzednim kroku punkt końcowy.

   **Źródło danych**: Z listy rozwijanej wybierz pozycję **Komunikaty telemetrii urządzenia**.

   **Zapytanie dotyczące routingu**: Wprowadź `level="critical"` jako ciąg zapytania. 

   ![Zrzut ekranu przedstawiający tworzenie zapytania dotyczącego zapytania dla kolejki usługi Service Bus.](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Kliknij pozycję **Zapisz**. Po powrocie do okienka Trasy zobaczysz obydwie nowe trasy wyświetlone w poniższy sposób.

   ![Zrzut ekranu przedstawiający właśnie skonfigurowane trasy.](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Aby wyświetlić skonfigurowane niestandardowe punkty końcowe, kliknij kartę **Niestandardowe punkty końcowe**.

   ![Zrzut ekranu przedstawiający właśnie skonfigurowane niestandardowe punkty końcowe.](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zamknij okienko Kierowanie komunikatów, co spowoduje powrót do okienka grupy zasobów.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki  

Kolejka usługi Service Bus jest używana do odbierania komunikatów oznaczonych jako krytyczne. Skonfiguruj aplikację logiki, aby monitorować kolejkę usługi Service Bus i wysyłać wiadomość e-mail po dodaniu komunikatu do kolejki. 

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **+ Utwórz zasób**. W polu wyszukiwania wpisz ciąg **aplikacja logiki** i naciśnij klawisz Enter. W wyświetlonych wynikach wyszukiwania wybierz aplikację logiki, a następnie kliknij pozycję **Utwórz**, aby przejść do okienka **Tworzenie aplikacji logiki**. Wypełnij pola. 

   **Nazwa**: To pole zawiera nazwę aplikacji logiki. W tym samouczku jest używana nazwa **ContosoLogicApp**. 

   **Subskrypcja**: Wybierz swoją subskrypcję platformy Azure.

   **Grupa zasobów**: Kliknij pozycję **Użyj istniejącej** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. 

   **Lokalizacja**: Użyj swojej lokalizacji. W tym samouczku jest używany region **Zachodnie stany USA**. 

   **Log Analytics**: Ten przełącznik powinien być wyłączony. 

   ![Zrzut ekranu przedstawiający ekran Tworzenie aplikacji logiki.](./media/tutorial-routing/create-logic-app.png)

   Kliknij pozycję **Utwórz**.

2. Teraz przejdź do aplikacji logiki. Najprostszym sposobem przejścia do aplikacji logiki jest kliknięcie pozycji **Grupy zasobów**, wybranie grupy zasobów (w tym samouczku jest używana grupa **ContosoResources**), a następnie wybranie aplikacji logiki z listy zasobów. Zostanie wyświetlona strona Projektanta aplikacji usługi Logic Apps (w celu wyświetlenia całej strony może być konieczne przewinięcie w prawo). Na stronie Projektanta aplikacji usługi Logic Apps przewiń w dół do kafelka z opisem **Pusta aplikacja logiki +** i kliknij go. 

3. Zostanie wyświetlona lista łączników. Wybierz pozycję **Service Bus**. 

   ![Zrzut ekranu przedstawiający listę łączników.](./media/tutorial-routing/logic-app-connectors.png)

4. Zostanie wyświetlona lista wyzwalaczy. Wybierz pozycję **Service Bus — po odebraniu komunikatu w kolejce (autouzupełnianie)**. 

   ![Zrzut ekranu przedstawiający listę wyzwalaczy usługi Service Bus.](./media/tutorial-routing/logic-app-triggers.png)

5. Na następnym ekranie wprowadź nazwę połączenia. W tym samouczku jest używana nazwa **ContosoConnection**. 

   ![Zrzut ekranu przedstawiający konfigurowanie połączenia na potrzeby kolejki usługi Service Bus.](./media/tutorial-routing/logic-app-define-connection.png)

   Kliknij przestrzeń nazw usługi Service Bus. W tym samouczku jest używana nazwa **ContosoSBNamespace**. Po wybraniu przestrzeni nazw portal wykonuje zapytanie w przestrzeni nazw usługi Service Bus w celu pobrania kluczy. Wybierz pozycję **RootManageSharedAccessKey** i kliknij pozycję **Utwórz**. 

   ![Zrzut ekranu przedstawiający zakończenie konfigurowania połączenia.](./media/tutorial-routing/logic-app-finish-connection.png)

6. Na następnym ekranie wybierz nazwę kolejki (w tym samouczku jest używana kolejka **contososbqueue**) z listy rozwijanej. W pozostałych polach można użyć wartości domyślnych. 

   ![Zrzut ekranu przedstawiający opcje kolejki.](./media/tutorial-routing/logic-app-queue-options.png)

7. Teraz skonfiguruj akcję wysyłania wiadomości e-mail po odebraniu komunikatu w kolejce. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **+ Nowy krok**, aby dodać krok, a następnie kliknij pozycję **Dodaj akcję**. W okienku **Wybieranie akcji** znajdź i kliknij pozycję **Office 365 Outlook**. Na ekranie wyzwalaczy wybierz pozycję **Office 365 Outlook — wysyłanie wiadomości e-mail**.  

   ![Zrzut ekranu przedstawiający opcje usługi Office 365.](./media/tutorial-routing/logic-app-select-outlook.png)

8. Następnie zaloguj się do konta usługi Office 365, aby skonfigurować połączenie. Podaj adresy e-mail adresatów wiadomości e-mail. Określ temat, a następnie wpisz treść wiadomości, którą ma zobaczyć adresat. Na potrzeby testowania wprowadź własny adres e-mail jako adresata.

   Kliknij pozycję **Dodaj zawartość dynamiczną**, aby wyświetlić zawartość komunikatu, którą można dołączyć. Wybierz pozycję **Zawartość** — będzie ona obejmować komunikat z wiadomości e-mail. 

   ![Zrzut ekranu przedstawiający opcje poczty e-mail dla aplikacji logiki.](./media/tutorial-routing/logic-app-send-email.png)

9. Kliknij pozycję **Zapisz**. Następnie zamknij Projektanta aplikacji logiki.

## <a name="set-up-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics

Aby wyświetlić dane w wizualizacji usługi Power BI, najpierw skonfiguruj zadanie usługi Stream Analytics w celu pobrania danych. Pamiętaj, że tylko komunikaty, w których właściwość **level** ma wartość **normal**, są wysyłane do domyślnego punktu końcowego i zostaną pobrane przez zadanie usługi Stream Analytics na potrzeby wizualizacji usługi Power BI.

### <a name="create-the-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: Nazwa zadania. Nazwa musi być unikatowa w skali globalnej. W tym samouczku jest używana nazwa **contosoJob**.

   **Grupa zasobów**: Użyj grupy zasobów używanej już w centrum IoT Hub. W tym samouczku jest używana grupa **ContosoResources**. 

   **Lokalizacja**: Użyj lokalizacji stosowanej już w skrypcie konfiguracji. W tym samouczku jest używany region **Zachodnie stany USA**. 

   ![Zrzut ekranu pokazujący sposób tworzenia zadania usługi Log Analytics.](./media/tutorial-routing/stream-analytics-create-job.png)

3. Kliknij pozycję **Utwórz**, aby utworzyć zadanie. Aby powrócić do zadania, kliknij pozycję **Grupy zasobów**. W tym samouczku jest używana grupa **ContosoResources**. Wybierz grupę zasobów, a następnie kliknij zadanie usługi Stream Analytics na liście zasobów. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.

1. W okienku **Dane wejściowe** kliknij pozycję **Dodaj wejście strumienia** i wybierz pozycję centrum IoT Hub. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias danych wejściowych**: W tym samouczku jest używany alias **contosoinputs**.

   **Subskrypcja**: Wybierz subskrypcję.

   **IoT Hub**: Wybierz centrum usługi IoT Hub. W tym samouczku jest używane centrum **ContosoTestHub**.

   **Punkt końcowy**: Wybierz pozycję **Obsługa komunikatów**. (W przypadku wybrania pozycji Monitorowanie operacji otrzymasz dane telemetryczne dotyczące centrum IoT Hub, a nie wysyłane dane). 

   **Nazwa zasad dostępu współdzielonego**: Wybierz pozycję **iothubowner**. Portal wypełni klucz wstępny zasad dostępu współdzielonego za Ciebie.

   **Grupy użytkowników**: Wybierz wcześniej utworzoną grupę użytkowników. W tym samouczku jest używana grupa **contosoconsumers**.

   W pozostałych polach zaakceptuj wartości domyślne. 

   ![Zrzut ekranu przedstawiający sposób konfigurowania danych wejściowych zadania usługi Stream Analytics.](./media/tutorial-routing/stream-analytics-job-inputs.png)

1. Kliknij pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.

2. W okienku **Dane wyjściowe** kliknij pozycję **Dodaj**, a następnie wybierz pozycję **Power BI**. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias danych wyjściowych**: Unikatowy alias danych wyjściowych. W tym samouczku jest używany alias **contosooutputs**. 

   **Nazwa zestawu danych**: Nazwa zestawu danych do użycia w usłudze Power BI. W tym samouczku jest używany zestaw danych **contosodataset**. 

   **Nazwa tabeli**: Nazwa tabeli do użycia w usłudze Power BI. W tym samouczku jest używana tabela **contosotable**.

   Zaakceptuj wartości domyślne w pozostałych polach.

3. Kliknij pozycję **Autoryzuj** i zaloguj się do konta usługi Power BI.

   ![Zrzut ekranu przedstawiający sposób konfigurowania danych wyjściowych zadania usługi Stream Analytics.](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. Kliknij pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania. W tym samouczku jest używany alias **contosoinputs**.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania. W tym samouczku jest używany alias **contosooutputs**.

   ![Zrzut ekranu przedstawiający sposób konfigurowania zapytania zadania usługi Stream Analytics.](./media/tutorial-routing/stream-analytics-job-query.png)

4. Kliknij pozycję **Zapisz**.

5. Zamknij okienko Zapytanie. Nastąpi powrót do widoku zasobów w grupie zasobów. Kliknij zadanie usługi Stream Analytics. W tym samouczku nosi ono nazwę **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu usługi Stream Analytics kliknij kolejno pozycje **Uruchom** > **Teraz** > **Uruchom**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

Do skonfigurowania raportu usługi Power BI potrzebujesz danych, dlatego po utworzeniu urządzenia i uruchomieniu aplikacji do symulacji urządzenia skonfigurujesz usługę Power BI.

## <a name="run-simulated-device-app"></a>Uruchamianie aplikacji urządzenia symulowanego

Wcześniej w sekcji konfigurowania skryptu skonfigurowano urządzenia do symulowania użycia urządzenia IoT. Ta sekcja zawiera instrukcje dotyczące pobierania aplikacji konsolowej .NET, która symuluje urządzenie wysyłające komunikaty z urządzenia do chmury do centrum IoT Hub. Ta aplikacja wysyła komunikaty przy użyciu każdej z różnych metod routingu. 

Pobierz rozwiązanie na potrzeby [symulacji urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Spowoduje to pobranie do niego repozytorium zawierającego kilka aplikacji; szukane rozwiązanie znajduje się w lokalizacji iot-hub/Tutorials/Routing/SimulatedDevice/.

Kliknij dwukrotnie plik rozwiązania (SimulatedDevice.sln), aby otworzyć kod w programie Visual Studio, a następnie otwórz plik Program.cs. Zastąp element `{iot hub hostname}` nazwą hosta centrum IoT Hub. Format nazwy hosta centrum IoT Hub to **{nazwa-centrum-iot}.azure-devices.net**. W tym samouczku jest używana nazwa hosta centrum **ContosoTestHub.azure-devices.net**. Następnie zastąp ciąg `{device key}` kluczem urządzenia, który został wcześniej zapisany podczas konfigurowania urządzenia symulowanego. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Uruchamianie i testowanie 

Uruchom aplikację konsolową. Zaczekaj kilka minut. Możesz zobaczyć komunikaty wysyłane na ekranie konsoli aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Poziom jest losowo przypisywany jako `critical` lub `storage`, co powoduje kierowanie komunikatu do konta magazynu lub do kolejki usługi Service Bus (która wyzwala aplikację logiki, aby wysłać wiadomość e-mail). Odczyty domyślne (`normal`) zostaną wyświetlone w raporcie analizy biznesowej konfigurowanym w kolejnym kroku.

Jeśli wszystko zostanie prawidłowo skonfigurowane, w tym momencie powinny być widoczne następujące wyniki:

1. Możesz zacząć otrzymywać wiadomości e-mail dotyczące komunikatów o krytycznym znaczeniu. 

   ![Zrzut ekranu przedstawiający wynikowe wiadomości e-mail.](./media/tutorial-routing/results-in-email.png)

   Oznacza to, że:

   * Routing do kolejki usługi Service Bus działa poprawnie.
   * Pobieranie komunikatu z kolejki usługi Service Bus do aplikacji logiki działa poprawnie.
   * Łącznik aplikacji logiki do programu Outlook działa poprawnie. 

2. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Grupy zasobów** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. Wybierz konto magazynu, kliknij pozycję **Obiekty blob**, a następnie wybierz kontener. W tym samouczku jest używany kontener **contosoresults**. Powinien pojawić się folder, w którym będzie można przechodzić do szczegółów katalogów do momentu wyświetlenia co najmniej jednego pliku. Otwórz jeden z tych plików; zawierają one wpisy kierowane do konta magazynu. 

   ![Zrzut ekranu przedstawiający pliki wyników w magazynie.](./media/tutorial-routing/results-in-storage.png)

Oznacza to, że:

   * Routing do konta magazynu działa poprawnie.

Teraz — przy nadal działającej aplikacji — skonfiguruj wizualizację usługi Power BI, aby widzieć komunikaty przechodzące przez trasę domyślną. 

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurowanie wizualizacji usługi Power BI

1. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/).

2. Przejdź do obszaru **Obszary robocze** i wybierz obszar roboczy ustawiony podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana nazwa **Mój obszar roboczy**. 

3. Kliknij pozycję **Zestawy danych**.

   Powinien pojawić się wymieniony na liście zestaw danych, który został wybrany podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używany zestaw danych **contosodataset**. (Wyświetlenie zestawu danych po raz pierwszy może potrwać 5–10 minut).

4. W obszarze **AKCJE** kliknij pierwszą ikonę, aby utworzyć raport.

   ![Zrzut ekranu przedstawiający obszar roboczy usługi Power BI z wyróżnioną pozycją Akcje i ikoną raportu.](./media/tutorial-routing/power-bi-actions.png)

5. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   * Na stronie tworzenia raportu dodaj wykres liniowy, klikając ikonę wykresu liniowego.

   ![Zrzut ekranu przedstawiający wizualizacje i pola.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   * W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana tabela **contosotable**.

   * Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   * Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

   Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

6. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby skonfigurować drugi wykres, wykonaj te same kroki, co powyżej, i umieść element **EventEnqueuedUtcTime** na osi X, a element **humidity** (Wilgotność) na osi Y.

   ![Zrzut ekranu przedstawiający końcowy raport usługi Power BI z dwoma wykresami.](./media/tutorial-routing/power-bi-report.png)

7. Kliknij przycisk **Zapisz**, aby zapisać raport.

Powinny być widoczne dane na obydwu wykresach. Oznacza to, że:

   * Routing do domyślnego punktu końcowego działa poprawnie.
   * Zadanie usługi Azure Stream Analytics jest poprawnie przesyłane strumieniowo.
   * Wizualizacja usługi Power BI została prawidłowo skonfigurowana.

Wykresy można odświeżać w celu wyświetlenia najnowszych danych, klikając przycisk Odśwież w górnej części okna usługi Power BI. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli chcesz usunąć wszystkie utworzone przez siebie zasoby, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W naszym przypadku nastąpi usunięcie centrum IoT Hub, przestrzeni nazw i kolejki usługi Service Bus, aplikacji logiki, konta magazynu i samej grupy zasobów. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Oczyszczanie zasobów w wizualizacji usługi Power BI

Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/). Przejdź do swojego obszaru roboczego. W tym samouczku jest używana nazwa **Mój obszar roboczy**. Aby usunąć wizualizację usługi Power BI, przejdź do obszaru zestawów danych i kliknij ikonę kosza w celu usunięcia zestawu danych. W tym samouczku jest używany zestaw danych **contosodataset**. Podczas usuwania zestawu danych usuwany jest również raport.

### <a name="clean-up-resources-using-azure-cli"></a>Oczyszczanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Oczyszczanie zasobów przy użyciu programu PowerShell

Aby usunąć grupę zasobów, użyj polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). Element $resourceGroup został ustawiony na wartość **ContosoIoTRG1** na początku tego samouczka.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia routingu komunikatów do rozsyłania komunikatów usługi IoT Hub do różnych miejsc docelowych. W tym celu wykonano następujące zadania:  

> [!div class="checklist"]
> * Używanie interfejsu wiersza polecenia platformy Azure lub programu PowerShell do konfigurowania zasobów podstawowych — centrum IoT Hub, konta magazynu, kolejki usługi Service Bus i urządzenia symulowanego.
> * Konfigurowanie punktów końcowych i tras w centrum IoT Hub dla konta magazynu i kolejki usługi Service Bus.
> * Tworzenie aplikacji logiki, która jest wyzwalana i wysyła wiadomość e-mail po dodaniu komunikatu do kolejki usługi Service Bus.
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum przy użyciu różnych opcji routingu.
> * Tworzenie wizualizacji usługi Power BI na potrzeby danych wysyłanych do domyślnego punktu końcowego.
> * Przeglądanie wyników...
> * ...w kolejce usługi Service Bus i wiadomościach e-mail.
> * ...na koncie magazynu.
> * ...w wizualizacji usługi Power BI.

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Konfigurowanie i używanie metryk i diagnostyki w usłudze IoT Hub](tutorial-use-metrics-and-diags.md)
