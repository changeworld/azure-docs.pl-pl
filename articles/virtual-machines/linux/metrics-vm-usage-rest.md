---
title: Pobieranie danych użycia maszyn wirtualnych platformy Azure przy użyciu interfejsu API REST
description: Użyj interfejsów API REST platformy Azure, aby zbierać metryki wykorzystania dla maszyny wirtualnej.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 523b81e53f2b0622b237993dbd88fb9492079c86
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035804"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Pobieranie metryk użycia maszyn wirtualnych przy użyciu interfejsu API REST

Ten przykład pokazuje, jak pobrać użycie procesora dla [maszyny wirtualnej z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) przy użyciu [interfejsu API REST platformy Azure](/rest/api/azure/).

Kompletna dokumentacja referencyjna i dodatkowe przykłady dla interfejsu API REST są dostępne w temacie [Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Żądanie kompilacji

Użyj następującego żądania GET, aby zebrać [procentową metrykę procesora CPU](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) z maszyny wirtualnej

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustawiony na prawidłowy `Bearer`token dostępu[ ](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, który identyfikuje subskrypcję platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Nazwa grupy zasobów platformy Azure skojarzonej z zasobem. Tę wartość można uzyskać za pomocą interfejsu API Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| VMName | Nazwa maszyny wirtualnej platformy Azure. |
| metricnames | Rozdzielana przecinkami lista prawidłowych [metryk Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> W tym dokumencie opisano `2018-01-01`API-Version, zawarte w powyższym adresie URL.  |
| TimeSpan | Ciąg o następującym formacie `startDateTime_ISO/endDateTime_ISO`, który definiuje zakres czasu zwracanych metryk. Ten opcjonalny parametr ma ustawioną wartość zwracającą wartość dnia w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Żadna treść żądania nie jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany, gdy lista wartości metryk zostanie zwrócona pomyślnie. Pełną listę kodów błędów można znaleźć w [dokumentacji referencyjnej](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
