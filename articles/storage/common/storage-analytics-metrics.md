---
title: Metryki analizy usługi Azure Storage (wersja klasyczna)
description: Dowiedz się, jak przy użyciu metryk w usłudze Azure Storage.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: c15242b0c480e2da39897b850ab7b2a2fd05bf11
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489293"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metryki analizy usługi Azure Storage (wersja klasyczna)

Usługa Storage Analytics może przechowywać metryki, które obejmują zagregowane transakcji statystyki oraz dane dyspozycyjności o żądaniach do usługi storage. Transakcje są zgłaszane zarówno na poziomie operacji interfejsu API, a także na poziomie usługi storage, a pojemność jest zgłaszany na poziomie usługi magazynu. Dane metryk można analizować użycie usługi magazynu, diagnozowanie problemów z żądania skierowanego do usługi storage i poprawić wydajność aplikacji, które używają usługi.  

 Metryk usługi Storage Analytics są domyślnie włączone dla nowych kont magazynu. Można skonfigurować metryki w [witryny Azure portal](https://portal.azure.com/); Aby uzyskać więcej informacji, zobacz [monitorowania na koncie magazynu w witrynie Azure portal](/azure/storage/storage-monitor-storage-account). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Aby włączyć analityka magazynu dla każdej usługi, należy użyć operacji ustawiania właściwości usługi.  

> [!NOTE]
> Storage Analytics metryki są dostępne dla usług obiektów Blob, kolejka, tabela i plik.
> Metryk usługi Storage Analytics są teraz metryki model klasyczny. Firma Microsoft zaleca używanie [metryk usługi Storage w usłudze Azure Monitor](storage-metrics-in-azure-monitor.md) zamiast metryk usługi Storage Analytics.

## <a name="transaction-metrics"></a>Metryki transakcji  
 Niezawodny zestaw danych jest rejestrowana w odstępach co godzinę i minutę dla każdej usługi storage i żądanej operacji interfejsu API, łącznie z ruchem przychodzącym/wychodzącym, dostępności, błędy i kategorii wartości procentowych żądania. Można wyświetlić pełną listę szczegółów transakcji w [schemat tabeli metryk usługi Analytics magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema) tematu.  

 Dane transakcji są rejestrowane na dwóch poziomach — poziom usługi i poziom operacji interfejsu API. Na poziomie usługi statystyki podsumowujące wszystkie żądane operacje interfejsu API są zapisywane na jednostkę tabeli co godzinę nawet wtedy, gdy żadne żądania nie zostały wprowadzone do usługi. Na poziomie operacji interfejsu API statystyki są zapisywane tylko do jednostki, jeśli zażądano operacji w ciągu danej godziny.  

 Na przykład, jeśli wykonujesz **GetBlob** operacji w usłudze Blob Storage Analytics Metrics będzie rejestrować żądania i uwzględnić go w zagregowanych danych dla usługi Blob, jak również **GetBlob** Operacja. Jednakże jeśli nie **GetBlob** operacji jest wymagana w ciągu godziny, jednostki nie będą zapisywane do *$MetricsTransactionsBlob* dla tej operacji.  

 Metryki transakcji są rejestrowane dla żądań użytkowników i żądań wysyłanych przez analityka magazynu, sam. Na przykład żądania przez analityka magazynu do zapisywania dzienników i jednostek tablic są rejestrowane.

## <a name="capacity-metrics"></a>Metryki wydajności  

> [!NOTE]
>  Obecnie metryki pojemności są dostępne tylko dla usługi Blob.

 Dane dotyczące pojemności są rejestrowane codziennie dla konta magazynu usługi Blob, a dwie jednostki z tabeli są zapisywane. Jednej jednostki zawiera dane statystyczne danych przez użytkownika, a drugi zawiera dane statystyczne dotyczące `$logs` kontenera obiektów blob używane przez analityka magazynu. *$MetricsCapacityBlob* tabela zawiera następujące statystyki:  

- **Pojemność**: Ilość miejsca używanego przez usługę obiektów Blob konta magazynu, w bajtach.  
- **ContainerCount**: Liczba kontenerów obiektów blob w usłudze obiektów Blob konta magazynu.  
- **ObjectCount**: Liczba zatwierdzone i niezatwierdzone blokowy lub stronicowy obiekty BLOB w usłudze obiektów Blob konta magazynu.  

  Aby uzyskać więcej informacji na temat metryki pojemności, zobacz [schemat tabeli metryk usługi Analytics magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak są przechowywane metryki  

 Wszystkie dane metryk dla wszystkich usług magazynu są przechowywane w trzech tabelach zarezerwowane dla usługi: jednej tabeli, aby uzyskać informacje o transakcji, jednej tabeli, aby uzyskać informacje o transakcji na minutę i informacje o pojemności z innej tabeli. Informacje o transakcji transakcji i minutowe składa się z danych żądań i odpowiedzi, i informacje o pojemności składa się z magazynu danych użycia. Metryki godzin, metryki minut i pojemność magazynu, który jest możliwy obiektu Blob dla konta usługi jest w tabelach, które są nazwane zgodnie z opisem w poniższej tabeli.  

|Poziom metryki|Nazwy tabel|Obsługiwane w wersjach|  
|-------------------|-----------------|----------------------------|  
|Godzinowe metryki, Lokalizacja podstawowa|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Wersje przed 2013-08-15 tylko. Te nazwy są nadal obsługiwane, zaleca się przejście na poniższych tabel.|  
|Godzinowe metryki, Lokalizacja podstawowa|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Wszystkie wersje. Pomoc dotycząca metryk usług plików jest dostępne tylko w wersji 2015-04-05 i nowszych wersjach.|  
|Metryki minut lokalizacji głównej|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Wszystkie wersje. Pomoc dotycząca metryk usług plików jest dostępne tylko w wersji 2015-04-05 i nowszych wersjach.|  
|Godzinowe metryki, lokalizacja pomocnicza|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Wszystkie wersje. Musi być włączona replikacja geograficznie nadmiarowa geograficznie nadmiarowy.|  
|Metryki minut dodatkowej lokalizacji|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Wszystkie wersje. Musi być włączona replikacja geograficznie nadmiarowa geograficznie nadmiarowy.|  
|Pojemność (tylko w przypadku usługi obiektów Blob)|$MetricsCapacityBlob|Wszystkie wersje.|  

 Te tabele są tworzone automatycznie, gdy analityka magazynu jest włączona dla punktu końcowego usługi magazynu. Uzyskiwania do nich za pośrednictwem obszaru nazw konta magazynu, na przykład: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Tabulky metrik nie występują na liście Operacja tworzenia listy i muszą być dostępne bezpośrednio za pomocą nazwy tabeli.  

## <a name="enable-metrics-using-the-azure-portal"></a>Włącz metryki w witrynie Azure portal
Wykonaj następujące kroki, aby włączyć metryki w [witryny Azure portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu.
1. Wybierz **ustawienia diagnostyczne (klasyczne)** z **Menu** okienka.
1. Upewnij się, że **stan** ustawiono **na**.
1. Wybierz metryki dla usługi, które chcesz monitorować.
1. Określ zasady przechowywania, aby wskazać, jak długo zachowuje metryki i rejestrowanie danych.
1. Wybierz pozycję **Zapisz**.

[Witryny Azure portal](https://portal.azure.com) nie obecnie umożliwiają skonfigurowanie metryki minut w ramach konta magazynu; należy włączyć metryki minut przy użyciu programu PowerShell lub programowo.

> [!NOTE]
>  Należy pamiętać, że witryny Azure portal nie obecnie umożliwiają skonfigurowanie metryki minut na koncie magazynu. Należy włączyć metryki minut przy użyciu programu PowerShell lub programowo.

## <a name="enable-storage-metrics-using-powershell"></a>Włączanie metryk usługi Storage przy użyciu programu PowerShell  
PowerShell na komputerze lokalnym służy do konfigurowania metryk usługi Storage na koncie magazynu za pomocą polecenia cmdlet programu Azure PowerShell **Get AzureStorageServiceMetricsProperty** można pobrać bieżące ustawienia i polecenia cmdlet  **Zestaw AzureStorageServiceMetricsProperty** do zmiany bieżących ustawień.  

Polecenia cmdlet, które kontrolują metryk usługi Storage, użyj następujących parametrów:  

* **Typ ServiceType**, możliwe wartości są **Blob**, **kolejki**, **tabeli**, i **pliku**.
* **MetricsType**, możliwe wartości to **godzinę** i **minutę**.  
* **MetricsLevel**, możliwe wartości to:
* **Brak**: Wyłącza monitorowanie.
* **Usługa**: Zbiera metryki, takie jak ruch przychodzący i wychodzący, dostępności, opóźnienia i procent powodzenia, które są agregowane dla obiektów blob, kolejek, tabel i usług plików.
* **ServiceAndApi**: Oprócz metryk usług gromadzi informacje o ten sam zestaw metryk dla każdej operacji magazynu w interfejsie API usługi Azure Storage.

Na przykład następujące polecenie zmienia się na metryki minut dla usługi blob na koncie magazynu domyślnego przechowywania ustawionej pięć dni:  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

Następujące polecenie pobiera bieżący godzinowe metryki poziomu i przechowywania dni na usługę blob service na koncie magazynu domyślnego:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Aby uzyskać informacje o sposobie konfigurowania poleceń cmdlet programu Azure PowerShell do pracy z subskrypcją platformy Azure i jak wybrać domyślne konto magazynu do użycia, zobacz: [Jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Programowe Włączanie metryk usługi Storage  
Oprócz używania witryny Azure portal lub poleceń cmdlet programu Azure PowerShell do sterowania metryk usługi Storage, umożliwia także jeden z interfejsów API usługi Azure Storage. Na przykład jeśli używasz języka .NET przy użyciu biblioteki klienta magazynu.  

Klasy **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**, i **CloudFileClient** wszystkie mają metody takie jak  **SetServiceProperties** i **SetServicePropertiesAsync** o **ServiceProperties** obiektu jako parametr. Możesz użyć **ServiceProperties** obiektu, aby skonfigurować metryk usługi Storage. Na przykład następująca C# fragment kodu przedstawia sposób zmiany dni poziom i przechowywania metryki godzinowe metryki kolejki:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Aby uzyskać więcej informacji na temat konfigurowania metryk usługi Storage za pomocą języka .NET, zobacz [biblioteki klienta usługi Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Aby uzyskać ogólne informacje o konfigurowaniu metryk usługi Storage przy użyciu interfejsu API REST, zobacz [Włączanie i konfigurowanie Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Wyświetlanie metryk usługi Storage  
Po skonfigurowaniu metryki Storage Analytics do monitorowania konta magazynu, usługa Storage Analytics rejestruje metryki w dobrze znanych tabelach na koncie magazynu. Można skonfigurować wykresy, aby wyświetlić metryki godzinowe w [witryny Azure portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu w [witryny Azure portal](https://portal.azure.com).
1. Wybierz **metryki (klasyczne)** w **Menu** bloku usługi metryki, którego chcesz wyświetlić.
1. Kliknij wykres, który chcesz skonfigurować.
1. W **Edytuj wykres** bloku wybierz **zakres czasu**, **typ wykresu**i metryk, które mają być wyświetlane na wykresie.

W **monitorowanie (wersja klasyczna)** sekcji bloku menu konta magazynu w witrynie Azure portal, można skonfigurować [reguły alertów](#metrics-alerts), takie jak wysyłanie wiadomości e-mail alertów, aby otrzymywać powiadomienia, jeśli określona Metryka osiągnie Określona wartość.

Jeśli chcesz pobrać metryk w celu przechowywania długoterminowego lub analizować je lokalnie, należy użyć narzędzia lub pisanie kodu służącego do odczytu w tabelach. Należy pobrać metryki minut do analizy. Tabele nie są wyświetlane, jeśli lista wszystkich tabel na koncie magazynu, ale można go wywołać bezpośrednio przez nazwę. Wiele narzędzi do przeglądania magazynu zdawali sobie sprawę z tych tabel i można wyświetlać je bezpośrednio (zobacz [usługi Azure Storage Client Tools](/azure/storage/storage-explorers) listę dostępnych narzędzi).

||||  
|-|-|-|  
|**Metryki**|**Nazwy tabel**|**Uwagi**|  
|Godzinowe metryki|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|W wersjach wcześniejszych niż 2013-08-15 te tabele były znane jako:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metryki dla usługi plików są dostępne od wersji 2015-04-05.|  
|Metryki minut|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Można włączyć tylko przy użyciu programu PowerShell lub programowo.<br /><br /> Metryki dla usługi plików są dostępne od wersji 2015-04-05.|  
|Pojemność|$MetricsCapacityBlob|Tylko usługi obiektów blob.|  

Możesz znaleźć szczegółowe informacje ze schematów dla tych tabel w [schemat tabeli metryk usługi Analytics magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema). Poniższe wiersze przykładowe pokazują tylko podzestaw kolumn, które są dostępne, ale zilustrowania niektórych ważnych funkcji sposób, w jaki metryk usługi Storage zapisuje te metryki:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**właściwości partitionKey**|**RowKey**|**Sygnatura czasowa**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Dostępność**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|Użytkownik; QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

W przykładowe dane metryki minut klucza partycji używa czasu w rozdzielczości minuty. Klucz wiersza określa typ informacji przechowywanych w wierszu i składa się z dwóch części informacji, typ dostępu i typ żądania:  

-   Typ dostępu jest **użytkownika** lub **systemu**, gdzie **użytkownika** odnosi się do wszystkich żądań użytkowników do usługi storage i **systemu** odwołuje się do żądań wysyłanych przez analityka magazynu.  

-   Typ żądania jest **wszystkich** w którym to przypadku jest to wiersz podsumowania lub identyfikuje określonych interfejsów API takich jak **QueryEntity** lub **UpdateEntity**.  

Powyższe przykładowe dane wyświetlane są wszystkie rekordy na minutę (rozpoczyna się od 11:00:00), więc liczba **QueryEntities** żądań oraz liczbę **QueryEntity** żądań oraz liczbę  **UpdateEntity** żądania Dodaj maksymalnie siedem, czyli sumę wyświetlane na **użytkownika: All** wiersza. Podobnie możesz wywodzić średnie opóźnienie end-to-end 104.4286 na **użytkownika: All** wiersza, obliczając ((143.8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Alerty metryki
Należy rozważyć skonfigurowanie alertów w [witryny Azure portal](https://portal.azure.com) , dzięki czemu można będzie automatycznie powiadamiany o ważnych zmian w działaniu usług magazynu. Jeśli używasz narzędzia Eksploratora magazynu można pobrać te dane metryk w formacie rozdzielanym, można użyć programu Microsoft Excel do analizowania danych. Zobacz [usługi Azure Storage Client Tools](/azure/storage/storage-explorers) lista narzędzi Eksploratora dostępnego magazynu. Można skonfigurować alerty w **Alert (klasyczny)** bloku, dostępny w ramach **monitorowanie (wersja klasyczna)** w bloku menu konta magazynu.

> [!IMPORTANT]
> Może to być opóźnienie między zdarzenia magazynu, a kiedy jest rejestrowany odpowiadające im dane metryk co godzinę lub minut. W przypadku metryki minut kilka minut danych mogą być zapisane tylko raz. Może to prowadzić do transakcji z wcześniejszych minuty są agregowane do transakcji w ciągu bieżącej minuty. W takiej sytuacji usługa alertu dotyczącego nie może mieć wszystkie dane dostępne metryki dla skonfigurowany interwał alertów, co może prowadzić do nieoczekiwanego wyzwalania alertów.
>

## <a name="accessing-metrics-data-programmatically"></a>Programowe uzyskiwanie dostępu do danych metryk  
Poniżej przedstawiono przykładowy kod C#, która uzyskuje dostęp do metryki minut dla zakresu minut, a następnie wyświetla wyniki w oknie konsoli. Przykładowy kod używa biblioteki klienta usługi Azure Storage w wersji 4.x lub nowszej, która obejmuje **CloudAnalyticsClient** klasy, która upraszcza dostęp do metryk tabel w magazynie.  

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

## <a name="billing-on-storage-metrics"></a>Rozliczenia na metryk usługi storage  
Zapisu żądań w celu utworzenia tabeli jednostki dla metryk są naliczane zgodnie ze standardowymi stawkami mające zastosowanie do wszystkich operacji usługi Azure Storage.  

Żądania odczytu i usuwania danych metryk przez klienta także są płatne zgodnie ze standardowymi stawkami. Jeśli skonfigurowano zasady przechowywania danych nie są naliczane w przypadku usługi Azure Storage usuwa stare dane metryk. Jednak jeśli usuniesz analizy danych, Twoje konto jest rozliczane za operacje usuwania.  

Pojemność posługują się tabulky metrik jest również płatne. Następujące umożliwia szacowanie ilości wydajności używany do przechowywania danych metryk:  

-   Jeśli co godzinę usługa korzysta z każdego interfejsu API w każdej usługi, następnie 148KB danych są przechowywane w tabeli metryk transakcji na godzinę po włączeniu zarówno usługi interfejsu API poziomie i podsumowania.  
-   Jeśli w ciągu każdej godziny, usługa korzysta z każdego interfejsu API w usłudze, a następnie 12KB danych są przechowywane w tabeli metryk transakcji na godzinę, po włączeniu podsumowanie po prostu poziomu usług.  
-   Tabeli pojemności dla obiektów blob ma dwa wiersze dodane każdego dnia, pod warunkiem możesz mieć wyrażeniu zgody na uczestnictwo w dla dzienników. Oznacza to, że codziennie, rozmiar tej tabeli zwiększa się o około 300 bajtów.

## <a name="next-steps"></a>Kolejne kroki
* [Jak monitorować konta magazynu](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schemat tabeli metryk usługi Analytics magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Usługa Storage Analytics rejestrowane komunikaty o stanie i operacje](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Rejestrowanie danych analizy magazynu](storage-analytics-logging.md)
