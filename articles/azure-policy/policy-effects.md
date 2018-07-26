---
title: Omówienie usługi Azure Policy efekty
description: Definicja zasad platformy Azure ma różne efekty, określające sposób zarządzania i podać zgodności.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 17ad631e2441e4b8d6314557c17be143fd2f3de0
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248730"
---
# <a name="understanding-policy-effects"></a>Opis zasad efekty

Każda definicja zasad w zasadach usługi Azure ma wpływ jednego, który określa, co się dzieje podczas skanowania, kiedy **Jeśli** segmentu reguła zasad jest oceniany w celu on niezgodny z zasobem skanowania. Efekty można także zachowywać się inaczej, jeśli są one dla nowego zasobu, zasób zaktualizowane lub istniejący zasób.

Obecnie istnieją pięciu efektów, które są obsługiwane w definicji zasad:

- Append
- Inspekcja
- AuditIfNotExists
- Zablokuj
- DeployIfNotExists (dostępne tylko **wbudowanych** zasad)

## <a name="order-of-evaluation"></a>Kolejność obliczania

Po wysłaniu żądania do tworzenia lub aktualizowania zasobów za pomocą usługi Azure Resource Manager, zasady są przetwarzane kilka efektów przed przekazanie żądania do odpowiedniego dostawcy zasobów.
Uniemożliwi to niepotrzebne przetwarzania przez dostawcę zasobów, gdy zasób nie spełnia zaprojektowane nadzoru kontrolki zasad. Zasady tworzy listę wszystkie definicje zasad przypisany przez przypisanie inicjatywy lub zasad, które dotyczą według zakresu (minus wykluczenia) zasobu i przygotowuje się do oceny zasobu wobec każdej definicji.

- **Dołącz** jest stosowana jako pierwsza. Ponieważ Dołącz może zmienić żądania, zmiany wprowadzone przez dołączanie może uniemożliwić inspekcji lub odmówić efekt wraz z.
- **Odmów** jest następnie oceniany. Oceniając odmówić przed inspekcji, double rejestrowanie niepożądane zasobów nie będzie mógł.
- **Inspekcja** jest następnie oceniany przed żądanie, przechodząc do dostawcy zasobów.

Gdy żądanie znajduje się na potrzeby dostawcy zasobów i dostawcy zasobów zwraca kod stanu powodzenia **AuditIfNotExists** i **DeployIfNotExists** są obliczane, aby określić, czy monitowania wymagana jest zgodność rejestrowania lub akcji.

## <a name="append"></a>Append

Dołącz służy do dodania kolejnych pól do żądanego zasobu podczas jej tworzenia lub aktualizacji. Może być przydatne w przypadku dodawania tagów na zasoby, takie jak costCenter lub określając dozwolone adresy IP dla zasobów magazynu.

### <a name="append-evaluation"></a>Dołącz oceny

Jak wspomniano wcześniej, Dołącz ocenia przed żądania przetwarzane przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodanie pola do zasobu podczas **Jeśli** warunek reguły jest spełniony. Jeśli efekt Dołącz przesłonić wartość oryginalne żądanie, podając inną wartość, następnie go działa jako efektu odrzucenia i odrzucić żądanie.

Po uruchomieniu definicji zasad przy użyciu efektu dołączania w ramach cyklu oceny nie powoduje ona zmiany do zasobów, które już istnieją. Zamiast tego oznacza dowolnego zasobu, który spełnia **Jeśli** warunek jako niezgodne.

### <a name="append-properties"></a>Dołącz właściwości

