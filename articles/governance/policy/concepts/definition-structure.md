---
title: Struktura definicji zasad platformy Azure
description: W tym artykule opisano, jak zasobu definicji zasad jest używany przez usługę Azure Policy do ustanawiania konwencje dla zasobów w Twojej organizacji, poprzez opisanie, gdy zasady są wymuszane i wpływ, jaki do wykonania.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 329bba11c95056b038fdab5041637cbb6a0feb6a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407267"
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji zasad platformy Azure

Definicja zasad zasobów używane przez usługę Azure Policy umożliwia ustanawianie konwencje dla zasobów w Twojej organizacji poprzez opisanie, gdy zasady są wymuszane i wpływ, jaki do wykonania. Definiowanie Konwencji, pozwalają na nadzorowanie kosztów i zarządzania zasobami. Na przykład można określić, że dozwolone są tylko niektóre typy maszyn wirtualnych. Alternatywnie można wymagać, że wszystkie zasoby mają określony tag. Zasady są dziedziczone przez wszystkie zasoby podrzędne. Tak Jeśli zasady są stosowane do grupy zasobów, ma zastosowanie do wszystkich zasobów w danej grupie zasobów.

Schemat używany przez usługę Azure Policy można znaleźć tutaj: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

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

## <a name="mode"></a>Tryb

**Tryb** określa typów zasobów, które zostanie obliczone dla zasad. Obsługiwane metody to:

- `all`: oceny grupy zasobów i wszystkie typy zasobów
- `indexed`: tylko ocenić typy zasobów, które obsługuje tagi i lokalizacji

Firma Microsoft zaleca, aby ustawić **tryb** do `all` w większości przypadków. Wszystkie definicje zasad, została utworzona za pośrednictwem portalu użycia `all` trybu. Jeśli używasz programu PowerShell lub wiersza polecenia platformy Azure, możesz określić **tryb** parametru ręcznie. Jeśli nie zawiera definicji zasad **tryb** wartość jego wartość domyślna to `all` w programie Azure PowerShell i do `null` w interfejsie wiersza polecenia platformy Azure, który jest odpowiednikiem `indexed`, dla wstecznej zgodności.

