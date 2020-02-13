---
title: 'Wzorzec: operatory logiczne w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykłady użycia operatorów logicznych w definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172849"
---
# <a name="azure-policy-pattern-logical-operators"></a>Wzorzec Azure Policy: operatory logiczne

Definicja zasad może zawierać kilka instrukcji warunkowych. Może być konieczne, aby każda instrukcja była prawdziwa lub tylko niektóre z nich były prawdziwe. Aby zapewnić obsługę tych wymagań, język ma [Operatory logiczne](../concepts/definition-structure.md#logical-operators) dla **not**, **allOf**i **anyOf**. Są one opcjonalne i mogą być zagnieżdżane w celu tworzenia złożonych scenariuszy.

## <a name="sample-1-one-logical-operator"></a>Przykład 1: jeden operator logiczny

Ta definicja zasad szacuje konta CosmosDB, aby sprawdzić, czy skonfigurowano automatyczne przełączanie do trybu failover i wiele lokalizacji zapisu. Gdy nie są one, [wyzwalają](../concepts/effects.md#audit) wyzwalacze i tworzą wpis dziennika w przypadku utworzenia lub zaktualizowania niezgodnego zasobu.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**Klasa policyrule. if** blokuje użycie jednego **allOf** , aby upewnić się, że wszystkie trzy warunki są spełnione.
Tylko wtedy, gdy wszystkie te warunki mają wartość true, jest wyzwalany przez wyzwalacz efektu **inspekcji** .

## <a name="sample-2-multiple-logical-operators"></a>Przykład 2: wiele operatorów logicznych

Ta definicja zasad służy do obliczania zasobów dla wzorca nazewnictwa. Jeśli zasób nie jest zgodny, zostanie on [odrzucony](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Ten **Klasa policyrule. Jeśli** blok zawiera również pojedynczy **allOf**, ale każdy warunek jest opakowany operatorem **not** Logical. Warunek wewnątrz operatora **not** logicznego jest obliczany jako pierwszy, a następnie szacuje **, czy** cała klauzula ma wartość true lub false. Jeśli operator logiczny **nie** zwróci wartości true, wyzwalacze efektu zasad.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).