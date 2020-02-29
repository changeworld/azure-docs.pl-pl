---
title: Szczegóły struktury definicji zasad
description: Opisuje, w jaki sposób definicje zasad są używane do ustanawiania Konwencji dla zasobów platformy Azure w organizacji.
ms.date: 02/26/2020
ms.topic: conceptual
ms.openlocfilehash: 1100248b43dbdf668dc1164651f3d9f941f3f016
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920216"
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji zasad platformy Azure

Azure Policy ustanawia konwencje dla zasobów. Definicje zasad opisują [warunki](#conditions) zgodności zasobów i wpływ, jaki należy wykonać w przypadku spełnienia warunku. Warunek porównuje [pole](#fields) właściwości zasobu z wymaganą wartością. Pola właściwości zasobu są dostępne za pomocą [aliasów](#aliases). Pole właściwości zasobu to pole pojedynczej wartości lub [Tablica](#understanding-the--alias) wielu wartości. Ocena warunku różni się w przypadku tablic.
Dowiedz się więcej o [warunkach](#conditions).

Definiowanie Konwencji, pozwalają na nadzorowanie kosztów i zarządzania zasobami. Na przykład można określić, że dozwolone są tylko niektóre typy maszyn wirtualnych. Alternatywnie można wymagać, że wszystkie zasoby mają określony tag. Zasady są dziedziczone przez wszystkie zasoby podrzędne. Jeśli zasady są stosowane do grupy zasobów, ma zastosowanie do wszystkich zasobów w tej grupie zasobów.

Schemat definicji zasad znajduje się tutaj: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

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

Wszystkie przykłady Azure Policy znajdują się na [Azure Policy próbkach](../samples/index.md).

## <a name="mode"></a>Tryb

**Tryb** jest skonfigurowany w zależności od tego, czy zasady są ukierunkowane na Właściwość Azure Resource Manager lub Właściwość dostawcy zasobów.

### <a name="resource-manager-modes"></a>Tryby Menedżer zasobów

**Tryb** określa, które typy zasobów będą oceniane dla zasad. Obsługiwane metody to:

- `all`: Oceń grupy zasobów i wszystkie typy zasobów
- `indexed`: Oceń tylko typy zasobów obsługujące Tagi i lokalizację

Na przykład zasób `Microsoft.Network/routeTables` obsługuje tagi i lokalizację i jest oceniany w obu trybach. Nie można jednak oznaczyć zasobów `Microsoft.Network/routeTables/routes` nie są oceniane w trybie `Indexed`.

Zalecamy skonfigurowanie **trybu** do `all` w większości przypadków. Wszystkie definicje zasad utworzone za pomocą portalu używają trybu `all`. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz określić parametr **mode** ręcznie. Jeśli definicja zasad nie zawiera wartości **trybu** , domyślnie `all` w Azure PowerShell i `null` w interfejsie wiersza polecenia platformy Azure. Tryb `null` jest taki sam jak w przypadku używania `indexed` w celu zapewnienia zgodności z poprzednimi wersjami.

`indexed` należy używać podczas tworzenia zasad, które wymuszają Tagi lub lokalizacje. Chociaż nie jest to wymagane, uniemożliwiają one nie obsługujące tagów i lokalizacji, ponieważ nie są one zgodne z wynikami sprawdzania zgodności. Wyjątkiem są **grupy zasobów**. Zasady, które wymuszają lokalizację lub Tagi w grupie zasobów, powinny ustawiać **tryb** , aby `all`, a w odniesieniu do typu `Microsoft.Resources/subscriptions/resourceGroups`. Aby zapoznać się z przykładem, zobacz [Wymuszaj Tagi grupy zasobów](../samples/enforce-tag-rg.md). Aby uzyskać listę zasobów, które obsługują Tagi, zobacz [obsługa tagów dla zasobów platformy Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a>tryby dostawcy zasobów <a name="resource-provider-modes" />(wersja zapoznawcza)

Następujące tryby dostawcy zasobów są obecnie obsługiwane w wersji zapoznawczej:

- `Microsoft.ContainerService.Data` zarządzania regułami kontrolera przyjmowania w [usłudze Azure Kubernetes](../../../aks/intro-kubernetes.md). Zasady korzystające z tego trybu dostawcy zasobów **muszą** używać efektu [EnforceRegoPolicy](./effects.md#enforceregopolicy) .
- `Microsoft.Kubernetes.Data` zarządzania niezarządzanymi klastrami Kubernetes Engine AKS na platformie Azure.
  Zasady korzystające z tego trybu dostawcy zasobów **muszą** używać efektu [EnforceOPAConstraint](./effects.md#enforceopaconstraint) .
- `Microsoft.KeyVault.Data` zarządzania magazynami i certyfikatami w programie [Azure Key Vault](../../../key-vault/key-vault-overview.md).

> [!NOTE]
> Tryby dostawcy zasobów obsługują tylko wbudowane definicje zasad i nie obsługują inicjatyw w wersji zapoznawczej.

## <a name="parameters"></a>Parametry

Parametry ułatwiają zarządzanie zasadami dzięki zmniejszeniu liczby definicji zasad. Należy traktować parametry, takie jak pola w formularzu — `name`, `address`, `city``state`. Te parametry pozostają niezmienione, jednak ich wartości zmieniają poszczególnych wypełniania formularza.
Parametry działają tak samo, podczas tworzenia zasad. Jeśli dołączysz parametrów w definicji zasad, można ponownie użyć tej zasady dla różnych scenariuszy przy użyciu innej wartości.

> [!NOTE]
> Parametry można dodawać do istniejącej i przypisanej definicji. Nowy parametr musi zawierać właściwość **DefaultValue** . Zapobiega to pośrednio odbywa się nieprawidłowe istniejące przypisania zasad lub inicjatywy.

### <a name="parameter-properties"></a>Właściwości parametru

Parametr ma następujące właściwości, które są używane w definicji zasad:

- **name**: Nazwa parametru. Używane przez funkcję wdrażania `parameters` w ramach reguły zasad. Aby uzyskać więcej informacji, zobacz [Używanie wartości parametru](#using-a-parameter-value).
- `type`: określa, czy parametr to **ciąg**, **Tablica**, **obiekt**, **wartość logiczna**, **Liczba całkowita**, **zmiennoprzecinkowa**lub **DateTime**.
- `metadata`: definiuje podwłaściwości używane głównie przez Azure Portal do wyświetlania informacji przyjaznych dla użytkownika:
  - `description`: wyjaśnienie, w jaki sposób jest używany parametr. Może służyć do podania przykładów akceptowalnych wartości.
  - `displayName`: przyjazna nazwa wyświetlana w portalu dla parametru.
  - `version`: (opcjonalnie) śledzi szczegółowe informacje o wersji zawartości definicji zasad.

    > [!NOTE]
    > Usługa Azure Policy używa właściwości `version`, `preview`i `deprecated`, aby przekazywać poziom zmian do wbudowanej definicji lub inicjatywy i stanu. Format `version` to: `{Major}.{Minor}.{Patch}`. Określone Stany, takie jak _przestarzałe_ lub w _wersji zapoznawczej_, są dołączane do właściwości `version` lub w innej właściwości jako **wartość logiczna**.

  - `category`: (opcjonalnie) określa, w której kategorii Azure Portal zostanie wyświetlona definicja zasad.
  - `strongType`: (opcjonalnie) używany podczas przypisywania definicji zasad za pomocą portalu. Zawiera listę kontekstową. Aby uzyskać więcej informacji, zobacz [strongtype](#strongtype).
  - `assignPermissions`: (opcjonalnie) ustawiono _wartość true_ , aby mieć Azure Portal tworzenia przypisań ról podczas przypisywania zasad. Ta właściwość jest przydatna w przypadku, gdy chcesz przypisać uprawnienia poza zakresem przypisania. Istnieje jedno przypisanie roli w ramach zasad (lub definicji roli we wszystkich zasadach z inicjatywy). Wartość parametru musi być prawidłowym zasobem lub zakresem.
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

W regule zasad, należy odwołać się do parametrów za pomocą następującej składni funkcji `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ten przykład odwołuje się do parametru **allowedLocations** , który został pokazany we [właściwościach parametrów](#parameter-properties).

### <a name="strongtype"></a>strongtype

We właściwości `metadata` można użyć **silnego** elementu, aby udostępnić listę opcji dostępnych w ramach Azure Portal. **silntype** może być obsługiwanym _typem zasobu_ lub dozwoloną wartością. Aby określić, czy _Typ zasobu_ jest prawidłowy dla **silnego**elementu, użyj polecenie [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Niektóre _typy zasobów_ , które nie są zwracane przez **Get-AzResourceProvider** , są obsługiwane. Są to:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Dozwolone wartości _typu_ niezwiązanego z typem " **strongtype** " to:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Lokalizacja definicji

Podczas tworzenia inicjatywy lub zasad, należy określić lokalizację definicji. Lokalizacja definicji musi być grupą zarządzania lub subskrypcji. Ta lokalizacja określa zakres, do której można przypisać inicjatywy lub zasad. Zasoby muszą być bezpośredni członkowie lub elementy podrzędne w hierarchii Lokalizacja definicji docelową przypisania.

Jeśli lokalizacja definicji to:

- Tylko zasoby z **subskrypcją** w ramach tej subskrypcji mogą być przypisane do zasad.
- Zasady mogą być przypisywane tylko zasobom należącym do **grupy** zarządzania w ramach podrzędnych grup administracyjnych i subskrypcji podrzędnych. Jeśli planowane jest zastosowanie definicję zasad do wielu subskrypcji, lokalizacji musi być grupą zarządzania, który zawiera te subskrypcje.

## <a name="display-name-and-description"></a>Nazwę wyświetlaną i opis

Użyj **DisplayName** i **Description** , aby zidentyfikować definicję zasad i podać kontekst, który ma być używany. **Nazwa wyświetlana** ma maksymalną długość _128_ znaków i **Opis** ma maksymalną długość _512_ znaków.

> [!NOTE]
> Podczas tworzenia lub aktualizowania definicji zasad, **identyfikatora**, **typu**i **nazwy** są zdefiniowane przez właściwości zewnętrzne w formacie JSON i nie są wymagane w pliku JSON. Pobieranie definicji zasad za pomocą zestawu SDK zwraca **Identyfikator**, **Typ**i właściwości **nazwy** w ramach JSON, ale każda z nich jest informacjami tylko do odczytu związanymi z definicją zasad.

## <a name="policy-rule"></a>Reguła zasad

Reguła zasad składa się z elementów **if** i **then** . W bloku **if** definiuje się jeden lub więcej warunków, które określają, kiedy zasady są wymuszane. Operatory logiczne można zastosować do tych warunków, aby precyzyjnie zdefiniować scenariusz dla zasad.

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

Można zagnieżdżać operatorów logicznych. W poniższym przykładzie przedstawiono operację **not** , która jest zagnieżdżona w ramach operacji **allOf** .

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

Warunek oblicza, czy **pole** lub metoda dostępu do **wartości** spełniają określone kryteria. Obsługiwane warunki to:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

W przypadku używania warunków **like** i **notLike** , w wartości należy podać symbol wieloznaczny `*`.
Wartość nie może mieć więcej niż jednego `*`symbolu wieloznacznego.

W przypadku używania warunków **Match** i **notMatch** Podaj `#` w celu dopasowania do cyfry, `?` dla litery, `.` aby dopasować dowolny znak i dowolny inny znak, aby dopasować go do rzeczywistego znaku. While, **Match** i **notMatch** uwzględnia wielkość liter, a wszystkie inne warunki, które szacują _stringValue_ , nie uwzględniają wielkości liter. Alternatywy bez uwzględniania wielkości liter są dostępne w **matchInsensitively** i **notMatchInsensitively**.

W **\[\*\] alias** wartości pola tablicy, każdy element w tablicy jest obliczany pojedynczo za pomocą logicznych **i** między elementami. Aby uzyskać więcej informacji, zobacz [ocenę aliasu \] \*\[](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Pola

Warunki są tworzone przy użyciu pól. Pole jest zgodna właściwości w ładunku żądania zasobów i opisuje stan zasobu.

Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu. Pełna nazwa zasobu jest nazwa zasobu, dołączony przez wszystkie nazwy zasobu nadrzędnego (na przykład "myServer/Moja_baza_danych").
- `kind`
- `type`
- `location`
  - Użyj **globalnych** dla zasobów, które są lokalizacją niezależny od.
- `identity.type`
  - Zwraca typ [zarządzanej tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md) włączonej dla zasobu.
- `tags`
- `tags['<tagName>']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które mają znaki interpunkcyjne, takie jak łącznik, kropka lub spacja.
  - Gdzie **\<tagName\>** jest nazwą tagu, aby zweryfikować warunek.
  - Przykłady: `tags['Acct.CostCenter']`, gdzie **ACCT. CostCenter** jest nazwą tagu.
- `tags['''<tagName>''']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które zawierają apostrofy przez ucieczki z podwójnym apostrofem.
  - Gdzie **"\<tagName\>"** jest nazwą tagu, dla którego ma zostać zweryfikowany warunek.
  - Przykład: `tags['''My.Apostrophe.Tag''']`, gdzie **element "My. apostrof. tag"** jest nazwą tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`i `tags[tag.with.dots]` są nadal akceptowalnymi sposobami deklarowania pola Tagi. Jednak preferowane wyrażenia są wymienione powyżej.

#### <a name="use-tags-with-parameters"></a>Używanie tagów z parametrami

Wartość parametru może być przekazanie do pola tagu. Przekazywanie parametru do pola tagu zwiększa elastyczność definicji zasad podczas przypisywania zasad.

W poniższym przykładzie `concat` jest używany do tworzenia wyszukiwania pól tagów dla tagu o nazwie **TagName** parametru. Jeśli ten tag nie istnieje, efekt **modyfikacji** jest używany do dodawania znacznika przy użyciu wartości tego samego nazwanego tagu ustawionego w nadrzędnej grupie zasobów poddane inspekcji przy użyciu funkcji wyszukiwania `resourcegroup()`.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Wartość

Warunki mogą być również tworzone przy użyciu **wartości**. **wartość** sprawdza warunki względem [parametrów](#parameters), [obsługiwanych funkcji szablonów](#policy-functions)lub literałów.
**wartość** jest sparowana z dowolnym obsługiwanym [warunkiem](#conditions).

> [!WARNING]
> Jeśli wynik _funkcji szablonu_ jest błąd, Ocena zasad kończy się niepowodzeniem. Niepowodzenie oceny to niejawne **odmowa**. Aby uzyskać więcej informacji, zobacz [unikanie niepowodzeń związanych z szablonami](#avoiding-template-failures). Użyj [wymuszania](./assignment-structure.md#enforcement-mode) elementu **DoNotEnforce** , aby zapobiec wpływowi oceny zakończonej niepowodzeniem na nowe lub zaktualizowane zasoby podczas testowania i sprawdzania poprawności nowej definicji zasad.

#### <a name="value-examples"></a>Przykłady wartości

Ta reguła zasad używa tego przykładu **wartości** do porównania wyniku funkcji `resourceGroup()` i zwróconej właściwości **name** do warunku **przypominającego** `*netrg`. Zasada odmówi, że żaden zasób nie jest **typu** `Microsoft.Network/*` w żadnej grupie zasobów, której nazwa jest zakończona w `*netrg`.

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

Powyższa Przykładowa reguła zasad używa [podciągu ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) , aby porównać pierwsze trzy znaki **nazwy** z **ABC**. Jeśli **Nazwa** jest krótsza niż trzy znaki, funkcja `substring()` powoduje błąd. Ten błąd powoduje, że zasady stają się efektem **odmowy** .

Zamiast tego należy użyć funkcji [if ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) , aby sprawdzić, czy pierwsze trzy znaki **nazwy** są równe **ABC** bez dopuszczania **nazwy** krótszej niż trzy znaki w celu spowodowania błędu:

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

Po zmodyfikowaniu reguły zasad, `if()` sprawdza długość **nazwy** przed próbą uzyskania `substring()` na wartości mniejszej niż trzy znaki. Jeśli **Nazwa** jest za krótka, zamiast tego jest zwracana wartość "nie zaczyna się od ABC" i porównana z opcją **ABC**. Zasób z krótką nazwą, która nie zaczyna się od **ABC** , nadal kończy się niepowodzeniem reguły zasad, ale nie powoduje już błędu podczas obliczania.

### <a name="count"></a>Licznik

Warunki określające, ile elementów członkowskich tablicy w ładunku zasobów spełnia wyrażenie warunku, można utworzyć za pomocą wyrażenia **Count** . Typowe scenariusze sprawdzają, czy "co najmniej jeden z", "dokładnie jeden z", "All" lub "none" elementów członkowskich tablicy spełnia warunek. **licznik** oblicza każdy [\[\*\]](#understanding-the--alias) elementu członkowskiego tablicy aliasów dla wyrażenia warunku i sumuje _prawdziwe_ wyniki, które są następnie porównywane z operatorem wyrażenia.

Struktura wyrażenia **Count** jest:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Następujące właściwości są używane z funkcją **Count**:

- **Count. pole** (wymagane): zawiera ścieżkę do tablicy i musi być aliasem tablicy. Jeśli brakuje tablicy, wyrażenie jest oceniane na _wartość false_ bez uwzględniania wyrażenia warunku.
- **Count. WHERE** (opcjonalnie): wyrażenie warunku do poszczególnych ocen [\[\*\]](#understanding-the--alias) składowej tablicy aliasu **Count. Field**. Jeśli ta właściwość nie jest określona, wszystkie elementy członkowskie tablicy ze ścieżką "pole" są oceniane na _wartość true_. Dowolny [warunek](../concepts/definition-structure.md#conditions) może być używany wewnątrz tej właściwości.
  [Operatory logiczne](#logical-operators) mogą być używane wewnątrz tej właściwości, aby utworzyć złożone wymagania dotyczące oceny.
- **\>warunku\<** (wymagane): wartość jest porównywana z liczbą elementów, które osiągnęły liczbę. wyrażenie warunku **WHERE** . Należy użyć [warunku](../concepts/definition-structure.md#conditions) liczbowego.

#### <a name="count-examples"></a>Liczba przykładów

Przykład 1: sprawdzenie, czy tablica jest pusta

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Przykład 2: Sprawdź tylko jeden element członkowski tablicy, aby spełnić wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Przykład 3: Sprawdź co najmniej jeden element członkowski tablicy, aby spełnić wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Przykład 4: Sprawdź, czy wszystkie elementy członkowskie tablicy obiektów spełniają wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Przykład 5: Sprawdź, czy wszystkie elementy członkowskie tablicy ciągów spełniają wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Przykład 6: Użyj **pola** wewnątrz **wartości** , aby sprawdzić, czy wszystkie elementy członkowskie tablicy spełniają wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Przykład 7: Sprawdź, czy co najmniej jeden element członkowski tablicy jest zgodny z wieloma właściwościami w wyrażeniu warunku

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Efekt

Azure Policy obsługuje następujące typy efektów:

- **Append**: Dodaje zdefiniowany zestaw pól do żądania
- **Inspekcja**: generuje zdarzenie ostrzegawcze w dzienniku aktywności, ale nie kończy się niepowodzeniem żądania
- **AuditIfNotExists**: generuje zdarzenie ostrzegawcze w dzienniku aktywności, jeśli powiązany zasób nie istnieje
- **Odmów**: generuje zdarzenie w dzienniku aktywności i kończy się niepowodzeniem żądania
- **DeployIfNotExists**: wdraża powiązane zasoby, jeśli jeszcze nie istnieją
- **Wyłączone**: nie oblicza zasobów pod kątem zgodności z regułą zasad
- **EnforceOPAConstraint** (wersja zapoznawcza): konfiguruje kontroler "Open Policy Agent Admission Control" z strażnikiem v3 dla samozarządzanej klastrów Kubernetes na platformie Azure (wersja zapoznawcza)
- **EnforceRegoPolicy** (wersja zapoznawcza): konfiguruje kontroler "Open Policy Agent Admission Control" z strażnikiem v2 w usłudze Azure Kubernetes Service
- **Modyfikowanie**: dodaje, aktualizuje lub usuwa zdefiniowane znaczniki z zasobu

Aby uzyskać szczegółowe informacje na temat każdego efektu, kolejności oceny, właściwości i przykładów, zobacz [opis efektów Azure Policy](effects.md).

### <a name="policy-functions"></a>Funkcje zasad

Wszystkie [funkcje szablonu Menedżer zasobów](../../../azure-resource-manager/templates/template-functions.md) są dostępne do użycia w regule zasad, z wyjątkiem następujących funkcji i funkcji zdefiniowanych przez użytkownika:

- Funkcji copyindex ()
- wdrożenie ()
- staw
- newGuid()
- pickZones()
- dostawcy ()
- Reference ()
- resourceId()
- zmienne ()

Następujące funkcje są dostępne do użycia w regule zasad, ale różnią się od użycia w szablonie Azure Resource Manager:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [Required] ciąg ciągu w formacie daty/godziny uniwersalnego ISO 8601 "RRRR-MM-DDTgg: mm: SS. fffffffZ"
  - **numberOfDaysToAdd**: [Required] liczba dni do dodania
- `utcNow()`, w przeciwieństwie do szablonu Menedżer zasobów, można go użyć poza elementem DefaultValue.
  - Zwraca ciąg, który jest ustawiony na bieżącą datę i godzinę w formacie uniwersalnego ISO 8601 DateTime-MM-DDTgg: mm: SS. fffffffZ

Następujące funkcje są dostępne tylko w regułach zasad:

- `field(fieldName)`
  - **FieldName**: [Required] — nazwa [pola](#fields) do pobrania
  - Zwraca wartość tego pola z zasobu, który jest obliczany przez warunek if
  - `field` jest używany głównie z **AuditIfNotExists** i **DeployIfNotExists** do pól referencyjnych na analizowanym zasobie. Przykład tego zastosowania można zobaczyć w [przykładzie DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Zwraca wersję interfejsu API żądania, które spowodowało wyzwolenie oceny zasad (przykład: `2019-09-01`). Będzie to wersja interfejsu API, która została użyta w żądaniu PUT/PATCH do oceny przy tworzeniu/aktualizowaniu zasobów. Najnowsza wersja interfejsu API jest zawsze używana podczas oceny zgodności dla istniejących zasobów.
  


#### <a name="policy-function-example"></a>Przykład funkcji zasad

Ten przykład reguły używa funkcji zasobów `resourceGroup` w celu uzyskania właściwości **name** połączonej z `concat` tablicą i funkcją obiektu w celu skompilowania `like` warunku, który wymusza nazwę zasobu, aby rozpoczynać się od nazwy grupy zasobów.

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

- Rozszerzenie Azure Policy dla Visual Studio Code (zalecane)

  Użyj [rozszerzenia Azure Policy](../how-to/extension-for-vscode.md) , aby Visual Studio Code do wyświetlania i odnajdywania aliasów dla właściwości zasobów.

  ![Azure Policy rozszerzenie dla Visual Studio Code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Użyj operatora `project`, aby wyświetlić **alias** zasobu.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

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

- Interfejs API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Opis alias [*]

Kilka dostępnych aliasów ma wersję, która jest wyświetlana jako nazwa "normal" i inną, która ma **\[\*\]** do niej dołączony. Na przykład:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias "normal" reprezentuje pole jako pojedynczą wartość. To pole jest przeznaczone do dokładnego dopasowania scenariuszy porównywania, gdy cały zestaw wartości musi być dokładnie zdefiniowany, nie więcej i nie rzadziej.

Alias **\]\[\*** umożliwia porównanie wartości poszczególnych elementów w tablicy i konkretnych właściwości każdego elementu. Takie podejście umożliwia porównanie właściwości elementów dla elementu "If None of", "if any" lub "If all of". W przypadku bardziej złożonych scenariuszy Użyj wyrażenia warunku [Count](#count) . Korzystając z **ipRules\[\*\]** , przykładem będzie sprawdzanie, czy każda _Akcja_ jest _odrzucana_, ale nie martw się o liczbę istniejących reguł lub _wartość_ IP.
Ta przykładowa reguła sprawdza, czy istnieją dopasowania **ipRules\[\*\]. Value** do **10.0.4.1** i stosuje wartość **effecttype** tylko wtedy, gdy nie znaleziono co najmniej jednego dopasowania:

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

Inicjatywy pozwalają grupować kilka definicji zasad powiązane w celu uproszczenia przypisań i zarządzania, ponieważ współdziała z grupą jako pojedynczy element. Na przykład można grupować powiązane definicje zasad tagowania w jednym inicjatywy. Zamiast przypisywać każdej z zasad indywidualnie, mają zastosowanie tej inicjatywy.

> [!NOTE]
> Po przypisaniu inicjatywy nie można zmienić parametrów poziomu Initative. Ze względu na to zalecenie polega na ustawieniu elementu **DefaultValue** podczas definiowania parametru.

Poniższy przykład ilustruje sposób tworzenia inicjatywy do obsługi dwóch tagów: `costCenter` i `productName`. Używa dwóch wbudowane zasady do zastosowania domyślną wartość tagu.

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
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
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
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
