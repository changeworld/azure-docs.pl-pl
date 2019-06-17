---
title: Wdrażanie modułu usługi Azure Blob Storage do urządzeń — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Wdróż moduł usługi Azure Blob Storage do urządzenia usługi IoT Edge do przechowywania danych na urządzeniach brzegowych.
author: kgremban
ms.author: kgremban
ms.date: 05/21/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: philmea
ms.openlocfilehash: d844e81de9cfb556e91ab5c0d5a8074c822cce0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65990464"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Wdrażanie usługi Azure Blob Storage na moduł usługi IoT Edge na urządzeniu

Istnieje kilka sposobów, aby wdrożyć moduły na urządzeniu usługi IoT Edge i ich wszystkich pracy dla usługi Azure Blob Storage na moduły usługi IoT Edge. Te dwie metody najprostszym mają używać witryny Azure portal lub szablonów programu Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

- [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.
- [Urządzenie usługi IoT Edge](how-to-register-device-portal.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) i [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) wdrażania z programu Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Wdrażanie w witrynie Azure portal

Witryna Azure portal przeprowadzi Cię przez tworzenie manifestu wdrażania, a następnie wypychanie wdrożenia na urządzeniu usługi IoT Edge.

### <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
1. Wybierz **usługi IoT Edge** z menu.
1. Kliknij identyfikator urządzenie docelowe z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Witryna Azure portal ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrażania, zamiast ręcznego tworzenia dokumentu JSON. Posiada trzy kroki: **Dodaj moduły**, **określić trasy**, i **Przejrzyj wdrożenia**.

#### <a name="add-modules"></a>Dodaj moduły

1. W **moduły wdrożeń** części strony wybierz **Dodaj**.

1. Z typów modułów na liście rozwijanej wybierz **moduł usługi IoT Edge**.

1. Podaj nazwę dla modułu, a następnie określ obraz kontenera:

   - **Nazwa** -azureblobstorageoniotedge
   - **Identyfikator URI obrazu** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Usługa Azure IoT Edge jest uwzględniana wielkość liter, wykonywać wywołania do modułów, gdy zestaw SDK usługi Storage również domyślnie na małe litery. Chociaż nazwa modułu w [portalu Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) jest **AzureBlobStorageonIoTEdge**, zmiana nazwy na małe litery, pomaga upewnić się, że połączenia w usłudze Azure Blob Storage na moduł usługi IoT Edge nie są przerywane.

1. Wartość domyślna **opcje tworzenia kontenera** wartości definiują powiązań portów, które wymaga kontenera, ale należy również dodać informacje o koncie magazynu i powiązania dla katalogu magazynu na urządzeniu. Zastąp domyślną JSON w portalu JSON poniżej:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Zaktualizuj kod JSON, który został skopiowany z następującymi informacjami:

   - Zastąp `<your storage account name>` o nazwie, który można zapamiętać. Nazwy konta powinny być 3 do 24 znaków, małe litery i cyfry. Nie może zawierać spacji.

   - Zastąp `<your storage account key>` kluczem base64 64 bajtów. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.

   - Zastąp `<storage directory bind>` zgodnie z kontenera w systemie operacyjnym. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych. Powiązania katalogu magazynu mapuje lokalizacji na urządzeniu, które należy podać lokalizację zestawu w module.

     - W przypadku kontenerów systemu Linux, format jest  *\<ścieżka magazynu >: / blobroot*. Na przykład **/srv/containerdata: / blobroot** lub **Moje woluminu: / blobroot**.
     - W przypadku kontenerów Windows format jest  *\<ścieżka magazynu >: C: / BlobRoot*. Na przykład **C: / ContainerData:C: / BlobRoot** lub **Moje — wolumin: C: / blobroot**.

     > [!IMPORTANT]
     > Nie zmieniaj druga połowa katalog magazynu powiązać wartości, które wskazuje do określonej lokalizacji w module. Powiązania katalogu magazynu zawsze powinien kończyć się znakiem **: / blobroot** dla kontenerów systemu Linux i **: C: / BlobRoot** dla kontenerów Windows.

    ![Aktualizacja modułu kontenera tworzenie opcji — portal](./media/how-to-store-data-blob/edit-module.png)

1. Ustaw [warstw](how-to-store-data-blob.md#tiering-properties) i [time-to-live](how-to-store-data-blob.md#time-to-live-properties) właściwości modułu kopiując następujący kod JSON i wklejając ją do **żądane właściwości zestawu modułu bliźniaczej reprezentacji** pole. Konfigurowanie każdej właściwości przy użyciu odpowiedniej wartości, zapisz go i kontynuować wdrożenia.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

      ```

   ![Ustawianie właściwości warstw i czas wygaśnięcia](./media/how-to-store-data-blob/iotedge_custom_module.png)

   Aby uzyskać informacje na temat konfigurowania obsługi warstw i czas wygaśnięcia, po wdrożeniu modułu, zobacz [Edytuj bliźniaczą reprezentację modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji na temat żądane właściwości zobacz [Definiuj lub aktualizacji żądane właściwości](module-composition.md#define-or-update-desired-properties).

1. Wybierz pozycję **Zapisz**.

1. Wybierz **dalej** aby przejść do sekcji trasy.

#### <a name="specify-routes"></a>Określanie tras

Zachowaj trasy domyślne, a następnie wybierz pozycję **dalej** można przejść do sekcji przeglądu.

#### <a name="review-deployment"></a>Przegląd wdrożenia

Przejrzyj sekcję programy, które możesz wdrożenia JSON manifestu, które utworzono na podstawie dokonanego wyboru w poprzednich sekcjach. Istnieją również dwa moduły zadeklarować, że nie zostały dodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane ustawienia domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz **przesyłania**.

### <a name="verify-your-deployment"></a>Sprawdź wdrożenie

Po przesłaniu wdrożenia, można wrócić do **usługi IoT Edge** strony Centrum IoT Hub.

1. Wybierz urządzenie usługi IoT Edge docelowych przy użyciu wdrażania, aby otworzyć jego szczegóły.
1. W szczegółowych informacji o urządzeniu, sprawdź, czy moduł magazynu obiektów blob znajduje się w obu **określonym we wdrożeniu** i **zgłoszona przez urządzenie**.

Może upłynąć kilka minut w przypadku modułu do pracy na urządzeniu i następnie odsyłane do usługi IoT Hub. Odśwież stronę aby zobaczyć zaktualizowany stan.

## <a name="deploy-from-visual-studio-code"></a>Deploy from Visual Studio Code

Usługa Azure IoT Edge zapewnia szablony programu Visual Studio Code, aby pomóc w tworzeniu rozwiązań brzegowych. Wykonaj następujące kroki, aby utworzyć nowe rozwiązanie IoT Edge przy użyciu modułu magazynu obiektów blob i skonfigurować manifest wdrożenia.

1. Wybierz **widoku** > **polecenia palety**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge).

   ![Uruchamianie nowego rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację znajdującą się na komputerze deweloperskim programu Visual Studio Code do tworzenia plików rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **istniejącego modułu (wprowadź pełny obrazu URL)** . |
   | Podaj nazwę modułu | Wprowadź nazwę wszystkie małe dla modułu, takie jak **usłudze Azure blob Storage**.<br /><br />Należy używać małych liter nazwy usługi Azure Blob Storage na moduł usługi IoT Edge. Przy odwoływaniu się do modułów, IoT Edge jest rozróżniana wielkość liter, a zestaw SDK usługi Storage domyślnie na małe litery. |
   | Udostępnianie obrazu platformy Docker modułu | Podaj identyfikator URI obrazu: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code przyjmuje informacje podane lub tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie. Szablon rozwiązania, tworzy szablon manifestu wdrożenia, który zawiera obraz modułu magazynu obiektów blob, ale należy skonfigurować opcje tworzenia modułu.

1. Otwórz *deployment.template.json* nowy obszar roboczy rozwiązania i Znajdź **modułów** sekcji. Wprowadź następujące zmiany konfiguracji:

   1. Usuń **tempSensor** modułu, ponieważ nie użytkownika wymaganych dla tego wdrożenia.

   1. Skopiuj i wklej następujący kod do `createOptions` pola:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Zaktualizuj moduł CreateOptions, można żądań — Visual Studio Code](./media/how-to-store-data-blob/create-options.png)

1. Zastąp `<your storage account name>` o nazwie, który można zapamiętać. Nazwy konta powinny być 3 do 24 znaków, małe litery i cyfry. Nie może zawierać spacji.

1. Zastąp `<your storage account key>` kluczem base64 64 bajtów. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.

1. Zastąp `<storage directory bind>` zgodnie z kontenera w systemie operacyjnym. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych. Powiązania katalogu magazynu mapuje lokalizacji na urządzeniu, które należy podać lokalizację zestawu w module.  

      - W przypadku kontenerów systemu Linux, format jest  *\<ścieżka magazynu >: / blobroot*. Na przykład **/srv/containerdata: / blobroot** lub **Moje woluminu: / blobroot**.
      - W przypadku kontenerów Windows format jest  *\<ścieżka magazynu >: C: / BlobRoot*. Na przykład **C: / ContainerData:C: / BlobRoot** lub **Moje — wolumin: C: / blobroot**.

      > [!IMPORTANT]
      > Nie zmieniaj druga połowa katalog magazynu powiązać wartości, które wskazuje do określonej lokalizacji w module. Powiązania katalogu magazynu zawsze powinien kończyć się znakiem **: / blobroot** dla kontenerów systemu Linux i **: C: / BlobRoot** dla kontenerów Windows.

1. Konfigurowanie [warstw](how-to-store-data-blob.md#tiering-properties) i [time-to-live](how-to-store-data-blob.md#time-to-live-properties) właściwości dla modułu, dodając następujący kod JSON do *deployment.template.json* pliku. Konfigurowanie każdej właściwości przy użyciu odpowiedniej wartości, a następnie zapisz plik.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }
   ```

   ![Ustaw żądane właściwości azureblobstorageoniotedge — Visual Studio Code](./media/how-to-store-data-blob/tiering_ttl.png)

   Aby uzyskać informacje na temat konfigurowania obsługi warstw i czas wygaśnięcia, po wdrożeniu modułu, zobacz [Edytuj bliźniaczą reprezentację modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Aby uzyskać więcej informacji na temat kontenerów opcje tworzenia, uruchom ponownie znaleźć zasady, a żądany stan [EdgeAgent żądane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Zapisz plik *deployment.template.json*.

1. Kliknij prawym przyciskiem myszy **deployment.template.json** i wybierz **manifest wdrożenia usługi IoT Edge Generowanie**.

1. Visual Studio Code przyjmuje informacje podane w *deployment.template.json* i używa jej do utworzenia nowego pliku manifestu wdrożenia. Manifest wdrożenia jest tworzony w nowym **config** folder w obszarze roboczym Twojego rozwiązania. Po utworzeniu tego pliku, możesz wykonać kroki w [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md) lub [modułów wdrożenia usługi Azure IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Wdrażanie wielu wystąpień modułu

Jeśli chcesz wdrożyć wiele wystąpień usługi Azure Blob Storage na moduł usługi IoT Edge, należy podać ścieżkę innego magazynu i zmienić `HostPort` wartość, która wiąże modułu. Moduły magazynu obiektów blob jest zawsze udostępniania portów 11002 w kontenerze, ale można zadeklarować port, który jest powiązany z na hoście.

Edytuj **opcje tworzenia kontenera** (w witrynie Azure portal) lub **CreateOptions, można żądań** pola (w *deployment.template.json* pliku w programie Visual Studio Code) Aby zmienić `HostPort` wartość:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Po podłączeniu do modułów magazynu obiektów blob dodatkowe Zmiana punktu końcowego portu zaktualizowanego hosta.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).
