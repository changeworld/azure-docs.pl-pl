---
title: Wdróż moduł Blob Storage platformy Azure na urządzeniach — Azure IoT Edge | Microsoft Docs
description: Wdróż moduł usługi Azure Blob Storage do urządzenia usługi IoT Edge do przechowywania danych na urządzeniach brzegowych.
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: kgremban
manager: mchad
ms.openlocfilehash: 089c90abb999751db77bbe1d89d1d118ae712b52
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947080"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Wdrażanie Blob Storage platformy Azure w module IoT Edge na urządzeniu

Istnieje kilka sposobów wdrażania modułów na urządzeniu IoT Edge i wszystkie z nich pracują na platformie Azure Blob Storage w modułach IoT Edge. Te dwie metody najprostszym mają używać witryny Azure portal lub szablonów programu Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

- [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.
- [Urządzenie usługi IoT Edge](how-to-register-device-portal.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) i [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) w przypadku wdrażania z Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Wdróż z Azure Portal

Azure Portal przeprowadzi Cię przez proces tworzenia manifestu wdrażania i wypychania wdrożenia na urządzenie IoT Edge.

### <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
1. Wybierz **usługi IoT Edge** z menu.
1. Kliknij identyfikator urządzenie docelowe z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON. Składa się z trzech kroków: **Dodaj moduły**, **Określ trasy**i **Przejrzyj wdrożenie**.

#### <a name="add-modules"></a>Dodaj moduły

1. W **moduły wdrożeń** części strony wybierz **Dodaj**.

1. Z typów modułów na liście rozwijanej wybierz pozycję **moduł IoT Edge**.

1. Podaj nazwę modułu, a następnie określ obraz kontenera:

   - **Nazwa** — azureblobstorageoniotedge
   - **Identyfikator URI obrazu** — MCR.Microsoft.com/Azure-Blob-Storage:Latest

   > [!IMPORTANT]
   > W Azure IoT Edge jest rozróżniana wielkość liter podczas wykonywania wywołań do modułów, a zestaw SDK magazynu jest również domyślnie pisany małymi literami. Mimo że nazwa modułu w [witrynie Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) to **AzureBlobStorageonIoTEdge**, zmiana nazwy na małe jest pomocne, aby upewnić się, że połączenia z usługą Azure Blob Storage na IoT Edge module nie zostaną przerwane.

1. Wartości domyślne **opcji tworzenia kontenera** definiują powiązania portów wymagane przez kontener, ale konieczne jest również dodanie informacji o koncie magazynu i instalacji magazynu na urządzeniu. Zastąp domyślny kod JSON w portalu następującym kodem JSON:

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

1. Zaktualizuj skopiowany kod JSON przy użyciu następujących informacji:

   - Zamień `<your storage account name>` na nazwę, którą można zapamiętać. Nazwy kont powinny składać się z od 3 do 24 znaków, z małymi literami i cyframi. Bez spacji.

   - Zamień `<your storage account key>` na 64-bajtowy klucz Base64. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.

   - Zamień `<storage mount>` według systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych. Instalacja magazynu mapuje lokalizację na urządzeniu dostarczaną do lokalizacji zestawu w module.

     - W przypadku kontenerów systemu Linux format jest  *\<ścieżką magazynu lub woluminem >:/blobroot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-Volume:/blobroot** 
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Upewnij się, że postępuj zgodnie z instrukcjami, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows format  *\<jest ścieżką do magazynu lub woluminem >: C:/BlobRoot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-wolumin: C:/blobroot**. 
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData: c:/BlobRoot**.
         - Zamiast korzystać z dysku lokalnego, można zmapować lokalizację sieciową SMB. Aby uzyskać więcej informacji, zobacz [Używanie udziału SMB jako magazynu lokalnego](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje konkretną lokalizację w module. Instalacja magazynu powinna zawsze kończyć się z **:/blobroot** dla kontenerów systemu Linux i **: C:/blobroot** for Windows Containers.

1. Ustaw właściwości [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) modułu przez SKOPIOWANIE poniższego kodu JSON i wklejenie go do okna **Ustaw odpowiednie właściwości sznurka modułu** . Skonfiguruj każdą właściwość z odpowiednią wartością, Zapisz ją i Kontynuuj wdrażanie.

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
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
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![Ustawianie opcji tworzenia kontenera, właściwości deviceAutoDeleteProperties i deviceToCloudUploadProperties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Aby uzyskać informacje na temat konfigurowania deviceToCloudUploadProperties i deviceAutoDeleteProperties po wdrożeniu modułu, zobacz [Edycja sznurka modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji na temat żądanych właściwości, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](module-composition.md#define-or-update-desired-properties).

1. Wybierz pozycję **Zapisz**.

1. Wybierz **dalej** aby przejść do sekcji trasy.

#### <a name="specify-routes"></a>Określanie tras

Zachowaj trasy domyślne, a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd.

#### <a name="review-deployment"></a>Przegląd wdrożenia

Przejrzyj sekcję programy, które możesz wdrożenia JSON manifestu, które utworzono na podstawie dokonanego wyboru w poprzednich sekcjach. Istnieją także dwa moduły zadeklarowane jako niedodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane ustawienia domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz **przesyłania**.

### <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

Po przesłaniu wdrożenia wrócisz do **IoT Edge** stronie Centrum IoT Hub.

1. Wybierz urządzenie IoT Edge, do którego należy wdrożenie, aby otworzyć jego szczegóły.
1. Sprawdź szczegóły urządzenia, aby sprawdzić, czy moduł usługi BLOB Storage jest wymieniony jako **określony we wdrożeniu** i **zgłoszony przez urządzenie**.

Uruchomienie modułu na urządzeniu może potrwać kilka minut, a następnie zgłoszone z powrotem do IoT Hub. Odśwież stronę, aby zobaczyć zaktualizowany stan.

## <a name="deploy-from-visual-studio-code"></a>Wdróż z Visual Studio Code

Usługa Azure IoT Edge zapewnia szablony programu Visual Studio Code, aby pomóc w tworzeniu rozwiązań brzegowych. Wykonaj następujące kroki, aby utworzyć nowe rozwiązanie IoT Edge przy użyciu modułu BLOB Storage i skonfigurować manifest wdrożenia.

1. Wybierz **widoku** > **polecenia palety**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge).

   ![Uruchom nowe rozwiązanie IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na komputerze deweloperskim, aby Visual Studio Code utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **istniejący moduł (wprowadź pełny obraz URL)** . |
   | Podaj nazwę modułu | Wprowadź wszystkie małe nazwy dla modułu, takie jak **azureblobstorageoniotedge**.<br /><br />Ważne jest używanie małych nazw dla Blob Storage platformy Azure na IoT Edge module. W IoT Edge jest rozróżniana wielkość liter podczas odwoływania się do modułów, a zestaw SDK magazynu jest domyślnie pisany małymi literami. |
   | Podaj obraz platformy Docker dla modułu | Podaj identyfikator URI obrazu: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code pobiera podane informacje, tworzy rozwiązanie IoT Edge, a następnie ładuje je w nowym oknie. Szablon rozwiązania, tworzy szablon manifestu wdrożenia, który zawiera obraz modułu magazynu obiektów blob, ale należy skonfigurować opcje tworzenia modułu.

1. Otwórz *deployment.template.json* nowy obszar roboczy rozwiązania i Znajdź **modułów** sekcji. Wprowadź następujące zmiany w konfiguracji:

   1. Usuń moduł **SimulatedTemperatureSensor** , ponieważ nie jest to konieczne w przypadku tego wdrożenia.

   1. Skopiuj i wklej następujący kod do `createOptions` pola:

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

1. Zamień `<your storage account name>` na nazwę, którą można zapamiętać. Nazwy kont powinny składać się z od 3 do 24 znaków, z małymi literami i cyframi. Bez spacji.

1. Zamień `<your storage account key>` na 64-bajtowy klucz Base64. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.

1. Zamień `<storage mount>` według systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych. Instalacja magazynu mapuje lokalizację na urządzeniu dostarczaną do lokalizacji zestawu w module.  

      
     - W przypadku kontenerów systemu Linux format jest  *\<ścieżką magazynu lub woluminem >:/blobroot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-Volume:/blobroot** 
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Upewnij się, że postępuj zgodnie z instrukcjami, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows format  *\<jest ścieżką do magazynu lub woluminem >: C:/BlobRoot*. Na przykład:
         - Użyj [instalacji woluminu](https://docs.docker.com/storage/volumes/): **My-wolumin: C:/blobroot**. 
         - Użyj [instalacji bind](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData: c:/BlobRoot**.
         - Zamiast korzystać z dysku lokalnego, można zmapować lokalizację sieciową SMB. Aby uzyskać więcej informacji, zobacz [Używanie udziału SMB jako magazynu lokalnego](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje konkretną lokalizację w module. Instalacja magazynu powinna zawsze kończyć się z **:/blobroot** dla kontenerów systemu Linux i **: C:/blobroot** for Windows Containers.

1. Skonfiguruj [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) dla modułu, dodając następujący kod JSON do pliku *Deployment. Template. JSON* . Skonfiguruj każdą właściwość z odpowiednią wartością i Zapisz plik.

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

Jeśli chcesz wdrożyć wiele wystąpień BLOB Storage platformy Azure w module IoT Edge, musisz podać inną ścieżkę magazynu i zmienić `HostPort` wartość, z którą jest powiązany moduł. Moduły magazynu obiektów blob jest zawsze udostępniania portów 11002 w kontenerze, ale można zadeklarować port, który jest powiązany z na hoście.

Edytuj **Opcje tworzenia kontenera** (w Azure Portal) lub w polu "w pliku *Deployment. Template. JSON* w `HostPort` Visual Studio Code), aby zmienić wartość:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Po podłączeniu do modułów magazynu obiektów blob dodatkowe Zmiana punktu końcowego portu zaktualizowanego hosta.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [usłudze Azure Blob Storage w systemie IoT Edge](how-to-store-data-blob.md)

Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).
