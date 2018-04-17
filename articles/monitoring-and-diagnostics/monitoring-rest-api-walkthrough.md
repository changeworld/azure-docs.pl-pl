---
title: Azure wskazówki monitorowania interfejsu API REST | Dokumentacja firmy Microsoft
description: Sposób uwierzytelniania żądań i pobrać dostępnej definicji metryk i metryki wartości za pomocą interfejsu API REST Monitor Azure.
author: mcollier
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.search.region: ''
ms.search.scope: ''
ms.search.validFrom: ''
ms.dyn365.ops.version: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: mcollier
ms.openlocfilehash: a87f60b04806fb337a9b4558a67ffa11da661ad5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Wskazówki monitorowania interfejsu API REST Azure
W tym artykule przedstawiono sposób uwierzytelniania, tak aby korzystać z kodu [Microsoft Azure Monitor REST API Reference](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

Monitor interfejsu API Azure umożliwia programowane pobieranie definicji metryk dostępnych domyślnych, poziom szczegółowości i wartości metryki. Dane można zapisać w magazynie oddzielnego danych, takie jak bazy danych SQL Azure, Azure DB rozwiązania Cosmos lub usługi Azure Data Lake. Z tego miejsca dodatkowe analizy mogą być wykonywane zgodnie z potrzebami.

Oprócz pracy z różnych punktów danych metryki, interfejsu API Monitor również umożliwia wyświetlić listę reguł alertów, widok Dzienniki aktywności i o wiele więcej. Aby uzyskać pełną listę dostępnych operacji, zobacz [Microsoft Azure Monitor REST API Reference](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Monitor Azure uwierzytelniania żądania
Pierwszym krokiem jest uwierzytelnić żądania.

Wszystkie zadania wykonywane monitora interfejsu API Azure używają modelu uwierzytelniania usługi Azure Resource Manager. W związku z tym wszystkie żądania musi zostać uwierzytelniony w usłudze Azure Active Directory (Azure AD). Jeden ze sposobów uwierzytelniania aplikacji klienckiej jest tworzenie nazwy głównej usługi Azure AD i pobrać tokenu uwierzytelniania (JWT). Poniższy przykładowy skrypt pokazuje tworzenie główną za pomocą programu PowerShell usługi Azure AD. Aby uzyskać bardziej szczegółowy przewodnik, zapoznaj się z dokumentacją na [Tworzenie nazwy głównej usługi dostępu do zasobów przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Istnieje również możliwość [Tworzenie nazwy głównej usługi za pośrednictwem portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Aby odpytać interfejsu API Azure monitora, aplikacja kliencka należy używać główną usługi utworzonej wcześniej do uwierzytelniania. W poniższym przykładzie skrypt programu PowerShell pokazano jedną podejścia, przy użyciu [biblioteki uwierzytelniania usługi Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL), można uzyskać tokenu uwierzytelniania tokenu JWT. JWT token jest przekazywany jako część parametrem autoryzacji HTTP w żądaniach do interfejsu API REST Azure monitora.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $secureStringPassword)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Po uwierzytelnieniu następnie można wykonać zapytania do interfejsu API REST Monitor Azure. Istnieją dwa zapytania pomocne:

1. Lista definicje metryki dla zasobu
2. Pobieranie wartości metryki

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Pobieranie definicji metryk (wielowymiarowych interfejsu API)

Użyj [Metryka Monitor Azure definicji interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) dostęp do listy metryk, które są dostępne dla usługi.

**Metoda**: GET

**Identyfikator URI żądania**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{zasobu resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Na przykład można pobrać definicji metryk dla konta usługi Azure Storage, żądanie będzie wyglądać następująco:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Aby pobrać definicji metryk przy użyciu wielowymiarowych metryki Azure Monitor interfejsu API REST, należy użyć "2018-01-01" jako wersja interfejsu API.
>
>

Wynikowa treści odpowiedzi JSON będzie podobny do poniższego przykładu: (należy pamiętać, że druga metryka ma wymiary)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Pobieranie wartości wymiaru (wielowymiarowych interfejsu API)
Po definicji metryk dostępne są znane, może to być niektóre metryki, które mają wymiary. Przed wykonaniem kwerendy metryki może być, aby dowiedzieć się, jakie wartości z zakresu wymiar ma. Na podstawie tych wartości wymiaru, który można wybrać do filtrowania lub segmentu metryki na podstawie wymiaru wartości podczas wykonywania zapytania dotyczącego metryki.  Użyj [interfejsu API REST Azure Monitor metryki](https://docs.microsoft.com/rest/api/monitor/metrics) można to osiągnąć.

Nazwa metryki "wartość" (nie "localizedValue") na użytek filtrowania żądań. Jeżeli nie określono żadnych filtrów, jest zwracana Metryka domyślnej. Użycie tego interfejsu API umożliwia tylko jeden wymiar ma Filtr symbolu wieloznacznego.

> [!NOTE]
> Aby pobrać wartości wymiaru przy użyciu interfejsu API REST Azure Monitor, należy użyć "2018-01-01" jako wersja interfejsu API.
>
>

**Metoda**: GET

**Identyfikator URI żądania**: https://management.azure.com/subscriptions/ *{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów-}*/providers/*{-— przestrzeń nazw dostawcy zasobów}* / *{typ zasobu}*/*{nazwa}*/providers/microsoft.insights/metrics?metric=*{Metryka}* & timespan =*{starttime/endtime}*& $filter =*{filtru}*& resultType = metadanych & api-version =*{apiVersion}*

Na przykład, aby pobrać listę wartości wymiaru, które zostały wyemitowane gdzie "Nazwa interfejsu API wymiaru" metryki "Transakcji" wymiar GeoType = "Primary" w określonym czasie, żądanie będzie wyglądało następująco:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Wynikowa treści odpowiedzi JSON będzie podobny do poniższego przykładu:

```JSON
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]    
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Pobieranie wartości metryki (wielowymiarowych interfejsu API)
Po definicji metryk dostępnych i możliwych wartości są znane, następnie jest możliwe do pobierania wartości powiązane metryki.  Użyj [interfejsu API REST Azure Monitor metryki](https://docs.microsoft.com/rest/api/monitor/metrics) można to osiągnąć.

Nazwa metryki "wartość" (nie "localizedValue") na użytek filtrowania żądań. Jeżeli nie określono żadnych filtrów wymiarów, jest zwracana zestawionych zagregowane metryki. Jeśli Metryka zapytanie zwraca wiele timeseries, można Użyj parametrów zapytania "Top" i "OrderBy", aby zwrócić ograniczone uporządkowaną listę timeseries.

> [!NOTE]
> Aby pobrać wielowymiarowych metryki wartości przy użyciu interfejsu API REST Azure Monitor, należy użyć "2018-01-01", jako wersja interfejsu API.
>
>

**Metoda**: GET

**Identyfikator URI żądania**: https://management.azure.com/subscriptions/ *{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów-}*/providers/*{-— przestrzeń nazw dostawcy zasobów}* / *{typ zasobu}*/*{nazwa}*/providers/microsoft.insights/metrics?metric=*{Metryka}* & timespan =*{starttime/endtime}*& $filter =*{filtru}*& interwał =*{ziarnem czasu}*& agregacji =*{ aggreation}*& api-version =*{apiVersion}*

Na przykład można pobrać 3 pierwszych interfejsów API, malejąco wartość przez liczbę transakcji, w zakresie 5 minut, gdzie GeotType został "Podstawowy", żądanie będzie wyglądało następująco:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Wynikowa treści odpowiedzi JSON będzie podobny do poniższego przykładu:

```JSON
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Pobieranie definicji metryk
Użyj [Metryka Monitor Azure definicji interfejsu API REST](https://msdn.microsoft.com/library/mt743621.aspx) dostęp do listy metryk, które są dostępne dla usługi.

**Metoda**: GET

**Identyfikator URI żądania**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{zasobu resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Na przykład można pobrać definicji metryk dla aplikacji logiki platformy Azure, żądanie będzie wyglądać następująco:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Aby pobrać definicji metryk przy użyciu interfejsu API REST Azure Monitor, należy użyć "2016-03-01" jako wersja interfejsu API.
>
>

Wynikowa treści odpowiedzi JSON będzie podobny do poniższego przykładu:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Aby uzyskać więcej informacji, zobacz [listy definicji metryk zasobu w interfejsie API REST Monitor Azure](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentacji.

## <a name="retrieve-metric-values"></a>Pobieranie wartości metryki
Po definicji metryk dostępne są znane, następnie jest możliwe do pobierania wartości powiązane metryki. Nazwa metryki "wartość" (nie "localizedValue") na użytek żądań filtrowania (na przykład pobierania punktów danych metryki "CpuTime" i "Żądania"). Jeżeli nie określono żadnych filtrów, jest zwracana Metryka domyślnej.

> [!NOTE]
> Aby pobrać metryki wartości przy użyciu interfejsu API REST Azure Monitor, należy użyć "2016-09-01" jako wersja interfejsu API.
>
>

**Metoda**: GET

**Identyfikator URI żądania**: https://management.azure.com/subscriptions/ *{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów-}*/providers/*{-— przestrzeń nazw dostawcy zasobów}* / *{typ zasobu}*/*{nazwa}*/providers/microsoft.insights/metrics?$filter=*{filtru}*& api-version =*{apiVersion}*

Na przykład pobierania punktów danych metryki RunsSucceeded dla danego okresie i ziarno czasu 1 godz, żądanie będzie następujące:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

Wynikowa treści odpowiedzi JSON będzie podobny do poniższego przykładu:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Aby pobrać wiele punktów danych lub agregacji, Dodaj metryki definicji nazwy i typy agregacji do filtra, jak pokazano w poniższym przykładzie:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
Wynikowa treści odpowiedzi JSON będzie podobny do poniższego przykładu:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Użyj ARMClient
Dodatkowe rozwiązaniem jest użycie [ARMClient](https://github.com/projectkudu/armclient) na komputerze z systemem Windows. ARMClient obsługuje automatycznie uwierzytelniania usługi Azure AD (i tokenu JWT). Poniższe kroki przedstawiają użycie ARMClient pobierania danych metryki:

1. Zainstaluj [Chocolatey](https://chocolatey.org/) i [ARMClient](https://github.com/projectkudu/armclient).
2. W oknie terminalu, wpisz *logowania armclient.exe*. Dzięki temu monituje o logowanie do platformy Azure.
3. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Na przykład aby można było pobrać definicji metryk dla określonej aplikacji logiki, należy wydać następujące polecenie:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Pobierz identyfikator zasobu
Dostępne metryki definicje, poziom szczegółowości i powiązane wartości naprawdę może pomóc przy użyciu interfejsu API REST. Informacje są pomocne przy użyciu [Biblioteka zarządzania Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Dla poprzedniego kodu identyfikator zasobu do użycia jest pełna ścieżka do żądanego zasobu platformy Azure. Na przykład wykonać zapytanie względem aplikacji sieci Web platformy Azure, będzie identyfikator zasobu:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Poniższa lista zawiera kilka przykładów formaty identyfikator zasobu dla różnych zasobów platformy Azure:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Puli elastycznej SQL** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów-}*/providers/Microsoft.Sql/servers/*{puli db}*/elasticpools/*{nazwa sql puli}*
* **Baza danych SQL (v12)** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów-}*/providers/Microsoft.Sql/servers/*{nazwa serwera}*/databases/*{Nazwa bazy danych}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Zestawy skalowania maszyny wirtualnej** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów-}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{Nazwa maszyny wirtualnej}*
* **VMs** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Centra zdarzeń** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów-}*/providers/Microsoft.EventHub/namespaces/*{ przestrzeń nazw eventhub}*

Istnieje kilka alternatywnych rozwiązań do pobierania identyfikator zasobu, w tym o korzystaniu z Eksploratora zasobów Azure, wyświetlanie żądanego zasobu w portalu Azure i przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure.

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure
Aby znaleźć identyfikator zasobu dla żądanego zasobu, co przydatne rozwiązaniem jest użycie [Eksploratora zasobów Azure](https://resources.azure.com) narzędzia. Przejdź do żądanego zasobu, a następnie sprawdź identyfikator pokazano, jak na poniższym zrzucie ekranu:

![ALT "Eksploratora zasobów Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal
Identyfikator zasobu można uzyskać w taki sposób, w portalu Azure. Aby to zrobić, przejdź do żądanego zasobu, a następnie wybierz polecenie Właściwości. Identyfikator zasobu jest wyświetlany w sekcji właściwości, jak pokazano na poniższym zrzucie ekranu:

![ALT "identyfikator zasobu wyświetlane w bloku właściwości, w portalu Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Identyfikator zasobu można pobrać przy użyciu również polecenia cmdlet programu Azure PowerShell. Na przykład aby uzyskać identyfikator zasobu dla aplikacji logiki platformy Azure, wykonaj polecenie cmdlet Get-AzureLogicApp, jak w poniższym przykładzie:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Wynik powinien być podobny do poniższego przykładu:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby pobrać identyfikator zasobu dla konta usługi Azure Storage za pomocą interfejsu wiersza polecenia Azure, wykonaj polecenie "Pokaż konto magazynu az", jak pokazano w poniższym przykładzie:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Wynik powinien być podobny do poniższego przykładu:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Aplikacje logiki platformy Azure nie są jeszcze dostępne za pośrednictwem interfejsu wiersza polecenia Azure, w związku z tym konta usługi Azure Storage jest wyświetlany w poprzednim przykładzie.
>
>

## <a name="retrieve-activity-log-data"></a>Pobieranie danych dziennika aktywności
Oprócz definicji metryk oraz powiązanych wartości jest także możliwość użycia interfejsu API REST Monitor Azure można pobrać dodatkowe interesujące informacje szczegółowe dotyczące zasobów platformy Azure. Na przykład istnieje możliwość zapytania [dziennik aktywności](https://msdn.microsoft.com/library/azure/dn931934.aspx) danych. W poniższym przykładzie pokazano, przy użyciu interfejsu API REST Monitor Azure wykonać zapytania o dane dziennika aktywności w określonym zakresie dat. subskrypcji platformy Azure:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Kolejne kroki
* Przegląd [omówienie monitorowania](monitoring-overview.md).
* Widok [obsługiwane metryki z monitorem Azure](monitoring-supported-metrics.md).
* Przegląd [platformy Microsoft Azure monitorować dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Przegląd [Biblioteka zarządzania Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
