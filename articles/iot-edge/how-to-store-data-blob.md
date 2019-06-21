---
title: Store blokowych obiektów blob na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zrozumieć funkcje obsługi warstw i czas wygaśnięcia, zobacz operacje magazynu obsługiwanych obiektów blob i nawiąż połączenie z kontem magazynu obiektów blob.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f70ca550f1688551abb94bb30ba4f76eb3c36404
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303963"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage na urządzeniach brzegowych IoT Edge (wersja zapoznawcza)

Usługa Azure Blob Storage w usłudze IoT Edge zapewnia [blokowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) rozwiązanie do magazynowania na urządzeniach brzegowych. Moduł magazynu obiektów blob na urządzeniu usługi IoT Edge, który zachowuje się jak usługi Azure block blob, ale blokowe obiekty BLOB są przechowywane lokalnie na urządzeniu usługi IoT Edge. Możesz uzyskać dostęp przy użyciu tych samych metod zestawu SDK usługi Azure storage blob lub blokować wywołań interfejsu API obiektu blob, które zostały już one użyte do.

Ten moduł jest dostarczany z **deviceToCloudUpload** i **deviceAutoDelete** funkcji.
> [!NOTE]
> Usługa Azure Blob Storage w usłudze IoT Edge jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Obejrzyj film dotyczący szybkie wprowadzenie
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** jest można skonfigurować funkcję, która pozwala na automatyczne przekazywanie danych z magazynu lokalnego obiektu blob na platformie Azure przy użyciu sporadyczne Obsługa połączenia internetowego. Umożliwia:

- Włączanie/wyłączenie funkcji deviceToCloudUpload.
- Wybierz kolejność, w którym dane są kopiowane do platformy Azure, takich jak NewestFirst lub OldestFirst.
- Określ konto usługi Azure Storage, do którego mają zostać przekazane dane.
- Określ kontenery, które chcesz przekazać na platformę Azure. Moduł ten umożliwia określenie nazwy kontenera elementy źródłowe i docelowe.
- Wybierz możliwość usunięcia obiektów blob, natychmiast po zakończeniu przekazywania do magazynu w chmurze
- Pełne przekazywania obiektów blob (przy użyciu `Put Blob` operacji) i blokuj przekazywanie poziomu (za pomocą `Put Block` i `Put Block List` operacji).

W tym module korzystamy przekazywania poziomu bloku, gdy obiekt blob składa się z bloków. Poniżej przedstawiono niektóre typowe scenariusze:

- Aplikacja aktualizuje niektóre bloki wcześniej przekazanego obiektu blob, w tym module przekazuje tylko zaktualizowane bloki, a nie cały obiekt blob.
- Moduł przekazywania obiektów blob i połączenie z Internetem znika, gdy połączenie jest ponownie ponownie przekazuje tylko bloki pozostałe a nie cały obiekt blob.

Jeśli po zakończeniu procesu nieoczekiwany (np. awarii zasilania) będzie się działo podczas przekazywania obiektów blob, wszystkie bloki, które zostały do przekazywania będzie można przekazać ponownie, gdy moduł powróci do trybu online.

**deviceAutoDelete** jest można konfigurować funkcje, w którym moduł spowoduje automatyczne usunięcie obiektów blob z magazynu lokalnego po upływie określonego czasu trwania (mierzony w minutach). Umożliwia:

- Włączanie/wyłączenie funkcji deviceAutoDelete.
- Określ czas w minutach (deleteAfterMinutes), po przekroczeniu której obiekty BLOB zostaną automatycznie usunięte.
- Wybrać można przechowywać obiektu blob podczas przekazywania, jeśli wartość deleteAfterMinutes wygaśnie.

Scenariusze, w którym danych, takich jak wideo, obrazów, dane finansowe, dane szpitali lub wszelkie dane, które muszą być przechowywane lokalnie, później, mogą być przetwarzane lokalnie lub przeniesione do chmury to dobre przykłady, aby użyć tego modułu.

W tym artykule opisano pojęcia związane z usługi Azure Blob Storage na kontenera usługi IoT Edge, w którym jest uruchomiona usługa blob na urządzeniu usługi IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

- Jako urządzenia usługi IoT Edge można użyć komputera deweloperskiego lub maszynie wirtualnej, wykonując kroki opisane w przewodniku Szybki Start dla [Linux](quickstart-linux.md) lub [urządzenia Windows](quickstart.md).

- Usługi Azure Blob Storage na moduł usługi IoT Edge obsługuje następujące konfiguracje urządzeń:

  | System operacyjny | Architektura |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Serwer Ubuntu 18.04 | AMD64 |
  | Windows 10 IoT Enterprise | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian stretch | ARM32 |

