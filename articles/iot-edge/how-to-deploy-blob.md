---
title: Wdróż magazyn obiektów BLOB w module na urządzeniu — Azure IoT Edge
description: Wdróż moduł usługi Azure Blob Storage do urządzenia usługi IoT Edge do przechowywania danych na urządzeniach brzegowych.
author: kgremban
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 8c2df4854f4cdb93c08e22f7dcdc23b1b69b13d6
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548785"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Wdrażanie Blob Storage platformy Azure w module IoT Edge na urządzeniu

Istnieje kilka sposobów wdrażania modułów na urządzeniu IoT Edge i wszystkie z nich pracują na platformie Azure Blob Storage w modułach IoT Edge. Te dwie metody najprostszym mają używać witryny Azure portal lub szablonów programu Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

- [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.
- [Urządzenie usługi IoT Edge](how-to-register-device.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) i [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) w przypadku wdrażania z Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Wdróż z Azure Portal

Azure Portal przeprowadzi Cię przez proces tworzenia manifestu wdrażania i wypychania wdrożenia na urządzenie IoT Edge.

### <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
1. Wybierz **usługi IoT Edge** z menu.
1. Kliknij identyfikator urządzenie docelowe z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia. Trzy kroki są zorganizowane na kartach: **moduły**, **trasy**i **Przegląd + Utwórz**.

#### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj** listę rozwijaną i wybierz **moduł IoT Edge** , aby wyświetlić stronę **Dodawanie modułu IoT Edge** .

2. Na karcie **Ustawienia modułu** Podaj nazwę modułu, a następnie określ identyfikator URI obrazu kontenera:

   Przykłady:
  
   - **Nazwa modułu IoT Edge**: `azureblobstorageoniotedge`
   - **Identyfikator URI obrazu**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Ustawienia sznurka modułu](./media/how-to-deploy-blob/addmodule-tab1.png)

   Nie zaznaczaj opcji **Dodaj** , dopóki nie zostaną określone wartości na kartach **Ustawienia modułu**, **Opcje tworzenia kontenera**i **Ustawienia sznurka modułu** , zgodnie z opisem w tej procedurze.

   > [!IMPORTANT]
   > W Azure IoT Edge jest rozróżniana wielkość liter podczas wykonywania wywołań do modułów, a zestaw SDK magazynu jest również domyślnie pisany małymi literami. Mimo że nazwa modułu w [witrynie Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) to **AzureBlobStorageonIoTEdge**, zmiana nazwy na małe jest pomocne, aby upewnić się, że połączenia z usługą Azure Blob Storage na IoT Edge module nie zostaną przerwane.

