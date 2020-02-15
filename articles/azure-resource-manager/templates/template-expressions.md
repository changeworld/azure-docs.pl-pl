---
title: Składnia i wyrażenia szablonu
description: Zawiera opis deklaratywnej składni JSON dla szablonów Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207404"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Składnia i wyrażenia w szablonach Azure Resource Manager

Podstawowa składnia szablonu to JSON. Można jednak użyć wyrażeń do rozszerania wartości JSON dostępnych w ramach szablonu.  Wyrażenia rozpoczynają się i kończą z nawiasami: odpowiednio `[` i `]`. Wartość wyrażenia jest szacowana podczas wdrażania szablonu. Wyrażenie może zwracać ciąg, liczbę całkowitą, wartość logiczną, tablicę lub obiekt.

Wyrażenie szablonu nie może zawierać więcej niż 24 576 znaków.

## <a name="use-functions"></a>Korzystanie z funkcji

Azure Resource Manager udostępnia [funkcje](template-functions.md) , których można użyć w szablonie. Poniższy przykład przedstawia wyrażenie, które używa funkcji w wartości domyślnej parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

W wyrażeniu składnia `resourceGroup()` wywołuje jedną z funkcji, które Menedżer zasobów zapewnia do użycia w ramach szablonu. W tym przypadku jest to funkcja obiektu [resources](template-functions-resource.md#resourcegroup) . Podobnie jak w języku JavaScript, wywołania funkcji są sformatowane jako `functionName(arg1,arg2,arg3)`. Składnia `.location` pobiera jedną właściwość z obiektu zwróconego przez tę funkcję.

W funkcjach szablonów i ich parametrach nie jest rozróżniana wielkość liter. Na przykład Menedżer zasobów rozpoznaje **zmienne ("var1")** i **zmienne ("var1")** jako takie same. W przypadku oceny, chyba że funkcja wyraźnie modyfikuje wielkość liter (na przykład toUpper lub toLower), funkcja zachowuje wielkość liter. Niektóre typy zasobów mogą mieć wymagania dotyczące wielkości liter, które są niezależne od sposobu oceniania funkcji.

Aby przekazać wartość ciągu jako parametr do funkcji, należy użyć apostrofów.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Większość funkcji działa tak samo, niezależnie od tego, czy zostały wdrożone w grupie zasobów, subskrypcji, grupie zarządzania czy dzierżawie. Następujące funkcje mają ograniczenia oparte na zakresie:

* Grupa [zasobów — może](template-functions-resource.md#resourcegroup) być używana tylko we wdrożeniach w ramach grupy.
* [ResourceID](template-functions-resource.md#resourceid) — można użyć w dowolnym zakresie, ale prawidłowe parametry zmieniają się w zależności od zakresu.
* [subskrypcja](template-functions-resource.md#subscription) — może być używana tylko we wdrożeniach w grupie zasobów lub subskrypcji.

## <a name="escape-characters"></a>Znaki ucieczki

Aby ciąg literału rozpoczynał się od lewego nawiasu `[` i kończyć się nawiasem klamrowym `]`, ale nie powinien być interpretowany jako wyrażenie, Dodaj dodatkowy nawias, aby uruchomić ciąg z `[[`. Na przykład zmienna:

```json
"demoVar1": "[[test value]"
```

Rozwiązanie jest rozpoznawane jako `[test value]`.

Jeśli jednak ciąg literału nie kończy się nawiasem, nie należy określać pierwszego nawiasu. Na przykład zmienna:

```json
"demoVar2": "[test] value"
```

Rozwiązanie jest rozpoznawane jako `[test] value`.

Aby wypróbować podwójne cudzysłowy w wyrażeniu, takie jak dodanie obiektu JSON w szablonie, użyj ukośnika odwrotnego.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="null-values"></a>Wartości null

Aby ustawić właściwość na null, można użyć **wartości null** lub **[JSON ("null")]** . [Funkcja JSON](template-functions-array.md#json) zwraca pusty obiekt, gdy podajesz `null` jako parametr. W obu przypadkach Menedżer zasobów szablony traktują go tak, jakby właściwość nie jest obecna.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pełną listą funkcji szablonu, zobacz [Azure Resource Manager Template Functions](template-functions.md).
* Aby uzyskać więcej informacji na temat plików szablonów, zobacz [Omówienie struktury i składni szablonów Azure Resource Manager](template-syntax.md).
