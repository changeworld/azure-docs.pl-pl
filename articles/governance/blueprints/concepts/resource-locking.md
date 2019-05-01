---
title: Zrozumienie blokowanie zasobów
description: Informacje o opcjach blokowania, aby chronić zasoby podczas przypisywania planu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: db0b5bbe1261c7bdf76393c69a1189d2a850cd07
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719757"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Zrozumienie blokowania w plany usługi Azure resource

Tworzenie spójnego środowiska na dużą skalę jest tylko naprawdę przydatne, jeśli istnieje mechanizm w celu zachowania spójności. W tym artykule opisano, jak blokowania zasobów działa w plany usługi Azure. Aby zobaczyć przykład blokowania zasobów oraz stosowanie _Odmów przypisania_, zobacz [ochrony nowych zasobów](../tutorials/protect-new-resources.md) samouczka.

## <a name="locking-modes-and-states"></a>Tryby blokowania i Stany

Tryb blokowania odnosi się do przypisania planu i ma trzy opcje: **Nie blokuj**, **tylko do odczytu**, lub **nie usuwaj**. Tryb blokowania jest skonfigurowane podczas wdrażania artefaktu podczas przypisywania planu. Można ustawić inny tryb blokowania, aktualizując przypisanie planu.
Blokowanie trybach, jednak nie można zmienić poza schematy.

Zasoby utworzone w ramach artefaktów na przypisanie planu ma cztery stany: **Nie jest zablokowany**, **tylko do odczytu**, **nie może edytować / usuwać**, lub **nie można usunąć**. Każdy typ artefaktu może być w **nie zablokowany** stanu. Poniższa tabela może służyć do określenia stanu zasobu:

|Tryb|Typ zasobu artefaktu|Stan|Opis|
|-|-|-|-|
|Nie blokuj|*|Nie jest zablokowany|Zasoby nie są chronione przez schematy. Ten stan jest również używany do zasoby dodane do **tylko do odczytu** lub **nie usuwaj** artefaktu grupy zasobów z poza przypisanie planu.|
|Tylko do odczytu|Grupa zasobów|Nie można edytować / Delete|Grupa zasobów jest tylko do odczytu i nie można zmodyfikować tagów w grupie zasobów. **Nie jest zablokowany** zasoby mogą być dodane, przeniesiony, zmieniony lub usunięty z tej grupy zasobów.|
|Tylko do odczytu|Grupa zasobów spoza|Tylko do odczytu|Zasób nie można zmienić w dowolny sposób — bez zmian i nie można usunąć.|
|Nie usuwaj|*|Nie można usunąć|Zasoby mogą ulec zmianie, ale nie można jej usunąć. **Nie jest zablokowany** zasoby mogą być dodane, przeniesiony, zmieniony lub usunięty z tej grupy zasobów.|

## <a name="overriding-locking-states"></a>Zastępowanie stany blokowania

Zazwyczaj można niepowołanym odpowiedni [kontroli dostępu opartej na rolach](../../../role-based-access-control/overview.md) (RBAC) w ramach subskrypcji, takie jak rola "Właściciel", być może zmienić lub usunąć dowolnego zasobu. Dostęp nie schematy stosuje blokowania przypisania wdrożonej w ramach. Jeśli ustawiono przypisanie z **tylko do odczytu** lub **nie usuwaj** opcji, a nie nawet subskrypcji właściciela można wykonać akcji zablokowanych chronionego zasobu.

Ta miara zabezpieczeń chroni spójność zdefiniowanych planu i środowiska, który został zaprojektowany do utworzenia z przypadkowego lub programowy usuwania lub zmiany.

## <a name="removing-locking-states"></a>Stany blokady usuwania

Jeśli okaże się to konieczne, można zmodyfikować lub usunąć zasób chronione przez przypisanie, istnieją dwa sposoby, aby to zrobić.

- Aktualizowanie przypisania planu w celu blokowania tryb **nie blokady**
- Usuwanie przypisania planu

Usunięcie przypisania blokad utworzone przez schematy są usuwane. Jednakże zasób jest pozostawione i czy muszą zostać usunięte w normalny sposób.

## <a name="how-blueprint-locks-work"></a>Jak planu blokuje pracy

RBAC [Odmów przypisania](../../../role-based-access-control/deny-assignments.md) Odmów, akcja jest stosowane do artefaktu zasobów podczas przypisywania planu, jeśli wybrane przypisania **tylko do odczytu** lub **nie usuwaj** Opcja. Akcja odmowy jest dodawany przez zarządzaną tożsamością przypisanie planu i może zostać usunięty tylko ze źródeł artefaktów w tej samej tożsamości zarządzanej. To zabezpieczenie wymusza mechanizm blokowania i uniemożliwia usunięcie blokady plan poza schematy.

![Plan Odmów przypisania dla grupy zasobów](../media/resource-locking/blueprint-deny-assignment.png)

[Odmów przypisania właściwości](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) każdego trybu są następujące:

|Tryb |Permissions.Actions |Permissions.NotActions |Nazwy główne [i]. Typ |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Tylko do odczytu |**\*** |**\*/ odczytu** |SystemDefined (wszyscy) |Przypisanie planu i zdefiniowane przez użytkownika w **excludedPrincipals** |Grupa zasobów - _true_; Zasób - _false_ |
|Nie usuwaj |**\*/ DELETE** | |SystemDefined (wszyscy) |Przypisanie planu i zdefiniowane przez użytkownika w **excludedPrincipals** |Grupa zasobów - _true_; Zasób - _false_ |

> [!IMPORTANT]
> Usługa Azure Resource Manager buforuje Szczegóły przypisania roli dla maksymalnie 30 minut. Odmów co w efekcie przypisania odmowy Akcja zasobów planu natychmiast może nie być w pełnego wpływu. W tym okresie czasu może istnieć możliwość usuwania zasobu mają być chronione przez planu blokad.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Wykluczanie jednostki z przypisania Odmów

W niektórych scenariuszach projektu lub zabezpieczeń może być konieczne wykluczyć jednostki z [Odmów przypisania](../../../role-based-access-control/deny-assignments.md) tworzy przypisanie planu. Odbywa się w interfejsie API REST, dodając do pięciu wartości **excludedPrincipals** tablicy w **blokad** właściwości podczas [Tworzenie przypisania](/rest/api/blueprints/assignments/createorupdate).
Jest to przykład treści żądania, który zawiera **excludedPrincipals**:

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

## <a name="next-steps"></a>Kolejne kroki

- Postępuj zgodnie z [chronić nowe zasoby](../tutorials/protect-new-resources.md) samouczka.
- Uzyskaj informacje na temat [cyklu życia strategii](lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).