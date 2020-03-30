---
title: Wdrażanie magazynu obiektów blob na module na urządzeniu — usługa Azure IoT Edge
description: Wdrażanie modułu usługi Azure Blob Storage na urządzeniu usługi IoT Edge w celu przechowywania danych na urządzeniach brzegowych.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 04b145622a1a4237b576a1bb512b5f749f9c3823
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133333"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Deploy the Azure Blob Storage on IoT Edge module to your device (Wdrażanie modułu usługi Azure Blob Storage w usłudze IoT Edge na urządzeniu)

Istnieje kilka sposobów wdrażania modułów na urządzeniu usługi IoT Edge i wszystkie z nich działają w usłudze Azure Blob Storage w modułach usługi IoT Edge. Dwie najprostsze metody są przy użyciu szablonów portalu Azure lub Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

- [Centrum IoT w](../iot-hub/iot-hub-create-through-portal.md) subskrypcji platformy Azure.
- [Urządzenie IoT Edge](how-to-register-device.md) z zainstalowanym czasem wykonywania IoT Edge.
- [Kod programu Visual Studio](https://code.visualstudio.com/) i [narzędzia IoT platformy Azure,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) jeśli wdrażanie z programu Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Wdrażanie z witryny Azure portal

Portal Azure prowadzi użytkownika przez tworzenie manifestu wdrażania i wypychanie wdrożenia do urządzenia usługi IoT Edge.

### <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. Wybierz pozycję **IoT Edge** z menu.
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Portal Azure ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrożenia. Ma trzy kroki podzielone na karty: **Moduły**, **Trasy**i **Przegląd + Utwórz**.

#### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **Moduły krawędzi IoT** na stronie kliknij pozycję rozwijaną **Dodaj** i wybierz **pozycję Moduł krawędzi IoT,** aby wyświetlić stronę Dodaj moduł krawędzi **IoT.**

2. Na karcie **Ustawienia modułu** podaj nazwę modułu, a następnie określ identyfikator URI obrazu kontenera:

   Przykłady:
  
   - **Nazwa modułu krawędzi IoT:**`azureblobstorageoniotedge`
   - **Identyfikator URI obrazu**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![Ustawienia bliźniaczej reprezentacji modułu](./media/how-to-deploy-blob/addmodule-tab1.png)

   Nie **wybieraj** opcji Dodaj, dopóki nie zostaną określone wartości na kartach **Ustawienia modułu,** **Opcje tworzenia kontenera**i **Ustawienia bliźniaczej reprezentacji modułu,** jak opisano w tej procedurze.

   > [!IMPORTANT]
   > Usługa Azure IoT Edge jest rozróżniana wielkość liter podczas wykonywania połączeń z modułami, a zestaw SDK magazynu również domyślnie zmniejsza wielkość liter. Chociaż nazwa modułu w [portalu Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) jest **AzureBlobStorageonIoTEdge,** zmiana nazwy na małe litery pomaga zapewnić, że połączenia z usługą Azure Blob Storage w module Usługi IoT Edge nie są przerywane.

3. Otwórz kartę **Opcje tworzenia kontenera.**

   ![Ustawienia bliźniaczej reprezentacji modułu](./media/how-to-deploy-blob/addmodule-tab3.png)

   Skopiuj i wklej następujące JSON do pudełka, aby podać informacje o koncie pamięci masowej i uchwyt do przechowywania na urządzeniu.
  
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

4. Zaktualizuj skopiowany do **kontenera opcje tworzenia** json, aby uzyskać następujące informacje:

   - Zastąp `<your storage account name>` nazwą, którą zapamiętasz. Nazwy kont powinny mieć od 3 do 24 znaków, z małych liter i cyfr. Brak spacji.

   - Wymień `<your storage account key>` na 64-bajtowy klucz base64. Klucz można wygenerować za pomocą narzędzi takich jak [GeneratePlus](https://generate.plus/en/base64). Te poświadczenia będą używane do uzyskiwania dostępu do magazynu obiektów blob z innych modułów.

   - Wymień `<storage mount>` zgodnie z systemem operacyjnym kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) lub ścieżkę bezwzględną do istniejącego katalogu na urządzeniu usługi IoT Edge, w którym moduł obiektu blob będzie przechowywać swoje dane. Instalacja pamięci masowej mapuje lokalizację na urządzeniu, którą udostępniasz, w określonej lokalizacji w module.

     - W przypadku kontenerów systemu Linux formatem jest * \<ścieżka pamięci masowej lub wolumin>:/blobroot*. Na przykład:
         - użyj [mocowania woluminu:](https://docs.docker.com/storage/volumes/) **my-volume:/blobroot**
         - użyj [powiązania mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Wykonaj czynności, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows formatem jest * \<ścieżka pamięci masowej lub wolumin>:C:/BlobRoot*. Na przykład:
         - użyj [mocowania głośności:](https://docs.docker.com/storage/volumes/) **my-volume:C:/blobroot**.
         - użyj [powiązania:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - Zamiast korzystać z dysku lokalnego, możesz mapować lokalizację sieciową SMB, aby uzyskać więcej informacji, zobacz [używanie udziału SMB jako lokalnej pamięci masowej](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje określoną lokalizację w module. Instalacja magazynu powinna zawsze kończyć się **na :/blobroot** dla kontenerów systemu Linux i **:C:/BlobRoot** dla kontenerów systemu Windows.

5. Na karcie **Ustawienia bliźniaczej reprezentacji modułu** skopiuj następujący json i wklej go do pola.

   ![Ustawienia bliźniaczej reprezentacji modułu](./media/how-to-deploy-blob/addmodule-tab4.png)

   Skonfiguruj każdą właściwość z odpowiednią wartością, jak wskazują symbole zastępcze. Jeśli używasz symulatora IoT Edge, ustaw wartości powiązanych zmiennych środowiskowych dla tych właściwości, zgodnie z opisem przez [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

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

   Aby uzyskać informacje na temat konfigurowania urządzeniaToCloudUploadPrzewładnienia i deviceAutoDeleteProperties po wdrożeniu modułu, zobacz [Edytowanie bliźniaczej reprezentacji modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji na temat żądanych właściwości, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](module-composition.md#define-or-update-desired-properties).

6. Wybierz pozycję **Dodaj**.

7. Wybierz **dalej: Trasy,** aby przejść do sekcji tras.

#### <a name="specify-routes"></a>Określanie tras

Zachowaj trasy domyślne i wybierz **przycisk Dalej: Przejrzyj + utwórz,** aby przejść do sekcji recenzji.

#### <a name="review-deployment"></a>Przegląd wdrożenia

Sekcja przeglądu zawiera manifest wdrożenia JSON, który został utworzony na podstawie wybranych w poprzednich dwóch sekcjach. Istnieją również dwa moduły zadeklarowane, że nie dodać: **$edgeAgent** i **$edgeHub**. Te dwa moduły tworzą [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

### <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

Po utworzeniu wdrożenia powrócisz do strony **usługi IoT Edge** w centrum IoT Hub.

1. Wybierz urządzenie usługi IoT Edge, które są przeznaczone dla wdrożenia, aby otworzyć jego szczegóły.
1. W szczegółach urządzenia sprawdź, czy moduł magazynu obiektów blob jest wymieniony jako **określony we wdrożeniu** i **zgłoszony przez urządzenie**.

Może upłynąć kilka chwil, aby moduł został uruchomiony na urządzeniu, a następnie zgłoszony z powrotem do usługi IoT Hub. Odśwież stronę, aby wyświetlić zaktualizowany stan.

## <a name="deploy-from-visual-studio-code"></a>Wdrażanie z kodu programu Visual Studio

Usługa Azure IoT Edge udostępnia szablony w programie Visual Studio Code, które ułatwią opracowywanie rozwiązań brzegowych. Poniższe kroki można wykonać, aby utworzyć nowe rozwiązanie usługi IoT Edge z modułem magazynu obiektów blob i skonfigurować manifest wdrożenia.

1. Wybierz **polecenie Wyświetl** > **paletę poleceń**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**.

   ![Uruchom nowe rozwiązanie IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na komputerze deweloperskim dla programu Visual Studio Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **istniejący moduł (wprowadź pełny adres URL obrazu).** |
   | Podaj nazwę modułu | Wprowadź nazwę wszystkich małych liter dla modułu, taką jak **azureblobstorageoniotedge**.<br/><br/>Należy użyć nazwy małych liter dla usługi Azure Blob Storage w module Usługi IoT Edge. W ubudowie IoT edge jest rozróżniana wielkość liter podczas odwoływania się do modułów, a domyślnie sedno SDK magazynu jest małe. |
   | Podaj obraz platformy Docker dla modułu | Podaj identyfikator URI obrazu: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code pobiera podane informacje, tworzy rozwiązanie usługi IoT Edge, a następnie ładuje je w nowym oknie. Szablon rozwiązania tworzy szablon manifestu wdrożenia, który zawiera obraz modułu magazynu obiektów blob, ale należy skonfigurować opcje tworzenia modułu.

1. Otwórz *deployment.template.json* w nowym obszarze roboczym rozwiązania i znajdź sekcję **modułów.** Wprowadzać następujące zmiany konfiguracji:

   1. Usuń **moduł SimulatedTemperatureSensor,** ponieważ nie jest to konieczne dla tego wdrożenia.

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

      ![Aktualizacja modułu createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Zastąp `<your storage account name>` nazwą, którą zapamiętasz. Nazwy kont powinny mieć od 3 do 24 znaków, z małych liter i cyfr. Brak spacji.

1. Wymień `<your storage account key>` na 64-bajtowy klucz base64. Klucz można wygenerować za pomocą narzędzi takich jak [GeneratePlus](https://generate.plus/en/base64). Te poświadczenia będą używane do uzyskiwania dostępu do magazynu obiektów blob z innych modułów.

1. Wymień `<storage mount>` zgodnie z systemem operacyjnym kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) lub ścieżkę bezwzględną do katalogu na urządzeniu usługi IoT Edge, w którym moduł obiektu blob ma przechowywać jego dane. Instalacja pamięci masowej mapuje lokalizację na urządzeniu, którą udostępniasz, w określonej lokalizacji w module.  

     - W przypadku kontenerów systemu Linux formatem jest * \<ścieżka pamięci masowej lub wolumin>:/blobroot*. Na przykład:
         - użyj [mocowania woluminu:](https://docs.docker.com/storage/volumes/) **my-volume:/blobroot**
         - użyj [powiązania mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Wykonaj czynności, aby [udzielić dostępu do katalogu użytkownikowi kontenera](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - W przypadku kontenerów systemu Windows formatem jest * \<ścieżka pamięci masowej lub wolumin>:C:/BlobRoot*. Na przykład:
         - użyj [mocowania głośności:](https://docs.docker.com/storage/volumes/) **my-volume:C:/blobroot**.
         - użyj [powiązania:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - Zamiast korzystać z dysku lokalnego, możesz mapować lokalizację sieciową SMB, aby uzyskać więcej informacji, zobacz [używanie udziału SMB jako lokalnej pamięci masowej](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Nie należy zmieniać drugiej połowy wartości instalacji magazynu, która wskazuje określoną lokalizację w module. Instalacja magazynu powinna zawsze kończyć się **na :/blobroot** dla kontenerów systemu Linux i **:C:/BlobRoot** dla kontenerów systemu Windows.

1. Skonfiguruj [właściwości deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) dla modułu, dodając następujący JSON do pliku *deployment.template.json.* Skonfiguruj każdą właściwość o odpowiedniej wartości i zapisz plik. Jeśli używasz symulatora IoT Edge, ustaw wartości powiązanych zmiennych środowiskowych dla tych właściwości, które można znaleźć w sekcji objaśnienia [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) i [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![zestaw żądanych właściwości dla azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Aby uzyskać informacje na temat konfigurowania urządzeniaToCloudUploadPrzewładnienia i deviceAutoDeleteProperties po wdrożeniu modułu, zobacz [Edytowanie bliźniaczej reprezentacji modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji na temat opcji tworzenia kontenera, zasad ponownego uruchamiania i żądanego stanu, zobacz [Odpowiednie właściwości programu EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Zapisz plik *deployment.template.json*.

1. Kliknij prawym przyciskiem myszy **deployment.template.json** i wybierz polecenie **Generowanie manifestu wdrażania usługi IoT Edge**.

1. Program Visual Studio Code pobiera informacje podane w *pliku deployment.template.json* i używa go do utworzenia nowego pliku manifestu wdrożenia. Manifest wdrażania jest tworzony w nowym folderze **konfiguracji** w obszarze roboczym rozwiązania. Po uzyskaniu tego pliku można wykonać kroki opisane w [obszarze Wdrażanie modułów usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md) lub [wdrażanie modułów usługi Azure IoT Edge za pomocą interfejsu wiersza polecenia 2.0 platformy Azure.](how-to-deploy-modules-cli.md)

## <a name="deploy-multiple-module-instances"></a>Wdrażanie wielu wystąpień modułów

Jeśli chcesz wdrożyć wiele wystąpień usługi Azure Blob Storage w module Usługi IoT Edge, musisz podać inną ścieżkę magazynu i zmienić `HostPort` wartość, z którą wiąże się moduł. Moduły magazynu obiektów blob zawsze udostępnić port 11002 w kontenerze, ale można zadeklarować, który port jest powiązany z hosta.

Edytuj **opcje tworzenia kontenera** (w witrynie Azure portal) lub pole **createOptions** (w pliku *deployment.template.json* w programie Visual Studio Code), aby zmienić `HostPort` wartość:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Po nawiązaniu połączenia z dodatkowymi modułami magazynu obiektów blob należy zmienić punkt końcowy na punkt do zaktualizowanego portu hosta.

## <a name="configure-proxy-support"></a>Konfigurowanie obsługi serwera proxy

Jeśli organizacja korzysta z serwera proxy, należy skonfigurować obsługę serwera proxy dla modułów środowiska uruchomieniowego edgeAgent i edgeHub. Proces ten obejmuje dwa zadania:

- Skonfiguruj demony środowiska wykonawczego i agenta IoT Edge na urządzeniu.
- Ustaw HTTPS_PROXY zmienną środowiskową dla modułów w pliku JSON manifestu wdrożenia.

Ten proces jest opisany w [temacie Konfigurowanie urządzenia usługi IoT Edge do komunikacji za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

Ponadto moduł magazynu obiektów blob wymaga również ustawienia HTTPS_PROXY w pliku wdrażania manifestu. Można bezpośrednio edytować plik manifestu wdrożenia lub użyć witryny Azure portal.

1. Przejdź do centrum Iot w witrynie Azure portal i wybierz **pozycję Iot Edge** z menu lewego okienka.

1. Wybierz urządzenie z modułem do skonfigurowania.

1. Wybierz pozycję **Ustaw moduły**.

1. W sekcji **Moduły krawędzi IoT** na stronie wybierz moduł magazynu obiektów blob.

1. Na stronie **Aktualizacja modułu krawędzi IoT** wybierz kartę **Zmienne środowiskowe.**

1. Dodaj `HTTPS_PROXY` **nazwę** i adres URL serwera proxy dla **wartości**.

      ![Ustawianie zmiennej środowiskowej HTTPS_PROXY](./media/how-to-deploy-blob/https-proxy-config.png)

1. Kliknij **pozycję Aktualizuj**, a następnie pozycję Recenzja + **Utwórz**.

1. Należy zauważyć, że serwer proxy jest dodawany do modułu w manifeście wdrażania i wybierz pozycję **Utwórz**.

1. Sprawdź to ustawienie, wybierając moduł ze strony szczegółów urządzenia, a w dolnej części strony **Szczegóły modułów krawędzi IoT** wybierz kartę **Zmienne środowiskowe.**

      ![Ustawianie zmiennej środowiskowej HTTPS_PROXY](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure Blob Storage w usłudze IoT Edge](how-to-store-data-blob.md).

Aby uzyskać więcej informacji na temat działania manifestów wdrażania i sposobu ich tworzenia, zobacz Opis sposobu [użycia, skonfigurowania i ponownego użycia modułów usługi IoT Edge.](module-composition.md)
