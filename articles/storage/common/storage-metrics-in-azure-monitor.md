---
title: Metryki usługi Azure Storage w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Więcej informacji o nowe metryki oferowane za pośrednictwem usługi Azure Monitor.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 244d7fc3caa96173e408a193e13acd656d4a7f77
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698779"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metryki usługi Azure Storage w usłudze Azure Monitor

Za pomocą metryk w usłudze Azure Storage można analizować trendy użycia, Śledzenie żądań i diagnozowanie problemów z kontem usługi storage.

Usługa Azure Monitor udostępnia interfejsy użytkownika ujednoliconego do monitorowania w różnych usługach platformy Azure. Aby uzyskać więcej informacji, zobacz [usługi Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Usługa Azure Storage integruje usługi Azure Monitor, wysyłając metryki danych na platformę Azure Monitor.

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Użytkownik może uzyskiwać do nich dostęp z [witryny Azure portal](https://portal.azure.com), interfejsów API usługi Azure Monitor (REST i .NET) i rozwiązań analitycznych, takich jak Event Hubs. Aby uzyskać więcej informacji, zobacz [metryk usługi Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Metryki są domyślnie włączone, a dostęp można uzyskać dane z ostatnich dni 93. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. To ustawienie jest konfigurowane w [ustawień diagnostycznych](../../azure-monitor/platform/diagnostic-logs-overview.md) w usłudze Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Dostęp do metryk w witrynie Azure portal

Możesz monitorować metryki, wraz z upływem czasu w witrynie Azure portal. Poniższy przykład pokazuje, jak wyświetlić **transakcji** na poziomie konta.

![Zrzut ekranu przedstawiający dostęp do metryk w witrynie Azure portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Metryki obsługi wymiarów można filtrować metryki z żądaną wartością. Poniższy przykład pokazuje, jak wyświetlić **transakcji** na poziomie konta na konkretną operacją, wybierając wartości **Nazwa interfejsu API** wymiaru.

![Zrzut ekranu przedstawiający dostęp do metryk z wymiarem w witrynie Azure portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Dostęp do metryk z interfejsu API REST

Usługa Azure Monitor zapewnia [interfejsów API REST](/rest/api/monitor/) na odczytywanie definicji metryk i wartości. W tej sekcji dowiesz się, jak odczytać metryk usługi storage. Identyfikator zasobu jest używana w wszystkich interfejsów API REST. Aby uzyskać więcej informacji, przeczytaj informacje o identyfikatorze zasobu usługi w ramach magazynu.

Poniższy przykład pokazuje, jak używać [ArmClient](https://github.com/projectkudu/ARMClient) w wierszu polecenia, aby uprościć testowanie za pomocą interfejsu API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Lista kont poziomu definicja metryki interfejsu API REST

Poniższy przykład ilustruje sposób wyświetlenia listy definicja metryki na poziomie konta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Jeśli chcesz wyświetlić listę definicji metryk dla obiektów blob, tabel, plików lub kolejek, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

Odpowiedź zawiera definicja metryki w formacie JSON:

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Odczyt wartości metryk na poziomie konta, za pomocą interfejsu API REST

Poniższy przykład pokazuje, jak odczytać dane metryk na poziomie konta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

W powyżej przykładzie, jeśli chcesz odczytać wartości metryk dla obiektów blob, tabel, plik lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

Następującą odpowiedź zawiera wartości metryk w formacie JSON:

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

### <a name="access-metrics-with-the-net-sdk"></a>Metryki dostępu przy użyciu zestawu .NET SDK

Usługa Azure Monitor zapewnia [zestawu .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) na odczytywanie definicji metryk i wartości. [Przykładowego kodu](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) pokazuje, jak używać zestawu SDK z innymi parametrami. Należy użyć `0.18.0-preview` lub nowsza wersja dla metryk usługi storage. Identyfikator zasobu jest używany w zestawie SDK platformy .NET. Aby uzyskać więcej informacji, przeczytaj informacje o identyfikatorze zasobu usługi w ramach magazynu.

Poniższy przykład pokazuje, jak odczytywanie metryk usługi storage przy użyciu usługi Azure Monitor .NET SDK.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Konta poziomu metryki definicji listy przy użyciu zestawu .NET SDK

Poniższy przykład ilustruje sposób wyświetlenia listy definicja metryki na poziomie konta:

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

Jeśli chcesz wyświetlić listę definicji metryk dla obiektów blob, tabel, plików lub kolejek, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Wartości metryk odczytu przy użyciu zestawu .NET SDK

Poniższy przykład pokazuje, jak odczytać `UsedCapacity` danych na poziomie konta:

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

W powyżej przykładzie, jeśli chcesz odczytać wartości metryk dla obiektów blob, tabel, plik lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Odczytu wartości metryk wielowymiarowych za pomocą zestawu SDK platformy .NET

Dla metryk wielowymiarowych należy zdefiniować meta danych filtru, aby odczytać metryki danych na podstawie wartości określonego wymiaru.

Poniższy przykład pokazuje, jak odczytać dane metryk na metryce, obsługa wielu wymiarów:

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Informacje o identyfikatorze zasobu usługi w usłudze Azure Storage

Identyfikator zasobu jest unikatowy identyfikator zasobu na platformie Azure. Korzystając z interfejsu API REST usługi Azure Monitor na odczytywanie definicji metryk lub wartości, należy użyć Identyfikatora zasobu dla zasobu, na którym ma działać. Szablon identyfikator zasobu ma następujący format:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Magazyn generuje dane pomiarowe zarówno na poziomie konta magazynu, jak i na poziomie usługi, za pomocą usługi Azure Monitor. Na przykład można pobrać metryki tylko magazynu obiektów Blob. Każdy poziom ma swój własny identyfikator zasobu i służy do pobierania metryki dla właśnie na tym poziomie.

### <a name="resource-id-for-a-storage-account"></a>Identyfikator zasobu dla konta magazynu

Na poniższym obrazie przedstawiono format Określ identyfikator ID zasobu dla konta magazynu.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Identyfikator zasobu w przypadku usług storage

Na poniższym obrazie przedstawiono formatu do określania Identyfikatora zasobu dla wszystkich usług magazynu.

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

### <a name="resource-id-in-azure-monitor-rest-api"></a>Identyfikator zasobu w usłudze Azure Monitor interfejsu API REST

Na poniższym obrazie przedstawiono wzorzec używany podczas wywoływania interfejsu API REST usługi Azure Monitor.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metryki wydajności
Wartości metryk wydajności są wysyłane do usługi Azure Monitor co godzinę. Wartości są odświeżane raz dziennie. Ziarno czasu określa interwał czasu, dla której są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk dotyczących pojemności to jedna godzina (PT1H).

Usługa Azure Storage udostępnia następujące metryki pojemności w usłudze Azure Monitor.

### <a name="account-level"></a>Na poziomie konta

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| UsedCapacity | Ilość miejsca używanego przez konta magazynu. W przypadku kont magazynu w warstwie standardowa jest sumą pojemność wykorzystana przez obiektów blob, tabel, plików i kolejek. Dla kont usługi premium storage i kont usługi Blob storage jest taka sama jak BlobCapacity. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| BlobCapacity | Suma magazynu obiektów Blob na koncie magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Wymiary: **BlobType**, i **BlobTier** ([definicji](#metrics-dimensions)) |
| BlobCount    | Liczba obiektów blob przechowywanych na koncie magazynu. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Wymiary: **BlobType**, i **BlobTier** ([definicji](#metrics-dimensions)) |
| ContainerCount    | Liczba kontenerów na koncie magazynu. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| IndexCapacity     | Ilość miejsca używanego przez indeks hierarchiczne Gen2 Azure Data Lake Store <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="table-storage"></a>Magazyn tabel

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| TableCapacity | Ilość usługi Table storage używane przez konto magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| TableCount   | Liczba tabel na koncie magazynu. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| TableEntityCount | Liczba jednostek tabel na koncie magazynu. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| QueueCapacity | Ilość usługi Queue storage używane przez konto magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueCount   | Liczba kolejek na koncie magazynu. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueMessageCount | Liczba komunikatów w kolejce niewygasłe na koncie magazynu. <br/><br/>Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="file-storage"></a>File Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| FileCapacity | Wielkość magazynu plików używane przez konto magazynu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileCount   | Liczba plików w ramach konta magazynu. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileShareCount | Liczba udziałów plików na koncie magazynu. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

## <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane na każde żądanie do konta magazynu z usługi Azure Storage do usługi Azure Monitor. W przypadku żadnej aktywności na koncie magazynu nie będzie żadnych danych na metryk transakcji w okresie. Wszystkie metryki transakcji są dostępne na poziomie konta, jak i usługi (usługi Blob storage, Table storage, Azure Files i Queue storage). Ziarno czasu określa interwał czasu, które są prezentowane wartości metryk. Ziarno czasu obsługiwane dla wszystkich metryk transakcji są PT1H i PT1M.

Usługa Azure Storage udostępnia następujące metryki transakcji w usłudze Azure Monitor.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Transakcje | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. <br/><br/> Jednostka: Licznik <br/> Typ agregacji: Łącznie <br/> Wymiary stosowane: Wartość ResponseType, GeoType, ApiName i uwierzytelniania ([definicji](#metrics-dimensions))<br/> Przykład wartości: 1024 |
| Ruch przychodzący | Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Łącznie <br/> Wymiary stosowane: GeoType, ApiName i uwierzytelniania ([definicji](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Ruch wychodzący | Ilość danych wychodzących. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. <br/><br/> Jednostka: Bajty <br/> Typ agregacji: Łącznie <br/> Wymiary stosowane: GeoType, ApiName i uwierzytelniania ([definicji](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessServerLatency | Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. <br/><br/> Jednostka: Milisekundy <br/> Typ agregacji: Średnia <br/> Wymiary stosowane: GeoType, ApiName i uwierzytelniania ([definicji](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessE2ELatency | Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. <br/><br/> Jednostka: Milisekundy <br/> Typ agregacji: Średnia <br/> Wymiary stosowane: GeoType, ApiName i uwierzytelniania ([definicji](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Dostępność | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana wartość łączną liczbę płatnych żądań przez liczbę żądań mających zastosowanie, łącznie z tymi żądaniami, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API. <br/><br/> Jednostka: Procent <br/> Typ agregacji: Średnia <br/> Wymiary stosowane: GeoType, ApiName i uwierzytelniania ([definicji](#metrics-dimensions)) <br/> Przykład wartości: 99.99 |

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Storage obsługuje następujące wymiary metryk w usłudze Azure Monitor.

| Nazwa wymiaru | Opis |
| ------------------- | ----------------- |
| **BlobType** | Typ obiektu blob na potrzeby tylko metryki obiektów Blob. Obsługiwane wartości to **BlockBlob**, **PageBlob**, i **usługi Azure Data Lake Storage**. Dołącz obiekt Blob znajduje się w BlockBlob. |
| **BlobTier** | Usługa Azure storage oferuje dostęp do różnych warstw, które umożliwiają przechowywanie danych obiektów blob w sposób najbardziej efektywny. Zobacz więcej w [warstwy obiektu blob usługi Azure Storage](../blobs/storage-blob-storage-tiers.md). Obsługiwane wartości to: <br/> <li>**Gorąca**: Warstwy gorąca i używanie</li> <li>**Chłodna**: Warstwa chłodna</li> <li>**Archiwum**: Warstwę archiwum</li> <li>**Premium**: Blokowe obiekty blob w warstwie Premium</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Typy warstwy premium stronicowych obiektów blob</li> <li>**Standardowa**: Typ warstwy standardowej strony obiektów Blob</li> <li>**Untiered**: Typ warstwy konto magazynu ogólnego przeznaczenia w wersji 1</li> |
| **GeoType** | Transakcja z podstawowy lub pomocniczy klastra. Dostępne wartości to **podstawowego** i **dodatkowej**. Dotyczy ona dostęp do odczytu geograficznie nadmiarowy Storage(RA-GRS) podczas odczytywania obiektów ze dodatkowej dzierżawy. |
| **ResponseType** | Typ odpowiedzi transakcji. Dostępne wartości obejmują: <br/><br/> <li>**ServerOtherError**: Wszystkie inne błędy po stronie serwera z wyjątkiem opisanych </li> <li>**ServerBusyError**: Uwierzytelnione żądanie, które zwróciło kod stanu HTTP 503. </li> <li>**ServerTimeoutError**: Uwierzytelnione żądanie z przekroczonym limitem czasu, które zwróciło kod stanu HTTP 500. Przekroczenie limitu czasu wystąpiło z powodu błędu serwera. </li> <li>**AuthorizationError**: Uwierzytelnione żądanie, które nie powiodło się z powodu nieautoryzowanego dostępu do danych lub błędu autoryzacji. </li> <li>**NetworkError**: Uwierzytelnione żądanie, które nie powiodło się z powodu błędów sieci. Najczęściej występuje, gdy klient przedwcześnie zamyka połączenie przed wygaśnięciem limitu czasu. </li> <li>**ClientThrottlingError**: Błąd ograniczania przepływności po stronie klienta. </li> <li>**ClientTimeoutError**: Uwierzytelnione żądanie z przekroczonym limitem czasu, które zwróciło kod stanu HTTP 500. Jeśli limit czasu sieci klienta lub limit czasu żądania jest ustawiony na mniejszą wartość niż oczekiwana przez usługę magazynu, jest to oczekiwany limit czasu. W przeciwnym razie zostanie zgłoszone jako ServerTimeoutError. </li> <li>**ClientOtherError**: Wszystkie inne błędy po stronie klienta z wyjątkiem opisanych. </li> <li>**Success**: Żądanie zakończone powodzeniem</li> |
| **ApiName** | Nazwa operacji. Na przykład: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Aby uzyskać wszystkie nazwy operacji, zobacz [dokumentu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Uwierzytelnianie** | Typ uwierzytelniania używany w transakcji. Dostępne wartości obejmują: <br/> <li>**AccountKey**: Transakcja jest uwierzytelniana przy użyciu klucza konta magazynu.</li> <li>**SAS**: Transakcja jest uwierzytelniana przy użyciu sygnatury dostępu współdzielonego.</li> <li>**OAuth**: Transakcja jest uwierzytelniana przy użyciu tokenów dostępu protokołu OAuth.</li> <li>**Anonimowe**: Transakcja jest proszony anonimowo. Nie zawiera żądania wstępnego.</li> <li>**AnonymousPreflight**: Transakcja została żądania wstępnego.</li> |

W przypadku wymiarów pomocnicze metryki należy określić wartości wymiaru, aby wyświetlić odpowiednie wartości metryk. Na przykład, jeśli przyjrzymy się **transakcji** wartość dla pomyślnej odpowiedzi, trzeba filtrować **ResponseType** wymiaru przy użyciu **Powodzenie**. Lub jeśli przyjrzymy się **BlobCount** wartość dla blokowych obiektów Blob, trzeba filtrować **BlobType** wymiaru przy użyciu **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Ciągłość działania usługi starszych metryk

Starsze metryki są dostępne równolegle za pomocą metryk usługi Azure Monitor zarządzane. Wsparcie przechowuje takie same, do czasu usługi Azure Storage zakończenia usługę w starszych metryki.

## <a name="faq"></a>Często zadawane pytania

**Nowe metryki obsługuje klasycznego konta magazynu?**

Nie, nowe metryki w ramach kont magazynu usługi Azure Resource Manager tylko pomocy technicznej usługi Azure Monitor. Jeśli chcesz przy użyciu metryk dotyczących kont magazynu, należy przeprowadzić migrację do konta magazynu usługi Resource Manager platformy Azure. Zobacz [migrację do usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Usługi Azure Storage obsługuje metryki dla dysków zarządzanych lub dysków niezarządzanych?**

Nie, usługi Azure Compute obsługuje metryki na dyskach. Zobacz [artykułu](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) Aby uzyskać więcej informacji.

**Jak zamapować i Migrowanie klasycznej metryki o nowe metryki?**

Można znaleźć szczegółowe mapowanie między klasycznym metryki i nowe metryki w [migracji metryk usługi Azure Storage](./storage-metrics-migration.md).

## <a name="next-steps"></a>Kolejne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
