---
title: Szczegóły struktury przypisywania zasad
description: W tym artykule opisano definicję przypisania zasad używaną przez usługę Azure Policy do powiązania definicji zasad i parametrów z zasobami do oceny.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265300"
---
# <a name="azure-policy-assignment-structure"></a>Struktura przypisań usługi Azure Policy

Przypisania zasad są używane przez usługę Azure Policy do definiowania, które zasoby są przypisywane, które zasady lub inicjatywy. Przypisanie zasad można określić wartości parametrów dla tej grupy zasobów w czasie przydziału, dzięki czemu można ponownie użyć definicji zasad, które dotyczą tych samych właściwości zasobów z różnych potrzeb zgodności.

JSON służy do tworzenia przypisania zasad. Przypisanie zasad zawiera elementy dla:

- nazwa wyświetlana
- description
- metadane
- tryb egzekwowania
- definicja zasad
- parameters

Na przykład następujący JSON pokazuje przypisanie zasad w trybie _DoNotEnforce_ z parametrami dynamicznymi:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Wszystkie przykłady zasad platformy Azure znajdują się w [przykładach zasad platformy Azure.](../samples/index.md)

## <a name="display-name-and-description"></a>Wyświetlana nazwa i opis

**DisplayName** i **opis** służy do identyfikowania przypisania zasad i podaj kontekst do jego użycia z określonym zestawem zasobów. **displayName** ma maksymalną długość _128_ znaków i **opis** maksymalną długość _512_ znaków.

## <a name="enforcement-mode"></a>Tryb egzekwowania

Właściwość **enforcementMode** zapewnia klientom możliwość testowania wyników zasad dotyczących istniejących zasobów bez inicjowania efektu zasad lub wyzwalania wpisów w [dzienniku aktywności platformy Azure.](../../../azure-monitor/platform/platform-logs-overview.md) Ten scenariusz jest powszechnie określany jako "Co jeśli" i dostosowuje się do bezpiecznych praktyk wdrażania. **enforcementMode** różni się od [wyłączonego](./effects.md#disabled) efektu, ponieważ ten efekt zapobiega oceny zasobów w ogóle.

Ta właściwość ma następujące wartości:

|Tryb |Wartość JSON |Typ |Korygowanie ręcznie |Wpis dziennika aktywności |Opis |
|-|-|-|-|-|-|
|Enabled (Włączony) |Domyślne |ciąg |Tak |Tak |Efekt zasad jest wymuszany podczas tworzenia lub aktualizowania zasobów. |
|Disabled (Wyłączony) |DoNotEnforce (DoNotEnforce) |ciąg |Tak |Nie | Efekt zasad nie jest wymuszany podczas tworzenia lub aktualizowania zasobów. |

Jeśli **tryb enforcementMode** nie jest określony w definicji zasad lub inicjatywy, używana jest wartość _Domyślna._ [Zadania korygowania](../how-to/remediate-resources.md) można uruchomić dla [wdrażaniaIfNotExists](./effects.md#deployifnotexists) zasad, nawet wtedy, gdy **enforcementMode** jest ustawiona na _DoNotEnforce_.

## <a name="policy-definition-id"></a>Identyfikator definicji zasad

To pole musi być pełną nazwą ścieżki definicji zasad lub definicji inicjatywy.
`policyDefinitionId`jest ciągiem, a nie tablicą. Zaleca się, aby w przypadku często przypisywanych do siebie wielu zasad, zamiast tego użyć [inicjatywy.](./definition-structure.md#initiatives)

## <a name="parameters"></a>Parametry

Ten segment przypisania zasad zawiera wartości parametrów zdefiniowanych w [definicji zasad lub definicji inicjatywy](./definition-structure.md#parameters).
Ten projekt umożliwia ponowne użycie definicji zasad lub inicjatywy z różnymi zasobami, ale sprawdź różne wartości biznesowe lub wyniki.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

W tym przykładzie parametry zdefiniowane wcześniej `prefix` w `suffix`definicji zasad są i . To konkretne przypisanie zasad `prefix` ustawia `suffix` **deptA** i **-LC**. Ta sama definicja zasad jest wielokrotnego użytku z innym zestawem parametrów dla innego działu, zmniejszając powielanie i złożoność definicji zasad, zapewniając jednocześnie elastyczność.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).