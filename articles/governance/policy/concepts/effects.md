---
title: Dowiedz się, jak działają efekty
description: Definicje zasad platformy Azure mają różne efekty, które określają sposób zarządzania i raportowania zgodności.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 0330cb5c732921efda3627dec92e486657097d82
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422452"
---
# <a name="understand-azure-policy-effects"></a>Opis efektów zasad platformy Azure

Każda definicja zasad w usłudze Azure Policy ma odzwierciedlenie w pojedynczym efekcie. Ten efekt określa, co się dzieje, gdy reguła zasad jest oceniana do dopasowania. Efekty zachowują się inaczej, jeśli są dla nowego zasobu, zaktualizowanego zasobu lub istniejącego zasobu.

Efekty te są obecnie obsługiwane w definicji zasad:

- [Append](#append)
- [Inspekcja](#audit)
- [AuditIfNotExists (AuditIfNotExists)](#auditifnotexists)
- [Zablokuj](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Wyłączone](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (wersja zapoznawcza)
- [EnforceRegoPolicy](#enforceregopolicy) (wersja zapoznawcza)
- [Modyfikuj](#modify)

## <a name="order-of-evaluation"></a>Kolejność obliczeń

Żądania utworzenia lub zaktualizowania zasobu za pomocą usługi Azure Resource Manager są najpierw oceniane przez usługę Azure Policy. Usługa Azure Policy tworzy listę wszystkich przydziałów, które mają zastosowanie do zasobu, a następnie ocenia zasób względem każdej definicji. Usługa Azure Policy przetwarza kilka efektów przed przekazaniem żądania odpowiedniego dostawcy zasobów. Zapobiega to niepotrzebnemu przetwarzaniu przez dostawcę zasobów, gdy zasób nie spełnia zaprojektowanych kontroli nadzoru usługi Azure Policy.

- **Wyłączone** jest sprawdzane najpierw, aby ustalić, czy reguła zasad powinna być oceniana.
- Następnie są oceniane **dołączanie** i **modyfikowanie.** Ponieważ albo może zmienić żądanie, wprowadzone zmiany mogą uniemożliwić inspekcji lub odmówić efekt wyzwalania.
- **Odmowa** jest następnie oceniane. Oceniając odmowę przed inspekcją, zapobiega podwójnemu rejestrowaniu niepożądanego zasobu.
- **Inspekcja** jest następnie oceniana przed przejściem żądania do dostawcy zasobów.

Po dostawcy zasobów zwraca kod **sukcesu, AuditIfNotExists** i **DeployIfNotExists** ocenić, aby ustalić, czy wymagane jest dodatkowe rejestrowanie zgodności lub akcja.

Obecnie nie ma żadnej kolejności oceny dla **EnforceOPAConstraint** lub **EnforceRegoPolicy** efekty.

## <a name="disabled"></a>Disabled (Wyłączony)

Efekt ten jest przydatny do testowania sytuacji lub gdy definicja zasad ma sparametryzowany efekt. Ta elastyczność umożliwia wyłączenie pojedynczego przypisania zamiast wyłączania wszystkich przypisań tej zasady.

Alternatywą dla wyłączone efekt jest **enforcementMode,** który jest ustawiony na przypisanie zasad.
Gdy **enforcementMode** jest _wyłączona,_ zasoby są nadal oceniane. Rejestrowanie, takie jak dzienniki aktywności i efekt zasad nie występują. Aby uzyskać więcej informacji, zobacz [przypisywanie zasad — tryb wymuszania](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Append

Dołącz służy do dodawania dodatkowych pól do żądanego zasobu podczas tworzenia lub aktualizacji. Typowym przykładem jest określenie dozwolonych usług IP dla zasobu magazynu.

> [!IMPORTANT]
> Dołącz jest przeznaczony do użytku z właściwościami nietag. Podczas dołączania można dodać tagi do zasobu podczas żądania tworzenia lub aktualizacji, zaleca się użycie efektu [Modyfikuj](#modify) dla tagów zamiast.

### <a name="append-evaluation"></a>Dołącz ocenę

Dołącz ocenia, zanim żądanie zostanie przetworzone przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodaje pola do zasobu, gdy warunek **if** reguły zasad jest spełniony. Jeśli efekt dołączania zastąpi wartość w oryginalnym żądaniu inną wartością, działa jako efekt odmowy i odrzuca żądanie. Aby dołączyć nową wartość do istniejącej tablicy, należy użyć wersji **[\*]** aliasu.

Gdy definicja zasad przy użyciu efektu dołączania jest uruchamiana w ramach cyklu oceny, nie wprowadza zmian w zasobach, które już istnieją. Zamiast tego oznacza każdy zasób, który spełnia warunek **if** jako niezgodny.

### <a name="append-properties"></a>Właściwości dołączania

Efekt dołączania ma tylko tablicę **szczegółów,** która jest wymagana. Ponieważ **szczegóły** są tablicą, może to zająć jedno **pole/parę wartości** lub wielokrotności. Zobacz [strukturę definicji](definition-structure.md#fields) dla listy dopuszczalnych pól.

### <a name="append-examples"></a>Dołączanie przykładów

Przykład 1: Pojedyncza para **pól/wartości** przy użyciu [aliasu](definition-structure.md#aliases) nie-**[\*]** z **wartością** tablicy do ustawiania reguł IP na koncie magazynu. Gdy alias non-**\*[ ]** jest tablicą, efekt dołącza **wartość** jako całą tablicę. Jeśli tablica już istnieje, wystąpienie zdarzenia odmowy z konfliktu.

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

Przykład 2: Pojedyncza para **pól/wartości** przy użyciu [aliasu](definition-structure.md#aliases) **[\*]** z **wartością** tablicy do ustawiania reguł IP na koncie magazynu. Za pomocą **\*aliasu [ ]** efekt dołącza **wartość** do potencjalnie istniejącej tablicy. Jeśli tablica jeszcze nie istnieje, zostanie utworzona.

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

## <a name="modify"></a>Modyfikowanie

Modyfikacja służy do dodawania, aktualizowania lub usuwania znaczników na zasobie podczas tworzenia lub aktualizacji. Typowym przykładem jest aktualizowanie tagów na zasoby, takie jak costCenter. Zasady modyfikowania powinny `mode` zawsze być ustawione _na Indeksowane,_ chyba że zasób docelowy jest grupą zasobów. Istniejące zasoby niezgodne można skorygować za pomocą [zadania korygowania](../how-to/remediate-resources.md). Pojedyncza reguła modyfikowania może mieć dowolną liczbę operacji.

> [!IMPORTANT]
> Modyfikacja jest obecnie tylko do użytku z tagami. Jeśli zarządzasz tagami, zaleca się użycie modyfikuj zamiast dołączania jako modyfikacja zapewnia dodatkowe typy operacji i możliwość korygowanie istniejących zasobów. Jednak dołącz jest zalecane, jeśli nie można utworzyć tożsamości zarządzanej.

### <a name="modify-evaluation"></a>Modyfikowanie oceny

Modyfikowanie ocenia, zanim żądanie zostanie przetworzone przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Modyfikowanie dodaje lub aktualizuje tagi na zasobie, gdy warunek **if** reguły zasad jest spełniony.

Gdy definicja zasad przy użyciu efektu modyfikowania jest uruchamiana jako część cyklu oceny, nie wprowadza zmian w zasobach, które już istnieją. Zamiast tego oznacza każdy zasób, który spełnia warunek **if** jako niezgodny.

### <a name="modify-properties"></a>Modyfikowanie właściwości

Właściwość **szczegółów** efektu Modyfikowania ma wszystkie właściwości podrzędne, które definiują uprawnienia potrzebne do korygowania oraz **operacje** używane do dodawania, aktualizowania lub usuwania wartości znaczników.

- **roleDefinitionIds** [wymagane]
  - Ta właściwość musi zawierać tablicę ciągów, które pasują do roli kontroli dostępu opartej na rolach opartych na rolach dostępnych dla subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
  - Zdefiniowana rola musi zawierać wszystkie operacje przyznane roli [współautora.](../../../role-based-access-control/built-in-roles.md#contributor)
- **operacje** [wymagane]
  - Tablica wszystkich operacji tagów, które mają zostać ukończone na pasujących zasobach.
  - Właściwości:
    - **operacja** [wymagana]
      - Określa, jakie działania należy podjąć w pasującym zasobie. Dostępne opcje to: _addOrReplace_, _Add_, _Remove_. _Add_ zachowuje się podobnie do efektu [Dołączanie.](#append)
    - **pole** [wymagane]
      - Znacznik do dodania, zastąpienia lub usunięcia. Nazwy znaczników muszą być zgodne z tą samą konwencją nazewnictwa dla innych [pól](./definition-structure.md#fields).
    - **wartość** (opcjonalnie)
      - Wartość, na która ma być ustawiona.
      - Ta właściwość jest wymagana, jeśli **operacja** jest _addOrReplace_ lub _Add_.

### <a name="modify-operations"></a>Modyfikowanie operacji

Tablica właściwości **operations** umożliwia zmianę kilku tagów na różne sposoby z definicji pojedynczej zasady. Każda operacja składa się z **właściwości operacji,** **pola**i **wartości.** Operacja określa, co zadanie korygujące robi z tagami, pole określa, który tag został zmieniony, a wartość definiuje nowe ustawienie dla tego tagu. Poniższy przykład wprowadza następujące zmiany znaczników:

- Ustawia `environment` tag na "Test", nawet jeśli już istnieje z inną wartością.
- Usuwa znacznik `TempResource`.
- Ustawia `Dept` znacznik na parametr zasad _DeptName_ skonfigurowany w przypisywanie zasad.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

Właściwość **operacji** ma następujące opcje:

|Operacja |Opis |
|-|-|
|addOrReplace (Miejsce) |Dodaje zdefiniowany znacznik i wartość do zasobu, nawet jeśli tag już istnieje z inną wartością. |
|Dodaj |Dodaje zdefiniowany znacznik i wartość do zasobu. |
|Remove |Usuwa zdefiniowany tag z zasobu. |

### <a name="modify-examples"></a>Modyfikowanie przykładów

Przykład 1: `environment` Dodaj znacznik `environment` i zastąp istniejące tagi "Test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Przykład 2: `env` Usuń znacznik `environment` i dodaj `environment` znacznik lub zastąp istniejące znaczniki wartością sparametryzowaną:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Zablokuj

Deny jest używany do zapobiegania żądania zasobów, który nie pasuje do zdefiniowanych standardów za pomocą definicji zasad i nie powiedzie się żądanie.

### <a name="deny-evaluation"></a>Odmów oceny

Podczas tworzenia lub aktualizowania dopasowanego zasobu odmowa zapobiega żądaniu przed wysłaniem do dostawcy zasobów. Żądanie jest zwracane `403 (Forbidden)`jako . W portalu Zabronione mogą być wyświetlane jako stan wdrożenia, który został uniemożliwiony przez przypisanie zasad.

Podczas oceny istniejących zasobów zasobów, które spełniają definicję zasad odmowy są oznaczone jako niezgodne.

### <a name="deny-properties"></a>Właściwości odmów

Efekt odmowy nie ma żadnych dodatkowych właściwości do użycia w **ówczesnym** warunku definicji zasad.

### <a name="deny-example"></a>Odmów przykładu

Przykład: Przy użyciu efektu odmowy.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Inspekcja

Inspekcja jest używana do tworzenia zdarzenia ostrzegawczego w dzienniku działań podczas oceny zasobu niezgodnego, ale nie zatrzymuje żądania.

### <a name="audit-evaluation"></a>Ocena audytu

Inspekcja jest ostatnim efektem sprawdzanym przez usługę Azure Policy podczas tworzenia lub aktualizowania zasobu. Usługa Azure Policy następnie wysyła zasób do dostawcy zasobów. Inspekcja działa tak samo dla żądania zasobów i cyklu oceny. Usługa Azure `Microsoft.Authorization/policies/audit/action` Policy dodaje operację do dziennika działań i oznacza zasób jako niezgodny.

### <a name="audit-properties"></a>Inspekcja właściwości

Efekt inspekcji nie ma żadnych dodatkowych właściwości do użycia w **ówczesnym** warunku definicji zasad.

### <a name="audit-example"></a>Przykład inspekcji

Przykład: Przy użyciu efektu inspekcji.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists (AuditIfNotExists)

AuditIfNotExists umożliwia inspekcję zasobów, które odpowiadają **if** warunek, ale nie ma składników określonych w **szczegółach** warunku **then.**

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists oceny

AuditIfNotExists działa po dostawcy zasobów obsługi żądania tworzenia lub aktualizacji zasobu i zwrócił kod stanu sukcesu. Inspekcja występuje, jeśli nie ma żadnych powiązanych zasobów lub jeśli zasoby zdefiniowane przez **ExistenceCondition** nie ocenić true. Usługa Azure `Microsoft.Authorization/policies/audit/action` Policy dodaje operację do dziennika aktywności w taki sam sposób, jak efekt inspekcji. Po wyzwoleniu zasób, który spełnił warunek **if,** jest zasobem oznaczonym jako niezgodny.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

Właściwość **szczegółów** auditIfNotExists efekty ma wszystkie właściwości podrzędne, które definiują powiązane zasoby do dopasowania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Jeśli **details.type** jest typem zasobu pod **zasobem** if warunek, kwerendy zasad dla zasobów tego **typu** w zakresie ocenianego zasobu. W przeciwnym razie kwerendy zasad w tej samej grupie zasobów co oceniony zasób.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu, aby dopasować i powoduje, że zasady do pobrania jednego określonego zasobu zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku **if.field.type** i **then.details.type** są zgodne, `[field('name')]` **nazwa** staje się _wymagana_ i musi być . Zamiast tego należy rozważyć efekt [kontroli.](#audit)
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu pochodzić z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **typ** jest zasób, który będzie pod zasobem **if** warunek.
  - Wartość **domyślna** to grupa zasobów zasobu if.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _Subskrypcja_ i _ResourceGroup_.
  - Ustawia zakres, gdzie należy pobrać powiązany zasób, aby dopasować z.
  - Nie ma zastosowania, jeśli **typ** jest zasób, który będzie pod zasobem **if** warunek.
  - W przypadku _grupy zasobów_można ograniczyć do grupy zasobów zasobu **if** lub grupy zasobów określonej w **pliku ResourceGroupName**.
  - W przypadku _subskrypcji_kwerendy całej subskrypcji dla powiązanego zasobu.
  - Domyślnie jest _ResourceGroup_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełnia efekt i nie wyzwala inspekcji.
  - Używa tego samego języka co reguła zasad dla **if** condition, ale jest oceniana względem każdego powiązanego zasobu indywidualnie.
  - Jeśli dowolny pasujący powiązany zasób ma wartość true, efekt jest spełniony i nie wyzwala inspekcji.
  - Można użyć [field()], aby sprawdzić równoważność z wartościami w warunku **if.**
  - Na przykład może służyć do sprawdzania poprawności, że zasób nadrzędny (w **if** condition) znajduje się w tej samej lokalizacji zasobu co pasujący powiązany zasób.

### <a name="auditifnotexists-example"></a>Przykład AuditIfNotExists

Przykład: Ocenia maszyny wirtualne, aby ustalić, czy rozszerzenie ochrony przed złośliwym oprogramowaniem istnieje następnie inspekcji, gdy brakuje.

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

Podobnie jak AuditIfNotExists, DeployIfNotExists definicji zasad wykonuje wdrożenie szablonu, gdy warunek jest spełniony.

> [!NOTE]
> [Szablony zagnieżdżone](../../../azure-resource-manager/templates/linked-templates.md#nested-template) są obsługiwane przez **deployIfNotExists**, ale [połączone szablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nie są obecnie obsługiwane.

### <a name="deployifnotexists-evaluation"></a>Ocena DeployIfNotExists

DeployIfNotExists działa około 15 minut po dostawcy zasobów obsługi żądania zasobów tworzenia lub aktualizacji i zwrócił kod stanu sukcesu. Wdrożenie szablonu występuje, jeśli nie ma żadnych powiązanych zasobów lub jeśli zasoby zdefiniowane przez **ExistenceCondition** nie ocenić true.
Czas trwania wdrożenia zależy od złożoności zasobów zawartych w szablonie.

Podczas cyklu oceny definicje zasad z DeployIfNotExists efekt, które pasują do zasobów są oznaczone jako niezgodne, ale nie jest podejmowana żadna akcja na tym zasobie. Istniejące zasoby niezgodne można skorygować za pomocą [zadania korygowania](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>DeployIfNotExists właściwości

Właściwość **szczegółów** deployIfNotExists efekt ma wszystkie właściwości podrzędne, które definiują powiązane zasoby, aby dopasować i wdrożenia szablonu do wykonania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Rozpoczyna się od próby pobrania zasobu pod zasobem **if** warunek, a następnie kwerend w tej samej grupie zasobów, jak **jeśli** zasób warunek.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu, aby dopasować i powoduje, że zasady do pobrania jednego określonego zasobu zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku **if.field.type** i **then.details.type** są zgodne, `[field('name')]` **nazwa** staje się _wymagana_ i musi być .
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu pochodzić z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **typ** jest zasób, który będzie pod zasobem **if** warunek.
  - Wartość **domyślna** to grupa zasobów zasobu if.
  - Jeśli wdrożenie szablonu jest wykonywane, jest wdrażany w grupie zasobów o tej wartości.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _Subskrypcja_ i _ResourceGroup_.
  - Ustawia zakres, gdzie należy pobrać powiązany zasób, aby dopasować z.
  - Nie ma zastosowania, jeśli **typ** jest zasób, który będzie pod zasobem **if** warunek.
  - W przypadku _grupy zasobów_można ograniczyć do grupy zasobów zasobu **if** lub grupy zasobów określonej w **pliku ResourceGroupName**.
  - W przypadku _subskrypcji_kwerendy całej subskrypcji dla powiązanego zasobu.
  - Domyślnie jest _ResourceGroup_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasób **typu** spełnia efekt i nie wyzwala wdrożenia.
  - Używa tego samego języka co reguła zasad dla **if** condition, ale jest oceniana względem każdego powiązanego zasobu indywidualnie.
  - Jeśli dowolny pasujący powiązany zasób ma wartość true, efekt jest spełniony i nie wyzwala wdrożenia.
  - Można użyć [field()], aby sprawdzić równoważność z wartościami w warunku **if.**
  - Na przykład może służyć do sprawdzania poprawności, że zasób nadrzędny (w **if** condition) znajduje się w tej samej lokalizacji zasobu co pasujący powiązany zasób.
- **roleDefinitionIds** [wymagane]
  - Ta właściwość musi zawierać tablicę ciągów, które pasują do roli kontroli dostępu opartej na rolach opartych na rolach dostępnych dla subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcjonalnie)
  - Dozwolone wartości to _Subskrypcja_ i _ResourceGroup_.
  - Ustawia typ wdrożenia, które mają zostać wyzwolone. _Subskrypcja_ wskazuje [wdrożenie na poziomie subskrypcji](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ wskazuje wdrożenie do grupy zasobów.
  - Właściwość _lokalizacji_ musi być określona w _wdrożenie_ podczas korzystania z wdrożeń na poziomie subskrypcji.
  - Domyślnie jest _ResourceGroup_.
- **Wdrożenie** [wymagane]
  - Ta właściwość powinna zawierać pełne wdrożenie szablonu, ponieważ zostanie przekazana do interfejsu API `Microsoft.Resources/deployments` PUT. Aby uzyskać więcej informacji, zobacz [interfejs API REST wdrożenia](/rest/api/resources/deployments).

  > [!NOTE]
  > Wszystkie funkcje wewnątrz **Deployment** właściwości są oceniane jako składniki szablonu, a nie zasady. Wyjątkiem jest właściwość **parametrów,** która przekazuje wartości z zasad do szablonu. **Wartość** w tej sekcji pod nazwą parametru szablonu jest używana do wykonywania przekazywania tej wartości (zobacz _fullDbName_ w przykładzie DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Przykład DeployIfNotExists

Przykład: Ocenia bazy danych programu SQL Server, aby ustalić, czy transparentDataEncryption jest włączona. Jeśli nie, to wdrożenie, aby włączyć jest wykonywane.

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

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Efekt ten jest używany w `Microsoft.Kubernetes.Data` *trybie* definicji zasad . Służy do przekazywania gatekeeper v3 reguły kontroli wstępu zdefiniowane za pomocą [frameworka ograniczenia OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) do [open policy agent](https://www.openpolicyagent.org/) (OPA) do samodzielnie zarządzanych klastrów Kubernetes na platformie Azure.

> [!NOTE]
> [Usługa Azure Policy for AKS Engine](aks-engine.md) jest dostępna w publicznej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint ocena

Kontroler admission agenta otwartych zasad ocenia każde nowe żądanie w klastrze w czasie rzeczywistym.
Co 5 minut pełne skanowanie klastra jest zakończone, a wyniki zgłaszane do zasad platformy Azure.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint właściwości

Właściwości **szczegóły** EnforceOPAConstraint efekt ma właściwości podrzędne, które opisują Gatekeeper v3 reguły kontroli dopuszczenia.

- **tablica ograniczenia** [wymagana]
  - Szablon ograniczenia CustomResourceDefinition (CRD), który definiuje nowe ograniczenia. Szablon definiuje logikę Rego, schemat ograniczeń i parametry ograniczeń, które są przekazywane za pośrednictwem **wartości** z usługi Azure Policy.
- **ograniczenie** [wymagane]
  - Implementacja CRD szablonu Ograniczenia. Używa parametrów przekazywanych przez **wartości** jako `{{ .Values.<valuename> }}`. W poniższym przykładzie `{{ .Values.cpuLimit }}` byłoby `{{ .Values.memoryLimit }}`to i .
- **wartości** [opcjonalnie]
  - Definiuje wszelkie parametry i wartości, które mają być przedysysy, które mają być przesuwne do ograniczenia. Każda wartość musi istnieć w szablonie ograniczenia CRD.

### <a name="enforceopaconstraint-example"></a>Przykład EnforceOPAConstraint

Przykład: Reguła kontroli wstępu gatekeeper v3, aby ustawić limity zasobów procesora CPU i pamięci kontenera w aks engine.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EgzekwacjaPolityka Najmocniej

Efekt ten jest używany w `Microsoft.ContainerService.Data` *trybie* definicji zasad . Służy do przekazywania reguł kontroli wstępu Gatekeeper v2 zdefiniowanych za pomocą [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) do [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) w [usłudze Azure Kubernetes.](../../../aks/intro-kubernetes.md)

> [!NOTE]
> [Usługa Azure Policy for AKS](rego-for-aks.md) jest dostępna w ograniczonej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy ocena

Kontroler admission agenta otwartych zasad ocenia każde nowe żądanie w klastrze w czasie rzeczywistym.
Co 5 minut pełne skanowanie klastra jest zakończone, a wyniki zgłaszane do zasad platformy Azure.

### <a name="enforceregopolicy-properties"></a>Właściwości EnforceRegoPolicy

Właściwości **szczegóły** EnforceRegoPolicy efekt ma właściwości podrzędne, które opisują Gatekeeper v2 reguły kontroli dopuszczenia.

- **policyId** [wymagane]
  - Unikatowa nazwa przekazana jako parametr do reguły kontroli wstępu Rego.
- **[wymagane]**
  - Określa identyfikator URI reguły kontroli wstępu Rego.
- **policyParameters** [opcjonalnie]
  - Definiuje wszystkie parametry i wartości, które mają być przedyszone do zasad rego.

### <a name="enforceregopolicy-example"></a>Przykład EnforceRegoPolicy

Przykład: Gatekeeper v2 reguły kontroli wstępu, aby zezwolić tylko na określone obrazy kontenera w AKS.

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

## <a name="layering-policies"></a>Zasady nakładania warstw

Na zasób może mieć wpływ kilka przypisań. Te przydziały mogą znajdować się w tym samym zakresie lub w różnych zakresach. Każde z tych przypisań może mieć również inny efekt zdefiniowany. Warunek i efekt dla każdej zasady jest oceniany niezależnie. Przykład:

- Polityka 1
  - Ogranicza lokalizację zasobów do "westus"
  - Przypisany do subskrypcji A
  - Efekt odmów
- Polityka 2
  - Ogranicza lokalizację zasobów do "eastus"
  - Przypisany do grupy zasobów B w subskrypcji A
  - Efekt inspekcji
  
Ta konfiguracja spowoduje następujący wynik:

- Każdy zasób już w grupie zasobów B w "eastus" jest zgodny z zasadą 2 i niezgodny z zasadą 1
- Każdy zasób już w grupie zasobów B nie w "eastus" jest niezgodny z zasadą 2 i niezgodne z zasadą 1, jeśli nie w "westus"
- Wszelkie nowe zasoby w subskrypcji A nie w "westus" jest odrzucana przez zasady 1
- Każdy nowy zasób w subskrypcji A i grupie zasobów B w "westus" jest tworzony i niezgodny z zasadami 2

Jeśli zarówno polityka 1, jak i polityka 2 miały skutek odmowy, sytuacja zmienia się na:

- Każdy zasób już w grupie zasobów B nie w "eastus" jest niezgodny z zasadą 2
- Każdy zasób już w grupie zasobów B nie w "westus" jest niezgodny z zasadą 1
- Wszelkie nowe zasoby w subskrypcji A nie w "westus" jest odrzucana przez zasady 1
- Każdy nowy zasób w grupie zasobów B subskrypcji A jest odrzucany

Każde przypisanie jest oceniane indywidualnie. W związku z tym nie ma możliwości zasobu, aby prześlizgnąć się przez lukę od różnic w zakresie. Wynik netto nakładania się zasad warstw lub zasad jest uważany za **najbardziej restrykcyjny.** Na przykład, jeśli zarówno zasady 1, jak i 2 miały efekt odmowy, zasób zostanie zablokowany przez nakładające się i sprzeczne zasady. Jeśli nadal potrzebujesz zasobu do utworzenia w zakresie docelowym, przejrzyj wykluczenia dla każdego przypisania, aby sprawdzić poprawność odpowiednich zasad, które mają wpływ na odpowiednie zakresy.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
