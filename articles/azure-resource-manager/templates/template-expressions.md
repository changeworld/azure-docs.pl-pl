---
title: Składnia i wyrażenia szablonu
description: Zawiera opis deklaratywnej składni JSON dla szablonów Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: b7682ba10c30290e5935bc2dd17e2a83852d92f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484196"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Składnia i wyrażenia w szablonach Azure Resource Manager

Podstawowa składnia szablonu to JSON. Można jednak użyć wyrażeń do rozszerania wartości JSON dostępnych w ramach szablonu.  Wyrażenia zaczynają się i kończą nawiasami kwadratowymi: odpowiednio `[` i `]`. Wartość wyrażenia jest obliczana podczas wdrażania szablonu. Wyrażenie może zwrócić ciąg, liczbę całkowitą, wartość logiczną, tablicę lub obiekt.

Wyrażenie szablonu nie może zawierać więcej niż 24 576 znaków.

## <a name="use-functions"></a>Korzystanie z funkcji

Poniższy przykład przedstawia wyrażenie w wartości domyślnej parametru:

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

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pełną listą funkcji szablonu, zobacz [Azure Resource Manager Template Functions](template-functions.md).
* Aby uzyskać więcej informacji na temat plików szablonów, zobacz [Omówienie struktury i składni szablonów Azure Resource Manager](template-syntax.md).
