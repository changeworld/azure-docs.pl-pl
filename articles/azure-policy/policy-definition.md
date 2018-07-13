---
title: Struktura definicji zasad platformy Azure
description: W tym artykule opisano, jak zasobu definicji zasad jest używany przez usługę Azure Policy do ustanawiania konwencje dla zasobów w Twojej organizacji, poprzez opisanie, gdy zasady są wymuszane i wpływ, jaki do wykonania.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 7f01464c4b9063f20a83c3626d7f92a5e0524f7a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989129"
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

Wszystkie przykłady usługi Azure Policy znajdują się na [Przykłady zasad](json-samples.md).

## <a name="mode"></a>Tryb

**Tryb** określa typów zasobów, które zostanie obliczone dla zasad. Obsługiwane metody to:

- `all`: oceny grupy zasobów i wszystkie typy zasobów
- `indexed`: tylko ocenić typy zasobów, które obsługuje tagi i lokalizacji

Firma Microsoft zaleca, aby ustawić **tryb** do `all` w większości przypadków. Wszystkie definicje zasad, została utworzona za pośrednictwem portalu użycia `all` trybu. Jeśli używasz programu PowerShell lub wiersza polecenia platformy Azure, możesz określić **tryb** parametru ręcznie. Jeśli nie zawiera definicji zasad **tryb** wartość jego wartość domyślna to `all` w programie Azure PowerShell i do `null` w interfejsie wiersza polecenia platformy Azure, który jest odpowiednikiem `indexed`, dla wstecznej zgodności.

`indexed` należy używać podczas tworzenia zasad, który będzie wymuszać tagów lub lokalizacji. Nie jest to wymagane, ale uniemożliwi zasoby, które nie obsługują tagów i lokalizacji wyświetlane jako niezgodne w wyniki sprawdzania zgodności. Jedynym wyjątkiem jest **grup zasobów**. Należy ustawić zasady, które próbujesz wymuszanie lokalizacji lub tagów w grupie zasobów **tryb** do `all` i docelowy specjalnie `Microsoft.Resources/subscriptions/resourceGroup` typu. Aby uzyskać przykład, zobacz [wymusić tagi z grupy zasobów](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>Parametry

Parametry ułatwiają zarządzanie zasadami dzięki zmniejszeniu liczby definicji zasad. Reakcji parametrów, takich jak pola w formularzu — `name`, `address`, `city`, `state`. Te parametry pozostają niezmienione, jednak ich wartości zmieniają poszczególnych wypełniania formularza. Parametry działają tak samo, podczas tworzenia zasad. Jeśli dołączysz parametrów w definicji zasad, można ponownie użyć tej zasady dla różnych scenariuszy przy użyciu innej wartości.

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

W ramach właściwości metadanych, możesz użyć **strongType** zapewnienie wielokrotnego wyboru listę opcji w witrynie Azure portal.  Dozwolone wartości **strongType** obejmują:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

W regule zasad możesz odwoływać się do parametrów za pomocą następującej składni:

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

Korzystając z **dopasowania** i **notMatch** warunki `#` do reprezentowania cyfrę, `?` litery i znak reprezentuje rzeczywisty znaku. Aby uzyskać przykłady, zobacz [zezwolić wielu wzorców nazwy](scripts/allow-multiple-name-patterns.md).

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
  - Przykład: `tags.[Acct.CostCenter]` gdzie **Acct.CostCenter** jest nazwa tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

### <a name="alternative-accessors"></a>Alternatywne metody dostępu

**Pole** jest podstawowym metody dostępu używane w regułach zasad. Go bezpośrednio sprawdza zasób, który jest szacowana. Jednak zasady obsługują jedną inne metody dostępu **źródła**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Źródło** obsługuje tylko jedną wartość, **akcji**. Akcja zwraca działanie autoryzacji żądania, które jest oceniane. Działania autoryzacji są widoczne w sekcji autoryzacji [dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

W przypadku zasad dokonuje oceny istniejących zasobów w tle, ustawia **akcji** do `/write` działanie autoryzacji dla typu zasobu.

### <a name="effect"></a>Efekt

Zasady obsługuje następujące typy wpływu:

- **Odmów**: generuje zdarzenie w dzienniku inspekcji i kończy się niepowodzeniem żądania
- **Inspekcja**: generuje to zdarzenie ostrzegawcze w dzienniku inspekcji, ale nie wystąpi niepowodzenie żądania
- **Dołącz**: dodaje zestaw zdefiniowanych pól do żądania
- **AuditIfNotExists**: umożliwia inspekcję, jeśli zasób nie istnieje.
- **DeployIfNotExists**: wdraża zasobu, jeśli jeszcze nie istnieje. Ten efekt jest obecnie obsługiwane tylko za pomocą wbudowanych zasad.

Aby uzyskać **Dołącz**, należy podać następujące informacje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Wartość może być ciąg lub obiekt do formatu JSON.

Za pomocą **AuditIfNotExists** i **DeployIfNotExists** można ocenić istnienie powiązanego zasobu i Zastosuj regułę i odpowiedni wpływ, gdy ten zasób nie istnieje. Na przykład można wymagać, że usługi network watcher jest wdrażana dla wszystkich sieci wirtualnych.
Aby uzyskać przykład inspekcję, gdy rozszerzenie maszyny wirtualnej nie została wdrożona, zobacz [inspekcji, jeśli rozszerzenie nie istnieje](scripts/audit-ext-not-exist.md).

Aby uzyskać szczegółowe informacje dotyczące każdego skutku, kolejność oceny, właściwości i przykłady, zobacz [zrozumienie zasad efekty](policy-effects.md).

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
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- Interfejs API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

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

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](json-samples.md).
