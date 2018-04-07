---
title: Privileged Identity Management zasobów Azure - przeglądu pełny dostęp do zasobów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wykonania przeglądu dostępu do zasobów Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Przegląd dostępu Zakończ zarządzania - role zasobów - tożsamości uprzywilejowanych
Administratorzy ról uprzywilejowanych można przejrzeć uprzywilejowanego dostępu raz [przeglądu zabezpieczeń została uruchomiona](pim-resource-roles-start-access-review.md). Uprzywilejowane Identity Management (PIM) zasobów Azure automatycznie spowoduje wysłanie wiadomości e-mail, monitowania użytkowników, aby przejrzeć jego uprawnienia dostępu. Jeśli użytkownik nie pobrały wiadomości e-mail, możesz wysłać je zgodnie z instrukcjami [jak przeprowadzić przegląd zabezpieczeń](pim-resource-roles-perform-access-review.md).

Po umieszczeniu okresu przeglądu zabezpieczeń lub wszystkich użytkowników mają Zakończono ich własnym Przejrzyj, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądu i wyświetlić wyniki.

## <a name="manage-security-reviews"></a>Zarządzanie inspekcje zabezpieczeń
1. Przejdź do [portalu Azure](https://portal.azure.com/) i wybierz **zasobów Azure** aplikacji na pulpicie nawigacyjnym.
2. Wybierz zasób.
3. Wybierz **dostępu przeglądami** pulpitu nawigacyjnego.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Wybierz Przegląd dostępu, które mają być zarządzane.

W bloku szczegółów przejrzyj dostępu istnieje kilka opcji zarządzania przeglądu.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Wszystkie przeglądy dostępu mają datę końcową, ale można użyć **zatrzymać** przycisk, aby zakończyć wcześniej. Jeśli jeszcze nie zostały sprawdzone wszyscy użytkownicy tego czasu, będą mogli po zatrzymaniu przeglądu. Nie można ponownie uruchomić przeglądu, po jest została zatrzymana.

### <a name="reset"></a>Reset
Możesz resetować Przegląd dostępu, aby usunąć wszystkie decyzje na nim. Po zmianie po Przegląd dostępu, wszyscy użytkownicy są oznaczone jako nieprzejrzanymi ponownie. 

### <a name="apply"></a>Zastosuj
Po zakończeniu Przegląd dostępu, ponieważ osiągnięto datę zakończenia lub zatrzymana go ręcznie, **Zastosuj** przycisk implementuje wyniku przeglądu. Jeśli w przeglądzie nastąpiła odmowa dostępu użytkownika, to krok, który spowoduje usunięcie przypisania roli.  

### <a name="delete"></a>Usuwanie
Jeśli nie jest konieczne w żadnych późniejszych przeglądu, usuń go. **Usunąć** przycisk usuwa aplikacji PIM przeglądu.

## <a name="results"></a>Wyniki
Wyświetl i pobierać listę Przejrzyj wyniki na karcie wyników. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenci
Wyświetl i dodawać recenzentów do istniejącej recenzji dostępu. Przypomnij recenzentów przeprowadzenie ich przeglądu.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



