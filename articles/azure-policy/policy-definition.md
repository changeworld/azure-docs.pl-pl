---
title: Struktura definicji zasad platformy Azure
description: Opisuje, jak zasobów definicji zasad jest używane przez zasady usługi Azure ustanowienie konwencje dla zasobów w organizacji przez opisujące, gdy zasady są wymuszane i wpływ, jaki do wykonania.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-policy
ms.custom: ''
ms.openlocfilehash: 3750bc409753868566c91c01cf6093f439c599f9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji zasad platformy Azure

Definicja zasad zasobu używanego przez zasady usługi Azure umożliwia ustalenie Konwencji zasobów w organizacji przez opisujące, gdy zasady są wymuszane i wpływ, jaki można wykonać. Definiowanie Konwencji, można kontrolować koszty i zarządzania zasobami. Na przykład można określić, czy dozwolone są tylko niektóre typy maszyn wirtualnych. Alternatywnie można wymagać, że wszystkie zasoby mają określony tag. Zasady są dziedziczone przez wszystkie zasoby podrzędne. Tak Jeśli zasady są stosowane do grupy zasobów, ma zastosowanie do wszystkich zasobów w danej grupie zasobów.

Schemat używany przez zasady usługi Azure można znaleźć tutaj: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

JSON służy do tworzenia definicji zasad. Definicja zasad zawiera elementy dla:

- mode
- parameters
- Nazwa wyświetlana
- description
- Reguła zasad
  - Ocena logiczne
  - Efekt

Na przykład następujące JSON zawiera zasadę, która ogranicza wdrożonym zasobów:

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

Wszystkie próbki szablonu zasad Azure są [szablony zasad Azure](json-samples.md).

## <a name="mode"></a>Tryb

**Tryb** Określa, jakie typy zasobów, które zostanie obliczone dla zasad. Obsługiwane tryby to:

- `all`: ocena grup zasobów i wszystkie typy zasobów
- `indexed`: tylko ocenić typy zasobów, które obsługują tagów i lokalizacja

Firma Microsoft zaleca, aby ustawić **tryb** do `all` w większości przypadków. Wszystkie definicje zasady utworzone za pomocą portalu `all` tryb. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia Azure, można określić **tryb** parametru ręcznie. Jeśli nie zawiera definicji zasad **tryb** wartość jego wartość domyślna to `all` w programie Azure PowerShell i do `null` w wiersza polecenia platformy Azure, który jest odpowiednikiem `indexed`, dla wstecz zgodności.

