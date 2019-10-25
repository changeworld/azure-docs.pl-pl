---
title: Samouczek — korzystanie z funkcji wzbogacania komunikatów IoT Hub platformy Azure
description: Samouczek przedstawiający sposób używania wzbogacania komunikatów dla komunikatów usługi Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 8b74621f2c5a9c91ece58c8118cd2bc952c3a464
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809700"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Samouczek: korzystanie z wzbogacania komunikatów IoT Hub platformy Azure (wersja zapoznawcza)

*Wzbogacanie komunikatów* polega na tym, że IoT Hub *sygnatury* wiadomości z dodatkowymi informacjami przed wysłaniem komunikatów do określonego punktu końcowego. Jednym z powodów użycia wzbogacania komunikatów jest dołączenie danych, które mogą być używane do uproszczenia przetwarzania podrzędnego. Na przykład wzbogacanie komunikatów telemetrycznych urządzeń za pomocą znacznika sznurka urządzenia może zmniejszyć obciążenie klientów, aby umożliwić wywoływanie interfejsu API z użyciem urządzeń z systemem dla tych informacji. Aby uzyskać więcej informacji, zobacz [Omówienie wzbogacania komunikatów](iot-hub-message-enrichments-overview.md).

W tym samouczku użyjesz interfejsu wiersza polecenia platformy Azure, aby skonfigurować zasoby, w tym dwa punkty końcowe wskazujące na dwa różne kontenery magazynu — **wzbogacone** i **oryginalne**. Następnie użyj [Azure Portal](https://portal.azure.com) , aby skonfigurować wzbogacanie komunikatów, które mają być stosowane tylko do wiadomości wysyłanych do punktu końcowego przy użyciu **wzbogaconego** kontenera magazynu. Komunikaty są wysyłane do IoT Hub, które są kierowane do obu kontenerów magazynu. Tylko komunikaty wysyłane do punktu końcowego dla **wzbogaconego** kontenera magazynu zostaną wzbogacone.

Poniżej przedstawiono zadania, które należy wykonać w celu wykonania tego samouczka:

**Korzystanie z IoT Hub wzbogacania komunikatów**
> [!div class="checklist"]
> * Korzystając z interfejsu wiersza polecenia platformy Azure, Utwórz zasoby — Centrum IoT, konto magazynu z dwoma punktami końcowymi i konfigurację routingu.
> * Użyj Azure Portal, aby skonfigurować wzbogacania komunikatów.
> * Uruchom aplikację, która symuluje wysyłanie komunikatów przez urządzenie IoT do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacania komunikatów działają zgodnie z oczekiwaniami.

## <a name="prerequisites"></a>Wymagania wstępne

* Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Instalacja programu [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Pobieranie przykładowego kodu

Pobierz [symulację urządzenia IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i rozpakuj go. To repozytorium zawiera kilka aplikacji, w tym te, które będą używane do wysyłania komunikatów do centrum IoT.

Ten pobieranie zawiera również skrypt służący do tworzenia zasobów używanych do testowania wzbogacania komunikatów. Skrypt jest w/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Na razie możesz sprawdzić skrypt i użyć go. Możesz również skopiować skrypt bezpośrednio z artykułu.

Gdy wszystko będzie gotowe do rozpoczęcia testowania, użyjesz aplikacji symulacji urządzenia z tego pobrania, aby wysłać wiadomość do centrum IoT.

## <a name="set-up-and-configure-resources"></a>Konfigurowanie i Konfigurowanie zasobów

Oprócz tworzenia niezbędnych zasobów skrypt interfejsu wiersza polecenia platformy Azure konfiguruje również dwa trasy do punktów końcowych, które są oddzielnymi kontenerami magazynu. Aby uzyskać więcej informacji na temat konfigurowania routingu, zapoznaj się z [samouczkiem](tutorial-routing.md)dotyczącym routingu. Po skonfigurowaniu zasobów Użyj [Azure Portal](https://portal.azure.com) , aby skonfigurować wzbogacania komunikatów dla każdego punktu końcowego, a następnie przejdź do kroku testowania.

> [!NOTE]
> Wszystkie komunikaty są kierowane do obu punktów końcowych, ale tylko komunikaty przechodzące do punktu końcowego ze skonfigurowanymi wzbogacami komunikatów zostaną wzbogacone.
>

Możesz użyć poniższego skryptu lub otworzyć skrypt w folderze/Resources pobranego repozytorium. Poniżej przedstawiono kroki, które zostaną wykonane przez skrypt:

* Tworzenie centrum IoT Hub.
* Tworzenie konta magazynu
* Utwórz dwa kontenery na koncie magazynu — jeden dla ulepszonych komunikatów i jeden dla komunikatów, które nie są wzbogacane.
* Skonfiguruj Routing dla dwóch różnych kont magazynu.
    * Utwórz punkt końcowy dla każdego kontenera konta magazynu.
    * Utwórz trasę do każdego z punktów końcowych kontenera kont magazynu.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowe, takie jak nazwa IoT Hub i nazwa konta magazynu. Aby ułatwić uruchamianie skryptu, nazwy tych zasobów są dołączane losowo wartością alfanumeryczną o nazwie *randomValue*. RandomValue jest generowany raz u góry skryptu i dołączany do nazw zasobów zgodnie z wymaganiami w całym skrypcie. Jeśli nie chcesz go losowo, możesz ustawić go na pusty ciąg lub do określonej wartości.

Jeśli jeszcze tego nie zrobiono, Otwórz [okno Cloud Shell](https://shell.azure.com) i upewnij się, że jest ono ustawione na bash. Otwórz skrypt w niespakowanym repozytorium, użyj klawiszy Ctrl-A, aby go zaznaczyć, a następnie naciśnij klawisze CTRL-C, aby skopiować go. Alternatywnie można skopiować następujący skrypt interfejsu wiersza polecenia lub otworzyć go bezpośrednio w Cloud Shell. Wklej skrypt w oknie Cloud Shell, klikając prawym przyciskiem myszy wiersz polecenia i wybierając pozycję **Wklej**. Skrypt jest uruchamiany jedną instrukcją w danym momencie. Po zatrzymaniu skryptu wybierz **klawisz ENTER** , aby upewnić się, że uruchomiono ostatnie polecenie. Poniższy blok kodu pokazuje używany skrypt z komentarzami wyjaśniającymi, co robi.

Poniżej przedstawiono zasoby utworzone przez skrypt. **Ulepszony** oznacza, że zasób jest przeznaczony dla komunikatów z wzbogacaniem. **Oryginalna** oznacza, że zasób jest przeznaczony dla komunikatów, które nie są wzbogacane.

| Nazwa | Wartość |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nazwa kontenera | Oryginał  |
| nazwa kontenera | wzbogacone  |
| Nazwa urządzenia IoT | Contoso-test-Device |
| Nazwa IoT Hub | ContosoTestHubMsgEn |
| Nazwa konta magazynu | contosostorage |
| Nazwa punktu końcowego 1 | ContosoStorageEndpointOriginal |
| Nazwa punktu końcowego 2 | ContosoStorageEndpointEnriched|
| Nazwa trasy 1 | ContosoStorageRouteOriginal |
| Nazwa trasy 2 | ContosoStorageRouteEnriched |

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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

W tym momencie wszystkie zasoby są skonfigurowane i skonfigurowano Routing. Konfigurację routingu wiadomości można wyświetlić w portalu i skonfigurować wzbogacanie komunikatów dla komunikatów przechodzących do **wzbogaconego** kontenera magazynu.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Wyświetlanie routingu i Konfigurowanie wzbogacania komunikatów

1. Przejdź do IoT Hub, wybierając pozycję **grupy zasobów**, a następnie wybierz grupę zasobów skonfigurowaną dla tego samouczka (**ContosoResources_MsgEn**). Znajdź IoT Hub na liście i wybierz ją. Wybierz pozycję **routing wiadomości** dla Centrum IoT.

   ![Wybierz routing wiadomości](./media/tutorial-message-enrichments/select-iot-hub.png)

   Okienko routing wiadomości zawiera trzy karty — **trasy**, **niestandardowe punkty końcowe**i **komunikaty wzbogacania**. Możesz przeglądać dwie pierwsze karty, aby zobaczyć konfigurację skonfigurowaną przez skrypt. Użyj trzeciej karty, aby dodać wzbogacania komunikatów. Wzbogacamy komunikaty przechodzące do punktu końcowego dla kontenera magazynu o nazwie **wzbogacone**. Wypełnij pola Nazwa i wartość, a następnie wybierz punkt końcowy **ContosoStorageEndpointEnriched** z listy rozwijanej. Oto przykład konfigurowania wzbogacania, które dodaje nazwę IoT Hub do komunikatu:

   ![Dodaj pierwsze wzbogacanie](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Dodaj te wartości do listy dla punktu końcowego ContosoStorageEndpointEnriched.

   | Nazwa | Wartość | Punkt końcowy (lista rozwijana) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | deviceLocation | $twin. Tags. Location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Identyfikator | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Jeśli urządzenie nie ma sznurka, wartość wprowadzona w tym miejscu będzie sygnaturą ciągu dla wartości w wzbogacaniu komunikatów. Aby wyświetlić informacje dotyczące sznurka urządzenia, przejdź do centrum w portalu, a następnie wybierz pozycję **urządzenia IoT**, wybierz urządzenie, a następnie wybierz pozycję **sznurki urządzenia** u góry strony.
   >
   > Można edytować informacje o przędze w celu dodania tagów (takich jak lokalizacja) i ustawić ją na określoną wartość, jeśli chcesz. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](iot-hub-devguide-device-twins.md)

3. Po zakończeniu okienko powinno wyglądać podobnie do tego obrazu:

   ![Tabela ze wszystkimi dodanymi wzbogacaniem](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Wybierz pozycję **Zastosuj** , aby zapisać zmiany.

## <a name="send-messages-to-the-iot-hub"></a>Wysyłanie komunikatów do IoT Hub

Teraz, gdy w punkcie końcowym są skonfigurowane wzbogacania komunikatów, uruchom aplikację symulowanego urządzenia, aby wysyłać komunikaty do IoT Hub. Koncentrator został skonfigurowany z użyciem ustawień, które spełniają następujące kwestie:

* Komunikaty kierowane do punktu końcowego magazynu ContosoStorageEndpointOriginal nie zostaną wzbogacone i będą przechowywane w kontenerze magazynu `original`.

* Komunikaty kierowane do punktu końcowego magazynu ContosoStorageEndpointEnriched będą wzbogacone i przechowywane w kontenerze magazynu `enriched`.

Aplikacja symulowanego urządzenia jest jedną z aplikacji w niespakowanym pobieraniu. Aplikacja wysyła komunikaty dla każdej z różnych metod routingu komunikatów w [samouczku routingu](tutorial-routing.md); obejmuje to usługę Azure Storage.

Kliknij dwukrotnie plik rozwiązania (IoT_SimulatedDevice. sln), aby otworzyć kod w programie Visual Studio, a następnie otwórz Program.cs. Zastąp nazwę Centrum IoT Hub `{your hub name}`znacznika. Format nazwy hosta Centrum IoT to **{nazwa centrum}. Azure-Devices.NET**. W tym samouczku nazwa hosta centrum to **ContosoTestHubMsgEn.Azure-Devices.NET**. Następnie zastąp klucz urządzenia zapisany wcześniej podczas uruchamiania skryptu, aby utworzyć zasoby dla znacznika `{your device key}`.

Jeśli nie masz klucza urządzenia, możesz go pobrać z portalu. Po zalogowaniu przejdź do pozycji **grupy zasobów**, wybierz grupę zasobów, a następnie wybierz IoT Hub. Sprawdź w obszarze **urządzenia IoT** dla urządzenia testowego i wybierz urządzenie. Wybierz ikonę kopiowania obok **klucza podstawowego** , aby skopiować ją do Schowka.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Uruchamianie i testowanie

Uruchom aplikację konsolową. Zaczekaj kilka minut. Wysyłane komunikaty są wyświetlane na ekranie konsoli aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Losowo przypisuje poziom `critical` lub `storage`, powodując kierowanie komunikatu do konta magazynu lub domyślnego punktu końcowego. Komunikaty wysyłane do **wzbogaconego** kontenera na koncie magazynu zostaną wzbogacone.

Po wysłaniu kilku komunikatów magazynu Wyświetl dane.

1. Wybierz pozycję **grupy zasobów**, a następnie Znajdź grupę zasobów (ContosoResourcesMsgEn) i wybierz ją.

2. Wybierz konto magazynu (contosostorage). Następnie wybierz pozycję **Eksplorator usługi Storage (wersja zapoznawcza)** z okienka wyboru po lewej stronie.

   ![Wybieranie Eksploratora magazynu](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Wybierz **kontenery obiektów BLOB** , aby wyświetlić dwa kontenery, których można użyć.

   ![Zobacz kontenery na koncie magazynu](./media/tutorial-message-enrichments/show-blob-containers.png)

Komunikaty w kontenerze o nazwie **wzbogacono** mają wzbogacenia komunikatów zawarte w wiadomościach. Wiadomości w kontenerze o nazwie **Original** będą zawierać nieprzetworzone komunikaty bez wzbogacania. Przechodzenie do szczegółów jednego z kontenerów, aż do momentu, w którym zostanie otwarty najnowszy plik komunikatów, a następnie wykonaj te same czynności dla drugiego kontenera, aby sprawdzić, czy do komunikatów w tym kontenerze nie zostały dodane żadne wzbogacania.

Podczas przeglądania komunikatów, które zostały wzbogacone, powinna zostać wyświetlona wartość "My IoT Hub" z nazwą centrum oraz lokalizacją i IDENTYFIKATORem klienta, w tym:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Oto niewzbogacony komunikat. "My IoT Hub", "devicelocation" i "customerID" nie są wyświetlane w tym miejscu, ponieważ są to pola, które byłyby dodawane przez wzbogacenia, a ten punkt końcowy nie ma wzbogacania.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć wszystkie zasoby utworzone w ramach tego samouczka, Usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT, konta magazynu i samej grupy zasobów.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Czyszczenie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` został ustawiony tak, aby **ContosoResources** na początku tego samouczka.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i przetestowano Dodawanie wzbogacania komunikatów do IoT Hub komunikatów, wykonując następujące czynności:

**Korzystanie z IoT Hub wzbogacania komunikatów**
> [!div class="checklist"]
> * Korzystając z interfejsu wiersza polecenia platformy Azure, Utwórz zasoby — Centrum IoT, konto magazynu z dwoma enendpoints i konfigurację routingu.
> * Użyj Azure Portal, aby skonfigurować wzbogacania komunikatów.
> * Uruchom aplikację, która symuluje wysyłanie komunikatów przez urządzenie IoT do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacania komunikatów działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat wzbogacania komunikatów, zobacz [Omówienie wzbogacania komunikatów](iot-hub-message-enrichments-overview.md).

Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz następujące artykuły:

* [Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

* [Samouczek: Routing IoT Hub](tutorial-routing.md)