---
title: Pobieranie danych użycia maszyny wirtualnej platformy Azure przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: Interfejsy API REST platformy Azure umożliwiają zbieranie metryk użycia dla maszyny wirtualnej.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 2b888d1ac9b5ebffc95d35ecda4ab8d9d260fa6d
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669857"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Pobierz metryki użycia maszyny wirtualnej przy użyciu interfejsu API REST

W tym przykładzie pokazano, jak pobrać wykorzystaniu Procesora dotyczące [maszyny wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) przy użyciu [interfejsu API REST usługi Azure](/rest/api/azure/).

Pełna dokumentacja i więcej przykładów dla interfejsu API REST są dostępne w [dokumentacja usługi Azure Monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Żądanie kompilacji

Użyj następującego żądania GET do zbierania [metryki procentowe użycie procesora CPU](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) z maszyny wirtualnej

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw `application/json`.|  
|*Autoryzacja:*|Wymagany. Ustawić prawidłową `Bearer` [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Name (Nazwa) | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, która identyfikuje subskrypcji platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). |
| resourceGroupName | Nazwa grupy zasobów platformy Azure skojarzone z zasobem. Tę wartość można uzyskać z interfejsu API usługi Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| vmname | Nazwa maszyny wirtualnej platformy Azure. |
| metricnames | Rozdzielana przecinkami lista prawidłowe [metryk modułu równoważenia obciążenia](/azure/load-balancer/load-balancer-standard-diagnostics). |
| wersja interfejsu API | Wersja interfejsu API do użycia dla żądania.<br /><br /> W tym dokumencie opisano parametru api-version `2018-01-01`zawarte w powyższym adresie URL.  |
| Przedział czasu | Ciąg o następującym formacie `startDateTime_ISO/endDateTime_ISO` definiuje przedział czasu zwracane metryki. Ten parametr opcjonalny jest ustawiony do zwrócenia dnia, przez które dane w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Nie treści żądania jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Dojście do odpowiedzi

Pomyślnie zwrócona lista wartości metryk zwracany jest kod stanu 200. Pełną listę kodów błędów jest dostępna w [dokumentację referencyjną](/rest/api/monitor/metrics/list#errorresponse).

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