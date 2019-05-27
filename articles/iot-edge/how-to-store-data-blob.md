---
title: Store blokowych obiektów blob na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zrozumieć funkcje obsługi warstw i czas wygaśnięcia, zobacz operacje magazynu obsługiwanych obiektów blob i nawiąż połączenie z kontem magazynu obiektów blob.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991467"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage na urządzeniach brzegowych IoT Edge (wersja zapoznawcza)

Usługa Azure Blob Storage w usłudze IoT Edge zapewnia [blokowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) rozwiązanie do magazynowania na urządzeniach brzegowych. Moduł magazynu obiektów blob na urządzeniu usługi IoT Edge, który zachowuje się jak usługi Azure block blob, ale blokowe obiekty BLOB są przechowywane lokalnie na urządzeniu usługi IoT Edge. Możesz uzyskać dostęp przy użyciu tych samych metod zestawu SDK usługi Azure storage blob lub blokować wywołań interfejsu API obiektu blob, które zostały już one użyte do.

> [!NOTE]
> Usługa Azure Blob Storage w usłudze IoT Edge jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten moduł jest dostarczany z **warstw** i **time-to-live** funkcji.

> [!NOTE]
> Obecnie warstw i czas wygaśnięcia funkcje są dostępne tylko w AMD64 systemu Linux i ARM32 systemu Linux.

**Obsługa warstw** jest można skonfigurować funkcję, która pozwala na automatyczne przekazywanie danych z magazynu lokalnego obiektu blob na platformie Azure przy użyciu sporadyczne Obsługa połączenia internetowego. Umożliwia:

- Włączanie/wyłączenie funkcji obsługi warstw
- Wybierz kolejność, w którym dane są kopiowane do platformy Azure, takich jak NewestFirst lub OldestFirst
- Określ konto usługi Azure Storage, do którego mają zostać przekazane dane.
- Określ kontenery, które chcesz przekazać na platformę Azure. Moduł ten umożliwia określenie nazwy kontenera elementy źródłowe i docelowe.
- Pełne warstw obiektów blob (przy użyciu `Put Blob` operacji) i blokowanie obsługi warstw na poziomie (przy użyciu `Put Block` i `Put Block List` operacji).

W tym module korzystamy blokowych poziomu warstw, gdy obiekt blob składa się z bloków. Poniżej przedstawiono niektóre typowe scenariusze:

- Aplikacja aktualizuje niektóre bloki wcześniej przekazanego obiektu blob, w tym module przekazuje tylko zaktualizowane bloki, a nie cały obiekt blob.
- Moduł przekazywania obiektów blob i połączenie z Internetem znika, gdy połączenie jest ponownie ponownie przekazuje tylko bloki pozostałe a nie cały obiekt blob.

Jeśli po zakończeniu procesu nieoczekiwany (np. awarii zasilania) będzie się działo podczas przekazywania obiektów blob, wszystkie bloki, które zostały do przekazywania będzie można przekazać ponownie, gdy moduł powróci do trybu online.

**Czas wygaśnięcia** (czasu wygaśnięcia TTL) jest można konfigurować funkcje, gdzie moduł spowoduje automatyczne usunięcie obiektów blob z magazynu lokalnego po upływie określonego czasu trwania (mierzony w minutach). Czas wygaśnięcia można:

- Włączanie/wyłączenie funkcji obsługi warstw
- Określ czas wygaśnięcia w ciągu kilku minut

Scenariusze, w którym danych, takich jak wideo, obrazów, dane finansowe, dane szpitali lub wszelkie dane, które muszą być przechowywane lokalnie, później, mogą być przetwarzane lokalnie lub przeniesione do chmury to dobre przykłady, aby użyć tego modułu.

Ten artykuł zawiera instrukcje dotyczące wdrażania usługi Azure Blob Storage na kontenera usługi IoT Edge, w którym jest uruchomiona usługa blob na urządzeniu usługi IoT Edge.

> [!NOTE]
> Warunki "auto warstw" i "auto wygaśnięcia" używany w wideo zostały zastąpione przez "warstw" i "time-to-live".

Obejrzyj film dotyczący szybkie wprowadzenie
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

- Jako urządzenia usługi IoT Edge można użyć komputera deweloperskiego lub maszynie wirtualnej, wykonując kroki opisane w przewodniku Szybki Start dla [Linux](quickstart-linux.md) lub [urządzenia Windows](quickstart.md).

- Usługi Azure Blob Storage na moduł usługi IoT Edge obsługuje następujące konfiguracje urządzeń:

  | System operacyjny | Architektura |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Serwer Ubuntu 18.04 | AMD64 |
  | Windows 10 IoT Core (aktualizacja z października) | AMD64 |
  | Windows 10 IoT Enterprise (aktualizacja z października) | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian stretch | ARM32 |

Zasoby w chmurze:

Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure.

## <a name="tiering-and-time-to-live-properties"></a>Właściwości warstw i czas wygaśnięcia

Użyj czasu wygaśnięcia właściwości i żądane właściwości można ustawić warstw. Można je ustawić podczas wdrażania lub zmienić później, edytując bliźniaczą reprezentację modułu bez konieczności ponownego wdrażania. Firma Microsoft zaleca sprawdzenie "Bliźniaczą reprezentację modułu" dla `reported configuration` i `configurationValidation` się upewnić, że wartości są poprawnie propagowane.

