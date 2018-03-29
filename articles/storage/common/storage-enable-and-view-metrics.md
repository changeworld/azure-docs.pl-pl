---
title: Włączanie metryk magazynu w portalu Azure | Dokumentacja firmy Microsoft
description: Jak włączyć metryki magazynu dla usługi obiektów Blob, kolejki, tabel i plików
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.openlocfilehash: 0caa4eff80877ad4bf8d501a276e82922b1a84c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Włączanie metryk usługi Azure Storage i wyświetlanie danych metryk
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Przegląd
Metryki magazynu jest domyślnie włączona, podczas tworzenia nowego konta magazynu. Można skonfigurować monitorowanie za pomocą [portalu Azure](https://portal.azure.com) lub środowiska Windows PowerShell lub programowo przy użyciu jednej z bibliotek klienckich magazynu.

Można skonfigurować okres przechowywania danych metryki: tego okresu określa, jak długo magazynu usługi śledzi metryki i opłat dla miejsca trzeba je przechowywać. Zwykle należy używać krótszy okres przechowywania dla metryki minuty niż co godzinę metryki ze względu na dodatkowe miejsce znaczące wymagane dla metryki minuty. Wybierz okres przechowywania w taki sposób, że masz wystarczającą ilość czasu do analizowania danych i pobierania wszystkie metryki, które chcesz zachować dla celów raportowania analizy offline lub. Należy pamiętać, możesz również są rozliczane pobierania danych metryki z konta magazynu.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Jak włączyć metryki przy użyciu portalu Azure
Wykonaj następujące kroki, aby włączyć metryki w [portalu Azure](https://portal.azure.com):

1. Przejdź do swojego konta magazynu.
1. Wybierz **diagnostyki** z **Menu** okienka.
1. Upewnij się, że **stan** ustawiono **na**.
1. Wybierz metryki dla usługi, które chcesz monitorować.
1. Określ zasady przechowywania, aby wskazać, jak długo zachować metryki i dane dziennika.
1. Wybierz pozycję **Zapisz**.

[Portalu Azure](https://portal.azure.com) nie obecnie obsługuje skonfigurowania minuty metryki na koncie magazynu; należy włączyć metryki minuty przy użyciu programu PowerShell lub programowo.

## <a name="how-to-enable-metrics-using-powershell"></a>Jak włączyć metryki przy użyciu programu PowerShell
PowerShell na komputerze lokalnym służy do konfigurowania metryki magazynu na koncie magazynu przy użyciu polecenia cmdlet programu Azure PowerShell Get-AzureStorageServiceMetricsProperty pobrać bieżące ustawienia i polecenia cmdlet Set-AzureStorageServiceMetricsProperty do zmiany bieżących ustawień.

Polecenia cmdlet, określające metryki magazynu używają następujące parametry:

* MetricsType: możliwe wartości to godziny i minuty.
* Typ ServiceType: możliwe wartości to obiektów Blob, kolejki i tabeli.
* MetricsLevel: możliwe wartości to None, usługi i ServiceAndApi.

Na przykład następujące polecenie zmienia się na minutę metryki dla usługi obiektów Blob na koncie magazynu domyślne przechowywania ustawionej do pięciu dni:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Polecenie pobiera bieżący co godzinę metryki poziomu i przechowywania dni usługi obiektów blob na koncie magazynu domyślne:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Aby uzyskać informacje o sposobie konfigurowania poleceń cmdlet programu Azure PowerShell do pracy z subskrypcją platformy Azure i jak wybrać domyślne konto magazynu do użycia, zobacz: [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Jak programowo włączyć metryki magazynu
Poniższy fragment kodu C# pokazano, jak włączyć rejestrowanie dla usługi obiektów Blob za pomocą biblioteki klienta usługi storage dla platformy .NET i metryki:

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

## <a name="viewing-storage-metrics"></a>Wyświetlanie metryki magazynu
Po skonfigurowaniu metryki analityka magazynu do konta magazynu monitorowania analityka magazynu rejestruje metryki w zestawie dobrze znanego tabel na koncie magazynu. Można skonfigurować wykresy, aby wyświetlić godzinę metryki w [portalu Azure](https://portal.azure.com):

1. Przejdź do swojego konta magazynu w [portalu Azure](https://portal.azure.com).
1. Wybierz **metryki** w **Menu** okienko usługi metryki, którego chcesz wyświetlić.
1. Wybierz **Edytuj** na wykresie, aby skonfigurować.
1. W **Edytuj wykres** okienku wybierz **zakres czasu**, **typ wykresu**i metryk, które mają być wyświetlane na wykresie.
1. Kliknij przycisk **OK**

Jeśli chcesz pobrać metryk do długoterminowego przechowywania lub do analizowania je lokalnie, musisz:

* Za pomocą narzędzia, który zna te tabele i będzie można wyświetlić i pobrać je.
* Napisać aplikację niestandardową lub skrypt, aby odczytać i zapisać tabel.

Wiele narzędzi Przeglądanie magazynu innych firm wiedzą tych tabel i można je wyświetlać bezpośrednio.
Zobacz [narzędzi klienta magazynu Azure](storage-explorers.md) listę dostępnych narzędzi.

> [!NOTE]
> Począwszy od wersji 0.8.0 [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/), można wyświetlić i pobrać tabel metryki analytics.
> 
> 

Aby uzyskać dostęp do tabel analytics programowo, należy pamiętać, że tabele analytics nie są wyświetlane jeśli listy wszystkich tabel na koncie magazynu. Możesz uzyskiwać do nich dostęp bezpośrednio według nazwy, lub użyj [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) w bibliotece klienta .NET do badania nazwy tabeli.

### <a name="hourly-metrics"></a>Metryki co godzinę
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Metryki minuty
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Pojemność
* $MetricsCapacityBlob

Dla tych tabel w można znaleźć szczegółowe informacje dotyczące schematów [schemat tabeli metryki analityka magazynu](https://msdn.microsoft.com/library/azure/hh343264.aspx). Poniższe wiersze próbki Pokaż tylko podzbiór dostępnych kolumn, ale zilustrować niektóre ważne funkcje sposób metryki magazynu zapisuje te metryki:

| PartitionKey | RowKey | Sygnatura czasowa | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Dostępność | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |Użytkownik; QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

W przykładowe dane metryk minuty klucza partycji jest używany czas rozdzielczością minuty. Klucz wiersza określa typ informacji przechowywanych w wierszu. Klucz wiersza składa się z dwóch części informacji, typ dostępu i typ żądania:

* Typ dostępu jest użytkownika lub systemu, której użytkownik odwołuje się do wszystkich żądań użytkowników z usługą Magazyn, a system odwołuje się do żądań wysyłanych przez analityka magazynu.
* Typ żądania jest wszystkie w takim przypadku on jest wiersz podsumowania albo identyfikuje określonego interfejsu API, takich jak QueryEntity lub UpdateEntity.

Przykładowe dane powyżej wyświetlane są wszystkie rekordy na minutę (rozpoczyna się od 11:00:00), tak liczba żądań QueryEntities oraz liczbę QueryEntity żądań i liczby UpdateEntity żądań, Dodaj do 7, który jest sumą wyświetlany w wierszu użytkownika: All. Podobnie, mogą dziedziczyć średnie opóźnienie end-to-end 104.4286 wiersza użytkownika: All, obliczając ((143.8 * 5) + 3 + 9) / 7.

## <a name="metrics-alerts"></a>Metryki alertów
Należy rozważyć skonfigurowanie alerty w [portalu Azure](https://portal.azure.com) tak metryki magazynu automatycznie może powiadomić o ważne zmiany zachowania usługi magazynu. Jeśli używasz narzędzia Eksploratora magazynu można pobrać te dane metryk w formacie rozdzielanym, można użyć programu Microsoft Excel do analizowania danych. Zobacz [narzędzi klienta magazynu Azure](storage-explorers.md) listę narzędzi Eksploratora dostępny magazyn. Można skonfigurować alerty w **reguły alertów** okienka, dostępny w ramach **monitorowanie** w okienku menu konta magazynu.

> [!IMPORTANT]
> Mogą występować opóźnienia między zdarzeniem magazynu i gdy odpowiednie dane metryk godzinowe i minutowe są rejestrowane. Po zalogowaniu się metryki minuty, kilka minut danych mogą być zapisane na raz. Transakcje z wcześniejszych minut następnie mogą być łączone w transakcji dla bieżącej minuty. W takim przypadku usługa alertów nie może mieć wszystkich danych dostępne metryki skonfigurowanego interwału alertu, co może prowadzić do nieoczekiwanego wyzwalania alertów.
>

## <a name="accessing-metrics-data-programmatically"></a>Uzyskiwanie dostępu do danych metryki programowo
Poniżej przedstawiono przykładowy C# kod, który uzyskuje dostęp do minuty metryki dla zakresu minut i wyświetla wyniki w oknie konsoli. Korzysta z biblioteki usługi Azure Storage wersji 4, który zawiera klasę CloudAnalyticsClient, który ułatwia uzyskiwanie dostępu do tabel metryki w magazynie.

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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Jakie opłat ponosisz po włączeniu metryki magazynu?
Zapisu ze stawkami standardowymi mające zastosowanie do wszystkich operacji magazynowania Azure naliczane są opłaty żądania utworzenia jednostek tabeli dla metryki.

Żądania odczytu i usuwania przez klienta do danych metryki są również rozliczeniowy stawkami standardowymi. Jeśli zostały skonfigurowane zasady przechowywania danych, nie są naliczane gdy magazyn Azure usuwa stare dane metryk. Jeśli usuniesz dane analityczne, Twoje konto jest pobierana dla operacji delete.

Używane przez metryki tabel jest również rozliczeniowy: następujące umożliwia oszacowania ilości wydajności używany do przechowywania danych metryki:

* Jeśli co godzinę usługa korzysta z każdego interfejsu API w każdej usługi, następnie 148KB danych jest przechowywana co godzinę w tabelach transakcji metryk po włączeniu zarówno usługi, jak i interfejs API na poziomie podsumowania.
* Jeśli co godzinę usługa korzysta z każdego interfejsu API w każdej usługi, następnie 12KB danych jest przechowywana co godzinę w tabelach transakcji metryk po włączeniu tylko poziom usługi podsumowania.
* Tabela pojemności dla obiektów blob ma dwa wiersze dodane każdego dnia (zakładając, że użytkownik wybrał w dzienników): oznacza to, że codziennie rozmiar tej tabeli zwiększa o około 300 bajtów.

## <a name="next-steps"></a>Kolejne kroki
[Włączanie magazynu, rejestrowania i uzyskiwanie dostępu do danych dziennika](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
