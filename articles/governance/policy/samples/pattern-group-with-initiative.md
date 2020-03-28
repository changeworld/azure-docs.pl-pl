---
title: 'Wzorzec: definicje zasad grupy z inicjatywami'
description: Ten wzorzec zasad platformy Azure zawiera przykład sposobu grupowania definicji zasad w inicjatywę
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172856"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Wzorzec zasad platformy Azure: definicje zasad grupy

Inicjatywa jest grupą definicji zasad. Grupując powiązane definicje zasad w jeden obiekt, można utworzyć pojedyncze przypisanie, które byłoby wielokrotnym przydziałem.

## <a name="sample-initiative-definition"></a>Przykładowa definicja inicjatywy

Ta inicjatywa wdraża dwie definicje zasad, z których każda przyjmuje parametry **tagName** i **tagValue.** Sama inicjatywa ma dwa parametry: **costCenterValue** i **productNameValue**.
Te parametry inicjatywy są dostarczane do każdej z grupowanych definicji zasad. Ten projekt maksymalizuje ponowne użycie istniejących definicji zasad, ograniczając jednocześnie liczbę przydziałów utworzonych w celu ich zaimplementowania w razie potrzeby.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Wyjaśnienie

#### <a name="initiative-parameters"></a>Parametry inicjatywy

Inicjatywa może zdefiniować własne parametry, które są następnie przekazywane do definicji zasad zgrupowanych.
W tym przykładzie zarówno **costCenterValue,** jak i **productNameValue** są definiowane jako parametry inicjatywy. Wartości są podane podczas przypisywania inicjatywy.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Zawiera definicje zasad

Każda z nich musi zawierać **zasadDefinitionId** i tablicy **parametrów,** jeśli definicja zasad akceptuje parametry. W poniższym urywek definicja uwzględnionych zasad przyjmuje dwa parametry: **tagName** i **tagValue**. **tagName** jest zdefiniowany za pomocą literału, ale **tagValue** używa parametru **costCenterValue** zdefiniowanego przez inicjatywę. Ten przekaz wartości poprawia ponowne użycie.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).