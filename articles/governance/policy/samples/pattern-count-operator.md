---
title: 'Wzorzec: Operator licznika w definicji zasad'
description: Ten wzorzec zasad platformy Azure zawiera przykład sposobu używania operatora zliczania w definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172947"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Wzorzec zasad platformy Azure: operator zliczania

Operator [count](../concepts/definition-structure.md#count) ocenia członków \[ \* \] aliasu.

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad [przeprowadza inspekcję sieciowych](../concepts/effects.md#audit) grup zabezpieczeń skonfigurowanych w taki sposób, aby zezwalać na ruch przychodzący protokołu RDP (Remote Desktop Protocol).

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Wyjaśnienie

Podstawowymi składnikami operatora **zliczania** są _pole_, _gdzie_i warunek. Każdy z nich jest wyróżniony we wtorkowym urywku.

- _pole_ informuje, który [alias](../concepts/definition-structure.md#aliases) do oceny członków. W tym miejscu przyjrzymy się _tablicy_ aliasów **\[ \* securityRules** sieciowej grupy zabezpieczeń.
- _gdzie_ używa języka zasad do definiowania, które elementy członkowskie _tablicy_ spełniają kryteria. W tym przykładzie **allOf** operator logiczny grupuje trzy różne oceny stanu właściwości _tablicy_ aliasu: _kierunek_, _dostęp_i _destinationPortRange_.
- Warunek liczby w tym przykładzie jest **większy**. Count ocenia jako true, gdy jeden lub więcej członków _tablicy_ aliasu pasuje _do klauzuli where._

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).