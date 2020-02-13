---
title: 'Wzorzec: właściwości pola w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład użycia właściwości pola w definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172863"
---
# <a name="azure-policy-pattern-field-properties"></a>Wzorzec Azure Policy: właściwości pola

Operator [Field](../concepts/definition-structure.md#fields) oblicza określoną właściwość lub [alias](../concepts/definition-structure.md#aliases) dla podanej wartości dla danego [warunku](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad umożliwia zdefiniowanie dozwolonych regionów spełniających wymagania geograficznej lokalizacji w organizacji. Dozwolone zasoby są zdefiniowane w parametrze **listOfAllowedLocations** (_Array_). Zasoby, które pasują do definicji, są [odrzucane](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

Operator **pola** jest używany trzy razy w obrębie [operatora logicznego](../concepts/definition-structure.md#logical-operators) **allOf**.

- Pierwsze użycie szacuje Właściwość `location` z warunkiem **notIn** do parametru **listOfAllowedLocations** . **notIn** działa, ponieważ oczekuje _tablicy_ , a parametr jest _tablicą_. Jeśli `location` utworzonego lub zaktualizowanego zasobu nie znajduje się na liście zatwierdzonych, ten element ma wartość true.
- Drugie użycie również oblicza Właściwość `location`, ale używa warunku **notEquals** , aby sprawdzić, czy zasób jest _globalny_. Jeśli `location` utworzonego lub zaktualizowanego zasobu nie jest _globalna_, ten element ma wartość true.
- Ostatnie użycie szacuje Właściwość `type` i używa warunku **notEquals** do sprawdzenia, czy typ zasobu nie jest _Microsoft. usługi azureactivedirectory/b2cDirectories_. Jeśli nie, ten element ma wartość true.

Jeśli wszystkie trzy instrukcje warunku w operatorze logicznym **allOf** oceńą wartość true, tworzenie lub aktualizowanie zasobów jest blokowane przez Azure Policy.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).