Zasoby w chmurze:

Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>właściwości deviceToCloudUpload i deviceAutoDelete

Umożliwia ustawianie deviceToCloudUploadProperties i deviceAutoDeleteProperties żądane właściwości. Można je ustawić podczas wdrażania lub zmienić później, edytując bliźniaczą reprezentację modułu bez konieczności ponownego wdrażania. Firma Microsoft zaleca sprawdzenie "Bliźniaczą reprezentację modułu" dla `reported configuration` i `configurationValidation` się upewnić, że wartości są poprawnie propagowane.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Nazwa tego ustawienia jest `deviceToCloudUploadProperties`

| Pole | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| uploadOn | true, false | Domyślnie jest ustawiona `false`, jeśli chcesz włączyć ją na wartość `true`|
| uploadOrder | NewestFirst, OldestFirst | Można wybrać kolejność, w którym dane są kopiowane do platformy Azure. Domyślnie jest ustawiona `OldestFirst`. Kolejność jest określana przez godzinę ostatniej modyfikacji obiektu Blob |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` Parametry połączenia, które można określić konto usługi Azure Storage, do której chcesz danych jest przekazywany. Określ `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Dodaj odpowiednie EndpointSuffix platformy Azure z którym dane zostaną przekazane, jego różni się w globalnej platformy Azure, Azure dla instytucji rządowych i Microsoft Azure Stack. |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umożliwia określenie nazwy kontenera, który chcesz przekazać na platformę Azure. Moduł ten umożliwia określenie nazwy kontenera elementy źródłowe i docelowe. Jeśli nie określisz nazwy kontenera docelowego, automatycznie przypisze nazwę kontenera jako `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Można utworzyć parametry szablonu dla nazwy kontenera docelowego, zapoznaj się z kolumny możliwych wartości. <br>* %h -> nazwą Centrum IoT (3 – 50 znaków). <br>* %d -> Identyfikator urządzenia usługi Edge IoT (1-129 znaków). <br>* %m -> Nazwa modułu (od 1 do 64 znaków). <br>* %c -> Nazwa kontenera danych źródłowych (od 3 do 63 znaków). <br><br>Maksymalny rozmiar nazwa kontenera jest 63 znaków podczas automatycznego przypisywania nazwy kontenera docelowego jeśli rozmiar kontenera przekracza 63 znaków, który go będzie ograniczać każdej sekcji (IoTHubName IotEdgeDeviceID, ModuleName, SourceContainerName) do 15 znaki. |
| deleteAfterUpload | true, false | Domyślnie jest ustawiona `false`. Jeśli jest równa `true`, usługa automatycznie usunie je po zakończeniu przekazywania do magazynu w chmurze |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Nazwa tego ustawienia jest `deviceAutoDeleteProperties`

| Pole | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| deleteOn | true, false | Domyślnie jest ustawiona `false`, jeśli chcesz włączyć ją na wartość `true`|
| deleteAfterMinutes | `<minutes>` | Określa wartość godziny w ciągu kilku minut. Moduł automatycznie spowoduje usunięcie obiektów blob z magazynu lokalnego po wygaśnięciu tej wartości |
| retainWhileUploading | true, false | Domyślnie jest ustawiona `true`, i zostaną zachowane obiektu blob, gdy jest przekazanie do magazynu w chmurze, jeśli deleteAfterMinutes wygaśnie. Możesz ustawić na `false` i spowoduje usunięcie danych, zaraz po wygaśnięciu deleteAfterMinutes. Uwaga: Dla tej właściwości do pracy uploadOn powinna być ustawiona na wartość true|

## <a name="configure-log-files"></a>Konfigurowanie plików dziennika

Aby uzyskać informacje na temat konfigurowania plików dziennika dla modułu, zobacz te [najlepsze rozwiązania w środowisku produkcyjnym](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Łączenie usługi modułu magazynu obiektów blob

Można użyć nazwy konta i klucz konta, które skonfigurowano dla modułu dostępu do magazynu obiektów blob na urządzeniu usługi IoT Edge.

Określ urządzenia usługi IoT Edge jako punkt końcowy obiektu blob do przechowywania dowolnego żądania, które można wprowadzać w nim. Możesz [utworzyć parametry połączenia dla punktu końcowego magazynu jawne](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu informacji o urządzeniu usługi IoT Edge i nazwę konta, które zostały skonfigurowane.

- Dla modułów, które zostały wdrożone na tym samym urządzeniu, co gdzie jest uruchomione usługi Azure Blob Storage na moduł usługi IoT Edge, punkt końcowy obiektu blob jest: `http://<module name>:11002/<account name>`.
- Dla zewnętrznych modułów lub aplikacje są uruchamiane na innym urządzeniu, od których usługi Azure Blob Storage na moduł usługi IoT Edge jest uruchomiona, a następnie w zależności od konfiguracji sieci, tak, aby ruch danych z modułu zewnętrznego lub aplikacji można nawiązać połączenia z urządzeniem Uruchamianie usługi Azure Blob Storage na moduł usługi IoT Edge, punkt końcowy obiektu blob jest jedną z:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Przykłady szybkiego startu usługi Azure Blob Storage

