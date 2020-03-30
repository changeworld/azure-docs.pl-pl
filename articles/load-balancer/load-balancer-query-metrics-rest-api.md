---
title: Pobieranie metryk za pomocą interfejsu API REST
titleSuffix: Azure Load Balancer
description: W tym artykule rozpocznij korzystanie z interfejsów API rest platformy Azure do zbierania metryk kondycji i użycia dla modułu Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225254"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Pobierz metryki użycia modułu równoważenia obciążenia przy użyciu interfejsu API REST

Zbieranie liczby bajtów przetworzonych przez [standardowy moduł równoważenia obciążenia](/azure/load-balancer/load-balancer-standard-overview) przez interwał czasu przy użyciu [interfejsu API rest platformy Azure](/rest/api/azure/).

Pełna dokumentacja referencyjna i dodatkowe przykłady interfejsu API REST są dostępne w [odwołaniu rest usługi Azure Monitor.](/rest/api/monitor) 

## <a name="build-the-request"></a>Tworzenie żądania

Użyj następującego żądania GET, aby zebrać [metrykę ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) ze standardowego modułu równoważenia obciążenia. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw na prawidłowy  [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, który identyfikuje subskrypcję platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Nazwa grupy zasobów zawierającej zasób. Tę wartość można uzyskać z interfejsu API usługi Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| loadBalancerName | Nazwa modułu równoważenia obciążenia platformy Azure. |
| nazwy metryki | Oddzielona przecinkami lista prawidłowych [wskaźników modułu równoważenia obciążenia](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> Ten dokument obejmuje `2018-01-01`wersję api, zawartą w powyższym adresie URL.  |
| Timespan | Czas kwerendy. Jest to ciąg o następującym formacie `startDateTime_ISO/endDateTime_ISO`. Ten opcjonalny parametr jest ustawiony na zwracanie danych o wartości dnia w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Dla tej operacji nie jest potrzebna żadna treść żądania.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany po pomyślnym pomyślnym powrocie listy wartości metryki. Pełna lista kodów błędów jest dostępna w [dokumentacji referencyjnej](/rest/api/monitor/metrics/list#errorresponse).

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
