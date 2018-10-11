---
title: Usługa Azure Blob Storage na urządzeniach z usługą Azure IoT Edge | Dokumentacja firmy Microsoft
description: Wdróż moduł usługi Azure Blob Storage do urządzenia usługi IoT Edge do przechowywania danych na urządzeniach brzegowych.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 10/03/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6094236269df881eac6f8cd2fd04183dd9d6df3b
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068761"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage na urządzeniach brzegowych IoT Edge (wersja zapoznawcza)

Azure magazyn obiektów blob w usłudze IoT Edge zapewnia rozwiązanie magazynu blokowych obiektów blob na urządzeniach brzegowych. Moduł magazynu obiektów blob na urządzeniu usługi IoT Edge, który zachowuje się jak usługi obiektów blob platformy Azure, ale blokowe obiekty BLOB są przechowywane lokalnie na urządzeniu usługi IoT Edge. Możesz uzyskać dostęp przy użyciu tych samych metod zestawu SDK usługi Azure storage blob lub blokować wywołań interfejsu API obiektu blob, które zostały już one użyte do. 

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
   | System Windows Server 2019 r | AMD64 |
   | Raspbian Stretch | ARM32 |

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Wdrażanie usługi blob storage do Twojego urządzenia

Usługa Azure Blob Storage w usłudze IoT Edge zapewnia trzy obrazy standardowy kontener, dwa kontenery systemu Linux (architekturach AMD64 oraz ARM32) i jeden dla kontenerów Windows (AMD64). Korzystając z jednej z tych obrazów modułu do wdrażania magazynu obiektów blob na urządzeniu usługi IoT Edge, podaj są trzy informacje, które można skonfigurować wystąpienia modułu dla urządzenia:

* **Nazwa konta** i **klucz konta**. Aby pozostać zgodne z usługą Azure Storage, moduły magazynu obiektów blob Użyj nazwy konta i klucze konta do zarządzania dostępem. Nazwy konta powinny być 3 do 24 znaków, małe litery i cyfry. Klucze konta powinna być zakodowane w formacie base64, jak i 64 bajtów długości. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64).
* A **opcję lokalnego magazynu**. Moduł magazynu obiektów blob przechowuje obiektów blob lokalnie na urządzeniu usługi IoT Edge, aby utrwalić obiektów blob, jeśli moduł zatrzymuje się lub ponowne uruchomienie. Zadeklaruj istniejące [woluminu](https://docs.docker.com/storage/volumes
) lub ścieżka folderu lokalnego przechowywania obiektów blob na urządzeniu. 

Istnieje kilka sposobów, aby wdrożyć moduły na urządzeniu usługi IoT Edge, a wszystkie z nich pracy dla usługi Azure Blob Storage na moduły usługi IoT Edge. Te dwie metody najprostszym mają używać witryny Azure portal lub szablonów programu Visual Studio Code. 

### <a name="azure-portal"></a>Azure Portal

Aby wdrożyć usługi blob storage za pośrednictwem witryny Azure portal, wykonaj kroki opisane w [modułów wdrożenia usługi Azure IoT Edge w witrynie Azure portal](how-to-deploy-modules-portal.md). Przed przejdź do wdrażania modułu, skopiuj identyfikator URI obrazu i przygotowanie kontenera Utwórz opcji w zależności od używanego systemu operacyjnego kontenera. Użyć tych wartości w **skonfigurować manifest wdrożenia** w dalszej części artykułu wdrożenia. 

Podaj identyfikator URI obrazu dla modułu magazynu obiektów blob: **mcr.microsoft.com/azure-blob-storage:latest**. 
   
Użyj następującego szablonu JSON dla **opcje tworzenia kontenera** pola. Skonfiguruj za pomocą pliku JSON z nazwy konta magazynu, klucz konta magazynu i powiązania katalogu magazynu.  
   
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
   
W opcji tworzenia JSON, zaktualizuj `\<your storage account name\>` o dowolnej nazwie. Aktualizacja `\<your storage account key\>` kluczem base64 64 bajtów. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64) umożliwia wybierz długość w bajtach. Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów.

W opcji tworzenia JSON, zaktualizuj `<storage directory bind>` w zależności od używanego systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych.  

   * Kontenery systemu Linux:  **\<ścieżka magazynu >: / blobroot**. Na przykład/srv/containerdata: / blobroot. Ewentualnie Moje woluminu: / blobroot. 
   * Kontenery Windows:  **\<ścieżka magazynu >: C: / BlobRoot**. Na przykład C: / ContainerData:C: / BlobRoot. Ewentualnie Moje — wolumin: C: / blobroot.
   
   > [!CAUTION]
   > Nie zmieniaj "/ blobroot" dla systemów Linux i "C:/BlobRoot" dla Windows, aby uzyskać  **\<powiązania katalogu magazynu >** wartości.

Nie musisz podać poświadczenia rejestru, aby dostęp do usługi Azure Blob Storage w usłudze IoT Edge, a nie trzeba deklarować wszystkie trasy dla danego wdrożenia. 

### <a name="visual-studio-code-templates"></a>Szablony programu Visual Studio Code

