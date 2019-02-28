---
title: Store blokowych obiektów blob na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Wdróż moduł usługi Azure Blob Storage do urządzenia usługi IoT Edge do przechowywania danych na urządzeniach brzegowych.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ac40c4c974049f802da97846db5010652a420691
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961214"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage na urządzeniach brzegowych IoT Edge (wersja zapoznawcza)

Usługa Azure Blob Storage w usłudze IoT Edge zapewnia [blokowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) rozwiązanie do magazynowania na urządzeniach brzegowych. Moduł magazynu obiektów blob na urządzeniu usługi IoT Edge, który zachowuje się jak usługi Azure block blob, ale blokowe obiekty BLOB są przechowywane lokalnie na urządzeniu usługi IoT Edge. Możesz uzyskać dostęp przy użyciu tych samych metod zestawu SDK usługi Azure storage blob lub blokować wywołań interfejsu API obiektu blob, które zostały już one użyte do. 

Scenariusze, w którym danych, takich jak wideo, obrazów, dane finansowe, dane szpitali lub wszelkie dane, które muszą być przechowywane lokalnie, później, które mogą być przetwarzane lokalnie lub przeniesione do chmury to dobre przykłady, aby użyć tego modułu.

Ten artykuł zawiera instrukcje dotyczące wdrażania usługi Azure Blob Storage na kontenera usługi IoT Edge, w którym jest uruchomiona usługa blob na urządzeniu usługi IoT Edge. 

>[!NOTE]
>Usługa Azure Blob Storage w usłudze IoT Edge jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

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

   4. Wybierz pozycję **Zapisz**.

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

5. Zapisz plik **deployment.template.json**.

6. Otwórz **ENV** plik rozwiązania obszaru roboczego. 

7. Plik ENV jest skonfigurowany do odbierania poświadczeń rejestru kontenerów, ale nie ma potrzeby, obrazu magazynu obiektów blob, ponieważ jest on dostępny publicznie. Zamiast tego należy zamienić plik na dwa nowe zmienne środowiskowe: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Wprowadź wartość w polu `STORAGE_ACCOUNT_NAME`, nazwy konta powinny być 3 do 24 znaków, małe litery i cyfry. Podaj klucz 64 bajtów w formacie base64 dla `STORAGE_ACCOUNT_KEY`. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów. 

   Nie zawierają spacji ani znaków cudzysłowu wokół wartości, których udzielasz. 

9. Zapisz plik **ENV**. 

10. Kliknij prawym przyciskiem myszy **deployment.template.json** i wybierz **manifest wdrożenia usługi IoT Edge Generowanie**. 

11. Visual Studio Code przyjmuje informacje podane w deployment.template.json i ENV, a następnie używa jej do utworzenia nowego pliku manifestu wdrożenia. Manifest wdrożenia jest tworzony w nowym **config** folder w obszarze roboczym Twojego rozwiązania. Po utworzeniu tego pliku, możesz wykonać kroki w [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md) lub [modułów wdrożenia usługi Azure IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Łączenie usługi modułu magazynu obiektów blob

Można użyć nazwy konta i klucz konta, które skonfigurowano dla modułu dostępu do magazynu obiektów blob na urządzeniu usługi IoT Edge. 

Określ urządzenia usługi IoT Edge jako punkt końcowy obiektu blob do przechowywania dowolnego żądania, które można wprowadzać w nim. Możesz [utworzyć parametry połączenia dla punktu końcowego magazynu jawne](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu informacji o urządzeniu usługi IoT Edge i nazwę konta, które zostały skonfigurowane. 

1. Dla modułów, które zostały wdrożone na tym samym urządzeniu krawędzi, gdzie jest uruchomione "Azure Blob Storage na usługi IoT Edge" punkt końcowy obiektu blob jest: `http://<module name>:11002/<account name>`. 
2. Dla modułów, które zostały wdrożone na urządzenia edge innego, niż urządzenia edge, w których jest uruchomiony "Azure Blob Storage na usługi IoT Edge", a następnie w zależności od konfiguracji punktu końcowego obiektu blob jest: `http://<device IP >:11002/<account name>` lub `http://<IoT Edge device hostname>:11002/<account name>` lub `http://<FQDN>:11002/<account name>`

## <a name="logs"></a>Dzienniki

Dzienniki wewnątrz kontenera, można znaleźć w obszarze: 
* Dla systemu Linux: /blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>Wdrażanie wielu wystąpień

Jeśli chcesz wdrożyć wiele wystąpień usługi Azure Blob Storage w usłudze IoT Edge, wystarczy zmienić HostPort, który powiąże modułu. Moduły magazynu obiektów blob jest zawsze udostępniania portów 11002 w kontenerze, ale można zadeklarować port, który jest powiązany z na hoście. 

Edytuj moduł utworzyć opcje, aby zmienić wartość HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Po podłączeniu do modułów magazynu obiektów blob dodatkowe Zmiana punktu końcowego portu zaktualizowanego hosta. 

## <a name="try-it-out"></a>Wypróbuj

Dokumentacja usługi Azure Blob Storage zawiera Przewodniki Szybki Start, zapewniające przykładowego kodu w kilku językach. Możesz uruchomić te przykłady do testowania usługi Azure Blob Storage w usłudze IoT Edge, zmieniając punktu końcowego obiektu blob, aby wskazywał modułu magazynu obiektów blob.

Języki, które są również obsługiwane przez usługi IoT Edge, dzięki czemu można je wdrożyć jako moduły usługi IoT Edge wraz z modułu magazynu obiektów blob należy użyć następujących przewodników Szybki Start:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Obsługiwane operacje

Moduły magazynu obiektów blob na brzegowych urządzeniach IoT używać tych samych zestawów SDK usługi Azure Storage i są zgodne z interfejsu API usługi Azure Storage dla blokowych obiektów blob z punktów końcowych w wersji 2018-03-28. Nowszych są zależne od potrzeb klienta. 

Nie wszystkie operacje usługi Azure Blob Storage są obsługiwane przez usługi Azure Blob Storage w usłudze IoT Edge. W poniższych sekcjach szczegółowo, jakie operacje są nie są obsługiwane. 

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

Nieobsługiwane: 
* Pobieranie i ustawianie ACL kontenera
* Dzierżawa kontenera
* Metadane kontenera zestawu

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
* Umieść bloku:-bloku musi być mniejsza niż 4 MB rozmiar
* Umieszczanie i Uzyskaj listę zablokowanych

Nieobsługiwane:
* Umieść blok z adresu URL

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