`indexed` powinien być używany podczas tworzenia zasad, który będzie wymuszać znaczników lub lokalizacji. Nie jest to wymagane, ale uniemożliwi zasoby, które nie obsługują znaczniki i lokalizacje wyświetlane jako niezgodne w wyniki oceny zgodności. Jedynym wyjątkiem jest **grup zasobów**. Należy ustawić zasady, które próbują wymusić lokalizacji lub tagi dla grupy zasobów **tryb** do `all` , a w szczególności docelowej `Microsoft.Resources/subscriptions/resourceGroup` typu. Na przykład zobacz [wymusić tagi grupy zasobów](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>Parametry

Parametry ułatwić zarządzanie zasadami dzięki zmniejszeniu liczby definicje zasad. Pomyśl o parametry, takie jak pól na formularzu — `name`, `address`, `city`, `state`. Te parametry zawsze znajdują się takie same, jednak zmienić ich wartości opartych na poszczególnych wypełniania formularza. Parametry działają tak samo, podczas tworzenia zasad. Przy tym parametrów w definicji zasad, można ponownie użyć tej zasady dla różnych scenariuszy przy użyciu innej wartości.

Na przykład można zdefiniować zasady dla właściwości zasobów ograniczyć lokalizacje wdrożonym zasobów. W takim przypadku może zadeklarować następujące parametry podczas tworzenia zasad:

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

Typ parametru może być ciągiem lub tablicą. Właściwość metadanych służy do narzędzi, takich jak portalu Azure do wyświetlania informacji przyjazną dla użytkownika.

W ramach właściwości metadanych można użyć **strongType** zapewnienie wielokrotnego wyboru listy opcji w portalu Azure.  Dozwolone wartości **strongType** obecnie obejmują:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

W regule zasad możesz odwołania do parametrów przy użyciu następującej składni:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Lokalizacja definicji

Podczas tworzenia zasady lub inicjatywy definicji, ważne jest, aby określić lokalizację definicji.

Lokalizacja definicji określa zakres, któremu inicjatywy lub zasady definicji można przypisać do. Lokalizację można określić jako grupę zarządzania lub subskrypcji.

> [!NOTE]
> Jeśli planujesz dotyczą tej definicji zasad wiele subskrypcji, lokalizacja musi być zawierającą subskrypcje, które będą przypisywane inicjatywy lub zasady grupy zarządzania.

## <a name="display-name-and-description"></a>Nazwę wyświetlaną i opis

Można użyć **displayName** i **opis** do identyfikowania definicji zasad i podanie gdy jest używana w kontekście.

## <a name="policy-rule"></a>Reguła zasad

Reguły składa się z **Jeśli** i **następnie** bloków. W **Jeśli** bloku, należy zdefiniować co najmniej jeden warunek, które określają, gdy zasady są wymuszane. Operatory logiczne można zastosować do tych warunków, aby precyzyjnie zdefiniować scenariusz dla zasad.

W **następnie** bloku, zdefiniuj efekt, która jest wywoływana po **Jeśli** warunki są spełnione.

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

Operatory logiczne obsługiwane są:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**Nie** składni odwraca wynik w warunku. **Wszystkie** składni (podobny do logicznego **i** operacji) wymaga wszystkie warunki, które muszą być spełnione. **AnyOf** składni (podobny do logicznego **lub** operacji) wymaga co najmniej jeden warunków.

Operatory logiczne można zagnieżdżać. W poniższym przykładzie przedstawiono **nie** operacja, która jest zagnieżdżona w **wszystkie** operacji.

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

Wynikiem warunku jest czy **pola** spełnia określone kryteria. Są obsługiwane warunki:

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

Korzystając z **jak** i **notLike** warunki, można podać symbol wieloznaczny (*) w wartości.

Korzystając z **odpowiada** i **notMatch** warunki `#` do reprezentowania cyfrę, `?` literę i znak reprezentują rzeczywiste znaku. Aby uzyskać przykłady, zobacz [Zezwalaj wielu wzorce nazw](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Pola

Warunki są utworzone za pomocą pola. Pole reprezentuje właściwości w ładunku żądania zasobu, który jest używany do opisu stanu typu zasobu.  

Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu, włącznie z elementów nadrzędnych (na przykład "MójSerwer/mojabazadanych")
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - Ta składnia nawiasu obsługuje nazwy tagu, zawierające kropki
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

### <a name="alternative-accessors"></a>Alternatywne metody dostępu

**Pole** jest akcesor głównej, używane w regułach zasad. Sprawdza on bezpośrednio zasób, który jest oceniane. Jednak zasady obsługuje jeden innych akcesor **źródła**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Źródło** obsługuje tylko jedną wartość, **akcji**. Akcja zwraca działanie autoryzacji żądania, które jest oceniane. Akcje autoryzacji są widoczne w sekcji autoryzacji [dziennik aktywności](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Gdy zasad dokonuje oceny istniejących zasobów w tle, ustawia **akcji** do `/write` akcji autoryzacji dla typu zasobu.

### <a name="effect"></a>Efekt

Zasady obsługuje następujące typy wpływu:

- **Odmów**: generuje zdarzenie w dzienniku inspekcji i niepowodzenia żądania
- **Inspekcja**: generuje to zdarzenie ostrzegawcze w dzienniku inspekcji, ale nie wystąpi niepowodzenie żądania
- **Dołącz**: dodaje zestaw określonych pól na żądanie
- **AuditIfNotExists**: włącza inspekcji, jeśli zasób nie istnieje.
- **DeployIfNotExists**: wdraża zasobu, jeśli jeszcze nie istnieje. Obecnie ten efekt jest obsługiwana tylko przy użyciu wbudowanych zasad.

Aby uzyskać **Dołącz**, należy podać następujące informacje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Wartość może być ciągiem lub obiekt do formatu JSON.

Z **AuditIfNotExists** i **DeployIfNotExists** można ocenić istnienie powiązanych zasobów i Zastosuj regułę i ich wpływ, gdy ten zasób nie istnieje. Można na przykład wymagać, że dla wszystkich sieci wirtualnych jest wdrażany obserwatora sieciowego.
Przykład inspekcję, gdy rozszerzenie maszyny wirtualnej nie została wdrożona, zobacz [inspekcji, jeśli rozszerzenie nie istnieje](scripts/audit-ext-not-exist.md).

## <a name="aliases"></a>Aliasy

Aliasy właściwości umożliwia dostęp do właściwości specyficzne dla typu zasobu. Aliasy pozwalają ograniczyć, jakie wartości lub warunki są dozwolone dla właściwości w zasobie. Każdy alias mapuje ścieżek w różnych wersjach interfejsu API dla typu zasobu. Podczas oceny zasad aparat zasad pobiera ścieżki właściwości dla tej wersji interfejsu API.

**Microsoft.Cache/Redis**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Ustaw czy bez użycia protokołu ssl serwera Redis portu (6379) jest włączona. |
| Microsoft.Cache/Redis/shardCount | Ustaw liczbę fragmentów do utworzenia na pamięć podręczna klastra Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Ustaw rozmiar pamięci podręcznej Redis do wdrożenia.  |
| Microsoft.Cache/Redis/sku.family | Ustaw rodziny SKU do użycia. |
| Microsoft.Cache/Redis/sku.name | Ustaw typ pamięci podręcznej Redis do wdrożenia. |

**Microsoft.Cdn/profiles**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Ustaw nazwę warstwy cenowej. |

**Microsoft.Compute/disks**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Ustaw oferta obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imagePublisher | Ustaw wydawcy obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imageSku | Jednostka SKU obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej zestawu. |
| Microsoft.Compute/imageVersion | Ustaw wersję obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |


**Microsoft.Compute/virtualMachines**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Compute/imageId | Ustaw identyfikator obraz używany do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imageOffer | Ustaw oferta obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imagePublisher | Ustaw wydawcy obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imageSku | Jednostka SKU obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej zestawu. |
| Microsoft.Compute/imageVersion | Ustaw wersję obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/licenseType | Ustaw, że obraz lub dysk jest licencjonowane lokalnymi. Ta wartość jest używana tylko dla obrazów systemu operacyjnego Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Ustaw oferta obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/virtualMachines/imagePublisher | Ustaw wydawcy obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/virtualMachines/imageSku | Jednostka SKU obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej zestawu. |
| Microsoft.Compute/virtualMachines/imageVersion | Ustaw wersję obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Ustaw identyfikator URI dysku vhd. |
| Microsoft.Compute/virtualMachines/sku.name | Ustaw rozmiar maszyny wirtualnej. |
| Microsoft.Compute/virtualMachines/availabilitySet.id | Ustawia identyfikator dla maszyny wirtualnej zestawu dostępności. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Ustaw nazwę wydawcy rozszerzenia. |
| Microsoft.Compute/virtualMachines/extensions/type | Ustaw typ rozszerzenia. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Ustaw wersję rozszerzenia. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Compute/imageId | Ustaw identyfikator obraz używany do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imageOffer | Ustaw oferta obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imagePublisher | Ustaw wydawcy obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/imageSku | Jednostka SKU obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej zestawu. |
| Microsoft.Compute/imageVersion | Ustaw wersję obrazu platformy lub obrazu z witryny marketplace użyty do utworzenia maszyny wirtualnej. |
| Microsoft.Compute/licenseType | Ustaw, że obraz lub dysk jest licencjonowane lokalnymi. Ta wartość jest używana tylko dla obrazów systemu operacyjnego Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Ustaw prefiks nazwy komputera dla wszystkich maszyn wirtualnych w zestawie skalowania. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Ustaw identyfikator URI obiektu blob obrazu użytkownika. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Ustawianie adresów URL kontenera, które są używane do przechowywania dysków systemu operacyjnego dla zestawu skalowania. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Ustaw rozmiar maszyn wirtualnych w zestawie skalowania. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Ustawienie warstwy maszyn wirtualnych w zestawie skalowania. |

**Microsoft.Network/applicationGateways**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Ustawianie rozmiaru bramy. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Ustaw typ tej bramy sieci wirtualnej. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Ustaw nazwę jednostki SKU bramy. |

**Microsoft.Sql/servers**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Ustaw wersję serwera. |

**Microsoft.Sql/databases**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Ustaw wersję bazy danych. |
| Microsoft.Sql/servers/databases/elasticPoolName | Zestaw nazwę puli elastycznej bazy danych jest. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Ustawienia poziomu identyfikatorów celu skonfigurowanej usługi bazy danych. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Ustaw nazwę celu poziomu usługi skonfigurowane bazy danych.  |

**Microsoft.Sql/elasticpools**

| Alias | Opis |
| ----- | ----------- |
| serwery/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Wartość całkowita udostępnionych jednostek DTU dla puli elastycznej bazy danych. |
| serwery/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Ustaw edition puli elastycznej. |

**Microsoft.Storage/storageAccounts**

| Alias | Opis |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Ustawianie warstwy dostępu używany na potrzeby rozliczeń. |
| Microsoft.Storage/storageAccounts/accountType | Nazwa jednostki SKU zestawu. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Określ, czy usługa szyfruje dane, jak są przechowywane w usłudze magazyn obiektów blob. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Określ, czy usługa szyfruje dane, jak są przechowywane w usłudze magazyn plików. |
| Microsoft.Storage/storageAccounts/sku.name | Nazwa jednostki SKU zestawu. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Ustaw, aby zezwolić tylko na ruch protokołu https do usługi Magazyn. |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | Sprawdź, czy punkt końcowy usługi sieci wirtualnej jest włączone. |

## <a name="initiatives"></a>Inicjatywy

Włącz inicjatyw kilka grupy powiązane definicje zasad, aby uprościć zadania i zarządzania, ponieważ współdziała z grupą jako pojedynczy element. Na przykład można grupować wszystkie powiązane definicje zasad znakowania w jednym inicjatywy. Zamiast przypisywać każdej zasady pojedynczo, należy zastosować tej inicjatywy.

Poniższy przykład przedstawia sposób tworzenia inicjatywą obsługi dwa tagi: `costCenter` i `productName`. Używa dwóch wbudowane zasady do zastosowania domyślną wartość tagu.

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

- Przejrzyj przykłady szablonu zasad Azure w [szablony zasad Azure](json-samples.md).