---
title: Rejestrowanie analityki magazynu platformy Azure
description: Dowiedz się, jak rejestrować szczegóły dotyczące żądań dotyczących usługi Azure Storage.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 25c047dc9b2ce08ca39e69c6f106e41c5d9bd0dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268420"
---
# <a name="azure-storage-analytics-logging"></a>Rejestrowanie analityki magazynu platformy Azure

Usługa Storage Analytics rejestruje szczegółowe informacje dotyczące żądań do usługi magazynu zakończonych powodzeniem i niepowodzeniem. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszych starań.

 Rejestrowanie w usłudze Storage Analytics nie jest domyślnie włączone dla Twojego konta magazynu. Można go włączyć w [witrynie Azure portal;](https://portal.azure.com/) aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w witrynie Azure portal](/azure/storage/storage-monitor-storage-account). Można również włączyć usługi Storage Analytics programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj [get właściwości usługi obiektów blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [Pobierz właściwości usługi kolejki](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)i Pobierz właściwości usługi [tabel,](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) aby włączyć analizę magazynu dla każdej usługi.

 Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania wykonane względem punktu końcowego usługi. Na przykład jeśli konto magazynu ma działanie w jego blob punktu końcowego, ale nie w jego tabeli lub kolejka punktów końcowych, zostaną utworzone tylko dzienniki odnoszące się do usługi obiektów Blob.

> [!NOTE]
>  Rejestrowanie w usłudze Storage Analytics jest aktualnie dostępne tylko dla usług Blob Storage, Queue Storage i Table Storage. Jednak konto magazynu w warstwie Premium nie jest obsługiwane.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Żądania logowania do logowania
### <a name="logging-authenticated-requests"></a>Rejestrowanie uwierzytelnionych żądań

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania przy użyciu sygnatury dostępu współdzielonego (SAS) lub protokołu OAuth, w tym żądania zakończone niepowodzeniem i zakończone powodzeniem
- Żądania dotyczące danych analityki

  Żądania wykonane przez samą analizę magazynu, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w tematach [Operacje rejestrowane i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) analizy magazynu oraz Format dziennika analizy [magazynu.](/rest/api/storageservices/storage-analytics-log-format)

### <a name="logging-anonymous-requests"></a>Rejestrowanie żądań anonimowych

 Rejestrowane są następujące typy żądań anonimowych:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy limitu czasu zarówno dla klienta, jak i serwera
- Żądania GET zakończone niepowodzeniem z kodem błędu 304 (Nie zmodyfikowano)

  Wszystkie inne żądania anonimowe nie powiodły się, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w tematach [Operacje rejestrowane i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) analizy magazynu oraz Format dziennika analizy [magazynu.](/rest/api/storageservices/storage-analytics-log-format)

## <a name="how-logs-are-stored"></a>Sposób przechowywania dzienników

Wszystkie dzienniki są przechowywane w blokowych `$logs`obiektach blob w kontenerze o nazwie , który jest tworzony automatycznie, gdy usługa Storage Analytics jest włączona dla konta magazynu. Kontener `$logs` znajduje się w obszarze nazw obiektów blob konta `http://<accountname>.blob.core.windows.net/$logs`magazynu, na przykład: . Tego kontenera nie można usunąć po włączeniu usługi Storage Analytics, ale jego zawartość można usunąć. Jeśli używasz narzędzia do przeglądania magazynu, aby przejść bezpośrednio do kontenera, zobaczysz wszystkie obiekty blob, które zawierają dane rejestrowania.

> [!NOTE]
>  Kontener `$logs` nie jest wyświetlany po wykonaniu operacji listy kontenerów, takiej jak operacja Kontenery listy. Dostęp do niego musi być dostępny bezpośrednio. Na przykład można użyć operacji Lista obiektów blob, `$logs` aby uzyskać dostęp do obiektów blob w kontenerze.

Ponieważ żądania są rejestrowane, Analiza magazynu będzie przesyłać wyniki pośrednie jako bloki. Okresowo analiza magazynu zatwierdzi te bloki i udostępni je jako obiekt blob. Może upłynąć do godziny dla danych dziennika pojawiają się w obiektach blob w kontenerze **$logs,** ponieważ częstotliwość, z jaką usługa magazynu opróżnia moduły zapisujących dziennik. Zduplikowane rekordy mogą istnieć dla dzienników utworzonych w tej samej godzinie. Można określić, czy rekord jest duplikatem, sprawdzając numer **RequestId** i **Operation.**

Jeśli masz dużą ilość danych dziennika z wieloma plikami dla każdej godziny, możesz użyć metadanych obiektu blob, aby określić, jakie dane zawiera dziennik, badając pola metadanych obiektów blob. Jest to również przydatne, ponieważ czasami może wystąpić opóźnienie, gdy dane są zapisywane w plikach dziennika: metadane obiektu blob daje dokładniejsze wskazanie zawartości obiektu blob niż nazwa obiektu blob.

Większość narzędzi do przeglądania magazynu umożliwiają wyświetlanie metadanych obiektów blob; można również odczytać te informacje za pomocą programu PowerShell lub programowo. Poniższy fragment kodu programu PowerShell jest przykładem filtrowania listy obiektów blob dziennika według nazwy, aby określić czas i przez metadane, aby zidentyfikować tylko te dzienniki, które zawierają operacje **zapisu.**  

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

Aby uzyskać informacje dotyczące programowego wyświetlania obiektów blob, zobacz [Wyliczanie zasobów obiektów blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) oraz [ustawianie i pobieranie właściwości i metadanych zasobów obiektów blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Konwencje nazewnictwa dzienników

 Każdy dziennik zostanie zapisany w następującym formacie:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 W poniższej tabeli opisano każdy atrybut w nazwie dziennika:

|Atrybut|Opis|
|---------------|-----------------|
|`<service-name>`|Nazwa usługi magazynu. Na `blob`przykład: `table`, , lub`queue`|
|`YYYY`|Czterocyfrowy rok dla dziennika. Na przykład: `2011`|
|`MM`|Dwucyfrowy miesiąc dla dziennika. Na przykład: `07`|
|`DD`|Dwucyfrowy dzień dla dziennika. Na przykład: `31`|
|`hh`|Dwucyfrowa godzina, która wskazuje godzinę początkową dzienników w formacie 24-godzinnym UTC. Na przykład: `18`|
|`mm`|Dwucyfrowy numer, który wskazuje minutę rozpoczęcia dzienników. **Uwaga:**  Ta wartość nie jest poparta w bieżącej wersji analizy magazynu, a jej wartość będzie zawsze `00`.|
|`<counter>`|Licznik od zera z sześcioma cyframi, który wskazuje liczbę obiektów blob dziennika wygenerowanych dla usługi magazynu w ciągu godziny. Ten licznik zaczyna `000000`się od . Na przykład: `000001`|

 Poniżej znajduje się pełna nazwa dziennika przykładu, który łączy powyższe przykłady:

 `blob/2011/07/31/1800/000001.log`

 Poniżej przedstawiono przykładowy identyfikator URI, który może służyć do uzyskiwania dostępu do powyższego dziennika:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Po zarejestrowaniu żądania magazynu wynikowa nazwa dziennika jest skorelowana z godziną po zakończeniu żądanej operacji. Na przykład jeśli żądanie GetBlob zostało ukończone o godzinie 18:30 w dniu 31.07.2011 r., dziennik zostanie zapisany z następującym prefiksem:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadane dziennika

 Wszystkie obiekty BLOB dziennika są przechowywane z metadanymi, które mogą służyć do identyfikowania danych rejestrowania obiektu blob zawiera. W poniższej tabeli opisano każdy atrybut metadanych:

|Atrybut|Opis|
|---------------|-----------------|
|`LogType`|Opisuje, czy dziennik zawiera informacje dotyczące operacji odczytu, zapisu lub usuwania. Ta wartość może zawierać jeden typ lub kombinację wszystkich trzech, oddzielonych przecinkami.<br /><br /> Przykład 1:`write`<br /><br /> Przykład 2:`read,write`<br /><br /> Przykład 3:`read,write,delete`|
|`StartTime`|Najwcześniejsza godzina wpisu w dzienniku, `YYYY-MM-DDThh:mm:ssZ`w formie . Na przykład: `2011-07-31T18:21:46Z`|
|`EndTime`|Ostatni czas wpisu w dzienniku, w `YYYY-MM-DDThh:mm:ssZ`formie . Na przykład: `2011-07-31T18:22:09Z`|
|`LogVersion`|Wersja formatu dziennika.|

 Na poniższej liście są wyświetlane pełne przykładowe metadane przy użyciu powyższych przykładów:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Włącz rejestrowanie magazynu

Rejestrowanie magazynu można włączyć za pomocą witryny Azure portal, powershell i zestawów SDK magazynu.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Włącz rejestrowanie magazynu przy użyciu witryny Azure portal  

W witrynie Azure portal użyj bloku **Ustawienia diagnostyki (klasyczne),** aby sterować rejestrowaniem magazynu, dostępną z sekcji **Monitorowanie (klasyczna)** **bloku Menu**konta magazynu.

Można określić usługi magazynu, które chcesz zarejestrować, oraz okres przechowywania (w dniach) dla zarejestrowanych danych.  

### <a name="enable-storage-logging-using-powershell"></a>Włączanie rejestrowania magazynu przy użyciu programu PowerShell  

 Za pomocą programu PowerShell na komputerze lokalnym można skonfigurować rejestrowanie magazynu na koncie magazynu przy użyciu polecenia cmdlet Azure PowerShell **Get-AzureStorageServiceLoggingProperty** do pobierania bieżących ustawień i **właściwości cmdlet Set-AzureStorageServiceLoggingProperty,** aby zmienić bieżące ustawienia.  

 Polecenia cmdlet, które kontrolują rejestrowanie magazynu używać **LoggingOperations** parametr, który jest ciągiem zawierającym listę typów żądań oddzielonych przecinkami do dziennika. Trzy możliwe typy żądań są **odczytywane,** **zapisywane**i **usuwane**. Aby wyłączyć rejestrowanie, należy użyć wartości **brak** dla **LoggingOperations** parametru.  

 Następujące polecenie włącza rejestrowanie żądań odczytu, zapisu i usuwania w usłudze Kolejka na domyślnym koncie magazynu z przechowywaniem ustawionym na pięć dni:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Następujące polecenie wyłącza rejestrowanie usługi tabel na domyślnym koncie magazynu:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Aby uzyskać informacje dotyczące konfigurowania poleceń cmdlet programu Azure PowerShell do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia, zobacz: [Jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Programowo włączanie rejestrowania magazynu  

 Oprócz korzystania z witryny Azure portal lub poleceń cmdlet programu Azure PowerShell do kontrolowania rejestrowania magazynu, można również użyć jednego z interfejsów API usługi Azure Storage. Na przykład jeśli używasz języka .NET, możesz użyć biblioteki klienta magazynu.  

 Klasy **CloudBlobClient**, **CloudQueueClient**i **CloudTableClient** mają metody, takie jak **SetServicePropertiesasync,** które przyjmują obiekt **ServiceProperties** jako parametr. **SetServicePropertiesAsync** Za pomocą obiektu **ServiceProperties** można skonfigurować rejestrowanie magazynu. Na przykład następujący fragment kodu języka C# pokazuje, jak zmienić to, co jest rejestrowane i okres przechowywania dla rejestrowania kolejki:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Aby uzyskać więcej informacji na temat konfigurowania rejestrowania rejestrowania magazynu przy użyciu języka .NET, zobacz [Odwołanie do biblioteki klienta magazynu](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Aby uzyskać ogólne informacje dotyczące konfigurowania rejestrowania magazynu przy użyciu interfejsu API REST, zobacz [Włączanie i konfigurowanie analizy magazynu](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Pobieranie danych dziennika rejestrowania magazynu

 Aby wyświetlić i przeanalizować dane dziennika, należy pobrać obiekty BLOB, które zawierają dane dziennika, które cię interesują na komputerze lokalnym. Wiele narzędzi do przeglądania pamięci masowej umożliwia pobieranie obiektów blob z konta magazynu; można również użyć zespołu usługi Azure Storage dostarczone wiersza polecenia Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) do pobierania danych dziennika.  
 
>[!NOTE]
> Kontener `$logs` nie jest zintegrowany z siatką zdarzeń, więc nie będzie otrzymywać powiadomień, gdy pliki dziennika są zapisywane. 

 Aby upewnić się, że pobierasz dane dziennika, które Cię interesują, i aby uniknąć pobierania tych samych danych dziennika więcej niż jeden raz:  

-   Użyj konwencji nazewnictwa daty i godziny dla obiektów blob zawierających dane dziennika, aby śledzić obiekty blob, które zostały już pobrane do analizy, aby uniknąć ponownego pobrania tych samych danych więcej niż raz.  

-   Użyj metadanych na obiektach blob zawierających dane dziennika, aby zidentyfikować określony okres, dla którego obiekt blob przechowuje dane dziennika, aby zidentyfikować dokładny obiekt blob, który należy pobrać.  

Aby rozpocząć korzystanie z AzCopy, zobacz [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md) 

W poniższym przykładzie pokazano, jak można pobrać dane dziennika dla usługi kolejki dla godzin rozpoczynających się od 09:00, 10:00 i 11:00 w dniu 20 maja 2014 r.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Aby dowiedzieć się więcej o pobieraniu określonych plików, zobacz [Pobieranie określonych plików](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Po pobraniu danych dziennika można wyświetlić wpisy dziennika w plikach. Te pliki dziennika używają rozdzielanego formatu tekstu, który wiele narzędzi do odczytu dziennika jest w stanie przeanalizować, w tym Microsoft Message Analyzer (aby uzyskać więcej informacji, zobacz przewodnik [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Różne narzędzia mają różne możliwości formatowania, filtrowania, sortowania, przeszukiwania zawartości plików dziennika. Aby uzyskać więcej informacji na temat formatu pliku dziennika rejestrowania magazynu i zawartości, zobacz [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format) i operacje rejestrowane analizy magazynu i [komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Następne kroki

* [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format)
* [Operacje rejestrowane i komunikaty o stanie analizy magazynu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Dane analizy przestrzeni dyskowej (klasyczne)](storage-analytics-metrics.md)
