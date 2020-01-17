---
title: Etapy wdrażania strategii
description: Zapoznaj się z tematami dotyczącymi zabezpieczeń i artefaktów, które są wykonywane przez usługi platformy Azure podczas tworzenia przypisania planu.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: daa7722fa37547929aa21b76b870f70143ae71ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156628"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapy wdrażania strategii

Gdy plan zostanie wdrożony, do wdrożenia zasobów zdefiniowanych w planie jest wykonywana seria akcji podejmowana przez usługę plany platformy Azure. Ten artykuł zawiera szczegółowe informacje o tym, co obejmuje każdy krok.

Wdrożenie planu jest wyzwalane przez przypisanie planu do subskrypcji lub [zaktualizowanie istniejącego przypisania](../how-to/update-existing-assignments.md). Podczas wdrażania plany zajmują następujące czynności wysokiego poziomu:

> [!div class="checklist"]
> - Plany przyznane praw właściciela
> - Tworzony jest obiekt przypisania strategii
> - Opcjonalne-plany tworzą tożsamość zarządzaną **przypisaną przez system**
> - Tożsamość zarządzana wdraża artefakty strategii
> - Uprawnienia do usługi strategii i zarządzanej tożsamości **przypisane do systemu** są odwoływane

## <a name="blueprints-granted-owner-rights"></a>Plany przyznane praw właściciela

Nazwa główna usługi planów platformy Azure ma uprawnienia właściciela przypisanej subskrypcji lub subskrypcji, gdy zostanie użyta tożsamość zarządzana [tożsamości zarządzanej przez system](../../../active-directory/managed-identities-azure-resources/overview.md) . Przyznana rola pozwala na tworzenie i późniejsze odwoływanie się do tożsamości zarządzanej **przypisanej do systemu** . Jeśli jest używana tożsamość zarządzana **przypisana przez użytkownika** , jednostka usługi Azure Planes nie jest pobierana i nie wymaga praw właściciela subskrypcji.

Prawa są przyznawane automatycznie, jeśli przypisanie odbywa się za pomocą portalu. Jednak jeśli przypisanie odbywa się za pomocą interfejsu API REST, przyznanie praw musi zostać wykonane przy użyciu oddzielnego wywołania interfejsu API. Identyfikator AppId platformy Azure planuje się `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, ale jednostka usługi zależy od dzierżawy. Aby uzyskać nazwę główną usługi, użyj [Azure Active Directory interfejs API programu Graph](../../../active-directory/develop/active-directory-graph-api.md) i [serviceprincipals](/graph/api/resources/serviceprincipal) punktu końcowego. Następnie należy przydzielić roli _właściciela_ na platformę Azure za pomocą [portalu](../../../role-based-access-control/role-assignments-portal.md), [interfejsu](../../../role-based-access-control/role-assignments-rest.md) [wiersza polecenia platformy Azure](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)lub [szablonu Menedżer zasobów](../../../role-based-access-control/role-assignments-template.md).

Usługa plany nie wdraża bezpośrednio zasobów.

## <a name="the-blueprint-assignment-object-is-created"></a>Tworzony jest obiekt przypisania strategii

Użytkownik, Grupa lub jednostka usługi przypisuje plan do subskrypcji. Obiekt przypisania istnieje na poziomie subskrypcji, do którego przypisano plan. Zasoby utworzone przez wdrożenie nie są wykonywane w kontekście wdrożenia jednostki.

Podczas tworzenia przypisania planu jest wybierany typ [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md) . Wartością domyślną jest tożsamość zarządzana **przypisana przez system** . Można wybrać tożsamość zarządzaną **przypisaną przez użytkownika** . W przypadku korzystania z tożsamości zarządzanej **przypisanej przez użytkownika** przed utworzeniem przypisania strategii należy zdefiniować i przyznać uprawnienia. Role wbudowanego operatora [i](../../../role-based-access-control/built-in-roles.md#owner) [operator](../../../role-based-access-control/built-in-roles.md#blueprint-operator) planu mają uprawnienia `blueprintAssignment/write` niezbędne do utworzenia przypisania, które korzysta z tożsamości zarządzanej **przypisanej przez użytkownika** .

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcjonalne-plany tworzą tożsamość zarządzaną przypisaną przez system

W przypadku wybrania [tożsamości zarządzanej przypisanej do systemu](../../../active-directory/managed-identities-azure-resources/overview.md) podczas przypisywania program planuje tożsamość i przyznaje zarządzanej tożsamości rolę [właściciela](../../../role-based-access-control/built-in-roles.md#owner) . Jeśli [istniejące przypisanie zostanie uaktualnione](../how-to/update-existing-assignments.md), plany korzystają z wcześniej utworzonej tożsamości zarządzanej.

Zarządzana tożsamość związana z przypisaniem strategii służy do wdrażania lub ponownego wdrażania zasobów zdefiniowanych w planie. Ten projekt pozwala uniknąć nieumyślnego zakłócania przypisań.
Ten projekt obsługuje również funkcję [blokowania zasobów](./resource-locking.md) , kontrolując zabezpieczenia poszczególnych wdrożonych zasobów z planu.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Tożsamość zarządzana wdraża artefakty strategii

Tożsamość zarządzana następnie wyzwala Menedżer zasobów wdrożenia artefaktów w ramach planu w zdefiniowanej [kolejności sekwencjonowania](./sequencing-order.md). Kolejność można dostosować, aby upewnić się, że artefakty zależne od innych artefaktów są wdrożone w odpowiedniej kolejności.

Niepowodzenie dostępu przez wdrożenie jest często wynikiem poziomu dostępu udzielonego tożsamości zarządzanej. Usługa plany zarządza cyklem życia zabezpieczeń zarządzanej tożsamości **przypisanej do systemu** . Jednak użytkownik jest odpowiedzialny za zarządzanie prawami i cyklem życia tożsamości zarządzanej **przypisanej przez użytkownika** .

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Uprawnienia do usługi strategii i zarządzanej tożsamości przypisane do systemu są odwoływane

Po zakończeniu wdrożeń plany odwołują prawa do zarządzanej tożsamości **przypisanej do systemu** z subskrypcji. Następnie usługa plany odwołuje swoje prawa z subskrypcji. Usunięcie praw uniemożliwia nietrwałego właściciela subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).
