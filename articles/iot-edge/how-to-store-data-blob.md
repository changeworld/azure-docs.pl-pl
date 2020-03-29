---
title: Przechowuj obiekty blob bloków na urządzeniach — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Poznaj funkcje warstwowe i czas do przeżycia, zobacz obsługiwane operacje magazynu obiektów blob i połącz się z kontem magazynu obiektów blob.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509822"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Store data at the edge with Azure Blob Storage on IoT Edge (Przechowywanie danych na urządzeniu brzegowym dzięki usłudze Azure Blob Storage w usłudze IoT Edge)

Usługa Azure Blob Storage w usłudze IoT Edge udostępnia [blokowy obiekt blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) i dołączanie rozwiązania magazynu [obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) na urządzeniach brzegowych. Moduł magazynu obiektów blob na urządzeniu usługi Usługi IoT Edge zachowuje się jak usługa obiektów blob platformy Azure, z tą różnicą, że obiekty blob są przechowywane lokalnie na urządzeniu usługi IoT Edge. Można uzyskać dostęp do obiektów blob przy użyciu tych samych metod zestawów SDK magazynu platformy Azure lub wywołań interfejsu API obiektów blob, które są już używane. W tym artykule opisano pojęcia związane z usługą Azure Blob Storage w kontenerze usługi IoT Edge, który uruchamia usługę obiektu blob na urządzeniu usługi IoT Edge.

Ten moduł jest przydatny w scenariuszach:

* gdzie dane muszą być przechowywane lokalnie, dopóki nie będą mogły być przetwarzane lub przesyłane do chmury. Dane te mogą być filmami, obrazami, danymi finansowymi, danymi szpitalnymi lub innymi danymi nieustrukturyzowanymi.
* gdy urządzenia znajdują się w miejscu o ograniczonej łączności.
* jeśli chcesz wydajnie przetwarzać dane lokalnie, aby uzyskać dostęp do danych o małym opóźnieniu, dzięki czemu można reagować na sytuacje awaryjne tak szybko, jak to możliwe.
* gdy chcesz zmniejszyć koszty przepustowości i uniknąć przesyłania terabajtów danych do chmury. Można przetwarzać dane lokalnie i wysyłać tylko przetworzone dane do chmury.

Obejrzyj film, aby uzyskać szybkie wprowadzenie
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Ten moduł jest wyposażony **w funkcje deviceToCloudUpload** i **deviceAutoDelete.**

**deviceToCloudUpload** jest konfigurowalną funkcją. Ta funkcja automatycznie przekazuje dane z lokalnego magazynu obiektów blob na platformę Azure z przerwową obsługą łączności z Internetem. Pozwala na:

* Włącz/WYŁĄCZ funkcję deviceToCloudUpload.
* Wybierz kolejność, w jakiej dane są kopiowane na platformę Azure, takie jak NewestFirst lub OldestFirst.
* Określ konto usługi Azure Storage, na które mają zostać przekazane dane.
* Określ kontenery, które chcesz przekazać na platformę Azure. Ten moduł umożliwia określenie nazw kontenerów źródłowych i docelowych.
* Wybierz możliwość natychmiastowego usunięcia obiektów blob po zakończeniu przesyłania do magazynu w chmurze
* Wykonaj pełne przekazywanie `Put Blob` obiektów blob (przy `Put Block`użyciu `Put Block List` `Append Block` operacji) i przekazywanie na poziomie bloku (przy użyciu i operacjach).

Ten moduł używa przekazywania na poziomie bloku, gdy obiekt blob składa się z bloków. Oto niektóre z typowych scenariuszy:

* Aplikacja aktualizuje niektóre bloki wcześniej przekazanego bloku blob lub dołącza nowe bloki do obiektu blob dołączania, ten moduł przekazuje tylko zaktualizowane bloki, a nie cały obiekt blob.
* Moduł jest przekazywanie obiektu blob i połączenie internetowe znika, gdy łączność jest z powrotem przekazuje tylko pozostałe bloki, a nie cały obiekt blob.

Jeśli nieoczekiwane zakończenie procesu (jak awaria zasilania) dzieje się podczas przekazywania obiektu blob, wszystkie bloki, które były należne do przesłania zostaną przesłane ponownie po powrocie modułu do trybu online.

