---
title: Pobierz dane użycia maszyny wirtualnej platformy Azure przy użyciu interfejsu API REST
description: Użyj interfejsów API rest platformy Azure do zbierania metryk wykorzystania maszyny wirtualnej.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 07e91f3d9fd32f01db91415bfd90746cd1aef403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944752"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Pobierz metryki użycia maszyny wirtualnej przy użyciu interfejsu API REST

W tym przykładzie pokazano, jak pobrać użycie procesora CPU dla [maszyny wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) przy użyciu interfejsu API rest platformy [Azure](/rest/api/azure/).

Pełna dokumentacja referencyjna i dodatkowe przykłady interfejsu API REST są dostępne w [odwołaniu rest usługi Azure Monitor.](/rest/api/monitor) 

## <a name="build-the-request"></a>Tworzenie żądania

Użyj następującego żądania GET, aby zebrać [metrykę procentowego procesora CPU](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) z maszyny wirtualnej

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | Nazwa grupy zasobów platformy Azure skojarzonej z zasobem. Tę wartość można uzyskać z interfejsu API usługi Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| nazwa wirtualna | Nazwa maszyny wirtualnej platformy Azure. |
| nazwy metryczne | Oddzielona przecinkami lista prawidłowych [wskaźników modułu równoważenia obciążenia](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> Ten dokument obejmuje `2018-01-01`wersję api, zawartą w powyższym adresie URL.  |
| Timespan | Ciąg z następującym formatem, `startDateTime_ISO/endDateTime_ISO` który definiuje zakres czasu zwracanych metryk. Ten opcjonalny parametr jest ustawiony na zwracanie danych o wartości dnia w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Dla tej operacji nie jest potrzebna żadna treść żądania.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany po pomyślnym pomyślnym powrocie listy wartości metryki. Pełna lista kodów błędów jest dostępna w [dokumentacji referencyjnej](/rest/api/monitor/metrics/list#errorresponse).

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
