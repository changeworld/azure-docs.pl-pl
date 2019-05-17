---
title: Szczegóły struktura definicji zasad
description: W tym artykule opisano, jak zasobu definicji zasad jest używany przez usługę Azure Policy do ustanawiania konwencje dla zasobów w Twojej organizacji, poprzez opisanie, gdy zasady są wymuszane i wpływ, jaki do wykonania.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b9fe723ca13cbee0e31b14e60a6bd740d2a282df
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779290"
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji zasad platformy Azure

Definicje zasad zasobów są używane przez usługę Azure Policy można ustanowić konwencje dla zasobów. Każda definicja opisano zgodność zasobów i jakie efektu do podjęcia, gdy zasób jest niezgodna.
Definiowanie Konwencji, pozwalają na nadzorowanie kosztów i zarządzania zasobami. Na przykład można określić, że dozwolone są tylko niektóre typy maszyn wirtualnych. Alternatywnie można wymagać, że wszystkie zasoby mają określony tag. Zasady są dziedziczone przez wszystkie zasoby podrzędne. Jeśli zasady są stosowane do grupy zasobów, ma zastosowanie do wszystkich zasobów w tej grupie zasobów.

Schemat używany przez usługę Azure Policy można znaleźć tutaj: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Użyjesz JSON do tworzenia definicji zasad. Definicja zasad zawiera elementy dla:

- mode
- parameters
- Nazwa wyświetlana
- description
- Reguła zasad
  - Ocena logiczne
  - Efekt

