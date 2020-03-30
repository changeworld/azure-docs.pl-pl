---
title: Metryki usługi Azure Storage w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się więcej o nowych metrykach oferowanych w usłudze Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247099"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metryki usługi Azure Storage w usłudze Azure Monitor

Dzięki metrycznym metrycznym usługom Azure Storage możesz analizować trendy użycia, śledzić żądania i diagnozować problemy z kontem magazynu.

Usługa Azure Monitor udostępnia ujednolicone interfejsy użytkownika do monitorowania w różnych usługach platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Usługa Azure Storage integruje usługę Azure Monitor, wysyłając dane metryki do platformy Azure Monitor.

## <a name="access-metrics"></a>Dostęp do danych

Usługa Azure Monitor udostępnia wiele sposobów dostępu do metryk. Dostęp do nich można uzyskać za pomocą [witryny Azure Portal](https://portal.azure.com), interfejsów API usługi Azure Monitor (REST i .NET) oraz rozwiązań analitycznych, takich jak Centra zdarzeń. Aby uzyskać więcej informacji, zobacz [Metryki usługi Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Metryki są domyślnie włączone i można uzyskać dostęp do ostatnich 93 dni danych. Jeśli chcesz zachować dane przez dłuższy okres czasu, możesz archiwizować dane metryk na koncie usługi Azure Storage. Jest to skonfigurowane w [ustawieniach diagnostycznych](../../azure-monitor/platform/platform-logs-overview.md) w usłudze Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Dostęp do danych w witrynie Azure portal

Metryki można monitorować w czasie w witrynie Azure portal. W poniższym przykładzie pokazano, jak wyświetlić **transakcje** na poziomie konta.

![zrzut ekranu przedstawiający uzyskiwanie dostępu do danych w witrynie Azure portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

W przypadku metryk pomocniczych wymiarów można filtrować metrykę z żądaną wartością wymiaru. W poniższym przykładzie pokazano, jak wyświetlić **transakcje** na poziomie konta w określonej operacji, wybierając wartości dla wymiaru **nazwa interfejsu API.**

![zrzut ekranu przedstawiający uzyskiwanie dostępu do metryk z wymiarem w witrynie Azure portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Dostęp do metryk za pomocą interfejsu API REST

Usługa Azure Monitor udostępnia [interfejsy API REST](/rest/api/monitor/) do odczytu definicji metryki i wartości. W tej sekcji pokazano, jak odczytać metryki magazynu. Identyfikator zasobu jest używany we wszystkich REST APIS. Aby uzyskać więcej informacji, przeczytaj opis identyfikatora zasobu dla usług w magazynie.

W poniższym przykładzie pokazano, jak używać [ArmClient](https://github.com/projectkudu/ARMClient) w wierszu polecenia, aby uprościć testowanie za pomocą interfejsu API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Definicja metryki poziomu konta listy za pomocą interfejsu API REST

W poniższym przykładzie pokazano, jak wyświetlić definicję metryki na poziomie konta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Jeśli chcesz wyświetlić listę definicji metryk dla obiektu blob, tabeli, pliku lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

Odpowiedź zawiera definicję metryki w formacie JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Odczytywanie wartości metryk na poziomie konta za pomocą interfejsu API REST

W poniższym przykładzie pokazano, jak odczytać dane metryki na poziomie konta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

W powyższym przykładzie, jeśli chcesz odczytać wartości metryki dla obiektu blob, tabeli, pliku lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

Następująca odpowiedź zawiera wartości metryki w formacie JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Dostęp do danych za pomocą sdk .NET

Usługa Azure Monitor udostępnia [zestaw SDK platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) do odczytu definicji metryki i wartości. [Przykładowy kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) pokazuje, jak używać zestawu SDK z różnymi parametrami. Należy użyć `0.18.0-preview` lub nowszej wersji dla metryk magazynu. Identyfikator zasobu jest używany w pliku .NET SDK. Aby uzyskać więcej informacji, przeczytaj opis identyfikatora zasobu dla usług w magazynie.

W poniższym przykładzie pokazano, jak używać narzędzia Azure Monitor .NET SDK do odczytywania metryk magazynu.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Definicja metryki poziomu konta listy z pakietem SDK .NET

W poniższym przykładzie pokazano, jak wyświetlić definicję metryki na poziomie konta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Jeśli chcesz wyświetlić listę definicji metryk dla obiektu blob, tabeli, pliku lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Odczyt wartości metryk za pomocą sdk .NET

W poniższym przykładzie `UsedCapacity` pokazano, jak odczytać dane na poziomie konta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

W powyższym przykładzie, jeśli chcesz odczytać wartości metryki dla obiektu blob, tabeli, pliku lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Odczyt wartości metryk wielowymiarowych za pomocą sdk .NET

W przypadku metryk wielowymiarowych należy zdefiniować filtr danych metadanych, jeśli chcesz odczytać dane metryki dotyczące określonej wartości wymiaru.

W poniższym przykładzie pokazano, jak odczytać dane metryki na metrykę obsługującą wielowymiarowe:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Opis identyfikatora zasobu dla usług w usłudze Azure Storage

Identyfikator zasobu to unikatowy identyfikator zasobu na platformie Azure. Korzystając z interfejsu API REST usługi Azure Monitor do odczytu definicji lub wartości metryk, należy użyć identyfikatora zasobu dla zasobu, na którym zamierzasz działać. Szablon identyfikatora zasobu jest zgodny z następującym formatem:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Magazyn udostępnia metryki zarówno na poziomie konta magazynu, jak i na poziomie usługi za pomocą usługi Azure Monitor. Na przykład można pobrać metryki tylko magazynu obiektów Blob. Każdy poziom ma swój własny identyfikator zasobu, który jest używany do pobierania metryk tylko dla tego poziomu.

### <a name="resource-id-for-a-storage-account"></a>Identyfikator zasobu dla konta magazynu

Poniżej przedstawiono format określania identyfikatora zasobu dla konta magazynu.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Identyfikator zasobu dla usług magazynu

Poniżej przedstawiono format określania identyfikatora zasobu dla każdej z usług magazynu.

* Identyfikator zasobu usługi obiektów blob
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Identyfikator zasobu usługi tabeli
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Identyfikator zasobu usługi kolejki
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Identyfikator zasobu usługi plików
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Identyfikator zasobu w interfejsie API REST monitora platformy Azure

Poniżej przedstawiono wzorzec używany podczas wywoływania interfejsu API REST usługi Azure Monitor.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metryki pojemności
Wartości metryk pojemności są wysyłane do usługi Azure Monitor co godzinę. Wartości są odświeżane codziennie. Ziarno czasu definiuje przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi jedną godzinę (PT1H).

Usługa Azure Storage udostępnia następujące metryki pojemności w usłudze Azure Monitor.

### <a name="account-level"></a>Poziom konta

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| UżywaneCapacity | Ilość miejsca używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont w warstwie Premium i kont usługi Blob Storage wartość jest taka sama jak BlobCapacity. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| BlobCapacity (BlobCapacity) | Całkowita ilość magazynu obiektów Blob używanego na koncie magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 <br/> Wymiary: **BlobType**i **BlobTier** [(Definicja)](#metrics-dimensions) |
| Liczba obiektów BlobCount    | Liczba obiektów obiektów blob przechowywanych na koncie magazynu. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 <br/> Wymiary: **BlobType**i **BlobTier** [(Definicja)](#metrics-dimensions) |
| Liczba kontenerów    | Liczba kontenerów na koncie magazynu. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |
| Indeks Zdolności     | Ilość pamięci masowej używana przez hierarchiczny indeks ADLS Gen2 <br/><br/> Jednostka: Bajty <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |

### <a name="table-storage"></a>Magazyn tabel

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| TableCapacity (Zdolność do pońoliczy) | Ilość magazynu tabeli używanego przez konto magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |
| Liczba tabel   | Liczba tabel na koncie magazynu. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |
| Liczba entity tabeli | Liczba jednostek tabeli na koncie magazynu. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| QueueCapacity (Zdolność do kolejek) | Ilość magazynu kolejki używanego przez konto magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |
| Liczba kolejek   | Liczba kolejek na koncie magazynu. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |
| KolejkaLiczna liczba | Liczba niewygasłych wiadomości kolejki na koncie magazynu. <br/><br/>Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |

### <a name="file-storage"></a>File Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Zdolność do pończęcia plików | Ilość magazynu plików używanego przez konto magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |
| Liczba plików   | Liczba plików na koncie magazynu. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |
| Liczba plików ShareCount | Liczba udziałów plików na koncie magazynu. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: średnia <br/> Przykład wartości: 1024 |

## <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane przy każdym żądaniu do konta magazynu z usługi Azure Storage do usługi Azure Monitor. W przypadku braku aktywności na koncie magazynu, nie będzie żadnych danych na temat metryk transakcji w okresie. Wszystkie metryki transakcji są dostępne zarówno na poziomie konta, jak i usługi (magazyn obiektów Blob, magazyn tabel, pliki azure i magazyn kolejki). Ziarno czasu definiuje przedział czasu, w który są prezentowane wartości metryki. Obsługiwane ziarna czasu dla wszystkich metryk transakcji to PT1H i PT1M.

Usługa Azure Storage udostępnia następujące metryki transakcji w usłudze Azure Monitor.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Transakcje | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Odpowiednie wymiary: ResponseType, GeoType, ApiName i Authentication[(Definicja)](#metrics-dimensions)<br/> Przykład wartości: 1024 |
| Ruch przychodzący | Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Odpowiednie wymiary: GeoType, ApiName i Authentication[(Definicja)](#metrics-dimensions) <br/> Przykład wartości: 1024 |
| Ruch wychodzący | Ilość danych wychodzących. Ten numer obejmuje ruch wychodzący do klienta zewnętrznego z usługi Azure Storage, a także ruch wychodzący na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Odpowiednie wymiary: GeoType, ApiName i Authentication[(Definicja)](#metrics-dimensions) <br/> Przykład wartości: 1024 |
| SuccessServerLatency | Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. <br/><br/> Jednostka: Milisekundy <br/> Typ agregacji: średnia <br/> Odpowiednie wymiary: GeoType, ApiName i Authentication[(Definicja)](#metrics-dimensions) <br/> Przykład wartości: 1024 |
| SuccessE2ELatency | Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. <br/><br/> Jednostka: Milisekundy <br/> Typ agregacji: średnia <br/> Odpowiednie wymiary: GeoType, ApiName i Authentication[(Definicja)](#metrics-dimensions) <br/> Przykład wartości: 1024 |
| Dostępność | Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości wszystkich żądań do rozliczenia przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API. <br/><br/> Jednostka: Procent <br/> Typ agregacji: średnia <br/> Odpowiednie wymiary: GeoType, ApiName i Authentication[(Definicja)](#metrics-dimensions) <br/> Przykład wartości: 99,99 |

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Storage obsługuje następujące wymiary metryk w usłudze Azure Monitor.

| Nazwa wymiaru | Opis |
| ------------------- | ----------------- |
| **Typ obiektu blob** | Typ obiektu blob tylko dla metryk obiektów blob. Obsługiwane wartości to **BlockBlob**, **PageBlob**i **Azure Data Lake Storage**. Dołącz obiekt blob znajduje się w BlockBlob. |
| **Obiekt BlobTier** | Azure storage offers different access tiers, which allow you to store blob object data in the most cost-effective manner. Zobacz więcej w [warstwie obiektów blob usługi Azure Storage.](../blobs/storage-blob-storage-tiers.md) Obsługiwane wartości obejmują: <br/> <li>**Hot**: Gorąca warstwa</li> <li>**Cool**: Fajny poziom</li> <li>**Archiwum**: Warstwa archiwum</li> <li>**Premium**: Warstwa Premium dla bloków blob</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Typy poziomów dla obiektów blob strony premium</li> <li>**Standardowy**: Typ warstwy dla standardowego obiektu blob strony</li> <li>**Nieupołączone:** Typ warstwy dla konta magazynu ogólnego przeznaczenia w wersji 1</li> |
| **Typ geograficzny** | Transakcja z klastra podstawowego lub pomocniczego. Dostępne wartości obejmują **podstawowe** i **pomocnicze**. Ma zastosowanie do dostępu do odczytu geo nadmiarowego magazynu (RA-GRS) podczas odczytywania obiektów z dzierżawy pomocniczej. |
| **Rodzaj odpowiedzi** | Typ odpowiedzi transakcji. Dostępne wartości obejmują: <br/><br/> <li>**ServerOtherError**: Wszystkie inne błędy po stronie serwera z wyjątkiem opisanych </li> <li>**ServerBusyError**: Uwierzytelnione żądanie, które zwróciło kod stanu HTTP 503. </li> <li>**ServerTimeoutError**: Czasowe uwierzytelnione żądanie, które zwróciło kod stanu HTTP 500. Przekroczenie limitu czasu wystąpiło z powodu błędu serwera. </li> <li>**AuthorizationError**: Uwierzytelnione żądanie, które nie powiodło się z powodu nieautoryzowanego dostępu do danych lub niepowodzenia autoryzacji. </li> <li>**NetworkError**: Uwierzytelnione żądanie, które nie powiodło się z powodu błędów sieciowych. Najczęściej występuje, gdy klient przedwcześnie zamyka połączenie przed wygaśnięciem limitu czasu. </li>  <li>**ClientAccountBandwidthThrottlingError**: Żądanie jest ograniczane na przepustowość w celu przekroczenia [limitów skalowalności konta magazynu.](scalability-targets-standard-account.md)</li><li>**ClientAccountRequestThrottlingError:** Żądanie jest ograniczane na szybkość żądania za przekroczenie [limitów skalowalności konta magazynu](scalability-targets-standard-account.md).<li>**ClientThrottlingError:** Inny błąd ograniczania przepustowości po stronie klienta. ClientAccountBandwidthThrottlingError i ClientAccountRequestThrottlingError są wykluczone.</li> <li>**ClientTimeoutError:** Czasomierz uwierzytelnione żądanie, które zwróciło kod stanu HTTP 500. Jeśli limit czasu sieci klienta lub limit czasu żądania jest ustawiony na mniejszą wartość niż oczekiwana przez usługę magazynu, jest to oczekiwany limit czasu. W przeciwnym razie zostanie zgłoszone jako ServerTimeoutError.</li> </li> <li>**ClientOtherError**: Wszystkie inne błędy po stronie klienta, z wyjątkiem opisanych. </li> <li>**Sukces**: Pomyślna prośba</li> <li> **SuccessWithThrottling:** Pomyślne żądanie, gdy klient SMB zostanie ograniczony w pierwszej próbie(-ach), ale powiedzie się po ponownych próbach.</li> |
| **Nazwa api** | Nazwa operacji. Przykład: <br/> <li>**Utwórz Kontener**</li> <li>**Usuńblob**</li> <li>**GetBlob ( GetBlob )**</li> Dla wszystkich nazw operacji zobacz [dokument](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Uwierzytelnianie** | Typ uwierzytelniania używany w transakcjach. Dostępne wartości obejmują: <br/> <li>**AccountKey:** Transakcja jest uwierzytelniona przy użyciu klucza konta magazynu.</li> <li>**Sygnatury dostępu**Współdzielonego: Transakcja jest uwierzytelniana za pomocą podpisów dostępu współdzielonego.</li> <li>**OAuth:** Transakcja jest uwierzytelniona za pomocą tokenów dostępu OAuth.</li> <li>**Anonimowy:** Transakcja jest żądana anonimowo. Nie obejmuje żądań inspekcji wstępnej.</li> <li>**AnonymousPreflight:** Transakcja jest żądaniem inspekcji wstępnej.</li> |

W przypadku metryk obsługujących wymiary należy określić wartość wymiaru, aby wyświetlić odpowiednie wartości metryk. Na przykład, jeśli spojrzysz na **Wartość Transakcje** dla pomyślnych odpowiedzi, musisz filtrować wymiar **ResponseType** za pomocą **sukcesu**. Lub jeśli spojrzeć na wartość **BlobCount** dla bloku blob, należy filtrować wymiar **BlobType** z **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Ciągłość usług starszych metryk

Starsze metryki są dostępne równolegle z metrykami zarządzanymi usługi Azure Monitor. Obsługa utrzymuje to samo, dopóki usługa Azure Storage nie zakończy usługi na starszych metrykach.

## <a name="faq"></a>Najczęściej zadawane pytania

**Czy nowe metryki obsługują konto magazynu klasycznego?**

Nie, nowe metryki w usłudze Azure Monitor obsługują tylko konta magazynu usługi Azure Resource Manager. Jeśli chcesz użyć metryki na kontach magazynu, musisz przeprowadzić migrację do konta usługi Azure Resource Manager. Zobacz [Migracja do usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Czy usługa Azure Storage obsługuje metryki dysków zarządzanych lub dysków niezarządzanych?**

Nie, usługa Azure Compute obsługuje metryki na dyskach. Zobacz [artykuł,](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) aby uzyskać więcej informacji.

**Jak mapować i migrować klasyczne metryki za pomocą nowych danych?**

Szczegółowe mapowanie między metrykami klasycznymi a nowymi metrykami można znaleźć w [migracji metryk usługi Azure Storage.](./storage-metrics-migration.md)

## <a name="next-steps"></a>Następne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
