---
title: 'Wzorzec: skutki definicji zasad'
description: Ten wzorzec zasad platformy Azure zawiera przykład sposobu używania różnych efektów definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372639"
---
# <a name="azure-policy-pattern-effects"></a>Wzorzec zasad platformy Azure: efekty

Usługa Azure Policy ma wiele [efektów,](../concepts/effects.md) które określają, jak usługa reaguje na niezgodne zasoby. Niektóre efekty są proste i nie wymagają żadnych dodatkowych właściwości w definicji zasad, podczas gdy inne wymagają kilku właściwości.

## <a name="sample-1-simple-effect"></a>Przykład 1: Prosty efekt

Ta definicja zasad sprawdza, czy tag zdefiniowany w **parametrze tagName** istnieje w ocenianym zasobie. Jeśli tag jeszcze nie istnieje, efekt [modyfikacji](../concepts/effects.md#modify) jest wyzwalany w celu dodania znacznika o wartości w **parametrze tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Efekt **modyfikowania** wymaga bloku **policyRule.then.details,** który definiuje **roleDefinitionIds** i **operacje**. Te parametry informują usługi Azure Policy, jakie role są potrzebne do dodania tagu i korygowania zasobu oraz które **modyfikują** operację do wykonania. W tym przykładzie **operacja** jest _dodawany_ i parametry są używane do ustawiania tagu i jego wartości.

## <a name="sample-2-complex-effect"></a>Przykład 2: Efekt złożony

Ta definicja zasad przeprowadza inspekcje każdej maszyny wirtualnej, gdy rozszerzenie, zdefiniowane w **parametrach wydawcy** i **typu,** nie istnieje. Używa [auditIfNotExists](../concepts/effects.md#auditifnotexists) do sprawdzenia zasobu związanego z maszyną wirtualną, aby sprawdzić, czy istnieje wystąpienie, które pasuje do zdefiniowanych parametrów. W tym przykładzie sprawdza typ **rozszerzeń.**

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Efekt **auditIfNotExists** wymaga **policyRule.then.details** bloku do definiowania zarówno **typu** i **existenceCondition** szukać. **ExistenceCondition** używa elementów języka zasad, takich jak [operatory logiczne,](../concepts/definition-structure.md#logical-operators)aby ustalić, czy istnieje pasujący powiązany zasób. W tym przykładzie wartości zaznaczone względem każdego [aliasu](../concepts/definition-structure.md#aliases) są zdefiniowane w parametrach.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).