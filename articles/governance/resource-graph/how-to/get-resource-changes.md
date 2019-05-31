---
title: Uzyskiwanie zmian zasobów
description: Dowiedz się, jak można znaleźć, kiedy zasób został zmieniony i uzyskać listę właściwości, które zmieniane.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: b6ef57a3f39c82be30d92aef72c1bbe03b653768
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236510"
---
# <a name="get-resource-changes"></a>Uzyskiwanie zmian zasobów

Zasoby Pobierz zmienić za pomocą kurs limitem dziennego użycia, ponownej konfiguracji i nawet ponownego wdrażania.
Zmiany mogą pochodzić z osoba lub zautomatyzowanego procesu. Większość zmian jest zgodne z projektem, ale czasami nie jest. Ostatnich 14 dni historii zmian wykres zasobów platformy Azure umożliwiają:

- Znajdowanie wykrytych zmian we właściwości usługi Azure Resource Manager
- Sprawdzanie, które właściwości uległy zmianie w wyniku zdarzenia zmiany

Wykrywanie zmian i szczegóły są cenne dla następujących przykładowe scenariusze:

- Podczas zarządzania zdarzeniami w celu zrozumienia _potencjalnie_ powiązanych zmian. Wyszukać zdarzenia zmiany w określonym przedziale czasu i oceń szczegóły zmian.
- Utrzymywanie bazą zarządzania konfiguracją, znane jako CMDB aktualne. Zamiast odświeżanie wszystkich zasobów i ich zestawy pełną właściwość z częstotliwością, zaplanowane, pobierać tylko co się zmieniło.
- Zrozumienie, jakie inne właściwości mogły zostać zmienione podczas zasobem zmieniono stan zgodności. Ocena te dodatkowe właściwości może zapewnić wgląd w innych właściwości, które może być konieczne, można zarządzać za pomocą usługi Azure Policy definition.

W tym artykule pokazano, jak do zebrania tych informacji za pośrednictwem zestawu SDK wykres zasobów. Aby wyświetlić te informacje w witrynie Azure portal, zobacz usługi Azure Policy [historię zmian](../../policy/how-to/determine-non-compliance.md#change-history-preview) lub dziennik aktywności platformy Azure [historię zmian](../../../azure-monitor/platform/activity-log-view.md#azure-portal).

> [!NOTE]
> Szczegóły zmiany w wykresie zasobów są przeznaczone dla właściwości Resource Manager. Śledzenie zmian na maszynie wirtualnej, dla usługi Azure Automation [śledzenie zmian](../../../automation/automation-change-tracking.md) lub usługi Azure Policy [Konfiguracja gościa dla maszyn wirtualnych](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Historia zmian w wykresie zasobów platformy Azure jest w publicznej wersji zapoznawczej.

## <a name="find-when-changes-were-detected"></a>Dowiedz się, gdy wykryto zmiany

Pierwszym krokiem podczas wyświetlania, co się zmieniło w zasobie jest można znaleźć zdarzenia zmiany powiązanych z tym zasobem w przedziale czasu. W tym kroku odbywa się za pośrednictwem **resourceChanges** punktu końcowego REST.

**ResourceChanges** punktu końcowego wymaga dwóch parametrów w treści żądania:

- **resourceId**: Zasób platformy Azure do wyszukania zmian.
- **Interwał**: Właściwość o _start_ i _zakończenia_ daty, kiedy mają być sprawdzane dla zdarzenia zmiany za pomocą **Zulu strefy czasowej (Z)** .

Przykład treść żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Za pomocą powyższych treści żądania, identyfikator URI interfejsu API REST dla **resourceChanges** jest:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpowiedź wygląda następująco:

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

Każdej wykrytej zdarzenia zmiany dla **resourceId** ma **changeId** unikatowy dla tego zasobu. Gdy **changeId** parametry mogą czasami zawierać inne właściwości, tylko ma musi być unikatowa. Rekord modyfikacji obejmuje czas, przed i po tym wykonano migawek.
Zdarzenia zmiany wystąpił w pewnym momencie w tym oknie czasu.

## <a name="see-what-properties-changed"></a>Zobacz, co zmieniło właściwości

Za pomocą **changeId** z **resourceChanges** punktu końcowego, **resourceChangeDetails** punktu końcowego REST jest następnie używany do pobierania szczegółowych zdarzenia zmiany.

**ResourceChangeDetails** punktu końcowego wymaga dwóch parametrów w treści żądania:

- **resourceId**: Zasób platformy Azure do wyszukania zmian.
- **changeId**: Zdarzenie zmiany unikatowy dla **resourceId** zebrane z **resourceChanges**.

Przykład treść żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Za pomocą powyższych treści żądania, identyfikator URI interfejsu API REST dla **resourceChangeDetails** jest:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Odpowiedź wygląda następująco:

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

**beforeSnapshot** i **afterSnapshot** Nadaj każdej czasu migawki i właściwości w tym czasie. Zmiana wystąpiło w pewnym momencie między te migawki. Patrząc w powyższym przykładzie, widać, że została zmieniona właściwość **supportsHttpsTrafficOnly**.

Aby programowo należy porównać wyniki, porównaj **zawartości** część każdej migawki w celu ustalenia różnic. Jeśli porównasz migawkę całego **sygnatura czasowa** zawsze jest wyświetlany jako różnica pomimo jest oczekiwany.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz język używany w [początkowego zapytania](../samples/starter.md).
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md).
- Dowiedz się, jak [zapoznaj się z zasobami](../concepts/explore-resources.md).