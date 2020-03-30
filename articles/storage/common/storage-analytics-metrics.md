---
title: Metryki analizy usługi Azure Storage (klasyczne)
description: Dowiedz się, jak używać metryk w usłudze Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268407"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metryki analizy usługi Azure Storage (klasyczne)

Usługa Storage Analytics może przechowywać dane, które zawierają zagregowane statystyki transakcji i dane pojemności dotyczące żądań do usługi magazynu. Transakcje są zgłaszane zarówno na poziomie operacji interfejsu API, jak i na poziomie usługi magazynu, a pojemność jest raportowana na poziomie usługi magazynu. Dane metryki mogą służyć do analizowania użycia usługi magazynu, diagnozowania problemów z żądaniami dla usługi magazynu i poprawy wydajności aplikacji korzystających z usługi.  

 Metryki analizy magazynu są domyślnie włączone dla nowych kont magazynu. Metryki można skonfigurować w [witrynie Azure portal;](https://portal.azure.com/) aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w witrynie Azure portal](/azure/storage/storage-monitor-storage-account). Można również włączyć usługi Storage Analytics programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj operacji Ustaw właściwości usługi, aby włączyć analizę magazynu dla każdej usługi.  

> [!NOTE]
> Metryki analizy magazynu są dostępne dla usług obiektów Blob, Queue, Table i File.
> Dane analizy przestrzeni dyskowej są teraz metrykami klasycznymi. Firma Microsoft zaleca używanie [metryk magazynu w usłudze Azure Monitor](storage-metrics-in-azure-monitor.md) zamiast metryk usługi Storage Analytics.

## <a name="transaction-metrics"></a>Metryki transakcji  
 Niezawodny zestaw danych jest rejestrowany w odstępach godzinowych lub minutowych dla każdej usługi magazynu i żądanej operacji interfejsu API, w tym transferu danych przychodzących/wychodzących, dostępności, błędów i sklasyfikowanych wartości procentowych żądań. Pełną listę szczegółów transakcji można wyświetlić w temacie [Schemat tabeli metryk analizy magazynu.](/rest/api/storageservices/storage-analytics-metrics-table-schema)  

 Dane transakcji są rejestrowane na dwóch poziomach — na poziomie usługi i na poziomie operacji interfejsu API. Na poziomie usługi statystyki podsumowujące wszystkie żądane operacje interfejsu API są zapisywane w jednostce tabeli co godzinę, nawet jeśli nie zostały wysłane żadne żądania do usługi. Na poziomie operacji interfejsu API statystyki są zapisywane tylko do jednostki, jeśli operacja została zażądana w ciągu tej godziny.  

 Na przykład jeśli wykonasz operację **GetBlob** w usłudze obiektów Blob, metryki analizy magazynu zarejestruje żądanie i dołączą go do zagregowanych danych zarówno dla usługi obiektów Blob, jak i operacji **GetBlob.** Jednak jeśli nie **GetBlob** operacji jest wymagane w ciągu godziny, jednostka nie zostaną zapisane w *$MetricsTransactionsBlob* dla tej operacji.  

 Metryki transakcji są rejestrowane zarówno dla żądań użytkowników, jak i żądań składanych przez samą analizę magazynu. Na przykład żądania przez usługi Storage Analytics do pisania dzienników i jednostek tabeli są rejestrowane.

## <a name="capacity-metrics"></a>Metryki pojemności  

> [!NOTE]
>  Obecnie metryki pojemności są dostępne tylko dla usługi obiektów Blob.

 Dane pojemności są rejestrowane codziennie dla usługi obiektów Blob konta magazynu, a dwie jednostki tabeli są zapisywane. Jedna jednostka udostępnia statystyki dla danych użytkownika, a `$logs` druga udostępnia statystyki dotyczące kontenera obiektów blob używanego przez usługi Storage Analytics. Tabela *$MetricsCapacityBlob* zawiera następujące statystyki:  

- **Pojemność:** ilość miejsca do magazynowania używanego przez usługę obiektów Blob konta magazynu w bajtach.  
- **ContainerCount:** Liczba kontenerów obiektów blob w usłudze obiektów Blob konta magazynu.  
- **ObjectCount:** Liczba zatwierdzonych i niezatwierdzonych bloków lub stronicowych obiektów blob w usłudze obiektów Blob konta magazynu.  

  Aby uzyskać więcej informacji na temat metryk pojemności, zobacz [Schemat tabeli metryk analizy magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Sposób przechowywania danych  

 Wszystkie dane metryki dla każdej z usług magazynu są przechowywane w trzech tabelach zarezerwowanych dla tej usługi: jedna tabela dla informacji o transakcjach, jedna tabela dla minutowych informacji o transakcji i inna tabela informacji o pojemności. Informacje o transakcji i transakcji minutowej składają się z danych żądania i odpowiedzi, a informacje o pojemności składają się z danych użycia magazynu. Metryki godzin, metryki minut i pojemność usługi obiektów Blob konta magazynu są dostępne w tabelach o nazwie opisanej w poniższej tabeli.  

|Poziom metryki|Nazwy tabel|Obsługiwane dla wersji|  
|-------------------|-----------------|----------------------------|  
|Metryki godzinowe, lokalizacja podstawowa|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Wersje sprzed 2013-08-15 tylko. Chociaż te nazwy są nadal obsługiwane, zaleca się przełączenie się do korzystania z tabel wymienionych poniżej.|  
|Metryki godzinowe, lokalizacja podstawowa|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 2015-04-05 i nowszej.|  
|Dane minutowe, lokalizacja podstawowa|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 2015-04-05 i nowszej.|  
|Metryki godzinowe, lokalizacja dodatkowa|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficzna dostępu do odczytu musi być włączona.|  
|Dane minutowe, lokalizacja dodatkowa|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficzna dostępu do odczytu musi być włączona.|  
|Pojemność (tylko usługa obiektów blob)|$MetricsCapacityBlob|Wszystkie wersje.|  

 Te tabele są tworzone automatycznie, gdy usługa Storage Analytics jest włączona dla punktu końcowego usługi magazynu. Są one dostępne za pośrednictwem obszaru nazw konta `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`magazynu, na przykład: . Tabele metryk nie są wyświetlane w operacji aukcji i muszą być dostępne bezpośrednio za pomocą nazwy tabeli.  

## <a name="enable-metrics-using-the-azure-portal"></a>Włączanie metryk przy użyciu witryny Azure portal
Wykonaj następujące kroki, aby włączyć metryki w [witrynie Azure portal:](https://portal.azure.com)

1. Przejdź do swojego konta magazynu.
1. Z okienka **Menu** wybierz **pozycję Ustawienia diagnostyki (klasyczne).**
1. Upewnij się, że **stan** jest ustawiony **na Wł.**
1. Wybierz metryki dla usług, które chcesz monitorować.
1. Określ zasady przechowywania, aby wskazać, jak długo zachować metryki i dane dziennika.
1. Wybierz **pozycję Zapisz**.

[Witryna Azure portal](https://portal.azure.com) nie umożliwia obecnie konfigurowania metryk minut na koncie magazynu; należy włączyć metryki minut przy użyciu programu PowerShell lub programowo.

## <a name="enable-storage-metrics-using-powershell"></a>Włączanie metryk magazynu przy użyciu programu PowerShell  
Za pomocą programu PowerShell na komputerze lokalnym można skonfigurować metryki magazynu na koncie magazynu przy użyciu polecenia cmdlet Azure PowerShell **Get-AzStorageServiceMetricsProperty** do pobierania bieżących ustawień i **właściwości cmdlet Set-AzStorageServiceMetricsProperty,** aby zmienić bieżące ustawienia.  

Polecenia cmdlet sterujące metrykami magazynu używają następujących parametrów:  

* **ServiceType**, możliwa wartość to **Blob**, **Queue**, **Table**i **File**.
* **MetrykiTyty**, możliwe wartości **to Godzina** i **Minuta**.  
* **MetrykiLevel**, możliwe wartości to:
* **Brak:** Wyłącza monitorowanie.
* **Usługa**: Zbiera metryki, takie jak ruch przychodzący/wychodzący, dostępność, opóźnienie i procenty sukcesu, które są agregowane dla obiektów blob, kolejki, tabeli i usług plików.
* **ServiceAndApi:** Oprócz metryki usługi, zbiera ten sam zestaw metryk dla każdej operacji magazynu w interfejsie API usługi Azure Storage.

Na przykład następujące polecenie przełącza metryki minut dla usługi obiektu blob na koncie magazynu z okresem przechowywania ustawionym na pięć dni: 

> [!NOTE]
> To polecenie zakłada, że zalogowano się do `Connect-AzAccount` subskrypcji platformy Azure za pomocą polecenia.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Zastąp `<resource-group-name>` wartość zastępczą nazwą grupy zasobów.
        
* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.



Następujące polecenie pobiera bieżący poziom metryk godzinowych i dni przechowywania dla usługi obiektu blob na domyślnym koncie magazynu:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Aby uzyskać informacje dotyczące konfigurowania poleceń cmdlet programu Azure PowerShell do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia, zobacz: [Jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Programowo włączanie metryk magazynu  
Oprócz korzystania z witryny Azure portal lub poleceń cmdlet programu Azure PowerShell do kontrolowania metryk magazynu, można również użyć jednego z interfejsów API usługi Azure Storage. Na przykład jeśli używasz języka .NET, możesz użyć biblioteki klienta magazynu.  

Klasy **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**i **CloudFileClient** mają metody, takie jak **SetServiceProperties** i **SetServicePropertiesAsync,** które przyjmują **obiekt ServiceProperties** jako parametr. Za pomocą **obiektu ServiceProperties** można skonfigurować metryki magazynu. Na przykład następujący fragment kodu języka C# pokazuje, jak zmienić poziom metryki i dni przechowywania dla metryk kolejki godzinowej:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Aby uzyskać więcej informacji na temat konfigurowania metryk magazynu przy użyciu języka .NET, zobacz [Biblioteka klienta magazynu dla platformy .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Aby uzyskać ogólne informacje dotyczące konfigurowania metryk magazynu przy użyciu interfejsu API REST, zobacz [Włączanie i konfigurowanie analizy magazynu](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Wyświetlanie danych magazynu  
Po skonfigurowaniu metryk analizy magazynu w celu monitorowania konta magazynu, Storage Analytics rejestruje metryki w zestawie dobrze znanych tabel na koncie magazynu. Można skonfigurować wykresy do wyświetlania metryk godzinowych w [witrynie Azure portal:](https://portal.azure.com)

1. Przejdź do konta magazynu w [witrynie Azure portal](https://portal.azure.com).
1. Wybierz **metryki (klasyczne)** w **menu** bloku dla usługi, której metryki chcesz wyświetlić.
1. Kliknij wykres, który chcesz skonfigurować.
1. W bloku **Edytuj wykres** wybierz **zakres czasu**, **Typ wykresu**i metryki, które chcesz wyświetlić na wykresie.

W sekcji **Monitorowanie (klasyczne)** w bloku menu konta magazynu w witrynie Azure portal można skonfigurować [reguły alertów,](#metrics-alerts)takie jak wysyłanie alertów e-mail, aby powiadomić cię, gdy określona metryka osiągnie określoną wartość.

Jeśli chcesz pobrać metryki dla długoterminowego przechowywania lub analizować je lokalnie, należy użyć narzędzia lub napisać kod, aby odczytać tabele. Należy pobrać dane minut do analizy. Tabele nie są wyświetlane, jeśli lista wszystkich tabel na koncie magazynu, ale można uzyskać do nich dostęp bezpośrednio według nazwy. Wiele narzędzi do przeglądania magazynu są świadomi tych tabel i umożliwiają wyświetlanie ich bezpośrednio (zobacz [Narzędzia klienta usługi Azure Storage](/azure/storage/storage-explorers) dla listy dostępnych narzędzi).

||||  
|-|-|-|  
|**Metryki**|**Nazwy tabel**|**Uwagi**|  
|Metryki godzinowe|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|W wersjach wcześniejszych niż 2013-08-15 tabele te były znane jako:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metryki dla usługi Plik są dostępne począwszy od wersji 2015-04-05.|  
|Dane minutowe|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Można włączyć tylko za pomocą programu PowerShell lub programowo.<br /><br /> Metryki dla usługi Plik są dostępne począwszy od wersji 2015-04-05.|  
|Pojemność|$MetricsCapacityBlob|Tylko usługa obiektów blob.|  

Szczegółowe informacje na temat schematów dla tych tabel można znaleźć w [schemacie tabeli metryk](/rest/api/storageservices/storage-analytics-metrics-table-schema)analizy magazynu . Przykładowe wiersze poniżej pokazują tylko podzbiór dostępnych kolumn, ale ilustrują niektóre ważne funkcje sposobu, w jaki metryki magazynu zapisują te metryki:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Sygnatura czasowa**|**TotalRequests**|**TotalBillableRequests (Liczba zaliczek na zobowiązania do)**|**TotalIngress**|**TotalEgress**|**Dostępność**|**AverageE2ELatency**|**AverageServerLatency**|**ProcentSukces**|  
|20140522T1100|użytkownika; Wszystkie|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|użytkownika; QueryEntities (Cechy zapytania)|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|użytkownika; QueryEntity (wciążk.|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|użytkownika; Aktualizujentość|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

W tym przykładzie minut metryki danych klucz partycji używa czasu w rozdzielczości minut. Klucz wiersza identyfikuje typ informacji, który jest przechowywany w wierszu i składa się z dwóch informacji, typu dostępu i typu żądania:  

-   Typ dostępu jest **użytkownikiem** lub **systemem**, gdzie **użytkownik** odwołuje się do wszystkich żądań użytkownika do usługi magazynu, a **system** odnosi się do żądań składanych przez usługę Storage Analytics.  

-   Typ żądania jest **albo wszystkim,** w którym to przypadku jest to wiersz podsumowania lub identyfikuje określony interfejs API, taki jak **QueryEntity** lub **UpdateEntity**.  

Powyższe przykładowe dane pokazują wszystkie rekordy dla jednej minuty (począwszy od 11:00), więc liczba **żądań QueryEntities** plus liczba żądań **QueryEntity** plus liczba żądań **UpdateEntity** sumują się do siedmiu, co jest sumą wyświetlaną w wierszu **user:All.** Podobnie można wyprowadzić średnie opóźnienie end-to-end 104.4286 na **user:All** wiersza przez obliczenie ((143.8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Alerty dotyczące metryk
Należy rozważyć skonfigurowanie alertów w [witrynie Azure portal,](https://portal.azure.com) dzięki czemu zostaniesz automatycznie powiadomiony o ważnych zmian w zachowaniu usług magazynu. Jeśli do pobierania tych danych w rozdzielonych formatach jest używane narzędzie do eksplorowania magazynu, można użyć programu Microsoft Excel do analizowania danych. Zobacz [Narzędzia klienta usługi Azure Storage,](/azure/storage/storage-explorers) aby uzyskać listę dostępnych narzędzi eksploratora magazynu. Alerty można skonfigurować w bloku **Alert (klasyczny),** dostępny w obszarze **Monitorowanie (klasyczny)** w bloku menu konta magazynu.

> [!IMPORTANT]
> Może wystąpić opóźnienie między zdarzeniem magazynu i po zarejestrowaniu odpowiednich danych metryk godzinowych lub minutowych. W przypadku metryk minutowych kilka minut danych może być zapisywanych jednocześnie. Może to prowadzić do transakcji z wcześniejszych minut są agregowane do transakcji dla bieżącej minuty. W takim przypadku usługa alertów może nie mieć wszystkich dostępnych danych metryk dla skonfigurowanych interwałów alertów, co może prowadzić do nieoczekiwanego wypalania alertów.
>

## <a name="accessing-metrics-data-programmatically"></a>Programowy uzyskiwanie dostępu do danych metryk  
Na poniższej liście przedstawiono przykładowy kod języka C#, który uzyskuje dostęp do metryk minut dla zakresu minut i wyświetla wyniki w oknie konsoli. Przykładowy kod używa biblioteki klienta usługi Azure Storage w wersji 4.x lub nowszej, która zawiera **cloudanalyticsClient** klasy, która upraszcza dostęp do tabel metryk w magazynie.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Rozliczanie danych dotyczących magazynu  
Żądania zapisu do tworzenia jednostek tabeli dla metryk są naliczane według stawek standardowych mających zastosowanie do wszystkich operacji usługi Azure Storage.  

Odczyt i usunięcie żądań danych metryk przez klienta są również rozliczane według stawek standardowych. Jeśli skonfigurowano zasady przechowywania danych, nie są naliczane, gdy usługa Azure Storage usuwa stare dane metryk. Jeśli jednak usuniesz dane analityczne, twoje konto zostanie obciążone kosztami operacji usuwania.  

Pojemność używana przez tabele metryk jest również rozliczana. Aby oszacować ilość pojemności używanej do przechowywania danych metryk, można użyć następujących wartości:  

-   Jeśli co godzinę usługa korzysta z każdego interfejsu API w każdej usłudze, około 148 KB danych jest przechowywanych co godzinę w tabelach transakcji metryk, jeśli włączono podsumowanie na poziomie usługi i interfejsu API.  
-   Jeśli w ciągu każdej godziny usługa korzysta z każdego interfejsu API w usłudze, około 12 KB danych jest przechowywanych co godzinę w tabelach transakcji metryk, jeśli włączono tylko podsumowanie poziomu usługi.  
-   Tabela pojemności dla obiektów blob ma dwa wiersze dodawane każdego dnia, pod warunkiem, że masz opt-in dla dzienników. Oznacza to, że każdego dnia rozmiar tej tabeli zwiększa się nawet o około 300 bajtów.

## <a name="next-steps"></a>Następne kroki
* [Jak monitorować konto magazynu](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schemat tabeli metryk analizy magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Operacje rejestrowane i komunikaty o stanie analizy magazynu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Rejestrowanie analityki magazynu](storage-analytics-logging.md)
