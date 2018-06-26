---
title: Opis skutków zasad Azure
description: Azure definicji zasad efekty różnych określające sposób zarządzania i podać zgodności.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 1566cf2b61749121c4eaff5a32b0a940f3341f7e
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751782"
---
# <a name="understanding-policy-effects"></a>Opis skutków zasad

Każda definicja zasad w zasadach Azure ma wpływ pojedynczego określający, co się dzieje podczas skanowania, kiedy **Jeśli** segmentu reguły jest obliczane na niezgodny z zasobem skanowania. Skutki mogą także zachowywać się inaczej w takim przypadku nowy zasób, zaktualizowanego zasobu lub istniejący zasób.

Brak aktualnie pięć efekty, które są obsługiwane w definicji zasad:

- Append
- Inspekcja
- AuditIfNotExists
- Zablokuj
- DeployIfNotExists

## <a name="order-of-evaluation"></a>Kolejność obliczania

Po wysłaniu żądania do tworzenia lub aktualizowania zasobów za pomocą usługi Azure Resource Manager przetwarzania zasad kilka skutków przed przekazanie żądania do odpowiedniego dostawcę zasobów.
Pozwala to zapobiec niepotrzebnych przetwarzania przez dostawcę zasobów, jeśli zasób nie spełnia kontroli zaprojektowanych ładu zasad. Zasady tworzy listę wszystkich definicji zasad przypisane przy użyciu zasad lub inicjatywy przypisania przez zakres (minus wykluczenia) zasobu i przygotowuje się do oceny względem każdej definicji zasobu.

- **Dołącz** jest szacowana jako pierwsza. Ponieważ Dołącz może zmienić żądania, zmiany wprowadzone przez Dołącz może zapobiec inspekcji lub uniemożliwić wyzwalania efekt.
- **Odmów** jest oceniane oddzielnie. Wyniku obliczenia odmowy przed inspekcji, double rejestrowanie niepożądane zasobów nie będzie mógł.
- **Inspekcji** następnie jest oceniane przed żądanie, przechodząc do dostawcy zasobów.

Po żądaniu podano dostawcy zasobów i dostawcy zasobów zwraca kod stanu powodzenia **AuditIfNotExists** i **DeployIfNotExists** są obliczane, aby określić, czy monitowania Rejestrowanie zgodności lub akcji jest wymagana.

## <a name="append"></a>Append

Dołącz służy do dodawania dodatkowych pól do żądanego zasobu, podczas tworzenia lub aktualizacji. Może być przydatne w przypadku dodawania tagów na zasoby, takie jak costCenter lub określanie dozwolone adresy IP dla zasobu magazynu.

### <a name="append-evaluation"></a>Dołącz oceny

Jak wspomniano, Dołącz ocenia przed żądania przetwarzane przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodaje następującą liczbę pól: do zasobu podczas **Jeśli** warunek reguły jest spełniony. Jeśli efekt append spowoduje zastąpienie jako wartość w oryginalnego żądania, podając inną wartość, następnie je zachowuje się jak efekt Odmów i odrzucić żądanie.

Po uruchomieniu definicję zasad za pomocą efektu dołączania w ramach cykl oceny go nie zmieniać zasoby, które już istnieją. Zamiast tego oznacza dowolnego zasobu, który spełnia **Jeśli** warunku jako niezgodne.

### <a name="append-properties"></a>Dołączanie właściwości