Na przykład następujący kod JSON przedstawiono zasady, które ogranicza, gdzie są wdrożone zasoby:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Wszystkie przykłady usługi Azure Policy znajdują się na [przykładów usługi Azure Policy](../samples/index.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Tryb

**Tryb** określa typów zasobów, które zostanie obliczone dla zasad. Obsługiwane metody to:

- `all`: oceny grupy zasobów i wszystkie typy zasobów
- `indexed`: tylko ocenić typy zasobów, które obsługuje tagi i lokalizacji

Firma Microsoft zaleca, aby ustawić **tryb** do `all` w większości przypadków. Wszystkie definicje zasad, została utworzona za pośrednictwem portalu użycia `all` trybu. Jeśli używasz programu PowerShell lub wiersza polecenia platformy Azure, możesz określić **tryb** parametru ręcznie. Jeśli nie zawiera definicji zasad **tryb** wartości, jego wartość domyślna to `all` w programie Azure PowerShell i do `null` w interfejsie wiersza polecenia platformy Azure. A `null` tryb jest taki sam, jak przy użyciu `indexed` do zapewnienia obsługi zgodności.

`indexed` należy używać podczas tworzenia zasad, które wymuszają tagów lub lokalizacji. Chociaż nie jest to wymagane, zapobiega zasoby, które nie obsługują tagów i lokalizacji wyświetlane jako niezgodne w wyniki sprawdzania zgodności. Wyjątek stanowi **grup zasobów**. Należy ustawić zasady, które wymuszają lokalizacji lub tagów w grupie zasobów **tryb** do `all` i docelowy specjalnie `Microsoft.Resources/subscriptions/resourceGroups` typu. Aby uzyskać przykład, zobacz [wymusić tagi z grupy zasobów](../samples/enforce-tag-rg.md). Aby uzyskać listę zasobów, które obsługują tagów, zobacz [obsługę dla zasobów platformy Azure tagów](../../../azure-resource-manager/tag-support.md).

## <a name="parameters"></a>Parametry

Parametry ułatwiają zarządzanie zasadami dzięki zmniejszeniu liczby definicji zasad. Reakcji parametrów, takich jak pola w formularzu — `name`, `address`, `city`, `state`. Te parametry pozostają niezmienione, jednak ich wartości zmieniają poszczególnych wypełniania formularza.
Parametry działają tak samo, podczas tworzenia zasad. Jeśli dołączysz parametrów w definicji zasad, można ponownie użyć tej zasady dla różnych scenariuszy przy użyciu innej wartości.

> [!NOTE]
> Parametry mogą być dodawane do definicji interfejsu istniejące i przypisane. Nowy parametr musi zawierać **defaultValue** właściwości. Zapobiega to pośrednio odbywa się nieprawidłowe istniejące przypisania zasad lub inicjatywy.

### <a name="parameter-properties"></a>Właściwości parametru

Parametr ma następujące właściwości, które są używane w definicji zasad:

- **name**: Nazwa parametru. Używane przez `parameters` funkcji wdrażania w ramach reguły zasad. Aby uzyskać więcej informacji, zobacz [przy użyciu wartości parametru](#using-a-parameter-value).
- `type`: Określa, czy parametr **ciąg** lub **tablicy**.
- `metadata`: Definiuje właściwości podrzędnych głównie używana przez witryny Azure portal, aby wyświetlić informacje o przyjazny dla użytkownika:
  - `description`: Opis dotyczący przeznaczenia parametru. Może służyć do zapewnienia przykładowe dopuszczalne wartości.
  - `displayName`: Przyjazna nazwa wyświetlana w portalu dla parametru.
  - `strongType`: (Opcjonalnie) Używane podczas przypisywania definicji zasad za pośrednictwem portalu. Zawiera listę pamiętać kontekstu. Aby uzyskać więcej informacji, zobacz [strongType](#strongtype).
  - `assignPermissions`: (Opcjonalnie) Ustaw jako _true_ mieć witryny Azure portal utworzyć przypisania roli podczas przypisywania zasad. Ta właściwość jest przydatna w przypadku, gdy chcesz przypisać uprawnienia poza zakres przypisania. Istnieje jedno przypisanie roli na definicji roli w ramach zasad (lub definicji roli we wszystkich zasad w inicjatywy). Wartość tego parametru musi być prawidłowym zasobem lub zakresu.
- `defaultValue`: (Opcjonalnie) Jeśli wartość nie zostanie określony, ustawia wartość parametru w przypisania. Wymagane podczas aktualizowania istniejącej definicji zasad, która jest przypisana.
- `allowedValues`: (Opcjonalnie) Zawiera tablicę wartości, które akceptuje parametr, podczas przypisywania.

Na przykład można zdefiniować definicję zasad, aby ograniczyć lokalizacje, w której można wdrożyć zasoby. Parametr dla tej definicji zasad może być **allowedLocations**. Ten parametr będzie używany przez każdy przypisanie definicji zasad, aby ograniczyć akceptowanych wartości. Korzystanie z **strongType** udostępnia udoskonalone funkcje podczas kończenia przypisania za pośrednictwem portalu:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Przy użyciu wartości parametru

W regule zasad możesz odwoływać się do parametrów za pomocą następujących `parameters` Składnia funkcji wartość wdrożenia:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

W tym przykładzie odwołuje się do **allowedLocations** parametru, która została przedstawiona w [właściwości parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

W ramach `metadata` właściwości, można użyć **strongType** zapewnienie wielokrotnego wyboru listę opcji w witrynie Azure portal. Dozwolone wartości **strongType** obejmują:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Lokalizacja definicji

Podczas tworzenia inicjatywy lub zasad, należy określić lokalizację definicji. Lokalizacja definicji musi być grupą zarządzania lub subskrypcji. Ta lokalizacja określa zakres, do której można przypisać inicjatywy lub zasad. Zasoby muszą być bezpośredni członkowie lub elementy podrzędne w hierarchii Lokalizacja definicji docelową przypisania.

Jeśli lokalizacja definicji to:

- **Subskrypcja** — tylko do zasobów w ramach tej subskrypcji można przypisać zasady.
- **Grupa zarządzania** — tylko do zasobów w ramach subskrypcji podrzędnych i podrzędne grupy zarządzania można przypisać zasady. Jeśli planowane jest zastosowanie definicję zasad do wielu subskrypcji, lokalizacji musi być grupą zarządzania, który zawiera te subskrypcje.

## <a name="display-name-and-description"></a>Nazwę wyświetlaną i opis

Możesz użyć **displayName** i **opis** do identyfikowania definicji zasad i dostarczanie kontekstu, gdy jest używany. **displayName** się składać z maksymalnie _128_ znaków i **opis** maksymalną długość _512_ znaków.

## <a name="policy-rule"></a>Reguła zasad

Reguła zasad składa się z **Jeśli** i **następnie** bloków. W **Jeśli** bloku, należy zdefiniować co najmniej jeden warunek, które określają, kiedy zasady są wymuszane. Operatory logiczne można zastosować do tych warunków, aby precyzyjnie zdefiniować scenariusz dla zasad.

W **następnie** bloku, należy zdefiniować wpływ, jaki się stanie, gdy **Jeśli** warunki są spełnione.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Operatory logiczne

Obsługiwane operatory logiczne to:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**Nie** składni odwraca wynik warunku. **Nieobsługiwanymi** składni (podobne do logicznego **i** operacji) wymaga wszystkie warunki muszą być spełnione. **AnyOf** składni (podobne do logicznego **lub** operacji) wymaga co najmniej jeden warunek to true.

Można zagnieżdżać operatorów logicznych. W poniższym przykładzie przedstawiono **nie** operacji, która jest zagnieżdżony w **nieobsługiwanymi** operacji.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Warunki

Wynikiem warunku jest czy **pola** lub **wartość** akcesor spełnia określone kryteria. Obsługiwane warunki to:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Korzystając z **takich jak** i **notLike** warunki, należy podać symbol wieloznaczny `*` wartości.
Wartość nie powinna zawierać więcej niż jeden symbol wieloznaczny `*`.

Korzystając z **dopasowania** i **notMatch** warunki `#` do dopasowania cyfrę, `?` literę `.` aby dopasować dowolny znak i innych znaków, aby dopasować rzeczywiste znaku.
**odpowiada** i **notMatch** jest rozróżniana wielkość liter. Bez uwzględniania wielkości liter alternatywy są dostępne w **matchInsensitively** i **notMatchInsensitively**. Aby uzyskać przykłady, zobacz [Zezwalaj na kilka wzorców nazwy](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Pola

Warunki są tworzone przy użyciu pól. Pole jest zgodna właściwości w ładunku żądania zasobów i opisuje stan zasobu.

Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu. Pełna nazwa zasobu jest nazwa zasobu, dołączony przez wszystkie nazwy zasobu nadrzędnego (na przykład "myServer/Moja_baza_danych").
- `kind`
- `type`
- `location`
  - Użyj **globalnego** dla zasobów, które są niezależne od lokalizacji. Aby uzyskać przykład, zobacz [przykłady — dozwolone lokalizacje](../samples/allowed-locations.md).
- `identity.type`
  - Zwraca typ [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md) włączona w zasobie.
- `tags`
- `tags['<tagName>']`
  - Ta składnia nawiasu obsługuje nazwy tagów, zawierających znaki interpunkcyjne, takie jak łącznik, kropką ani spacją.
  - Gdzie **\<tagName\>** jest nazwa tagu do sprawdzania warunku.
  - Przykłady: `tags['Acct.CostCenter']` gdzie **Acct.CostCenter** jest nazwa tagu.
- `tags['''<tagName>''']`
  - Ta składnia nawiasu obsługuje nazwy tagu, które mają apostrofy w nim z podwójnym apostrofy.
  - Gdzie **"\<tagName\>"** jest nazwa tagu do sprawdzania warunku.
  - Przykład: `tags['''My.Apostrophe.Tag''']` gdzie **"\<tagName\>"** jest nazwa tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`, i `tags[tag.with.dots]` nadal dopuszczalne sposoby deklarowania pól tagów.
> Jednak preferowaną wyrażenia są wymienione powyżej.

#### <a name="use-tags-with-parameters"></a>Za pomocą tagów z parametrami

Wartość parametru może być przekazywany do pola tag. Przekazywanie parametru do pola tag zwiększa elastyczność definicji zasad podczas przypisywania zasad.

W poniższym przykładzie `concat` służy do tworzenia wyszukiwanie tag o nazwie wartość pola tagi **tagName** parametru. Jeśli ten znacznik nie istnieje, **Dołącz** efekt służy do dodawania tagów przy użyciu wartości tego samego tagu o nazwie ustawić w grupie zasobów, nadrzędny inspekcji zasobów przy użyciu `resourcegroup()` funkcji wyszukiwania.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Wartość

Warunki można również tworzone za pomocą **wartość**. **wartość** sprawdza warunki względem [parametry](#parameters), [obsługiwane funkcje szablonu](#policy-functions), albo literały.
**wartość** jest powiązany z żadną obsługiwane [warunek](#conditions).

> [!WARNING]
> Jeśli wynik _funkcji szablonu_ , występuje błąd, zasady oceny zakończy się niepowodzeniem. Obliczanie nie powiodło się to bezwarunkowy **Odmów**. Aby uzyskać więcej informacji, zobacz [unikanie błędów szablonu](#avoiding-template-failures).

#### <a name="value-examples"></a>Przykładowe wartości

W tym przykładzie reguły zasad użyto **wartość** do porównania z wynikiem `resourceGroup()` funkcji i zwrócony **nazwa** właściwości **takich jak** warunek `*netrg`. Reguła nie zezwala na dowolny zasób nie jest `Microsoft.Network/*` **typu** w dowolnej grupie zasobów, których nazwa kończy się na `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

W tym przykładzie reguły zasad użyto **wartość** do sprawdzenia, jeśli wynikiem wielu zagnieżdżone funkcje **jest równa** `true`. Reguła nie zezwala na dowolnym zasobem, który nie ma co najmniej trzy znaczniki.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Unikanie błędów szablonu

Korzystanie z _funkcje szablonu_ w **wartość** umożliwia złożonych dużo zagnieżdżonych funkcji. Jeśli wynik _funkcji szablonu_ , występuje błąd, zasady oceny zakończy się niepowodzeniem. Obliczanie nie powiodło się to bezwarunkowy **Odmów**. Przykładem **wartość** niepowodzenia w niektórych scenariuszach:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Przykładowa reguła zasad powyżej używa [substring()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) do porównania pierwsze trzy znaki **nazwa** do **abc**. Jeśli **nazwa** jest krótszy niż trzy znaki `substring()` funkcja powoduje błąd. Ten błąd powoduje, że zasady aby stać się **Odmów** efekt.

Zamiast tego należy użyć [if()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) funkcji, aby sprawdzić, czy pierwsze trzy znaki **nazwa** równy **abc** bez możliwości **nazwa** mniej niż trzy znaki do powodują wystąpienie błędu:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Reguły zasad poprawione `if()` sprawdza długość **nazwa** przed próby uzyskania `substring()` na wartość składającą się z mniej niż trzy znaki. Jeśli **nazwa** jest zbyt krótki, a wartość "nie począwszy od abc", jest zwracana zamiast i w porównaniu do **abc**. Zasób o krótką nazwę, która nie rozpoczyna się od **abc** nadal kończy się niepowodzeniem reguły zasad, ale nie powoduje już wystąpił błąd podczas obliczania wartości.

### <a name="effect"></a>Efekt

Usługa Azure Policy obsługuje następujące typy wpływu:

- **Odmów**: generuje zdarzenie w dzienniku aktywności i kończy się niepowodzeniem żądania
- **Inspekcja**: generuje to zdarzenie ostrzegawcze w dzienniku aktywności, ale nie zakończy się niepowodzeniem żądania
- **Dołącz**: dodaje zestaw zdefiniowanych pól do żądania
- **AuditIfNotExists**: umożliwia inspekcję, jeśli zasób nie istnieje.
- **DeployIfNotExists**: wdraża zasobu, jeśli jeszcze nie istnieje.
- **Wyłączone**: nie szacuje zasoby pod kątem zgodności z regułą zasad

Aby uzyskać **Dołącz**, należy podać następujące informacje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Wartość może być ciąg lub obiekt do formatu JSON.

**AuditIfNotExists** i **DeployIfNotExists** ocenić istnienie powiązanego zasobu i stosowania reguły. Jeśli zasób nie odpowiada regule, efekt jest zaimplementowana. Na przykład można wymagać, że usługi network watcher jest wdrażana dla wszystkich sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [inspekcji, jeśli rozszerzenie nie istnieje](../samples/audit-ext-not-exist.md) przykład.

**DeployIfNotExists** wymaga efekt **roleDefinitionId** właściwość **szczegóły** część reguły. Aby uzyskać więcej informacji, zobacz [korygowania — konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Aby uzyskać szczegółowe informacje dotyczące każdego skutku, kolejność oceny, właściwości i przykłady, zobacz [Understanding Azure zasad efekty](effects.md).

### <a name="policy-functions"></a>Funkcje zasad

Wszystkie [funkcje szablonu usługi Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) są dostępne do użycia w ramach reguły zasad, z wyjątkiem następujących funkcji i funkcji zdefiniowanych przez użytkownika:

- copyIndex()
- Deployment()
- Lista *
- newGuid()
- pickZones()
- Providers()
- Reference()
- resourceId()
- variables()

Następujące funkcje są dostępne do użycia w regule zasad, ale różnią się od użycia w szablonie usługi Azure Resource Manager:

- addDays (daty/godziny, numberOfDaysToAdd)
  - **Data i godzina**: [wymagane] ciąg - ciąg w formacie uniwersalnej daty/godziny ISO 8601 "RRRR-MM-ddTHH:mm:ss.fffffffZ"
  - **numberOfDaysToAdd**: liczba całkowita [wymagane] - liczba dni do dodania
- utcNow() — w przeciwieństwie do szablonu usługi Resource Manager, może być używany poza defaultValue.
  - Zwraca ciąg, który jest ustawiona na bieżącą datę i godzinę w formacie uniwersalnej daty/godziny ISO 8601 "RRRR-MM-ddTHH:mm:ss.fffffffZ"

Ponadto `field` funkcja jest dostępna z regułami zasad. `field` jest używany głównie z **AuditIfNotExists** i **DeployIfNotExists** do pola odniesienia dla zasobu, które są oceniane. Przykładem użycia tego można zobaczyć w [przykład DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Przykład funkcji zasad

Używa w tym przykładzie reguły zasad `resourceGroup` funkcję zasobów w celu uzyskania **nazwa** właściwość w połączeniu z `concat` tablicy i obiektu funkcji do tworzenia `like` warunek, który wymusza Nazwa zasobu, aby rozpocząć nazwą grupy zasobów.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliasy

Aliasy właściwości umożliwia dostęp do właściwości specyficzne dla typu zasobu. Aliasy pozwalają ograniczyć, jakie wartości lub warunków, które są dozwolone dla właściwości zasobu. Każdy alias mapuje do ścieżki w różnych wersjach interfejsu API dla danego typu zasobu. Podczas oceny zasad aparatu zasad pobiera ścieżkę właściwości dla danej wersji interfejsu API.

Zawsze rośnie listę aliasów. Aby dowiedzieć się, jakie aliasy są obecnie obsługiwane przez usługę Azure Policy, użyj jednej z następujących metod:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
  ```

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- Interfejs API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Opis alias [*]

Mieć wiele aliasów, które są dostępne wersji, która jest wyświetlana jako nazwa "normal", a drugi zawierający **[\*]** podłączone do niego. Na przykład:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias "normal" reprezentuje pole jako pojedyncza wartość. To pole jest scenariuszach porównania dokładnego dopasowania, gdy cały zestaw wartości muszą zawierać dokładnie zgodnie z definicją, już nie.

**[\*]** Alias sprawia, że można porównać wartości każdego elementu w tablicy i właściwości każdego elementu. To podejście sprawia, że można porównać właściwości elementu "Jeśli żadna z", "ewentualne", lub "Jeśli wszystkie z" scenariuszy. Za pomocą **ipRules [\*]**, przykład czy sprawdzanie poprawności, które każdy _akcji_ jest _Odmów_, ale nie martwiąc się o istnieją reguły ile lub jaki adres IP _wartość_ jest. Ta przykładowa reguła sprawdza, czy wszystkie dopasowania **ipRules [\*] .value** do **10.0.4.1** i stosuje **effectType** tylko wtedy, gdy co najmniej jedno dopasowanie nie zostanie znaleziona:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Aby uzyskać więcej informacji, zobacz [dokonanie oceny oprogramowania [\*] alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Inicjatywy

Inicjatywy pozwalają grupować kilka definicji zasad powiązane w celu uproszczenia przypisań i zarządzania, ponieważ współdziała z grupą jako pojedynczy element. Na przykład można grupować powiązane definicje zasad tagowania w jednym inicjatywy. Zamiast przypisywać każdej z zasad indywidualnie, mają zastosowanie tej inicjatywy.

Poniższy przykład ilustruje sposób tworzenia inicjatywy do obsługi dwa tagi: `costCenter` i `productName`. Używa dwóch wbudowane zasady do zastosowania domyślną wartość tagu.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Zrozumienie sposobu [programowe tworzenie zasad](../how-to/programmatically-create.md).
- Dowiedz się, jak [Pobierz dane zgodności](../how-to/getting-compliance-data.md).
- Dowiedz się, jak [korygowanie niezgodnych zasobów](../how-to/remediate-resources.md).
- Przejrzyj grupy zarządzania jest [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../management-groups/overview.md).