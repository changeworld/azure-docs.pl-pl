---
title: Utwórz niestandardową definicję zasad
description: Utwórz niestandardową definicję zasad Azure Policy, aby wymusić niestandardowe reguły biznesowe.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 240d0fa388fbdfdd3d29d735aed708a096440740
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980354"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Samouczek: Tworzenie definicji zasad niestandardowych

Niestandardowa definicja zasad pozwala klientom definiować własne reguły korzystania z platformy Azure. Te reguły często wymuszają:

- Praktyki dotyczące zabezpieczeń
- Zarządzanie kosztami
- Reguły specyficzne dla organizacji (takie jak nazewnictwo lub lokalizacje)

Niezależnie od tego, jaki jest sterownik biznesowy do tworzenia zasad niestandardowych, kroki te są takie same dla definiowania nowych zasad niestandardowych.

Przed utworzeniem zasad niestandardowych zapoznaj się z [przykładami zasad](../samples/index.md) , aby sprawdzić, czy zasady zgodne z potrzebami już istnieją.

Podejście do tworzenia zasad niestandardowych wykonuje następujące czynności:

> [!div class="checklist"]
> - Określ wymagania biznesowe
> - Mapuj każde wymaganie na Właściwość zasobów platformy Azure
> - Mapowanie właściwości na alias
> - Określ, którego efektu użyć
> - Redagowanie definicji zasad

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="identify-requirements"></a>Identyfikowanie wymagań

Przed utworzeniem definicji zasad należy zapoznać się z intencjami zasad. W tym samouczku użyjemy typowego wymagania dotyczącego zabezpieczeń przedsiębiorstwa, co ma na celu zilustrowanie pożądanych czynności:

- Każde konto magazynu musi być włączone dla protokołu HTTPS
- Każde konto magazynu musi być wyłączone dla protokołu HTTP

Wymagania powinny jasno identyfikować zarówno Stany zasobów "to", jak i "nie do".