**deviceAutoDelete** jest konfigurowalną funkcją. Ta funkcja automatycznie usuwa obiekty blob z magazynu lokalnego po upływie określonego czasu trwania (mierzonego w minutach). Pozwala na:

* Włącz/WYŁĄCZ funkcję deviceAutoDelete.
* Określ czas w minutach (deleteAfterMinutes), po którym obiekty blob zostaną automatycznie usunięte.
* Wybierz możliwość zachowania obiektu blob podczas przekazywania, jeśli wartość deleteAfterMinutes wygaśnie.

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Maszynę deweloperającą lub maszynę wirtualną można używać jako urządzenia usługi IoT Edge, wykonując kroki opisane w przewodniku Szybki start dla urządzeń [z systemem Linux](quickstart-linux.md) lub [Windows.](quickstart.md)

* Zobacz [systemy obsługiwane przez usługę Azure IoT Edge,](support.md#operating-systems) aby uzyskać listę obsługiwanych systemów operacyjnych i architektur. Moduł usługi Azure Blob Storage on IoT Edge obsługuje następujące architektury:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (wersja zapoznawcza)

Zasoby w chmurze:

Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>właściwości deviceToCloudUpload i deviceAutoDelete

Użyj żądanych właściwości modułu, aby ustawić **deviceToCloudUploadProperties** i **deviceAutoDeleteProperties**. Żądane właściwości można ustawić podczas wdrażania lub później zmienić, edytując bliźniaczej reprezentacji modułu bez konieczności ponownego wdrażania. Zalecamy sprawdzenie "Moduł Twin" `reported configuration` `configurationValidation` dla i upewnij się, że wartości są poprawnie propagowane.

### <a name="devicetoclouduploadproperties"></a>właściwości deviceToCloudUploadProperties

Nazwa tego ustawienia `deviceToCloudUploadProperties`to . Jeśli używasz symulatora usługi IoT Edge, ustaw wartości powiązanych zmiennych środowiskowych dla tych właściwości, które można znaleźć w sekcji objaśnienia.

| Właściwość | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| uploadOn (uploadOn) | wartość true, false | Domyślnie `false` ustawiona na wartość. Jeśli chcesz włączyć operację, ustaw to `true`pole na . <br><br> Zmienna środowiskowa:`deviceToCloudUploadProperties__uploadOn={false,true}` |
| wyślij pozycję Poorator | NajnowszePierwsza, NajstarszaFirst | Umożliwia wybranie kolejności, w jakiej dane są kopiowane na platformę Azure. Domyślnie `OldestFirst` ustawiona na wartość. Kolejność jest określana przez ostatni zmodyfikowany czas blob. <br><br> Zmienna środowiskowa:`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionSstring |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`to parametry połączenia, które umożliwia określenie konta magazynu, na które mają zostać przekazane dane. `Azure Storage Account Name`Określ `Azure Storage Account Key` `End point suffix`, , . Dodaj odpowiedni endpointSuffix platformy Azure, gdzie zostaną przekazane dane, różni się w przypadku globalnej platformy Azure, platformy rządowej i usługi Microsoft Azure Stack. <br><br> W tym miejscu można określić parametry połączenia usługi Azure Storage SAS. Ale musisz zaktualizować tę właściwość, gdy wygaśnie. <br><br> Zmienna środowiskowa:`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| magazynContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umożliwia określenie nazw kontenerów, które chcesz przekazać na platformę Azure. Ten moduł umożliwia określenie nazw kontenerów źródłowych i docelowych. Jeśli nie określisz nazwy kontenera docelowego, automatycznie przypisze `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`nazwę kontenera jako . Można utworzyć ciągi szablonów dla nazwy kontenera docelowego, wyewidencjonować kolumnę możliwych wartości. <br>* %h -> Nazwa centrum IoT (3-50 znaków). <br>* %d -> identyfikator urządzenia IoT Edge (od 1 do 129 znaków). <br>* %m -> nazwa modułu (od 1 do 64 znaków). <br>* %c -> nazwa kontenera źródłowego (od 3 do 63 znaków). <br><br>Maksymalny rozmiar nazwy kontenera to 63 znaki, podczas gdy automatycznie przypisuje nazwę kontenera docelowego, jeśli rozmiar kontenera przekracza 63 znaki, przytnie każdą sekcję (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) do 15 Znaków. <br><br> Zmienna środowiskowa:`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| usuńPo załadowaniu | wartość true, false | Domyślnie `false` ustawiona na wartość. Po ustawieniu `true`na opcję automatycznie usunie dane po zakończeniu przekazywania do magazynu w chmurze. <br><br> **PRZESTROGA:** Jeśli używasz dołączania obiektów blob, to ustawienie spowoduje usunięcie dołączanych obiektów blob z magazynu lokalnego po pomyślnym przekazaniu, a wszelkie przyszłe operacje dołączania bloku do tych obiektów blob zakończy się niepowodzeniem. Użyj tego ustawienia z ostrożnością, nie włączaj tego, jeśli aplikacja wykonuje rzadkie operacje dołączania lub nie obsługuje ciągłych operacji dołączania<br><br> Zmienna `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`środowiskowa: . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteWłaściwości

Nazwa tego ustawienia `deviceAutoDeleteProperties`to . Jeśli używasz symulatora usługi IoT Edge, ustaw wartości powiązanych zmiennych środowiskowych dla tych właściwości, które można znaleźć w sekcji objaśnienia.

| Właściwość | Możliwe wartości | Wyjaśnienie |
| ----- | ----- | ---- |
| deleteOn (usuńOn) | wartość true, false | Domyślnie `false` ustawiona na wartość. Jeśli chcesz włączyć operację, ustaw to `true`pole na . <br><br> Zmienna środowiskowa:`deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Określ czas w minutach. Moduł automatycznie usunie obiekty BLOB z magazynu lokalnego po wygaśnięciu tej wartości. <br><br> Zmienna środowiskowa:`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | wartość true, false | Domyślnie jest ustawiona na `true`, i zachowa obiektu blob podczas przekazywania do magazynu w chmurze, jeśli deleteAfterMinutes wygasa. Można go ustawić `false` i usunie dane, jak tylko deleteAfterMinutes wygasa. Uwaga: Dla tej właściwości do pracy uploadOn powinny być ustawione na true.  <br><br> **PRZESTROGA:** Jeśli używasz dołączania obiektów blob, to ustawienie spowoduje usunięcie dołączanych obiektów blob z magazynu lokalnego po wygaśnięciu wartości, a wszelkie przyszłe operacje dołączania bloku do tych obiektów blob zakończy się niepowodzeniem. Można upewnić się, że wartość wygaśnięcia jest wystarczająco duża dla oczekiwanej częstotliwości dołączania operacji wykonywanych przez aplikację.<br><br> Zmienna środowiskowa:`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Używanie udziału SMB jako magazynu lokalnego

Podczas wdrażania kontenera tego modułu w usłudze Windows na hoście systemu Windows można udostępnić udział SMB jako ścieżkę magazynu lokalnego.

Upewnij się, że udział SMB i urządzenie IoT znajdują się w wzajemnie zaufanych domenach.

Polecenie Programu `New-SmbGlobalMapping` PowerShell można uruchomić, aby lokalnie mapować udział SMB na urządzeniu IoT z systemem Windows.

Poniżej znajdują się kroki konfiguracji:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Przykład:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

To polecenie użyje poświadczeń do uwierzytelniania za pomocą zdalnego serwera SMB. Następnie zamapuj zdalną ścieżkę udostępniania na literę dysku G: (może to być dowolna inna dostępna litera dysku). Urządzenie IoT ma teraz wolumin danych mapowany na ścieżkę na dysku G:.

Upewnij się, że użytkownik w urządzeniu IoT może odczytywać/zapisywać w zdalnym udziale SMB.

Dla wdrożenia wartość `<storage mount>` może być **G:/ContainerData:C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Udzielanie dostępu do katalogu użytkownikowi kontenera w systemie Linux

Jeśli użyto [instalacji woluminu](https://docs.docker.com/storage/volumes/) do przechowywania w opcjach tworzenia kontenerów systemu Linux, nie musisz wykonywać żadnych dodatkowych kroków, ale jeśli użyto [instalacji powiązania,](https://docs.docker.com/storage/bind-mounts/) te kroki są wymagane do prawidłowego uruchomienia usługi.

Zgodnie z zasadą najmniejszych uprawnień, aby ograniczyć prawa dostępu dla użytkowników do minimalnych uprawnień potrzebnych do wykonywania swojej pracy, moduł ten zawiera użytkownika (nazwa: absie, ID: 11000) i grupę użytkowników (nazwa: absie, ID: 11000). Jeśli kontener zostanie uruchomiony jako **katalog główny** (domyślny użytkownik jest **root),** nasza usługa zostanie uruchomiona jako użytkownik **absie** o niskich uprawnieniach.

To zachowanie sprawia, że konfiguracja uprawnień na ścieżce hosta wiąże kluczowe dla usługi do poprawnego działania, w przeciwnym razie usługa upaść z błędami odmowy dostępu. Ścieżka używana w powiązaniu katalogu musi być dostępna dla użytkownika kontenera (np. absie 11000). Możesz udzielić użytkownikowi kontenera dostępu do katalogu, wykonując poniższe polecenia na hoście:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Przykład:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Jeśli musisz uruchomić usługę jako użytkownik inny niż **absie**, można określić niestandardowy identyfikator użytkownika w createOptions w ramach właściwości "Użytkownik" w manifeście wdrożenia. W takim przypadku należy użyć domyślnego `0`lub głównego identyfikatora grupy .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Teraz udziel użytkownikowi kontenera dostępu do katalogu

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Konfigurowanie plików dziennika

Aby uzyskać informacje na temat konfigurowania plików dziennika dla modułu, zobacz te [najlepsze rozwiązania produkcyjne](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Łączenie się z modułem magazynu obiektów blob

Można użyć nazwy konta i klucza konta skonfigurowane dla modułu, aby uzyskać dostęp do magazynu obiektów blob na urządzeniu usługi IoT Edge.

Określ urządzenie usługi IoT Edge jako punkt końcowy obiektu blob dla wszystkich żądań magazynu, które zostały do niego złożone. Można [utworzyć parametry połączenia dla jawnego punktu końcowego magazynu](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) przy użyciu informacji o urządzeniu usługi IoT Edge i skonfigurowanej nazwy konta.

* W przypadku modułów wdrożonych na tym samym urządzeniu, w którym jest uruchomiony moduł usługi Azure Blob Storage w usłudze IoT Edge, punkt końcowy obiektu blob to: `http://<module name>:11002/<account name>`.
* W przypadku modułów lub aplikacji działających na innym urządzeniu należy wybrać odpowiedni punkt końcowy dla sieci. W zależności od konfiguracji sieci wybierz format punktu końcowego, tak aby ruch danych z modułu zewnętrznego lub aplikacji mógł dotrzeć do urządzenia z uruchomionym usługą Azure Blob Storage w module Usługi IoT Edge. Punkt końcowy obiektu blob dla tego scenariusza jest jednym z:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Przykłady szybkiego startu usługi Azure Blob Storage

Dokumentacja usługi Azure Blob Storage zawiera przykładowy kod przewodnika Szybki start w kilku językach. Można uruchomić te przykłady, aby przetestować usługę Azure Blob Storage w usłudze IoT Edge, zmieniając punkt końcowy obiektu blob, aby połączyć się z lokalnym modułem magazynu obiektów blob.

Następujące przykłady szybkiego startu używają języków, które są również obsługiwane przez usługi IoT Edge, dzięki czemu można je wdrożyć jako moduły usługi IoT Edge obok modułu magazynu obiektów blob:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Wersje przed wersją 2.1 sdk języka Python mają znany problem, w którym moduł nie zwraca czasu tworzenia obiektów blob. Z powodu tego problemu niektóre metody, takie jak listy obiektów blob nie działa. Jako obejście jawnie ustaw wersję interfejsu API na kliencie obiektu blob na "2017-04-17". Przykład:`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Dołącz przykład obiektu blob](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Przejdź](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Łączenie się z magazynem lokalnym za pomocą Eksploratora usługi Azure Storage

Za pomocą [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) można połączyć się z kontem magazynu lokalnego.

1. Pobieranie i instalowanie Eksploratora usługi Azure Storage

1. Łączenie się z usługą Azure Storage przy użyciu ciągu połączenia

1. Podaj parametry połączenia:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Przejdź przez kroki, aby połączyć.

1. Tworzenie kontenera wewnątrz konta magazynu lokalnego

1. Rozpocznij przekazywanie plików jako blokuj obiekty blob lub dołączaj obiekty blob.
   > [!NOTE]
   > Ten moduł nie obsługuje obiektów blob page.

1. Można również połączyć konta magazynu platformy Azure w Eksploratorze magazynu. Ta konfiguracja zapewnia jeden widok zarówno dla konta magazynu lokalnego, jak i konta magazynu platformy Azure

## <a name="supported-storage-operations"></a>Obsługiwane operacje magazynowania

Moduły magazynu obiektów Blob w usłudze IoT Edge używają zestawów SDK usługi Azure Storage i są zgodne z wersją interfejsu API usługi Azure Storage dla bloków blob 2017.

Ponieważ nie wszystkie operacje usługi Azure Blob Storage są obsługiwane przez usługę Azure Blob Storage w usłudze IoT Edge, w tej sekcji wymieniono stan każdego z nich.

### <a name="account"></a>Konto

Obsługiwane:

* Wyświetlanie listy kontenerów

Nieobsługiwane:

* Pobierz i ustaw właściwości usługi obiektów blob
* Żądanie obiektu blob inspekcji wstępnej
* Pobierz statystyki usługi obiektów blob
* Uzyskaj informacje o koncie

### <a name="containers"></a>Kontenery

Obsługiwane:

* Tworzenie i usuwanie kontenera
* Pobierz właściwości kontenera i metadane
* Wyświetlanie listy obiektów blob
* Pobierz i ustaw pojemnik ACL
* Ustawianie metadanych kontenera

Nieobsługiwane:

* Kontener dzierżawy

### <a name="blobs"></a>Obiekty blob

Obsługiwane:

* Umieszczanie, poładywać i usuwać obiekt blob
* Właściwości obiektów blob
* Pobierz i ustaw metadane obiektów blob

Nieobsługiwane:

* Dzierżawa obiektu blob
* Obiekt blob migawki
* Kopiowanie i przerywanie kopiowania obiektów blob
* Cofanie usunia obiektu blob
* Ustawianie warstwy obiektów blob

### <a name="block-blobs"></a>Blokowe obiekty blob

Obsługiwane:

* Umieść blok
* Umieszczanie i ujmować listę zablokowanych

Nieobsługiwane:

* Umieść blok z adresu URL

### <a name="append-blobs"></a>Uzupełnialne obiekty blob

Obsługiwane:

* Dołączanie bloku

Nieobsługiwane:

* Dołącz blok z adresu URL

## <a name="event-grid-on-iot-edge-integration"></a>Siatka zdarzeń w integracji usługi IoT Edge

> [!CAUTION]
> Integracja z siatką zdarzeń w uiszcza łańszce IoT

Ten moduł usługi Azure Blob Storage w usłudze IoT Edge zapewnia teraz integrację z siatką zdarzeń w usłudze IoT Edge. Aby uzyskać szczegółowe informacje na temat tej integracji, zobacz [samouczek, aby wdrożyć moduły, opublikować zdarzenia i zweryfikować dostarczanie zdarzeń](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Informacje o wersji

Oto [informacje o wersji w centrum docker](https://hub.docker.com/_/microsoft-azure-blob-storage) dla tego modułu

## <a name="feedback"></a>Opinia

Twoja opinia jest dla nas ważna, aby ten moduł i jego funkcje były przydatne i łatwe w użyciu. Podziel się swoją opinią i daj nam znać, jak możemy poprawić.

Możesz skontaktować się z nami pod adresemabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrożyć usługę Azure Blob Storage w usłudze IoT Edge](how-to-deploy-blob.md)

Bądź na bieżąco z najnowszymi aktualizacjami i ogłoszeniami w [blogu usługi Azure Blob Storage w usłudze IoT Edge](https://aka.ms/abs-iot-blogpost)
