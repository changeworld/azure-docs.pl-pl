---
title: Zrozumienie, jak działają efekty
description: Definicja zasad platformy Azure ma różne efekty, określające sposób zarządzania i podać zgodności.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c2bf19a2599d59b9ff2b3d189b26134f1528a878
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448568"
---
# <a name="understand-azure-policy-effects"></a>Omówienie usługi Azure Policy efekty

Każda definicja zasad w zasadach usługi Azure ma wpływ jednego. Powiadamiaj decyduje o tym, co się stanie, gdy reguła zasad jest oceniany w celu dopasowania. Efekty zachowywać się inaczej, jeśli są one dla nowego zasobu, zasób zaktualizowane lub istniejącego zasobu.

Efekty te są obecnie obsługiwane w definicji zasad:

- [Dołącz](#append)
- [Inspekcja](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Odmów](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled (Wyłączone)](#disabled)
- [EnforceRegoPolicy](#enforceregopolicy) (wersja zapoznawcza)

## <a name="order-of-evaluation"></a>Kolejność obliczania

Żądania można utworzyć lub zaktualizować zasobów za pomocą usługi Azure Resource Manager są obliczane najpierw przez usługę Azure Policy. Usługa Azure Policy tworzy listę wszystkich przydziałów, stosowanie do zasobu, które ocenia następnie zasobu wobec każdej definicji. Usługa Azure Policy przetwarza kilka efektów przed przekazaniem żądania do odpowiedniego dostawcy zasobów. Uniemożliwi to niepotrzebne przetwarzania przez dostawcę zasobów, gdy zasób nie spełnia wymagań kontrolki zaprojektowane zarządzania usługi Azure Policy.

- **Wyłączone** jest najpierw sprawdzane w celu określenia, jeśli powinna być oceniana reguła zasad.
- **Dołącz** jest następnie oceniany. Ponieważ Dołącz może zmienić żądania, zmiany wprowadzone przez dołączanie może uniemożliwić inspekcji lub odmówić efekt wraz z.
- **Odmów** jest następnie oceniany. Oceniając odmówić przed inspekcji, double rejestrowanie niepożądane zasobów nie będzie mógł.
- **Inspekcja** jest następnie oceniany przed żądaniem, przechodząc do dostawcy zasobów.

Po dostawcy zasobów zwróci kod powodzenia, **AuditIfNotExists** i **DeployIfNotExists** obliczenia w celu określenia, czy konieczne jest dodatkowe zgodności rejestrowania lub akcji.

Obecnie nie ma żadnych kolejności dla **EnforceRegoPolicy** efekt.

## <a name="disabled"></a>Wyłączone

Efekt jest przydatna do testowania sytuacji lub gdy definicja zasad ma sparametryzowane efekt. Ta elastyczność sprawia, że można wyłączyć jednego przypisania zamiast wyłączać wszystkie przypisania tej zasady.

## <a name="append"></a>Append

Dołącz służy do dodania kolejnych pól do żądanego zasobu podczas jej tworzenia lub aktualizacji. Typowym przykładem jest dodawanie tagów do zasobów, takich jak costCenter lub określając dozwolone adresy IP dla zasobów magazynu.

### <a name="append-evaluation"></a>Dołącz oceny

Dołącz ocenia przed żądania przetwarzane przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodaje pola do zasobu podczas **Jeśli** warunek reguły jest spełniony. Jeśli efekt Dołącz przesłonić wartość oryginalne żądanie, podając inną wartość, następnie go działa jako efektu odrzucenia i odrzuca żądanie. Aby dołączyć nową wartość do istniejącej tablicy, należy użyć **[\*]** wersji aliasu.

Po uruchomieniu definicji zasad przy użyciu efektu dołączania w ramach cyklu oceny go nie zmieniać zasoby, które już istnieją. Zamiast tego oznacza dowolnego zasobu, który spełnia **Jeśli** warunek jako niezgodne.

### <a name="append-properties"></a>Dołącz właściwości

Ma jedynie wpływ dołączania **szczegóły** tablicy, która jest wymagana. Jako **szczegóły** jest tablicą, może upłynąć, może to być pojedynczy **pól i wartości** pary lub wielokrotności węzła. Zapoznaj się [struktura definicji](definition-structure.md#fields) dla listy pól dopuszczalne.

### <a name="append-examples"></a>Dołącz przykłady

Przykład 1: Pojedynczy **pól i wartości** Paruj, aby dołączyć tag.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Przykład 2: Dwa **pól i wartości** pary do dołączenia zestawu tagów.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Przykład 3: Pojedynczy **pól i wartości** Sparuj przy użyciu innej niż **[\*]** [alias](definition-structure.md#aliases) z tablicą **wartość** do konfigurowania reguł adresów IP na koncie magazynu. Gdy non - **[\*]** aliasu jest tablicą, dołącza efekt **wartość** jako macierz w całości. Jeśli tablica już istnieje, Odmów zdarzeniu z konflikt.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Przykład 4: Pojedynczy **pól i wartości** Sparuj przy użyciu **[\*]** [alias](definition-structure.md#aliases) z tablicą **wartość** do konfigurowania reguł adresów IP na koncie magazynu. Za pomocą **[\*]** dołącza efekt alias **wartość** potencjalnie wcześniej istniejącej tablicy. Jeśli tablica nie istnieje, zostanie utworzony.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="deny"></a>Zablokuj

Odmów używany w celu zapobiegania żądania zasobów nie jest zgodna standardów zdefiniowanych za pośrednictwem definicji zasad, która kończy się niepowodzeniem żądania.

### <a name="deny-evaluation"></a>Odmów oceny

Podczas tworzenia lub aktualizowania zasobem dopasowane Odmów zapobiega żądanie przed wysłaniem do dostawcy zasobów. Żądanie jest zwracana jako `403 (Forbidden)`. W portalu można wyświetlić zabronione stanu wdrożenia, która została uniemożliwiona przez przypisanie zasad.

Podczas oceny istniejących zasobów zasobów, które pasują do definicji zasad odmowy są oznaczone jako niezgodne.

### <a name="deny-properties"></a>Odmów właściwości

Efektu odrzucenia nie ma żadnych dodatkowych właściwości do użycia w **następnie** warunek definicji zasad.

### <a name="deny-example"></a>Przykład Odmów

Przykład: Przy użyciu efektu odrzucenia.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Inspekcja

Inspekcja służy do tworzenia to zdarzenie ostrzegawcze w dzienniku aktywności, oceniając niezgodnym zasobem, ale go nie zatrzymuje żądania.

### <a name="audit-evaluation"></a>Ocena inspekcji

Inspekcja jest ostatni efekt sprawdzane przez usługę Azure Policy, podczas tworzenia lub aktualizacji zasobu. Usługa Azure Policy wysyła następnie zasobu na potrzeby dostawcy zasobów. Inspekcja działa tak samo, dla żądania zasobów i cykl oceny. Usługa Azure Policy dodaje `Microsoft.Authorization/policies/audit/action` operacji z dziennikiem aktywności i oznaczy zasobu jako niezgodne.

### <a name="audit-properties"></a>Właściwości inspekcji

Efekt inspekcji nie ma żadnych dodatkowych właściwości do użycia w **następnie** warunek definicji zasad.

### <a name="audit-example"></a>Przykład inspekcji

Przykład: Przy użyciu efektu audytu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umożliwia inspekcję na zasoby, które odpowiadają **Jeśli** warunku, ale nie ma elementów określonych w **szczegóły** z **następnie** warunku.

### <a name="auditifnotexists-evaluation"></a>Ocena AuditIfNotExists

AuditIfNotExists uruchomiony po dostawcy zasobów ma obsługiwane żądania tworzenia lub aktualizacji zasobu i zwrócił kod stanu powodzenia. Audyt występuje, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** nie zostało oszacowane jako prawdziwe. Usługa Azure Policy dodaje `Microsoft.Authorization/policies/audit/action` na działanie operacji logowania tak samo jako efekt inspekcji. Po wyzwoleniu zasób, który spełnione **Jeśli** warunek jest zasobem, który jest oznaczony jako niezgodne.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

**Szczegóły** właściwość skutków AuditIfNotExists ma wszystkie właściwości, które definiują powiązanych zasobów w celu dopasowania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Jeśli **details.type** jest typem zasobu poniżej **Jeśli** warunku zasobu, zasad, który wysyła zapytanie dotyczące zasobów tego **typu** w zakresie zasobów ocenione. W przeciwnym razie zapytania zasad w ramach tej samej grupie zasobów co ocenianą zasób.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
  - Gdy stan wartości **if.field.type** i **then.details.type** są zgodne, następnie **nazwa** staje się _wymagane_ i musi być `[field('name')]`. Jednak [inspekcji](#audit) zamiast tego należy rozważyć wpływ.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu pochodzić z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **typu** jest zasobem, który będzie poniżej **Jeśli** warunku zasobu.
  - Wartość domyślna to **Jeśli** warunku zasobu, grupy zasobów.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcji_ i _ResourceGroup_.
  - Ustawia zakres, gdzie można pobrać powiązanego zasobu, aby dopasować z.
  - Nie ma zastosowania, jeśli **typu** jest zasobem, który będzie poniżej **Jeśli** warunku zasobu.
  - Aby uzyskać _ResourceGroup_, będzie ograniczona do **Jeśli** warunku zasobu, grupy zasobów lub grupy zasobów określonej w **ResourceGroupName**.
  - Aby uzyskać _subskrypcji_, odpytuje całej subskrypcji dla powiązanych zasobów.
  - Wartość domyślna to _ResourceGroup_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełniający efekt i nie spowoduje wyzwolenia inspekcji.
  - Używa tego samego języka co reguły dla **Jeśli** warunku, ale jest obliczany osobno dla poszczególnych powiązanych zasobów.
  - Jeśli wszystkie dopasowania powiązanego zasobu zwraca wartość true, efekt jest spełniony i nie spowoduje wyzwolenia inspekcji.
  - Umożliwia [field()] Sprawdź równoważność wartości w **Jeśli** warunku.
  - Na przykład, można sprawdzić, czy zasób nadrzędny (w **Jeśli** warunku) znajduje się w tej samej lokalizacji zasobów zgodnych powiązanego zasobu.

### <a name="auditifnotexists-example"></a>Przykład AuditIfNotExists

Przykład: Oblicza maszyn wirtualnych, aby określić, jeśli rozszerzenia ochrony przed złośliwym oprogramowaniem istnieje, a następnie przeprowadza inspekcję, gdy brak.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Podobnie jak AuditIfNotExists, wdrożenie szablonu wykonuje DeployIfNotExists, gdy warunek jest spełniony.

> [!NOTE]
> [Zagnieżdżone szablony](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) są obsługiwane w przypadku **deployIfNotExists**, ale [połączone szablony](../../../azure-resource-manager/resource-group-linked-templates.md) nie są obecnie obsługiwane.

### <a name="deployifnotexists-evaluation"></a>Ocena DeployIfNotExists

DeployIfNotExists uruchomiony po dostawcy zasobów ma obsługiwane żądania tworzenia lub aktualizacji zasobu i zwrócił kod stanu powodzenia. Wdrożenie szablonu występuje, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** nie zostało oszacowane jako prawdziwe.

Podczas cyklu oszacowania definicji zasad z efektem DeployIfNotExists, dopasowywanie zasobów, które są oznaczone jako niezgodne, ale nie podjęto żadnej akcji dla tego zasobu.

### <a name="deployifnotexists-properties"></a>Właściwości DeployIfNotExists

**Szczegóły** właściwość efekty DeployIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania i wdrożenie szablonu do wykonania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Rozpoczyna się od próby pobrania zasobu poniżej **Jeśli** zasobów warunek, a następnie zapytań w ramach tej samej grupie zasobów co **Jeśli** warunku zasobu.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
  - Gdy stan wartości **if.field.type** i **then.details.type** są zgodne, następnie **nazwa** staje się _wymagane_ i musi być `[field('name')]`.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu pochodzić z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **typu** jest zasobem, który będzie poniżej **Jeśli** warunku zasobu.
  - Wartość domyślna to **Jeśli** warunku zasobu, grupy zasobów.
  - Jeśli wdrożenie szablonu jest wykonywane, jest ona wdrożona w grupie zasobów tej wartości.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcji_ i _ResourceGroup_.
  - Ustawia zakres, gdzie można pobrać powiązanego zasobu, aby dopasować z.
  - Nie ma zastosowania, jeśli **typu** jest zasobem, który będzie poniżej **Jeśli** warunku zasobu.
  - Aby uzyskać _ResourceGroup_, będzie ograniczona do **Jeśli** warunku zasobu, grupy zasobów lub grupy zasobów określonej w **ResourceGroupName**.
  - Aby uzyskać _subskrypcji_, odpytuje całej subskrypcji dla powiązanych zasobów.
  - Wartość domyślna to _ResourceGroup_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełniający efekt i nie spowoduje wyzwolenia wdrożenia.
  - Używa tego samego języka co reguły dla **Jeśli** warunku, ale jest obliczany osobno dla poszczególnych powiązanych zasobów.
  - Jeśli wszystkie dopasowania powiązanego zasobu zwraca wartość true, efekt jest spełniony i nie spowoduje wyzwolenia wdrożenia.
  - Umożliwia [field()] Sprawdź równoważność wartości w **Jeśli** warunku.
  - Na przykład, można sprawdzić, czy zasób nadrzędny (w **Jeśli** warunku) znajduje się w tej samej lokalizacji zasobów zgodnych powiązanego zasobu.
- **roleDefinitionIds** [wymagane]
  - Ta właściwość musi zawierać tablicę ciągów, które jest zgodny z Identyfikatorem roli kontroli dostępu opartej na rolach dostępna w subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowania — konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcji_ i _ResourceGroup_.
  - Ustawia typ wdrożenia wyzwolenie. _Subskrypcja_ wskazuje [wdrażania na poziomie subskrypcji](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ oznacza wdrożenie w grupie zasobów.
  - A _lokalizacji_ musi zostać określona właściwość _wdrożenia_ przy użyciu wdrożeń poziomu subskrypcji.
  - Wartość domyślna to _ResourceGroup_.
- **Wdrożenie** [wymagane]
  - Ta właściwość powinna zawierać wdrożenia pełnym szablonem znajdującym się, jak zostałaby przekazana do `Microsoft.Resources/deployments` umieścić interfejsu API. Aby uzyskać więcej informacji, zobacz [interfejsu API REST wdrożenia](/rest/api/resources/deployments).

  > [!NOTE]
  > Wszystkie funkcje wewnątrz **wdrożenia** właściwości są obliczane jako części szablonu nie zasad. Wyjątek stanowi **parametry** właściwość, która przekazuje wartości z zasad do szablonu. **Wartość** w tej sekcji, w ramach szablonu Nazwa parametru służy do wykonywania tej wartości, przekazując (zobacz _fullDbName_ w przykładzie DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Przykład DeployIfNotExists

Przykład: Oblicza baz danych SQL Server w celu ustalenia, czy włączono transparentDataEncryption. Jeśli nie, następnie jest wykonywany wdrożenia, aby umożliwić.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Efekt ten jest używany z definicji zasad *tryb* z `Microsoft.ContainerService.Data`. Jest używany do przekazywania zdefiniowane przy użyciu zasad kontroli przyjęcia [Rego](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego) do [Otwórz agenta zasad](https://www.openpolicyagent.org/) (Nieprzez) na [usługi Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Usługa Azure Policy dla rozwiązania Kubernetes](rego-for-aks.md) w publicznej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad.

### <a name="enforceregopolicy-evaluation"></a>Ocena EnforceRegoPolicy

Kontroler dopuszczenie Otwórz agenta zasad ocenia wszelkie nowe żądanie w klastrze w czasie rzeczywistym.
Co 5 minut, pełne skanowanie klastra zostanie ukończona, a wyniki zgłoszone do usługi Azure Policy.

### <a name="enforceregopolicy-properties"></a>Właściwości EnforceRegoPolicy

**Szczegóły** właściwość efektu EnforceRegoPolicy ma właściwości podrzędnych, które opisują reguły kontroli Rego czasowej.

- **policyId** [wymagane]
  - Unikatowa nazwa przekazany jako parametr do reguły kontroli Rego czasowej.
- **zasady** [wymagane]
  - Określa identyfikator URI reguły kontroli Rego czasowej.
- **policyParameters** [opcjonalnie]
  - Definiuje żadnych parametrów i wartości do przekazania do zasad rego.

### <a name="enforceregopolicy-example"></a>Przykład EnforceRegoPolicy

Przykład: Rego dopuszczenie kontroli reguły zezwalającej na tylko obrazy określonego kontenera w usłudze AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Zasady warstwowe

Zasób może mieć wpływ kilka przypisania. W tym samym zakresie lub w różnych zakresach, może być tych przydziałów. Każdy z tych przydziałów jest również mogą mieć różne efekty zdefiniowane. Warunek i efekt dla każdej zasady niezależnie jest oceniany. Na przykład:

- Zasady 1
  - Ogranicza lokalizację zasobu 'westus'
  - Przypisany do subskrypcji A
  - Odmów efektu
- Zasady 2
  - Ogranicza możliwość użycia zasobów lokalizację "eastus"
  - Przypisane do grupy zasobów B w subskrypcji A
  - Efekt inspekcji
  
Ten Instalator mogłoby spowodować następujące wyniki:

- Żaden zasób już w grupie zasobów B w "eastus" jest zgodne z zasadami 2 i niezgodne z zasadami 1
- Żaden zasób już w grupie zasobów B nie znajduje się w "eastus" jest niezgodne z zasadami 2 i niezgodne z zasadami 1 w przeciwnym razie w 'westus'
- Wszelkie nowy zasób, a subskrypcja nie znajduje się w 'westus' zostanie odrzucona przez zasady 1
- Nowego zasobu w subskrypcji A i grupa zasobów B w 'westus' został utworzony i niezgodne zasady 2

Jeśli zasady 1 i 2 zasad miała efektu metod deny, sytuacji zmieni się na:

- Żaden zasób już w grupie zasobów B nie znajduje się w "eastus" nie jest zgodna z zasadami 2
- Żaden zasób już w grupie zasobów B nie znajduje się w 'westus' nie jest zgodna z zasadami 1
- Wszelkie nowy zasób, a subskrypcja nie znajduje się w 'westus' zostanie odrzucona przez zasady 1
- Nowego zasobu w grupie zasobów B, a subskrypcja zostanie odrzucone.

Każdego przydziału jest szacowana osobno. Jako takie nie ma szansę zasobu do dostawy za pośrednictwem przerwa z różnic w zakresie. Wynikiem zasady warstwowe lub zasad nakładają się w sieci jest uważany za **zbiorczą najbardziej restrykcyjne**. Na przykład efektu odrzucenia, gdyby obie zasady 1 i 2 zasobem zostałby zablokowany za pomocą zasad nakładających się i powodujące konflikt. Jeśli nadal potrzebujesz zasób, który ma być tworzonych w zakresie docelowym, przejrzyj wykluczenia na każdego przypisania, aby sprawdzić odpowiednie zasady mają wpływ na odpowiednie zakresy.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Zrozumienie sposobu [programowe tworzenie zasad](../how-to/programmatically-create.md).
- Dowiedz się, jak [Pobierz dane zgodności](../how-to/getting-compliance-data.md).
- Dowiedz się, jak [korygowanie niezgodnych zasobów](../how-to/remediate-resources.md).
- Przejrzyj grupy zarządzania jest [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../management-groups/overview.md).