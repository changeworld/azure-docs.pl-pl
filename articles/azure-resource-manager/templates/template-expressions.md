---
title: Składnia i wyrażenia szablonu
description: Zawiera opis deklaratywnej składni JSON dla szablonów usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460113"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Składnia i wyrażenia w szablonach usługi Azure Resource Manager

Podstawową składnią szablonu jest JSON. Można jednak użyć wyrażeń, aby rozszerzyć wartości JSON dostępne w szablonie.  Wyrażenia zaczynają się i kończą nawiasami kwadratowymi: odpowiednio `[` i `]`. Wartość wyrażenia jest obliczana podczas wdrażania szablonu. Wyrażenie może zwrócić ciąg, liczbę całkowitą, wartość logiczną, tablicę lub obiekt.

Wyrażenie szablonu nie może przekraczać 24 576 znaków.

## <a name="use-functions"></a>Korzystanie z funkcji

Usługa Azure Resource Manager udostępnia [funkcje,](template-functions.md) których można używać w szablonie. Poniższy przykład przedstawia wyrażenie, które używa funkcji w wartości domyślnej parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

W ramach wyrażenia składnia `resourceGroup()` wywołuje jedną z funkcji, które Menedżer zasobów zapewnia do użycia w szablonie. W takim przypadku jest to [resourceGroup](template-functions-resource.md#resourcegroup) funkcja. Podobnie jak w języku JavaScript, `functionName(arg1,arg2,arg3)`wywołania funkcji są sformatowane jako . Składnia `.location` pobiera jedną właściwość z obiektu zwróconego przez tę funkcję.

Funkcje szablonu i ich parametry są niewrażliwe na wielkości liter. Na przykład Menedżer zasobów rozpoznaje **zmienne("var1")** i **ZMIENNE("VAR1")** jako takie same. Po ocenie, chyba że funkcja wyraźnie modyfikuje przypadek (na przykład toUpper lub toLower), funkcja zachowuje przypadek. Niektóre typy zasobów mogą mieć wymagania dotyczące spraw, które są niezależne od sposobu oceny funkcji.

Aby przekazać wartość ciągu jako parametr do funkcji, należy użyć pojedynczych cudzysłowów.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Większość funkcji działa tak samo, niezależnie od tego, czy są wdrażane w grupie zasobów, subskrypcji, grupie zarządzania lub dzierżawie. Następujące funkcje mają ograniczenia oparte na zakresie:

* [resourceGroup](template-functions-resource.md#resourcegroup) - może być używany tylko we wdrożeniach do grupy zasobów.
* [resourceId](template-functions-resource.md#resourceid) - może być używany w dowolnym zakresie, ale prawidłowe parametry zmieniają się w zależności od zakresu.
* [subskrypcja](template-functions-resource.md#subscription) — może być używana tylko we wdrożeniach do grupy zasobów lub subskrypcji.

## <a name="escape-characters"></a>Znaki ucieczki

Aby ciąg dosłowny rozpoczynał `[` się od lewego nawiasu i kończył prawym nawiasem , `]`ale nie `[[`interpretowano go jako wyrażenie, dodaj dodatkowy nawias, aby rozpocząć ciąg od . Na przykład zmienna:

```json
"demoVar1": "[[test value]"
```

Rozpoznaje `[test value]`.

Jeśli jednak ciąg literał nie kończy się nawiasem, nie uniknij pierwszego nawiasu. Na przykład zmienna:

```json
"demoVar2": "[test] value"
```

Rozpoznaje `[test] value`.

Aby uniknąć podwójnych cudzysłowów w wyrażeniu, takich jak dodawanie obiektu JSON w szablonie, należy użyć ukośnika odwrotnego.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Podczas przekazywania wartości parametrów użycie znaków ucieczki zależy od tego, gdzie określono wartość parametru. Jeśli ustawisz wartość domyślną w szablonie, potrzebujesz dodatkowego lewego nawiasu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Jeśli używasz wartości domyślnej, `[test value]`szablon zwraca .

Jednak jeśli przekażesz wartość parametru za pośrednictwem wiersza polecenia, znaki są interpretowane dosłownie. Wdrażanie poprzedniego szablonu za pomocą:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Zwraca wartość `[[test value]`. Zamiast tego należy użyć:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

To samo formatowanie ma zastosowanie podczas przekazywania wartości z pliku parametrów. Znaki są interpretowane dosłownie. W przypadku użycia z poprzednim szablonem `[test value]`zwraca się następujący plik parametrów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Wartości null

Aby ustawić właściwość na wartość null, można użyć **null** lub **[json('null')]**. [Funkcja json](template-functions-array.md#json) zwraca pusty obiekt `null` po podaniu jako parametr. W obu przypadkach szablony Menedżera zasobów traktują go tak, jakby właściwość nie była obecna.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę funkcji szablonu, zobacz [Funkcje szablonów usługi Azure Resource Manager](template-functions.md).
* Aby uzyskać więcej informacji o plikach [szablonów, zobacz Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
