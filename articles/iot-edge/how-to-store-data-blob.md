---
title: Store blokowych obiektów blob na urządzeniach — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Informacje o warstwach i funkcjach typu Time-to-Live można znaleźć w temacie obsługiwane operacje usługi BLOB Storage i nawiązać połączenie z kontem usługi BLOB Storage.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c5a27a8016202f7f8c9e256eaf6b3077fbef295b
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414517"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage na urządzeniach brzegowych IoT Edge (wersja zapoznawcza)

Usługa Azure Blob Storage w usłudze IoT Edge zapewnia [blokowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) rozwiązanie do magazynowania na urządzeniach brzegowych. Moduł magazynu obiektów BLOB na urządzeniu IoT Edge zachowuje się jak usługa blokowych obiektów blob platformy Azure, z tą różnicą, że blokowe obiekty blob są przechowywane lokalnie na urządzeniu IoT Edge. Możesz uzyskać dostęp przy użyciu tych samych metod zestawu SDK usługi Azure storage blob lub blokować wywołań interfejsu API obiektu blob, które zostały już one użyte do. W tym artykule wyjaśniono koncepcje związane z Blob Storage platformy Azure na IoT Edge kontenera, w którym jest uruchomiona usługa BLOB na urządzeniu IoT Edge.

Ten moduł jest użyteczny w scenariuszach, w których dane muszą być przechowywane lokalnie, dopóki nie będzie można ich przetworzyć lub przenieść do chmury. Mogą to być dane wideo, obrazy, dane finansowe, dane szpitalne lub inne dane bez struktury.

> [!NOTE]
> Usługa Azure Blob Storage w usłudze IoT Edge jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Obejrzyj film wideo, aby uzyskać szybkie wprowadzenie
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

Ten moduł zawiera funkcje **deviceToCloudUpload** i **deviceAutoDelete** .

**deviceToCloudUpload** jest konfigurowalną funkcją. Ta funkcja automatycznie przekazuje dane z lokalnego magazynu obiektów BLOB na platformę Azure, używając sporadycznej obsługi łączności z Internetem. Umożliwia to:

- Włącz/Wyłącz funkcję deviceToCloudUpload.
- Wybierz kolejność kopiowania danych na platformę Azure, np. NewestFirst lub OldestFirst.
- Określ konto usługi Azure Storage, do którego chcesz przekazać dane.
- Określ kontenery, które chcesz przekazać do platformy Azure. Ten moduł pozwala określić źródłową i docelową nazwę kontenera.
- Wybierz możliwość natychmiastowego usunięcia obiektów BLOB po zakończeniu przekazywania do magazynu w chmurze
- Wykonaj pełne przekazywanie obiektów BLOB ( `Put Blob` przy użyciu operacji) i przekazywanie na poziomie `Put Block` bloku `Put Block List` (przy użyciu i operacji).

Ten moduł używa przekazywania na poziomie bloku, gdy obiekt BLOB zawiera bloki. Poniżej przedstawiono niektóre typowe scenariusze:

- Aplikacja aktualizuje niektóre bloki wcześniej przekazanego obiektu BLOB, ten moduł przekazuje tylko zaktualizowane bloki, a nie cały obiekt BLOB.
- Moduł przekazuje obiekt BLOB i połączenie internetowe odchodzi, gdy połączenie zostanie ponownie przekazane, a następnie naładuje tylko pozostałe bloki, a nie cały obiekt BLOB.

Jeśli nieoczekiwane zakończenie procesu (na przykład awaria napięcia) wystąpi podczas przekazywania obiektu BLOB, wszystkie bloki, które były spowodowane przekazaniem, zostaną przekazane ponownie po powrocie modułu do trybu online.

**deviceAutoDelete** jest konfigurowalną funkcją. Ta funkcja automatycznie usuwa obiekty blob z magazynu lokalnego, gdy upłynie określony czas (mierzony w minutach). Umożliwia to:

- Włącz/Wyłącz funkcję deviceAutoDelete.
- Określ czas (w minutach) (deleteAfterMinutes), po którym obiekty blob zostaną automatycznie usunięte.
- Wybierz możliwość zachowania obiektu BLOB podczas przekazywania, gdy wartość deleteAfterMinutes wygaśnie.


## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

- Możesz użyć maszyny deweloperskiej lub maszyny wirtualnej jako urządzenia IoT Edge, wykonując czynności opisane w przewodniku szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

- Usługi Azure Blob Storage na moduł usługi IoT Edge obsługuje następujące konfiguracje urządzeń:

  | System operacyjny | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian stretch | Nie | Yes | Nie |  
  | Ubuntu Server 16.04 | Yes | Nie | Tak |
  | Serwer Ubuntu 18.04 | Yes | Nie | Yes |
  | Windows 10 IoT Enterprise, kompilacja 17763 | Tak | Nie | Nie |
  | Windows Server 2019, build 17763 | Yes | Nie | Nie |
  

Zasoby w chmurze:

Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Właściwości deviceToCloudUpload i deviceAutoDelete

Użyj odpowiednich właściwości modułu, aby ustawić **deviceToCloudUploadProperties** i **deviceAutoDeleteProperties**. Żądane właściwości można ustawić podczas wdrażania lub zmienić je później, edytując sznurek modułu bez konieczności ponownego wdrażania. Zalecamy sprawdzenie "sznurka modułu" dla `reported configuration` i, `configurationValidation` aby upewnić się, że wartości są prawidłowo propagowane.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Nazwa tego ustawienia to`deviceToCloudUploadProperties`

| Pole | Możliwe wartości | Wyjaśnienie | Zmienna środowiskowa |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | `false` Domyślnie ustawiona wartość. Jeśli chcesz włączyć tę funkcję, ustaw to pole na `true`. | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Umożliwia wybranie kolejności, w której dane są kopiowane na platformę Azure. `OldestFirst` Domyślnie ustawiona wartość. Kolejność jest określana według czasu ostatniej modyfikacji obiektu BLOB | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`to parametry połączenia, które umożliwiają określenie konta usługi Azure Storage, do którego chcesz przekazać dane. Określ `Azure Storage Account Name`, `Azure Storage Account Key`, .`End point suffix` Dodaj odpowiednie EndpointSuffix systemu Azure, w przypadku których dane zostaną przekazane, różnią się w zależności od globalnego platformy Azure, platformy Azure dla instytucji rządowych i Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Pozwala określić nazwy kontenerów, które mają zostać przekazane na platformę Azure. Ten moduł pozwala określić źródłową i docelową nazwę kontenera. Jeśli nie określisz nazwy kontenera docelowego, automatycznie przypiszesz nazwę kontenera jako `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Można utworzyć ciągi szablonów dla docelowej nazwy kontenera, zapoznaj się z kolumną możliwe wartości. <br>*% h-> IoT Hub nazwy (3-50 znaków). <br>*% d-> IoT Edge identyfikator urządzenia (od 1 do 129 znaków). <br>*% m-> Nazwa modułu (od 1 do 64 znaków). <br>*% c-> nazwę kontenera źródłowego (od 3 do 63 znaków). <br><br>Maksymalny rozmiar nazwy kontenera to 63 znaków, podczas gdy automatyczne przypisanie nazwy kontenera docelowego, jeśli rozmiar kontenera przekracza 63 znaków, spowoduje to przycinanie każdej sekcji (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) do 15 znaków. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | `false` Domyślnie ustawiona wartość. Gdy jest ustawiona na `true`, automatycznie usunie dane po zakończeniu przekazywania do magazynu w chmurze | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Nazwa tego ustawienia to`deviceAutoDeleteProperties`

| Pole | Możliwe wartości | Wyjaśnienie | Zmienna środowiskowa |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | `false` Domyślnie ustawiona wartość. Jeśli chcesz włączyć tę funkcję, ustaw to pole na `true`. | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Określ czas (w minutach). Po wygaśnięciu tej wartości moduł automatycznie usunie obiekty blob z magazynu lokalnego. | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Domyślnie jest ustawiony na `true`i zachowuje obiekt BLOB podczas przekazywania go do magazynu w chmurze, jeśli deleteAfterMinutes wygasa. Można ją ustawić na `false` i będzie ona usuwać dane natychmiast po wygaśnięciu deleteAfterMinutes. Uwaga: Aby ta właściwość działała, uploadOn powinna mieć wartość true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Konfigurowanie plików dziennika

