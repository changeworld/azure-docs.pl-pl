---
title: Przeglądanie użycia zasobów usługi platformy Azure przy użyciu interfejsu API REST | Microsoft Docs
description: Dowiedz się, jak używać interfejsów API REST platformy Azure do przeglądania użycia zasobów usługi platformy Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443061"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Przeglądanie użycia zasobów platformy Azure przy użyciu interfejsu API REST

Azure Cost Management interfejsy API ułatwiają przeglądanie i zarządzanie użyciem zasobów platformy Azure.

W tym artykule dowiesz się, jak utworzyć raport dzienny, który wygeneruje dokument z wartościami rozdzielanymi przecinkami o informacje o godzinie użycia, a następnie jak używać filtrów do dostosowywania raportu, aby można było wysyłać zapytania dotyczące użycia maszyn wirtualnych, baz danych i otagowanych zasoby w grupie zasobów platformy Azure.

>[!NOTE]
> Interfejs API Cost Management jest obecnie w prywatnej wersji zapoznawczej.

## <a name="create-a-basic-cost-management-report"></a>Tworzenie raportu o podstawowym zarządzaniu kosztami

`reports` Użyj operacji w interfejsie API Cost Management, aby określić sposób generowania raportowania kosztów i miejsce, w którym będą publikowane raporty.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` Parametr jest wymagany i powinien zawierać identyfikator subskrypcji, który można odczytać przy użyciu poświadczeń podanych w tokenie interfejsu API. Polu`{reportName}`

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*| Wymagane. Ustaw wartość `application/json`. |  
|*Authorization:*| Wymagana. Ustaw prawidłowy `Bearer` token. |

Skonfiguruj parametry raportu w treści żądania HTTP. W poniższym przykładzie raport jest ustawiany do generowania każdego dnia, gdy jest aktywny, jest plikiem CSV zapisanym w kontenerze obiektów BLOB usługi Azure Storage i zawiera godzinowe informacje o kosztach dla wszystkich zasobów w `westus`grupie zasobów.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Dla zasobu

## <a name="filtering-reports"></a>Filtrowanie raportów

Sekcja `filter` i`dimensions` treści żądania podczas tworzenia raportu umożliwia skoncentrowanie się na kosztach dla określonych typów zasobów. W poprzedniej treści żądania pokazano, jak filtrować według wszystkich zasobów w regionie. 

### <a name="get-all-compute-usage"></a>Pobierz wszystkie użycie obliczeniowe

Za pomocą `ResourceType` wymiaru można raportować koszty maszyn wirtualnych platformy Azure w ramach subskrypcji we wszystkich regionach.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Pobierz wszystkie użycie bazy danych

Za pomocą `ResourceType` wymiaru można raportować koszty Azure SQL Database w ramach subskrypcji we wszystkich regionach.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Raport dotyczący określonych wystąpień

`Resource` Wymiar umożliwia raportowanie kosztów określonych zasobów.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Zmienianie przedziałów czasu

`timeframe` Ustaw`Custom` definicję, aby ustawić limit czasu poza tydzień na datę i miesiąc, aby wyświetlić opcje wbudowane.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)   
