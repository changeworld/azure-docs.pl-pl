---
title: Store blokowych obiektów blob na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Wdróż moduł usługi Azure Blob Storage do urządzenia usługi IoT Edge do przechowywania danych na urządzeniach brzegowych.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3a0df408e70ed61355ffba319f6261f90d8e4348
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499162"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage na urządzeniach brzegowych IoT Edge (wersja zapoznawcza)

Usługa Azure Blob Storage w usłudze IoT Edge zapewnia [blokowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) rozwiązanie do magazynowania na urządzeniach brzegowych. Moduł magazynu obiektów blob na urządzeniu usługi IoT Edge, który zachowuje się jak usługi Azure block blob, ale blokowe obiekty BLOB są przechowywane lokalnie na urządzeniu usługi IoT Edge. Możesz uzyskać dostęp przy użyciu tych samych metod zestawu SDK usługi Azure storage blob lub blokować wywołań interfejsu API obiektu blob, które zostały już one użyte do. 

Ten moduł jest dostarczany z **warstw automatycznie** i **automatyczne wygaśnięcie** funkcji.

> [!NOTE]
> Obecnie warstw automatycznie i automatyczne wygaśnięcie funkcje są dostępne tylko w AMD64 systemu Linux i ARM32 systemu Linux.

**Obsługa warstw automatycznie** jest można skonfigurować funkcję, która pozwala na automatyczne przekazywanie danych z magazynu lokalnego obiektu blob na platformie Azure przy użyciu sporadyczne Obsługa połączenia internetowego. Umożliwia:
- Włączanie/wyłączenie funkcji obsługi warstw
- Wybierz kolejność, w którym dane zostaną skopiowane na platformę Azure, takich jak NewestFirst lub OldestFirst
- Określ konto usługi Azure Storage, do którego mają zostać przekazane dane.
- Określ kontenery, które chcesz przekazać na platformę Azure. Moduł ten umożliwia określenie nazwy kontenera elementy źródłowe i docelowe.
- Pełne warstw obiektów blob (przy użyciu `Put Blob` operacji) i blokowanie obsługi warstw na poziomie (przy użyciu `Put Block` i `Put Block List` operacji).

W tym module korzystamy blokowych poziomu warstw, gdy obiekt blob składa się z bloków. Poniżej przedstawiono niektóre typowe scenariusze:
- Aplikacja aktualizuje niektóre bloki wcześniej przekazanego obiektu blob, w tym module przekazać tylko zaktualizowane bloki, a nie cały obiekt blob.
- Moduł przekazywania obiektów blob i połączenie z Internetem znika, gdy połączenie jest ponownie ponownie przekaże pozostałe bloków i nie cały obiekt blob.

Jeśli po zakończeniu procesu nieoczekiwany (np. awarii zasilania) będzie się działo podczas przekazywania obiektów blob, wszystkie bloki, które zostały do przekazywania będzie można przekazać ponownie, gdy moduł powróci do trybu online.

**Automatyczne wygaśnięcie** jest można konfigurować funkcje, w którym ten moduł spowoduje automatyczne usunięcie obiektów blob z magazynu lokalnego po upływie czasu wygaśnięcia (TTL). Jest on mierzony w ciągu kilku minut. Umożliwia:
- Włączanie/wyłączenie funkcji automatycznej wygaśnięcia
- Określ czas wygaśnięcia w ciągu kilku minut

Scenariusze, w którym danych, takich jak wideo, obrazów, dane finansowe, dane szpitali lub wszelkie dane, które muszą być przechowywane lokalnie, później, mogą być przetwarzane lokalnie lub przeniesione do chmury to dobre przykłady, aby użyć tego modułu.

Ten artykuł zawiera instrukcje dotyczące wdrażania usługi Azure Blob Storage na kontenera usługi IoT Edge, w którym jest uruchomiona usługa blob na urządzeniu usługi IoT Edge. 

