---
title: Uprawnienia w usłudze Azure Advisor
description: Uprawnienia usługi Advisor i jak może zablokować możliwość konfigurowania subskrypcji lub odłożyć lub odrzucania zaleceń.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467616"
---
# <a name="permissions-in-azure-advisor"></a>Uprawnienia w usłudze Azure Advisor

Usługa Azure Advisor zapewnia zalecenia na podstawie użycia i konfiguracji zasobów platformy Azure i subskrypcji. Używa usługi Advisor [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) dostarczone przez [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) do zarządzania Twoim dostępem do zaleceń i funkcji usługi Advisor. 

## <a name="roles-and-their-access"></a>Role oraz ich dostępu

W poniższej tabeli opisano role i dostępem, znajdujących się w ramach usługi Advisor:

| **Rola** | **Wyświetl zalecenia** | **Edytuj reguły** | **Edytuj konfigurację subskrypcji** | **Edytuj konfigurację grupy zasobów**| **Odrzuć i odroczyć zalecenia**|
|---|:---:|:---:|:---:|:---:|:---:|
|Właściciel subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Współautor subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Czytelnik subskrypcji|**X**|--|--|--|--|
|Grupa zasobów właściciela|**X**|--|--|**X**|**X**|
|Grupa zasobów współautora|**X**|--|--|**X**|**X**|
|Grupa zasobów czytnika|**X**|--|--|--|--|
|Właściciel zasobu|**X**|--|--|--|**X**|
|Współautor zasobów|**X**|--|--|--|**X**|
|Czytnik zasobów|**X**|--|--|--|--|

> [!NOTE]
> Dostęp do wyświetlania zalecenia jest zależna od Ci dostęp do zaleceń zasób objęty wpływem.

## <a name="permissions-and-unavailable-actions"></a>Uprawnienia i dostępne akcje

Braku odpowiednich uprawnień, można zablokować możliwość wykonywania akcji w usługi Advisor. Poniżej przedstawiono niektóre typowe problemy.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nie można skonfigurować grup zasobów lub subskrypcji

Podczas próby konfigurowania grup zasobów lub subskrypcji klasyfikatora może zostać wyświetlony wyłączenia opcję, aby uwzględnić lub wykluczyć. Ten stan wskazuje, że nie masz wystarczającego poziomu uprawnień do tej grupy zasobów lub subskrypcji. Aby rozwiązać ten problem, Dowiedz się, jak [udzielić użytkownikowi dostępu](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nie można odroczyć lub odrzucić zalecenia

Jeśli otrzymasz komunikat o błędzie podczas próby odroczyć lub odrzucić zalecenia, nie masz wystarczających uprawnień. Upewnij się, że masz co najmniej dostęp współautora do zasób objęty wpływem zalecenia są opóźnienia lub odrzuceniu. Aby rozwiązać ten problem, Dowiedz się, jak [udzielić użytkownikowi dostępu](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Kolejne kroki

W tym artykule nadaje się z omówieniem usługi Advisor używaniu RBAC kontrolowanie uprawnień użytkowników i jak rozwiązać typowe problemy. Aby dowiedzieć się więcej o usłudze Advisor, zobacz:

- [Co to jest usługa Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Wprowadzenie do usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
