---
title: 'Wzorzec: Parametry w definicji zasad'
description: Ten wzorzec zasad platformy Azure zawiera przykład użycia parametrów w definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172807"
---
# <a name="azure-policy-pattern-parameters"></a>Wzorzec zasad platformy Azure: parametry

Definicja zasad może być dynamiczna w celu zmniejszenia liczby definicji zasad, które są potrzebne przy użyciu [parametrów](../concepts/definition-structure.md#parameters). Parametr jest definiowany podczas przypisywania zasad. Parametry mają zestaw wstępnie zdefiniowanych właściwości, które opisują parametr i sposób jego użycia.

## <a name="sample-1-string-parameters"></a>Przykład 1: Parametry ciągu

Ta definicja zasad używa dwóch parametrów: **tagName** i **tagValue,** aby ustawić, czego szuka przypisanie zasad w zasobach. Ten format umożliwia zasady do użycia dla dowolnej liczby kombinacji nazw tagów i wartości znacznika, ale tylko zachować jedną definicję zasad.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Przykład 1: Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

W tej części definicji zasad parametr **tagName** jest zdefiniowany jako _ciąg_ znaków, a opis jest podany do jego użycia.

Parametr jest następnie używany w **policyRule.if** bloku, aby zasady dynamiczne. W tym miejscu służy do definiowania pola, które jest oceniane, który jest tagiem o wartości **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Przykład 2: Parametry tablicy

Ta definicja zasad używa jednego **parametru, listOfBandwidthinMbps,** aby sprawdzić, czy zasób obwodu trasy ekspresowej skonfigurował ustawienie przepustowości na jedną z zatwierdzonych wartości. Jeśli nie jest zgodny, tworzenie lub aktualizowanie zasobu zostanie [odrzucone](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Przykład 2: Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

W tej części definicji zasad parametr **listOfBandwidthinMbps** jest zdefiniowany jako _tablica_ i opis jest podany do jego użycia. Jako _tablica_ma wiele wartości do dopasowania.

Parametr jest następnie używany w **policyRule.if** bloku. Jako parametr _tablicy_ musi być używany[warunek](../concepts/definition-structure.md#conditions) _tablicy_
w **lub** **nieIn.**
W tym miejscu jest używany względem **aliasu serviceProvider.bandwidthInMbps** jako jednej ze zdefiniowanych wartości.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).