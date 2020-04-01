---
title: Opis blokowania zasobów
description: Dowiedz się więcej o opcjach blokowania w planach platformy Azure, aby chronić zasoby podczas przypisywania planu.
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 86897ae6665f7a339b51aaae5f1c00144d8b7309
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437746"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Opis blokowania zasobów w planach platformy Azure

Tworzenie spójnych środowisk na dużą skalę jest naprawdę cenne tylko wtedy, gdy istnieje mechanizm, aby utrzymać tę spójność. W tym artykule wyjaśniono, jak działa blokowanie zasobów w planach platformy Azure. Aby zobaczyć przykład blokowania zasobów i stosowania _przypisań odmów,_ zobacz samouczek [ochrony nowych zasobów.](../tutorials/protect-new-resources.md)

> [!NOTE]
> Blokady zasobów wdrożone przez plany platformy Azure są stosowane tylko do zasobów wdrożonych przez przypisanie planu. Istniejące zasoby, takie jak te w grupach zasobów, które już istnieją, nie mają blokad dodanych do nich.

## <a name="locking-modes-and-states"></a>Tryby i stany blokowania

Tryb blokowania ma zastosowanie do przypisania planu i ma trzy opcje: **Nie blokuj**, **Nie tylko do odczytu**lub **Nie usuwaj**. Tryb blokowania jest skonfigurowany podczas wdrażania artefaktów podczas przypisywania planu. Inny tryb blokowania można ustawić, aktualizując przypisanie planu.
Tryby blokowania nie można jednak zmienić poza schematami.

Zasoby utworzone przez artefakty w przypisaniu planu mają cztery stany: **Nie zablokowany,** Nie można **edytować,** **usuwać**lub **nie można usunąć**. Każdy typ artefaktu może być w stanie **Nie zablokowany.** Poniższa tabela może służyć do określenia stanu zasobu:

|Tryb|Typ zasobu artefaktu|Stan|Opis|
|-|-|-|-|
|Nie zamykaj|*|Nie zablokowany|Zasoby nie są chronione przez plany. Ten stan jest również używany dla zasobów dodanych do artefaktu grupy tylko do **odczytu** lub **Nie usuwaj** z zewnątrz przypisania planu.|
|Tylko do odczytu|Grupa zasobów|Nie można edytować / usunąć|Grupa zasobów jest tylko do odczytu i nie można modyfikować znaczników w grupie zasobów. Nie zablokowane zasoby mogą być **dodawane,** przenoszone, zmieniane lub usuwane z tej grupy zasobów.|
|Tylko do odczytu|Grupa niebędąca zasobami|Tylko do odczytu|Zasobu nie można w żaden sposób zmienić — bez zmian i nie można go usunąć.|
|Nie usuwaj|*|Nie można usunąć|Zasoby można zmienić, ale nie można ich usunąć. Nie zablokowane zasoby mogą być **dodawane,** przenoszone, zmieniane lub usuwane z tej grupy zasobów.|

## <a name="overriding-locking-states"></a>Zastępowanie stanów blokowania

Zazwyczaj jest możliwe dla kogoś z odpowiedniej [kontroli dostępu opartej](../../../role-based-access-control/overview.md) na rolach (RBAC) w subskrypcji, takich jak "Właściciel" roli, aby mieć możliwość zmiany lub usunięcia dowolnego zasobu. Ten dostęp nie jest w przypadku, gdy plany stosuje blokowanie jako część wdrożonego przypisania. Jeśli przypisanie zostało ustawione za pomocą opcji **Tylko do odczytu** lub **Nie usuwaj,** nawet właściciel subskrypcji nie może wykonać zablokowanej akcji na chronionym zasobie.

Ten środek zabezpieczeń chroni spójność zdefiniowanego planu i środowiska, które zostało zaprojektowane do tworzenia przed przypadkowym lub programowym usunięciem lub zmianą.

### <a name="assign-at-management-group"></a>Przypisywanie w grupie zarządzania

Dodatkową opcją, aby uniemożliwić właścicielom subskrypcji usunięcie przypisania planu, jest przypisanie planu do grupy zarządzania. W tym scenariuszu tylko **właściciele** grupy zarządzania mają uprawnienia potrzebne do usunięcia przypisania planu.

Aby przypisać plan do grupy zarządzania zamiast subskrypcji, wywołanie interfejsu API REST zmienia się w następującą o następującej wyglądzie:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Grupa zarządzania `{assignmentMG}` zdefiniowana przez musi znajdować się w hierarchii grupy zarządzania lub być tą samą grupą zarządzania, w której jest zapisywana definicja planu.

Treść żądania przypisania planu wygląda następująco:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

