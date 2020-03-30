---
title: Zasady tworzenia właściwości tablicy w zasobach
description: Dowiedz się, jak pracować z parametrami tablicy i wyrażeniami języka tablicy, oceniać alias [*] i dołączać elementy z regułami definicji zasad platformy Azure.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280666"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Zasady tworzenia właściwości tablicy w zasobach platformy Azure

Właściwości usługi Azure Resource Manager są powszechnie definiowane jako ciągi i wartości logiczne. Gdy istnieje relacja jeden-do-wielu, złożone właściwości są zamiast tego definiowane jako tablice. W usłudze Azure Policy tablice są używane na kilka różnych sposobów:

- Typ [parametru definicji](../concepts/definition-structure.md#parameters), aby zapewnić wiele opcji
- Część [reguły zasad](../concepts/definition-structure.md#policy-rule) przy użyciu **notIn** warunków **w**
- Część reguły zasad, która ocenia [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) do oceny:
  - Scenariusze takie jak **Brak,** **Dowolny**lub **Wszystkie**
  - Złożone scenariusze z **liczbą**
- W [efekcie dołączania,](../concepts/effects.md#append) aby zastąpić lub dodać do istniejącej tablicy

W tym artykule opisano każde użycie przez usługę Azure Policy i zawiera kilka przykładowych definicji.

## <a name="parameter-arrays"></a>Tablice parametrów

### <a name="define-a-parameter-array"></a>Definiowanie tablicy parametrów

Definiowanie parametru jako tablicy umożliwia elastyczność zasad, gdy potrzebna jest więcej niż jedna wartość.
Ta definicja zasad umożliwia dowolną pojedynczą lokalizację dla parametru **allowedLocations** i domyślnie _eastus2:_

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Jako **typ** był _ciągiem_, podczas przypisywania zasad można ustawić tylko jedną wartość. Jeśli ta zasada jest przypisana, zasoby w zakresie są dozwolone tylko w jednym regionie platformy Azure. Większość definicji zasad musi umożliwiać wyświetlanie listy zatwierdzonych opcji, takich jak _zezwolenie na eastus2_, _eastus_i _westus2._

Aby utworzyć definicję zasad zezwalania na wiele opcji, użyj **typu** _tablicy_ . Te same zasady można przepisać w następujący sposób:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Po zapisaniu definicji zasad nie można zmienić właściwości **typu** parametru.

Ta nowa definicja parametru przyjmuje więcej niż jedną wartość podczas przypisywania zasad. Z właściwością tablicy **allowedValues zdefiniowane,** wartości dostępne podczas przypisywania są dodatkowo ograniczone do wstępnie zdefiniowanej listy wyborów. Użycie **allowedValues** jest opcjonalne.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Przekazywanie wartości do tablicy parametrów podczas przypisywania

Podczas przypisywania zasad za pośrednictwem witryny Azure portal parametr _tablicy_ **typu** jest wyświetlany jako pojedynczy pole tekstowe. Wskazówka mówi: "Użyj ; aby oddzielić wartości. (np. w Londynie; Nowy Jork)". Aby przekazać do parametru dozwolone wartości lokalizacji _eastus2_, _eastus_i _westus2,_ należy użyć następującego ciągu:

`eastus2;eastus;westus2`

Format dla wartości parametru jest inny podczas korzystania z interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell lub interfejsu API REST. Wartości są przekazywane przez ciąg JSON, który zawiera również nazwę parametru.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Aby użyć tego ciągu z każdym sdk, należy użyć następujących poleceń:

- Azure CLI: Command az policy assignment create with parameter params Azure CLI: Command az policy assignment create with parameter params Azure CLI: Command [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) with parameter **params** Azure CLI
- Azure PowerShell: polecenie cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) z **parametrem PolicyParameter**
- INTERFEJS API REST: W _operacji put_ [create](/rest/api/resources/policyassignments/create) jako część treści żądania jako wartość **właściwości properties.parameters**

## <a name="policy-rules-and-arrays"></a>Reguły zasad i tablice

### <a name="array-conditions"></a>Warunki tablicy

[Warunki](../concepts/definition-structure.md#conditions) reguły zasad, z którymi może być używany `in` `notIn`**typ** _tablicy_
parametru, są ograniczone do . Weźmy na przykład `equals` następującą definicję zasad z warunkiem:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Próba utworzenia tej definicji zasad za pośrednictwem witryny Azure portal prowadzi do błędu, takiego jak ten komunikat o błędzie:

- Nie można sparametryzować zasad '{GUID}' z powodu błędów sprawdzania poprawności. Sprawdź, czy parametry zasad są poprawnie zdefiniowane. Wewnętrzny wyjątek 'Wynik oceny wyrażenia języka '[parameters('allowedLocations')]' to typ 'Array', oczekiwany typ to 'String'.'.

Oczekiwanym **typem** warunku `equals` jest _ciąg_. Ponieważ **dozwoloneLokacje** są definiowane jako _tablica_ **typów,** aparat zasad ocenia wyrażenie języka i zgłasza błąd. Z `in` i `notIn` warunek aparat zasad oczekuje _tablicy_ **typów** w wyrażeniu języka. Aby rozwiązać ten komunikat `equals` o `in` błędzie, zmień na jedną lub `notIn`.

### <a name="evaluating-the--alias"></a>Ocena aliasu [*]

Aliasy dołączone ** \[ \* ** do ich nazwy wskazują, że **typem** jest _tablica_. Zamiast oceniać wartość całej tablicy, ** \[ \* ** umożliwia ocenę każdego elementu tablicy indywidualnie, z logicznym i między nimi. Istnieją trzy standardowe scenariusze, które na ocenę elementu jest przydatne w: _Brak_, _Any_lub _Wszystkie_ elementy są zgodne. W przypadku scenariuszy złożonych należy użyć [funkcji Count](../concepts/definition-structure.md#count).

Aparat zasad wyzwala **efekt** **wówczas** tylko wtedy, gdy **if** reguła ocenia jako true.
Fakt ten jest ważne, aby ** \[ \* ** zrozumieć w kontekście sposobu ocenia każdy pojedynczy element tablicy.

Przykładowa reguła zasad dla poniższej tabeli scenariuszy:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Tablica **ipRules** jest następująca dla poniższej tabeli scenariuszy:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Dla każdego poniższego `<field>` przykładu stanu zastąp `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Następujące wyniki są wynikiem kombinacji warunku i przykładowej reguły zasad i tablicy istniejących wartości powyżej:

|Warunek |Wynik | Scenariusz |Wyjaśnienie |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Brak dopasowania |Jeden element tablicy jest obliczany jako fałszywy (127.0.0.1 != 127.0.0.1) i jeden jako true (127.0.0.1 != 192.168.1.1), więc warunek **notEquals** jest _fałszywy,_ a efekt nie jest wyzwalany. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efekt polityki |Brak dopasowania |Oba elementy tablicy oceniają jako prawdziwe (10.0.4.1 != 127.0.0.1 i 10.0.4.1 != 192.168.1.1), więc warunek **notEquals** jest _prawdziwy_ i efekt jest wyzwalany. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efekt polityki |Co najmniej jedno dopasowanie |Jeden element tablicy jest oblicza jako fałszywy (127.0.0.1 != 127.0.0.1) i jeden jako prawdziwy (127.0.0.1 != 192.168.1.1), więc warunek **notEquals** jest _fałszywy_. Operator logiczny ocenia jako true (**nie** _false_), więc efekt jest wyzwalany. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Co najmniej jedno dopasowanie |Oba elementy tablicy oceniają jako prawdziwe (10.0.4.1 != 127.0.0.1 i 10.0.4.1 != 192.168.1.1), więc warunek **notEquals** jest _prawdziwy_. Operator logiczny ocenia jako false (**nie** _prawda_), więc efekt nie jest wyzwalany. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efekt polityki |Nie wszystkie pasują |Jeden element tablicy jest oblicza jako prawdziwy (127.0.0.1 == 127.0.0.1) i jeden jako false (127.0.0.1 == 192.168.1.1), więc warunek **Equals** jest _fałszywy_. Operator logiczny ocenia jako true (**nie** _false_), więc efekt jest wyzwalany. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efekt polityki |Nie wszystkie pasują |Oba elementy tablicy są obliczane jako fałszywe (10.0.4.1 == 127.0.0.1 i 10.0.4.1 == 192.168.1.1), więc warunek **Równości** jest _fałszywy_. Operator logiczny ocenia jako true (**nie** _false_), więc efekt jest wyzwalany. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Wszystkie mecze |Jeden element tablicy jest obliczany jako true (127.0.0.1 == 127.0.0.1) i jeden jako false (127.0.0.1 == 192.168.1.1), więc warunek **Equals** jest _fałszywy,_ a efekt nie jest wyzwalany. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Wszystkie mecze |Oba elementy tablicy są obliczane jako fałszywe (10.0.4.1 == 127.0.0.1 i 10.0.4.1 == 192.168.1.1), więc warunek **Jest** _fałszywy_ i efekt nie jest wyzwalany. |

## <a name="the-append-effect-and-arrays"></a>Efekt dołączania i tablice

[Efekt dołączania](../concepts/effects.md#append) zachowuje się inaczej w zależności od ** \[ \* ** tego, czy plik **details.field** jest aliasem, czy nie.

- Jeśli nie ** \[ \* ** jest to alias, dołącz zastępuje całą tablicę właściwością **value**
- Gdy alias, dołącz dodaje właściwość wartość do istniejącej tablicy lub tworzy nową tablicę **value** ** \[ \* **

Aby uzyskać więcej informacji, zobacz [przykłady dołączania](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [korygować niezgodne zasoby](remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
