---
title: Włączenie metryk usługi storage w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Jak włączyć metryki magazynu usługi Blob, kolejki, tabela i plik
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 3fc2ebe30a9be685c62e46e351e3958eefdd6ac7
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417755"
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Włączanie metryk usługi Azure Storage i wyświetlanie danych metryk
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Przegląd
Metryki magazynu jest domyślnie włączona, podczas tworzenia nowego konta magazynu. Możesz skonfigurować monitorowanie za pomocą [witryny Azure portal](https://portal.azure.com) lub środowiska Windows PowerShell, lub programowo przy użyciu jednej z bibliotek klienckich magazynu.

Można skonfigurować okres przechowywania dla danych metryk: okres ten określa, ile magazynu usługa zapewnia metryki i opłaty za miejsce wymagane do ich przechowywania. Zazwyczaj należy użyć krótszy okres przechowywania dla metryki minut niż godzinowe metryki ze względu na znaczne dodatkowe miejsce wymagane dla metryki minut. Wybierz okres przechowywania w taki sposób, że masz wystarczająco dużo czasu, aby analizować dane i pobrać wszelkie metryk, które chcesz zachować dla celów raportowania lub analizy offline. Należy pamiętać, możesz również są rozliczane pobierania danych metryk z konta magazynu.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Jak włączyć metryki w witrynie Azure portal
Wykonaj następujące kroki, aby włączyć metryki w [witryny Azure portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu.
1. Wybierz **diagnostyki** z **Menu** okienka.
1. Upewnij się, że **stan** ustawiono **na**.
1. Wybierz metryki dla usługi, które chcesz monitorować.
1. Określ zasady przechowywania, aby wskazać, jak długo zachowuje metryki i rejestrowanie danych.
1. Wybierz pozycję **Zapisz**.

[Witryny Azure portal](https://portal.azure.com) nie obecnie umożliwiają skonfigurowanie metryki minut w ramach konta magazynu; należy włączyć metryki minut przy użyciu programu PowerShell lub programowo.

## <a name="how-to-enable-metrics-using-powershell"></a>Jak włączyć metryki za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell na komputerze lokalnym służy do konfigurowania metryk usługi Storage na koncie magazynu przy użyciu programu Azure PowerShell polecenia cmdlet Get-AzStorageServiceMetricsProperty, aby pobrać bieżące ustawienia i polecenia cmdlet Set-AzStorageServiceMetricsProperty Aby zmienić bieżących ustawień.

Polecenia cmdlet, które kontrolują metryk usługi Storage, użyj następujących parametrów:

* MetricsType: możliwe wartości to godzinowe i minutowe.
* Typ ServiceType: możliwe wartości to obiektów Blob, kolejek i tabel.
* MetricsLevel: możliwe wartości to None, usługi i ServiceAndApi.

Na przykład następujące polecenie zmienia się na metryki minut dla usługi Blob na koncie magazynu domyślnego przechowywania ustawionej pięć dni:

```powershell
$storagecontext = New-AzStorageContext -StorageAccountName <storageaccountname> -StorageAccountKey <storageaccountkey>

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`  -Context $storagecontext.context
```

Następujące polecenie pobiera bieżący godzinowe metryki poziomu i przechowywania dni na usługę blob service na koncie magazynu domyślnego:

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Aby uzyskać informacje o sposobie konfigurowania poleceń cmdlet programu Azure PowerShell do pracy z subskrypcją platformy Azure i jak wybrać domyślne konto magazynu do użycia, zobacz: [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Jak włączyć metryki magazynu, programowo
Poniższy fragment kodu języka C# pokazano, jak włączyć metryki i rejestrowanie dla usługi Blob przy użyciu biblioteki klienta usługi storage dla platformy .NET:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Wyświetlanie metryk usługi Storage
Po skonfigurowaniu metryki Storage Analytics do monitorowania konta magazynu, usługa Storage Analytics rejestruje metryki w dobrze znanych tabelach na koncie magazynu. Można skonfigurować wykresy, aby wyświetlić metryki godzinowe w [witryny Azure portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu w [witryny Azure portal](https://portal.azure.com).
1. Wybierz **metryki** w **Menu** okienko usługi metryki, którego chcesz wyświetlić.
1. Wybierz **Edytuj** na wykresie, którą chcesz skonfigurować.
1. W **Edytuj wykres** okienku wybierz **zakres czasu**, **typ wykresu**i metryk, które mają być wyświetlane na wykresie.
1. Kliknij przycisk **OK**

Jeśli chcesz pobrać metryk w celu przechowywania długoterminowego lub analizować je lokalnie, musisz:

* Za pomocą narzędzia, który zna te tabele i będzie można wyświetlać i pobierać je.
* Zapis niestandardową aplikację lub skrypt, aby odczytywać i przechowywanie tabel.

Wiele firm magazynu narzędzi do przeglądania zdawali sobie sprawę z tych tabel i można wyświetlać je bezpośrednio.
Zobacz [usługi Azure Storage Client Tools](storage-explorers.md) listę dostępnych narzędzi.

> [!NOTE]
> Począwszy od wersji 0.8.0 [Microsoft Azure Storage Explorer](https://storageexplorer.com/), można wyświetlić i pobrać tabulky metrik analizy.
>
>

Aby uzyskać dostęp do tabel analytics programowo, należy pamiętać, że tabele analizy nie są wyświetlane po wyświetleniu listy wszystkich tabel na koncie magazynu. Możesz uzyskać do nich dostęp bezpośrednie według nazwy, lub użyj [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) w bibliotece klienta .NET do wykonywania zapytań nazwy tabeli.

### <a name="hourly-metrics"></a>Godzinowe metryki
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Metryki minut
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Pojemność
* $MetricsCapacityBlob

Możesz znaleźć szczegółowe informacje ze schematów dla tych tabel w [schemat tabeli metryk usługi Analytics magazynu](https://msdn.microsoft.com/library/azure/hh343264.aspx). Poniższe wiersze przykładowe pokazują tylko podzestaw kolumn, które są dostępne, ale zilustrowania niektórych ważnych funkcji sposób, w jaki metryk usługi Storage zapisuje te metryki:

| PartitionKey | RowKey | Znacznik czasu | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Dostępność | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |Użytkownik; QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

W przykładowe dane metryki minut klucza partycji używa czasu w rozdzielczości minuty. Klucz wiersza określa typ informacji przechowywanych w wierszu. Klucz wiersza składa się z dwóch części informacji, typ dostępu i typ żądania:

* Typ dostępu jest użytkownika lub systemu, w którym użytkownik odwołuje się do wszystkich żądań użytkowników do usługi storage, a system, który odwołuje się do żądań wysyłanych przez analityka magazynu.
* W którym zamierzone, zapisz go jest wiersz podsumowania lub identyfikuje określonego interfejsu API, takich jak QueryEntity lub UpdateEntity jest typ żądania.

Powyższe przykładowe dane wyświetlane są wszystkie rekordy na minutę (rozpoczyna się od 11:00:00), aby liczba żądań QueryEntities plus liczba QueryEntity żądań oraz liczbę UpdateEntity żądań, Dodaj maksymalnie siedem, czyli sumę wyświetlane w wierszu użytkownika: All. Podobnie możesz wywodzić średnie opóźnienie end-to-end 104.4286 wiersz użytkownika: All, obliczając ((143.8 * 5) + 3 + 9) / 7.

Należy pamiętać, że **Blob ustawienia metryki godzinowe** dotyczą zarówno **metryki pojemności Blob** ($MetricsCapacityBlob) i **godzinowych metryk transakcji Blob** ($ MetricsHourPrimaryTransactionsBlob). Obie są włączone lub wyłączone ze sobą i przy użyciu tych samych zasad przechowywania.

## <a name="metrics-alerts"></a>Alerty metryki
Należy rozważyć skonfigurowanie alertów w [witryny Azure portal](https://portal.azure.com) tak metryk magazynowania może automatycznie powiadamiać użytkownika o ważnych zmian w działaniu usług magazynu. Jeśli używasz narzędzia Eksploratora magazynu można pobrać te dane metryk w formacie rozdzielanym, można użyć programu Microsoft Excel do analizowania danych. Zobacz [usługi Azure Storage Client Tools](storage-explorers.md) lista narzędzi Eksploratora dostępnego magazynu. Można skonfigurować alerty w **reguły alertów** okienko dostępny w ramach **monitorowanie** w okienku menu konta magazynu.

> [!IMPORTANT]
> Może to być opóźnienie między zdarzenia magazynu, a kiedy jest rejestrowany odpowiadające im dane metryk co godzinę lub minut. Gdy logujesz się metryki minut, kilka minut danych mogą być zapisane tylko raz. Transakcje z wcześniejszych minut może następnie agregowane do transakcji w ciągu bieżącej minuty. W takiej sytuacji usługa alertu dotyczącego nie może mieć wszystkie dane dostępne metryki dla skonfigurowany interwał alertów, co może prowadzić do nieoczekiwanego wyzwalania alertów.
>

## <a name="accessing-metrics-data-programmatically"></a>Programowe uzyskiwanie dostępu do danych metryk
Poniżej przedstawiono przykładowy kod C#, która uzyskuje dostęp do metryki minut dla zakresu minut, a następnie wyświetla wyniki w oknie konsoli. Używa biblioteki Azure Storage w wersji 4, który zawiera klasę CloudAnalyticsClient, który ułatwia uzyskiwanie dostępu do tabel metryki w magazynie.

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
        // (StartsWith is not supported using LINQ with Azure table storage)
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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Jakie opłaty wiąże się po włączeniu metryk usługi storage?
Zapisu żądań w celu utworzenia tabeli jednostki dla metryk są naliczane zgodnie ze standardowymi stawkami mające zastosowanie do wszystkich operacji usługi Azure Storage.

Żądania odczytu i usuwania przez klienta do danych metryk także są płatne zgodnie ze standardowymi stawkami. Jeśli skonfigurowano zasady przechowywania danych nie są naliczane w przypadku usługi Azure Storage usuwa stare dane metryk. Jednak jeśli usuniesz analizy danych, Twoje konto jest rozliczane za operacje usuwania.

Pojemność posługują się tabulky metrik również jest rozliczany: następujące umożliwia szacowanie ilości wydajności używany do przechowywania danych metryk:

* Jeśli co godzinę usługa korzysta z każdego interfejsu API w każdej usługi, następnie 148KB danych są przechowywane w tabeli metryk transakcji na godzinę po włączeniu usługi i poziom interfejsu API podsumowania.
* Jeśli co godzinę usługa korzysta z każdego interfejsu API w każdej usługi, następnie 12KB danych są przechowywane w tabeli metryk transakcji na godzinę włączenie tylko poziom dodatku service summary.
* Tabela pojemności dla obiektów blob ma dwa wiersze dodane każdego dnia (pod warunkiem użytkownik dołączył w przypadku dzienników): oznacza to, że codziennie rozmiar tej tabeli zwiększa się o około 300 bajtów.

## <a name="next-steps"></a>Kolejne kroki
[Włączanie magazynu, rejestrowania i uzyskiwania dostępu do danych dziennika](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
