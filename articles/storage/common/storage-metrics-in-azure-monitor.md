---
title: Azure metryki magazynu w monitorze Azure | Dokumentacja firmy Microsoft
description: Więcej informacji o nowe metryki oferowane za pośrednictwem Monitora Azure.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: 05021d5ab8d33e36bff16ce7d2ebacd3db72639a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036039"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metryki usługi Azure Storage w usłudze Azure Monitor

Metryki dotyczące usługi Azure Storage możesz analizować trendy użycia i śledzenie żądań i diagnozowanie problemów z kontem magazynu.

Azure Monitor udostępnia interfejsy użytkownika ujednoliconego do monitorowania w różnych usługach Azure. Aby uzyskać więcej informacji, zobacz [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Magazyn Azure integruje się Azure Monitor wysyłając metryki danych do platformy Azure monitora.

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor oferuje wiele sposobów metryki dostępu. Użytkownik może uzyskiwać do nich dostęp z [portalu Azure](https://portal.azure.com), interfejsów API usługi Azure monitora (REST i .net) i rozwiązań analitycznych, takie jak operacji Management Suite i usługi Event Hubs. Aby uzyskać więcej informacji, zobacz [metryki Monitor Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Metryki są domyślnie włączone i ostatnich 30 dni danych można uzyskać dostęp. Jeśli chcesz zachować dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. Te ustawienia zostaną skonfigurowane w [ustawień diagnostycznych](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) w monitorze Azure.

### <a name="access-metrics-in-the-azure-portal"></a>Metryki dostępu w portalu Azure

Można monitorować metryki wraz z upływem czasu, w portalu Azure. Poniższy przykład przedstawia sposób wyświetlania **UsedCapacity** na poziomie konta.

![Zrzut ekranu przedstawiający dostęp do metryk w portalu Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Metryki pomocnicze, wymiary można filtrować Metryka z żądaną wartością. Poniższy przykład przedstawia sposób wyświetlania **transakcji** na poziomie konta na określonej operacji po wybraniu wartości dla **Nazwa interfejsu API** wymiaru.

![Zrzut ekranu przedstawiający dostęp do metryk z wymiarem w portalu Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Metryki dostępu przy użyciu interfejsu API REST

Azure Monitor udostępnia [interfejsów API REST](/rest/api/monitor/) odczytać metryki definicji i wartości. W tej sekcji przedstawiono sposób odczytać metryki magazynu. Identyfikator zasobu jest używany podczas wszystkich interfejsów API REST. Aby uzyskać więcej informacji, przeczytaj [opis identyfikator zasobu dla usług w magazynie](#understanding-resource-id-for-services-in-storage).

Poniższy przykład przedstawia użycie [ArmClient](https://github.com/projectkudu/ARMClient) w wierszu polecenia, aby uprościć testowanie za pomocą interfejsu API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Konto poziomu metryki definicji listy przy użyciu interfejsu API REST

Poniższy przykład przedstawia sposób wyświetlania definicji metryki na poziomie konta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Jeśli chcesz listy definicji metryk dla obiekt blob, tabel, pliku lub kolejek, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

Odpowiedź zawiera metryki definicji w formacie JSON:

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Odczytać wartości metryki poziomu kont przy użyciu interfejsu API REST

Poniższy przykład pokazuje, jak można odczytać danych metryki na poziomie konta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

W powyżej przykład, jeśli chcesz odczytać wartości metryki dla obiekt blob, tabeli, plik lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

Następującą odpowiedź zawiera metryki wartości w formacie JSON:

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

### <a name="access-metrics-with-the-net-sdk"></a>Metryki dostępu z platformą .net SDK

Azure Monitor udostępnia [.Net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) odczytać metryki definicji i wartości. [Przykładowy kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) przedstawia sposób użycia zestawu SDK z innymi parametrami. Należy użyć `0.18.0-preview` lub nowszej wersji dla metryki magazynu. Identyfikator zasobu jest używana w środowisku .net SDK. Aby uzyskać więcej informacji, przeczytaj [opis identyfikator zasobu dla usług w magazynie](#understanding-resource-id-for-services-in-storage).

Poniższy przykład przedstawia użycie monitora Azure .net SDK odczytać metryki magazynu.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Konto poziomu metryki definicji listy z platformą .net SDK

Poniższy przykład przedstawia sposób wyświetlania definicji metryki na poziomie konta:

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
        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
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

Jeśli chcesz listy definicji metryk dla obiekt blob, tabel, pliku lub kolejek, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Metryki wartości z zestawu SDK platformy .net do odczytu

Poniższy przykład przedstawia sposób odczytać `UsedCapacity` danych na poziomie konta:

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

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metric: "UsedCapacity",

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

W powyżej przykład, jeśli chcesz odczytać wartości metryki dla obiekt blob, tabeli, plik lub kolejki, należy określić różne identyfikatory zasobów dla każdej usługi przy użyciu interfejsu API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Przeczytaj wielowymiarowych wartości metryki z platformą .net SDK

Dla wielowymiarowych metryki należy zdefiniować meta danych filtru, aby odczytać dane na wartość określonego wymiaru.

Poniższy przykład pokazuje, jak można odczytać danych metryki na Metryka obsługi wielu wymiaru:

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

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metric: "BlobCapacity",
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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Identyfikator zasobu opis dla usług w usłudze Azure Storage

Identyfikator zasobu jest identyfikator zasobu na platformie Azure. Korzystając z interfejsu API REST Azure Monitor odczytać definicje metryk lub wartości, należy użyć Identyfikatora zasobu dla zasobu, na którym ma działać. Szablon Identyfikatora zasobu ma następujący format:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Magazyn zapewnia metryk zarówno poziom konta magazynu i poziomu usług z monitorem Azure. Na przykład można pobrać metryki tylko magazynu obiektów Blob. Każdy poziom ma własną identyfikatorów zasobów, które służy do pobierania metryki tylko na tym poziomie.

### <a name="resource-id-for-a-storage-account"></a>Identyfikator zasobu dla konta magazynu

Poniżej przedstawiono format Określ identyfikator ID zasobu dla konta magazynu.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>Identyfikator zasobu dla usług magazynu

Poniżej przedstawiono format Określ identyfikator ID zasobu dla każdej z usług magazynu.

* Identyfikator zasobu usługi blob `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* Identyfikator zasobu usługi tabel `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* Identyfikator zasobu usługi kolejki `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* Identyfikator zasobu usługi plików `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>Identyfikator zasobu w monitorze Azure interfejsu API REST

Poniżej przedstawiono wzorzec używana przy wywoływaniu interfejsu API REST Azure monitora.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Metryki pojemności
Wartości metryk wydajności są wysyłane do monitora Azure co godzinę. Wartości są odświeżane codziennie. Ziarno czasu określa przedział czasu, dla której przedstawiono wartości metryk. Ziarno czasu obsługiwanych na wszystkie metryki pojemności to jedna godzina (PT1H).

Usługa Azure Storage udostępnia następujące metryki wydajności w monitorze Azure.

### <a name="account-level"></a>Poziom konta

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| UsedCapacity | Wielkość magazynu używanego przez konta magazynu. W przypadku kont magazynu w warstwie standardowa jest to Suma pojemności używane przez obiekt blob, tabeli, pliku i kolejki. Dla kont premium magazynu i kont magazynu obiektów Blob jest taka sama jak BlobCapacity. <br/><br/> Jednostka: bajtów <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| BlobCapacity | Suma magazynu obiektów Blob używane w ramach konta magazynu. <br/><br/> Jednostka: bajtów <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 <br/> Wymiar: BlobType ([definicji](#metrics-dimensions)) |
| BlobCount    | Liczba przechowywanych w ramach konta magazynu obiektów blob. <br/><br/> Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 <br/> Wymiar: BlobType ([definicji](#metrics-dimensions)) |
| ContainerCount    | Liczba kontenerów w ramach konta magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |

### <a name="table-storage"></a>Magazyn tabel

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| TableCapacity | Wielkość magazynu tabel używanych przez konto magazynu. <br/><br/> Jednostka: bajtów <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |
| TableCount   | Liczba tabel na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |
| TableEntityCount | Liczba jednostek tabeli na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| QueueCapacity | Wielkość kolejki magazynu używanego przez konta magazynu. <br/><br/> Jednostka: bajtów <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |
| QueueCount   | Liczba kolejek na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |
| QueueMessageCount | Liczba wiadomości w kolejce niewygasłe na koncie magazynu. <br/><br/>Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |

### <a name="file-storage"></a>File Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| FileCapacity | Ilość przechowywania plików używane przez konto magazynu. <br/><br/> Jednostka: bajtów <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |
| FileCount   | Liczba plików w ramach konta magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |
| FileShareCount | Liczba plików udziałów na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: średni <br/> Przykładowe wartości: 1024 |

## <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są wysyłane z usługi Azure Storage do monitorowania Azure co minutę. Wszystkie metryki transakcji są dostępne na poziomie usługi oraz konta (magazynu obiektów Blob, Magazyn tabel Azure plików i magazynu kolejek). Ziarno czasu określa przedział czasu, które są prezentowane wartości metryki. Ziarno czasu obsługiwane dla wszystkich transakcji metryki są PT1H i PT1M.

Usługa Azure Storage udostępnia następujące metryki transakcji w monitorze Azure.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Transakcje | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Zastosowanie wymiarów: wartość ResponseType, GeoType, Nazwa_funkcji_api ([definicji](#metrics-dimensions))<br/> Przykładowe wartości: 1024 |
| Zdarzenia związane z transferem danych przychodzących | Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. <br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Zastosowanie wymiarów: GeoType, Nazwa_funkcji_api ([definicji](#metrics-dimensions)) <br/> Przykładowe wartości: 1024 |
| Ruch wychodzący | Ilość danych wychodzących. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. <br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Zastosowanie wymiarów: GeoType, Nazwa_funkcji_api ([definicji](#metrics-dimensions)) <br/> Przykładowe wartości: 1024 |
| SuccessServerLatency | Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. <br/><br/> Jednostka: w milisekundach <br/> Typ agregacji: średni <br/> Zastosowanie wymiarów: GeoType, Nazwa_funkcji_api ([definicji](#metrics-dimensions)) <br/> Przykładowe wartości: 1024 |
| SuccessE2ELatency | Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. <br/><br/> Jednostka: w milisekundach <br/> Typ agregacji: średni <br/> Zastosowanie wymiarów: GeoType, Nazwa_funkcji_api ([definicji](#metrics-dimensions)) <br/> Przykładowe wartości: 1024 |
| Dostępność | Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez pobranie wartość całkowita liczba żądań rozliczeń i podzielenie go przez liczbę odpowiednie żądania, w tym te żądania, które tworzone nieoczekiwane błędy. Wszystkie powodować nieoczekiwane błędy zmniejszona dostępność usługi magazynu lub określonej operacji interfejsu API. <br/><br/> Jednostka: procent <br/> Typ agregacji: średni <br/> Zastosowanie wymiarów: GeoType, Nazwa_funkcji_api ([definicji](#metrics-dimensions)) <br/> Przykładowe wartości: 99,99 |

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Storage obsługuje następujące wymiary metryki w monitorze Azure.

| Nazwa wymiaru | Opis |
| ------------------- | ----------------- |
| BlobType | Typ obiektu blob dla tylko metryki obiektów Blob. Obsługiwane wartości to **BlockBlob** i **PageBlob**. Dołącz obiekt Blob jest uwzględniona w BlockBlob. |
| Wartość ResponseType | Typ odpowiedzi transakcji. Dostępne wartości to: <br/><br/> <li>ServerOtherError: Inne błędy po stronie serwera, z wyjątkiem opisano te </li> <li> ServerBusyError: Żądanie uwierzytelniony zwrócił kod stanu HTTP 503. </li> <li> ServerTimeoutError: Upłynął limit czasu żądania uwierzytelnionego który zwrócił kod stanu HTTP 500. Nastąpiło przekroczenie limitu czasu z powodu błędu serwera. </li> <li> AuthorizationError: Żądanie uwierzytelniony nie powiodło się z powodu nieautoryzowanego dostępu do danych lub wystąpił błąd autoryzacji. </li> <li> NetworkError: Żądanie uwierzytelniony nie powiodło się z powodu błędów sieci. Najczęściej występuje, gdy klient przedwcześnie zamyka połączenie przed jego wygaśnięciem limitu czasu. </li> <li>    ClientThrottlingError: Błąd ograniczenia przepustowości po stronie klienta. </li> <li> ClientTimeoutError: Upłynął limit czasu żądania uwierzytelnionego który zwrócił kod stanu HTTP 500. Jeśli limit czasu sieci klienta lub limit czasu żądania jest ustawiona na mniejszą wartość niż oczekiwano przez usługę magazynu, jest oczekiwany limitu czasu. W przeciwnym razie zostanie zgłoszone jako ServerTimeoutError. </li> <li> ClientOtherError: Inne błędy po stronie klienta, z wyjątkiem opisane z nich. </li> <li> Powodzenie: Pomyślnie żądania|
| GeoType | Transakcja z podstawowej lub pomocniczej klastra. Dostępne wartości to podstawowe i pomocnicze. Dotyczy to dostęp do odczytu z magazynu geograficznie nadmiarowego Storage(RA-GRS) podczas odczytywania obiektów ze dodatkowej dzierżawy. |
| Nazwa_funkcji_api | Nazwa operacji. Na przykład: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Nazwy wszystkich operacji, zobacz [dokumentu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

Metryki wymiarów pomocnicze należy określić wartość wymiaru, aby wyświetlić odpowiednie wartości metryki. Na przykład, jeśli przyjrzymy się **transakcji** wartość dla pomyślnej odpowiedzi, należy filtrować **wartość ResponseType** wymiaru z **Powodzenie**. Lub jeśli przyjrzymy się **BlobCount** wartość dla blokowych obiektów Blob, należy filtrować **BlobType** wymiaru z **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Ciągłość usługi starszych metryk

Metryki starszej wersji są dostępne równolegle z metryki Monitor Azure zarządzanych. Obsługa utrzymuje takie same, aż do usługi Azure Storage kończy się usługę w starszych metryki.

## <a name="faq"></a>Często zadawane pytania

**Usługi Azure Storage obsługuje metryki dla dysków zarządzane lub niezarządzane dysków?**

Nie, rozwiązań usługi obliczenia Azure obsługuje metryki na dyskach. Zobacz [artykułu](https://azure.microsoft.com/en-us/blog/per-disk-metrics-managed-disks/) więcej szczegółów.

## <a name="next-steps"></a>Kolejne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
