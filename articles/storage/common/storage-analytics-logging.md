---
title: Usługa Azure Storage Analytics rejestrowania
description: Dowiedz się, jak rejestrować szczegóły dotyczące żądania skierowanego do usługi Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 1e0e9cb899fb28257b864d0e31daa724fbd59e4c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153957"
---
# <a name="azure-storage-analytics-logging"></a>Rejestrowania analizy magazynu platformy Azure

Usługa Storage Analytics rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi storage. Informacja ta może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane na zasadzie największej staranności.

 Nie włączono rejestrowania danych analizy magazynu domyślnie konta magazynu. Możesz ją włączyć w [witryny Azure portal](https://portal.azure.com/); Aby uzyskać więcej informacji, zobacz [monitorowania na koncie magazynu w witrynie Azure portal](/azure/storage/storage-monitor-storage-account). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Użyj [pobrać właściwości usługi obiektów Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [pobrać właściwości usługi kolejki](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), i [pobrać właściwości usługi tabeli](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) Procedura włączania analityka magazynu dla każdej usługi.

 Wpisy dziennika są tworzone tylko wtedy, gdy żądania skierowanego do punktu końcowego usługi. Na przykład jeśli konto magazynu ma działania, w jej punkt końcowy usługi Blob, ale nie w jego tabel lub kolejek punkty końcowe, zostaną utworzone tylko dzienniki odnoszących się do usługi obiektów Blob.

> [!NOTE]
>  Rejestrowanie danych analizy magazynu jest obecnie dostępna tylko dla usług obiektów Blob, kolejek i tabel. Konto magazynu premium storage nie jest obsługiwana.

## <a name="requests-logged-in-logging"></a>Rejestrowane podczas rejestrowania żądań
### <a name="logging-authenticated-requests"></a>Uwierzytelnione rejestrowanie żądań

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem oraz tym limit czasu, ograniczanie przepustowości, sieci, autoryzacji i inne błędy
- Żądania przy użyciu sygnatury dostępu współdzielonego (SAS) lub protokołu OAuth, w tym realizowania żądań nie powiodło się, jak i pomyślnie
- Żądania do analizy danych

  Żądania wysyłane przez analityka magazynu, takie jak tworzenie dziennika lub usuwanie, nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [operacji rejestrowane analizy magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format) tematów.

### <a name="logging-anonymous-requests"></a>Rejestrowanie żądań anonimowych

 Rejestrowane są następujące typy anonimowe żądania:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy przekroczenia limitu czasu dla klienta i serwera
- Żądania GET zakończone niepowodzeniem z kodem błędu 304 (nie zmodyfikowano)

  Inne zakończone niepowodzeniem żądania anonimowe nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [operacji rejestrowane analizy magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) i [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format) tematów.

## <a name="how-logs-are-stored"></a>Jak są przechowywane dzienniki

Wszystkie dzienniki są przechowywane w blokowych obiektów blob w kontenerze o nazwie `$logs`, które są tworzone po analityka magazynu jest włączona dla konta magazynu. `$logs` Kontenera znajduje się w przestrzeni nazw obiektów blob konta magazynu, na przykład: `http://<accountname>.blob.core.windows.net/$logs`. Ten kontener nie można usunąć po włączeniu usługa Storage Analytics, jednak można je usunąć jego zawartość. Przejdź do kontenera bezpośrednio za pomocą usługi z Przeglądanie magazynu narzędzia, zobaczysz wszystkie obiekty BLOB, które zawierają dane rejestrowania.

> [!NOTE]
>  `$logs` Kontenera nie jest wyświetlany, gdy przeprowadzane jest kontenerem listę operacji takich jak kontenery listy operacji. Muszą być dostępne bezpośrednio. Na przykład, można użyć operacji wyświetlanie listy obiektów blob, dostęp do obiektów blob w `$logs` kontenera.

Jak żądania są rejestrowane, usługa Storage Analytics przekazać wyników pośrednich jako bloki. Okresowo usługa Storage Analytics zatwierdzić te bloki i udostępnić je jako obiekt blob. Może potrwać do godziny dane dziennika, zostaną wyświetlone w obiektach BLOB w **$logs** kontenera ponieważ częstotliwością, jaką Usługa magazynu opróżnia składników zapisywania dziennika. Zduplikowane rekordy mogą istnieć dzienników utworzonych w tej samej godziny. Można określić, czy rekord jest duplikatem, sprawdzając **RequestId** i **operacji** numer.

Jeśli masz duże ilości danych dziennika z wieloma plikami za każdą godzinę, można użyć metadane obiektu blob, aby określić, jakie dane dziennika zawiera, sprawdzając pola metadanych obiektu blob. Jest to również przydatne, ponieważ może czasami wystąpić opóźnienie podczas, gdy dane są zapisywane w plikach dziennika: metadane obiektu blob daje bardziej dokładne wskazanie zawartość obiektu blob niż nazwa obiektu blob.

Większość narzędzi do przeglądania magazynu umożliwiają wyświetlanie metadanych obiektów blob; można również przeczytać te informacje przy użyciu programu PowerShell lub programowo. Poniższy fragment kodu programu PowerShell znajduje się przykład filtrowanie listy obiektów blob dziennika przez nazwę, aby określić godzinę oraz metadane, aby zidentyfikować tylko tych dzienników, które zawierają **zapisu** operacji.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Aby dowiedzieć się, jak programowo wyświetlanie listy obiektów blob, zobacz [wyliczanie zasobów obiektu Blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) i [ustawienia, pobieranie właściwości oraz metadanych dla zasobów obiektów Blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Zaloguj się konwencje nazewnictwa

 Każdy dziennik zostanie zapisany w następującym formacie:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 W poniższej tabeli opisano każdego atrybutu w parametrze nazwa dziennika:

|Atrybut|Opis|
|---------------|-----------------|
|`<service-name>`|Nazwa usługi storage. Na przykład: `blob`, `table`, lub `queue`|
|`YYYY`|Rok czterocyfrowej dziennika. Na przykład: `2011`|
|`MM`|Miesiąc dwucyfrowa dziennika. Na przykład: `07`|
|`DD`|Dzień dwucyfrowa dziennika. Na przykład: `31`|
|`hh`|Godzinę dwucyfrowa, która wskazuje, począwszy od godziny dla dzienników w 24-godzinnym formacie UTC z formatem. Na przykład: `18`|
|`mm`|Dwucyfrowa liczba wskazuje, począwszy od minutę dzienników. **Uwaga:**  Ta wartość nie jest obsługiwany w bieżącej wersji programu Storage Analytics, a jego wartość będzie zawsze `00`.|
|`<counter>`|Liczony od zera licznik z sześć cyfr wskazującą liczbę obiektów blob dziennika wygenerowany dla usługi storage w ciągu godziny przedziale czasu. Ten licznik rozpoczyna się od `000000`. Na przykład: `000001`|

 Poniżej znajduje się pełny przykład nazwę dziennika, łączącą powyższych przykładach:

 `blob/2011/07/31/1800/000001.log`

 Oto przykładowy identyfikator URI, który można uzyskać dostępu do dziennika powyżej:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Po zalogowaniu żądanie magazynu wynikowa nazwa dziennika skorelowany godzinę zakończenia żądanej operacji. Na przykład jeśli żądanie GetBlob zostało zakończone o godzinie 18:30:00 w 7/31/2011, dziennik będzie zapisywany z prefiksem następujące: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadane dziennika

 Wszystkie obiekty BLOB dziennika są przechowywane przy użyciu metadanych, który może służyć do identyfikowania danych rejestrowania, jakie zawiera obiekt blob. W poniższej tabeli opisano każdy atrybut metadanych:

|Atrybut|Opis|
|---------------|-----------------|
|`LogType`|Opisuje, czy dziennik zawiera informacje dotyczące odczytu, zapisu lub operacje usuwania. Ta wartość może zawierać jeden typ lub kombinacji tych trzech miejsc, oddzielonych przecinkami.<br /><br /> Przykład 1: `write`<br /><br /> Przykład 2: `read,write`<br /><br /> Przykład 3: `read,write,delete`|
|`StartTime`|Najwcześniejsza godzina wpisu w dzienniku w formie `YYYY-MM-DDThh:mm:ssZ`. Na przykład: `2011-07-31T18:21:46Z`|
|`EndTime`|Ostatni czas zapisu w dzienniku w formie `YYYY-MM-DDThh:mm:ssZ`. Na przykład: `2011-07-31T18:22:09Z`|
|`LogVersion`|Wersja format dziennika.|

 Poniższa lista zawiera pełny przykład metadanych przy użyciu powyższych przykładach:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Włącz rejestrowanie danych magazynu

Można włączyć rejestrowanie magazynu za pomocą witryny Azure portal, programu PowerShell i zestawy SDK magazynu.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Włączanie rejestrowania magazynu przy użyciu witryny Azure portal  

W witrynie Azure portal, należy użyć **ustawienia diagnostyczne (klasyczne)** bloku do kontroli magazynu, rejestrowanie, dostępna z **monitorowanie (wersja klasyczna)** części na koncie magazynu **Menu bloku** .

Można określić usługi magazynu, które mają być rejestrowane i okres przechowywania (w dniach) zarejestrowanych danych.  

### <a name="enable-storage-logging-using-powershell"></a>Włączanie rejestrowania magazynu przy użyciu programu PowerShell  

 Można użyć programu PowerShell na komputerze lokalnym Aby skonfigurować rejestrowanie magazynu na koncie magazynu za pomocą polecenia cmdlet programu Azure PowerShell **Get AzureStorageServiceLoggingProperty** można pobrać bieżące ustawienia i polecenia cmdlet  **Zestaw AzureStorageServiceLoggingProperty** do zmiany bieżących ustawień.  

 Użyj polecenia cmdlet, które kontrolują rejestrowania magazynu **LoggingOperations** parametr, który jest ciąg zawierający rozdzielaną przecinkami listę typów żądań logowania. Są trzy typy żądań możliwych **odczytu**, **zapisu**, i **Usuń**. Aby wyłączyć rejestrowanie, należy użyć wartości **Brak** dla **LoggingOperations** parametru.  

 Następujące polecenie zmienia rejestrowania do odczytu, zapisu i usuwania żądań w usłudze kolejki w domyślnym kontem magazynu za pomocą pięciu dni przechowywania:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Następujące polecenie wyłącza rejestrowania w usłudze table service na koncie magazynu domyślnego:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Aby uzyskać informacje o sposobie konfigurowania poleceń cmdlet programu Azure PowerShell do pracy z subskrypcją platformy Azure i jak wybrać domyślne konto magazynu do użycia, zobacz: [Jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Włącz rejestrowanie programowo Magazyn  

 Oprócz używania witryny Azure portal lub poleceń cmdlet programu Azure PowerShell do rejestrowania magazynu kontroli, umożliwia także jeden z interfejsów API usługi Azure Storage. Na przykład jeśli używasz języka .NET przy użyciu biblioteki klienta magazynu.  

 Klasy **CloudBlobClient**, **CloudQueueClient**, i **CloudTableClient** mają wszystkie metody takie jak **SetServiceProperties** i **SetServicePropertiesAsync** o **ServiceProperties** obiektu jako parametr. Możesz użyć **ServiceProperties** obiektu, aby skonfigurować rejestrowanie magazynu. Na przykład następująca C# fragment kodu przedstawia sposób zmiany, co jest rejestrowane i okres przechowywania dla rejestrowania kolejki:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Aby uzyskać więcej informacji o używaniu języka .NET, aby skonfigurować rejestrowanie magazynu, zobacz [odwołanie do biblioteki klienta magazynu](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Aby uzyskać ogólne informacje na temat konfigurowania magazynu rejestrowania przy użyciu interfejsu API REST, zobacz [Włączanie i konfigurowanie Storage Analytics](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Pobierz rejestrowania danych dziennika usługi Storage

 Aby przeglądać i analizować dane dziennika, należy pobrać obiekty BLOB, które zawierają dane dziennika, który Cię interesuje na komputerze lokalnym. Wiele narzędzi do przeglądania magazynu umożliwiają pobieranie obiektów blob z konta magazynu; Możesz również użyć zespół usługi Azure Storage, pod warunkiem narzędzia do kopiowania wiersza polecenia platformy Azure (**AzCopy**) można pobrać dane dziennika.  

 Aby upewnić się, że pobrano dane dziennika, który Cię interesuje, a także uniknąć pobierania tych samych danych dziennika w więcej niż raz:  

-   Użyj daty i godziny konwencji nazewnictwa dla obiektów blob zawierający dane dziennika do śledzenia, które obiekty BLOB, możesz już pobrane do analizy uniknąć ponownego pobrania tych samych danych więcej niż jeden raz.  

-   Użyj metadanych obiektów blob zawierający dane dziennika, aby zidentyfikować określonego okresu, dla którego obiekt blob przechowuje dane dziennika, aby zidentyfikować dokładną obiektów blob, czego potrzebujesz do pobrania.  

> [!NOTE]
>  Narzędzie AzCopy jest częścią zestawu SDK platformy Azure, ale zawsze możesz pobrać najnowszą wersję z [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). Domyślnie narzędzie AzCopy jest zainstalowane w folderze **\Microsoft C:\Program Files (x86) Azure\AzCopy**, i należy dodać ten folder do ścieżki, zanim spróbujesz uruchomić narzędzie w wierszu polecenia lub w oknie programu PowerShell.  

 Poniższy przykład pokazuje, jak można pobrać danych dziennika dla kolejki usługi godzin, począwszy od 09 AM, 10 AM i 11: 00 20 maja 2014 r. **/S** parametru powoduje, że narzędzia AzCopy do tworzenia struktury folderu lokalnego, na podstawie daty i godziny w nazwach plików dziennika; **/V** parametru powoduje, że narzędzia AzCopy uzyskać pełne wyniki; **/Y** parametru powoduje, że narzędzia AzCopy zastąpić wszelkie pliki lokalne. Zastąp **< yourstorageaccount\>**  nazwą konta magazynu i Zastąp **< yourstoragekey\>**  kluczem konta magazynu.  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 Narzędzie AzCopy ma również pewne przydatne parametry tej kontrolki jak Ustawia godzinę ostatniej modyfikacji plików, które są pobierane i czy spróbuje pobrać pliki, które są starsze lub nowsze niż pliki, które już istnieją na komputerze lokalnym. Można również uruchomić w trybie ponownego uruchamiania. Aby uzyskać szczegółowe informacje, Wyświetl Pomoc, uruchamiając **AzCopy /?** polecenie.  

 Na przykład jak programowo pobrać dane dziennika, zobacz wpis w blogu [rejestrowanie usługi Windows Azure Storage: Korzystanie z dzienników do śledzenia żądań magazynu](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) i wyszukaj słowa "DumpLogs" na stronie.  

 Po pobraniu danych dziennika można wyświetlić wpisy dziennika w plikach. Te pliki dziennika użyć format tekstu rozdzielanego tę liczbę dziennika odczytywanie narzędzi są w stanie przeanalizować, włącznie z Microsoft Message Analyzer (Aby uzyskać więcej informacji, zobacz podręcznik [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Różne narzędzia mają różne funkcje służące do formatowania, filtrowanie, sortowanie, wyszukiwanie zawartości plików dzienników usługi ad. Aby uzyskać więcej informacji na temat rejestrowania magazynu format pliku dziennika i zawartości, zobacz [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format) i [operacji rejestrowane analizy magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Kolejne kroki

* [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format)
* [Usługa Storage Analytics rejestrowane komunikaty o stanie i operacje](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metryk usługi Storage Analytics (wersja klasyczna)](storage-analytics-metrics.md)
