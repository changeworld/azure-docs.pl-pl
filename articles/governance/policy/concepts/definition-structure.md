---
title: Szczegóły struktury definicji zasad
description: Opisuje, w jaki sposób definicja zasad zasobów jest używana przez Azure Policy do ustanawiania konwencji dotyczących zasobów w organizacji, opisując, kiedy zasady są wymuszane i jakie skutki mają być wykonywane.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 27cf1539fc98b2ad7f1b82e194989c1619ab99fb
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980708"
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji Azure Policy

Definicje zasad zasobów są używane przez Azure Policy do ustanawiania konwencji dotyczących zasobów. Każda definicja zawiera opis zgodności zasobów i działania, które należy wykonać, gdy zasób nie jest zgodny.
Dzięki zdefiniowaniu Konwencji można kontrolować koszty i łatwiej zarządzać zasobami. Można na przykład określić, że dozwolone są tylko niektóre typy maszyn wirtualnych. Lub można wymagać, aby wszystkie zasoby miały określony tag. Zasady są dziedziczone przez wszystkie zasoby podrzędne. Jeśli zasady są stosowane do grupy zasobów, mają zastosowanie do wszystkich zasobów w tej grupie zasobów.

Schemat definicji zasad znajduje się tutaj: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Aby utworzyć definicję zasad, należy użyć formatu JSON. Definicja zasad zawiera elementy dla:

- tryb
- parametry
- Nazwa wyświetlana
- opis
- Reguła zasad
  - Ocena logiczna
  - skuteczność

Na przykład poniższy kod JSON przedstawia zasady, które ograniczają miejsce wdrożenia zasobów:

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

Wszystkie przykłady Azure Policy znajdują się na [Azure Policy próbkach](../samples/index.md).

## <a name="mode"></a>Tryb

**Tryb** jest skonfigurowany w zależności od tego, czy zasady są ukierunkowane na Właściwość Azure Resource Manager lub Właściwość dostawcy zasobów.

### <a name="resource-manager-modes"></a>Tryby Menedżer zasobów

**Tryb** określa, które typy zasobów będą oceniane dla zasad. Obsługiwane są następujące tryby:

- `all`: Oceń grupy zasobów i wszystkie typy zasobów
- `indexed`: Oceń tylko typy zasobów obsługujące Tagi i lokalizację

Zaleca się, aby w większości przypadków ustawić **tryb** na `all`. Wszystkie definicje zasad utworzone za pomocą portalu używają trybu `all`. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz określić parametr **mode** ręcznie. Jeśli definicja zasad nie zawiera wartości **trybu** , domyślnie `all` w Azure PowerShell i do `null` w interfejsie wiersza polecenia platformy Azure. Tryb `null` jest taki sam jak w przypadku używania `indexed` w celu zapewnienia zgodności z poprzednimi wersjami.