Ma wpływ append **szczegóły** tablicy, która jest wymagana. Jako **szczegóły** jest tablicą, może upłynąć pojedyncze **pól i wartości** pary lub wielokrotności. Zapoznaj się [definicji zasad](policy-definition.md#fields) dla listy pól akceptowane.

### <a name="append-examples"></a>Dołącz przykłady

Przykład 1: Pojedynczy **pól i wartości** pary, aby można było dodać tag.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Przykład 2: Wiele **pól i wartości** pary do dołączenia zestawu tagów.

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

Przykład 3: Pojedynczy **pól i wartości** łączyć się przy użyciu [alias](policy-definition.md#aliases) z tablicą **wartość** konfigurowania reguł IP dla konta magazynu.

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

## <a name="deny"></a>Zablokuj

Odmów używany w celu uniknięcia żądanie zasobów, które nie odpowiada żądanej standardów za pośrednictwem definicji zasad i nie powiodło się żądanie.

### <a name="deny-evaluation"></a>Odmów oceny

Podczas tworzenia lub aktualizowania zasobu, Odmów blokuje żądanie przed wysłaniem do dostawcy zasobów. Żądanie jest zwracana jako 403 (Dostęp zabroniony). W portalu można wyświetlić stanu wdrożenia, która nie została uruchomiona z powodu przypisania zasad zabroniony.

Podczas cyklu oceny program definicje zasad z efektem Odmów zgodne zasoby są oznaczone jako niezgodna, ale żadna akcja jest wykonywana na tego zasobu.

### <a name="deny-properties"></a>Odmów właściwości

Efekt Odmów nie ma żadnych dodatkowych właściwości do użycia w **następnie** warunku definicji zasad.

### <a name="deny-example"></a>Przykład Odmów

Przykład: Za pomocą efektu Odmów.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Inspekcja

Efekt inspekcji służy do tworzenia to zdarzenie ostrzegawcze w dzienniku inspekcji, gdy niezgodnych zasobów jest obliczane, ale nie zatrzymuje żądania.

### <a name="audit-evaluation"></a>Ocena inspekcji

Efekt inspekcji jest ostatnie do uruchomienia podczas tworzenia lub aktualizacji zasobów przed zasobu jest wysyłane do dostawcy zasobów. Inspekcja działa tak samo żądanie zasobu i cykl oceny i wykonuje `Microsoft.Authorization/policies/audit/action` operacji dziennik aktywności. W obu przypadkach zasobu jest oznaczony jako niezgodne.

### <a name="audit-properties"></a>Właściwości inspekcji

Efekt inspekcji nie ma żadnych dodatkowych właściwości do użycia w **następnie** warunku definicji zasad.

### <a name="audit-example"></a>Przykład inspekcji

Przykład: Za pomocą efektu inspekcji.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

Umożliwia AuditIfNotExists inspekcji na zasobie, który odpowiada **Jeśli** warunku, ale nie ma elementów określonych w **szczegóły** z **następnie** warunku.

### <a name="auditifnotexists-evaluation"></a>Ocena AuditIfNotExists

AuditIfNotExists uruchomiony po dostawcy zasobów obsłużyła tworzenia lub aktualizacji żądania do zasobu i zwrócił kod stanu Powodzenie. Efekt zostanie wywołany, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** oszacowane jako prawdziwe. Po wyzwoleniu efekt `Microsoft.Authorization/policies/audit/action` wykonaniem operacji do dziennika aktywności w taki sam sposób jak efekt inspekcji. Po wyzwoleniu zasób, który spełnia **Jeśli** warunek jest zasobem, który jest oznaczony jako niezgodne.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

**Szczegóły** właściwość skutków AuditIfNotExists ma wszystkie właściwości definiujące powiązane zasoby do dopasowania.

- **Typ** [wymagane]
  - Określa typ zasobu powiązanego do dopasowania.
  - Rozpoczyna się od próby pobrania zasobów poniżej **Jeśli** warunku zasobu, a następnie kwerend w tej samej grupie zasobów co **Jeśli** warunku zasobu.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu, aby dopasować oraz powoduje, że zasady, które można pobrać określonego zasobu jeden zamiast wszystkich zasobów określonego typu.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie zasobu powiązanego pochodzą z innej grupie zasobów.
  - Nie ma zastosowania, jeśli **typu** jest zasobów, które będą pod **Jeśli** warunku zasobu.
  - Domyślnie jest **Jeśli** warunku zasobu, grupy zasobów.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcji_ i _ResourceGroup_.
  - Ustawia zakres, gdzie można pobrać powiązanych zasobów do dopasowania z.
  - Nie ma zastosowania, jeśli **typu** jest zasobów, które będą pod **Jeśli** warunku zasobu.
  - Dla _ResourceGroup_, będzie ograniczona do **Jeśli** warunku zasobu, grupy zasobów lub grupy zasobów określonej w **ResourceGroupName**.
  - Aby uzyskać _subskrypcji_, wysyła zapytanie do całej subskrypcji dla zasobu powiązanego.
  - Domyślnie jest _ResourceGroup_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełnia wpływu i nie będzie wyzwalać inspekcji.
  - Używa tego samego języka reguły zasad dla **Jeśli** warunku, ale jest indywidualnie obliczone dla każdego zasobu powiązanego.
  - Jeśli wszystkie dopasowania zasobu powiązanego zwraca wartość true, efekt jest spełniony i nie powoduje wyzwolenia inspekcji.
  - Umożliwia [field()] Sprawdź równoważność wartości w **Jeśli** warunku.
  - Na przykład można sprawdzić, czy zasobu nadrzędnego (w **Jeśli** warunku) znajduje się w tej samej lokalizacji zasobu jako zgodnego zasobu powiązanego.

### <a name="auditifnotexists-example"></a>Przykład AuditIfNotExists

Przykład: Oblicza maszyn wirtualnych, aby określić, jeśli rozszerzenie ochrony przed złośliwym kodem istnieje, a następnie inspekcji w przypadku braku.

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

Podobnie jak AuditIfNotExists, wdrażania szablonu wykonuje DeployIfNotExists, gdy warunek jest spełniony.

### <a name="deployifnotexists-evaluation"></a>Ocena DeployIfNotExists

DeployIfNotExists również uruchamiane po dostawcy zasobów obsłużyła tworzenia lub aktualizacji żądania do zasobu i zwrócił kod stanu Powodzenie. Efekt zostanie wywołany, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** oszacowane jako prawdziwe. Po wyzwoleniu efekt jest wykonywany wdrażania szablonu.

Podczas cyklu oceny definicje zasad z efektem DeployIfNotExists zgodne zasoby są oznaczone jako niezgodne, ale żadna akcja jest wykonywana na tego zasobu.

### <a name="deployifnotexists-properties"></a>Właściwości DeployIfNotExists

**Szczegóły** właściwość skutków DeployIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania i wdrożenie szablonu w celu wykonania.

- **Typ** [wymagane]
  - Określa typ zasobu powiązanego do dopasowania.
  - Rozpoczyna się od próby pobrania zasobów poniżej **Jeśli** warunku zasobu, a następnie kwerend w tej samej grupie zasobów co **Jeśli** warunku zasobu.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu, aby dopasować oraz powoduje, że zasady, które można pobrać określonego zasobu jeden zamiast wszystkich zasobów określonego typu.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie zasobu powiązanego pochodzą z innej grupie zasobów.
  - Nie ma zastosowania, jeśli **typu** jest zasobów, które będą pod **Jeśli** warunku zasobu.
  - Domyślnie jest **Jeśli** warunku zasobu, grupy zasobów.
  - Jeśli wdrożenie szablonu jest wykonywane, jest wdrażana w grupie zasobów tej wartości.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcji_ i _ResourceGroup_.
  - Ustawia zakres, gdzie można pobrać powiązanych zasobów do dopasowania z.
  - Nie ma zastosowania, jeśli **typu** jest zasobów, które będą pod **Jeśli** warunku zasobu.
  - Dla _ResourceGroup_, będzie ograniczona do **Jeśli** warunku zasobu, grupy zasobów lub grupy zasobów określonej w **ResourceGroupName**.
  - Aby uzyskać _subskrypcji_, wysyła zapytanie do całej subskrypcji dla zasobu powiązanego.
  - Domyślnie jest _ResourceGroup_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełnia wpływu i nie będzie wyzwalać wdrożenia.
  - Używa tego samego języka reguły zasad dla **Jeśli** warunku, ale jest indywidualnie obliczone dla każdego zasobu powiązanego.
  - Jeśli wszystkie dopasowania zasobu powiązanego zwraca wartość true, efekt jest spełniony i nie powoduje wyzwolenia wdrożenia.
  - Umożliwia [field()] Sprawdź równoważność wartości w **Jeśli** warunku.
  - Na przykład można sprawdzić, czy zasobu nadrzędnego (w **Jeśli** warunku) znajduje się w tej samej lokalizacji zasobu jako zgodnego zasobu powiązanego.
- **Wdrożenie** [wymagane]
  - Ta właściwość powinna zawierać wdrożenia pełny szablon jako zostałyby przekazane do `Microsoft.Resources/deployments` PUT interfejsu API. Aby uzyskać więcej informacji, zobacz [interfejsu API REST wdrożeń](/rest/api/resources/deployments).

  > [!NOTE]
  > Wszystkie funkcje wewnątrz **wdrożenia** właściwości są oceniane jako części szablonu nie zasad. Wyjątek stanowi **parametry** właściwość, która przekazuje wartości z zasad do szablonu. **Wartość** w tej sekcji, na podstawie szablonu Nazwa parametru służy do wykonywania tej wartości przekazywanie (zobacz _fullDbName_ w przykładzie DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Przykład DeployIfNotExists

Przykład: Oblicza baz danych serwera SQL, aby określić, czy transparentDataEncryption jest włączony. Jeśli nie, następnie jest wykonywany wdrożenia, aby je włączyć.

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
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="layering-policies"></a>Tworzenie warstw zasad

Zasób może mieć wpływ na wiele przydziałów. Te przydziały można w tym samym zakresie (określonego zasobu, grupy zasobów, subskrypcji lub grupy zarządzania) lub w innych zakresach. Każdy z tych przydziałów jest również mogące mieć wpływ różne zdefiniowane. Niezależnie od tego niezależnie od siebie oceny warunku i efekt dla każdej z zasad (przypisane bezpośrednio lub w ramach inicjatywy). Na przykład jeśli zasady 1 zawiera warunek, który ogranicza lokalizacji zasobów subskrypcji A można tworzyć tylko w "westus" z mocą Odmów i zasady 2 ma być warunek, który ogranicza tylko lokalizacja zasobu dla grupy zasobów B (czyli w subskrypcji A) do utworzone w "eastus" w tym celu inspekcji są przypisane do wyniku wynikowy będzie::

- Żaden zasób już w grupie zasobów B w "eastus" jest zgodne z zasadami 2, ale jest oznaczony jako niezgodne z zasadami 1.
- Żaden zasób już w grupie zasobów B nie znajduje się w "eastus" zostaną oznaczone jako niezgodne z zasadami 2 i może być również oznaczone jako nie zgodne z zasadami 1, jeśli nie "westus".
- Wszystkich nowych zasobów w ramach subskrypcji, A nie w "westus" zostałyby odrzucone przez zasady 1.
- Dowolnego nowego zasobu w subskrypcji A / grupy zasobów B w "westus" może być oznaczony jako niezgodne na zasadzie 2, ale zostałyby utworzone (zgodne z zasady 1 i 2 zasad jest inspekcji i nie odmowy).

Jeśli ma wpływ zarówno zasady 2, jak i zasady 1 z Odmów, zmienić sytuacji:

- Żaden zasób już w grupie zasobów B nie znajduje się w "eastus" zostaną oznaczone jako niezgodne z zasadami 2.
- Żaden zasób już w grupie zasobów B nie znajduje się w "westus" zostaną oznaczone jako niezgodne z zasadami 1.
- Wszystkich nowych zasobów w ramach subskrypcji, A nie w "westus" zostałyby odrzucone przez zasady 1.
- Dowolnego nowego zasobu w subskrypcji A / grupy zasobów B zostałyby odrzucone (od momentu jego lokalizacji nigdy nie może spełnić zarówno zasady 2, jak i zasady 1).

Jak każdego przydziału jest szacowana osobno, nie ma możliwości zasobów do dostawy za pośrednictwem przerwę z powodu różnic w zakresie. W związku z tym wynikiem Tworzenie warstw zasad lub nakładają się na siebie zasad jest uznawane za **zbiorczą najbardziej restrykcyjne**. Innymi słowy zasób, który ma zostać utworzony mogą zostać zablokowane z powodu zasad nakładających się i powodujące konflikt, takich jak w powyższym przykładzie, jeśli zarówno zasady 2, jak i zasady 1 miały wpływ Odmów. Jeśli nadal potrzebujesz zasobu do utworzenia w zakresie docelowego, przejrzyj wykluczenia na poszczególnych przydziałów upewnij się, że prawo zasady mają wpływ na prawo zakresów.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz lepiej zrozumieć zasady definicji efekty, przejrzyj Przykłady zasad:

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](json-samples.md).