Dokumentacja usługi Azure Blob Storage zawiera przewodnik Szybki Start przykładowy kod w kilku językach. Możesz uruchomić te przykłady do testowania usługi Azure Blob Storage w usłudze IoT Edge, zmieniając punktu końcowego obiektu blob, połączyć się z modułu magazynu lokalnego obiektu blob.

Poniższe przykłady Szybki Start Użyj języki, które są również obsługiwane przez usługi IoT Edge, dzięki czemu można je wdrożyć jako moduły usługi IoT Edge wraz z modułu magazynu obiektów blob:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Nawiązać połączenie z magazynu lokalnego przy użyciu usługi Azure Storage Explorer

Możesz użyć [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) nawiązać połączenia z kontem magazynu lokalnego.

1. Pobieranie i instalowanie Eksploratora usługi Azure Storage

1. Łączenie z usługą Azure Storage przy użyciu parametrów połączenia

1. Podaj parametry połączenia: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Wykonaj czynności, aby połączyć.

1. Tworzenie kontenera w koncie magazynu lokalnego

1. Rozpocznij przekazywanie plików jako blokowe obiekty BLOB.
   > [!NOTE]
   > Ten moduł nie obsługuje stronicowych obiektów blob.

1. Można wybrać opcję połączenia konta magazynu platformy Azure, gdy przesyłasz dane. Zapewnia jeden widok dla konta magazynu lokalnego i konta magazynu platformy Azure

## <a name="supported-storage-operations"></a>Obsługiwane operacje

Moduły magazynu obiektów blob na brzegowych urządzeniach IoT używać tych samych zestawów SDK usługi Azure Storage i są zgodne z wersji 2017-04-17 interfejsu API usługi Azure Storage dla blokowych obiektów blob z punktów końcowych. Nowszych są zależne od potrzeb klienta.

Ponieważ nie wszystkie operacje usługi Azure Blob Storage są obsługiwane przez usługi Azure Blob Storage w usłudze IoT Edge, w tej sekcji przedstawiono stan każdego z nich.

### <a name="account"></a>Konto

Obsługiwane:

- Wyświetlanie listy kontenerów

Nieobsługiwane:

- Pobieranie i ustawianie właściwości usługi obiektów blob
- Żądania wstępnego obiektów blob
- Pobieranie statystyk usługi obiektów blob
- Uzyskaj informacje o koncie

### <a name="containers"></a>Containers

Obsługiwane:

- Tworzenie i usuwanie kontenera
- Pobierz kontener właściwości i metadanych
- Wyświetlanie listy obiektów blob
- Pobieranie i ustawianie ACL kontenera
- Metadane kontenera zestawu

Nieobsługiwane:

- Dzierżawa kontenera

### <a name="blobs"></a>Obiekty blob

Obsługiwane:

- Umieść, pobieranie i usuwanie obiektu blob
- Pobieranie i ustawianie właściwości obiektu blob
- Pobieranie i ustawianie metadane obiektu blob

Nieobsługiwane:

- Dzierżawienie obiektu blob
- Wykonywanie migawki obiektu blob
- Skopiuj i przerwać obiektu blob kopiowania
- Cofanie usunięcia obiektu blob
- Ustawianie warstwy obiektu blob

### <a name="block-blobs"></a>Obiekty BLOB typu Block

Obsługiwane:

- Umieść bloku
- Umieszczanie i Uzyskaj listę zablokowanych

Nieobsługiwane:

- Umieść blok z adresu URL

## <a name="release-notes"></a>Informacje o wersji

Poniżej przedstawiono [release notes w usłudze docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) dla tego modułu

## <a name="feedback"></a>Opinia

Twoja opinia jest ważna do nam tego modułu i jego funkcje przydatne i łatwych w użyciu. Podziel się swoją opinię i Daj nam znać, jak możemy ulepszyć.

Możesz docierać do nas pod numer absiotfeedback@microsoft.com

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wdrożenia usługi Azure Blob Storage na brzegowych urządzeniach IoT](how-to-deploy-blob.md)
