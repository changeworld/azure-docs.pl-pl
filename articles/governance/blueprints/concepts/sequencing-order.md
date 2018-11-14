---
title: Zrozumienie sekwencję wdrażania w plany usługi Azure
description: Informacje o cyklu życia, które przechodzi planu i szczegółowe informacje o każdym etapie.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: fcfffe6094361c8b47b1cc7ce42cb79561261b15
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51620599"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Zrozumienie sekwencję wdrażania w plany usługi Azure

Usługa Azure korzysta plany **sekwencjonowania** Aby określić kolejność tworzenia zasobów, podczas przetwarzania przypisanie planu. W tym artykule opisano następujące pojęcia:

- Domyślna kolejność sekwencjonowania, który jest używany
- Dostosowywanie kolejności
- Jak przebiega przetwarzanie zamówienia dostosowanych

W przykładach JSON, musisz zastąpić własnymi wartościami, które są zmiennymi:

- `{YourMG}` — zastąp nazwą swojej grupy zarządzania

## <a name="default-sequencing-order"></a>Domyślna kolejność sekwencji

Jeśli planu nie zawiera żadnych dyrektywy za zlecenie Wdrażaj artefakty, lub dyrektywa ma wartość null, jest używany następującej kolejności:

- Poziom subskrypcji **przypisania roli** artefaktów sortowane według nazwy artefaktu
- Poziom subskrypcji **przypisania zasad** artefaktów sortowane według nazwy artefaktu
- Poziom subskrypcji **szablonu usługi Azure Resource Manager** artefaktów sortowane według nazwy artefaktu
- **Grupa zasobów** artefakty (w tym artefaktów podrzędne), posortowane według nazwy symbolu zastępczego

W ramach każdej **grupy zasobów** artefaktu, w następującej kolejności sekwencji jest używany dla artefaktów ma zostać utworzony w tej grupie zasobów:

- Podrzędne grupy zasobów **przypisania roli** artefaktów sortowane według nazwy artefaktu
- Podrzędne grupy zasobów **przypisania zasad** artefaktów sortowane według nazwy artefaktu
- Podrzędne grupy zasobów **szablonu usługi Azure Resource Manager** artefaktów sortowane według nazwy artefaktu

## <a name="customizing-the-sequencing-order"></a>Dostosowywanie kolejności sekwencji

Podczas redagowania dużych schematy, może być konieczne dla zasobów w określonej kolejności. Najczęstszym wzorcem użycia tego scenariusza jest, gdy plan zawiera kilka szablonów usługi Azure Resource Manager. Plany obsługi tego wzorca, umożliwiając kolejności sekwencjonowania należy zdefiniować.

Kolejność odbywa się przez definiowanie `dependsOn` właściwości w formacie JSON. Tylko plan (dla grup zasobów) i obiekty artefaktu obsługują tej właściwości. `dependsOn` jest tablicą ciągów nazw artefaktów, które określonego artefaktu musi zostać utworzona przed jego tworzenia.

> [!NOTE]
> **Grupa zasobów** obsługuje artefaktów `dependsOn` właściwości, ale nie może być celem `dependsOn` przez dowolny typ artefaktu.

### <a name="example---blueprint-with-ordered-resource-group"></a>Przykład — planu z grupą zasobów uporządkowany

Ten przykładowy plan zawiera grupę zasobów, która została zdefiniowana kolejność niestandardowych sekwencjonowania przez zadeklarowanie wartość `dependsOn`, wraz z grupą zasobów standardowych. W tym przypadku o nazwie artefaktu **assignPolicyTags** zostanie przetworzone przed **uporządkowane rg** grupy zasobów. **Standardowa rg** będą przetwarzane na domyślną kolejność sekwencji.

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
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Przykład — artefakt o kolejności niestandardowej

W tym przykładzie jest artefaktu zasad, który zależy od szablonu usługi Azure Resource Manager. Przy domyślnej kolejności, artefaktu zasad zostałyby utworzone przed szablonu usługi Azure Resource Manager. Ta kolejność umożliwia artefaktu zasad oczekiwania dla szablonu usługi Azure Resource Manager, który ma zostać utworzony.

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
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

## <a name="processing-the-customized-sequence"></a>Przetwarzanie niestandardowych sekwencji

W trakcie procesu tworzenia topologii sortowania służy do tworzenia wykresu zależności artefaktów schematy. Sprawdzanie zapewnia, że każdy poziom zależności między grupami zasobów i artefaktów jest obsługiwana.

Jeśli zależność artefaktu zadeklarowano, że w takich sytuacjach przydałaby, zmienić domyślną kolejność, są wprowadzane żadne zmiany. Przykładem jest grupa zasobów, która jest zależna od zasadami poziomu subskrypcji. Innym przykładem jest zasób grupy "standard-rg" podrzędnych przypisanie zasad zależną od przypisania roli podrzędnej grupie zasobów "standard-rg". W obu przypadkach `dependsOn` w takich sytuacjach przydałaby zmienić domyślną kolejność sekwencjonowania i żadne zmiany nie będą wprowadzone.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat [cyklu życia strategii](lifecycle.md)
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md)
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md)