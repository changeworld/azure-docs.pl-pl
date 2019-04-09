---
title: Tworzenie zasad dla właściwości tablicy dla zasobów platformy Azure
description: Dowiedz się, jak tworzyć parametry tablicy, tworzenie reguł dla tablicy wyrażeń języka, oceny alias [*] i można dołączyć elementy do istniejącej tablicy przy użyciu usługi Azure Policy definition reguł.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 38cf6decb8e61768faa9680058f6366e1550ba40
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274727"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Tworzenie zasad dla właściwości tablicy dla zasobów platformy Azure

Właściwości usługi Azure Resource Manager często są definiowane jako ciągi i wartości logiczne. W przypadku istnienia relacji jeden do wielu złożonych właściwości zamiast tego są definiowane jako tablice. Usługa Azure Policy tablice są używane na kilka różnych sposobów:

- Typ [definicji parametru](../concepts/definition-structure.md#parameters)w celu zapewnienia wiele opcji
- Część [reguła zasad](../concepts/definition-structure.md#policy-rule) przy użyciu warunków **w** lub **notIn**
- Część reguły zasad, które ocenia [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) do oceny konkretnych scenariuszy, takich jak **Brak**, **wszelkie**, lub  **Wszystkie**
- W [Dołącz efekt](../concepts/effects.md#append) Zastąp lub dodać do istniejącej tablicy

W tym artykule opisano każde użycie przez usługę Azure Policy i udostępnia kilka definicje przykładu.

## <a name="parameter-arrays"></a>Parameter — tablice

### <a name="define-a-parameter-array"></a>Zdefiniuj tablicy parametrów

Definiowanie parametru w postaci tablicy umożliwia elastyczność zasad, gdy będzie potrzebny więcej niż jedną wartość.
Ta definicja zasad zezwala na dowolnej pojedynczej lokalizacji dla parametru **allowedLocations** i domyślnie ustawiany na _eastus2_:

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

Jako **typu** został _ciąg_tylko jedną wartość można ustawić po przypisaniu zasad. Jeśli zasada ta jest przypisany, zasobów w zakresie są dozwolone tylko w jednym regionie platformy Azure. Większość definicje zasad muszą zezwalać na listę zatwierdzonych opcje, takie jak umożliwiając _eastus2_, _eastus_, i _westus2_.

Aby utworzyć definicję zasad, aby zezwolić na wiele sposobów, użyj _tablicy_ **typu**. Te same zasady można inaczej w następujący sposób:

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
> Po zapisaniu definicję zasad **typu** nie można zmienić właściwości dla parametru.

Ta nowa definicja parametru ma więcej niż jednej wartości podczas przypisywania zasad. Za pomocą właściwości tablicy **allowedValues** zdefiniowane, dostępne podczas przypisywania wartości są dalej ograniczone do wstępnie zdefiniowanej listy opcji. Korzystanie z **allowedValues** jest opcjonalne.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Przekazywanie wartości do tablicy parametrów podczas przypisywania

Podczas przypisywania zasad w witrynie Azure portal, parametr **typu** _tablicy_ jest wyświetlany jako pojedynczego pola tekstowego. Wskazówka mówi "przy użyciu; do oddzielania wartości. (np. Londyn; New York) ". Aby przekazać wartości dozwolonych lokalizacji _eastus2_, _eastus_, i _westus2_ parametru należy użyć następującego ciągu:

`eastus2;eastus;westus2`

Format wartości parametru różni się przy użyciu wiersza polecenia platformy Azure, programu Azure PowerShell lub interfejsu API REST. Wartości są przekazywane za pośrednictwem ciągu JSON, która zawiera również nazwę parametru.

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

Aby użyć tego ciągu z każdego zestawu SDK, użyj następujących poleceń:

- Interfejs wiersza polecenia platformy Azure: Polecenie [utworzenia przypisania zasad az](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) z parametrem **params**
- Azure PowerShell: Polecenia cmdlet [New AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) z parametrem **PolicyParameter**
- INTERFEJS API REST: W _umieścić_ [tworzenie](/rest/api/resources/policyassignments/create) operacji jako część treści żądania, ponieważ wartość **properties.parameters** właściwości

## <a name="policy-rules-and-arrays"></a>Reguły zasad i tablice

### <a name="array-conditions"></a>Warunki tablicy

Reguła zasad [warunki](../concepts/definition-structure.md#conditions) , _tablicy_
**typu** parametru może być używany z jest ograniczona do `in` i `notIn`. Wykonaj następującą definicję zasad z warunkiem `equals` jako przykładu:

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

Próba ta definicja zasad za pośrednictwem portalu Azure potencjalnych klientów błąd, taki jak ten komunikat o błędzie:

- "Zasady"{GUID}"nie można sparametryzować z powodu błędów weryfikacji. Sprawdź, jeśli parametry zasad są poprawnie zdefiniowane. Wyjątek wewnętrzny 'oceny wynikiem wyrażenia języka "[parameters('allowedLocations')]" jest typu "Array", oczekiwany jest typ "String" "."

Oczekiwana **typu** warunku `equals` jest _ciąg_. Ponieważ **allowedLocations** jest zdefiniowany jako **typu** _tablicy_, aparat zasad ocenia wyrażenie języka i zgłasza błąd. Za pomocą `in` i `notIn` warunku oczekuje, że aparat zasad **typu** _tablicy_ w wyrażeniu języka. Aby rozwiązać ten komunikat o błędzie, zmień `equals` do jednej `in` lub `notIn`.

### <a name="evaluating-the--alias"></a>Ocena alias [*]

Aliasy, które mają **[\*]** dołączony do nazwy wskazują **typu** jest _tablicy_. Zamiast obliczenia wartości całej tablicy **[\*]** pozwala na ocenę każdego elementu tablicy. Istnieją trzy scenariusze, na obliczania wartości elementu to przydatne: Brak, dowolny i wszystkie.

Wyzwalacze aparatu zasad **efekt** w **następnie** tylko wtedy, gdy **Jeśli** reguły ma wartość true.
Ten fakt ważne jest, aby zrozumieć, w kontekście sposób **[\*]** ocenia każdego pojedynczego elementu tablicy.

Przykładowa reguła zasad dla poniższej tabeli scenariusza:

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

**IpRules** tablicy jest następująca dla poniższej tabeli scenariusza:

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

Dla każdego warunku poniższym przykładzie Zastąp `<field>` z `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Następujących rezultatów są wynikiem kombinacji warunek i przykładowa reguła zasad i Tablica powyższych wartości istniejących:

|Warunek |Wynik |Wyjaśnienie |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nie rób |Jednym z elementów tablicy jest interpretowany jako wartość false (127.0.0.1! = 127.0.0.1), a drugi jako wartość true (127.0.0.1! = 192.168.1.1), więc **notEquals** warunek jest _false_ i nie zostanie wyzwolony, efekt. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efekt zasad |Oba te elementy tablicy są interpretowane jako wartość true (10.0.4.1! = 127.0.0.1 oraz 10.0.4.1! = 192.168.1.1), więc **notEquals** warunek jest _true_ i jest wyzwalana efekt. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efekt zasad |Jednym z elementów tablicy jest interpretowany jako wartość true (127.0.0.1 == 127.0.0.1), a drugi jako FAŁSZ (127.0.0.1 == 192.168.1.1), więc **jest równa** warunek jest _false_. Operator logiczny, który jest interpretowany jako wartość true (**nie** _false_), więc efekt zostanie wywołany. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efekt zasad |Oba te elementy tablicy są interpretowane jako wartość false (10.0.4.1 == 127.0.0.1 i 10.0.4.1 == 192.168.1.1), więc **jest równa** warunek jest _false_. Operator logiczny, który jest interpretowany jako wartość true (**nie** _false_), więc efekt zostanie wywołany. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efekt zasad |Jednym z elementów tablicy jest interpretowany jako wartość false (127.0.0.1! = 127.0.0.1), a drugi jako wartość true (127.0.0.1! = 192.168.1.1), więc **notEquals** warunek jest _false_. Operator logiczny, który jest interpretowany jako wartość true (**nie** _false_), więc efekt zostanie wywołany. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nie rób |Oba te elementy tablicy są interpretowane jako wartość true (10.0.4.1! = 127.0.0.1 oraz 10.0.4.1! = 192.168.1.1), więc **notEquals** warunek jest _true_. Operator logiczny, który jest interpretowany jako wartość false (**nie** _true_), więc nie zostanie wyzwolony, efekt. |
|`{<field>,"Equals":"127.0.0.1"}` |Nie rób |Jednym z elementów tablicy jest interpretowany jako wartość true (127.0.0.1 == 127.0.0.1), a drugi jako FAŁSZ (127.0.0.1 == 192.168.1.1), więc **jest równa** warunek jest _false_ i nie zostanie wyzwolony, efekt. |
|`{<field>,"Equals":"10.0.4.1"}` |Nie rób |Oba te elementy tablicy są interpretowane jako wartość false (10.0.4.1 == 127.0.0.1 i 10.0.4.1 == 192.168.1.1), więc **jest równa** warunek jest _false_ i nie zostanie wyzwolony, efekt. |

## <a name="the-append-effect-and-arrays"></a>Dołącz efektu i tablice

[Dołącz efekt](../concepts/effects.md#append) zachowuje się inaczej w zależności od tego, jeśli **details.field** jest **[\*]** alias lub nie.

- Jeśli ta wartość nie **[\*]** alias, Dołącz zastępuje całej tablicy przy użyciu **wartość** właściwości
- Gdy **[\*]** alias, Dołącz dodaje **wartość** właściwości do istniejącej tablicy lub tworzy nową tablicę

Aby uzyskać więcej informacji, zobacz [Dołącz przykłady](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md)
- Przegląd [struktura definicji zasad](../concepts/definition-structure.md)
- Przegląd [zrozumienia efektów zasad](../concepts/effects.md)
- Zrozumienie sposobu [programowe tworzenie zasad](programmatically-create.md)
- Dowiedz się, jak [korygowanie niezgodnych zasobów](remediate-resources.md)
- Sprawdzanie, co to jest grupa zarządzania, na stronie [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure)](../../management-groups/overview.md)