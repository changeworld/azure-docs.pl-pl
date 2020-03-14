---
title: Informacje o działaniu efektów
description: Definicje Azure Policy mają różne skutki, które określają sposób zarządzania i zgłaszania zgodności.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 502c8a87c4e915ebd1fd764915daa9c89a307097
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281186"
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
- [Zmodyfikować](#modify)

## <a name="order-of-evaluation"></a>Kolejność obliczania

Żądania utworzenia lub zaktualizowania zasobu za pomocą Azure Resource Manager są oceniane przez Azure Policy pierwsze. Azure Policy tworzy listę wszystkich przypisań, które są stosowane do zasobu, a następnie szacuje zasób dla każdej definicji. Azure Policy przetwarza kilka efektów przed przekazaniem żądania do odpowiedniego dostawcy zasobów. Wykonanie tej czynności zapobiega niepotrzebnemu przetwarzaniu przez dostawcę zasobów, gdy zasób nie spełnia zaprojektowanych kontrolek ładu Azure Policy.

- **Wyłączone** jest najpierw zaznaczone, aby określić, czy reguła zasad powinna zostać oceniona.
- Następnie są **oceniane i** **modyfikowane** . Ponieważ może on zmienić żądanie, wprowadzona zmiana może uniemożliwić wywoływanie inspekcji lub skutków odmowy.
- Następnie zostanie obliczone **odmowa** . Oceniając odmówić przed inspekcji, double rejestrowanie niepożądane zasobów nie będzie mógł.
- **Inspekcja** jest następnie oceniana przed żądaniem przechodzenia do dostawcy zasobów.

Gdy dostawca zasobów zwróci kod sukcesu, **AuditIfNotExists** i **DeployIfNotExists** , aby określić, czy wymagane jest dodatkowe rejestrowanie zgodności lub akcja.

Obecnie nie ma żadnych kolejności oceny dla efektów **EnforceOPAConstraint** lub **EnforceRegoPolicy** .

## <a name="disabled"></a>Wyłączono

Efekt jest przydatna do testowania sytuacji lub gdy definicja zasad ma sparametryzowane efekt. Ta elastyczność sprawia, że można wyłączyć jednego przypisania zamiast wyłączać wszystkie przypisania tej zasady.

Alternatywą dla wyłączonego efektu jest **wymuszmode** , który jest ustawiony w przypisaniu zasad.
Gdy **wymuszanie** jest _wyłączone_, nadal są oceniane zasoby. Rejestrowanie, takie jak dzienniki aktywności, i efekt zasad nie wystąpi. Aby uzyskać więcej informacji, zobacz [Tryb wymuszania przypisywania zasad](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Append

Dołącz służy do dodania kolejnych pól do żądanego zasobu podczas jej tworzenia lub aktualizacji. Typowym przykładem jest określenie dozwolonych adresów IP dla zasobu magazynu.

> [!IMPORTANT]
> Dołączenie jest przeznaczone do użycia z właściwościami niebędącymi tagami. Podczas gdy Dołączanie może dodawać Tagi do zasobu w trakcie żądania utworzenia lub aktualizacji, zaleca się użycie zamiast nich efektów [modyfikacji](#modify) dla tagów.

### <a name="append-evaluation"></a>Dołącz oceny

Dołącz ocenia przed żądania przetwarzane przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodaje pola do zasobu, gdy spełniony **jest warunek reguły** zasad. Jeśli efekt Dołącz przesłonić wartość oryginalne żądanie, podając inną wartość, następnie go działa jako efektu odrzucenia i odrzuca żądanie. Aby dołączyć nową wartość do istniejącej tablicy, użyj wersji **[\*]** aliasu.

Po uruchomieniu definicji zasad przy użyciu efektu dołączania w ramach cyklu oceny go nie zmieniać zasoby, które już istnieją. Zamiast tego oznacza wszelkie zasoby, które spełniają warunek **if** jako niezgodne.

### <a name="append-properties"></a>Dołącz właściwości

Efekt dołączania zawiera tylko tablicę **szczegółów** , która jest wymagana. Ponieważ **szczegóły** są tablicami, może przyjmować jedną parę **pól/wartości** lub wiele. Zapoznaj się ze [strukturą definicji](definition-structure.md#fields) , aby uzyskać listę akceptowalnych pól.

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

Gdy definicja zasad używająca efektu Modyfikuj jest uruchamiana w ramach cyklu oceny, nie wprowadza zmian do już istniejących zasobów. Zamiast tego oznacza wszelkie zasoby, które spełniają warunek **if** jako niezgodne.

### <a name="modify-properties"></a>Modyfikuj właściwości

Właściwość **Details** efektu Modyfikuj ma wszystkie właściwości, które definiują uprawnienia, które są konieczne do korygowania, oraz **operacje** , które służą do dodawania, aktualizowania lub usuwania wartości tagów.

- **roleDefinitionIds** [wymagane]
  - Ta właściwość musi zawierać tablicę ciągów, które jest zgodny z Identyfikatorem roli kontroli dostępu opartej na rolach dostępna w subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — Konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
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
|Dodaj |Dodaje zdefiniowany tag i wartość do zasobu. |
|Usuwanie |Usuwa zdefiniowany tag z zasobu. |

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

Podczas tworzenia lub aktualizowania zasobem dopasowane Odmów zapobiega żądanie przed wysłaniem do dostawcy zasobów. Żądanie jest zwracane jako `403 (Forbidden)`. W portalu można wyświetlić zabronione stanu wdrożenia, która została uniemożliwiona przez przypisanie zasad.

Podczas oceny istniejących zasobów zasobów, które pasują do definicji zasad odmowy są oznaczone jako niezgodne.

### <a name="deny-properties"></a>Odmów właściwości

Efekt odmowy nie ma żadnych dodatkowych właściwości do użycia w warunku **then** definicji zasad.

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

Efekt inspekcji nie ma żadnych dodatkowych właściwości do użycia w warunku **then** definicji zasad.

### <a name="audit-example"></a>Przykład inspekcji

Przykład: Użycie efektu audytu.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists włącza inspekcję zasobów, które pasują do warunku **if** , ale nie mają składników określonych w **szczegółach** warunku **then** .

### <a name="auditifnotexists-evaluation"></a>Ocena AuditIfNotExists

AuditIfNotExists uruchomiony po dostawcy zasobów ma obsługiwane żądania tworzenia lub aktualizacji zasobu i zwrócił kod stanu powodzenia. Inspekcja występuje, gdy nie ma żadnych powiązanych zasobów lub jeśli zasoby zdefiniowane przez **ExistenceCondition** nie są oceniane na wartość true. Azure Policy dodaje operację `Microsoft.Authorization/policies/audit/action` do dziennika aktywności w taki sam sposób, jak efekt inspekcji. Gdy wyzwalane, zasób, który spełnił warunek **if** , jest zasobem oznaczonym jako niezgodny.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

Właściwość **Details** efektów AuditIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Jeśli **details. Type** jest typem zasobu poniżej zasobu warunku **if** , zasady zapytania dotyczące zasobów tego **typu** w zakresie szacowanego zasobu. W przeciwnym razie zapytania zasad w ramach tej samej grupy zasobów co obliczony zasób.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku dla opcji **IF. Field. Type** i **then. details. Type** są zgodne, **Nazwa** jest _wymagana_ i musi być `[field('name')]`. Jednak zamiast tego należy rozważyć efekt [inspekcji](#audit) .
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu pochodzić z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - Wartość domyślna to grupa zasobów warunku **if** .
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcja_ i _resourceName_.
  - Ustawia zakres, gdzie można pobrać powiązanego zasobu, aby dopasować z.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - W _przypadku grupy_zasobów należy ograniczyć liczbę do zasobu warunku **if** lub grupy zasobów określonej w **ResourceGroupName**.
  - W przypadku _subskrypcji_program wysyła zapytanie do całej subskrypcji powiązanego zasobu.
  - Wartość domyślna to _resourceName_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełnią efekt i nie wyzwalają inspekcji.
  - Używa tego samego języka, co reguła zasad dla warunku **if** , ale jest oceniane osobno dla każdego powiązanego zasobu.
  - Jeśli wszystkie dopasowania powiązanego zasobu zwraca wartość true, efekt jest spełniony i nie spowoduje wyzwolenia inspekcji.
  - Można użyć [Field ()], aby sprawdzić równoważność z wartościami w warunku **if** .
  - Można na przykład użyć do sprawdzenia, czy zasób nadrzędny (w warunku **if** ) znajduje się w tej samej lokalizacji zasobu co pasujący zasób powiązany.

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
> [Szablony zagnieżdżone](../../../azure-resource-manager/templates/linked-templates.md#nested-template) są obsługiwane w programie **deployIfNotExists**, ale [połączone szablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nie są obecnie obsługiwane.

### <a name="deployifnotexists-evaluation"></a>Ocena DeployIfNotExists

DeployIfNotExists uruchamia około 15 minut od momentu, gdy dostawca zasobów obsłużył żądanie utworzenia lub aktualizacji zasobu i zwrócił kod stanu sukcesu. Wdrożenie szablonu występuje, jeśli nie ma żadnych powiązanych zasobów lub jeśli zasoby zdefiniowane przez **ExistenceCondition** nie są oceniane na wartość true.
Czas trwania wdrożenia zależy od złożoności zasobów zawartych w szablonie.

Podczas cyklu oszacowania definicji zasad z efektem DeployIfNotExists, dopasowywanie zasobów, które są oznaczone jako niezgodne, ale nie podjęto żadnej akcji dla tego zasobu.

### <a name="deployifnotexists-properties"></a>Właściwości DeployIfNotExists

Właściwość **Details** efektu DeployIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania oraz wdrożenie szablonu do wykonania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Uruchamia się, próbując pobrać zasób poniżej zasobu warunku **if** , a następnie wykonać zapytania w tej samej grupie zasobów co zasób warunku **if** .
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku dla opcji **IF. Field. Type** i **then. details. Type** są zgodne, **Nazwa** jest _wymagana_ i musi być `[field('name')]`.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu pochodzić z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - Wartość domyślna to grupa zasobów warunku **if** .
  - Jeśli wdrożenie szablonu jest wykonywane, jest ona wdrożona w grupie zasobów tej wartości.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcja_ i _resourceName_.
  - Ustawia zakres, gdzie można pobrać powiązanego zasobu, aby dopasować z.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - W _przypadku grupy_zasobów należy ograniczyć liczbę do zasobu warunku **if** lub grupy zasobów określonej w **ResourceGroupName**.
  - W przypadku _subskrypcji_program wysyła zapytanie do całej subskrypcji powiązanego zasobu.
  - Wartość domyślna to _resourceName_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełnią skutek i nie wyzwalają wdrożenia.
  - Używa tego samego języka, co reguła zasad dla warunku **if** , ale jest oceniane osobno dla każdego powiązanego zasobu.
  - Jeśli wszystkie dopasowania powiązanego zasobu zwraca wartość true, efekt jest spełniony i nie spowoduje wyzwolenia wdrożenia.
  - Można użyć [Field ()], aby sprawdzić równoważność z wartościami w warunku **if** .
  - Można na przykład użyć do sprawdzenia, czy zasób nadrzędny (w warunku **if** ) znajduje się w tej samej lokalizacji zasobu co pasujący zasób powiązany.
- **roleDefinitionIds** [wymagane]
  - Ta właściwość musi zawierać tablicę ciągów, które jest zgodny z Identyfikatorem roli kontroli dostępu opartej na rolach dostępna w subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — Konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcja_ i _resourceName_.
  - Ustawia typ wdrożenia, które ma zostać wyzwolone. _Subskrypcja_ wskazuje [wdrożenie na poziomie subskrypcji](../../../azure-resource-manager/templates/deploy-to-subscription.md) _, Grupa_ zasobów wskazuje wdrożenie w grupie.
  - W przypadku korzystania z wdrożeń na poziomie subskrypcji należy określić właściwość _Location_ we _wdrożeniu_ .
  - Wartość domyślna to _resourceName_.
- **Wdrożenie** [wymagane]
  - Ta właściwość powinna obejmować pełne wdrożenie szablonu, ponieważ zostanie przesłane do interfejsu API `Microsoft.Resources/deployments` PUT. Aby uzyskać więcej informacji, zobacz [interfejs API REST wdrożeń](/rest/api/resources/deployments).

  > [!NOTE]
  > Wszystkie funkcje wewnątrz właściwości **wdrożenia** są oceniane jako składniki szablonu, a nie zasady. Wyjątkiem jest właściwość **Parameters** , która przekazuje wartości z zasad do szablonu. **Wartość** w tej sekcji w kolumnie Nazwa parametru szablonu służy do przekazywania tej wartości (zobacz _FullDbName_ w przykładzie DeployIfNotExists).

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

Każdego przydziału jest szacowana osobno. Jako takie nie ma szansę zasobu do dostawy za pośrednictwem przerwa z różnic w zakresie. Wynik sieci zasad dotyczących warstw lub zasad nakładania się na siebie jest traktowany jako **skumulowany najbardziej restrykcyjny**. Na przykład efektu odrzucenia, gdyby obie zasady 1 i 2 zasobem zostałby zablokowany za pomocą zasad nakładających się i powodujące konflikt. Jeśli nadal potrzebujesz zasób, który ma być tworzonych w zakresie docelowym, przejrzyj wykluczenia na każdego przypisania, aby sprawdzić odpowiednie zasady mają wpływ na odpowiednie zakresy.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