>[!NOTE]
>Usługa Azure Blob Storage w usłudze IoT Edge jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Obejrzyj film dotyczący szybkie wprowadzenie
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).
* Usługi Azure Blob Storage na moduł usługi IoT Edge obsługuje następujące konfiguracje urządzeń:

   | System operacyjny | Architektura |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Serwer Ubuntu 18.04 | AMD64 |
   | Windows 10 IoT Core (aktualizacja z października) | AMD64 |
   | Windows 10 IoT Enterprise (aktualizacja z października) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Wdrażanie usługi blob storage do Twojego urządzenia

Istnieje kilka sposobów, aby wdrożyć moduły na urządzeniu usługi IoT Edge, a wszystkie z nich pracy dla usługi Azure Blob Storage na moduły usługi IoT Edge. Te dwie metody najprostszym mają używać witryny Azure portal lub szablonów programu Visual Studio Code. 

### <a name="azure-portal"></a>Azure Portal

Portal Azure Marketplace udostępnia usługi IoT Edge, moduły, które można wdrażać bezpośrednio na urządzeniach usługi IoT Edge, w tym usługi Azure Blob Storage w usłudze IoT Edge. Wykonaj następujące kroki, aby wdrożyć moduł w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com), wyszukaj "Azure Blob Storage na usługi IoT Edge". I **wybierz** wynik wyszukiwania w witrynie Marketplace.

   ![Tworzenie modułu na podstawie wyszukiwania w portalu Marketplace](./media/how-to-store-data-blob/marketplace-module.png)

2. Wybierz urządzenie usługi IoT Edge, które ma odebrać ten moduł. Na stronie **Urządzenia docelowe dla modułu usługi IoT Edge** podaj następujące informacje:

   1. Wybierz **subskrypcji** zawierający korzystania z usługi IoT hub.

   2. Wybierz swoje **usługi IoT Hub**.

   3. Jeśli znasz swoje **nazwy urządzenia usługi IoT Edge**, wprowadź, w polu tekstowym. Możesz też wybrać opcję **Znajdź urządzenie**, aby wybrać urządzenie z listy urządzeń usługi IoT Edge w centrum IoT. 
   
   4. Wybierz pozycję **Utwórz**.

   Po wybraniu modułu usługi IoT Edge w witrynie Microsoft Azure Marketplace oraz wybraniu urządzenia usługi IoT Edge, które ma odebrać moduł, nastąpi przekierowanie do trzyetapowego kreatora, który ułatwia dokładne określenie sposobu wdrożenia modułu.

3. W **Dodawanie modułów** kroku kreatora zestawu modułów, zwróć uwagę, że **AzureBlobStorageonIoTEdge** moduł znajduje się już w obszarze **moduły wdrożeń**. 

2. Wybierz moduł magazynu obiektów blob z listy modułów wdrożenia, aby otworzyć szczegóły modułu. 

   ![Wybierz nazwę modułu, aby otworzyć szczegóły modułu](./media/how-to-store-data-blob/open-module-details.png)

