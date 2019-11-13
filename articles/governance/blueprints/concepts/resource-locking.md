---
title: Zrozumienie blokowania zasobów
description: Zapoznaj się z opcjami blokowania, aby chronić zasoby podczas przypisywania planu.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 754b9d7f73c6111abf7505e222a1ca5a8712ae45
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960483"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Zrozumienie blokowania zasobów w planach platformy Azure

Tworzenie spójnych środowisk na dużą skalę jest naprawdę cenne, jeśli istnieje mechanizm zapewnienia spójności. W tym artykule wyjaśniono, jak blokada zasobów działa w planach platformy Azure. Aby zapoznać się z przykładem blokowania zasobów i stosowania _przypisań Odmów_, zobacz artykuł [Ochrona nowych zasobów](../tutorials/protect-new-resources.md) .

## <a name="locking-modes-and-states"></a>Tryby blokowania i Stany

Tryb blokowania ma zastosowanie do przypisania planu i ma trzy opcje: **nie blokuj**, **tylko do odczytu**lub nie **usuwaj**. Tryb blokowania jest konfigurowany podczas wdrażania artefaktu podczas jego przypisywania. Inny tryb blokowania można ustawić, aktualizując przypisanie planu.
Nie można jednak zmienić trybów blokowania poza planami.

Zasoby utworzone przez artefakty w przypisaniu planu mają cztery stany: **nie zablokowane**, **tylko do odczytu**, **nie można edytować/usuwać**ani **nie mogą go usunąć**. Każdy typ artefaktu może być w stanie **niezablokowanym** . Poniższa tabela może służyć do określenia stanu zasobu:

|Tryb|Typ zasobu artefaktu|Stan|Opis|
|-|-|-|-|
|Nie blokuj|*|Niezablokowane|Zasoby nie są chronione przez plany. Ten stan jest również używany w przypadku zasobów dodanych do elementu " **tylko do odczytu** " lub nie **usuwaj** artefaktu grupy zasobów spoza przypisania planu.|
|Tylko do odczytu|Grupa zasobów|Nie można edytować/usunąć|Grupa zasobów jest tylko do odczytu i nie można modyfikować tagów w grupie zasobów. **Niezablokowane** zasoby mogą być dodawane, przenoszone, zmieniane lub usuwane z tej grupy zasobów.|
|Tylko do odczytu|Grupa niezasobów|Tylko do odczytu|Nie można zmienić zasobu w jakikolwiek sposób — brak zmian i nie można go usunąć.|
|Nie usuwaj|*|Nie można usunąć|Zasoby można zmienić, ale nie można ich usunąć. **Niezablokowane** zasoby mogą być dodawane, przenoszone, zmieniane lub usuwane z tej grupy zasobów.|

## <a name="overriding-locking-states"></a>Zastępowanie Stanów blokowania

Jest to zwykle możliwe dla kogoś z odpowiednią [rolą kontroli dostępu opartej na rolach](../../../role-based-access-control/overview.md) (RBAC) w ramach subskrypcji, takiej jak rola "właściciel", aby umożliwić zmianę lub usunięcie dowolnego zasobu. Ten dostęp nie jest dozwolony w przypadku, gdy plany stosują blokowanie w ramach wdrożonego przypisania. Jeśli przypisanie zostało ustawione przy użyciu opcji **tylko do odczytu** lub **nie usuwaj** , a właściciel subskrypcji może wykonać zablokowaną akcję dla chronionego zasobu.

Ten środek zabezpieczeń chroni spójność zdefiniowanego planu i środowiska, które zostało zaprojektowane do tworzenia z przypadkowego lub programowego usunięcia lub zmiany.

## <a name="removing-locking-states"></a>Usuwanie Stanów blokowania

Jeśli konieczna będzie modyfikacja lub usunięcie zasobu chronionego przez przypisanie, istnieją dwa sposoby tego celu.

- Aktualizowanie przypisania planu do trybu blokowania **nie blokuj**
- Usuń przypisanie planu

Po usunięciu przypisania blokady utworzone przez plany są usuwane. Zasób jest jednak pozostawiony i musi zostać usunięty w normalny sposób.

## <a name="how-blueprint-locks-work"></a>Jak działają blokowane plany

Akcja Odmów [przypisania](../../../role-based-access-control/deny-assignments.md) kontroli RBAC jest stosowana do zasobów artefaktu podczas przypisywania planu, jeśli przypisanie zabrało opcję **tylko do odczytu** lub **nie usuwaj** . Akcja Odmów jest dodawana przez zarządzaną tożsamość przypisania planu i może zostać usunięta tylko z zasobów artefaktów przez tę samą tożsamość zarządzaną. Ta miara zabezpieczeń wymusza mechanizm blokowania i uniemożliwia usunięcie blokady strategii poza planami.

![Zablokuj przypisanie do grupy zasobów](../media/resource-locking/blueprint-deny-assignment.png)

[Właściwości przypisania Odmów](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) dla każdego trybu są następujące:

|Tryb |Uprawnienia. akcje |Uprawnienia. nonaruszone |Podmioty zabezpieczeń [i]. Wprowadź |ExcludePrincipals [i]. # | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Tylko do odczytu |**\*** |**\*/Read** |SystemDefined (wszyscy) |przypisanie strategii i zdefiniowane przez użytkownika w **excludedPrincipals** |Grupa zasobów — _true_; Zasób — _Fałsz_ |
|Nie usuwaj |**\*/Delete** | |SystemDefined (wszyscy) |przypisanie strategii i zdefiniowane przez użytkownika w **excludedPrincipals** |Grupa zasobów — _true_; Zasób — _Fałsz_ |

> [!IMPORTANT]
> Azure Resource Manager buforuje Szczegóły przydziału roli przez maksymalnie 30 minut. W związku z tym odmowa akcji Odmów przypisań na zasoby strategii może nie być od razu całkowicie w pełni funkcjonalna. W tym czasie może być możliwe usunięcie zasobu przeznaczonego do ochrony przez blokady planu.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Wyklucz podmiot zabezpieczeń z przypisania Odmów

W niektórych scenariuszach projektowych lub zabezpieczających może być konieczne wykluczenie podmiotu zabezpieczeń z [przydziału Odmów](../../../role-based-access-control/deny-assignments.md) , które tworzy. Jest to realizowane w interfejsie API REST przez dodanie maksymalnie pięciu wartości do tablicy **excludedPrincipals** we właściwości **Locks** podczas [tworzenia przypisania](/rest/api/blueprints/assignments/createorupdate).
Jest to przykład treści żądania, która zawiera **excludedPrincipals**:

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

## <a name="next-steps"></a>Następne kroki

- Postępuj zgodnie z samouczkiem [Ochrona nowych zasobów](../tutorials/protect-new-resources.md) .
- Uzyskaj informacje na temat [cyklu życia strategii](lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).