`indexed` należy używać podczas tworzenia zasad, które wymuszają Tagi lub lokalizacje. Chociaż nie jest to wymagane, uniemożliwiają one nie obsługujące tagów i lokalizacji, ponieważ nie są one zgodne z wynikami sprawdzania zgodności. Wyjątkiem są **grupy zasobów**. Zasady, które wymuszają lokalizację lub Tagi w grupie zasobów, powinny ustawiać **tryb** na `all`, a w odniesieniu do typu `Microsoft.Resources/subscriptions/resourceGroups`. Aby zapoznać się z przykładem, zobacz [Wymuszaj Tagi grupy zasobów](../samples/enforce-tag-rg.md). Aby uzyskać listę zasobów, które obsługują Tagi, zobacz [obsługa tagów dla zasobów platformy Azure](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Tryby dostawcy zasobów

Jedynym obsługiwanym trybem dostawcy zasobów jest `Microsoft.ContainerService.Data` w celu zarządzania regułami kontrolera [usługi Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy Kubernetes](rego-for-aks.md) jest w publicznej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad.

## <a name="parameters"></a>Parametry

Parametry pomagają uprościć zarządzanie zasadami przez zmniejszenie liczby definicji zasad. Należy traktować parametry, takie jak pola w formularzu — `name`, `address`, `city`, `state`. Te parametry zawsze pozostają takie same, ale ich wartości zmieniają się w zależności od poszczególnych wypełniania formularza.
Parametry działają w ten sam sposób podczas kompilowania zasad. Dzięki dołączeniu parametrów w definicji zasad można ponownie użyć tych zasad dla różnych scenariuszy przy użyciu różnych wartości.

> [!NOTE]
> Parametry można dodawać do istniejącej i przypisanej definicji. Nowy parametr musi zawierać właściwość **DefaultValue** . Zapobiega to poprawnej nieprawidłowemu przypisywaniu zasad lub inicjatywy.

### <a name="parameter-properties"></a>Właściwości parametru

Parametr ma następujące właściwości, które są używane w definicji zasad:

- **name**: Nazwa parametru. Używane przez funkcję wdrożenia `parameters` w ramach reguły zasad. Aby uzyskać więcej informacji, zobacz [Używanie wartości parametru](#using-a-parameter-value).
- `type`: określa, czy parametr to **ciąg**, **Tablica**, **obiekt**, **wartość logiczna**, **Liczba całkowita**, **zmiennoprzecinkowa**lub **DateTime**.
- `metadata`: definiuje podwłaściwości używane głównie przez Azure Portal do wyświetlania informacji przyjaznych dla użytkownika:
  - `description`: wyjaśnienie, w jaki sposób jest używany parametr. Może służyć do podania przykładów akceptowalnych wartości.
  - `displayName`: przyjazna nazwa wyświetlana w portalu dla parametru.
  - `strongType`: (opcjonalnie) używany podczas przypisywania definicji zasad za pomocą portalu. Zawiera listę kontekstową. Aby uzyskać więcej informacji, zobacz [strongtype](#strongtype).
  - `assignPermissions`: (opcjonalnie) ustawiono jako _true_ , aby mieć Azure Portal tworzenia przypisań ról podczas przypisywania zasad. Ta właściwość jest przydatna w przypadku, gdy chcesz przypisać uprawnienia poza zakresem przypisania. Istnieje jedno przypisanie roli w ramach zasad (lub definicji roli we wszystkich zasadach z inicjatywy). Wartość parametru musi być prawidłowym zasobem lub zakresem.
- `defaultValue`: (opcjonalnie) ustawia wartość parametru w przypisaniu, jeśli nie podano wartości.
  Wymagane podczas aktualizowania istniejącej definicji zasad, która jest przypisana.
- `allowedValues`: (opcjonalnie) zawiera tablicę wartości akceptowanych przez parametr podczas przypisywania.

Można na przykład zdefiniować definicję zasad, aby ograniczyć lokalizacje, w których można wdrożyć zasoby. Parametr dla tej definicji zasad może być **allowedLocations**. Ten parametr będzie używany przez każde przypisanie definicji zasad w celu ograniczenia akceptowanych wartości. Użycie **silnego** typu zapewnia ulepszone środowisko podczas kończenia przydziału w portalu:

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

### <a name="using-a-parameter-value"></a>Używanie wartości parametru

W regule zasad można odwoływać się do parametrów przy użyciu następującej składni funkcji wartości wdrożenia `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ten przykład odwołuje się do parametru **allowedLocations** , który został pokazany we [właściwościach parametrów](#parameter-properties).

### <a name="strongtype"></a>strongtype

We właściwości `metadata` można użyć **silnego** elementu, aby udostępnić listę opcji dostępnych w ramach Azure Portal. Dozwolone wartości dla **silnego elementu strongtype** są obecnie:

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

Podczas tworzenia inicjatywy lub zasad należy określić lokalizację definicji. Lokalizacja definicji musi być grupą zarządzania lub subskrypcją. Ta lokalizacja określa zakres, do którego można przypisać inicjatywę lub zasady. Zasoby muszą być bezpośrednimi elementami członkowskimi lub elementami podrzędnymi w hierarchii lokalizacji definicji do przypisywania.

Jeśli lokalizacja definicji jest:

- Tylko zasoby z **subskrypcją** w ramach tej subskrypcji mogą być przypisane do zasad.
- Zasady mogą być przypisywane tylko zasobom należącym do **grupy** zarządzania w ramach podrzędnych grup administracyjnych i subskrypcji podrzędnych. Jeśli planujesz zastosowanie definicji zasad do kilku subskrypcji, lokalizacja musi być grupą zarządzania, która zawiera te subskrypcje.

## <a name="display-name-and-description"></a>Nazwa wyświetlana i opis

Użyj **DisplayName** i **Description** , aby zidentyfikować definicję zasad i podać kontekst, który ma być używany. **Nazwa wyświetlana** ma maksymalną długość _128_ znaków i **Opis** ma maksymalną długość _512_ znaków.

## <a name="policy-rule"></a>Reguła zasad

Reguła zasad składa się z elementów **if** i **then** . W bloku **if** definiuje się jeden lub więcej warunków, które określają, kiedy zasady są wymuszane. Operatory logiczne można stosować do tych warunków, aby precyzyjnie zdefiniować scenariusz dla zasad.

W bloku **then** definiujesz efekt, **który ma miejsce, gdy warunki są** spełnione.

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

**Nie** składnia odwraca wynik warunku. Składnia **allOf** (podobna do operacji logicznej **and** ) wymaga, aby wszystkie warunki były prawdziwe. Składnia **anyOf** (podobna do operacji logicznej **or** ) wymaga co najmniej jednego warunku.

Operatory logiczne można zagnieżdżać. W poniższym przykładzie przedstawiono operację **not** , która jest zagnieżdżona w ramach operacji **allOf** .

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

Warunek oblicza, czy **pole** lub metoda dostępu do **wartości** spełniają określone kryteria. Obsługiwane są następujące warunki:

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

W przypadku używania warunków **like** i **notLike** , w wartości należy podać symbol wieloznaczny `*`.
Wartość nie może mieć więcej niż jednego symbolu wieloznacznego `*`.

W przypadku używania warunków **Match** i **notMatch** podaj wartość `#` w celu dopasowania do cyfry, `?` dla litery, `.`, aby dopasować dowolny znak, i dowolny inny znak, aby dopasować go do rzeczywistego znaku.
**dopasowanie** i **notMatch** uwzględnia wielkość liter. Alternatywy bez uwzględniania wielkości liter są dostępne w **matchInsensitively** i **notMatchInsensitively**. Aby zapoznać się z przykładami, zobacz [Zezwalanie na kilka wzorców nazw](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Pola

Warunki są tworzone za pomocą pól. Pole jest zgodne z właściwościami w ładunku żądania zasobu i opisuje stan zasobu.

Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu. Pełna nazwa zasobu to nazwa zasobu poprzedzona przez wszystkie nadrzędne nazwy zasobów (na przykład "nie dotyczy".
- `kind`
- `type`
- `location`
  - Użyj **globalnych** dla zasobów, które są lokalizacją niezależny od. Aby zapoznać się z przykładem, zobacz sekcję [dozwolone lokalizacje](../samples/allowed-locations.md).
- `identity.type`
  - Zwraca typ [zarządzanej tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md) włączonej dla zasobu.
- `tags`
- `tags['<tagName>']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które mają znaki interpunkcyjne, takie jak łącznik, kropka lub spacja.
  - Gdzie **\<tagName @ no__t-2** to nazwa tagu, dla którego ma zostać zweryfikowany warunek.
  - Przykłady: `tags['Acct.CostCenter']` gdzie **ACCT. CostCenter** jest nazwą tagu.
- `tags['''<tagName>''']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które zawierają apostrofy przez ucieczki z podwójnym apostrofem.
  - Gdzie **"\<tagName @ no__t-2"** to nazwa tagu, dla którego ma zostać zweryfikowany warunek.
  - Przykład: `tags['''My.Apostrophe.Tag''']` gdzie **"\<tagName @ no__t-3"** jest nazwą tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` i `tags[tag.with.dots]` są nadal akceptowalnymi sposobami deklarowania pola tag. Jednak preferowane wyrażenia są wymienione powyżej.

#### <a name="use-tags-with-parameters"></a>Używanie tagów z parametrami

Wartość parametru może być przekazanie do pola tagu. Przekazywanie parametru do pola tagu zwiększa elastyczność definicji zasad podczas przypisywania zasad.

W poniższym przykładzie `concat` służy do tworzenia wyszukiwania pól tagów dla tagu o nazwie **TagName** parametru. Jeśli ten tag nie istnieje, efekt **dołączania** jest używany do dodawania znacznika przy użyciu wartości tego samego nazwanego tagu ustawionego w nadrzędnej grupie zasobów poddane inspekcji przy użyciu funkcji wyszukiwania `resourcegroup()`.

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

Warunki mogą być również tworzone przy użyciu **wartości**. **wartość** sprawdza warunki względem [parametrów](#parameters), [obsługiwanych funkcji szablonów](#policy-functions)lub literałów.
**wartość** jest sparowana z dowolnym obsługiwanym [warunkiem](#conditions).

> [!WARNING]
> Jeśli wynik _funkcji szablonu_ jest błąd, Ocena zasad kończy się niepowodzeniem. Niepowodzenie oceny to niejawne **odmowa**. Aby uzyskać więcej informacji, zobacz [unikanie niepowodzeń związanych z szablonami](#avoiding-template-failures).

#### <a name="value-examples"></a>Przykłady wartości

Ta reguła zasad używa tego przykładu **wartości** do porównania wyniku funkcji `resourceGroup()` i zwróconej właściwości **name** do warunku **przypominającego** `*netrg`. Reguła odmówi żadnego zasobu, który nie jest **typu** `Microsoft.Network/*` w żadnej grupie zasobów, której nazwa jest zakończona w `*netrg`.

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

Ta reguła zasad używa tego przykładu **wartości** do sprawdzenia, czy wynik wielu zagnieżdżonych funkcji **jest równy** `true`. Reguła odmówi wszelkich zasobów, które nie mają co najmniej trzech tagów.

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

#### <a name="avoiding-template-failures"></a>Unikanie niepowodzeń szablonów

Użycie _funkcji szablonu_ w **wartości** pozwala na wiele złożonych zagnieżdżonych funkcji. Jeśli wynik _funkcji szablonu_ jest błąd, Ocena zasad kończy się niepowodzeniem. Niepowodzenie oceny to niejawne **odmowa**. Przykład **wartości** , która kończy się niepowodzeniem w niektórych scenariuszach:

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

Powyższa Przykładowa reguła zasad używa [podciągu ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) , aby porównać pierwsze trzy znaki **nazwy** z **ABC**. Jeśli **Nazwa** jest krótsza niż trzy znaki, funkcja `substring()` spowoduje wystąpienie błędu. Ten błąd powoduje, że zasady stają się efektem **odmowy** .

Zamiast tego należy użyć funkcji [if ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) , aby sprawdzić, czy pierwsze trzy znaki **nazwy** są równe **ABC** bez dopuszczania **nazwy** krótszej niż trzy znaki w celu spowodowania błędu:

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

Po zmodyfikowaniu zasady zasad `if()` sprawdza długość **nazwy** przed próbą uzyskania `substring()` na wartości mniejszej niż trzy znaki. Jeśli **Nazwa** jest za krótka, zamiast tego jest zwracana wartość "nie zaczyna się od ABC" i porównana z opcją **ABC**. Zasób z krótką nazwą, która nie zaczyna się od **ABC** , nadal kończy się niepowodzeniem reguły zasad, ale nie powoduje już błędu podczas obliczania.

### <a name="effect"></a>Efekt

Azure Policy obsługuje następujące typy efektów:

- **Odmów**: generuje zdarzenie w dzienniku aktywności i kończy się niepowodzeniem żądania
- **Inspekcja**: generuje zdarzenie ostrzegawcze w dzienniku aktywności, ale nie kończy się niepowodzeniem żądania
- **Append**: Dodaje zdefiniowany zestaw pól do żądania
- **AuditIfNotExists**: włącza inspekcję, jeśli zasób nie istnieje
- **DeployIfNotExists**: służy do wdrażania zasobu, jeśli jeszcze nie istnieje
- **Wyłączone**: nie oblicza zasobów pod kątem zgodności z regułą zasad
- **EnforceRegoPolicy**: konfiguruje kontroler "Open Policy Agent Admission Control" w usłudze Azure Kubernetes Service (wersja zapoznawcza)
- **Modyfikowanie**: dodaje, aktualizuje lub usuwa zdefiniowane znaczniki z zasobu

W przypadku **dołączania**należy podać następujące informacje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Wartość może być albo ciągiem lub obiektem formatu JSON.

**AuditIfNotExists** i **DeployIfNotExists** ocenia istnienia powiązanego zasobu i zastosowania reguły. Jeśli zasób nie jest zgodny z regułą, efekt jest zaimplementowany. Można na przykład wymagać, aby obserwator sieci został wdrożony dla wszystkich sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [Inspekcja, jeśli rozszerzenie nie istnieje](../samples/audit-ext-not-exist.md) .

Efekt **DeployIfNotExists** wymaga, aby właściwość **zduplikowanych** była częścią **szczegółów** reguły zasad. Aby uzyskać więcej informacji, zobacz [korygowanie — Konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Podobnie **Modyfikacja** wymaga właściwości **zduplikowanych** w części **szczegółów** reguły zasad dla [zadania korygowania](../how-to/remediate-resources.md). **Modyfikacja** wymaga również tablicy **operacji** , aby zdefiniować akcje do wykonania względem tagów zasobów.

Aby uzyskać szczegółowe informacje na temat każdego efektu, kolejności oceny, właściwości i przykładów, zobacz [opis efektów Azure Policy](effects.md).

### <a name="policy-functions"></a>Funkcje zasad

Wszystkie [funkcje szablonu Menedżer zasobów](../../../azure-resource-manager/resource-group-template-functions.md) są dostępne do użycia w regule zasad, z wyjątkiem następujących funkcji i funkcji zdefiniowanych przez użytkownika:

- Funkcji copyindex ()
- wdrożenie ()
- staw
- newGuid()
- pickZones()
- dostawcy ()
- Reference ()
- resourceId ()
- zmienne ()

Następujące funkcje są dostępne do użycia w regule zasad, ale różnią się od użycia w szablonie Azure Resource Manager:

- AddDays (dateTime, numberOfDaysToAdd)
  - **DateTime**: [Required] ciąg ciągu w formacie daty/godziny uniwersalnego ISO 8601 "RRRR-MM-DDTgg: mm: SS. fffffffZ"
  - **numberOfDaysToAdd**: [Required] liczba dni do dodania
- utcNow () — w przeciwieństwie do szablonu Menedżer zasobów, można go użyć poza elementem DefaultValue.
  - Zwraca ciąg, który jest ustawiony na bieżącą datę i godzinę w formacie uniwersalnego ISO 8601 DateTime-MM-DDTgg: mm: SS. fffffffZ

Ponadto funkcja `field` jest dostępna dla reguł zasad. `field` jest używany głównie z **AuditIfNotExists** i **DeployIfNotExists** do pól referencyjnych na analizowanym zasobie. Przykład tego zastosowania można zobaczyć w [przykładzie DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Przykład funkcji zasad

W tym przykładzie reguła zasad używa funkcji zasobów `resourceGroup` w celu uzyskania właściwości **name** połączonej z `concat` tablicą i funkcją obiektu w celu utworzenia warunku `like`, który wymusza nazwę zasobu, rozpoczynając od nazwy grupy zasobów.

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

Aliasy właściwości są używane do uzyskiwania dostępu do określonych właściwości dla typu zasobu. Aliasy umożliwiają ograniczenie wartości lub warunków dozwolonych dla właściwości zasobu. Każdy alias mapuje do ścieżek w różnych wersjach interfejsu API dla danego typu zasobu. Podczas obliczania zasad aparat zasad Pobiera ścieżkę właściwości dla tej wersji interfejsu API.

Lista aliasów zawsze rośnie. Aby dowiedzieć się, jakie aliasy są obecnie obsługiwane przez Azure Policy, należy użyć jednej z następujących metod:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- Interfejs API REST/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informacje o aliasie [*]

Kilka dostępnych aliasów ma wersję, która jest wyświetlana jako nazwa "normal" (normalna), a druga, która ma do niej dołączony **[\*]** . Na przykład:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias "normal" reprezentuje pole jako pojedynczą wartość. To pole jest przeznaczone do dokładnego dopasowania scenariuszy porównywania, gdy cały zestaw wartości musi być dokładnie zdefiniowany, nie więcej i nie rzadziej.

Alias **[\*]** umożliwia porównanie wartości poszczególnych elementów w tablicy i konkretnych właściwości każdego elementu. Takie podejście umożliwia porównanie właściwości elementów dla elementu "If None of", "if any" lub "If all of". Przy użyciu **ipRules [\*]** , przykładem będzie sprawdzanie, czy każda _Akcja_ jest _odrzucana_, ale nie martw się o liczbę istniejących reguł lub _wartość_ IP. Ta przykładowa reguła sprawdza, czy istnieją dopasowań **ipRules [\*]. Value** do **10.0.4.1** i stosuje element **effecttype** tylko wtedy, gdy nie znajdzie co najmniej jednego dopasowania:

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

Aby uzyskać więcej informacji, zobacz [ocenianie aliasu [\*]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Inicjatyw

Inicjatywy umożliwiają grupowanie kilku powiązanych definicji zasad w celu uproszczenia przypisań i zarządzania, ponieważ pracujesz z grupą jako pojedynczy element. Na przykład można pogrupować powiązane definicje zasad oznakowania do jednej inicjatywy. Zamiast przypisywać poszczególne zasady indywidualnie, należy zastosować inicjatywę.

Poniższy przykład ilustruje sposób tworzenia inicjatywy do obsługi dwóch tagów: `costCenter` i `productName`. Używa dwóch wbudowanych zasad, aby zastosować domyślną wartość tagu.

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

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [Informacje o skutkach zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/getting-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).