3. Na **niestandardowe moduły usługi IoT Edge** strony, zaktualizuj usługi Azure Blob Storage na moduł usługi IoT Edge wykonując następujące kroki:

   1. Zmiany modułu **nazwa** są pisane małymi literami. Jeśli chcesz zmienić nazwę modułu lub użyj `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >Usługa Azure IoT Edge jest uwzględniana wielkość liter, wykonywać wywołania do modułów, gdy zestaw SDK usługi Storage domyślnie na małe litery. Aby upewnić się, że nie są przerwane połączenia w usłudze Azure Blob Storage na moduł usługi IoT Edge, nadaj jej nazwę małe litery. 

   2. Wartość domyślna **opcje tworzenia kontenera** powiązań portów, które wymaga kontenera, ale również należy dodać informacje o koncie magazynu i powiązania dla katalogu magazynu na urządzeniu. Zastąp kod JSON w portalu JSON poniżej:
    
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
   3. Zaktualizuj kod JSON, który został skopiowany z następującymi informacjami: 

      * Zastąp `<your storage account name>` o nazwie, który można zapamiętać. Nazwy konta powinny być 3 do 24 znaków, małe litery i cyfry.
      * Zastąp `<your storage account key>` kluczem base64 64 bajtów. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.
      * Zastąp `<storage directory bind>` w zależności od używanego systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych. Powiązania katalogu magazynu mapuje lokalizacji na urządzeniu, które należy podać lokalizację zestawu w module. 

         * Kontenery systemu Linux:  **\<ścieżka magazynu >: / blobroot**. Na przykład/srv/containerdata: / blobroot. Ewentualnie Moje woluminu: / blobroot. 
         * Kontenery Windows:  **\<ścieżka magazynu >: C: / BlobRoot**. Na przykład C: / ContainerData:C: / BlobRoot. Ewentualnie Moje — wolumin: C: / blobroot.
   
      > [!IMPORTANT]
      > Nie zmieniaj druga połowa katalog magazynu powiązać wartości, które wskazuje do określonej lokalizacji w module. Powiązania katalogu magazynu zawsze powinien kończyć się znakiem **: / blobroot** dla kontenerów systemu Linux i **: C: / BlobRoot** dla kontenerów Windows.

      ![Aktualizacja modułu kontenera tworzenie opcji — portal](./media/how-to-store-data-blob/edit-module.png)

   4. Ustaw [warstw automatycznie i automatyczne wygaśnięcie](#configure-auto-tiering-and-auto-expiration-via-azure-portal) w odpowiednich właściwości. Lista [warstw automatycznie](#auto-tiering-properties) i [automatyczne wygaśnięcie](#auto-expiration-properties) właściwości i ich możliwe wartości. 

   5. Wybierz pozycję **Zapisz**. 

4. Wybierz **Dalej**, aby przejść do następnego kroku w kreatorze.
5. W **określanie tras** kroku kreatora wybierz pozycję **dalej**.
6. W kroku **Przegląd wdrożenia** kreatora wybierz pozycję **Prześlij**.
7. Po przesłaniu wdrożenia, można wrócić do **usługi IoT Edge** strony Centrum IoT Hub. Wybierz urządzenie usługi IoT Edge docelowych przy użyciu wdrażania, aby otworzyć jego szczegóły. 
8. W szczegółowych informacji o urządzeniu, sprawdź, czy moduł magazynu obiektów blob znajduje się w obu **określonym we wdrożeniu** i **zgłoszona przez urządzenie**. Może upłynąć kilka minut w przypadku modułu do pracy na urządzeniu i następnie odsyłane do usługi IoT Hub. Odśwież stronę aby zobaczyć zaktualizowany stan. 

### <a name="visual-studio-code-templates"></a>Szablony programu Visual Studio Code

Usługa Azure IoT Edge zapewnia szablony programu Visual Studio Code, aby pomóc w tworzeniu rozwiązań brzegowych. Te kroki wymagają, że masz [programu Visual Studio Code](https://code.visualstudio.com/) zainstalowane na komputerze deweloperskim i skonfigurowano [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Wykonaj następujące kroki, aby utworzyć nowe rozwiązanie IoT Edge przy użyciu modułu magazynu obiektów blob, a następnie skonfiguruj manifestu wdrażania. 

1. Wybierz **widoku** > **polecenia palety**. 

2. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **istniejącego modułu (wprowadź pełny obrazu URL)**. |
   | Podaj nazwę modułu | Wprowadź nazwę wszystkie małe dla modułu, takie jak **usłudze Azure blob Storage**.<br><br>Należy używać małych liter nazwy usługi Azure Blob Storage na moduł usługi IoT Edge. Przy odwoływaniu się do modułów, IoT Edge jest rozróżniana wielkość liter, a zestaw SDK usługi Storage domyślnie na małe litery. |
   | Udostępnianie obrazu platformy Docker modułu | Podaj identyfikator URI obrazu: **mcr.microsoft.com/azure-blob-storage:latest** |

   Program VS Code przyjmuje informacje podane lub tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie. Szablon rozwiązania, tworzy szablon manifestu wdrożenia, który zawiera obraz modułu magazynu obiektów blob, ale należy skonfigurować opcje tworzenia modułu. 

3. Otwórz **deployment.template.json** nowy obszar roboczy rozwiązania i Znajdź **modułów** sekcji. Wprowadź następujące zmiany konfiguracji:

   1. Usuń **tempSensor** modułu, ponieważ nie użytkownika wymaganych dla tego wdrożenia. 

   2. Skopiuj i wklej następujący kod do **CreateOptions, można żądań** pola modułu magazynu obiektów blob: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Zaktualizuj moduł CreateOptions, można żądań — program VS Code](./media/how-to-store-data-blob/create-options.png)

4. W opcji tworzenia JSON, zaktualizuj `<storage directory bind>` w zależności od używanego systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych. Powiązania katalogu magazynu mapuje lokalizacji na urządzeniu, które należy podać lokalizację zestawu w module.  

   * Kontenery systemu Linux:  **\<ścieżka magazynu >: / blobroot**. Na przykład/srv/containerdata: / blobroot. Ewentualnie Moje woluminu: / blobroot.
   * Kontenery Windows:  **\<ścieżka magazynu >: C: / BlobRoot**. Na przykład C: / ContainerData:C: / BlobRoot. Ewentualnie Moje — wolumin: C: / blobroot.
   
   > [!IMPORTANT]
   > Nie zmieniaj druga połowa katalog magazynu powiązać wartości, które wskazuje do określonej lokalizacji w module. Powiązania katalogu magazynu zawsze powinien kończyć się znakiem **: / blobroot** dla kontenerów systemu Linux i **: C: / BlobRoot** dla kontenerów Windows.

5. Konfigurowanie [warstw automatycznie i automatyczne wygaśnięcie](#configure-auto-tiering-and-auto-expiration-via-vscode). Lista [warstw automatycznie](#auto-tiering-properties) i [automatyczne wygaśnięcie](#auto-expiration-properties) właściwości

6. Zapisz plik **deployment.template.json**.

7. Otwórz **ENV** plik rozwiązania obszaru roboczego. 

8. Plik ENV jest skonfigurowany do odbierania poświadczeń rejestru kontenerów, ale nie ma potrzeby, obrazu magazynu obiektów blob, ponieważ jest on dostępny publicznie. Zamiast tego należy zamienić plik na dwa nowe zmienne środowiskowe: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Wprowadź wartość w polu `STORAGE_ACCOUNT_NAME`, nazwy konta powinny być 3 do 24 znaków, małe litery i cyfry. Podaj klucz 64 bajtów w formacie base64 dla `STORAGE_ACCOUNT_KEY`. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów. 

   Nie zawierają spacji ani znaków cudzysłowu wokół wartości, których udzielasz. 

10. Zapisz plik **ENV**. 

11. Kliknij prawym przyciskiem myszy **deployment.template.json** i wybierz **manifest wdrożenia usługi IoT Edge Generowanie**. 

12. Visual Studio Code przyjmuje informacje podane w deployment.template.json i ENV, a następnie używa jej do utworzenia nowego pliku manifestu wdrożenia. Manifest wdrożenia jest tworzony w nowym **config** folder w obszarze roboczym Twojego rozwiązania. Po utworzeniu tego pliku, możesz wykonać kroki w [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md) lub [modułów wdrożenia usługi Azure IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>Obsługa warstw automatycznie właściwości automatycznego wygaśnięcia i konfiguracji

Użyj żądanych właściwości, aby ustawić właściwości automatycznego wygaśnięcia i warstw automatycznie. Można je ustawić podczas wdrażania lub zmienić później, edytując bliźniaczą reprezentację modułu bez konieczności ponownego wdrażania. Firma Microsoft zaleca sprawdzenie "Bliźniaczą reprezentację modułu" dla `reported configuration` i `configurationValidation` się upewnić, że wartości są poprawnie propagowane.

### <a name="auto-tiering-properties"></a>Właściwości warstwy automatycznie 
Nazwa tego ustawienia jest `tieringSettings`

| Pole | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| tieringOn | wartość true, false | Domyślnie jest ustawiona `false`, jeśli chcesz włączyć ją na wartość `true`|
| backlogPolicy | NewestFirst, OldestFirst | Pozwala wybrać kolejność, w którym dane zostaną skopiowane na platformę Azure. Domyślnie jest ustawiona `OldestFirst`. Kolejność jest określana przez godzinę ostatniej modyfikacji obiektu Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` Parametry połączenia, które można określić konto usługi Azure Storage, do której chcesz danych jest przekazywany. Określ `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Dodaj odpowiednie EndpointSuffix platformy Azure z którym dane zostaną przekazane, jego różni się w globalnej platformy Azure, Azure dla instytucji rządowych i Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umożliwia określenie nazwy kontenera, który chcesz przekazać na platformę Azure. Moduł ten umożliwia określenie nazwy kontenera elementy źródłowe i docelowe. Jeśli nie określisz nazwy kontenera docelowego, automatycznie przypisze nazwę kontenera jako `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Możesz utworzyć parametry szablonu dla nazwy kontenera docelowego, wyewidencjonowania kolumny możliwych wartości. <br>* %h -> nazwą Centrum IoT (3 – 50 znaków). <br>* %d -> urządzenia IoT identyfikator (1-129 znaków). <br>* %m -> Nazwa modułu (od 1 do 64 znaków). <br>* %c -> Nazwa kontenera danych źródłowych (od 3 do 63 znaków). <br><br>Maksymalny rozmiar nazwa kontenera jest 63 znaków podczas automatycznego przypisywania nazwy kontenera docelowego jeśli rozmiar kontenera przekracza 63 znaków, który go będzie ograniczać każdej sekcji (IoTHubName IotEdgeDeviceName, ModuleName, ContainerName) do 15 znaków. |

