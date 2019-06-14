---
title: Sprawdź użycie zasobów usługi platformy Azure przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą interfejsów API REST platformy Azure Sprawdź użycie zasobów usługi platformy Azure.
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
ms.author: erikre
ms.openlocfilehash: d3db4166810da981ff0117536d8550a6b2203924
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370989"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Sprawdź użycie zasobów platformy Azure przy użyciu interfejsu API REST

Pomoc platformy Azure interfejsów API usługi Cost Management możesz przejrzeć i zarządzać nimi zużycie zasobów platformy Azure.

W tym artykule dowiesz się, jak utworzyć dzienny raport, który wygeneruje dokument z wartości rozdzielanych przecinkami godzinowe informacje o użyciu, a następnie użyj filtrów, aby dostosować raport, dzięki czemu można tworzyć zapytania użytkowania maszyn wirtualnych, baz danych i oznaczony zasoby w grupie zasobów platformy Azure.

>[!NOTE]
> Interfejs API zarządzania kosztami jest aktualnie w prywatnej wersji zapoznawczej.

## <a name="create-a-basic-cost-management-report"></a>Utwórz raport zarządzania kosztu podstawowego

Użyj `reports` operacji w interfejsie API zarządzania kosztami do definiowania sposobu generowania raportowanie kosztów i której będą publikowane raporty.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` Parametr jest wymagany i może zawierać Identyfikatora subskrypcji, który może zostać odczytany przy użyciu poświadczeń podanych we token interfejsu API. W `{reportName}`

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*| Wymagany. Ustaw `application/json`. |  
|*Authorization:*| Wymagany. Ustawić prawidłową `Bearer` tokenu. |

Skonfiguruj parametry raportu w treści żądania HTTP. W poniższym przykładzie ustawiono raportu do wygenerowania każdego dnia, gdy aktywny, jest plikiem CSV zapisywane w kontenerze obiektów blob usługi Azure Storage i co godzinę zawiera informacje o kosztach dla wszystkich zasobów w grupie zasobów `westus`.

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

`filter` i `dimensions` części treści żądania podczas tworzenia raportu pozwalają skupić się w kosztów dla określonych typów zasobów. Treść żądania w poprzednim pokazuje sposób filtrowania przez wszystkie zasoby w regionie. 

### <a name="get-all-compute-usage"></a>Pobierz wszystkie wykorzystanie mocy obliczeniowej

Użyj `ResourceType` wymiaru, aby zgłosić kosztów maszyn wirtualnych platformy Azure w ramach subskrypcji we wszystkich regionach.

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

### <a name="get-all-database-usage"></a>Pobierz wszystkie bazy danych użycia

Użyj `ResourceType` wymiaru raportu kosztów usługi Azure SQL Database w ramach subskrypcji we wszystkich regionach.

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

### <a name="report-on-specific-instances"></a>Raport na temat konkretnych wystąpień

`Resource` Wymiar pozwala raportu kosztów określonych zasobów.

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

### <a name="changing-timeframes"></a>Zmiana harmonogramów

Ustaw `timeframe` definicji `Custom` można ustawić przedział czasowy poza tygodnia do dnia i miesiąca do wbudowanych opcji daty.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Kolejne kroki
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)   
