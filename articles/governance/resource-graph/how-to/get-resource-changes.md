---
title: Uzyskiwanie zmian zasobów
description: Dowiedz się, jak znaleźć, gdy zasób został zmieniony, uzyskać listę właściwości, które uległy zmianie i ocenić różnice.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873033"
---
# <a name="get-resource-changes"></a>Uzyskiwanie zmian zasobów

Zasoby są zmieniane w trakcie codziennego użytkowania, ponownej konfiguracji, a nawet ponownego rozmieszczania.
Zmiana może pochodzić od osoby lub przez zautomatyzowany proces. Większość zmian jest z założenia, ale czasami tak nie jest. W ciągu ostatnich 14 dni historii zmian usługa Azure Resource Graph umożliwia:

- Znajdowanie, kiedy zmiany zostały wykryte we właściwości usługi Azure Resource Manager
- Dla każdej zmiany zasobu zobacz szczegóły zmiany właściwości
- Zobacz pełne porównanie zasobu przed i po wykrytej zmianie

Wykrywanie zmian i szczegóły są przydatne dla następujących przykładowych scenariuszy:

- Podczas zarządzania zdarzeniami, aby zrozumieć _potencjalnie_ związane zmiany. Zapytanie o zdarzenia zmian w określonym oknie czasu i oceń szczegóły zmiany.
- Aktualizowanie bazy danych zarządzania konfiguracją, znanej jako CMDB. Zamiast odświeżania wszystkich zasobów i ich pełne zestawy właściwości na zaplanowanej częstotliwości, tylko uzyskać to, co się zmieniło.
- Zrozumienie, jakie inne właściwości mogły zostać zmienione, gdy stan zgodności zasobu został zmieniony. Ocena tych dodatkowych właściwości może zapewnić wgląd w inne właściwości, które mogą wymagać zarządzania za pomocą definicji zasad platformy Azure.

