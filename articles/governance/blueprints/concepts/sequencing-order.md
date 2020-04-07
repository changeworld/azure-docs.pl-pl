---
title: Opis kolejności sekwencji wdrażania
description: Dowiedz się więcej o domyślnej kolejności, w jakiej artefakty planu są wdrażane podczas przypisywania planu, oraz o tym, jak dostosować kolejność wdrażania.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 41b1b1ada5b7c6c919f227927001570332eeccbf
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677560"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Opis sekwencji wdrażania w planach platformy Azure

Plany platformy Azure używa **kolejności sekwencjonowania,** aby określić kolejność tworzenia zasobów podczas przetwarzania przypisania definicji planu. W tym artykule wyjaśniono następujące pojęcia:

- Domyślna kolejność sekwencjonowania używana
- Jak dostosować zamówienie
- Sposób przetwarzania niestandardowego zamówienia

Istnieją zmienne w przykładach JSON, które należy zastąpić własnymi wartościami:

- `{YourMG}` — zastąp nazwą swojej grupy zarządzania

## <a name="default-sequencing-order"></a>Domyślna kolejność sekwencjonowania

Jeśli definicja planu nie zawiera żadnej dyrektywy dla kolejności wdrażania artefaktów lub dyrektywa jest null, następnie wyzerowane następujące kolejność:

- Artefakty **przypisania roli** na poziomie subskrypcji posortowane według nazwy artefaktu
- Artefakty **przypisania zasad** na poziomie subskrypcji posortowane według nazwy artefaktu
- Artefakty **szablonu usługi Azure Resource Manager** na poziomie subskrypcji posortowane według nazwy artefaktu
- **Artefakty grupy zasobów** (w tym artefakty podrzędne) posortowane według nazwy symbolu zastępczego

W ramach każdego artefaktu **grupy zasobów** w ramach tej grupy zasobów używana jest następująca kolejność sekwencji dla artefaktów, które mają zostać utworzone w ramach tej grupy zasobów:

- Artefakty **przypisania roli** podrzędnej grupy zasobów posortowane według nazwy artefaktu
- Artefakty **przydziału zasad** podrzędnych grupy zasobów posortowane według nazwy artefaktu
- Podrzędne artefakty **szablonu usługi Azure Resource Manager** grupy zasobów posortowane według nazwy artefaktu

> [!NOTE]
> Użycie [artifacts()](../reference/blueprint-functions.md#artifacts) tworzy niejawną zależność od artefaktu, do którego się odnosi.

## <a name="customizing-the-sequencing-order"></a>Dostosowywanie kolejności sekwencjonowania

Podczas tworzenia dużych definicji planu, może być konieczne dla zasobów, które mają być tworzone w określonej kolejności. Najbardziej typowy wzorzec użycia tego scenariusza jest, gdy definicja planu zawiera kilka szablonów usługi Azure Resource Manager. Plany platformy Azure obsługuje ten wzorzec, umożliwiając kolejność sekwencjonowania, które mają być zdefiniowane.

Kolejność jest realizowana przez zdefiniowanie `dependsOn` właściwości w JSON. Definicja planu dla grup zasobów i obiektów artefaktów obsługuje tę właściwość. `dependsOn`jest tablicą ciągów nazw artefaktów, które należy utworzyć określonego artefaktu przed jego utworzeniem.

> [!NOTE]
> Podczas tworzenia obiektów planu każdy zasób artefaktu otrzymuje swoją nazwę od nazwy pliku, jeśli [używasz programu PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)lub punktu końcowego adresu URL, jeśli używasz [interfejsu API REST](/rest/api/blueprints/artifacts/createorupdate).
> _resourceGroup_ odwołania w artefaktach muszą być zgodne z tymi zdefiniowanymi w definicji planu.

### <a name="example---ordered-resource-group"></a>Przykład — uporządkowana grupa zasobów

W tym przykładzie definicji planu ma grupę zasobów, która zdefiniowała `dependsOn`niestandardową kolejność sekwencjonowania, deklarując wartość dla , wraz ze standardową grupą zasobów. W takim przypadku artefakt o nazwie **assignPolicyTags** zostanie przetworzony przed grupą zasobów **uporządkowanych rg.**
**standard-rg** będą przetwarzane zgodnie z domyślną kolejnością sekwencjonowania.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Przykład - artefakt z zamówieniem niestandardowym

W tym przykładzie jest artefakt zasad, który zależy od szablonu usługi Azure Resource Manager. Domyślnie zamawiania artefakt zasad zostanie utworzony przed szablonem usługi Azure Resource Manager. Ta kolejność umożliwia artefakt zasad czekać na szablon usługi Azure Resource Manager do utworzenia.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Przykład — artefakt szablonu subskrypcji w zależności od grupy zasobów

W tym przykładzie jest szablon Menedżera zasobów wdrożony na poziomie subskrypcji, aby zależeć od grupy zasobów. W kolejności domyślnej artefakty poziomu subskrypcji zostaną utworzone przed grupami zasobów i artefaktami podrzędnymi w tych grupach zasobów. Grupa zasobów jest zdefiniowana w definicji planu w ten sposób:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Artefakt szablonu poziomu subskrypcji w zależności od grupy zasobów **wait-for-me** jest zdefiniowany w ten sposób:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Przetwarzanie dostosowanej sekwencji

Podczas procesu tworzenia sortowania topologicznego jest używany do tworzenia wykresu zależności artefaktów planów. Sprawdzanie zapewnia, że każdy poziom zależności między grupami zasobów i artefaktów jest obsługiwany.

Jeśli zostanie zadeklarowana zależność artefaktu, która nie zmieniłaby domyślnej kolejności, nie zostanie wywleciona żadna zmiana. Przykładem jest grupa zasobów, która zależy od zasad na poziomie subskrypcji. Innym przykładem jest przypisanie zasad podrzędnych grupy zasobów "standard-rg", które zależy od przypisania roli podrzędnej grupy zasobów "standard-rg". W obu przypadkach `dependsOn` nie zmieniłby domyślnej kolejności sekwencjonowania i nie zostaną wprowadzone żadne zmiany.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [cyklu życia planu](lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).