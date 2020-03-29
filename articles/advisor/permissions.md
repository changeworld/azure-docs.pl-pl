---
title: Uprawnienia w usłudze Azure Advisor
description: Uprawnienia klasyfikatora i sposób, w jaki mogą one blokować możliwość konfigurowania subskrypcji lub odraczania lub odrzucania zaleceń.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422313"
---
# <a name="permissions-in-azure-advisor"></a>Uprawnienia w usłudze Azure Advisor

Usługa Azure Advisor zawiera zalecenia oparte na użyciu i konfiguracji zasobów i subskrypcji platformy Azure. Advisor używa [wbudowanych ról dostarczonych](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) przez [kontrolę dostępu opartą na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) do zarządzania dostępem do zaleceń i funkcji klasyfikatora. 

## <a name="roles-and-their-access"></a>Role i ich dostęp

W poniższej tabeli zdefiniowano role i dostęp, jaki mają w ramach advisora:

| **Roli** | **Wyświetlanie rekomendacji** | **Edytuj reguły** | **Edytowanie konfiguracji subskrypcji** | **Edytowanie konfiguracji grupy zasobów**| **Odrzucanie i odkładanie zaleceń**|
|---|:---:|:---:|:---:|:---:|:---:|
|Właściciel subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Współautor subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Czytnik subskrypcji|**X**|--|--|--|--|
|Właściciel grupy zasobów|**X**|--|--|**X**|**X**|
|Współautor grupy zasobów|**X**|--|--|**X**|**X**|
|Czytnik grupy zasobów|**X**|--|--|--|--|
|Właściciel zasobu|**X**|--|--|--|**X**|
|Współautor zasobów|**X**|--|--|--|**X**|
|Czytnik zasobów|**X**|--|--|--|--|

> [!NOTE]
> Dostęp do rekomendacji widoku zależy od dostępu do zasobu, na który ma wpływ zalecenie.

## <a name="permissions-and-unavailable-actions"></a>Uprawnienia i niedostępne akcje

Brak odpowiednich uprawnień może zablokować możliwość wykonywania akcji w programie Advisor. Poniżej przedstawiono kilka typowych problemów.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nie można skonfigurować subskrypcji lub grup zasobów

Podczas próby skonfigurowania subskrypcji lub grup zasobów w programie Advisor może zostać wyświetlona opcja dołączania lub wykluczania. Ten stan wskazuje, że nie masz wystarczającego poziomu uprawnień dla tej grupy zasobów lub subskrypcji. Aby rozwiązać ten problem, dowiedz się, jak [udzielić użytkownikowi dostępu](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nie można odroczyć lub odrzucić rekomendacji

Jeśli podczas próby odroczenia lub odrzucenia rekomendacji zostanie wyświetlony błąd, może nie mieć wystarczających uprawnień. Upewnij się, że masz co najmniej dostęp współautora do zasobu, którego dotyczy problem, które odraczasz lub odrzucasz. Aby rozwiązać ten problem, dowiedz się, jak [udzielić użytkownikowi dostępu](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono omówienie sposobu, w jaki doradca używa funkcji RBAC do kontrolowania uprawnień użytkowników i sposobu rozwiązywania typowych problemów. Aby dowiedzieć się więcej o doradcy, zobacz:

- [Co to jest usługa Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Wprowadzenie do usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
