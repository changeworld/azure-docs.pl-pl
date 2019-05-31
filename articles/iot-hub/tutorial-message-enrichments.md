---
title: Samouczek — używanie wzbogacenia komunikatów usługi Azure IoT Hub
description: Samouczek przedstawiający sposób użycia wzbogacenia komunikatu dla komunikatów usługi Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259077"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Samouczek: Za pomocą usługi Azure IoT Hub komunikatów wzbogacenia (wersja zapoznawcza)

*Komunikat wzbogaceń* to zdolność usług IoT Hub *sygnatury* wiadomości z dodatkowymi informacjami przed wysłaniem wiadomości do wyznaczonej punktu końcowego. Jednym z powodów używania wzbogacenia wiadomości jest zawierają dane, do którego można uproszczenie przetwarzania transmisji dla klientów. Na przykład wzbogaca komunikaty telemetryczne urządzenia przy użyciu tagu bliźniaczej reprezentacji urządzenia można zmniejszyć obciążenie klientom na podejmowanie bliźniaczej reprezentacji urządzenia, które wywołań interfejsu API, aby uzyskać te informacje. Aby uzyskać więcej informacji, zobacz [Przegląd wzbogacenia komunikat](iot-hub-message-enrichments-overview.md).

W tym samouczku Użyj wiersza polecenia platformy Azure można skonfigurować zasoby, w tym dwa punkty końcowe, które wskazują na dwa kontenery innego magazynu — **wzbogacony** i **oryginalnego**. Możesz użyć [witryny Azure portal](https://portal.azure.com) skonfigurować komunikat wzbogacenia mają być stosowane tylko dla komunikatów wysyłanych do punktu końcowego z **wzbogacony** kontenera magazynu. Możesz wysyłać komunikaty do Centrum IoT, które są kierowane do obu kontenery magazynu. Tylko komunikaty wysyłane do punktu końcowego dla **wzbogacony** kontenera magazynu będzie wzbogacony.

Poniżej przedstawiono zadania, należy wykonać do ukończenia tego samouczka:

**Za pomocą wzbogacenia komunikatów usługi IoT Hub**
> [!div class="checklist"]
> * Przy użyciu wiersza polecenia platformy Azure, Utwórz zasobów — Centrum IoT hub, konto magazynu z dwoma punktami końcowymi i konfiguracji routingu.
> * Użyj witryny Azure portal, aby skonfigurować wzbogacenia wiadomości.
> * Uruchom aplikację, która symuluje urządzenie wysyłanie komunikatów do Centrum IoT.
> * Wyświetlanie wyników i sprawdź, czy wzbogacenia komunikat działają zgodnie z oczekiwaniami.

## <a name="prerequisites"></a>Wymagania wstępne

* Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Instalacja programu [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Pobierz przykładowy kod

Pobierz [symulacji urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i Rozpakuj go. To repozytorium zawiera kilka aplikacji, w tym alert, które będą używane do wysyłania komunikatów do Centrum IoT hub.

Ten plik do pobrania zawiera także skrypt tworzenia zasobów, używane do testowania wzbogacenia wiadomości. The script is in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Teraz możesz przyjrzeć się skrypt i jej używać. Skrypt można również kopiować, bezpośrednio z tego artykułu.

Gdy wszystko jest gotowe do rozpoczęcia testowania, użyjesz aplikacji symulacji urządzenia z ten plik do pobrania do wysyłania komunikatów do Centrum IoT.

## <a name="set-up-and-configure-resources"></a>Instalowanie i konfigurowanie zasobów

Oprócz tworzenia niezbędne zasoby, wiersza polecenia platformy Azure konfiguruje również dwa tras do punktów końcowych, które są kontenery oddzielne magazynu. Aby uzyskać więcej informacji na temat konfigurowania routingu, zobacz [routingu samouczek](tutorial-routing.md). Po skonfigurowaniu zasoby, należy użyć [witryny Azure portal](https://portal.azure.com) do skonfigurowania wzbogacenia wiadomości dla każdego punktu końcowego, a następnie przejdź do kroku testowania.

> [!NOTE]
> Wszystkie komunikaty są kierowane do obu punktów końcowych, ale tylko wiadomości, przechodząc do punktu końcowego za pomocą wzbogacenia skonfigurowanego komunikat będzie wzbogacony.
>

Możesz użyć poniższego skryptu lub Otwórz skrypt w folderze /resources pobrany repozytorium. Poniżej przedstawiono kroki, które będzie wykonywać skrypt:

* Tworzenie centrum IoT Hub.
* Utwórz konto magazynu.
* Utwórz dwa kontenery na koncie magazynu — jeden dla wzbogaconego komunikatów i jeden dla wiadomości, które nie są wzbogacane.
* Konfigurowanie routingu dla dwóch różnych kont magazynu.
    * Utworzenie punktu końcowego dla każdego kontenera konta magazynu.
    * Utwórz trasę do każdego z punktów końcowych kontenera konta magazynu.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowa, takie jak nazwa usługi IoT Hub i nazwy konta magazynu. Aby upewnić się, uruchamiając skrypt łatwiejsze, te nazwy zasobu są dołączane za pomocą losowych wartości alfanumeryczne o nazwie *randomValue*. RandomValue jest generowane raz w górnej części skryptu i dołączany do nazwy zasobów, zgodnie z potrzebami w całym skrypcie. Jeśli nie chcesz, aby była losowych, można ustawić na ciąg pusty lub określoną wartość.

Jeśli jeszcze tego nie zrobiono, otwórz [okno usługi Cloud Shell dla programu Bash.](https://shell.azure.com). Otwórz skrypt w rozpakowany repozytorium, użyj klawiszy Ctrl-A, aby zaznaczyć wszystkie, a następnie Ctrl-C, aby go skopiować. Alternatywnie możesz Skopiuj poniższy skrypt interfejsu wiersza polecenia lub otworzyć go bezpośrednio w usłudze cloud shell. Wklej skrypt w oknie usługi Azure cloud shell, kliknij prawym przyciskiem myszy w wierszu polecenia i wybierając **Wklej**. Skrypt jest uruchamiany jednej instrukcji w danym momencie. Po zatrzymaniu skrypt uruchamiania wybierz **Enter** się upewnić, że uruchomieniu ostatnie polecenie. Poniższy blok kodu pokazuje skrypt, który jest używany z komentarze objaśniające, jakie jest zastosowanie.

Poniżej przedstawiono zasoby utworzone przez skrypt. **Wzbogacone** oznacza, że zasób dla komunikatów za pomocą wzbogacenia. **Oryginalny** oznacza, że komunikaty, które nie są wzbogacane zasobów.

| Name (Nazwa) | Wartość |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| Nazwa kontenera | Oryginał  |
| Nazwa kontenera | wzbogacony  |
| Nazwa urządzenia IoT | Contoso-Test-Device |
| Nazwa centrum IoT Hub | ContosoTestHubMsgEn |
| Nazwa konta magazynu | contosostorage |
| punkt końcowy nazwa 1 | ContosoStorageEndpointOriginal |
| punkt końcowy Nazwa 2 | ContosoStorageEndpointEnriched|
| trasy Nazwa 1 | ContosoStorageRouteOriginal |
| trasy Nazwa 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

W tym momencie zasoby rosną wszystko jest gotowe i routingu jest skonfigurowana. Możesz wyświetlać konfigurację routingu komunikatów w portalu i konfigurowanie wzbogacenia komunikatu dla komunikatów do **wzbogacony** kontenera magazynu.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Wyświetlanie, routingu i konfigurowanie wzbogacenia wiadomości

1. Przejdź do Centrum IoT, wybierając **grup zasobów**, następnie wybierz grupę zasobów, konfigurowanie na potrzeby tego samouczka (**ContosoResources_MsgEn**). Znajdź na liście usługi IoT Hub i zaznacz go. Wybierz *Routing komunikatów** dla Centrum Iot Hub.

   ![Wybierz pozycję routing wiadomości](./media/tutorial-message-enrichments/select-iot-hub.png)

   Komunikat o routingu okienko zawiera trzy karty — **trasy**, **niestandardowe punkty końcowe**, i **wzbogacania komunikatów**. Można przeglądać w pierwszych dwóch kart, aby wyświetlić konfigurację skonfigurować przy użyciu skryptu. Trzeci karta umożliwia dodawanie wzbogacenia wiadomości. Spróbujmy wzbogacania komunikatów, przechodząc do endpoint kontenera magazynu o nazwie **wzbogacony**. Wprowadź nazwę i wartość, a następnie wybierz punkt końcowy **ContosoStorageEndpointEnriched** z listy rozwijanej. Poniżej przedstawiono przykład konfigurowania wzbogacania, który dodaje nazwę Centrum IoT Hub do wiadomości:

   ![Dodaj pierwszy wzbogacanie](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Dodaj te wartości do listy do ContosoStorageEndpointEnriched endpoint.

   | Name (Nazwa) | Wartość | Punkt końcowy (listy rozwijanej) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Lokalizacja urządzenia | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |CustomerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Jeśli urządzenie nie ma bliźniaczego elementu, wartości, które można umieścić w tym miejscu zostanie oznaczony jako ciąg znaków na potrzeby wartości wzbogacenia wiadomości. Aby wyświetlić urządzeń, bliźniacza reprezentacja informacji, przejdź do Centrum w portalu, następnie wybierz pozycję **urządzeń IoT**, wybierz urządzenie, a następnie wybierz **bliźniaczej reprezentacji urządzenia** w górnej części strony.
   >
   > Można edytować informacje o bliźniaczych reprezentacji dodawania tagów (na przykład lokalizacja) i ustaw ją na konkretną wartość, jeśli chcesz. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](iot-hub-devguide-device-twins.md)

3. Gdy skończysz, okienku powinna wyglądać następująco:

   ![Tabela z wzbogacenia wszystkie dodane](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Wybierz **Zastosuj** Aby zapisać zmiany.

## <a name="send-messages-to-the-iot-hub"></a>Wysyłanie komunikatów do Centrum IoT Hub

Teraz, gdy wzbogacenia wiadomości są skonfigurowane dla punktu końcowego, uruchamianie aplikacji symulowane urządzenie w celu wysyłania komunikatów do Centrum IoT. Centrum została prawidłowo skonfigurowana przy użyciu reguł, których wykonywanie następujących czynności:

* Wiadomości kierowane do punktu końcowego magazynu ContosoStorageEndpointOriginal nie będzie wzbogacony i będą przechowywane w kontenerze magazynu `original`.

* Wiadomości kierowane do punktu końcowego magazynu ContosoStorageEndpointEnriched zostanie wzbogacony i przechowywane w kontenerze magazynu `enriched`.

Aplikacja symulowane urządzenie jest jedną z aplikacji w ramach rozpakowany pobierania. Aplikacja wysyła wiadomości dla każdej z metod routingu inny komunikat w [routingu samouczek](tutorial-routing.md); obejmuje to usługi Azure Storage.

Kliknij dwukrotnie plik rozwiązania (IoT_SimulatedDevice.sln), aby otworzyć kod w programie Visual Studio, a następnie otwórz plik Program.cs. Zastąp `{your hub name}` nazwą Centrum IoT. Format nazwy hosta Centrum IoT jest **{nazwa Twojego Centrum} .azure-devices.net**. W tym samouczku jest nazwą hosta Centrum **ContosoTestHubMsgEn.azure-devices.net**. Następnie zastąp `{device key}` kluczem urządzenia zostały zapisane wcześniej podczas uruchamiania skryptu, aby utworzyć zasoby.

Jeśli nie masz klucz urządzenia, możesz pobrać go z poziomu portalu. Po zalogowaniu przejdź do **grup zasobów**, wybierz grupę zasobów, a następnie wybierz swoje Centrum IoT. Sprawdź w obszarze **urządzeń IoT** dla Twojego urządzenia testowego i wybierz swoje urządzenie. Wybierz ikonę kopiowania obok **klucz podstawowy** skopiowania go do Schowka.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Uruchamianie i testowanie

Uruchom aplikację konsolową. Zaczekaj kilka minut. Wiadomości, które są wysyłane są wyświetlane na ekranie konsoli w aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Losowo przypisuje poziom `critical` lub `storage`, powodując komunikat, który ma być kierowany do konta magazynu lub do domyślnego punktu końcowego. Komunikaty wysyłane do **wzbogacony** kontenera na koncie magazynu będzie wzbogacony.

Po wysłaniu kilka komunikatów magazynu, należy wyświetlić dane.

1. Wybierz **grup zasobów**, a następnie znajdź swoją grupę zasobów (ContosoResourcesMsgEn) i wybierz ją.

2. Wybierz konto magazynu (contosostorage). Następnie wybierz pozycję **Eksploratora usługi Storage (wersja zapoznawcza)** z poziomu okienka wyboru po lewej stronie.

   ![Wybierz pozycję Eksplorator usługi storage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Wybierz **kontenery obiektów BLOB** się dwa kontenery, których można użyć.

   ![Zobacz kontenerów na koncie magazynu](./media/tutorial-message-enrichments/show-blob-containers.png)

Komunikaty w kontenerze o nazwie **wzbogacony** mają wzbogacenia komunikat zawarte w wiadomości. Komunikaty w kontenerze o nazwie **oryginalnego** będzie miał nieprzetworzone komunikaty z nie wzbogacenia. Przejść do szczegółów jednego z kontenerów do momentu możesz uzyskać dostęp do dołu i Otwórz plik najnowsze wiadomości, a następnie Zrób to samo dla innego kontenera zweryfikować, że nie istnieją żadne wzbogacenia dodawane do komunikatów w ramach tego kontenera.

Po wyświetleniu wiadomości, które został wzbogacony powinny zostać wyświetlone "Moje Centrum IoT" przy użyciu nazwy Centrum, a także lokalizacji i identyfikator klienta w następujący sposób:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

A Oto unenriched komunikat. "" Moje Centrum IoT Hub","Lokalizacja urządzenia"i"customerID"nie są wyświetlane w tym miejscu, ponieważ ten punkt końcowy nie ma żadnych wzbogacenia.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć wszystkie zasoby utworzone w ramach tego samouczka, Usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT, konta magazynu i samej grupy zasobów.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Użyj wiersza polecenia platformy Azure, aby wyczyścić zasoby

Aby usunąć grupę zasobów, użyj polecenia [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` ustawiono **ContosoResources** ponownie na początku tego samouczka.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zostanie skonfigurowane i przetestowane Dodawanie wzbogacenia wiadomości do komunikatów usługi IoT Hub wykonując następujące czynności:

**Za pomocą wzbogacenia komunikatów usługi IoT Hub**
> [!div class="checklist"]
> * Przy użyciu wiersza polecenia platformy Azure, Utwórz zasobów — Centrum IoT hub, konto magazynu przy użyciu dwóch enendpoints i konfiguracji routingu.
> * Użyj witryny Azure portal, aby skonfigurować wzbogacenia wiadomości.
> * Uruchom aplikację, która symuluje urządzenie IoT wysyłanie komunikatu do Centrum.
> * Wyświetlanie wyników i sprawdź, czy wzbogacenia komunikat działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat wzbogacenia wiadomości zobacz [Przegląd wzbogacenia komunikat](iot-hub-message-enrichments-overview.md).

Aby uzyskać więcej informacji na temat routingu komunikatów zobacz następujące artykuły:

* [Umożliwia routing komunikatów usługi IoT Hub wysyłanie komunikatów z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

* [Samouczek: Routing w Centrum IoT Hub](tutorial-routing.md)