Mimo że został zdefiniowany oczekiwany stan zasobu, nie zdefiniowano jeszcze tego, co chcemy zrobić z niezgodnymi zasobami. Azure Policy obsługuje wiele [efektów](../concepts/effects.md). W tym samouczku zdefiniujemy wymóg biznesowy, który uniemożliwia tworzenie zasobów, jeśli nie są one zgodne z regułami biznesowymi. Aby osiągnąć ten cel, użyjemy efektu [odmowy](../concepts/effects.md#deny) . Chcemy również zawiesić zasady dla określonych przypisań. W związku z tym użyjemy [wyłączonego](../concepts/effects.md#disabled) efektu i ustaw go jako [parametr](../concepts/definition-structure.md#parameters) w definicji zasad.

## <a name="determine-resource-properties"></a>Określanie właściwości zasobów

Zgodnie z wymaganiami biznesowymi zasób platformy Azure do inspekcji przy użyciu Azure Policy jest kontem magazynu. Nie wiemy jednak o właściwościach do użycia w definicji zasad. Azure Policy oblicza względem reprezentacji JSON zasobu, dlatego musimy zrozumieć właściwości dostępne dla tego zasobu.

Istnieje wiele sposobów na określenie właściwości zasobu platformy Azure. Zapoznajemy się z każdym z tych samouczków:

- Szablony Menedżer zasobów
  - Eksportuj istniejący zasób
  - Środowisko tworzenia
  - Szablony szybkiego startu (GitHub)
  - Dokumentacja szablonu dokumentacji
- Azure Resource Explorer

### <a name="resource-manager-templates"></a>Szablony Menedżer zasobów

Istnieje kilka sposobów, aby zapoznać się z [szablonem Menedżer zasobów](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) zawierającym właściwość, którą chcesz zarządzać.

#### <a name="existing-resource-in-the-portal"></a>Istniejący zasób w portalu

Najprostszym sposobem znajdowania właściwości jest wyszukanie istniejącego zasobu tego samego typu. Zasoby zostały już skonfigurowane z ustawieniem, które chcesz wymusić, a także podać wartość do porównania.
Przyjrzyj się stronie **Eksportuj szablon** (w obszarze **ustawienia**) w Azure Portal dla tego konkretnego zasobu.

![Eksportuj stronę szablonu do istniejącego zasobu](../media/create-custom-policy-definition/export-template.png)

W takim przypadku konto magazynu ujawnia szablon podobny do tego przykładu:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

W obszarze **Właściwości** jest wartość o nazwie **supportsHttpsTrafficOnly** ustawiona na **wartość false**. Ta właściwość wygląda na to, że może to być właściwość, której szukamy. Ponadto **typem** zasobu jest **Microsoft. Storage/storageAccounts**. Typ pozwala nam ograniczyć zasady tylko do zasobów tego typu.

#### <a name="create-a-resource-in-the-portal"></a>Tworzenie zasobu w portalu

Innym sposobem korzystania z portalu jest środowisko tworzenia zasobów. Podczas tworzenia konta magazynu za pomocą portalu, opcja na karcie **Zaawansowane** jest **wymagana**. Ta właściwość ma _wyłączone_ i _włączone_ opcje. Ikona informacji zawiera dodatkowy tekst potwierdzający, że ta opcja jest prawdopodobnie żądaną właściwością. Jednak Portal nie powiedzie nam nazwy właściwości na tym ekranie.

Na karcie **Recenzja + tworzenie** w dolnej części strony znajduje się link umożliwiający **pobranie szablonu do automatyzacji**. Po wybraniu linku zostanie otwarty szablon tworzący skonfigurowany zasób. W tym przypadku zobaczymy dwie kluczowe informacje:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Te informacje informują nas o typie właściwości, a także potwierdza **supportsHttpsTrafficOnly** jest właściwością, której szukamy.

#### <a name="quickstart-templates-on-github"></a>Szablony szybkiego startu w usłudze GitHub

[Szablony szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates) w usłudze GitHub mają setki szablonów Menedżer zasobów utworzonych dla różnych zasobów. Te szablony mogą być doskonałym sposobem znalezienia właściwości zasobu, którego szukasz. Niektóre właściwości mogą wyglądać na to, czego szukasz, ale kontrolują coś innego.

#### <a name="resource-reference-docs"></a>Dokumentacja dokumentacji zasobów

Aby sprawdzić poprawność właściwości **supportsHttpsTrafficOnly** , sprawdź informacje dotyczące szablonu Menedżer zasobów dla [zasobu konta magazynu](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) w dostawcy magazynu.
Obiekt Properties ma listę prawidłowych parametrów. Wybranie linku [StorageAccountPropertiesCreateParameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) pokazuje tabelę akceptowalnych właściwości. **supportsHttpsTrafficOnly** jest obecny i opis jest zgodny z tym, czego szukasz, aby spełnić wymagania biznesowe.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Innym sposobem eksplorowania zasobów platformy Azure jest użycie [Azure Resource Explorer](https://resources.azure.com) (wersja zapoznawcza). To narzędzie używa kontekstu subskrypcji, więc musisz uwierzytelnić się w witrynie sieci Web przy użyciu poświadczeń platformy Azure. Po uwierzytelnieniu można przeglądać według dostawców, subskrypcji, grup zasobów i zasobów.

Znajdź zasób konta magazynu i sprawdź właściwości. Tutaj zobaczymy Właściwość **supportsHttpsTrafficOnly** . Po wybraniu karty **Dokumentacja** zobaczymy, że opis właściwości jest zgodny z informacjami znajdującymi się w dokumentacji referencyjnej wcześniej.

## <a name="find-the-property-alias"></a>Znajdowanie aliasu właściwości

Określono właściwość zasobu, ale musimy zmapować tę właściwość na [alias](../concepts/definition-structure.md#aliases).

Istnieje kilka sposobów określenia aliasów dla zasobu platformy Azure. Zapoznajemy się z każdym z tych samouczków:

- Interfejs wiersza polecenia platformy Azure
- Azure PowerShell
- Wykres zasobów platformy Azure

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure Grupa poleceń `az provider` służy do wyszukiwania aliasów zasobów. Przefiltruje przestrzeń nazw **Microsoft. Storage** na podstawie szczegółowych informacji o zasobie platformy Azure.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

W wynikach zobaczymy alias obsługiwany przez konta magazynu o nazwie **supportsHttpsTrafficOnly**. Ta obecność aliasu oznacza, że możemy napisać zasady w celu wymuszenia spełnienia wymagań firmy.

### <a name="azure-powershell"></a>Azure PowerShell

W Azure PowerShell polecenie cmdlet `Get-AzPolicyAlias` służy do wyszukiwania aliasów zasobów. Przefiltruje przestrzeń nazw **Microsoft. Storage** na podstawie szczegółowych informacji o zasobie platformy Azure.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Podobnie jak w przypadku interfejsu wiersza polecenia platformy Azure, wyniki przedstawiają alias obsługiwany przez konta magazynu o nazwie **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Wykres zasobów platformy Azure

[Azure Resource Graph](../../resource-graph/overview.md) to nowa usługa w wersji zapoznawczej. Umożliwia ona kolejną metodę znajdowania właściwości zasobów platformy Azure. Oto przykładowe zapytanie dotyczące wyszukiwania na jednym koncie magazynu przy użyciu grafu zasobów:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Wyniki wyglądają podobnie jak te widoczne w szablonach Menedżer zasobów i przez Azure Resource Explorer. Jednak wyniki wykresu zasobów platformy Azure mogą również zawierać szczegóły [aliasu](../concepts/definition-structure.md#aliases) przez _projekcję_ tablicy _aliasów_ :

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Oto przykładowe dane wyjściowe z konta magazynu dla aliasów:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Usługa Azure Resource Graph (wersja zapoznawcza) może być używana w [Cloud Shell](https://shell.azure.com), co umożliwia szybkie i łatwe Eksplorowanie właściwości zasobów.

## <a name="determine-the-effect-to-use"></a>Określ efekt do użycia

Podejmowanie decyzji o tym, co należy zrobić w przypadku niezgodnych zasobów, jest niemal ważne, aby zdecydować, co należy oszacować w pierwszym miejscu. Każda możliwa odpowiedź do niezgodnego zasobu jest nazywana [efektem](../concepts/effects.md).
Efekt kontroluje, czy niezgodny zasób jest rejestrowany, blokowany, ma dołączone dane, czy też ma skojarzone z nim wdrożenie w celu przełączenia go z powrotem do stanu zgodności.

W naszym przykładzie odmowa ma wpływ na to, co chcemy zrobić, ponieważ nie chcemy, aby niezgodne zasoby zostały utworzone w środowisku platformy Azure. Inspekcja jest dobrym wyborem dla efektu zasad, aby określić, jaki wpływ zasad jest przed jego ustawieniem na Odmów. Jednym ze sposobów, aby zwiększyć wpływ na przydział, jest łatwiejsze Sparametryzuj efektu. Aby uzyskać szczegółowe informacje na temat tego, zobacz poniższe [Parametry](#parameters) .

## <a name="compose-the-definition"></a>Redagowanie definicji

Mamy teraz szczegóły właściwości i alias dla elementów, które planujemy zarządzać. Następnie utworzymy samą regułę zasad. Jeśli nie znasz jeszcze języka zasad, [Struktura definicji zasad](../concepts/definition-structure.md) referencyjnych dla struktury definicji zasad. Oto pusty szablon definicji zasad:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadane

Pierwsze trzy składniki są metadanymi zasad. Te składniki są łatwe w udostępnianiu wartości, ponieważ wiemy, dla czego tworzysz regułę. [Tryb](../concepts/definition-structure.md#mode) dotyczy głównie tagów i lokalizacji zasobów. Ponieważ nie trzeba ograniczać oceny do zasobów, które obsługują Tagi, będziemy używać wartości _All_ dla **mode**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametry

Chociaż nie używamy parametru do zmiany oceny, chcemy użyć parametru, aby umożliwić zmianę **wpływu** na rozwiązywanie problemów. Zdefiniujemy parametr **effecttype** i Ogranicz go tylko do **Odmów** i **wyłączenia**. Te dwie opcje odpowiadają wymaganiom biznesowym. Blok parametrów zakończonych wygląda podobnie do tego przykładu:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Reguła zasad

Tworzenie [reguły zasad](../concepts/definition-structure.md#policy-rule) jest ostatnim krokiem w tworzeniu naszej definicji zasad niestandardowych. Zidentyfikowano dwie instrukcje do przetestowania:

- **Typ** konta magazynu to **Microsoft. Storage/storageAccounts**
- Czy konto magazynu **supportsHttpsTrafficOnly** nie jest **prawdziwe**

Ponieważ obie te instrukcje muszą być prawdziwe, użyjemy [operatora logicznego](../concepts/definition-structure.md#logical-operators) **allOf** . Przekażemy do skutku parametr **effecttype** zamiast tworzenia deklaracji statycznej. Nasza ukończona reguła wygląda podobnie do tego przykładu:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Zakończona definicja

Wszystkie trzy części zdefiniowanych zasad są następujące:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Zakończona definicja może służyć do tworzenia nowych zasad. Portal i każdy zestaw SDK (interfejs wiersza polecenia platformy Azure, Azure PowerShell i API REST) akceptują definicję w różny sposób, więc Przejrzyj polecenia dla każdego z nich, aby sprawdzić poprawność użycia. Następnie przypisz go przy użyciu sparametryzowanego efektu do odpowiednich zasobów, aby zarządzać zabezpieczeniami kont magazynu.

## <a name="review"></a>Przegląd

W tym samouczku pomyślnie wykonano następujące zadania:

> [!div class="checklist"]
> - Zidentyfikowanie wymagań firmy
> - Zamapowane każde wymaganie do właściwości zasobów platformy Azure
> - Zamapowana właściwość na alias
> - Określono efekt do użycia
> - Składanie definicji zasad

## <a name="next-steps"></a>Następne kroki

Następnie utwórz zasady i przypisz je za pomocą niestandardowej definicji zasad:

> [!div class="nextstepaction"]
> [Tworzenie i przypisywanie definicji zasad](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)