Usługa Azure IoT Edge zapewnia szablony programu Visual Studio Code, aby pomóc w tworzeniu rozwiązań brzegowych. Te kroki wymagają, że masz [programu Visual Studio Code](https://code.visualstudio.com/) zainstalowane na komputerze deweloperskim i skonfigurowano [rozszerzenia usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Wykonaj następujące kroki, aby utworzyć nowe rozwiązanie IoT Edge przy użyciu modułu magazynu obiektów blob, a następnie skonfiguruj manifestu wdrażania. 

1. Wybierz **widoku** > **polecenia palety**. 

2. W palecie poleceń, należy wprowadzić, a następnie uruchom polecenie **usługi Azure IoT Edge: nowe rozwiązanie graniczne IoT**. 

3. Postępuj zgodnie z monitami, aby utworzyć nowe rozwiązanie: 

   1. **Wybierz Folder** — przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie.  
   
   2. **Podaj nazwę rozwiązania** — wprowadź nazwę dla swojego rozwiązania, lub zaakceptuj nazwę domyślną.
   
   3. **Szablon wybrany moduł** — wybierz **istniejącego modułu (wprowadź pełny obrazu URL)**.
   
   4. **Podaj nazwę modułu** -wprowadź rozpoznawalną nazwę modułu, takie jak **usłudze Azure blob Storage**.
   
   5. **Udostępnianie obrazu platformy Docker modułu** — Podaj identyfikator URI obrazu: **mcr.microsoft.com/azure-blob-storage:latest**

Program VS Code przyjmuje informacje podane lub tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie. 

Szablon rozwiązania, tworzy szablon manifestu wdrożenia, który zawiera obraz modułu magazynu obiektów blob, ale należy skonfigurować opcje tworzenia modułu. 

1. Otwórz **deployment.template.json** nowy obszar roboczy rozwiązania i Znajdź **modułów** sekcji. 

2. Usuń **tempSensor** modułu, ponieważ nie użytkownika wymaganych dla tego wdrożenia. 

3. Skopiuj i wklej następujący kod do **CreateOptions, można żądań** pola modułu magazynu obiektów blob: 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![Opcje tworzenia modułu aktualizacji](./media/how-to-store-data-blob/create-options.png)

4. W opcji tworzenia JSON, zaktualizuj `<storage directory bind>` w zależności od używanego systemu operacyjnego kontenera. Podaj nazwę [woluminu](https://docs.docker.com/storage/volumes/) bramy lub bezwzględna do katalogu na urządzeniu usługi IoT Edge, którego moduł obiektu blob do przechowywania swoich danych.  

   * Kontenery systemu Linux:  **\<ścieżka magazynu >: / blobroot**. Na przykład/srv/containerdata: / blobroot. Ewentualnie Moje woluminu: / blobroot.
   * Kontenery Windows:  **\<ścieżka magazynu >: C: / BlobRoot**. Na przykład C: / ContainerData:C: / BlobRoot. Ewentualnie Moje — wolumin: C: / blobroot.
   
   > [!CAUTION]
   > Nie zmieniaj "/ blobroot" dla systemów Linux i "C:/BlobRoot" dla Windows, aby uzyskać  **\<powiązania katalogu magazynu >** wartości.

5. Zapisz **deployment.template.json**.

6. Otwórz **ENV** w obszarze roboczym rozwiązania. 

7. Nie masz wprowadzać żadnych wartości rejestru kontenerów przez obraz magazynu obiektów blob, ponieważ jest on dostępny publicznie. Zamiast tego należy dodać dwa nowe zmienne środowiskowe: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Podaj dowolnej nazwy dla nazwy konta magazynu, a następnie podaj klucz 64 bajtów w formacie base64 dla klucza konta magazynu. Możesz wygenerować klucz za pomocą narzędzi, takich jak [GeneratePlus](https://generate.plus/en/base64). Użyjesz tych poświadczeń do uzyskania dostępu do magazynu obiektów blob z innych modułów. 

9. Zapisz **ENV**. 

10. Kliknij prawym przyciskiem myszy **deployment.template.json** i wybierz **manifest wdrożenia usługi IoT Edge Generowanie**. 

Visual Studio Code przyjmuje informacje podane w deployment.template.json i ENV, a następnie używa jej do utworzenia nowego pliku manifestu wdrożenia. Manifest wdrożenia jest tworzony w nowym **config** folder w obszarze roboczym Twojego rozwiązania. Po utworzeniu tego pliku, możesz wykonać kroki w [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md) lub [modułów wdrożenia usługi Azure IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Łączenie usługi modułu magazynu obiektów blob

Można użyć nazwy konta i klucz konta, które skonfigurowano dla modułu dostępu do magazynu obiektów blob na urządzeniu usługi IoT Edge. 

Określ urządzenia usługi IoT Edge jako punkt końcowy obiektu blob do przechowywania dowolnego żądania, które można wprowadzać w nim. Możesz [utworzyć parametry połączenia dla punktu końcowego magazynu jawne](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu informacji o urządzeniu usługi IoT Edge i nazwę konta, które zostały skonfigurowane. 

1. Dla modułów, które zostały wdrożone na tym samym urządzeniu krawędzi, gdzie jest uruchomione "Azure Blob Storage na usługi IoT Edge" punkt końcowy obiektu blob jest: `http://<Module Name>:11002/<account name>`. 
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

### <a name="try-it-out"></a>Wypróbuj

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
* Umieść bloku
* Umieszczanie i Uzyskaj listę zablokowanych

Nieobsługiwane:
* Umieść blok z adresu URL

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

