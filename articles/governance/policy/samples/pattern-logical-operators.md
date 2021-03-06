---
title: 'Wzorzec: Operatory logiczne w definicji zasad'
description: Ten wzorzec zasad platformy Azure zawiera przykłady sposobu używania operatorów logicznych w definicji zasad.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 691383b1f8ae34bbd51ce7f4f9310980e3c66537
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272512"
---
# <a name="azure-policy-pattern-logical-operators"></a>Wzorzec zasad platformy Azure: operatory logiczne

Definicja zasad może zawierać kilka instrukcji warunkowych. Może być konieczne każde stwierdzenie, aby być prawdziwe lub tylko trzeba niektóre z nich, aby być prawdziwe. Aby zaspokoić te potrzeby, język ma [operatory logiczne](../concepts/definition-structure.md#logical-operators) dla **nie**, **allOf**i **anyOf**. Są one opcjonalne i mogą być zagnieżdżone do tworzenia złożonych scenariuszy.

## <a name="sample-1-one-logical-operator"></a>Przykład 1: Jeden operator logiczny

Ta definicja zasad ocenia konta usługi CosmosDB, aby sprawdzić, czy skonfigurowano automatyczne tryb failover i wiele lokalizacji zapisu. Gdy nie są one wyzwalacze [inspekcji](../concepts/effects.md#audit) i tworzy wpis dziennika, gdy niezgodny zasób jest tworzony lub aktualizowany.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**PolicyRule.if** bloku używa jednego **allOf,** aby upewnić się, że wszystkie trzy warunki są spełnione.
Tylko wtedy, gdy wszystkie te warunki ocenić true wyzwala efekt **inspekcji.**

## <a name="sample-2-multiple-logical-operators"></a>Przykład 2: Wiele operatorów logicznych

Ta definicja zasad ocenia zasoby dla wzorca nazewnictwa. Jeśli zasób nie jest zgodny, zostanie [odrzucony.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Ten **policyRule.if** bloku zawiera również jeden **allOf**, ale każdy warunek jest zawinięty z **nie** logicznego operatora. Warunkowe wewnątrz **operatora nie** logiczne ocenia najpierw, a następnie ocenia **nie,** aby ustalić, czy cała klauzula jest true lub false. Jeśli oba **nie** logiczne operatory ocenić true, wyzwala efekt zasad.

## <a name="sample-3-combining-logical-operators"></a>Przykład 3: Łączenie operatorów logicznych

Ta definicja zasad ocenia konta Java Spring, aby sprawdzić, czy śledzenie nie jest włączone lub jeśli śledzenie nie jest w stanie pomyślnym.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Przykład 3: Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Ten **policyRule.if** bloku zawiera zarówno **allOf** i **anyOf** operatorów logicznych. Operator logiczny **anyOf** ocenia wartość true, o ile jeden uwzględniony warunek jest spełniony. Ponieważ _typ_ jest w rdzeniu **allOf**, zawsze musi ocenić true. Jeśli _typ_ i jeden z warunków w **anyOf** są prawdziwe, wyzwala efekt zasad.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).