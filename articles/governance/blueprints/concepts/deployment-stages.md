---
title: Etapy wdrażania strategii
description: Poznaj kroki związane z zabezpieczeniami i artefaktami, które przechodzą usługi Azure Blueprints podczas tworzenia przypisania planu.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: daa7722fa37547929aa21b76b870f70143ae71ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156628"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapy wdrażania strategii

Po wdrożeniu planu, szereg akcji jest podejmowana przez usługę Azure Blueprints do wdrażania zasobów zdefiniowanych w planie. Ten artykuł zawiera szczegółowe informacje o tym, co obejmuje każdy krok.

Wdrożenie planu jest wyzwalane przez przypisanie planu do subskrypcji lub [zaktualizowanie istniejącego przydziału](../how-to/update-existing-assignments.md). Podczas wdrażania plany wykonuje następujące kroki wysokiego poziomu:

> [!div class="checklist"]
> - Plany przyznane prawom właściciela
> - Obiekt przydziału planu jest tworzony
> - Opcjonalnie — plany tworzą tożsamość zarządzaną **przypisaną do systemu**
> - Tożsamość zarządzana wdraża artefakty planu
> - Usługa Blueprint i uprawnienia do tożsamości **zarządzanej przypisane do systemu** są odwoływane

## <a name="blueprints-granted-owner-rights"></a>Plany przyznane prawom właściciela

Podmiot usługi Azure Blueprints otrzymuje prawa właściciela do przypisanej subskrypcji lub subskrypcji, gdy używana jest tożsamość zarządzana [tożsamości przypisanej przez system.](../../../active-directory/managed-identities-azure-resources/overview.md) Przyznana rola umożliwia blueprints do tworzenia, a później odwołać, **tożsamości zarządzanej przypisanej przez system.** Jeśli przy użyciu tożsamości **zarządzanej przypisane przez użytkownika,** jednostki usługi Azure Blueprints nie pobiera i nie potrzebuje praw właściciela w ramach subskrypcji.

Prawa są przyznawane automatycznie, jeśli przypisanie odbywa się za pośrednictwem portalu. Jednak jeśli przypisanie odbywa się za pośrednictwem interfejsu API REST, przyznanie praw musi być wykonane za pomocą wywołania interfejsu API oddzielne. Azure Blueprints AppId `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`jest , ale jednostki usługi zależy od dzierżawy. Użyj [interfejsu API programu Azure Active Directory Graph](../../../active-directory/develop/active-directory-graph-api.md) i usługi punktu końcowego [RESTPrincipals,](/graph/api/resources/serviceprincipal) aby uzyskać jednostkę usługi. Następnie należy przyznać planom platformy Azure rolę _właściciela_ za pośrednictwem [portalu,](../../../role-based-access-control/role-assignments-portal.md) [interfejsu wiersza polecenia platformy Azure,](../../../role-based-access-control/role-assignments-cli.md) [interfejsu Azure PowerShell,](../../../role-based-access-control/role-assignments-powershell.md) [interfejsu API REST](../../../role-based-access-control/role-assignments-rest.md)lub [szablonu Menedżera zasobów.](../../../role-based-access-control/role-assignments-template.md)

Usługa Blueprints nie bezpośrednio wdraża zasoby.

## <a name="the-blueprint-assignment-object-is-created"></a>Obiekt przydziału planu jest tworzony

Użytkownik, grupa lub podmiot usługi przypisuje plan do subskrypcji. Obiekt przypisania istnieje na poziomie subskrypcji, na którym przypisano plan. Zasoby utworzone przez wdrożenie nie są wykonywane w kontekście jednostki wdrażającej.

Podczas tworzenia przypisania planu jest wybierany typ [tożsamości zarządzanej.](../../../active-directory/managed-identities-azure-resources/overview.md) Wartością domyślną jest tożsamość **zarządzana przypisana przez system.** Można wybrać tożsamość zarządzaną **przypisaną przez użytkownika.** Podczas korzystania z tożsamości **zarządzanej przypisanej przez użytkownika,** musi być zdefiniowane i przyznane uprawnienia przed przypisaniem planu jest tworzony. Wbudowane role [właściciela](../../../role-based-access-control/built-in-roles.md#owner) i [operatora planu](../../../role-based-access-control/built-in-roles.md#blueprint-operator) `blueprintAssignment/write` mają uprawnienia niezbędne do utworzenia przypisania, które używa tożsamości **zarządzanej przypisanej przez użytkownika.**

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcjonalnie — plany tworzą tożsamość zarządzaną przypisaną do systemu

Gdy [podczas przypisywania systemu tożsamość zarządzana](../../../active-directory/managed-identities-azure-resources/overview.md) jest zaznaczona podczas przypisywania schematów, plany tworzy tożsamość i przyznaje tożsamości zarządzanej roli [właściciela.](../../../role-based-access-control/built-in-roles.md#owner) Jeśli [istniejące przypisanie zostanie uaktualnione,](../how-to/update-existing-assignments.md)plany używają wcześniej utworzonej tożsamości zarządzanej.

Tożsamość zarządzana związana z przypisaniem planu jest używana do wdrażania lub ponownego wdrażania zasobów zdefiniowanych w planie. Ten projekt pozwala uniknąć przypisań przypadkowo zakłócających się ze sobą.
Ten projekt obsługuje również funkcję [blokowania zasobów, kontrolując](./resource-locking.md) zabezpieczenia każdego wdrożonego zasobu z planu.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Tożsamość zarządzana wdraża artefakty planu

Tożsamość zarządzana następnie wyzwala wdrożenia Menedżera zasobów artefaktów w planie w zdefiniowanej [kolejności sekwencjonowania](./sequencing-order.md). Kolejność można dostosować, aby upewnić się, że artefakty zależne od innych artefaktów są wdrażane w odpowiedniej kolejności.

Niepowodzenie dostępu przez wdrożenie jest często wynikiem poziomu dostępu przyznanego tożsamości zarządzanej. Usługa Blueprints zarządza cyklem życia zabezpieczeń tożsamości zarządzanej **przypisanej przez system.** Jednak użytkownik jest odpowiedzialny za zarządzanie prawami i cyklem życia tożsamości zarządzanej **przypisanej przez użytkownika.**

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Usługa Blueprint i uprawnienia do tożsamości zarządzanej przypisane do systemu są odwoływane

Po zakończeniu wdrażania plany unieważniają prawa tożsamości zarządzanej **przypisanej przez system** z subskrypcji. Następnie usługa Blueprints odwołuje swoje prawa z subskrypcji. Usuwanie praw uniemożliwia blueprints staje się stałym właścicielem w subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).
