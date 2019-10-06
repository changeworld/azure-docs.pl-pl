---
title: Pobierz zmiany zasobów
description: Dowiedz się, jak znaleźć, kiedy zasób został zmieniony i uzyskać listę właściwości, które uległy zmianie.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980336"
---
# <a name="get-resource-changes"></a>Pobierz zmiany zasobów

Zasoby są zmieniane w trakcie codziennego użytkowania, ponownej konfiguracji i nawet ponownego wdrożenia.
Zmiany mogą pochodzić od osoby lub przez proces zautomatyzowany. Większość zmian jest zaprojektowana, ale czasami nie jest. W przypadku historii zmian z ostatnich 14 dni usługa Azure Resource Graph umożliwia:

- Znajdź, kiedy zostały wykryte zmiany we właściwości Azure Resource Manager.
- Zobacz, jakie właściwości zostały zmienione w ramach tego zdarzenia zmiany.

Wykrywanie zmian i szczegółowe informacje są przydatne dla następujących przykładowych scenariuszy:

- Podczas zarządzania zdarzeniami w celu zrozumienia _potencjalnie_ powiązanych zmian. Wykonaj zapytanie o zdarzenia zmiany w określonym oknie czasu i Oceń szczegóły zmiany.
- Przechowywanie bazy danych zarządzania konfiguracją, znanej jako CMDB, na bieżąco. Zamiast odświeżania wszystkich zasobów i ich pełnych zestawów właściwości zgodnie z zaplanowaną częstotliwością, należy uzyskać tylko te zmiany.
- Informacje o innych właściwościach, które mogły zostać zmienione w przypadku zmiany stanu zgodności zasobu. Ocena tych dodatkowych właściwości może zapewnić wgląd w inne właściwości, które mogą być potrzebne do zarządzania za pośrednictwem definicji Azure Policy.

W tym artykule pokazano, jak zbierać te informacje za poorednictwem zestawu SDK grafu zasobów. Aby wyświetlić te informacje w Azure Portal, zobacz [historię zmian](../../policy/how-to/determine-non-compliance.md#change-history-preview) Azure Policy lub [historię zmian](../../../azure-monitor/platform/activity-log-view.md#azure-portal)dziennika aktywności platformy Azure.

> [!NOTE]
> Szczegóły zmiany w grafie zasobów są przeznaczone dla Menedżer zasobów właściwości. Aby śledzić zmiany wewnątrz maszyny wirtualnej, zobacz Azure Automation [śledzenie zmian](../../../automation/automation-change-tracking.md) lub [konfiguracja gościa Azure Policy dla maszyn wirtualnych](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Historia zmian w usłudze Azure Resource Graph jest w publicznej wersji zapoznawczej.

## <a name="find-when-changes-were-detected"></a>Znajdź, kiedy zostały wykryte zmiany

Pierwszym krokiem w przypadku wyświetlenia zmiany w zasobie jest znalezienie zdarzeń zmiany związanych z tym zasobem w oknie czasu. Ten krok jest realizowany za pomocą punktu końcowego REST **resourceChanges** .

Punkt końcowy **resourceChanges** wymaga dwóch parametrów w treści żądania:

- **ResourceID**: zasób platformy Azure, w którym mają zostać wyszukane zmiany.
- **Interwał**: Właściwość z datą _początkową_ i _końcową_ dla sytuacji, w której ma zostać wyszukane zdarzenie zmiany przy użyciu **strefy czasowej Zulu (Z)** .

Przykład treści żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

W przypadku powyższej treści żądania identyfikator URI interfejsu API REST dla **resourceChanges** jest:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpowiedź wygląda podobnie do tego przykładu:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Każde wykryte zdarzenie zmiany dla **ResourceID** ma element **changeId** , który jest unikatowy dla tego zasobu. Ciąg **changeId** może czasami zawierać inne właściwości, dlatego jest tylko unikatowy. Rekord zmiany obejmuje czasy wykonania migawek przed i po.
Zdarzenie zmiany wystąpiło w pewnym momencie w tym oknie.

## <a name="see-what-properties-changed"></a>Zobacz, jakie właściwości zostały zmienione

Za pomocą **changeId** z punktu końcowego **resourceChanges** , punkt końcowy REST **resourceChangeDetails** jest następnie używany do uzyskiwania określonych szczegółowych zdarzeń zmiany.

Punkt końcowy **resourceChangeDetails** wymaga dwóch parametrów w treści żądania:

- **ResourceID**: zasób platformy Azure, w którym mają zostać wyszukane zmiany.
- **changeId**: unikatowe zdarzenie zmiany dla elementu **ResourceID** zebrane z **resourceChanges**.

Przykład treści żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

W przypadku powyższej treści żądania identyfikator URI interfejsu API REST dla **resourceChangeDetails** jest:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Odpowiedź wygląda podobnie do tego przykładu:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** i **afterSnapshot** każdy podaje czas trwania migawki i właściwości w tym czasie. Zmiana miała miejsce w pewnym momencie między tymi migawkami. W powyższym przykładzie widać, że właściwość, która została zmieniona, została **supportsHttpsTrafficOnly**.

Aby porównać wyniki programowo, należy porównać część **zawartości** każdej migawki, aby określić różnicę. W przypadku porównania całej migawki **sygnatura czasowa** zawsze będzie widoczna jako różnica, mimo że jest to oczekiwane.

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).
- Dowiedz się, jak [eksplorować zasoby](../concepts/explore-resources.md).