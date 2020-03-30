---
title: Funkcje planu azure
description: W tym artykule opisano funkcje dostępne do użycia z artefaktami planu w definicjach i przydziałach planów platformy Azure.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280679"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funkcje do użytku z planami platformy Azure

Plany platformy Azure udostępnia funkcje, dzięki czemu definicja planu jest bardziej dynamiczna. Te funkcje są używane z definicjami planu i artefaktów planu. Artefakt szablonu Menedżera zasobów obsługuje pełne wykorzystanie funkcji Menedżera zasobów oprócz uzyskiwania wartości dynamicznej za pomocą parametru planu.

Obsługiwane są następujące funkcje:

- [Artefakty](#artifacts)
- [Concat](#concat)
- [Parametry](#parameters)
- [grupa zasobów](#resourcegroup)
- [zasobyGrupy](#resourcegroups)
- [Subskrypcji](#subscription)

## <a name="artifacts"></a>Artefakty

`artifacts(artifactName)`

Zwraca obiekt właściwości wypełnionych tym schematem artefaktów danych wyjściowych.

> [!NOTE]
> Funkcji `artifacts()` nie można używać z wewnątrz szablonu Menedżera zasobów. Funkcja może być używana tylko w definicji planu JSON lub w artefakt JSON podczas zarządzania planem za pomocą interfejsu API programu Azure PowerShell lub REST jako część [planów jako kodu.](https://github.com/Azure/azure-blueprints/blob/master/README.md)

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| nazwa artefaktu |Tak |ciąg |Nazwa artefaktu planu. |

### <a name="return-value"></a>Wartość zwracana

Obiekt właściwości wyjściowych. Właściwości **danych wyjściowych** są zależne od typu artefaktu planu, do którego się odwołuje. Wszystkie typy są zgodne z formatem:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefakt przypisania zasad

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefakt szablonu Menedżera zasobów

Właściwości **danych wyjściowych** zwróconego obiektu są definiowane w szablonie Menedżera zasobów i zwracane przez wdrożenie.

#### <a name="role-assignment-artifact"></a>Artefakt przypisania roli

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

Artefakt szablonu Menedżera zasobów z _identyfikatorem myTemplateArtifact_ zawierający następującą przykładową właściwość wyjściową:

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

Oto kilka przykładów pobierania danych z przykładu _myTemplateArtifact:_

| Wyrażenie | Typ | Wartość |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Tablica | \["pierwszy", "drugi"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Ciąg | "pierwszy" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Ciąg | "wartość mojego ciągu" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Obiekt | { "myproperty": "moja wartość", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Ciąg | "Moja wartość" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Wartość logiczna | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Łączy wiele wartości ciągów i zwraca ciąg łączony.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| ciąg 1 |Tak |ciąg |Pierwsza wartość łączenia. |
| dodatkowe argumenty |Nie |ciąg |Dodatkowe wartości w kolejności sekwencyjnej dla łączenia |

### <a name="return-value"></a>Wartość zwracana

Ciąg łączonych wartości.

### <a name="remarks"></a>Uwagi

Usługa Azure Blueprint funkcja różni się od funkcji szablonu usługi Azure Resource Manager, ponieważ działa tylko z ciągami.

### <a name="example"></a>Przykład

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Zwraca wartość parametru planu. Określona nazwa parametru musi być zdefiniowana w definicji planu lub w artefaktach planu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Parametername |Tak |ciąg |Nazwa parametru do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonego planu lub parametru artefaktu planu.

### <a name="remarks"></a>Uwagi

Usługa Azure Blueprint funkcja różni się od funkcji szablonu usługi Azure Resource Manager, ponieważ działa tylko z parametrami planu.

### <a name="example"></a>Przykład

Zdefiniuj _jednostki parametrówW_ definicji planu:

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

Następnie użyj _principalIds_ jako `parameters()` argument w artefakt planu:

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

Zwraca obiekt reprezentujący bieżącą grupę zasobów.

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Uwagi

Usługa Azure Blueprint, funkcja różni się od funkcji szablonu usługi Azure Resource Manager. Funkcji `resourceGroup()` nie można używać w artefakt poziomie subskrypcji lub definicji planu. Może być używany tylko w artefaktach planu, które są częścią artefaktu grupy zasobów.

Typowym zastosowaniem `resourceGroup()` funkcji jest tworzenie zasobów w tej samej lokalizacji co artefakt grupy zasobów.

### <a name="example"></a>Przykład

Aby użyć lokalizacji grupy zasobów, ustaw w definicji planu lub podczas przydziału, jako lokalizację innego artefaktu, zadeklaruj obiekt zastępczy grupy zasobów w definicji planu. W tym _przykładzie NetworkingPlaceholder_ jest nazwą symbolu zastępczego grupy zasobów.

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

Następnie użyj `resourceGroup()` funkcji w kontekście artefaktu planu, który jest ukierunkowany na obiekt zastępczy grupy zasobów. W tym przykładzie artefakt szablonu jest wdrażany w grupie zasobów _NetworkingPlaceholder_ i udostępnia _zasoby parametrówLokalizacja_ dynamicznie wypełniona lokalizacją grupy zasobów _NetworkingPlaceholder_ do szablonu. Lokalizacja grupy zasobów _NetworkingPlaceholder_ mogła być statycznie zdefiniowana w definicji planu lub zdefiniowana dynamicznie podczas przypisywania. W obu przypadkach artefakt szablonu jest pod warunkiem, że informacje jako parametr i używa go do wdrożenia zasobów do poprawnej lokalizacji.

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

## <a name="resourcegroups"></a>zasobyGrupy

`resourceGroups(placeholderName)`

Zwraca obiekt reprezentujący określony artefakt grupy zasobów. W `resourceGroup()`przeciwieństwie do , który wymaga kontekstu artefaktu, ta funkcja jest używana do uzyskania właściwości symbolu zastępczego określonej grupy zasobów, gdy nie w kontekście tej grupy zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| symbol zastępczyName |Tak |ciąg |Nazwa zastępcza artefaktu grupy zasobów do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Przykład

Aby użyć lokalizacji grupy zasobów, ustaw w definicji planu lub podczas przydziału, jako lokalizację innego artefaktu, zadeklaruj obiekt zastępczy grupy zasobów w definicji planu. W tym _przykładzie NetworkingPlaceholder_ jest nazwą symbolu zastępczego grupy zasobów.

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

Następnie użyj `resourceGroups()` funkcji z kontekstu dowolnego artefaktu planu, aby uzyskać odwołanie do obiektu zastępczego grupy zasobów. W tym przykładzie artefakt szablonu jest wdrażany poza grupą zasobów _NetworkingPlaceholder_ i udostępnia _artefakt parametruLokalizacja_ dynamicznie wypełniona lokalizacją grupy zasobów _NetworkingPlaceholder_ do szablonu. Lokalizacja grupy zasobów _NetworkingPlaceholder_ mogła być statycznie zdefiniowana w definicji planu lub zdefiniowana dynamicznie podczas przypisywania. W obu przypadkach artefakt szablonu jest pod warunkiem, że informacje jako parametr i używa go do wdrożenia zasobów do poprawnej lokalizacji.

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

## <a name="subscription"></a>subskrypcja

`subscription()`

Zwraca szczegółowe informacje o subskrypcji dla bieżącego przypisania planu.

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

Użyj nazwy wyświetlanej subskrypcji `concat()` i funkcji, aby utworzyć konwencję nazewnictwa przekazaną jako _parametr resourceName_ do artefaktu szablonu.

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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [cyklu życia planu](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).