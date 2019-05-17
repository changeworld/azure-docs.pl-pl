---
title: Kończenie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zakończyć Przegląd dostępu dla ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: aee8ac3c2638ede559f8a1f9c51f2d6e62604998
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602303"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Kończenie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM
Administratorzy ról uprzywilejowanych, można przejrzeć uprzywilejowanego dostępu po [przeglądu dostępu została uruchomiona](pim-resource-roles-start-access-review.md). Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatycznie wysyła wiadomość e-mail, który monituje użytkowników, aby zapoznać się z ich dostęp. Jeśli użytkownik nie otrzymają wiadomość e-mail, możesz wysłać im instrukcje [jak przeprowadzić przegląd dostępu](pim-resource-roles-perform-access-review.md).

Po zakończeniu okresu przeglądu dostępu lub po wszystkich użytkowników została zakończona w ich własnym Przejrzyj, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądu i wyświetlić wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie przeglądów dostępu
1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Na pulpicie nawigacyjnym wybierz **zasobów platformy Azure** aplikacji.

2. Wybierz zasób.

3. Wybierz **przeglądów dostępu** części pulpitu nawigacyjnego.
![Przeglądy dostępu](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Wybierz przeglądu dostępu, które mają być zarządzane.

W bloku szczegółów przeglądu dostępu istnieje kilka opcji związanych z zarządzaniem przeglądu. Dostępne są następujące opcje:

![Opcje zarządzania przeglądu](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Zatrzymaj
Wszystkie przeglądy dostępu mają datę końcową, ale można użyć **zatrzymać** przycisk, aby zakończyć jej początku. Wszyscy użytkownicy, którzy nie została jeszcze swojego przeglądu po tym czasie nie będzie mógł zakończyć ją po zakończeniu przeglądu. Nie można ponownie uruchomić przeglądu, po jest zatrzymana.

### <a name="reset"></a>Resetowanie
Możesz zresetować przeglądu dostępu, aby usunąć wszystkie decyzje, które składają się na nim. Po zresetowaniu Przegląd dostępu wszystkich użytkowników, są oznaczone jako wykonany ponownie. 

### <a name="apply"></a>Zastosuj
Po zakończeniu przeglądu dostępu za pomocą **Zastosuj** przycisku, zaimplementować wyniku przeglądu. Jeśli w przeglądzie nastąpiła odmowa dostępu użytkownika, ten krok powoduje usunięcie przypisania roli.  

### <a name="delete"></a>Usuń
Jeśli nie chcesz w przeglądzie więcej, należy go usunąć. **Usuń** przycisk usuwa przeglądu z poziomu aplikacji PIM.

## <a name="results"></a>Wyniki
Na **wyniki** kartę, przeglądanie i pobieranie listy wyników przeglądu. 
![Karta wyników](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenci
Wyświetlanie i dodawanie recenzentów do Twojej istniejący Przegląd dostępu. Przypomnij osób dokonujących przeglądu, aby ukończyć przeglądów ich.
![Dodaj recenzentów](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Kolejne kroki

- [Uruchamianie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-start-access-review.md)
- [Wykonywanie przeglądu dostępu ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-perform-access-review.md)
