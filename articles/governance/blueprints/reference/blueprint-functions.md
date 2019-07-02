---
title: Usługa Azure functions plany
description: Opisuje funkcje do użycia z usługą Azure schematy definicji i przypisań.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209401"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funkcje do użycia z usługą Azure plany

Plany usługi Azure udostępnia funkcje Tworzenie definicji planu bardziej dynamiczne. Te funkcje są przeznaczone do użytku z definicjami planu i artefakty planu. Artefakt szablonu usługi Resource Manager obsługuje pełne wykorzystanie funkcji Menedżera zasobów, oprócz wprowadzenie wartość dynamiczna za pomocą parametru planu.

Obsługiwane są następujące funkcje:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Zwraca obiekt właściwości wypełniane przy użyciu tego artefakty planu danych wyjściowych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| artifactName |Tak |string |Nazwa artefaktu planu. |

### <a name="return-value"></a>Wartość zwracana

Obiekt właściwości danych wyjściowych. **Generuje** właściwości są zależne od typu artefaktu planu, do którego nastąpiło odwołanie. Wszystkie typy wykonaj format:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefaktu przypisania zasad

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefakt szablonu usługi Resource Manager

**Generuje** właściwości zwracanego obiektu są zdefiniowane w szablonie usługi Resource Manager i zwrócony przez wdrożenie.

#### <a name="role-assignment-artifact"></a>Artefaktu przypisania roli

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Przykład

Artefakt szablonu usługi Resource Manager, z Identyfikatorem _myTemplateArtifact_ zawierający następujące przykładowe dane wyjściowe właściwości:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Niektóre przykłady pobierania danych z _myTemplateArtifact_ próbki są:

| Wyrażenie | Typ | Wartość |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "sekundę"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "Mój wartość ciągu" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"myproperty": "Moja value", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "Mój wartość" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Łączy wiele wartości parametrów i zwraca połączony ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| string1 |Tak |string |Pierwsza wartość łączenia. |
| dodatkowe argumenty |Nie |string |Dodatkowe wartości w kolejności sekwencyjnej dla łączenia |

### <a name="return-value"></a>Wartość zwracana

Ciąg wartości łączonych.

### <a name="remarks"></a>Uwagi

Funkcja Azure planu różni się od funkcji szablonu usługi Azure Resource Manager, w tym działa tylko z ciągami.

### <a name="example"></a>Przykład

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Zwraca wartość parametru planu. Określona nazwa parametru musi być zdefiniowany w definicji planu lub artefakty planu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| parameterName |Tak |string |Nazwa parametru do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonego planu lub parametr artefaktu planu.

### <a name="remarks"></a>Uwagi

Funkcja Azure planu różni się od funkcji szablonu usługi Azure Resource Manager, w tym działa tylko z parametry planu.

### <a name="example"></a>Przykład

Zdefiniuj parametr _principalIds_ w definicji planu:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Następnie użyj _principalIds_ jako argument dla `parameters()` w artefaktu planu:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Zwraca obiekt, który reprezentuje bieżącą grupę zasobów.

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Uwagi

Funkcja Azure planu różni się od funkcji szablonu usługi Azure Resource Manager. `resourceGroup()` Artefaktu do poziomu subskrypcji lub definicji planu nie można użyć funkcji. Tylko można je w artefakty planu, będące częścią artefaktu grupy zasobów.

Typowym zastosowaniem `resourceGroup()` funkcja jest do tworzenia zasobów w tej samej lokalizacji co artefaktu grupy zasobów.

### <a name="example"></a>Przykład

Aby użyć lokalizacji grupy zasobów, należy ustawić w jednym definicji planu, lub podczas przypisywania jako lokalizacji dla innego artefaktu, należy zadeklarować obiektu zastępczego grupy zasobów w definicji planu. W tym przykładzie _NetworkingPlaceholder_ to nazwa symbolu zastępczego grupy zasobów.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Następnie użyj `resourceGroup()` funkcję w kontekście artefaktu planu, który jest przeznaczony dla obiektu zastępczego grupy zasobów. W tym przykładzie artefaktu szablonu jest wdrażana w _NetworkingPlaceholder_ grupy zasobów i dostarcza parametr _resourceLocation_ dynamicznie wypełniony  _NetworkingPlaceholder_ lokalizację grupy zasobów do szablonu. Lokalizacja _NetworkingPlaceholder_ grupy zasobów może mieć statycznie zdefiniowanych w definicji planu lub definiowane dynamicznie podczas przypisywania. W obu przypadkach artefaktu szablonu podano te informacje jako parametr i używa go do wdrażania zasobów na poprawną lokalizację.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Zwraca obiekt, który reprezentuje artefaktu grupę określonego zasobu. W odróżnieniu od `resourceGroup()`, który wymaga kontekstu artefaktu, ta funkcja jest używana do pobrania właściwości symbolu zastępczego grupę określonego zasobu nie w kontekście danej grupie zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| placeholderName |Tak |string |Nazwa symbolu zastępczego artefaktu grupy zasobów do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Przykład

Aby użyć lokalizacji grupy zasobów, należy ustawić w jednym definicji planu, lub podczas przypisywania jako lokalizacji dla innego artefaktu, należy zadeklarować obiektu zastępczego grupy zasobów w definicji planu. W tym przykładzie _NetworkingPlaceholder_ to nazwa symbolu zastępczego grupy zasobów.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Następnie użyj `resourceGroups()` funkcji w kontekście dowolnego artefaktu planu, aby pobrać odwołanie do obiektu zastępczego grupy zasobów. W tym przykładzie wdrożono artefaktu szablonu poza _NetworkingPlaceholder_ grupy zasobów i dostarcza parametr _artifactLocation_ dynamicznie wypełniony  _NetworkingPlaceholder_ lokalizację grupy zasobów do szablonu. Lokalizacja _NetworkingPlaceholder_ grupy zasobów może mieć statycznie zdefiniowanych w definicji planu lub definiowane dynamicznie podczas przypisywania. W obu przypadkach artefaktu szablonu podano te informacje jako parametr i używa go do wdrażania zasobów na poprawną lokalizację.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

Zwraca szczegółowe informacje o subskrypcji dla bieżącego przydziału planu.

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Przykład

Użyj nazwę wyświetlaną tej subskrypcji i `concat()` funkcja służąca do tworzenia konwencji nazewnictwa przekazany jako parametr _resourceName_ do artefaktu szablonu.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).