---
title: Monitorowanie interfejsu API REST Azure — przewodnik
description: Jak uwierzytelnić żądania i użyć interfejsu API REST usługi Azure Monitor do pobrania dostępne definicje metryk i wartości metryk.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: bbc5aaf02f4ab4388e816faaf8df536770f3302a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205629"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Monitorowanie interfejsu API REST Azure — przewodnik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule pokazano, jak przeprowadzać uwierzytelnianie, dzięki czemu kod może użyć [dokumentacja interfejsu API REST dla usługi Azure Monitor, Microsoft](https://docs.microsoft.com/rest/api/monitor/).

Interfejs API usługi Azure Monitor umożliwia programowe pobieranie definicje metryk dostępnych domyślnych, poziom szczegółowości i wartości metryk. Dane mogą być zapisane w oddzielnym magazynem danych, takich jak Azure SQL Database, Azure Cosmos DB lub Azure Data Lake. W tym miejscu można wykonać dodatkowe analizy, zgodnie z potrzebami.

Oprócz pracy z różnymi punktami danych metryk, interfejs API monitora również umożliwia na liście reguł alertów, wyświetlanie dzienników aktywności i wiele innych. Aby uzyskać pełną listę dostępnych operacji, zobacz [dokumentacja interfejsu API REST dla usługi Azure Monitor, Microsoft](https://docs.microsoft.com/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Żądania uwierzytelniania usługi Azure Monitor

Pierwszym krokiem jest uwierzytelnić żądania.

Wszystkie zadania, które są wykonywane względem interfejsu API usługi Azure Monitor przy użyciu modelu uwierzytelniania usługi Azure Resource Manager. W związku z tym wszystkie żądania muszą uwierzytelnić się za pomocą usługi Azure Active Directory (Azure AD). Jedno z podejść do uwierzytelnienia aplikacja kliencka jest utworzyć nazwę główną usługi Azure AD i pobrać tokenu uwierzytelniania (JWT). Widoczny poniżej przykładowy skrypt demonstruje tworzenie podmiotu zabezpieczeń za pomocą programu PowerShell usługi Azure AD. Aby uzyskać bardziej szczegółowy przewodnik, zapoznaj się z dokumentacją na [przy użyciu programu Azure PowerShell, aby utworzyć jednostkę usługi, aby uzyskiwać dostęp do zasobów](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Istnieje również możliwość [utworzyć nazwę główną usługi za pośrednictwem witryny Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Do wykonywania zapytań interfejsu API usługi Azure Monitor, aplikacja kliencka powinna użyć jednostki usługi utworzonej wcześniej do uwierzytelniania. Poniższy przykład skryptu programu PowerShell przedstawia jedną podejście, za pomocą [Active Directory Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md) (ADAL), można uzyskać tokenu uwierzytelniania tokenu JWT. JWT token jest przekazywany jako część parametru autoryzacji HTTP w żądaniach wysyłanych do interfejsu API REST usługi Azure Monitor.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Po uwierzytelnieniu, następnie można wykonywać zapytania względem interfejsu API REST usługi Azure Monitor. Istnieją dwie przydatne kwerendy:

1. Listę definicji metryk dla zasobu
2. Pobieranie wartości metryk

> [!NOTE]
> Aby uzyskać dodatkowe informacje na temat uwierzytelniania za pomocą interfejsu API REST platformy Azure, zapoznaj się [dokumentacja interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Pobierz definicje metryk (wielowymiarowych interfejs API)

Użyj [definicje usługi Azure Monitor metryki interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) dostępu do listy metryk, które są dostępne dla usługi.

**Metoda**: GET

**Identyfikator URI żądania**: https:\/\/management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{zasobu resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{ apiVersion}*

Na przykład aby pobierać definicje metryk dla konta usługi Azure Storage, żądanie będzie wyglądać następująco:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Aby pobrać definicje metryk za pomocą usługi Azure Monitor metryk wielowymiarowych interfejsu API REST, należy użyć "2018-01-01" jako wersję interfejsu API.
>
>

Wynikowy treści odpowiedzi JSON będzie podobny do poniższego przykładu: (Zwróć uwagę, że druga metryka ma wymiarów)

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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Pobieranie wartości wymiaru (wielowymiarowych interfejs API)

Po dostępne definicje metryki są znane, może to być niektóre metryki, które ma wymiarów. Przed wykonaniem kwerendy dla metryki może być, aby dowiedzieć się, jakie zakres wartości wymiaru ma. Na podstawie tych wartości wymiarów, które następnie można filtrować lub segmentu metryki na podstawie wymiaru wartości podczas wysyłania zapytania dotyczące metryk.  Użyj [API REST usługi Azure Monitor metryki](https://docs.microsoft.com/rest/api/monitor/metrics) można to osiągnąć.

Nazwa metryki "value" (nie "localizedValue") na użytek filtrowania żądań. Jeśli nie określono żadnych filtrów, zwracany jest metryki domyślnej. Użycie tego interfejsu API umożliwia tylko jednego wymiaru ma filtr z symbolami wieloznacznymi.

> [!NOTE]
> Aby pobrać wartości wymiaru przy użyciu interfejsu API REST usługi Azure Monitor, należy użyć "2018-01-01" jako wersję interfejsu API.
>
>

**Metoda**: GET

**Identyfikator URI żądania**: https\://management.azure.com/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów —}*/providers/*{ — przestrzeń nazw dostawcy zasobów}*/*{typ_zasobu}*/*{Nazwa zasobu}*/providers/microsoft.insights/metrics? metricnames =*{Metryka}*& timespan =*{starttime/endtime}*& $filter =*{filter}*& resultType = metadanych & parametru api-version = *{apiVersion}*

Na przykład, można pobrać listy wartości wymiarów, które zostały emitowane na "dimension Nazwa interfejsu API" dla metryki "Transakcji", gdzie wymiaru GeoType = "Podstawowa" w określonym czasie, żądanie będzie wyglądało następująco:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Wynikowy treści odpowiedzi JSON będzie podobny do poniższego przykładu:

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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Pobieranie wartości metryk (wielowymiarowych interfejs API)

Po definicji metryk dostępnych i możliwych wartości są znane, następnie jest możliwe do pobrania powiązanych wartości metryk.  Użyj [API REST usługi Azure Monitor metryki](https://docs.microsoft.com/rest/api/monitor/metrics) można to osiągnąć.

Nazwa metryki "value" (nie "localizedValue") na użytek filtrowania żądań. Jeśli nie określono żadnych filtrów wymiaru, zwracany jest zestawiona metryki zagregowane. Metryki zapytanie zwraca wiele szereg czasowy, opcji można użyć "Najważniejsze" i "OrderBy" Parametry zapytania do zwrócenia ograniczone uporządkowaną listą Szeregi czasowe.

> [!NOTE]
> Aby pobrać wartości za pomocą metryk wielowymiarowych za pomocą interfejsu API REST usługi Azure Monitor, należy użyć "2018-01-01" jako wersję interfejsu API.
>
>

**Metoda**: GET

**Identyfikator URI żądania**: https://management.azure.com/subscriptions/ *{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów —}*/providers/*{— — przestrzeń nazw dostawcy zasobów}* / *{typ_zasobu}*/*{Nazwa zasobu}*/providers/microsoft.insights/metrics?metricnames=*{Metryka}*& timespan =*{starttime/endtime}*& $filter =*{filter}*& interwał =*{timeGrain}*& agregacji =*{ aggreation}*& parametru api-version =*{apiVersion}*

Na przykład, można pobrać pierwsze 3 interfejsów API, malejąco wartość przez liczbę transakcji, w zakresie 5 min, tam, gdzie GeotType nie "Podstawowa", żądanie będzie wyglądało następująco:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Wynikowy treści odpowiedzi JSON będzie podobny do poniższego przykładu:

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

## <a name="retrieve-metric-definitions"></a>Pobierz definicje metryk

Użyj [definicje usługi Azure Monitor metryki interfejsu API REST](https://msdn.microsoft.com/library/mt743621.aspx) dostępu do listy metryk, które są dostępne dla usługi.

**Metoda**: GET

**Identyfikator URI żądania**: https:\/\/management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{zasobu resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{ apiVersion}*

Na przykład aby pobierać definicje metryk dla aplikacji logiki platformy Azure, żądanie będzie wyglądać następująco:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Aby pobrać definicje metryk za pomocą interfejsu API REST usługi Azure Monitor, należy użyć "2016-03-01" jako wersję interfejsu API.
>
>

Wynikowy treści odpowiedzi JSON będzie podobny do poniższego przykładu:

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

Aby uzyskać więcej informacji, zobacz [listę definicji metryk dla zasobu w interfejsie API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentacji.

## <a name="retrieve-metric-values"></a>Pobieranie wartości metryk

Po dostępne definicje metryki są znane, następnie jest możliwe do pobrania powiązanych wartości metryk. Nazwa metryki "value" (nie "localizedValue") na użytek żądań filtrowania (na przykład, pobrać punktów danych metryk "CpuTime" i "Żądania"). Jeśli nie określono żadnych filtrów, zwracany jest metryki domyślnej.

> [!NOTE]
> Aby pobrać wartości metryk za pomocą interfejsu API REST usługi Azure Monitor, należy użyć "2016-09-01" jako wersję interfejsu API.
>
>

**Metoda**: GET

**Identyfikator URI żądania**: https://management.azure.com/subscriptions/ *{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów —}*/providers/*{— — przestrzeń nazw dostawcy zasobów}* / *{typ_zasobu}*/*{Nazwa zasobu}*/providers/microsoft.insights/metrics?$filter=*{filter}*& parametru api-version =*{apiVersion}*

Na przykład aby pobrać punktów danych metryk RunsSucceeded dla zakresu w danym momencie i ziarnem czasu godziny, żądanie będzie się następująco:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Wynikowy treści odpowiedzi JSON będzie podobny do poniższego przykładu:

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

Aby pobrać wiele punktów danych lub agregacji, Dodaj definicję metryki nazwy i typy agregacji do filtru, jak pokazano w poniższym przykładzie:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Wynikowy treści odpowiedzi JSON będzie podobny do poniższego przykładu:

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

Dodatkowe podejściem jest użycie [ARMClient](https://github.com/projectkudu/armclient) na komputerze Windows. ARMClient automatycznie obsługuje uwierzytelnianie w usłudze Azure AD (i wynikowy token JWT). Poniższe kroki przedstawiają użytkowania ARMClient pobierania danych metryki:

1. Zainstaluj [Chocolatey](https://chocolatey.org/) i [ARMClient](https://github.com/projectkudu/armclient).
2. W oknie terminalu wpisz *logowania armclient.exe*. Ten sposób wyświetli monit o logowanie do platformy Azure.
3. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Na przykład aby można było pobierać definicje metryk dla określonych aplikacji logiki, należy wydać następujące polecenie:

```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Pobierz identyfikator zasobu

Za pomocą interfejsu API REST naprawdę pomaga zrozumieć dostępne definicje metryk, poziom szczegółowości i powiązane wartości. Informacje są przydatne, gdy za pomocą [biblioteki zarządzania platformy Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Dla poprzedniego kodu identyfikator zasobu do użycia jest pełna ścieżka do żądanego zasobu platformy Azure. Na przykład do wykonywania zapytań względem usługi Azure Web App, będzie identyfikator zasobu:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Poniższa lista zawiera kilka przykładów formatów identyfikator zasobu dla różnych zasobów platformy Azure:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastyczna pula SQL** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów —}*/providers/Microsoft.Sql/servers/*{puli db}*/elasticpools/*{sql puli name}*
* **SQL Database (v12)** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów —}*/providers/Microsoft.Sql/servers/*{nazwa serwera}*/databases/*{Nazwa bazy danych}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Zestawy skalowania maszyn wirtualnych** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów —}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{nazwa_maszyny_wirtualnej}*
* **VMs** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Usługa Event Hubs** -/subscriptions/*{identyfikator subskrypcji}*/resourceGroups/*{— Nazwa grupy zasobów —}*/providers/Microsoft.EventHub/namespaces/*{ przestrzeń nazw usługi Event Hub}*

Istnieją alternatywne sposoby pobierania identyfikator zasobu, w tym o korzystaniu z Eksploratora zasobów Azure, wyświetlając żądanego zasobu w witrynie Azure portal, a także za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure

Aby znaleźć identyfikator zasobu dla żądanego zasobu, co przydatne podejściem jest użycie [Eksploratora zasobów Azure](https://resources.azure.com) narzędzia. Przejdź do żądanego zasobu, a następnie sprawdź identyfikator pokazano, jak na poniższym zrzucie ekranu:

![ALT "Eksplorator zasobów Azure"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

Identyfikator zasobu można uzyskać w taki sposób, w witrynie Azure portal. Aby to zrobić, przejdź do żądanego zasobu, a następnie wybierz właściwości. Identyfikator zasobu jest wyświetlany w sekcji właściwości, jak pokazano na poniższym zrzucie ekranu:

![ALT zasobu "ID" wyświetlanych w bloku właściwości w witrynie Azure portal](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Identyfikator zasobu można pobrać przy użyciu poleceń cmdlet programu Azure PowerShell w także. Na przykład aby uzyskać identyfikator zasobu dla aplikacji logiki platformy Azure, wykonaj następujące polecenie cmdlet Get-AzureLogicApp jak w poniższym przykładzie:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Wyniki powinny wyglądać podobnie do poniższego przykładu:

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

Aby pobrać identyfikator zasobu dla konta usługi Azure Storage przy użyciu wiersza polecenia platformy Azure, wykonaj `az storage account show` polecenia, jak pokazano w poniższym przykładzie:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Wyniki powinny wyglądać podobnie do poniższego przykładu:

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
> Usługa Azure Logic Apps nie są jeszcze dostępne za pośrednictwem wiersza polecenia platformy Azure, co konto usługi Azure Storage jest wyświetlany w poprzednim przykładzie.
>
>

## <a name="retrieve-activity-log-data"></a>Pobieranie danych dziennika aktywności

Oprócz definicje metryk i powiązanych wartości jest również możliwe, aby pobrać dodatkowe interesujących szczegółowych informacji powiązanych z zasobami platformy Azure przy użyciu interfejsu API REST usługi Azure Monitor. Na przykład istnieje możliwość kwerendy [dziennika aktywności](https://msdn.microsoft.com/library/azure/dn931934.aspx) danych. W poniższym przykładzie pokazano, za pomocą interfejsu API REST usługi Azure Monitor przesyłać zapytania dotyczące danych dziennika aktywności w określonym zakresie dat. dla subskrypcji platformy Azure:

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [omówienie monitorowania](../../azure-monitor/overview.md).
* Widok [metryki obsługiwane z usługą Azure Monitor](metrics-supported.md).
* Przegląd [platformy Microsoft Azure monitorowanie dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Przegląd [biblioteki zarządzania systemu Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).