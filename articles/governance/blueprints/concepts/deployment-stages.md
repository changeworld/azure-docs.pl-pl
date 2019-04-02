---
title: Etapy wdrażania strategii
description: Dowiedz się, jak usługi planu platformy Azure przechodzi przez podczas wdrażania.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ced8c8ab2e65fb1d3f8a976f122a387fe9161750
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801968"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapy wdrażania strategii

Po wdrożeniu planu pobiera szereg działań jest zajęta przez usługę Azure schematy wdrażać zasoby zdefiniowane w planie. Ten artykuł zawiera szczegółowe informacje o każdym kroku obejmuje.

Plan wdrożenia jest wyzwalany przez przypisanie planu do subskrypcji lub [aktualizowanie istniejącego przypisania](../how-to/update-existing-assignments.md). Podczas wdrażania schematy przyjmuje następujące ogólne kroki:

> [!div class="checklist"]
> - Schematy udzielono praw właściciela
> - Tworzony jest obiekt przypisanie planu
> - Opcjonalne — plany tworzy **przypisany systemowo** tożsamości zarządzanej
> - Tożsamość zarządzana wdraża artefakty planu
> - Planu usługi i **przypisany systemowo** tożsamości zarządzanej prawami zostaną odwołane

## <a name="blueprints-granted-owner-rights"></a>Schematy udzielono praw właściciela

Jednostki usługi Azure plany otrzymuje uprawnienia właściciela do przypisanych subskrypcji lub subskrypcji. Udzielone rola umożliwia schematy do tworzenia i późniejszego wycofania [przypisany systemowo tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md).

Prawa są udzielane automatycznie Jeśli przypisanie odbywa się za pośrednictwem portalu. Jednak jeśli przypisanie odbywa się za pośrednictwem interfejsu API REST, udzielanie uprawnień musi być z oddzielny interfejs API wywołanie. Identyfikator AppId planu platformy Azure jest `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, ale nazwa główna usługi jest zależna od dzierżawcy. Użyj [interfejsu API usługi Azure Active Directory Graph](../../../active-directory/develop/active-directory-graph-api.md) i punkt końcowy REST [servicePrincipals](/graph/api/resources/serviceprincipal) można pobrać nazwy głównej usługi. Następnie przyznać plany usługi Azure _właściciela_ roli za pośrednictwem [Portal](../../../role-based-access-control/role-assignments-portal.md), [wiersza polecenia platformy Azure](../../../role-based-access-control/role-assignments-cli.md), [programu Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST Interfejs API](../../../role-based-access-control/role-assignments-rest.md), lub [szablonu usługi Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Plany usługi nie bezpośrednio wdrażania zasobów.

## <a name="the-blueprint-assignment-object-is-created"></a>Tworzony jest obiekt przypisanie planu

Przypisuje planu subskrypcji użytkownika, grupy lub jednostki usługi. Obiekt przypisania nie istnieje na poziomie subskrypcji, której przypisano planu. Zasoby utworzone w ramach wdrożenia nie są wykonywane w kontekście wdrażanie jednostki.

Podczas tworzenia przypisanie planu, typ [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md) jest zaznaczone. Wartość domyślna to **przypisany systemowo** tożsamości zarządzanej. A **użytkownik przypisany** można wybrać tożsamość zarządzaną. Korzystając z **użytkownik przypisany** zarządzanych tożsamości, musi być zdefiniowane i uprawnień, przed utworzeniem przypisanie planu.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcjonalne — plany tworzy przypisany systemowo tożsamości zarządzanej

Gdy [przypisany systemowo tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md) wybrano podczas przypisywania, schematy tworzy tożsamość i przydziela tożsamość zarządzaną [właściciela](../../../role-based-access-control/built-in-roles.md#owner) roli. Jeśli [uaktualnienia istniejącego przypisania](../how-to/update-existing-assignments.md), schematy korzysta z utworzonej wcześniej tożsamości zarządzanej.

Tożsamość zarządzana związane z przypisanie planu służy do wdrażania lub ponownego wdrażania zasoby zdefiniowane w planie. Ten projekt umożliwia uniknięcie przypadkowo od wzajemnie zakłócać przypisania.
Ten projekt obsługuje również [blokowania zasobów](./resource-locking.md) funkcji poprzez kontrolowanie zabezpieczeń wszystkich wdrożonych zasobów z planu.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Tożsamość zarządzana wdraża artefakty planu

Tożsamość zarządzaną, a następnie uruchamiają wdrożeń usługi Resource Manager artefaktów w ramach planu na określonym [sekwencjonowania](./sequencing-order.md). Kolejność można dopasować do upewnij się, że artefakty zależne od innych artefaktów są wdrażane w odpowiedniej kolejności.

Wystąpił błąd dostępu przez wdrażanie często jest wynikiem stopień udzielono dostępu do tożsamości zarządzanej. Plany usługi Zarządzanie cyklem życia zabezpieczeń **przypisany systemowo** tożsamości zarządzanej. Jednak użytkownik jest odpowiedzialny za zarządzanie prawami i cyklem życia **użytkownik przypisany** tożsamości zarządzanej.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Plan usługi i przypisany systemowo tożsamość zarządzaną prawa zostaną odwołane

Po zakończeniu wdrożenia schematy odwołuje praw **przypisany systemowo** tożsamości zarządzanej z subskrypcji. Następnie usługa plany odwołuje się swoich praw z subskrypcji. Usuwanie praw zapobiega schematy staje się właścicielem subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).