3. Otwórz kartę **Opcje tworzenia kontenera** .

   ![Ustawienia sznurka modułu](./media/how-to-deploy-blob/addmodule-tab3.png)

   Skopiuj i wklej poniższy kod JSON w polu, aby podać informacje o koncie magazynu i instalację magazynu na urządzeniu.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. Zaktualizuj kod JSON skopiowany do **opcji tworzenia kontenera** z następującymi informacjami:

   - Zastąp `<your storage account name>` nazwą, którą można zapamiętać. Nazwy kont powinny składać się z od 3 do 24 znaków, z małymi literami i cyframi. Bez spacji.

   - Zastąp `<your storage account key>` kluczem Base64 o 64-bajcie. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.

   - Zastąp `<storage mount>` zgodnie z systemem operacyjnym kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) lub ścieżkę bezwzględną do istniejącego katalogu na urządzeniu IoT Edge, w którym moduł obiektów BLOB będzie przechowywał swoje dane. Instalacja magazynu mapuje lokalizację na urządzeniu dostarczaną do lokalizacji zestawu w module.

     - W przypadku kontenerów systemu Linux format jest *\<ścieżce magazynu lub woluminu >:/blobroot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-Volume:/blobroot**
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Upewnij się, że postępuj zgodnie z instrukcjami, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows format jest *\<ścieżka do magazynu lub wolumin >: C:/BlobRoot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-wolumin: C:/blobroot**.
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **c:/ContainerData: C:/BlobRoot**.
         - Zamiast korzystać z dysku lokalnego, możesz zmapować lokalizację sieciową SMB, aby uzyskać więcej informacji, zobacz [Używanie udziału SMB jako magazynu lokalnego](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje konkretną lokalizację w module. Instalacja magazynu powinna zawsze kończyć się z **:/blobroot** dla kontenerów systemu Linux i **: C:/blobroot** for Windows Containers.

5. Na karcie **Ustawienia sznurka modułu** Skopiuj poniższy kod JSON i wklej go w polu.

   ![Ustawienia sznurka modułu](./media/how-to-deploy-blob/addmodule-tab4.png)

   Skonfiguruj każdą właściwość z odpowiednią wartością, jak wskazano symbole zastępcze. Jeśli używasz symulatora IoT Edge, ustaw wartości dla zmiennych środowiskowych związanych z tymi właściwościami, zgodnie z opisem w [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Aby uzyskać informacje na temat konfigurowania deviceToCloudUploadProperties i deviceAutoDeleteProperties po wdrożeniu modułu, zobacz [Edycja sznurka modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji na temat żądanych właściwości, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](module-composition.md#define-or-update-desired-properties).

6. Wybierz pozycję **Dodaj**.

7. Wybierz pozycję **Dalej: trasy** , aby przejść do sekcji trasy.

#### <a name="specify-routes"></a>Określanie tras

Zachowaj trasy domyślne i wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do sekcji Przegląd.

#### <a name="review-deployment"></a>Przegląd wdrożenia

Przejrzyj sekcję programy, które możesz wdrożenia JSON manifestu, które utworzono na podstawie dokonanego wyboru w poprzednich sekcjach. Istnieją także dwa moduły zadeklarowane jako niedodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane ustawienia domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

### <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

Po utworzeniu wdrożenia powrócisz do **IoT Edge** stronie Centrum IoT Hub.

1. Wybierz urządzenie IoT Edge, do którego należy wdrożenie, aby otworzyć jego szczegóły.
1. Sprawdź szczegóły urządzenia, aby sprawdzić, czy moduł usługi BLOB Storage jest wymieniony jako **określony we wdrożeniu** i **zgłoszony przez urządzenie**.

Uruchomienie modułu na urządzeniu może potrwać kilka minut, a następnie zgłoszone z powrotem do IoT Hub. Odśwież stronę, aby zobaczyć zaktualizowany stan.

## <a name="deploy-from-visual-studio-code"></a>Wdróż z Visual Studio Code

Usługa Azure IoT Edge zapewnia szablony programu Visual Studio Code, aby pomóc w tworzeniu rozwiązań brzegowych. Wykonaj następujące kroki, aby utworzyć nowe rozwiązanie IoT Edge przy użyciu modułu BLOB Storage i skonfigurować manifest wdrożenia.

1. Wybierz **widoku** > **polecenia palety**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**.

   ![Uruchom nowe rozwiązanie IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na komputerze deweloperskim, aby Visual Studio Code utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **istniejący moduł (wprowadź pełny obraz URL)** . |
   | Podaj nazwę modułu | Wprowadź wszystkie małe nazwy dla modułu, takie jak **azureblobstorageoniotedge**.<br/><br/>Ważne jest używanie małych nazw dla Blob Storage platformy Azure na IoT Edge module. W IoT Edge jest rozróżniana wielkość liter podczas odwoływania się do modułów, a zestaw SDK magazynu jest domyślnie pisany małymi literami. |
   | Podaj obraz platformy Docker dla modułu | Podaj identyfikator URI obrazu: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code pobiera podane informacje, tworzy rozwiązanie IoT Edge, a następnie ładuje je w nowym oknie. Szablon rozwiązania, tworzy szablon manifestu wdrożenia, który zawiera obraz modułu magazynu obiektów blob, ale należy skonfigurować opcje tworzenia modułu.

1. Otwórz *deployment.template.json* nowy obszar roboczy rozwiązania i Znajdź **modułów** sekcji. Wprowadź następujące zmiany w konfiguracji:

   1. Usuń moduł **SimulatedTemperatureSensor** , ponieważ nie jest to konieczne w przypadku tego wdrożenia.

   1. Skopiuj i wklej następujący kod do pola `createOptions`:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Aktualizowanie modułu opcji "Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Zastąp `<your storage account name>` nazwą, którą można zapamiętać. Nazwy kont powinny składać się z od 3 do 24 znaków, z małymi literami i cyframi. Bez spacji.

1. Zastąp `<your storage account key>` kluczem Base64 o 64-bajcie. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.

1. Zastąp `<storage mount>` zgodnie z systemem operacyjnym kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych. Instalacja magazynu mapuje lokalizację na urządzeniu dostarczaną do lokalizacji zestawu w module.  

     - W przypadku kontenerów systemu Linux format jest *\<ścieżce magazynu lub woluminu >:/blobroot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-Volume:/blobroot**
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Upewnij się, że postępuj zgodnie z instrukcjami, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows format jest *\<ścieżka do magazynu lub wolumin >: C:/BlobRoot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-wolumin: C:/blobroot**.
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **c:/ContainerData: C:/BlobRoot**.
         - Zamiast korzystać z dysku lokalnego, można zmapować lokalizację sieciową SMB. Aby uzyskać więcej informacji, zobacz [Używanie udziału SMB jako magazynu lokalnego](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje konkretną lokalizację w module. Instalacja magazynu powinna zawsze kończyć się z **:/blobroot** dla kontenerów systemu Linux i **: C:/blobroot** for Windows Containers.

1. Skonfiguruj [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) dla modułu, dodając następujący kod JSON do pliku *Deployment. Template. JSON* . Skonfiguruj każdą właściwość z odpowiednią wartością i Zapisz plik. Jeśli używasz symulatora IoT Edge, ustaw wartości w powiązanych zmiennych środowiskowych dla tych właściwości, które można znaleźć w sekcji wyjaśnienie [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![Ustaw żądane właściwości dla azureblobstorageoniotedge-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Aby uzyskać informacje na temat konfigurowania deviceToCloudUploadProperties i deviceAutoDeleteProperties po wdrożeniu modułu, zobacz [Edycja sznurka modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji o opcjach tworzenia kontenera, zasadach ponownego uruchamiania i żądanym stanie, zobacz [EdgeAgent wymagane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Zapisz plik *deployment.template.json*.

1. Kliknij prawym przyciskiem myszy **deployment.template.json** i wybierz **manifest wdrożenia usługi IoT Edge Generowanie**.

1. Visual Studio Code pobiera informacje podane we *wdrożeniu. Template. JSON* i używa go do utworzenia nowego pliku manifestu wdrożenia. Manifest wdrożenia jest tworzony w nowym **config** folder w obszarze roboczym Twojego rozwiązania. Po utworzeniu tego pliku, możesz wykonać kroki w [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md) lub [modułów wdrożenia usługi Azure IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Wdróż wiele wystąpień modułów

Jeśli chcesz wdrożyć wiele wystąpień Blob Storage platformy Azure w module IoT Edge, musisz podać inną ścieżkę magazynu i zmienić wartość `HostPort`, z którą jest powiązany moduł. Moduły magazynu obiektów blob jest zawsze udostępniania portów 11002 w kontenerze, ale można zadeklarować port, który jest powiązany z na hoście.

Edytowanie **opcji tworzenia kontenera** (w Azure Portal) lub w polu " **isoptions** " (w pliku *Deployment. Template. JSON* w Visual Studio Code), aby zmienić wartość `HostPort`:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Po podłączeniu do modułów magazynu obiektów blob dodatkowe Zmiana punktu końcowego portu zaktualizowanego hosta.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure Blob Storage w systemie IoT Edge](how-to-store-data-blob.md)

Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).