Kluczową różnicą w tej treści żądania i jeden `properties.scope` jest przypisany do subskrypcji jest właściwością. Ta wymagana właściwość musi być ustawiona na subskrypcję, do których ma zastosowanie przypisanie planu. Subskrypcja musi być bezpośrednim podrzędnym hierarchii grupy zarządzania, w którym jest przechowywane przypisanie planu.

> [!NOTE]
> Plan przypisany do zakresu grupy zarządzania nadal działa jako przypisanie planu poziomu subskrypcji. Jedyną różnicą jest, gdzie przypisanie planu jest przechowywany, aby uniemożliwić właścicielom subskrypcji usunięcie przypisania i skojarzonych blokad.

## <a name="removing-locking-states"></a>Usuwanie stanów blokowania

Jeśli konieczne staje się zmodyfikowanie lub usunięcie zasobu chronionego przez przypisanie, istnieją dwa sposoby, aby to zrobić.

- Aktualizowanie przypisania planu do trybu blokowania **nie blokuj**
- Usuwanie przypisania planu

Po usunięciu przypisania blokady utworzone przez plany są usuwane. Jednak zasób pozostaje w tyle i musiałby zostać usunięty za pomocą zwykłych środków.

## <a name="how-blueprint-locks-work"></a>Jak działają blokady planu

Akcja [odmowy odmowy rbac](../../../role-based-access-control/deny-assignments.md) jest stosowana do zasobów artefaktów podczas przypisywania planu, jeśli w zadaniu wybrano opcję **Tylko do odczytu** lub **Nie usuwaj.** Akcja odmowy jest dodawana przez zarządzaną tożsamość przypisania planu i może zostać usunięta z zasobów artefaktu tylko przez tę samą tożsamość zarządzaną. Ten środek zabezpieczeń wymusza mechanizm blokowania i zapobiega usunięciu blokady planu poza planami.

![Przypisanie odmowy planu w grupie zasobów](../media/resource-locking/blueprint-deny-assignment.png)

[Właściwości przypisania odmowy](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) każdego trybu są następujące:

|Tryb |Uprawnienia.Akcje |Uprawnienia.NotActions |Zleceniodawcy[i]. Typu |ExcludePrincipals[i]. Identyfikator | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Tylko do odczytu |**\*** |**\*/czytaj** |SystemDefiniowany (wszyscy) |przypisanie planu i zdefiniowane przez użytkownika w **wykluczonychPrincipals** |Grupa zasobów — _true_; Zasób — _fałsz_ |
|Nie usuwaj |**\*/delete** | |SystemDefiniowany (wszyscy) |przypisanie planu i zdefiniowane przez użytkownika w **wykluczonychPrincipals** |Grupa zasobów — _true_; Zasób — _fałsz_ |

> [!IMPORTANT]
> Usługa Azure Resource Manager buforuje szczegóły przypisania roli przez maksymalnie 30 minut. W rezultacie odmowa przypisania odmówić akcji na zasoby planu nie może być natychmiast w pełni skuteczne. W tym okresie może być możliwe usunięcie zasobu przeznaczonego do ochrony przez blokady planu.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Wykluczanie głównego zobowiązanego z przypisania odmowy

W niektórych scenariuszach projektowania lub zabezpieczeń może być konieczne wykluczenie podmiotu zabezpieczeń z [przypisania odmowy,](../../../role-based-access-control/deny-assignments.md) które tworzy przypisanie planu. Ten krok jest wykonywany w interfejsie API REST przez dodanie maksymalnie pięciu wartości do **tablicy excludedPrincipals** we właściwości **blokad** podczas [tworzenia przypisania](/rest/api/blueprints/assignments/createorupdate). Następująca definicja przypisania jest przykładem treści żądania, która zawiera **excludedPrincipals:**

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Wykluczanie akcji z przypisania odmowy

Podobnie jak [wykluczając podmiot zabezpieczeń](#exclude-a-principal-from-a-deny-assignment) przy [przypisaniu odmowy](../../../role-based-access-control/deny-assignments.md) w przypisaniu planu, można wykluczyć określone [operacje RBAC](../../../role-based-access-control/resource-provider-operations.md). W obrębie bloku **properties.locks,** w tym samym miejscu, w które **wykluczonePrincipals** **jest, excludedActions** można dodać:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Chociaż **excludedPrincipals** musi być jawne, **excludedActions wpisy** mogą używać `*` do dopasowywania symboli wieloznacznych operacji RBAC.

## <a name="next-steps"></a>Następne kroki

- Postępuj zgodnie z samouczka [ochrony nowych zasobów.](../tutorials/protect-new-resources.md)
- Dowiedz się więcej o [cyklu życia planu](lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).