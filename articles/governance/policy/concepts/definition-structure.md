---
title: Szczegóły struktura definicji zasad
description: W tym artykule opisano, jak zasobu definicji zasad jest używany przez usługę Azure Policy do ustanawiania konwencje dla zasobów w Twojej organizacji, poprzez opisanie, gdy zasady są wymuszane i wpływ, jaki do wykonania.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: deaad5e7a04362ab021bfb90ab08377cb99694e0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250388"
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
                }
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

Wszystkie przykłady usługi Azure Policy znajdują się na [Przykłady zasad](../samples/index.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Tryb

**Tryb** określa typów zasobów, które zostanie obliczone dla zasad. Obsługiwane metody to:

- `all`: oceny grupy zasobów i wszystkie typy zasobów
- `indexed`: tylko ocenić typy zasobów, które obsługuje tagi i lokalizacji

Firma Microsoft zaleca, aby ustawić **tryb** do `all` w większości przypadków. Wszystkie definicje zasad, została utworzona za pośrednictwem portalu użycia `all` trybu. Jeśli używasz programu PowerShell lub wiersza polecenia platformy Azure, możesz określić **tryb** parametru ręcznie. Jeśli nie zawiera definicji zasad **tryb** wartości, jego wartość domyślna to `all` w programie Azure PowerShell i do `null` w interfejsie wiersza polecenia platformy Azure. A `null` tryb jest taki sam, jak przy użyciu `indexed` do zapewnienia obsługi zgodności.

`indexed` należy używać podczas tworzenia zasad, które wymuszają tagów lub lokalizacji. Chociaż nie jest to wymagane, zapobiega zasoby, które nie obsługują tagów i lokalizacji wyświetlane jako niezgodne w wyniki sprawdzania zgodności. Wyjątek stanowi **grup zasobów**. Należy ustawić zasady, które wymuszają lokalizacji lub tagów w grupie zasobów **tryb** do `all` i docelowy specjalnie `Microsoft.Resources/subscriptions/resourceGroup` typu. Aby uzyskać przykład, zobacz [wymusić tagi z grupy zasobów](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>Parametry

Parametry ułatwiają zarządzanie zasadami dzięki zmniejszeniu liczby definicji zasad. Reakcji parametrów, takich jak pola w formularzu — `name`, `address`, `city`, `state`. Te parametry pozostają niezmienione, jednak ich wartości zmieniają poszczególnych wypełniania formularza.
Parametry działają tak samo, podczas tworzenia zasad. Jeśli dołączysz parametrów w definicji zasad, można ponownie użyć tej zasady dla różnych scenariuszy przy użyciu innej wartości.

> [!NOTE]
> Definicji parametrów zasad lub definicji inicjatywy można skonfigurować tylko podczas początkowego tworzenia zasad lub inicjatywy. Nie można później zmienić definicji parametrów.
> Zapobiega to pośrednio odbywa się nieprawidłowe istniejące przypisania zasad lub inicjatywy.

Na przykład można zdefiniować zasadę, aby ograniczyć lokalizacje, w której można wdrożyć zasoby.
Podczas tworzenia zasad, może zadeklarować następujące parametry:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

Typ parametru może być ciąg lub tablicy. Właściwość metadanych służy do narzędzi, takich jak witryny Azure portal do wyświetlania informacji przyjazny dla użytkownika.

W ramach właściwości metadanych, możesz użyć **strongType** zapewnienie wielokrotnego wyboru listę opcji w witrynie Azure portal. Dozwolone wartości **strongType** obejmują:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

W regule zasad możesz odwoływać się do parametrów za pomocą następujących `parameters` Składnia funkcji wartość wdrożenia:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Lokalizacja definicji

Podczas tworzenia inicjatywy lub zasad, należy określić lokalizację definicji. Lokalizacja definicji musi być grupą zarządzania lub subskrypcji. Ta lokalizacja określa zakres, do której można przypisać inicjatywy lub zasad. Zasoby muszą być bezpośredni członkowie lub elementy podrzędne w hierarchii Lokalizacja definicji docelową przypisania.

Jeśli lokalizacja definicji to:

- **Subskrypcja** — tylko do zasobów w ramach tej subskrypcji można przypisać zasady.
- **Grupa zarządzania** — tylko do zasobów w ramach subskrypcji podrzędnych i podrzędne grupy zarządzania można przypisać zasady. Jeśli planowane jest zastosowanie definicję zasad do wielu subskrypcji, lokalizacji musi być grupą zarządzania, który zawiera te subskrypcje.

## <a name="display-name-and-description"></a>Nazwę wyświetlaną i opis

Możesz użyć **displayName** i **opis** do identyfikowania definicji zasad i dostarczanie kontekstu, gdy jest używany.

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

Wynikiem warunku jest czy **pola** spełnia określone kryteria. Obsługiwane warunki to:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Korzystając z **takich jak** i **notLike** warunki, należy podać symbol wieloznaczny `*` wartości.
Wartość nie powinna zawierać więcej niż jeden symbol wieloznaczny `*`.

Korzystając z **dopasowania** i **notMatch** warunki `#` do dopasowania cyfrę, `?` literę `.` spełnić wszystkie znaki i innych znaków, aby dopasować rzeczywiste znaku. Aby uzyskać przykłady, zobacz [Zezwalaj na kilka wzorców nazwy](../samples/allow-multiple-name-patterns.md).

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
- `tags.<tagName>`
  - Gdzie **\<tagName\>** jest nazwa tagu do sprawdzania warunku.
  - Przykład: `tags.CostCenter` gdzie **CostCenter** jest nazwa tagu.
- `tags[<tagName>]`
  - Ta składnia nawiasu obsługuje nazwy tagów, które mają okres.
  - Gdzie **\<tagName\>** jest nazwa tagu do sprawdzania warunku.
  - Przykład: `tags[Acct.CostCenter]` gdzie **Acct.CostCenter** jest nazwa tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

### <a name="effect"></a>Efekt

Zasady obsługuje następujące typy wpływu:

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

Aby uzyskać szczegółowe informacje dotyczące każdego skutku, kolejność oceny, właściwości i przykłady, zobacz [zrozumienie zasad efekty](effects.md).

### <a name="policy-functions"></a>Funkcje zasad

Kilka [funkcje szablonu usługi Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) są dostępne do użycia w ramach reguły zasad. Są obecnie obsługiwane funkcje:

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [Subskrypcja](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Ponadto `field` funkcja jest dostępna z regułami zasad. `field` jest używany głównie z **AuditIfNotExists** i **DeployIfNotExists** do pola odniesienia dla zasobu, które są oceniane. Przykładem użycia tego można zobaczyć w [przykład DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Przykłady operacji dotyczących zasad — funkcja

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

W tym przykładzie reguły zasad użyto `resourceGroup` funkcję zasobów w celu uzyskania **tagi** wartości właściwości tablicy **CostCenter** tagów w grupie zasobów, a następnie dołącza je do **CostCenter**  tagiem na nowy zasób.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
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

Pierwszy przykład jest używany do oceny całej tablicy, której **[\*]** alias ocenia każdy element tablicy.

Przyjrzyjmy się reguły zasad jako przykład. Te zasady będą **Odmów** konta magazynu, który ma skonfigurowane ipRules i, jeśli **Brak** ipRules ma wartość "127.0.0.1".

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

**IpRules** tablicy jest następujący przykład:

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Poniżej przedstawiono sposób przetwarzania w tym przykładzie:

- `networkAcls.ipRules` — Sprawdź, czy tablica jest różna od null. Oblicza wartość true, dlatego ocena jest kontynuowana.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` -Sprawdza każdą _wartość_ właściwość **ipRules** tablicy.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Jako tablicę każdy element zostanie przetworzone.

    - "127.0.0.1"! = "127.0.0.1" ma wartość false.
    - "127.0.0.1"! = "192.168.1.1" ma wartość true.
    - Co najmniej jeden _wartość_ właściwość **ipRules** tablicy ocenione jako fałszywe, więc ocena zostanie zatrzymane.

Jako wartość false, warunek **Odmów** efekt nie zostanie wyzwolony.

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

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md)
- Przegląd [zrozumienia efektów zasad](effects.md)
- Zrozumienie sposobu [programowe tworzenie zasad](../how-to/programmatically-create.md)
- Dowiedz się, jak [uzyskać dane na temat zgodności](../how-to/getting-compliance-data.md)
- Dowiedz się, jak [korygowanie niezgodnych zasobów](../how-to/remediate-resources.md)
- Sprawdzanie, co to jest grupa zarządzania, na stronie [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure)](../../management-groups/overview.md)