Aby uzyskać informacje na temat konfigurowania plików dziennika dla modułu, zobacz te [najlepsze rozwiązania](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)w zakresie produkcji.

## <a name="connect-to-your-blob-storage-module"></a>Łączenie usługi modułu magazynu obiektów blob

Można użyć nazwy konta i klucz konta, które skonfigurowano dla modułu dostępu do magazynu obiektów blob na urządzeniu usługi IoT Edge.

Określ urządzenia usługi IoT Edge jako punkt końcowy obiektu blob do przechowywania dowolnego żądania, które można wprowadzać w nim. Możesz [utworzyć parametry połączenia dla punktu końcowego magazynu jawne](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu informacji o urządzeniu usługi IoT Edge i nazwę konta, które zostały skonfigurowane.

- W przypadku modułów wdrożonych na tym samym urządzeniu, na których jest uruchomiony Blob Storage platformy Azure w module IoT Edge, punkt końcowy obiektu BLOB `http://<module name>:11002/<account name>`to:.
- W przypadku modułów lub aplikacji uruchamianych na innym urządzeniu należy wybrać odpowiedni punkt końcowy dla sieci. W zależności od konfiguracji sieci wybierz format punktu końcowego, który umożliwia dostęp danych z modułu zewnętrznego lub aplikacji do urządzenia z uruchomioną Blob Storage platformy Azure na IoT Edge module. Punkt końcowy obiektu BLOB w tym scenariuszu jest jednym z:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Przykłady szybkiego startu platformy Azure Blob Storage

Dokumentacja usługi Azure Blob Storage zawiera przykładowy kod szybkiego startu w kilku językach. Te przykłady można uruchomić w celu przetestowania Blob Storage platformy Azure na IoT Edge przez zmianę punktu końcowego obiektu BLOB w celu nawiązania połączenia z lokalnym modułem magazynu obiektów BLOB.

W poniższych przykładach szybkiego startu są używane Języki, które są również obsługiwane przez IoT Edge, więc można je wdrożyć jako moduły IoT Edge obok modułu BLOB Storage:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Nawiązywanie połączenia z magazynem lokalnym za pomocą Eksplorator usługi Azure Storage

Za pomocą [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) można nawiązać połączenie z kontem magazynu lokalnego.

1. Pobierz i zainstaluj Eksplorator usługi Azure Storage

1. Nawiązywanie połączenia z usługą Azure Storage przy użyciu parametrów połączenia

1. Podaj parametry połączenia:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Wykonaj kroki, aby nawiązać połączenie.

1. Utwórz kontener wewnątrz lokalnego konta magazynu

1. Rozpocznij przekazywanie plików jako blokowych obiektów BLOB.
   > [!NOTE]
   > Ten moduł nie obsługuje stronicowych obiektów BLOB.

1. Możesz również wybrać opcję łączenia kont usługi Azure Storage w Eksplorator usługi Storage. Ta konfiguracja zapewnia jeden widok zarówno dla konta magazynu lokalnego, jak i konta usługi Azure Storage

## <a name="supported-storage-operations"></a>Obsługiwane operacje

Moduły magazynu obiektów BLOB na IoT Edge korzystają z zestawów SDK usługi Azure Storage i są spójne z wersją 2017-04-17 interfejsu API usługi Azure Storage dla punktów końcowych blokowych obiektów BLOB. 

Ponieważ nie wszystkie operacje Blob Storage platformy Azure są obsługiwane przez usługę Azure Blob Storage w IoT Edge, w tej sekcji przedstawiono stan każdego z nich.

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

- Umieść blok
- Umieszczanie i Uzyskaj listę zablokowanych

Nieobsługiwane:

- Umieść blok z adresu URL

## <a name="release-notes"></a>Informacje o wersji

Poniżej znajdują się [Informacje o wersji usługi Docker Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) dla tego modułu

## <a name="feedback"></a>Opinia

Twoja opinia jest ważna dla nas, aby ułatwić korzystanie z tego modułu i jego funkcji. Podziel się swoją opinią i daj nam znać, jak możemy udoskonalić.

Możesz skontaktować się z nami naabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu BLOB Storage platformy Azure na IoT Edge](how-to-deploy-blob.md)
