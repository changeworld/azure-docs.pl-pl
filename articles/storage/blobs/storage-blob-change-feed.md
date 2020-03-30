---
title: Zmiana kanału informacyjnego w usłudze Azure Blob Storage (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Dowiedz się więcej o dziennikach zestawienia zmian w usłudze Azure Blob Storage i o tym, jak z nich korzystać.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536891"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage (wersja zapoznawcza)

Celem źródła danych o zmianach jest zapewnienie dzienników transakcji wszystkich zmian, które występują w obiektach blob i metadanych obiektu blob na koncie magazynu. Kanał zmian zawiera **uporządkowany,** **gwarantowany,** **trwały,** **niezmienny**, **tylko do odczytu** dziennik tych zmian. Aplikacje klienckie mogą odczytywać te dzienniki w dowolnym momencie, w trybie przesyłania strumieniowego lub w trybie wsadowym. Źródło danych o zmianach umożliwia tworzenie wydajnych i skalowalnych rozwiązań, które przetwarzają zdarzenia zmian występujące na koncie magazynu obiektów Blob przy niskich kosztach.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Źródło danych o [zmianach](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) jest przechowywane jako obiekty blob w specjalnym kontenerze na koncie magazynu przy standardowym [koszcie cen obiektów blob.](https://azure.microsoft.com/pricing/details/storage/blobs/) Możesz kontrolować okres przechowywania tych plików na podstawie wymagań (Zobacz [warunki](#conditions) bieżącej wersji). Zdarzenia zmian są dołączane do źródła danych zmian jako rekordy w specyfikacji formatu [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) kompaktowy, szybki, binarny format, który zapewnia struktury danych bogatych ze schematem wbudowanym. Ten format jest szeroko stosowany w ekosystemie Hadoop, usługi Stream Analytics i fabryce danych platformy Azure.

Można przetwarzać te dzienniki asynchronicznie, przyrostowo lub w całości. Dowolna liczba aplikacji klienckich może niezależnie odczytywać źródło zmian równolegle i we własnym tempie. Aplikacje analityczne, takie jak [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) lub [Apache Spark,](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) mogą korzystać z dzienników bezpośrednio jako pliki Avro, co pozwala przetwarzać je przy niskich kosztach, o dużej przepustowości i bez konieczności pisania niestandardowej aplikacji.

Obsługa kanału informacyjnego zmian jest odpowiednia dla scenariuszy, które przetwarzają dane na podstawie obiektów, które uległy zmianie. Na przykład aplikacje mogą:

  - Zaktualizuj indeks pomocniczy, zsynchronizuj z pamięcią podręczną, wyszukiwarką lub innymi scenariuszami zarządzania zawartością.
  
  - Wyodrębnij analizy biznesowe i metryki, na podstawie zmian, które występują w obiektach, albo w sposób przesyłania strumieniowego lub wsadowego.
  
  - Przechowuj, przeprowadzaj inspekcje i analizuj zmiany w obiektach w dowolnym okresie czasu w celu zapewnienia bezpieczeństwa, zgodności lub analizy w zakresie zarządzania danymi w przedsiębiorstwie.

  - Twórz rozwiązania do tworzenia kopii zapasowych, dublowania lub replikowania stanu obiektu na koncie w celu zarządzania klęskami żywiołowymi lub zgodności.

  - Tworzenie potoków połączonych aplikacji, które reagują na zmiany zdarzeń lub zaplanować wykonania na podstawie utworzonego lub zmienionego obiektu.

> [!NOTE]
> Źródło danych change zapewnia trwały, uporządkowany model dziennika zmian, które występują w obiekcie blob. Zmiany są zapisywane i udostępniane w dzienniku zmian w ciągu kilku minut od zmiany. Jeśli aplikacja musi reagować na zdarzenia znacznie szybciej niż to, należy rozważyć użycie [zdarzeń magazynu obiektów blob](storage-blob-event-overview.md) zamiast tego. [Zdarzenia magazynu obiektów BLOB](storage-blob-event-overview.md) udostępnia w czasie rzeczywistym zdarzenia jednorazowe, które umożliwiają usługi Azure Functions lub aplikacji, aby szybko reagować na zmiany, które występują do obiektu blob. 

## <a name="enable-and-disable-the-change-feed"></a>Włączanie i wyłączanie kanału informacyjnego zmian

Aby rozpocząć przechwytywanie i rejestrowanie zmian, należy włączyć plik danych o zmianach na koncie magazynu. Wyłącz kanał informacyjny zmian, aby zatrzymać przechwytywanie zmian. Zmiany można włączać i wyłączać przy użyciu szablonów usługi Azure Resource Manager w portalu lub programie Powershell.

Oto kilka rzeczy, o których należy pamiętać po włączeniu pliku danych o zmianach.

- Istnieje tylko jeden kanał informacyjny zmian dla usługi obiektu blob na każdym koncie magazynu i jest przechowywany w kontenerze **$blobchangefeed.**

- Zmiany dotyczące tworzenia, aktualizowania i usuwania są przechwytywane tylko na poziomie usługi obiektu blob.

- Plik danych o zmianach przechwytuje *wszystkie* zmiany dla wszystkich dostępnych zdarzeń występujących na koncie. Aplikacje klienckie mogą odfiltrować typy zdarzeń zgodnie z wymaganiami. (Zobacz [warunki](#conditions) bieżącej wersji).

- Tylko konta magazynu GPv2 i obiektów blob mogą włączyć pozycję Zmień źródło danych. Konta Programu Premium BlockBlobStorage i konta z włączoną hierarchiczną przestrzenią nazw nie są obecnie obsługiwane. Konta magazynu GPv1 nie są obsługiwane, ale można je uaktualnić do GPv2 bez przestojów, zobacz [Uaktualnianie do konta magazynu GPv2, aby](../common/storage-account-upgrade.md) uzyskać więcej informacji.

> [!IMPORTANT]
> Kanał zmian jest w publicznej wersji zapoznawczej i jest dostępny w regionach **westcentralus** i **westus2.** Zobacz sekcję [warunków](#conditions) tego artykułu. Aby zarejestrować się w wersji zapoznawczej, zobacz sekcję [Rejestrowanie subskrypcji](#register) w tym artykule. Aby włączyć plik danych o zmianie na kontach magazynu, musisz zarejestrować subskrypcję.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Włącz plik danych o zmianach na koncie magazynu przy użyciu witryny Azure portal:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz swoje konto magazynu. 

2. Przejdź do opcji **Ochrona danych** w obszarze Usługa **obiektów Blob**.

3. Kliknij **pozycję Włączone w** obszarze Źródła danych zmiany obiektu **Blob**

4. Wybierz przycisk **Zapisz,** aby potwierdzić ustawienia ochrony danych

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Włącz plik danych o zmianach przy użyciu programu PowerShell:

1. Zainstaluj najnowszą usługę PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zamknij, a następnie ponownie otwórz konsolę programu Powershell.

3. Zainstaluj moduł **podglądu Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Zaloguj się do subskrypcji `Connect-AzAccount` platformy Azure za pomocą polecenia i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie, aby uwierzytelnić.

   ```powershell
   Connect-AzAccount
   ```

5. Włącz plik danych o zmianach dla swojego konta magazynu.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Szablonu](#tab/template)
Użyj szablonu usługi Azure Resource Manager, aby włączyć pozycję Zmień źródło danych na istniejącym koncie magazynu za pośrednictwem witryny Azure portal:

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób**.

2. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.

3. Wybierz **[pozycję Wdrażanie szablonu niestandardowego](https://portal.azure.com/#create/Microsoft.Template)**, a następnie wybierz pozycję **Zbuduj własny szablon w edytorze**.

4. W edytorze szablonów wklej w następującym json. Zastąp symbol zastępczy `<accountName>` nazwą konta magazynu.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Wybierz przycisk **Zapisz,** określ grupę zasobów konta, a następnie wybierz przycisk **Zakup,** aby wdrożyć szablon i włącz plik danych o zmianach.

---

## <a name="consume-the-change-feed"></a>Korzystanie z kanału informacyjnego zmian

Źródło danych o zmianach generuje kilka metadanych i plików dziennika. Pliki te znajdują się w **kontenerze $blobchangefeed** konta magazynu. 

> [!NOTE]
> W bieżącej wersji kontener **$blobchangefeed** nie jest widoczny w Eksploratorze usługi Azure Storage lub w witrynie Azure portal. Obecnie nie widać kontenera $blobchangefeed podczas wywoływania ListContainers INTERFEJSU API, ale można wywołać ListBlobs INTERFEJSU API bezpośrednio w kontenerze, aby wyświetlić obiekty blob.

Aplikacje klienckie mogą korzystać z kanału informacyjnego zmian przy użyciu biblioteki procesora danych strumieniowych zmian obiektu blob, która jest dostarczana z SDK procesora kanału informacyjnego Zmiany. 

Zobacz [Dzienniki kanału informacyjnego zmiany procesu w usłudze Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Opis organizacji pliku danych o zmianach

<a id="segment-index"></a>

### <a name="segments"></a>Segmenty

Kanał informacyjny zmian to dziennik zmian, które są zorganizowane w *segmenty godzinowe,* ale dołączane i aktualizowane co kilka minut. **hourly** Te segmenty są tworzone tylko wtedy, gdy istnieją zdarzenia zmiany obiektu blob, które występują w tej godzinie. Dzięki temu aplikacja kliencka do korzystania ze zmian, które występują w określonych zakresach czasu bez konieczności przeszukiwania całego dziennika. Aby dowiedzieć się więcej, zobacz [specyfikacje](#specifications).

Dostępny co godzinę segment kanału informacyjnego zmian jest opisany w pliku manifestu, który określa ścieżki do plików zamieć zmiany dla tego segmentu. Lista katalogu `$blobchangefeed/idx/segments/` wirtualnego pokazuje te segmenty uporządkowane według czasu. Ścieżka segmentu opisuje początek godzinowego zakresu czasu, który reprezentuje segment. Za pomocą tej listy można odfiltrować segmenty dzienników, które Cię interesują.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Jest `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` tworzony automatycznie po włączeniu kanału informacyjnego zmian. Można bezpiecznie zignorować ten plik. Jest to zawsze pusty plik inicjowania. 

Plik manifestu`meta.json`segmentu ( ) pokazuje ścieżkę plików `chunkFilePaths` kanału informacyjnego zmian dla tego segmentu we właściwości. Oto przykład pliku manifestu segmentu.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> `$blobchangefeed` Kontener pojawi się dopiero po włączeniu funkcji kanału informacyjnego zmian na koncie. Musisz poczekać kilka minut po włączeniu źródła danych o zmianie, zanim będzie można wyświetlić listę obiektów blob w kontenerze. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Zmienianie rekordów zdarzeń

Pliki zestawienia zmian zawierają serię rekordów zdarzeń zmian. Każdy rekord zdarzenia zmiany odpowiada jednej zmianie w poszczególnych obiektach blob. Rekordy są serializowane i zapisywane w pliku przy użyciu specyfikacji formatu [Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) Rekordy można odczytywać przy użyciu specyfikacji formatu pliku Avro. Istnieje kilka bibliotek dostępnych do przetwarzania plików w tym formacie.

Zmień pliki kanału informacyjnego są przechowywane w katalogu wirtualnym `$blobchangefeed/log/` jako [dołączanie obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Pierwszy plik kanału informacyjnego zmian `00000` pod każdą ścieżką `00000.avro`będzie miał nazwę pliku (na przykład ). Nazwa każdego kolejnego pliku dziennika dodanego do tej ścieżki zwiększy `00001.avro`się o 1 (na przykład: ).

Oto przykład zmiany rekordu zdarzenia z pliku kanału informacyjnego zmiany przekonwertowanego na Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Aby uzyskać opis każdej właściwości, zobacz [schemat zdarzeń usługi Azure Event Grid dla magazynu obiektów Blob](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Pliki z nadajnikiem zmian dla segmentu nie są natychmiast wyświetlane po utworzeniu segmentu. Długość opóźnienia znajduje się w normalnym przedziale czasu publikowania źródła danych, które znajduje się w ciągu kilku minut od zmiany.

<a id="specifications"></a>

## <a name="specifications"></a>Specyfikacje

- Rekordy zdarzeń zmian są dołączane tylko do pliku danych o zmianach. Po dodaniu tych rekordów są one niezmienne, a pozycja rekordu jest stabilna. Aplikacje klienckie mogą obsługiwać własne punktu kontrolnego na pozycji odczytu źródła danych zmian.

- Rekordy zdarzeń zmiany są dołączane w kolejności kilku minut od zmiany. Aplikacje klienckie mogą korzystać z rekordów, ponieważ są one dołączane do dostępu do przesyłania strumieniowego lub zbiorczo w dowolnym innym czasie.

- Rekordy zdarzeń zmiany są uporządkowane według kolejności modyfikacji **na obiekt blob**. Kolejność zmian w obiektach blob jest niezdefiniowana w usłudze Azure Blob Storage. Wszystkie zmiany w poprzednim segmencie są przed wszelkimi zmianami w kolejnych segmentach.

- Rekordy zdarzeń zmian są serializowane w pliku dziennika przy użyciu specyfikacji formatu [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Zmieniaj rekordy `eventType` zdarzeń, w `Control` których ma wartość są wewnętrzne rekordy systemowe i nie odzwierciedlają zmiany obiektów na koncie. Można bezpiecznie zignorować te rekordy.

- Wartości w `storageDiagnonstics` torbie właściwości są przeznaczone tylko do użytku wewnętrznego i nie są przeznaczone do użytku przez aplikację. Aplikacje nie powinny mieć umownej zależności od tych danych. Można bezpiecznie zignorować te właściwości.

- Czas reprezentowany przez segment jest **przybliżony** z granicami 15 minut. Tak więc, aby zapewnić zużycie wszystkich rekordów w określonym czasie, zużywają kolejne segmenty poprzedniej i następnej godziny.

- Każdy segment może mieć `chunkFilePaths` inną liczbę ze względu na wewnętrzne partycjonowanie strumienia dziennika do zarządzania przepływnością publikowania. Pliki dziennika w `chunkFilePath` każdym z nich są gwarantowane zawierają wzajemnie wykluczające się obiekty BLOB i mogą być używane i przetwarzane równolegle bez naruszania kolejności modyfikacji na obiekt blob podczas iteracji.

- Segmenty zaczynają się `Publishing` w stanie. Po zakończeniu dołączania rekordów do segmentu będzie `Finalized`to . Pliki dziennika w dowolnym segmencie, który `LastConsumable` jest `$blobchangefeed/meta/Segments.json` datowany po dacie właściwości w pliku, nie powinny być używane przez aplikację. Oto przykład właściwości w `LastConsumable` `$blobchangefeed/meta/Segments.json` pliku:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Zarejestruj swoją subskrypcję (wersja zapoznawcza)

Ponieważ plik danych o zmianach jest dostępny tylko w publicznej wersji zapoznawczej, aby korzystać z tej funkcji, musisz zarejestrować subskrypcję.

### <a name="register-by-using-powershell"></a>Zarejestruj się przy użyciu programu PowerShell

W konsoli programu PowerShell uruchom następujące polecenia:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Zarejestruj się przy użyciu interfejsu wiersza polecenia platformy Azure

W usłudze Azure Cloud Shell uruchom następujące polecenia:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Warunki i znane problemy (wersja zapoznawcza)

W tej sekcji opisano znane problemy i warunki w bieżącej publicznej wersji zapoznawczej pliku danych o zmianach. 
- Aby zapoznać się z podglądem, musisz najpierw [zarejestrować subskrypcję,](#register) zanim będzie można włączyć plik danych o zmianach dla konta magazynu w regionach westcentralus lub westus2. 
- Źródło danych o zmianach przechwytuje tylko operacje tworzenia, aktualizowania, usuwania i kopiowania. Aktualizacje metadanych nie są obecnie przechwytywane w wersji zapoznawczej.
- Zmiana rekordów zdarzeń dla każdej pojedynczej zmiany może pojawić się więcej niż jeden raz w pliku danych o zmianach.
- Nie można jeszcze zarządzać okresem istnienia plików dziennika kanału informacyjnego zmian, ustawiając na nich oparte na czasie zasady przechowywania i nie można usunąć obiektów blob. 
- Właściwość `url` pliku dziennika jest obecnie zawsze pusta.
- Właściwość `LastConsumable` pliku segments.json nie zawiera listy pierwszego segmentu, który jest finalizany w pliku danych o zmianach. Ten problem występuje dopiero po sfinalizowaniu pierwszego segmentu. Wszystkie kolejne segmenty po pierwszej godzinie są `LastConsumable` dokładnie przechwytywane w właściwości.
- Obecnie nie widać kontenera **$blobchangefeed** podczas wywoływania interfejsu API ListContainers, a kontener nie jest widoczny w witrynie Azure portal lub Eksploratorze magazynu
- Konta magazynu, które wcześniej zainicjowały [przekładzenie awaryjne konta,](../common/storage-disaster-recovery-guidance.md) mogą mieć problemy z nieustawionym plikiem dziennika. Wszelkie przyszłe przewijane w tryb failover konta może również mieć wpływ na plik dziennika podczas podglądu.

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Jaka jest różnica między rejestrowaniem usługi Change feed i Storage Analytics?
Dzienniki analizy mają rekordy wszystkich operacji odczytu, zapisu, listy i usuwania z pomyślnymi i nieudanymi żądaniami we wszystkich operacjach. Dzienniki analizy są najlepszym rozwiązaniem i nie ma gwarancji zamawiania.

Plik danych o zmianach to rozwiązanie, które udostępnia dziennik transakcyjny udanych mutacji lub zmian na koncie, takich jak tworzenie obiektów blob, modyfikacja i usuwanie. Plik danych o zmianie gwarantuje, że wszystkie zdarzenia mają być rejestrowane i wyświetlane w kolejności pomyślnych zmian na obiekt blob, w związku z czym nie trzeba odfiltrowywać szumu z ogromnej ilości operacji odczytu lub żądań nieudanych. Plik danych o zmianach jest zasadniczo zaprojektowany i zoptymalizowany pod kątem tworzenia aplikacji, które wymagają pewnych gwarancji.

### <a name="should-i-use-change-feed-or-storage-events"></a>Czy należy używać zdarzeń zmień plik danych lub Magazyn?
Można korzystać z obu funkcji, jak zmiana źródła danych i [zdarzeń magazynu obiektów Blob](storage-blob-event-overview.md) zapewniają te same informacje z tej samej gwarancji niezawodności dostarczania, z główną różnicą jest opóźnienie, kolejność i przechowywanie rekordów zdarzeń. Źródło danych change publikuje rekordy w dzienniku w ciągu kilku minut od zmiany, a także gwarantuje kolejność operacji zmiany na obiekt blob. Zdarzenia magazynu są wypychane w czasie rzeczywistym i mogą nie być zamawiane. Zdarzenia pliku danych zmian są trwale przechowywane wewnątrz konta magazynu jako dzienniki stabilne tylko do odczytu z własnym zdefiniowanym przechowywaniem, podczas gdy zdarzenia magazynu są przejściowe do korzystania przez program obsługi zdarzeń, chyba że jawnie je przechowujesz. Dzięki plikowi danych informacyjnych Change dowolna liczba aplikacji może korzystać z dzienników w ich własnej wygody przy użyciu interfejsów API obiektów blob lub zestawu SDK. 

## <a name="next-steps"></a>Następne kroki

- Zobacz przykład sposobu odczytywania źródła danych o zmianach przy użyciu aplikacji klienckiej platformy .NET. Zobacz [Dzienniki kanału informacyjnego zmiany procesu w usłudze Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Dowiedz się, jak reagować na wydarzenia w czasie rzeczywistym. Zobacz [Reagowanie na zdarzenia magazynu obiektów blob](storage-blob-event-overview.md)
- Dowiedz się więcej o szczegółowych informacjach rejestrowania dla operacji udanych i nieudanych dla wszystkich żądań. Zobacz [rejestrowanie analizy usługi Azure Storage](../common/storage-analytics-logging.md)
