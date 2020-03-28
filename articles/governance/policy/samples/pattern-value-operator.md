---
title: 'Wzorzec: Operator wartości w definicji zasad'
description: Ten wzorzec zasad platformy Azure zawiera przykład użycia operatora wartości w definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172786"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Wzorzec zasad platformy Azure: operator wartości

Operator [wartości](../concepts/definition-structure.md#value) ocenia [parametry,](../concepts/definition-structure.md#parameters) [obsługiwane funkcje szablonu](../concepts/definition-structure.md#policy-functions)lub literały do podanej wartości dla danego [warunku.](../concepts/definition-structure.md#conditions)

> [!WARNING]
> Jeśli wynikiem _funkcji szablonu_ jest błąd, ocena zasad kończy się niepowodzeniem. Nieudana ocena jest niejawną **odmową.** Aby uzyskać więcej informacji, zobacz [unikanie błędów szablonu](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad dodaje lub zastępuje znacznik określony w **parametrze tagName** _(string)_ na zasobach i dziedziczy wartość **tagName** z grupy zasobów, wcenie zasobu. Ta ocena ma miejsce, gdy zasób jest tworzony lub aktualizowany. Jako efekt [modyfikacji](../concepts/effects.md#modify) korygowanie może być uruchamiane na istniejących zasobach za pomocą [zadania korygowania](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

Operator **wartości** jest używany w **ramach bloku policyRule.if** we **właściwościach**. W tym przykładzie [operator logiczny](../concepts/definition-structure.md#logical-operators) **allOf** jest używany do stwierdzenia, że obie instrukcje warunkowe muszą być prawdziwe dla efektu, **zmodyfikować**, aby mieć miejsce.

**wartość** oblicza wynik elementu [resourcegroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) funkcji szablonu do warunku **notEquals** wartości pustej. Jeśli nazwa tagu podana w **tagName** w nadrzędnej grupie zasobów istnieje, warunek ma wartość true.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).