W tym artykule pokazano, jak zebrać te informacje za pośrednictwem sdk wykresu zasobów. Aby wyświetlić te informacje w witrynie Azure portal, zobacz [Historia zmian](../../policy/how-to/determine-non-compliance.md#change-history-preview) zasad platformy Azure lub [Historia zmian](../../../azure-monitor/platform/activity-log-view.md#azure-portal)w dzienniku aktywności platformy Azure.
Aby uzyskać szczegółowe informacje na temat zmian w aplikacjach z warstwy infrastruktury aż do wdrożenia aplikacji, zobacz [Korzystanie z analizy zmian aplikacji (w wersji zapoznawczej)](../../../azure-monitor/app/change-analysis.md) w usłudze Azure Monitor.

> [!NOTE]
> Szczegóły zmian w wykresie zasobów są dla właściwości Menedżera zasobów. Aby śledzić zmiany wewnątrz maszyny wirtualnej, zobacz [Śledzenie zmian](../../../automation/automation-change-tracking.md) usługi Azure Automation lub Konfiguracja gościa zasad platformy Azure [dla maszyn wirtualnych.](../../policy/concepts/guest-configuration.md)

> [!IMPORTANT]
> Historia zmian w grafie zasobów platformy Azure jest w publicznej wersji zapoznawczej.

## <a name="find-detected-change-events-and-view-change-details"></a>Znajdowanie wykrytych zdarzeń zmiany i wyświetlanie szczegółów zmiany

Pierwszym krokiem w zobaczeniu, co zmieniło się w zasobie jest znalezienie zdarzeń zmiany związanych z tym zasobem w przedziale czasu. Każde zdarzenie zmiany zawiera również szczegółowe informacje o tym, co zmieniło się w zasobie. Ten krok jest wykonywany za pośrednictwem **resourceChanges** REST punktu końcowego.

Punkt końcowy **resourceChanges** akceptuje następujące parametry w treści żądania:

- **resourceId** \[wymagane:\]zasób platformy Azure, aby wyszukać zmiany na.
- wymagany\] **interwał:** \[Właściwość z datami _rozpoczęcia_ i _zakończenia,_ kiedy sprawdzić zdarzenie zmiany przy użyciu **strefy czasowej Zulu (Z)**.
- **fetchPropertyChanges** (opcjonalnie): Właściwość logiczna, która ustawia się, jeśli obiekt odpowiedzi zawiera zmiany właściwości.

Przykładowa treść żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

W powyższej treści żądania identyfikator URI interfejsu API REST dla **resourceChanges** jest:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpowiedź wygląda podobnie do tego przykładu:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Każde wykryte zdarzenie zmiany dla **identyfikatora resourceId** ma następujące właściwości:

- **changeId** — ta wartość jest unikatowa dla tego zasobu. Chociaż **ciąg changeId** może czasami zawierać inne właściwości, jest tylko gwarantowane, aby być unikatowe.
- **beforeSnapshot** — zawiera **identyfikator migawki** i **sygnaturę czasową** migawki zasobu, która została podjęta przed wykryciem zmiany.
- **afterSnapshot** — zawiera **identyfikator migawki** i **sygnaturę czasową** migawki zasobu, która została podjęta po wykryciu zmiany.
- **changeType** - Opisuje typ zmiany wykrytej dla całego rekordu zmiany między **beforeSnapshot** i **afterSnapshot**. Wartości to: _Tworzenie,_ _Aktualizacja_i _Usuwanie_. The **propertyChanges** property array is only included when **changeType** is _Update_.
- **propertyChanges** - Ta tablica właściwości szczegółowo wszystkie właściwości zasobu, które zostały zaktualizowane między **beforeSnapshot** i **afterSnapshot:**
  - **propertyName** - Nazwa właściwości zasobu, która została zmieniona.
  - **changeCategory** - Opisuje, co się zmieniło. Wartości to: _System_ i _Użytkownik_.
  - **changeType** - Opisuje typ zmiany wykrytej dla właściwości poszczególnych zasobów.
    Wartości to: _Wstaw,_ _Aktualizacja,_ _Usuń_.
  - **beforeValue** - Wartość właściwości zasobu w **beforeSnapshot**. Nie jest wyświetlany, gdy **changeType** jest _Wstaw ._
  - **afterValue** - Wartość właściwości zasobu w **afterSnapshot**. Nie jest wyświetlany, gdy **changeType** jest _Usuń_.

## <a name="compare-resource-changes"></a>Porównywanie zmian zasobów

Z **changeId** z **resourceChanges** punktu końcowego, **resourceChangeDetails** REST punkt końcowy jest następnie używany do uzyskania przed i po migawki zasobu, który został zmieniony.

**ResourceChangeDetails** punkt końcowy wymaga dwóch parametrów w treści żądania:

- **resourceId:** Zasób platformy Azure do porównywania zmian.
- **changeId**: Zdarzenie zmiany unikatowej dla **zasobId** zebrane z **resourceChanges**.

Przykładowa treść żądania:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

W powyższej treści żądania identyfikator URI interfejsu API REST dla **resourceChangeDetails** jest:

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

**beforeSnapshot** i **afterSnapshot** każdy podać czas migawki została podjęta i właściwości w tym czasie. Zmiana nastąpiła w pewnym momencie między tymi migawkami. Patrząc na powyższy przykład, widzimy, że właściwość, która uległa zmianie, **obsługujehttpsTrafficOnly**.

Aby porównać wyniki, użyj właściwości **zmiany** w **resourceChanges** lub oceny części **zawartości** każdej migawki w **resourceChangeDetails,** aby określić różnicę. Jeśli porównać migawki, **sygnatura czasowa** zawsze pokazuje jako różnicę, mimo oczekiwanych.

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [kwerendach startowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [kwerendach zaawansowanych](../samples/advanced.md).
- Dowiedz się więcej o [eksplorowanie zasobów](../concepts/explore-resources.md).