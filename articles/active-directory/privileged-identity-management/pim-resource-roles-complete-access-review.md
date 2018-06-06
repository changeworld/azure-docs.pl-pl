---
title: Wykonywanie przeglądu dostępu do zasobów platformy Azure za pomocą Privileged Identity Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wykonania przeglądu dostępu do zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1dd5eb4a2a0a4edd15e5299754028570806db09b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699458"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Wykonywanie przeglądu dostępu do zasobów platformy Azure za pomocą Privileged Identity Management
Ról uprzywilejowanych Administratorzy mogą sprawdzić uprzywilejowanego dostępu po [przeglądu zabezpieczeń została uruchomiona](pim-resource-roles-start-access-review.md). Privileged Identity zarządzania (PIM) dla zasobów platformy Azure automatycznie wysyła wiadomość e-mail, które monituje użytkowników, aby przejrzeć jego uprawnienia dostępu. Jeśli użytkownik nie odbiera wiadomości e-mail, możesz wysłać je zgodnie z instrukcjami [jak przeprowadzić przegląd zabezpieczeń](pim-resource-roles-perform-access-review.md).

Po zakończeniu okresu przeglądu zabezpieczeń lub po wszystkich użytkowników mają ich własnym Przejrzyj, wykonaj czynności opisane w tym artykule, aby zarządzać przeglądu i wyświetlić wyniki.

## <a name="manage-security-reviews"></a>Zarządzanie inspekcje zabezpieczeń
1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Na pulpicie nawigacyjnym i wybierz **zasobów Azure** aplikacji.

2. Wybierz zasób.

3. Wybierz **dostępu przeglądami** pulpitu nawigacyjnego.
![Przeglądy dostępu](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Wybierz Przegląd dostępu, które mają być zarządzane.

W bloku szczegółów kontroli dostępu istnieje kilka opcji zarządzania przeglądu. Dostępne są następujące opcje:

![Opcje zarządzania przeglądu](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Wszystkie przeglądy dostępu mają datę końcową, ale można użyć **zatrzymać** przycisk, aby zakończyć wcześniej. Wszyscy użytkownicy, którzy nie została zakończona ich przeglądu tego czasu nie będzie można go ukończyć po zatrzymaniu przeglądu. Nie można ponownie uruchomić przeglądu, po jest została zatrzymana.

### <a name="reset"></a>Reset
Możesz resetować Przegląd dostępu, aby usunąć wszystkie decyzje, które składają się na nim. Po zresetowaniu zostały Przegląd dostępu, wszyscy użytkownicy są oznaczone jako nieprzejrzanymi ponownie. 

### <a name="apply"></a>Zastosuj
Po zakończeniu Przegląd dostępu za pomocą **Zastosuj** przycisk, aby zaimplementować wyniku przeglądu. Jeśli w przeglądzie nastąpiła odmowa dostępu użytkownika, ten krok spowoduje usunięcie przypisania roli.  

### <a name="delete"></a>Usuwanie
Jeśli użytkownik nie są zainteresowane w przeglądzie więcej, należy ją usunąć. **Usunąć** przycisk usuwa aplikacji PIM przeglądu.

## <a name="results"></a>Wyniki
Na **wyniki** karcie, wyświetlać i pobierać listę wyników przeglądu. 
![Karta wyników](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenci
Wyświetl i dodawać recenzentów do istniejącej recenzji dostępu. Przypomnij recenzentów przeprowadzenie ich przeglądu.
![Dodawanie recenzentów](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



