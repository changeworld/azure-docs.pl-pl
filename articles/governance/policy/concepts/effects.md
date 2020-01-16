---
title: Informacje o działaniu efektów
description: Definicje Azure Policy mają różne skutki, które określają sposób zarządzania i zgłaszania zgodności.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 2b588cfb7c13a63e3fa5d3a65d9ccb24a2e854fd
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972812"
---
# <a name="understand-azure-policy-effects"></a>Omówienie usługi Azure Policy efekty

Każda definicja zasad w zasadach usługi Azure ma wpływ jednego. Powiadamiaj decyduje o tym, co się stanie, gdy reguła zasad jest oceniany w celu dopasowania. Efekty zachowywać się inaczej, jeśli są one dla nowego zasobu, zasób zaktualizowane lub istniejącego zasobu.

Te efekty są obecnie obsługiwane w definicji zasad:

- [Łączono](#append)
- [Inspekcja](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Pozbawić](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled (Wyłączone)](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (wersja zapoznawcza)
- [EnforceRegoPolicy](#enforceregopolicy) (wersja zapoznawcza)
- [Modyfikowanie](#modify)

## <a name="order-of-evaluation"></a>Kolejność obliczania

Żądania utworzenia lub zaktualizowania zasobu za pomocą usługi Azure Resource Manager są najpierw oceniane przez usługę Azure Policy. Azure Policy tworzy listę wszystkich przypisań, które są stosowane do zasobu, a następnie szacuje zasób dla każdej definicji. Azure Policy przetwarza kilka efektów przed przekazaniem żądania do odpowiedniego dostawcy zasobów. Wykonanie tej czynności zapobiega niepotrzebnemu przetwarzaniu przez dostawcę zasobów, gdy zasób nie spełnia zaprojektowanych kontrolek ładu Azure Policy.

- **Wyłączone** jest najpierw sprawdzane w celu określenia, jeśli powinna być oceniana reguła zasad.
- Następnie są **oceniane i** **modyfikowane** . Ponieważ może on zmienić żądanie, wprowadzona zmiana może uniemożliwić wywoływanie inspekcji lub skutków odmowy.
- **Odmów** jest następnie oceniany. Oceniając odmówić przed inspekcji, double rejestrowanie niepożądane zasobów nie będzie mógł.
- **Inspekcja** jest następnie oceniany przed żądaniem, przechodząc do dostawcy zasobów.

Po dostawcy zasobów zwróci kod powodzenia, **AuditIfNotExists** i **DeployIfNotExists** obliczenia w celu określenia, czy konieczne jest dodatkowe zgodności rejestrowania lub akcji.

Obecnie nie ma żadnych kolejności oceny dla efektów **EnforceOPAConstraint** lub **EnforceRegoPolicy** .

## <a name="disabled"></a>Disabled (Wyłączony)

Efekt jest przydatna do testowania sytuacji lub gdy definicja zasad ma sparametryzowane efekt. Ta elastyczność sprawia, że można wyłączyć jednego przypisania zamiast wyłączać wszystkie przypisania tej zasady.

Alternatywą dla wyłączonego efektu jest **wymuszmode** , który jest ustawiony w przypisaniu zasad.
Gdy **wymuszanie** jest _wyłączone_, nadal są oceniane zasoby. Rejestrowanie, takie jak dzienniki aktywności, i efekt zasad nie wystąpi. Aby uzyskać więcej informacji, zobacz [Tryb wymuszania przypisywania zasad](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Append

Dołącz służy do dodania kolejnych pól do żądanego zasobu podczas jej tworzenia lub aktualizacji. Typowym przykładem jest określenie dozwolonych adresów IP dla zasobu magazynu.

> [!IMPORTANT]
> Dołączenie jest przeznaczone do użycia z właściwościami niebędącymi tagami. Podczas gdy Dołączanie może dodawać Tagi do zasobu w trakcie żądania utworzenia lub aktualizacji, zaleca się użycie zamiast nich efektów [modyfikacji](#modify) dla tagów.

### <a name="append-evaluation"></a>Dołącz oceny

Dołącz ocenia przed żądania przetwarzane przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodaje pola do zasobu podczas **Jeśli** warunek reguły jest spełniony. Jeśli efekt Dołącz przesłonić wartość oryginalne żądanie, podając inną wartość, następnie go działa jako efektu odrzucenia i odrzuca żądanie. Aby dołączyć nową wartość do istniejącej tablicy, użyj wersji **[\*]** aliasu.

Po uruchomieniu definicji zasad przy użyciu efektu dołączania w ramach cyklu oceny go nie zmieniać zasoby, które już istnieją. Zamiast tego oznacza dowolnego zasobu, który spełnia **Jeśli** warunek jako niezgodne.

### <a name="append-properties"></a>Dołącz właściwości

Ma jedynie wpływ dołączania **szczegóły** tablicy, która jest wymagana. Jako **szczegóły** jest tablicą, może upłynąć, może to być pojedynczy **pól i wartości** pary lub wielokrotności węzła. Zapoznaj się [struktura definicji](definition-structure.md#fields) dla listy pól dopuszczalne.

### <a name="append-examples"></a>Dołącz przykłady

Przykład 1: pojedynczej pary **pól/wartości** przy użyciu [aliasu](definition-structure.md#aliases) innego niż **[\*]** z **wartością** tablicy, aby ustawić reguły adresów IP na koncie magazynu. Gdy alias inny niż **[\*]** jest tablicą, efekt dodaje **wartość** jako całą tablicę. Jeśli tablica już istnieje, zdarzenie odmowy występuje z powodu konfliktu.

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

Przykład 2: pojedyncze pary **pól/wartości** przy użyciu [aliasu](definition-structure.md#aliases) **[\*]** z **wartością** tablicy, aby ustawić reguły adresów IP na koncie magazynu. Korzystając z aliasu **[\*]** , efekt dołącza **wartość** do potencjalnie istniejącej tablicy. Jeśli tablica jeszcze nie istnieje, zostanie utworzona.

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

## <a name="modify"></a>Modyfikuj

Modyfikowanie służy do dodawania, aktualizowania lub usuwania tagów w zasobie podczas tworzenia lub aktualizowania. Typowym przykładem jest aktualizowanie tagów w zasobach, takich jak costCenter. Zasady modyfikowania powinny mieć zawsze ustawioną `mode` _indeksowanie_ , chyba że zasób docelowy jest grupą zasobów. Istniejące niezgodne zasoby można skorygować przy użyciu [zadania korygowania](../how-to/remediate-resources.md). Pojedyncza reguła modyfikowania może zawierać dowolną liczbę operacji.

> [!IMPORTANT]
> Modyfikacja jest obecnie tylko do użytku z tagami. Jeśli zarządzasz tagami, zaleca się korzystanie z funkcji Modify zamiast Dołącz jako Modyfikuj udostępnia dodatkowe typy operacji i możliwość korygowania istniejących zasobów. Jednakże zaleca się dołączenie, jeśli nie można utworzyć tożsamości zarządzanej.

### <a name="modify-evaluation"></a>Modyfikuj ocenę

Modyfikacja jest szacowana przed przetworzeniem żądania przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Modyfikuj Dodawanie lub aktualizowanie tagów w zasobie, gdy spełniony **jest warunek reguły** zasad.

Gdy definicja zasad używająca efektu Modyfikuj jest uruchamiana w ramach cyklu oceny, nie wprowadza zmian do już istniejących zasobów. Zamiast tego oznacza dowolnego zasobu, który spełnia **Jeśli** warunek jako niezgodne.

### <a name="modify-properties"></a>Modyfikuj właściwości

Właściwość **Details** efektu Modyfikuj ma wszystkie właściwości, które definiują uprawnienia, które są konieczne do korygowania, oraz **operacje** , które służą do dodawania, aktualizowania lub usuwania wartości tagów.

- **roleDefinitionIds** [wymagane]
  - Ta właściwość musi zawierać tablicę ciągów, które jest zgodny z Identyfikatorem roli kontroli dostępu opartej na rolach dostępna w subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowania — konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
  - Zdefiniowana rola musi obejmować wszystkie operacje przyznane do roli [współautor](../../../role-based-access-control/built-in-roles.md#contributor) .
- **operacje** [wymagane]
  - Tablica wszystkich operacji tagów do wykonania na pasujących zasobach.
  - Właściwości:
    - **operacja** [wymagana]
      - Definiuje akcję, która ma zostać podjęta względem pasującego zasobu. Dostępne opcje to: _addOrReplace_, _Add_, _Remove_. _Dodaj_ zachowania podobne do efektu [dołączania](#append) .
    - **pole** [wymagane]
      - Tag do dodania, zastępowania lub usunięcia. Nazwy tagów muszą być zgodne z tą samą konwencją nazewnictwa dla innych [pól](./definition-structure.md#fields).
    - **wartość** (opcjonalnie)
      - Wartość, dla której ma zostać ustawiony tag.
      - Ta właściwość jest wymagana, jeśli **operacja** jest _addOrReplace_ lub _Dodaj_.

### <a name="modify-operations"></a>Modyfikuj operacje

Tablica właściwości **operacji** umożliwia zmianę kilku tagów na różne sposoby z jednej definicji zasad. Każda operacja składa się z właściwości **operacji**, **pola**i **wartości** . Operacja określa, jakie działanie ma wykonać zadanie korygowania tagów, pole określa, który znacznik jest modyfikowany, a wartość definiuje nowe ustawienie dla tego tagu. Poniższy przykład powoduje zmianę następujących tagów:

- Ustawia tag `environment` na "test", nawet jeśli istnieje już z inną wartością.
- Usuwa tag `TempResource`.
- Ustawia tag `Dept` na parametr zasad _deptname_ skonfigurowany w przypisaniu zasad.

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

Właściwość **Operation** ma następujące opcje:

|Operacja |Opis |
|-|-|
|addOrReplace |Dodaje zdefiniowany tag i wartość do zasobu, nawet jeśli tag już istnieje z inną wartością. |
|Dodawanie |Dodaje zdefiniowany tag i wartość do zasobu. |
|Usuń |Usuwa zdefiniowany tag z zasobu. |

### <a name="modify-examples"></a>Modyfikuj przykłady

Przykład 1: Dodaj tag `environment` i Zastąp istniejące Tagi `environment` "test":

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

Przykład 2: Usuń tag `env` i Dodaj tag `environment` lub Zastąp istniejące Tagi `environment` wartością sparametryzowane:

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

## <a name="deny"></a>Odmów

Odmów używany w celu zapobiegania żądania zasobów nie jest zgodna standardów zdefiniowanych za pośrednictwem definicji zasad, która kończy się niepowodzeniem żądania.

### <a name="deny-evaluation"></a>Odmów oceny

Podczas tworzenia lub aktualizowania zasobem dopasowane Odmów zapobiega żądanie przed wysłaniem do dostawcy zasobów. Żądanie jest zwracana jako `403 (Forbidden)`. W portalu można wyświetlić zabronione stanu wdrożenia, która została uniemożliwiona przez przypisanie zasad.

Podczas oceny istniejących zasobów zasobów, które pasują do definicji zasad odmowy są oznaczone jako niezgodne.

### <a name="deny-properties"></a>Odmów właściwości

Efektu odrzucenia nie ma żadnych dodatkowych właściwości do użycia w **następnie** warunek definicji zasad.

### <a name="deny-example"></a>Przykład Odmów

Przykład: Użycie efektu odrzucenia.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Inspekcja

Inspekcja służy do tworzenia to zdarzenie ostrzegawcze w dzienniku aktywności, oceniając niezgodnym zasobem, ale go nie zatrzymuje żądania.

### <a name="audit-evaluation"></a>Ocena inspekcji

Inspekcja jest ostatnim efektem sprawdzonym przez Azure Policy podczas tworzenia lub aktualizowania zasobu. Azure Policy następnie wysyła zasób do dostawcy zasobów. Inspekcja działa tak samo, dla żądania zasobów i cykl oceny. Azure Policy dodaje operację `Microsoft.Authorization/policies/audit/action` do dziennika aktywności i oznacza zasób jako niezgodny.

### <a name="audit-properties"></a>Właściwości inspekcji

Efekt inspekcji nie ma żadnych dodatkowych właściwości do użycia w **następnie** warunek definicji zasad.

### <a name="audit-example"></a>Przykład inspekcji

Przykład: Użycie efektu audytu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umożliwia inspekcję na zasoby, które odpowiadają **Jeśli** warunku, ale nie ma elementów określonych w **szczegóły** z **następnie** warunku.

### <a name="auditifnotexists-evaluation"></a>Ocena AuditIfNotExists

AuditIfNotExists uruchomiony po dostawcy zasobów ma obsługiwane żądania tworzenia lub aktualizacji zasobu i zwrócił kod stanu powodzenia. Audyt występuje, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** nie zostało oszacowane jako prawdziwe. Azure Policy dodaje operację `Microsoft.Authorization/policies/audit/action` do dziennika aktywności w taki sam sposób, jak efekt inspekcji. Po wyzwoleniu zasób, który spełnione **Jeśli** warunek jest zasobem, który jest oznaczony jako niezgodne.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

**Szczegóły** właściwość skutków AuditIfNotExists ma wszystkie właściwości, które definiują powiązanych zasobów w celu dopasowania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Jeśli **details. Type** jest typem zasobu poniżej zasobu warunku **if** , zasady zapytania dotyczące zasobów tego **typu** w zakresie szacowanego zasobu. W przeciwnym razie zapytania zasad w ramach tej samej grupy zasobów co obliczony zasób.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku dla opcji **IF. Field. Type** i **then. details. Type** są zgodne, **Nazwa** jest _wymagana_ i musi być `[field('name')]`. Jednak zamiast tego należy rozważyć efekt [inspekcji](#audit) .
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

Przykład: Ocenia maszyn wirtualnych, aby określić, jeśli rozszerzenia ochrony przed złośliwym oprogramowaniem istnieje, a następnie przeprowadza inspekcję, gdy brak.

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

Podobnie jak w przypadku AuditIfNotExists, definicja zasad DeployIfNotExists wykonuje wdrożenie szablonu po spełnieniu warunku.

> [!NOTE]
> [Zagnieżdżone szablony](../../../azure-resource-manager/templates/linked-templates.md#nested-template) są obsługiwane w przypadku **deployIfNotExists**, ale [połączone szablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nie są obecnie obsługiwane.

### <a name="deployifnotexists-evaluation"></a>Ocena DeployIfNotExists

DeployIfNotExists uruchomiony po dostawcy zasobów ma obsługiwane żądania tworzenia lub aktualizacji zasobu i zwrócił kod stanu powodzenia. Wdrożenie szablonu występuje, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** nie zostało oszacowane jako prawdziwe.

Podczas cyklu oszacowania definicji zasad z efektem DeployIfNotExists, dopasowywanie zasobów, które są oznaczone jako niezgodne, ale nie podjęto żadnej akcji dla tego zasobu.

### <a name="deployifnotexists-properties"></a>Właściwości DeployIfNotExists

Właściwość **Details** efektu DeployIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania oraz wdrożenie szablonu do wykonania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Rozpoczyna się od próby pobrania zasobu poniżej **Jeśli** zasobów warunek, a następnie zapytań w ramach tej samej grupie zasobów co **Jeśli** warunku zasobu.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku dla opcji **IF. Field. Type** i **then. details. Type** są zgodne, **Nazwa** jest _wymagana_ i musi być `[field('name')]`.
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
  - Ustawia typ wdrożenia, które ma zostać wyzwolone. _Subskrypcja_ wskazuje [wdrożenie na poziomie subskrypcji](../../../azure-resource-manager/templates/deploy-to-subscription.md) _, Grupa_ zasobów wskazuje wdrożenie w grupie.
  - W przypadku korzystania z wdrożeń na poziomie subskrypcji należy określić właściwość _Location_ we _wdrożeniu_ .
  - Wartość domyślna to _ResourceGroup_.
- **Wdrożenie** [wymagane]
  - Ta właściwość powinna zawierać wdrożenia pełnym szablonem znajdującym się, jak zostałaby przekazana do `Microsoft.Resources/deployments` umieścić interfejsu API. Aby uzyskać więcej informacji, zobacz [interfejsu API REST wdrożenia](/rest/api/resources/deployments).

  > [!NOTE]
  > Wszystkie funkcje wewnątrz **wdrożenia** właściwości są obliczane jako części szablonu nie zasad. Wyjątek stanowi **parametry** właściwość, która przekazuje wartości z zasad do szablonu. **Wartość** w tej sekcji, w ramach szablonu Nazwa parametru służy do wykonywania tej wartości, przekazując (zobacz _fullDbName_ w przykładzie DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Przykład DeployIfNotExists

Przykład: Ocenia baz danych SQL Server w celu ustalenia, czy włączono transparentDataEncryption. Jeśli nie, zostanie wykonane wdrożenie do włączenia.

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

Ten efekt jest używany z *trybem* definicji zasad `Microsoft.Kubernetes.Data`. Jest on używany do przekazywania reguł kontroli przyjęcia strażnika v3 zdefiniowanych za pomocą [Nieprzez ograniczenia środowiska](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) , aby [otworzyć agenta zasad](https://www.openpolicyagent.org/) (nieprzez) do samozarządzanej klastrów Kubernetes na platformie Azure.

> [!NOTE]
> [Azure Policy aparatu AKS](aks-engine.md) jest w publicznej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad.

### <a name="enforceopaconstraint-evaluation"></a>Ocena EnforceOPAConstraint

Otwarty kontroler przyjmowania agentów zasad umożliwia ocenę każdego nowego żądania w klastrze w czasie rzeczywistym.
Co 5 minut jest wykonywane pełne skanowanie klastra i wyniki zgłoszone do Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Właściwości EnforceOPAConstraint

Właściwość **Details** efektu EnforceOPAConstraint ma właściwości SubProperties opisujące regułę kontroli dostępu strażnik v3.

- **constraintTemplate** [wymagane]
  - Szablon ograniczenia CustomResourceDefinition (CRD), który definiuje nowe ograniczenia. Szablon definiuje logikę rego, schemat ograniczenia i parametry ograniczenia, które są przesyłane za pośrednictwem **wartości** z Azure Policy.
- **ograniczenie** [wymagane]
  - Implementacja CRD szablonu ograniczenia. Używa parametrów przekazaną przez **wartości** jako `{{ .Values.<valuename> }}`. W poniższym przykładzie `{{ .Values.cpuLimit }}` i `{{ .Values.memoryLimit }}`.
- **wartości** [opcjonalne]
  - Definiuje wszelkie parametry i wartości, które mają zostać przekazane do ograniczenia. Każda wartość musi istnieć w szablonie ograniczenia CRD.

### <a name="enforceregopolicy-example"></a>Przykład EnforceRegoPolicy

Przykład: strażnik v3 reguła kontroli w celu ustawienia limitów zasobów procesora i pamięci kontenera w aparacie AKS.

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

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Ten efekt jest używany z *trybem* definicji zasad `Microsoft.ContainerService.Data`. Służy do przekazywania reguł kontroli wpływu strażnika v2 zdefiniowanych za pomocą [rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) , aby [otworzyć agenta zasad](https://www.openpolicyagent.org/) (Nieprzez) w [usłudze Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy dla AKS](rego-for-aks.md) jest w ograniczonej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad

### <a name="enforceregopolicy-evaluation"></a>Ocena EnforceRegoPolicy

Otwarty kontroler przyjmowania agentów zasad umożliwia ocenę każdego nowego żądania w klastrze w czasie rzeczywistym.
Co 5 minut jest wykonywane pełne skanowanie klastra i wyniki zgłoszone do Azure Policy.

### <a name="enforceregopolicy-properties"></a>Właściwości EnforceRegoPolicy

Właściwość **Details** efektu EnforceRegoPolicy ma właściwości, które opisują regułę kontroli dostępu strażnik v2.

- **policyId** [wymagane]
  - Unikatowa nazwa przenoszona jako parametr do reguły rego Admission Control.
- **zasady** [wymagane]
  - Określa identyfikator URI reguły rego Admission Control.
- **policyParameters** [opcjonalnie]
  - Definiuje wszelkie parametry i wartości, które mają zostać przekazane do zasad regoymi.

### <a name="enforceregopolicy-example"></a>Przykład EnforceRegoPolicy

Przykład: strażnika v2 reguła kontroli, aby zezwalać tylko na określone obrazy kontenerów w AKS.

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

Zasób może mieć wpływ kilka przypisania. W tym samym zakresie lub w różnych zakresach, może być tych przydziałów. Każdy z tych przydziałów jest również mogą mieć różne efekty zdefiniowane. Warunek i efekt dla każdej zasady niezależnie jest oceniany. Przykład:

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

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
