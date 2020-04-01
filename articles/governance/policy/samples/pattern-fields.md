---
title: 'Wzorzec: Właściwości pola w definicji zasad'
description: Ten wzorzec zasad platformy Azure zawiera przykład użycia właściwości pola w definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172863"
---
# <a name="azure-policy-pattern-field-properties"></a>Wzorzec zasad platformy Azure: właściwości pola

Operator [pola](../concepts/definition-structure.md#fields) ocenia określoną właściwość lub [alias](../concepts/definition-structure.md#aliases) do podanej wartości dla danego [warunku](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad umożliwia definiowanie dozwolonych regionów, które spełniają wymagania organizacji dotyczące lokalizacji geograficznej. Dozwolone zasoby są definiowane w **liście parametrówOfAllowedLocations** (_tablica_). Zasoby, które pasują do definicji są [odrzucane](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Wyjaśnienie

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

Operator **pola** jest używany trzy razy w [ramach operatora logicznego](../concepts/definition-structure.md#logical-operators) **allOf**.

- Pierwsze użycie ocenia `location` właściwość z **notIn** warunek do **listOfAllowedLocations** parametru. **notIn** działa zgodnie z oczekiwaniami _tablicy_ i parametr jest _tablicą_. Jeśli `location` utworzonego lub zaktualizowanego zasobu nie ma na liście zatwierdzonych, ten element ocenia true.
- Drugie użycie również ocenia `location` właściwość, ale używa **warunku notEquals,** aby sprawdzić, czy zasób jest _globalny._ `location` Jeśli utworzony lub zaktualizowany zasób nie jest _globalny,_ ten element ocenia wartość true.
- Ostatnie użycie ocenia `type` właściwość i używa warunku **notEquals** do sprawdzania poprawności typu zasobu nie jest _Microsoft.AzureActiveDirectory/b2cDirectory_. Jeśli tak nie jest, ten element ocenia true.

Jeśli wszystkie trzy instrukcje warunek w **allOf** operator logiczny ocenić true, tworzenie lub aktualizacja zasobów jest blokowany przez zasady platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).