---
title: Funkcje planów platformy Azure
description: Zawiera opis funkcji dostępnych do użycia z artefaktami strategii w definicjach i przypisaniach platformy Azure.
ms.date: 04/15/2019
ms.topic: reference
ms.openlocfilehash: 92539da02ddbe22f943454aff54dae4ccb5af3ce
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128756"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funkcje do użycia z planami platformy Azure

Plany platformy Azure udostępniają funkcje, które ułatwiają tworzenie definicji planu. Te funkcje są przeznaczone do użycia z definicjami planów i artefaktami strategii. Artefakt szablonu Menedżer zasobów obsługuje pełne korzystanie z funkcji Menedżer zasobów oprócz pobierania wartości dynamicznej za pomocą parametru strategii.

Obsługiwane są następujące funkcje:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [Subskrypcja](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Zwraca obiekt właściwości wypełniony przez dane wyjściowe artefaktów planu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| artefaktname |Yes |ciąg |Nazwa artefaktu planu. |

### <a name="return-value"></a>Wartość zwracana

Obiekt właściwości danych wyjściowych. Właściwości danych **wyjściowych** są zależne od typu artefaktu strategii, do którego się odwołuje. Wszystkie typy są zgodne z formatem:

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

#### <a name="resource-manager-template-artifact"></a>Menedżer zasobów artefaktu szablonu

Właściwości danych **wyjściowych** zwróconego obiektu są zdefiniowane w szablonie Menedżer zasobów i zwracane przez wdrożenie.

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

Artefakt szablonu Menedżer zasobów z IDENTYFIKATORem _myTemplateArtifact_ zawierający następującą przykładową Właściwość wyjściową:

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

Przykłady pobierania danych z przykładu _myTemplateArtifact_ są następujące:

| Wyrażenie | Typ | Wartość |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Tablica | \["pierwszy", "s"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Ciąg | pierwszego |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Ciąg | "wartość my ciągu" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Obiekt | {"noproperty": "My Value", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Ciąg | "Moja wartość" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | Prawda |

## <a name="concat"></a>Concat

`concat(string1, string2, string3, ...)`

Łączy wiele wartości ciągu i zwraca ciąg połączony.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| string1 |Yes |ciąg |Pierwsza wartość dla łączenia. |
| dodatkowe argumenty |Nie |ciąg |Dodatkowe wartości w kolejności sekwencyjnej dla łączenia |

### <a name="return-value"></a>Wartość zwracana

Ciąg łączonych wartości.

### <a name="remarks"></a>Uwagi

Funkcja Azure Blueprint różni się od funkcji szablonu Azure Resource Manager, która działa tylko z ciągami.

### <a name="example"></a>Przykład

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Zwraca wartość parametru planu. Określona nazwa parametru musi być zdefiniowana w definicji strategii lub w artefaktach strategii.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |ciąg |Nazwa parametru do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonego parametru strategii lub artefaktu strategii.

### <a name="remarks"></a>Uwagi

Funkcja Azure Blueprint różni się od funkcji szablonu Azure Resource Manager, która działa tylko z parametrami strategii.

### <a name="example"></a>Przykład

Zdefiniuj parametr _principalIds_ w definicji strategii:

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

Następnie użyj _principalIds_ jako argumentu dla `parameters()` w artefaktie planu:

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

Funkcja Azure Blueprint różni się od funkcji szablonu Azure Resource Manager. Funkcji `resourceGroup()` nie można używać w artefaktie poziomu subskrypcji ani w definicji strategii. Może być używany tylko w artefaktach planu, które są częścią artefaktu grupy zasobów.

Typowym zastosowaniem funkcji `resourceGroup()` jest tworzenie zasobów w tej samej lokalizacji co artefakt grupy zasobów.

### <a name="example"></a>Przykład

Aby użyć lokalizacji grupy zasobów, ustawić w definicji planu lub podczas przypisywania, jako lokalizację innego artefaktu Zadeklaruj Obiekt zastępczy grupy zasobów w definicji strategii. W tym przykładzie _NetworkingPlaceholder_ jest nazwą symbolu zastępczego grupy zasobów.

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

Następnie użyj funkcji `resourceGroup()` w kontekście artefaktu planu, który jest przeznaczony dla obiektu zastępczego grupy zasobów. W tym przykładzie artefakt szablonu jest wdrażany w grupie zasobów _NetworkingPlaceholder_ i udostępnia parametr _resourceLocation_ , który jest dynamicznie wypełniany przy użyciu lokalizacji grupy zasobów _NetworkingPlaceholder_ do szablonu. Lokalizacja grupy zasobów _NetworkingPlaceholder_ mogła być zdefiniowana statycznie w definicji strategii lub dynamicznie zdefiniowana podczas przypisywania. W obu przypadkach artefakt szablonu jest dostarczany jako parametr i używa go do wdrożenia zasobów we właściwym miejscu.

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

Zwraca obiekt, który reprezentuje określony artefakt grupy zasobów. W przeciwieństwie do `resourceGroup()`, które wymaga kontekstu artefaktu, ta funkcja jest używana do pobierania właściwości określonego symbolu zastępczego grupy zasobów, gdy nie znajduje się w kontekście tej grupy zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Symbol zastępczy |Yes |ciąg |Nazwa symbolu zastępczego artefaktu grupy zasobów do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Przykład

Aby użyć lokalizacji grupy zasobów, ustawić w definicji planu lub podczas przypisywania, jako lokalizację innego artefaktu Zadeklaruj Obiekt zastępczy grupy zasobów w definicji strategii. W tym przykładzie _NetworkingPlaceholder_ jest nazwą symbolu zastępczego grupy zasobów.

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

Następnie użyj funkcji `resourceGroups()` z kontekstu dowolnego artefaktu strategii, aby uzyskać odwołanie do obiektu zastępczego grupy zasobów. W tym przykładzie artefakt szablonu jest wdrażany poza grupą zasobów _NetworkingPlaceholder_ i dostarcza parametr _artifactLocation_ , który jest dynamicznie wypełniany przy użyciu lokalizacji grupy zasobów _NetworkingPlaceholder_ do szablonu. Lokalizacja grupy zasobów _NetworkingPlaceholder_ mogła być zdefiniowana statycznie w definicji strategii lub dynamicznie zdefiniowana podczas przypisywania. W obu przypadkach artefakt szablonu jest dostarczany jako parametr i używa go do wdrożenia zasobów we właściwym miejscu.

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

## <a name="subscription"></a>subskrypcję

`subscription()`

Zwraca szczegóły dotyczące subskrypcji bieżącego przypisania planu.

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

Użyj nazwy wyświetlanej subskrypcji i funkcji `concat()`, aby utworzyć konwencję nazewnictwa przekazaną jako parametr _resourceName_ szablonu do tego artefaktu.

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

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).