### <a name="tiering-properties"></a>Właściwości warstw

Nazwa tego ustawienia jest `tieringSettings`

| Pole | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| tieringOn | wartość true, false | Domyślnie jest ustawiona `false`, jeśli chcesz włączyć ją na wartość `true`|
| backlogPolicy | NewestFirst, OldestFirst | Można wybrać kolejność, w którym dane są kopiowane do platformy Azure. Domyślnie jest ustawiona `OldestFirst`. Kolejność jest określana przez godzinę ostatniej modyfikacji obiektu Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` Parametry połączenia, które można określić konto usługi Azure Storage, do której chcesz danych jest przekazywany. Określ `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Dodaj odpowiednie EndpointSuffix platformy Azure z którym dane zostaną przekazane, jego różni się w globalnej platformy Azure, Azure dla instytucji rządowych i Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umożliwia określenie nazwy kontenera, który chcesz przekazać na platformę Azure. Moduł ten umożliwia określenie nazwy kontenera elementy źródłowe i docelowe. Jeśli nie określisz nazwy kontenera docelowego, automatycznie przypisze nazwę kontenera jako `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Można utworzyć parametry szablonu dla nazwy kontenera docelowego, zapoznaj się z kolumny możliwych wartości. <br>* %h -> nazwą Centrum IoT (3 – 50 znaków). <br>* %d -> Identyfikator urządzenia IoT (1-129 znaków). <br>* %m -> Nazwa modułu (od 1 do 64 znaków). <br>* %c -> Nazwa kontenera danych źródłowych (od 3 do 63 znaków). <br><br>Maksymalny rozmiar nazwa kontenera jest 63 znaków podczas automatycznego przypisywania nazwy kontenera docelowego jeśli rozmiar kontenera przekracza 63 znaków, który go będzie ograniczać każdej sekcji (IoTHubName IotEdgeDeviceName, ModuleName, ContainerName) do 15 znaków. |

### <a name="time-to-live-properties"></a>Właściwości czasu wygaśnięcia

Nazwa tego ustawienia jest `ttlSettings`

| Pole | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| ttlOn | wartość true, false | Domyślnie jest ustawiona `false`, jeśli chcesz włączyć ją na wartość `true`|
| timeToLiveInMinutes | `<minutes>` | Określ czas wygaśnięcia w ciągu kilku minut. Moduł automatycznie spowoduje usunięcie obiektów blob z magazynu lokalnego po wygaśnięciu czasu wygaśnięcia |

## <a name="configure-log-files"></a>Konfigurowanie plików dziennika

Aby uzyskać informacje na temat konfigurowania plików dziennika dla modułu, zobacz te [najlepsze rozwiązania w środowisku produkcyjnym](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Łączenie usługi modułu magazynu obiektów blob

Można użyć nazwy konta i klucz konta, które skonfigurowano dla modułu dostępu do magazynu obiektów blob na urządzeniu usługi IoT Edge.

Określ urządzenia usługi IoT Edge jako punkt końcowy obiektu blob do przechowywania dowolnego żądania, które można wprowadzać w nim. Możesz [utworzyć parametry połączenia dla punktu końcowego magazynu jawne](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu informacji o urządzeniu usługi IoT Edge i nazwę konta, które zostały skonfigurowane.

- Dla modułów, które zostały wdrożone na tym samym urządzeniu, co gdzie jest uruchomione usługi Azure Blob Storage na moduł usługi IoT Edge, punkt końcowy obiektu blob jest: `http://<module name>:11002/<account name>`.
- Punkt końcowy dla modułów, które są wdrażane na innym urządzeniu, od których usługi Azure Blob Storage na moduł usługi IoT Edge jest uruchomiona, a następnie w zależności od konfiguracji obiekt blob jest jedną z:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Przykłady szybkiego startu usługi Azure Blob Storage

Dokumentacja usługi Azure Blob Storage zawiera Przewodniki Szybki Start, zapewniające przykładowego kodu w kilku językach. Możesz uruchomić te przykłady do testowania usługi Azure Blob Storage w usłudze IoT Edge, zmieniając punkt końcowy obiektu blob, połączyć się z modułu magazynu obiektów blob.

Języki, które są również obsługiwane przez usługi IoT Edge, dzięki czemu można je wdrożyć jako moduły usługi IoT Edge wraz z modułu magazynu obiektów blob należy użyć następujących przewodników Szybki Start:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Nawiązać połączenie z magazynu lokalnego przy użyciu usługi Azure Storage Explorer

Możesz użyć **Eksploratora usługi Azure Storage** nawiązać połączenia z kontem magazynu lokalnego. Ta opcja jest dostępna tylko w przypadku [Eksploratora usługi Azure Storage w wersji 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> Mogą wystąpić błędy, wykonując następujące czynności, takie jak dodawanie połączenia z kontem magazynu lokalnego lub tworzenia kontenerów na koncie magazynu lokalnego. Zignoruj ten komunikat i odświeżania.

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
- Wyświetl listę obiektów blob
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

## <a name="feedback"></a>Opinie

Twoja opinia jest ważna do nam tego modułu i jego funkcje przydatne i łatwych w użyciu. Podziel się swoją opinię i Daj nam znać, jak możemy ulepszyć.

Możesz docierać do nas pod numer absiotfeedback@microsoft.com

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wdrożenia usługi Azure Blob Storage na brzegowych urządzeniach IoT](how-to-deploy-blob.md)