Ma jedynie wpływ dołączania **szczegóły** tablicy, która jest wymagana. Jako **szczegóły** jest tablicą, może upłynąć, może to być pojedynczy **pól i wartości** pary lub wielokrotności węzła. Zapoznaj się [definicji zasad](policy-definition.md#fields) dla listy pól dopuszczalne.

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

Przykład 3: Pojedynczy **pól i wartości** Sparuj przy użyciu [alias](policy-definition.md#aliases) z tablicą **wartość** do konfigurowania reguł adresów IP na koncie magazynu.

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

Odmów używany w celu zapobiegania żądania zasobów nie jest zgodna z odpowiednią standardów za pośrednictwem definicji zasad, która kończy się niepowodzeniem żądania.

### <a name="deny-evaluation"></a>Odmów oceny

Podczas tworzenia lub aktualizowania zasobu, Odmów zapobiega żądania przed są wysyłane do dostawcy zasobów. Żądanie jest zwracana jako 403 (zabronione). W portalu można wyświetlić zabronione stanu wdrożenia, która nie została uruchomiona z powodu przypisania zasad.

Podczas cyklu oszacowania definicji zasad przy użyciu efektu odrzucenia, dopasowywanie zasobów, które są oznaczone jako niezgodne, ale na taki zasób jest wykonywana żadna akcja.

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

Efekt inspekcji służy do tworzenia to zdarzenie ostrzegawcze w dzienniku aktywności, gdy niezgodnych zasobów jest obliczane, ale nie zatrzymuje żądania.

### <a name="audit-evaluation"></a>Ocena inspekcji

Efekt inspekcji jest ostatnim do uruchomienia podczas tworzenia lub aktualizacji zasobu przed zasobu są wysyłane do dostawcy zasobów. Działa tak samo dla żądania zasobów i cykl oceny, inspekcji i wykonuje `Microsoft.Authorization/policies/audit/action` operacji z dziennikiem aktywności. W obu przypadkach zasobu jest oznaczone jako niezgodne.

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

AuditIfNotExists umożliwia inspekcję na zasób, który odpowiada **Jeśli** warunku, ale nie ma elementów określonych w **szczegóły** z **następnie** warunku.

### <a name="auditifnotexists-evaluation"></a>Ocena AuditIfNotExists

AuditIfNotExists uruchomiony po dostawcy zasobów ma obsługiwane żądania tworzenia lub aktualizacji do zasobu i zwrócił kod stanu powodzenia. Efekt jest wyzwalany, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** zostało oszacowane jako prawdziwe. Po wyzwoleniu efekt `Microsoft.Authorization/policies/audit/action` operacja z dziennikiem aktywności jest wykonywana w taki sam sposób jak efektu audytu. Po wyzwoleniu zasób, który spełnione **Jeśli** warunek jest zasobem, który jest oznaczony jako niezgodne.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

**Szczegóły** właściwość skutków AuditIfNotExists ma wszystkie właściwości, które definiują powiązanych zasobów w celu dopasowania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Rozpoczyna się od próby pobrania zasobu poniżej **Jeśli** zasobów warunek, a następnie zapytań w ramach tej samej grupie zasobów co **Jeśli** warunku zasobu.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
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
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełniający efekt i nie będzie wyzwalać inspekcji.
  - Używa tego samego języka co reguły dla **Jeśli** warunku, ale jest obliczany osobno dla poszczególnych powiązanych zasobów.
  - Jeśli wszystkie dopasowania powiązanego zasobu zwraca wartość true, efekt jest spełniony i nie będzie wyzwalać inspekcji.
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

Podobnie jak AuditIfNotExists, wdrożenie szablonu wykonuje DeployIfNotExists, gdy warunek jest spełniony.

> [!WARNING]
> DeployIfNotExists jest dostępna tylko dla **wbudowanych** zasad.

### <a name="deployifnotexists-evaluation"></a>Ocena DeployIfNotExists

DeployIfNotExists również jest uruchamiane po dostawcy zasobów obsłużonych tworzenia lub aktualizacji żądania do zasobu i zwrócił kod stanu powodzenia. Efekt jest wyzwalany, jeśli nie ma żadnych powiązanych zasobów lub zasoby zdefiniowane przez **ExistenceCondition** zostało oszacowane jako prawdziwe. Po wyzwoleniu efekt wdrożenie szablonu jest wykonywany.

Podczas cyklu oszacowania definicji zasad z efektem DeployIfNotExists, dopasowywanie zasobów, które są oznaczone jako niezgodne, ale na taki zasób jest wykonywana żadna akcja.

### <a name="deployifnotexists-properties"></a>Właściwości DeployIfNotExists

**Szczegóły** właściwość efekty DeployIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania i wdrożenie szablonu do wykonania.

- **Typ** [wymagane]
  - Określa typ powiązanego zasobu do dopasowania.
  - Rozpoczyna się od próby pobrania zasobu poniżej **Jeśli** zasobów warunek, a następnie zapytań w ramach tej samej grupie zasobów co **Jeśli** warunku zasobu.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady Aby pobrać jeden, konkretny zasób zamiast wszystkich zasobów określonego typu.
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
- **Wdrożenie** [wymagane]
  - Ta właściwość powinna zawierać wdrożenia pełnym szablonem znajdującym się, jak zostałaby przekazana do `Microsoft.Resources/deployments` umieścić interfejsu API. Aby uzyskać więcej informacji, zobacz [interfejsu API REST wdrożenia](/rest/api/resources/deployments).

  > [!NOTE]
  > Wszystkie funkcje wewnątrz **wdrożenia** właściwości są obliczane jako części szablonu nie zasad. Wyjątek stanowi **parametry** właściwość, która przekazuje wartości z zasad do szablonu. **Wartość** w tej sekcji, w ramach szablonu Nazwa parametru służy do wykonywania tej wartości, przekazując (zobacz _fullDbName_ w przykładzie DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Przykład DeployIfNotExists

Przykład: Ocenia baz danych SQL Server w celu ustalenia, czy włączono transparentDataEncryption. Jeśli nie, następnie jest wykonywany wdrożenia, aby go włączyć.

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

## <a name="layering-policies"></a>Zasady warstwowe

Zasób może mieć wpływ wiele przydziałów. W tym samym zakresie (określonego zasobu, grupy zasobów, subskrypcji lub grupy zarządzania) lub w różnych zakresach, może być tych przydziałów. Każdy z tych przydziałów jest również mogą mieć różne efekty zdefiniowane. Niezależnie od tego warunek i efekt dla poszczególnych zasad (przypisane bezpośrednio lub w ramach inicjatywy) jest niezależne oceniany. Na przykład jeśli zasady 1 ma warunek, który ogranicza możliwość użycia dla lokalizacji zasobów dla subskrypcji A można tworzyć tylko w 'westus' przy użyciu efektu odrzucenia, a zasady 2 można warunek, który ogranicza możliwość użycia dla lokalizacji zasobów dla zasobów — Grupa B (która znajduje się w subskrypcji A) do tylko utworzone w "eastus" począwszy inspekcji są przypisane, wynik wynikowy będzie::

- Żaden zasób już w grupie zasobów B w "eastus" jest zgodne z zasadami 2, ale są oznaczone jako niezgodne z zasadami 1.
- Żaden zasób już w grupie zasobów B nie znajduje się w "eastus" zostaną oznaczone jako niezgodne z zasadami 2 i będzie również być oznaczone jako niezgodne do zasad 1, jeśli nie 'westus'.
- Nowego zasobu w ramach subskrypcji, A nie w 'westus' odrzucone przez zasady 1.
- Nowego zasobu w ramach subskrypcji A / grupy zasobów B w 'westus' może być oznaczony jako niezgodne zasady 2, ale będzie można utworzyć (zgodnych polityki 1 i 2 zasad jest inspekcji, a nie odmawiać).

Jeśli zasady 1 i 2 zasad miała efektu metod deny, zmienić tę sytuację:

- Żaden zasób już w grupie zasobów B nie znajduje się w "eastus" zostaną oznaczone jako niezgodne z zasadami 2.
- Żaden zasób już w grupie zasobów B w 'westus' nie są oznaczane jako niezgodne z zasadami 1.
- Nowego zasobu w ramach subskrypcji, A nie w 'westus' odrzucone przez zasady 1.
- Nowego zasobu w ramach subskrypcji A / odrzucone zasobów — Grupa B (ponieważ lokalizacji nigdy nie może spełnić zasady 1 i 2 zasady).

Ponieważ każde przypisanie jest szacowana osobno, nie ma szansy sprzedaży dla tego zasobu do dokumentu za pośrednictwem przerwy z powodu różnic w zakresie. W związku z tym, wynikiem zasady warstwowe lub zasad nakładają się w sieci jest uważany za **zbiorczą najbardziej restrykcyjne**. Innymi słowy zasób, który ma zostać utworzony mogą zostać zablokowane z powodu nakładających się i konflikty zasad, takich jak w powyższym przykładzie, gdyby zasady 1 i 2 zasad efektu odrzucenia. Jeśli nadal potrzebujesz zasobu do utworzenia w zakresie docelowym, przejrzyj wykluczenia dla każdego przydziału, aby upewnić się, że odpowiednie zasady mają wpływ na odpowiednie zakresy.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz głębsze zrozumienie wpływu definicji zasad, zapoznaj się z przykładami zasad:

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](json-samples.md).