`indexed` należy używać podczas tworzenia zasad, który będzie wymuszać tagów lub lokalizacji. Nie jest to wymagane, ale uniemożliwi zasoby, które nie obsługują tagów i lokalizacji wyświetlane jako niezgodne w wyniki sprawdzania zgodności. Jedynym wyjątkiem jest **grup zasobów**. Należy ustawić zasady, które próbujesz wymuszanie lokalizacji lub tagów w grupie zasobów **tryb** do `all` i docelowy specjalnie `Microsoft.Resources/subscriptions/resourceGroup` typu. Aby uzyskać przykład, zobacz [wymusić tagi z grupy zasobów](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>Parametry

Parametry ułatwiają zarządzanie zasadami dzięki zmniejszeniu liczby definicji zasad. Reakcji parametrów, takich jak pola w formularzu — `name`, `address`, `city`, `state`. Te parametry pozostają niezmienione, jednak ich wartości zmieniają poszczególnych wypełniania formularza.
Parametry działają tak samo, podczas tworzenia zasad. Jeśli dołączysz parametrów w definicji zasad, można ponownie użyć tej zasady dla różnych scenariuszy przy użyciu innej wartości.

> [!NOTE]
> Definicji parametrów zasad lub definicji inicjatywy można skonfigurować tylko podczas początkowego tworzenia zasad lub inicjatywy. Nie można później zmienić definicji parametrów.
> Zapobiega to pośrednio odbywa się nieprawidłowe istniejące przypisania zasad lub inicjatywy.

Na przykład można zdefiniować zasady dla właściwości zasobów, ograniczyć lokalizacje, w której można wdrożyć zasoby. W takim przypadku może zadeklarować następujące parametry podczas tworzenia zasad:

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

Podczas tworzenia definicji inicjatywy lub zasad, ważne jest, aby określić lokalizację definicji.

Lokalizacja definicji określa zakres, do której można przypisać do definicji inicjatywy lub zasad. Lokalizację można określić jako grupę zarządzania lub subskrypcji.

> [!NOTE]
> Jeśli planowane jest zastosowanie tę definicję zasad do wielu subskrypcji, lokalizacji musi być zawierającą subskrypcje, które spowoduje przypisanie inicjatywy lub zasad grupy zarządzania.

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
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
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

Korzystając z **takich jak** i **notLike** warunków, można podać symbol wieloznaczny `*` wartości.
Wartość nie może zawierać więcej niż jeden symbol wieloznaczny `*`.

Korzystając z **dopasowania** i **notMatch** warunki `#` do reprezentowania cyfrę, `?` litery i znak reprezentuje rzeczywisty znaku. Aby uzyskać przykłady, zobacz [zezwolić wielu wzorców nazwy](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Pola

Warunki są tworzone przy użyciu pól. Pole reprezentuje właściwości ładunku żądania zasobów, który jest używany do opisu stan zasobu.

Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu. Pełna nazwa zasobu jest nazwa zasobu, dołączony przez wszystkie nazwy zasobu nadrzędnego (na przykład "myServer/Moja_baza_danych").
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - Gdzie **\<tagName\>** jest nazwa tagu do sprawdzania warunku.
  - Przykład: `tags.CostCenter` gdzie **CostCenter** jest nazwa tagu.
- `tags[<tagName>]`
  - Ta składnia nawiasu obsługuje nazwy tagów, które zawierać kropek.
  - Gdzie **\<tagName\>** jest nazwa tagu do sprawdzania warunku.
  - Przykład: `tags[Acct.CostCenter]` gdzie **Acct.CostCenter** jest nazwa tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

### <a name="effect"></a>Efekt

Zasady obsługuje następujące typy wpływu:

- **Odmów**: generuje zdarzenie w dzienniku inspekcji i kończy się niepowodzeniem żądania
- **Inspekcja**: generuje to zdarzenie ostrzegawcze w dzienniku inspekcji, ale nie wystąpi niepowodzenie żądania
- **Dołącz**: dodaje zestaw zdefiniowanych pól do żądania
- **AuditIfNotExists**: umożliwia inspekcję, jeśli zasób nie istnieje.
- **DeployIfNotExists**: wdraża zasobu, jeśli jeszcze nie istnieje.

Aby uzyskać **Dołącz**, należy podać następujące informacje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Wartość może być ciąg lub obiekt do formatu JSON.

Za pomocą **AuditIfNotExists** i **DeployIfNotExists** można ocenić istnienie powiązanego zasobu i Zastosuj regułę i odpowiedni wpływ, gdy ten zasób nie istnieje. Na przykład można wymagać, że usługi network watcher jest wdrażana dla wszystkich sieci wirtualnych. Aby uzyskać przykład inspekcję, gdy rozszerzenie maszyny wirtualnej nie została wdrożona, zobacz [inspekcji, jeśli rozszerzenie nie istnieje](../samples/audit-ext-not-exist.md).

Aby uzyskać szczegółowe informacje dotyczące każdego skutku, kolejność oceny, właściwości i przykłady, zobacz [zrozumienie zasad efekty](effects.md).

### <a name="policy-functions"></a>Funkcje zasad

Podzbiór [funkcje szablonu usługi Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) są dostępne do użycia w ramach reguły zasad. Są obecnie obsługiwane funkcje:

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [Subskrypcja](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Ponadto `field` funkcja jest dostępna z regułami zasad. Ta funkcja jest głównie do użytku z **AuditIfNotExists** i **DeployIfNotExists** do pola odniesienia dla zasobu, która jest szacowana. Przykładem tego są widoczne na [przykład DeployIfNotExists](effects.md#deployifnotexists-example).

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

Aliasy właściwości umożliwia dostęp do właściwości specyficzne dla typu zasobu. Aliasy pozwalają ograniczyć, jakie wartości lub warunków, które są dozwolone dla właściwości do zasobu. Każdy alias mapuje do ścieżki w różnych wersjach interfejsu API dla danego typu zasobu. Podczas oceny zasad aparatu zasad pobiera ścieżkę właściwości dla danej wersji interfejsu API.

Zawsze rośnie listę aliasów. Aby dowiedzieć się, jakie aliasy są obecnie obsługiwane przez usługę Azure Policy, użyj jednej z następujących metod:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
    'Authorization'='Bearer ' + $token.AccessToken
  }

  # Create a splatting variable for Invoke-RestMethod
  $invokeRest = @{
    Uri = 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases'
    Method = 'Get'
    ContentType = 'application/json'
    Headers = $authHeader
  }

  # Invoke the REST API
  $response = Invoke-RestMethod @invokeRest

  # Create an List to hold discovered aliases
  $aliases = [System.Collections.Generic.List[pscustomobject]]::new()

  foreach ($ns in $response.value)
  {
      foreach ($rT in $ns.resourceTypes)
      {
          if ($rT.aliases)
          {
              foreach ($obj in $rT.aliases)
              {
                  $alias = [PSCustomObject]@{
                      Namespace    = $ns.namespace
                      resourceType = $rT.resourceType
                      alias        = $obj.name
                  }
                  $aliases.Add($alias)
              }
          }
      }
  }

  # Output the list and sort it by Namespace, resourceType and alias. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias
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

Przyjrzyjmy się reguły zasad jako przykład. Te zasady będą **Odmów** konta magazynu, który ma skonfigurowane ipRules i, jeśli **Brak** z ipRules miała wartość "127.0.0.1".

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

Jako wartość false, warunek **Odmów** efekt nie zostanie wywołany.

## <a name="initiatives"></a>Inicjatywy

Inicjatywy pozwalają grupować kilka definicji zasad powiązane w celu uproszczenia przypisań i zarządzania, ponieważ współdziała z grupą jako pojedynczy element. Na przykład można grupować wszystkie powiązane definicje zasad tagowania w jednym inicjatywy. Zamiast przypisywać każdej z zasad indywidualnie, mają zastosowanie tej inicjatywy.

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
- Odkryj jak [korygowanie niezgodnych zasobów](../how-to/remediate-resources.md)
- Sprawdzanie, co to jest grupa zarządzania, na stronie [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure)](../../management-groups/overview.md)