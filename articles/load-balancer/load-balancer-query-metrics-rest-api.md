---
title: Pobieranie metryk przy użyciu interfejsu API REST
titlesuffix: Azure Load Balancer
description: Użyj interfejsów API REST platformy Azure, aby zbierać metryki dotyczące kondycji i wykorzystania dla Load Balancer dla danego zakresu czasu i dat.
services: sql-database
author: asudbring
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: allensu
ms.openlocfilehash: 0d12dc04aff58dd6273d8d29d422bdbd9e7c886b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274528"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Uzyskiwanie metryk użycia Load Balancer przy użyciu interfejsu API REST

W tym przykładzie pokazano, jak zbierać liczbę bajtów przetworzonych przez [Usługa Load Balancer w warstwie Standardowa](/azure/load-balancer/load-balancer-standard-overview) przez przedział czasu przy użyciu [interfejsu API REST platformy Azure](/rest/api/azure/).

Kompletna dokumentacja referencyjna i dodatkowe przykłady dla interfejsu API REST są dostępne w temacie [Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Żądanie kompilacji

Użyj następującego żądania GET, aby zebrać [metrykę byteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) z usługa Load Balancer w warstwie Standardowa. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagana. Ustaw prawidłowy `Bearer` [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Name (Nazwa) | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, który identyfikuje subskrypcję platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Nazwa grupy zasobów zawierającej zasób. Tę wartość można uzyskać za pomocą interfejsu API Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| loadBalancerName | Nazwa Azure Load Balancer. |
| metricnames | Rozdzielana przecinkami lista prawidłowych [metryk Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> Ten dokument obejmuje interfejs API- `2018-01-01`Version uwzględniony w powyższym adresie URL.  |
| TimeSpan | Przedział czasu zapytania. Jest to ciąg o następującym formacie `startDateTime_ISO/endDateTime_ISO`. Ten opcjonalny parametr ma ustawioną wartość zwracającą wartość dnia w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Żadna treść żądania nie jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany, gdy lista wartości metryk zostanie zwrócona pomyślnie. Pełną listę kodów błędów można znaleźć w [dokumentacji referencyjnej](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
