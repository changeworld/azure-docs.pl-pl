---
title: Zrozumienie sekwencję wdrażania w plany usługi Azure
description: Informacje o cyklu życia, które przechodzi planu i szczegółowe informacje o każdym etapie.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955456"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Zrozumienie sekwencję wdrażania w plany usługi Azure

Usługa Azure korzysta plany **sekwencjonowania** Aby określić kolejność tworzenia zasobów, podczas przetwarzania przypisanie planu. W tym artykule opisano domyślną kolejność sekwencjonowania, który jest używany, jak dostosować kolejność i przetwarzaniu dostosowane kolejności.

W przykładach JSON, musisz zastąpić własnymi wartościami, które są zmiennymi:

- `{YourMG}` -Zamień na nazwę grupy zarządzania

## <a name="default-sequencing-order"></a>Domyślna kolejność sekwencji

Jeśli planu nie zawiera żadnych dyrektywy za zlecenie Wdrażaj artefakty, lub dyrektywa ma wartość null, jest używany następującej kolejności:

- Poziom subskrypcji **przypisania roli** artefaktów sortowane według nazwy artefaktu
- Poziom subskrypcji **przypisania zasad** artefaktów sortowane według nazwy artefaktu
- Poziom subskrypcji **szablonu usługi Azure Resource Manager** artefaktów sortowane według nazwy artefaktu
- **Grupa zasobów** artefakty (w tym artefaktów podrzędne), posortowane według nazwy symbolu zastępczego

W ramach każdej **grupy zasobów** artefakt, który jest przetwarzany, następującej kolejności sekwencji jest używany dla artefaktów ma zostać utworzony w tej grupie zasobów:

- Podrzędne grupy zasobów **przypisania roli** artefaktów sortowane według nazwy artefaktu
- Podrzędne grupy zasobów **przypisania zasad** artefaktów sortowane według nazwy artefaktu
- Podrzędne grupy zasobów **szablonu usługi Azure Resource Manager** artefaktów sortowane według nazwy artefaktu

## <a name="customizing-the-sequencing-order"></a>Dostosowywanie kolejności sekwencji

Podczas redagowania dużych schematy, może być konieczne dla zasobu do utworzenia w określonej kolejności w stosunku do innego zasobu. Najczęstszym wzorcem użycia tego jest, gdy plan zawiera wiele szablonów usługi Azure Resource Manager. Schematy wykonuje tę wartość, umożliwiając kolejności sekwencjonowania należy zdefiniować.

Jest to realizowane przez zdefiniowanie `dependsOn` właściwości w formacie JSON. Tylko plan (dla grup zasobów) i obiekty artefaktu obsługują tej właściwości. `dependsOn` jest tablicą ciągów nazw artefaktów, które określonego artefaktu musi zostać utworzona przed jego tworzenia.

### <a name="example---blueprint-with-ordered-resource-group"></a>Przykład — planu z grupą zasobów uporządkowany

To jest plan przykład z grupą zasobów, która została zdefiniowana kolejność niestandardowych sekwencjonowania przez zadeklarowanie wartość `dependsOn`, wraz z grupą zasobów standardowych. W tym przypadku o nazwie artefaktu **assignPolicyTags** zostanie przetworzone przed **uporządkowane rg** grupy zasobów.
**Standardowa rg** będą przetwarzane na domyślną kolejność sekwencji.

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

Jest to przykład artefakt zasad, który jest zależny od szablonu usługi Azure Resource Manager. Przy domyślnej kolejności, artefaktu zasad zostałyby utworzone przed szablonu usługi Azure Resource Manager. Dzięki temu artefaktu zasad oczekiwania dla szablonu usługi Azure Resource Manager, który ma zostać utworzony.

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

W trakcie procesu tworzenia topologii sortowania służy do tworzenia wykresu zależności z planu i jego artefaktów. Dzięki temu wiele poziomów zależności między grupami zasobów i artefakty mogą być obsługiwane.

Jeśli zależność jest zadeklarowany na planu lub artefakt, który nie ma zmienić domyślną kolejność, w kolejności sekwencji są wprowadzane żadne zmiany. Przykładem tego są grupę zasobów, która jest zależna od przypisania zasad podrzędnych poziomu zasad lub zasób grupy "standard-rg" subskrypcji, która jest zależna od przypisania roli podrzędnej grupie zasobów "standard-rg". W obu przypadkach `dependsOn` w takich sytuacjach przydałaby zmienić domyślną kolejność sekwencjonowania i żadne zmiany nie będą wprowadzone.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [planu cyklu życia](lifecycle.md)
- Opis sposobu użycia [statycznych i dynamicznych parametrów](parameters.md)
- Dowiedz się, jak używać [planu blokowanie zasobów](resource-locking.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania planu z [Ogólne rozwiązywanie problemów](../troubleshoot/general.md)