### <a name="auto-expiration-properties"></a>Właściwości automatyczne wygaśnięcia
Nazwa tego ustawienia jest `ttlSettings`

| Pole | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| ttlOn | wartość true, false | Domyślnie jest ustawiona `false`, jeśli chcesz włączyć ją na wartość `true`|
| timeToLiveInMinutes | `<minutes>` | Określ czas wygaśnięcia w ciągu kilku minut. Moduł automatycznie spowoduje usunięcie obiektów blob z magazynu lokalnego po wygaśnięciu czasu wygaśnięcia |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Konfigurowanie obsługi warstw automatycznie i automatyczne wygaśnięcie za pośrednictwem witryny Azure portal

Ustaw żądane właściwości, aby umożliwić obsługę warstw automatycznie i automatycznie wygaśnięcia można ustawić następujące wartości:

- **Podczas początkowego wdrożenia**: Skopiuj kod JSON w **żądane właściwości zestawu modułu bliźniaczej reprezentacji** pole. Konfigurowanie każdej właściwości przy użyciu odpowiedniej wartości, zapisz go i kontynuować wdrożenia.

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

  ![Ustawianie właściwości warstw automatycznie i automatycznie wygaśnięcia](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **Po wdrożeniu modułu za pomocą funkcji "Bliźniaczą reprezentację modułu tożsamości"**: Przejdź do "Bliźniaczą reprezentację tożsamości modułu" tego modułu, skopiuj dane JSON w obszarze właściwości żądanego, skonfiguruj każdą właściwość z odpowiednią wartością i Zapisz. W formacie Json "Bliźniaczą reprezentację modułu tożsamości" Upewnij się, że za każdym razem, gdy Dodaj lub zaktualizuj dowolny żądaną właściwość, `reported configuration` sekcji odzwierciedla wprowadzone zmiany, a `configurationValidation` sekcji zgłosi powodzenie operacji dla każdej właściwości.

   ```json 
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

   ```

![module_identity_twin ttl warstw +](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Konfigurowanie obsługi warstw automatycznie i wygaśnięcia automatycznie za pomocą programu VSCode

- **Podczas początkowego wdrożenia**: Dodaj poniższe JSON w swojej deployment.template.json do definiowania żądane właściwości dla tego modułu. Konfigurowanie każdej właściwości z odpowiednią wartością i zapisz go.

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

Oto przykład żądane właściwości dla tego modułu: ![ustaw żądane właściwości azureblobstorageoniotedge — program VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **Po wdrożeniu modułu za pomocą "Bliźniaczą reprezentację modułu"**: [Edytuj bliźniaczą reprezentację modułu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) tego modułu, skopiuj kod JSON w obszarze właściwości żądanego, skonfiguruj każdą właściwość z odpowiednią wartością i Zapisz. W formacie Json "Bliźniaczą reprezentację modułu" Upewnij się, że za każdym razem, gdy Dodaj lub zaktualizuj dowolny żądaną właściwość, `reported configuration` sekcji odzwierciedla wprowadzone zmiany, a `configurationValidation` sekcji zgłosi powodzenie operacji dla każdej właściwości.

   ```json 
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

   ```
## <a name="logs"></a>Dzienniki

Wykonaj poniższe instrukcje [skonfigurować dzienniki platformy docker dla modułów usługi IoT Edge](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Łączenie usługi modułu magazynu obiektów blob

Można użyć nazwy konta i klucz konta, które skonfigurowano dla modułu dostępu do magazynu obiektów blob na urządzeniu usługi IoT Edge. 

Określ urządzenia usługi IoT Edge jako punkt końcowy obiektu blob do przechowywania dowolnego żądania, które można wprowadzać w nim. Możesz [utworzyć parametry połączenia dla punktu końcowego magazynu jawne](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu informacji o urządzeniu usługi IoT Edge i nazwę konta, które zostały skonfigurowane. 

1. W przypadku modułów, które zostały wdrożone na tym samym urządzeniu krawędzi, gdzie jest uruchomione "Azure Blob Storage na usługi IoT Edge", jest punkt końcowy obiektu blob: `http://<module name>:11002/<account name>`. 
2. Dla modułów, które zostały wdrożone na urządzeniu krawędzi innego niż urządzenia edge, w których jest uruchomiony "Azure Blob Storage na usługi IoT Edge", a następnie w zależności od konfiguracji punktu końcowego obiektu blob jest: `http://<device IP >:11002/<account name>` lub `http://<IoT Edge device hostname>:11002/<account name>` lub `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Wdrażanie wielu wystąpień

Wdrażanie wielu wystąpień usługi Azure Blob Storage w usłudze IoT Edge, należy podać ścieżkę do innego magazynu i zmień HostPort, który powiąże modułu. Moduły magazynu obiektów blob jest zawsze udostępniania portów 11002 w kontenerze, ale można zadeklarować port, który jest powiązany z na hoście. 

Edytuj moduł utworzyć opcje, aby zmienić wartość HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Po podłączeniu do modułów magazynu obiektów blob dodatkowe Zmiana punktu końcowego portu zaktualizowanego hosta. 

## <a name="try-it-out"></a>Testowanie

### <a name="azure-blob-storage-quickstart-samples"></a>Przykłady usługi Azure Blob Storage Quickstart
Dokumentacja usługi Azure Blob Storage zawiera Przewodniki Szybki Start, zapewniające przykładowego kodu w kilku językach. Możesz uruchomić te przykłady do testowania usługi Azure Blob Storage w usłudze IoT Edge, zmieniając punktu końcowego obiektu blob, aby wskazywał modułu magazynu obiektów blob. Postępuj zgodnie z instrukcjami, aby [nawiązać połączenie z modułu magazynu obiektów blob](#connect-to-your-blob-storage-module)

Języki, które są również obsługiwane przez usługi IoT Edge, dzięki czemu można je wdrożyć jako moduły usługi IoT Edge wraz z modułu magazynu obiektów blob należy użyć następujących przewodników Szybki Start:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Można też spróbować "Eksploratora usługi Azure Storage" do łączenia się z kontem magazynu lokalnego. Współdziała ona z [Eksploratora usługi Azure Storage w wersji 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> Mogą wystąpić błędy, wykonując następujące czynności, takie jak dodawanie połączenia z kontem magazynu lokalnego lub tworzenia kontenerów na koncie magazynu lokalnego. Zignoruj ten komunikat i odświeżania. 

1. Pobieranie i instalowanie Eksploratora usługi Azure Storage
2. Łączenie z usługą Azure Storage przy użyciu parametrów połączenia
3. Podaj parametry połączenia: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Wykonaj czynności, aby połączyć.
5. Tworzenie kontenera w koncie magazynu lokalnego
6. Rozpocznij przekazywanie plików jako blokowe obiekty BLOB.
   > [!NOTE]
   > Usuń zaznaczenie pola wyboru do przekazywania ich jako stronicowe obiekty BLOB. Ten moduł nie obsługuje stronicowych obiektów blob. Wystąpi tego monitu podczas przekazywania plików, takich jak ISO, VHD, vhdx lub wszelkie duże pliki.

7. Można wybrać opcję połączenia konta magazynu platformy Azure, gdy przesyłasz dane. Zapewnia jeden widok dla konta magazynu lokalnego i konta magazynu platformy Azure

## <a name="supported-storage-operations"></a>Obsługiwane operacje

Moduły magazynu obiektów blob na brzegowych urządzeniach IoT używać tych samych zestawów SDK usługi Azure Storage i są zgodne z wersji 2017-04-17 interfejsu API usługi Azure Storage dla blokowych obiektów blob z punktów końcowych. Nowszych są zależne od potrzeb klienta.

Nie wszystkie operacje usługi Azure Blob Storage są obsługiwane przez usługi Azure Blob Storage w usłudze IoT Edge. W poniższej sekcji przedstawiono obsługiwane i nieobsługiwane operacje.

### <a name="account"></a>Konto

Obsługiwane: 
* Wyświetlanie listy kontenerów

Nieobsługiwane: 
* Pobieranie i ustawianie właściwości usługi obiektów blob
* Żądania wstępnego obiektów blob
* Pobieranie statystyk usługi obiektów blob
* Uzyskaj informacje o koncie

### <a name="containers"></a>Containers

Obsługiwane: 
* Tworzenie i usuwanie kontenera
* Pobierz kontener właściwości i metadanych
* Wyświetlanie listy obiektów blob
* Pobieranie i ustawianie ACL kontenera
* Metadane kontenera zestawu

Nieobsługiwane:
* Dzierżawa kontenera

### <a name="blobs"></a>Obiekty blob

Obsługiwane: 
* Umieść, pobieranie i usuwanie obiektu blob
* Pobieranie i ustawianie właściwości obiektu blob
* Pobieranie i ustawianie metadane obiektu blob

Nieobsługiwane: 
* Dzierżawienie obiektu blob
* Wykonywanie migawki obiektu blob
* Skopiuj i przerwać obiektu blob kopiowania
* Cofanie usunięcia obiektu blob
* Ustawianie warstwy obiektu blob

### <a name="block-blobs"></a>Obiekty BLOB typu Block

Obsługiwane: 
* Umieść bloku
* Umieszczanie i Uzyskaj listę zablokowanych

Nieobsługiwane:
* Umieść blok z adresu URL

## <a name="feedback"></a>Opinia:
Twoja opinia jest bardzo ważne dla nas się tego modułu i jego funkcje przydatne i łatwych w użyciu. Podziel się swoją opinię i Daj nam znać, jak możemy ulepszyć.

Może się skontaktowanie się z nami pod adresem absiotfeedback@microsoft.com 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

