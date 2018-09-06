---
title: Konfigurowanie routingu komunikatów przy użyciu usługi Azure IoT Hub (.NET) | Microsoft Docs
description: Konfigurowanie routingu komunikatów przy użyciu usługi Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: a52ab4ff65312088e65d56006b6f99a7470b88f6
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287254"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Samouczek: konfigurowanie routingu wiadomości przy użyciu usługi IoT Hub

Routing komunikatów umożliwia wysyłanie danych telemetrycznych z urządzeń IoT do wbudowanych punktów końcowych zgodnych z centrum Event Hubs lub do niestandardowych punktów końcowych, takich jak usługa Blob Storage, kolejka usługi Service Bus, temat usługi Service Bus i usługa Event Hubs. Podczas konfigurowania routingu komunikatów można utworzyć reguły rozsyłania, aby dostosować trasę zgodną z wybraną regułą. Po zakończeniu konfiguracji dane przychodzące są automatycznie rozsyłane do punktów końcowych przez usługę IoT Hub. 

Z tego samouczka dowiesz się, jak skonfigurować reguły rozsyłania i korzystać z nich za pomocą usługi IoT Hub. Będziesz przekierowywać komunikaty z urządzenia IoT do jednej z wielu usług, w tym z magazynu obiektów blob i kolejki usługi Service Bus. Komunikaty do kolejki usługi Service Bus będą pobierane przez aplikację logiki i wysyłane pocztą e-mail. Komunikaty, które nie mają specjalnie skonfigurowanego routingu, są wysyłane do domyślnego punktu końcowego i wyświetlane w wizualizacji usługi Power BI.

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

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Zainstaluj program [Visual Studio dla systemu Windows](https://www.visualstudio.com/). 

- Konto usługi Power BI do analizowania danych analitycznych strumienia domyślnego punktu końcowego. ([Wypróbuj bezpłatnie usługę Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)).

- Konto usługi Office 365 do wysyłania powiadomień e-mail. 

Do wykonania kroków konfiguracji opisanych w tym samouczku potrzebujesz interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. 

Do korzystania z interfejsu wiersza polecenia platformy Azure, mimo że można go zainstalować lokalnie, zalecamy użycie usługi Azure Cloud Shell. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do uruchamiania skryptów interfejsu wiersza polecenia platformy Azure. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta, dlatego nie trzeba instalować ich lokalnie. 

Aby używać programu PowerShell, zainstaluj go lokalnie, korzystając z poniższych instrukcji. 

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Usługę Cloud Shell można otworzyć na kilka sposobów:

|  |   |
|-----------------------------------------------|---|
| Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. | ![Usługa Cloud Shell w tym artykule](./media/tutorial-routing/cli-try-it.png) |
| Otwórz usługę Cloud Shell w swojej przeglądarce. | [![https://shell.azure.com/bash](./media/tutorial-routing/launchcloudshell.png)](https://shell.azure.com) |
| Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu witryny [Azure Portal](https://portal.azure.com). |    ![Usługa Cloud Shell w portalu](./media/tutorial-routing/cloud-shell-menu.png) |
|  |  |

### <a name="using-azure-cli-locally"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure w środowisku lokalnym

Jeśli wolisz używać lokalnego interfejsu wiersza polecenia zamiast usługi Cloud Shell, musisz mieć moduł interfejsu wiersza polecenia Azure w wersji 2.0.30.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

### <a name="using-powershell-locally"></a>Używanie programu PowerShell lokalnie

Dla tego samouczka jest wymagany moduł Azure PowerShell w wersji 5.7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="set-up-resources"></a>Konfigurowanie zasobów

W tym samouczku potrzebujesz centrum IoT Hub, konta magazynu i kolejki usługi Service Bus. Wszystkie te zasoby można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Po zakończeniu możesz usunąć wszystko w jednym kroku, usuwając grupę zasobów.

W poniższych sekcjach opisano sposób wykonywania tych wymaganych czynności. Postępuj zgodnie z instrukcjami dotyczącymi interfejsu wiersza polecenia *lub* programu PowerShell.

1. Utwórz [grupę zasobów](../azure-resource-manager/resource-group-overview.md). 

    <!-- When they add the Basic tier, change this to use Basic instead of Standard. -->

1. Utwórz centrum IoT Hub w warstwie S1. Dodaj grupę użytkowników do centrum IoT Hub. Grupa użytkowników jest używana przez usługę Azure Stream Analytics podczas pobierania danych.

1. Utwórz standardowe konto magazynu w wersji 1 przy użyciu replikacji Standard_LRS.

1. Utwórz przestrzeń nazw i kolejkę usługi Service Bus. 

1. Utwórz tożsamość urządzenia symulowanego, które wysyła wiadomości do centrum. Zapisz klucz na potrzeby fazy testowania.

### <a name="azure-cli-instructions"></a>Instrukcje dotyczące interfejsu wiersza polecenia platformy Azure

Najprostszym sposobem użycia tego skryptu jest skopiowanie i wklejenie go w usłudze Cloud Shell. Przy założeniu, że użytkownik jest już zalogowany, usługa będzie kolejno uruchamiać po jednym wierszu skryptu. 

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

### <a name="powershell-instructions"></a>Instrukcje dotyczące programu PowerShell

Najprostszym sposobem użycia tego skryptu jest otwarcie [środowiska ISE programu PowerShell](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise?view=powershell-6), skopiowanie skryptu do schowka, a następnie wklejenie całego skryptu w oknie skryptu. Następnie można zmienić wartości nazw zasobów (w razie potrzeby) i uruchomić cały skrypt. 

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

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
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

W kolejnym kroku utwórz tożsamość urządzenia i zapisz jego klucz do późniejszego użycia. Ta tożsamość urządzenia jest używana przez aplikację symulacji do wysyłania komunikatów do centrum IoT Hub. Ta możliwość nie jest dostępna w programie PowerShell, ale można utworzyć urządzenie w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się do konta platformy Azure.

1. Kliknij pozycję **Grupy zasobów** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

1. Na liście zasobów kliknij swoje centrum IoT Hub. W tym samouczku jest używane centrum **ContosoTestHub**. Wybierz pozycję **Urządzenia IoT** w okienku centrum.

1. Kliknij pozycję **+ Dodaj**. W okienku Dodawanie urządzenia wypełnij pole identyfikatora urządzenia. W tym samouczku jest używany identyfikator **Contoso-Test-Device**. Pozostaw klucze puste i zaznacz pole **Automatycznie generuj klucze**. Upewnij się, że pozycja **Połącz urządzenie z centrum IoT Hub** została włączona. Kliknij pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający ekran dodawania urządzenia.](./media/tutorial-routing/add-device.png)

1. Po utworzeniu urządzenia kliknij je, aby wyświetlić wygenerowane klucze. Kliknij ikonę kopiowania na kluczu podstawowym i zapisz go w dowolnym miejscu, takim jak Notatnik, do użycia w fazie testowania opisanej w tym samouczku.

   ![Zrzut ekranu przedstawiający szczegóły urządzenia, w tym klucze.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

Planujesz rozsyłać komunikaty do różnych zasobów na podstawie właściwości dołączonych do komunikatu za pośrednictwem urządzenia symulowanego. Komunikaty bez tras niestandardowych są wysyłane do domyślnego punktu końcowego (wiadomości/zdarzenia). 

|wartość |Wynik|
|------|------|
|level=„storage” |Zapis w usłudze Azure Storage.|
|level=„critical” |Zapis w kolejce usługi Service Bus. Aplikacja logiki pobiera komunikat z kolejki i używa usługi Office 365 do wysłania go pocztą e-mail.|
|default |Wyświetlanie tych danych przy użyciu usługi Power BI.|

### <a name="routing-to-a-storage-account"></a>Routing do konta magazynu 

Teraz należy skonfigurować routing dla konta magazynu. Zdefiniuj punkt końcowy, a następnie skonfiguruj trasę dla tego punktu końcowego. Komunikaty, w których właściwość **level** została ustawiona na **storage**, są automatycznie zapisywane na koncie magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. Kliknij centrum IoT Hub na liście zasobów. W tym samouczku jest używane centrum **ContosoTestHub**. Kliknij przycisk **Punkty końcowe**. W okienku **Punkty końcowe** kliknij pozycję **+Dodaj**. Wprowadź następujące informacje:

   **Nazwa**: wprowadź nazwę punktu końcowego. W tym samouczku jest używana nazwa **StorageContainer**.
   
   **Typ punktu końcowego**: z listy rozwijanej wybierz pozycję **Kontener usługi Azure Storage**.

   Kliknij pozycję **Wybierz kontener**, aby wyświetlić listę kont magazynu. Wybierz swoje konto magazynu. W tym samouczku jest używana kolejka **contosostorage**. Następnie wybierz kontener. W tym samouczku jest używany kontener **contosoresults**. Kliknij pozycję **Wybierz**, aby wrócić do okienka **Dodawanie punktu końcowego**. 
   
   ![Zrzut ekranu przedstawiający dodawanie punktu końcowego.](./media/tutorial-routing/add-endpoint-storage-account.png)
   
   Kliknij przycisk **OK**, aby zakończyć dodawanie punktu końcowego.
   
1. Kliknij pozycję **Trasy** w centrum IoT Hub. Planujesz utworzyć regułę rozsyłania kierującą komunikaty do kontenera magazynu, który został właśnie dodany jako punkt końcowy. Kliknij pozycję **+Dodaj** w górnej części okienka Trasy. Wypełnij pola na ekranie. 

   **Nazwa**: wprowadź nazwę reguły rozsyłania. W tym samouczku jest używana reguła **StorageRule**.

   **Źródło danych**: z listy rozwijanej wybierz pozycję **Komunikaty urządzenia**.

   **Punkt końcowy**: wybierz właśnie skonfigurowany punkt końcowy. W tym samouczku jest używana nazwa **StorageContainer**. 
   
   **Ciąg zapytania**: wprowadź `level="storage"` jako ciąg zapytania. 

   ![Zrzut ekranu przedstawiający tworzenie reguły rozsyłania dla konta magazynu.](./media/tutorial-routing/create-a-new-routing-rule-storage.png)
   
   Kliknij pozycję **Zapisz**. Po zakończeniu nastąpi powrót do okienka Trasy, w którym można zobaczyć nową regułę rozsyłania dla magazynu. Zamknij okienko Trasy, co spowoduje powrót na stronę grupy zasobów.

### <a name="routing-to-a-service-bus-queue"></a>Routing do kolejki usługi Service Bus 

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Zdefiniuj punkt końcowy, a następnie skonfiguruj trasę dla tego punktu końcowego. Komunikaty, w których właściwość **level** została ustawiona na **critical**, są zapisywane do kolejki usługi Service Bus wyzwalającej aplikację logiki, która następnie wysyła wiadomość e-mail z informacjami. 

1. Na stronie grupy zasobów kliknij centrum IoT Hub, a następnie kliknij pozycję **punkty końcowe**. W okienku **Punkty końcowe** kliknij pozycję **+Dodaj**. Wprowadź następujące informacje.

   **Nazwa**: wprowadź nazwę punktu końcowego. W tym samouczku jest używana nazwa **CriticalQueue**. 

   **Typ punktu końcowego**: z listy rozwijanej wybierz pozycję **Kolejka usługi Service Bus**.

   **Przestrzeń nazw usługi Service Bus**: z listy rozwijanej wybierz przestrzeń nazw usługi Service Bus na potrzeby tego samouczka. W tym samouczku jest używana nazwa **ContosoSBNamespace**.

   **Kolejka usługi Service Bus**: z listy rozwijanej wybierz kolejkę usługi Service Bus. W tym samouczku jest używana kolejka **contososbqueue**.

   ![Zrzut ekranu przedstawiający dodawanie punktu końcowego dla kolejki usługi Service Bus.](./media/tutorial-routing/add-endpoint-sb-queue.png)

   Kliknij przycisk **OK**, aby zapisać punkt końcowy. Po zakończeniu zamknij okienko Punkty końcowe. 
    
1. Kliknij pozycję **Trasy** w centrum IoT Hub. Planujesz utworzyć regułę rozsyłania kierującą komunikaty do kolejki usługi Service Bus, która została właśnie dodana jako punkt końcowy. Kliknij pozycję **+Dodaj** w górnej części okienka Trasy. Wypełnij pola na ekranie. 

   **Nazwa**: wprowadź nazwę reguły rozsyłania. W tym samouczku jest używana reguła **SBQueueRule**. 

   **Źródło danych**: z listy rozwijanej wybierz pozycję **Komunikaty urządzenia**.

   **Punkt końcowy**: wybierz właśnie skonfigurowany punkt końcowy **CriticalQueue**.

   **Ciąg zapytania**: wprowadź `level="critical"` jako ciąg zapytania. 

   ![Zrzut ekranu przedstawiający tworzenie reguły rozsyłania dla kolejki usługi Service Bus.](./media/tutorial-routing/create-a-new-routing-rule-sbqueue.png)
   
   Kliknij pozycję **Zapisz**. Po powrocie do okienka Trasy zobaczysz obydwie nowe reguły rozsyłania wyświetlone w poniższy sposób.

   ![Zrzut ekranu przedstawiający właśnie skonfigurowane trasy.](./media/tutorial-routing/show-routing-rules-for-hub.png)

   Zamknij okienko Trasy, co spowoduje powrót na stronę grupy zasobów.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki  

Kolejka usługi Service Bus jest używana do odbierania komunikatów oznaczonych jako krytyczne. Skonfiguruj aplikację logiki, aby monitorować kolejkę usługi Service Bus i wysyłać wiadomość e-mail po dodaniu komunikatu do kolejki. 

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **+ Utwórz zasób**. W polu wyszukiwania wpisz ciąg **aplikacja logiki** i naciśnij klawisz Enter. W wyświetlonych wynikach wyszukiwania wybierz aplikację logiki, a następnie kliknij pozycję **Utwórz**, aby przejść do okienka **Tworzenie aplikacji logiki**. Wypełnij pola. 

   **Nazwa**: to pole zawiera nazwę aplikacji logiki. W tym samouczku jest używana nazwa **ContosoLogicApp**. 

   **Subskrypcja**: wybierz subskrypcję platformy Azure.

   **Grupa zasobów**: kliknij pozycję **Użyj istniejącej** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. 

   **Lokalizacja**: użyj swojej lokalizacji. W tym samouczku jest używany region **Zachodnie stany USA**. 

   **Log Analytics**: ten przełącznik powinien być wyłączony. 

   ![Zrzut ekranu przedstawiający ekran Tworzenie aplikacji logiki.](./media/tutorial-routing/create-logic-app.png)

   Kliknij pozycję **Utwórz**.

1. Teraz przejdź do aplikacji logiki. Najprostszym sposobem przejścia do aplikacji logiki jest kliknięcie pozycji **Grupy zasobów**, wybranie grupy zasobów (w tym samouczku jest używana grupa **ContosoResources**), a następnie wybranie aplikacji logiki z listy zasobów. Zostanie wyświetlona strona Projektanta aplikacji usługi Logic Apps (w celu wyświetlenia całej strony może być konieczne przewinięcie w prawo). Na stronie Projektanta aplikacji usługi Logic Apps przewiń w dół do kafelka z opisem **Pusta aplikacja logiki +** i kliknij go. 

1. Zostanie wyświetlona lista łączników. Wybierz pozycję **Service Bus**. 

   ![Zrzut ekranu przedstawiający listę łączników.](./media/tutorial-routing/logic-app-connectors.png)

1. Zostanie wyświetlona lista wyzwalaczy. Wybierz pozycję **Service Bus — po odebraniu komunikatu w kolejce (autouzupełnianie)**. 

   ![Zrzut ekranu przedstawiający listę wyzwalaczy usługi Service Bus.](./media/tutorial-routing/logic-app-triggers.png)

1. Na następnym ekranie wprowadź nazwę połączenia. W tym samouczku jest używana nazwa **ContosoConnection**. 

   ![Zrzut ekranu przedstawiający konfigurowanie połączenia na potrzeby kolejki usługi Service Bus.](./media/tutorial-routing/logic-app-define-connection.png)

   Kliknij przestrzeń nazw usługi Service Bus. W tym samouczku jest używana nazwa **ContosoSBNamespace**. Po wybraniu przestrzeni nazw portal wykonuje zapytanie w przestrzeni nazw usługi Service Bus w celu pobrania kluczy. Wybierz pozycję **RootManageSharedAccessKey** i kliknij pozycję **Utwórz**. 
   
   ![Zrzut ekranu przedstawiający zakończenie konfigurowania połączenia.](./media/tutorial-routing/logic-app-finish-connection.png)

1. Na następnym ekranie wybierz nazwę kolejki (w tym samouczku jest używana kolejka **contososbqueue**) z listy rozwijanej. W pozostałych polach można użyć wartości domyślnych. 

   ![Zrzut ekranu przedstawiający opcje kolejki.](./media/tutorial-routing/logic-app-queue-options.png)

1. Teraz skonfiguruj akcję wysyłania wiadomości e-mail po odebraniu komunikatu w kolejce. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **+ Nowy krok**, aby dodać krok, a następnie kliknij pozycję **Dodaj akcję**. W okienku **Wybieranie akcji** znajdź i kliknij pozycję **Office 365 Outlook**. Na ekranie wyzwalaczy wybierz pozycję **Office 365 Outlook — wysyłanie wiadomości e-mail**.  

   ![Zrzut ekranu przedstawiający opcje usługi Office 365.](./media/tutorial-routing/logic-app-select-outlook.png)

1. Następnie zaloguj się do konta usługi Office 365, aby skonfigurować połączenie. Podaj adresy e-mail adresatów wiadomości e-mail. Określ temat, a następnie wpisz treść wiadomości, którą ma zobaczyć adresat. Na potrzeby testowania wprowadź własny adres e-mail jako adresata.

   Kliknij pozycję **Dodaj zawartość dynamiczną**, aby wyświetlić zawartość komunikatu, którą można dołączyć. Wybierz pozycję **Zawartość** — będzie ona obejmować komunikat z wiadomości e-mail. 

   ![Zrzut ekranu przedstawiający opcje poczty e-mail dla aplikacji logiki.](./media/tutorial-routing/logic-app-send-email.png)

1. Kliknij pozycję **Zapisz**. Następnie zamknij Projektanta aplikacji logiki.

## <a name="set-up-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics

Aby wyświetlić dane w wizualizacji usługi Power BI, najpierw skonfiguruj zadanie usługi Stream Analytics w celu pobrania danych. Pamiętaj, że tylko komunikaty, w których właściwość **level** ma wartość **normal**, są wysyłane do domyślnego punktu końcowego i zostaną pobrane przez zadanie usługi Stream Analytics na potrzeby wizualizacji usługi Power BI.

### <a name="create-the-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.

1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej. W tym samouczku jest używana nazwa **contosoJob**.

   **Grupa zasobów**: użyj grupy zasobów stosowanej już w centrum IoT Hub. W tym samouczku jest używana grupa **ContosoResources**. 

   **Lokalizacja**: użyj lokalizacji stosowanej już w skrypcie konfiguracji. W tym samouczku jest używany region **Zachodnie stany USA**. 

   ![Zrzut ekranu pokazujący sposób tworzenia zadania usługi Log Analytics.](./media/tutorial-routing/stream-analytics-create-job.png)

1. Kliknij pozycję **Utwórz**, aby utworzyć zadanie. Aby powrócić do zadania, kliknij pozycję **Grupy zasobów**. W tym samouczku jest używana grupa **ContosoResources**. Wybierz grupę zasobów, a następnie kliknij zadanie usługi Stream Analytics na liście zasobów. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.

1. W okienku **Dane wejściowe** kliknij pozycję **Dodaj wejście strumienia** i wybierz pozycję centrum IoT Hub. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias wejściowy**: w tym samouczku jest używany alias **contosoinputs**.

   **Subskrypcja**: wybierz swoją subskrypcję.

   **Centrum IoT Hub**: wybierz centrum usługi IoT Hub. W tym samouczku jest używane centrum **ContosoTestHub**.

   **Punkt końcowy**: wybierz pozycję **Obsługa komunikatów**. (W przypadku wybrania pozycji Monitorowanie operacji otrzymasz dane telemetryczne dotyczące centrum IoT Hub, a nie wysyłane dane). 

   **Nazwa zasady dostępu współdzielonego**: wybierz pozycję **iothubowner**. Portal wypełni klucz wstępny zasad dostępu współdzielonego za Ciebie.

   **Grupy użytkowników**: wybierz wcześniej utworzoną grupę użytkowników. W tym samouczku jest używana grupa **contosoconsumers**.
   
   W pozostałych polach zaakceptuj wartości domyślne. 

   ![Zrzut ekranu przedstawiający sposób konfigurowania danych wejściowych zadania usługi Stream Analytics.](./media/tutorial-routing/stream-analytics-job-inputs.png)

1. Kliknij pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.

1. W okienku **Dane wyjściowe** kliknij pozycję **Dodaj**, a następnie wybierz pozycję **Power BI**. Na wyświetlonym ekranie wypełnij następujące pola:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych. W tym samouczku jest używany alias **contosooutputs**. 

   **Nazwa zestawu danych**: nazwa zestawu danych do użycia w usłudze Power BI. W tym samouczku jest używany zestaw danych **contosodataset**. 

   **Nazwa tabeli**: nazwa tabeli do użycia w usłudze Power BI. W tym samouczku jest używana tabela **contosotable**.

   Zaakceptuj wartości domyślne w pozostałych polach.

1. Kliknij pozycję **Autoryzuj** i zaloguj się do konta usługi Power BI.

   ![Zrzut ekranu przedstawiający sposób konfigurowania danych wyjściowych zadania usługi Stream Analytics.](./media/tutorial-routing/stream-analytics-job-outputs.png)

1. Kliknij pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.

1. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania. W tym samouczku jest używany alias **contosoinputs**.

1. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania. W tym samouczku jest używany alias **contosooutputs**.

   ![Zrzut ekranu przedstawiający sposób konfigurowania zapytania zadania usługi Stream Analytics.](./media/tutorial-routing/stream-analytics-job-query.png)

1. Kliknij pozycję **Zapisz**.

1. Zamknij okienko Zapytanie. Nastąpi powrót do widoku zasobów w grupie zasobów. Kliknij zadanie usługi Stream Analytics. W tym samouczku nosi ono nazwę **contosoJob**.

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

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Grupy zasobów** i wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**. Wybierz konto magazynu, kliknij pozycję **Obiekty blob**, a następnie wybierz kontener. W tym samouczku jest używany kontener **contosoresults**. Powinien pojawić się folder, w którym będzie można przechodzić do szczegółów katalogów do momentu wyświetlenia co najmniej jednego pliku. Otwórz jeden z tych plików; zawierają one wpisy kierowane do konta magazynu. 

   ![Zrzut ekranu przedstawiający pliki wyników w magazynie.](./media/tutorial-routing/results-in-storage.png)

Oznacza to, że:

   * Routing do konta magazynu działa poprawnie.

Teraz — przy nadal działającej aplikacji — skonfiguruj wizualizację usługi Power BI, aby widzieć komunikaty przechodzące przez trasę domyślną. 

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurowanie wizualizacji usługi Power BI

1. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/).

1. Przejdź do obszaru **Obszary robocze** i wybierz obszar roboczy ustawiony podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana nazwa **Mój obszar roboczy**. 

1. Kliknij pozycję **Zestawy danych**.

   Powinien pojawić się wymieniony na liście zestaw danych, który został wybrany podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używany zestaw danych **contosodataset**. (Wyświetlenie zestawu danych po raz pierwszy może potrwać 5–10 minut).

1. W obszarze **AKCJE** kliknij pierwszą ikonę, aby utworzyć raport.

   ![Zrzut ekranu przedstawiający obszar roboczy usługi Power BI z wyróżnioną pozycją Akcje i ikoną raportu.](./media/tutorial-routing/power-bi-actions.png)

1. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   a. Na stronie tworzenia raportu dodaj wykres liniowy, klikając ikonę wykresu liniowego.

   ![Zrzut ekranu przedstawiający wizualizacje i pola.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   b. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics. W tym samouczku jest używana tabela **contosotable**.

   d. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   d. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

   Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

1. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby skonfigurować drugi wykres, wykonaj te same kroki, co powyżej, i umieść element **EventEnqueuedUtcTime** na osi X, a element **humidity** (Wilgotność) na osi Y.

   ![Zrzut ekranu przedstawiający końcowy raport usługi Power BI z dwoma wykresami.](./media/tutorial-routing/power-bi-report.png)

1. Kliknij przycisk **Zapisz**, aby zapisać raport.

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

Aby usunąć grupę zasobów, użyj polecenia [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup). Element $resourceGroup został ustawiony na wartość **ContosoIoTRG1** na początku tego samouczka.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>Następne kroki

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
[Konfigurowanie urządzeń z poziomu usługi zaplecza](tutorial-device-twins.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->
