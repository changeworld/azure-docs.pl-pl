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
ms.openlocfilehash: 9903bb82a82291febf571829fb9874ba66d2eab2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476360"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Kończenie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM
Administratorzy ról uprzywilejowanych, można przejrzeć uprzywilejowanego dostępu po [przeglądu dostępu została uruchomiona](pim-resource-roles-start-access-review.md). Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatycznie wysyła wiadomość e-mail, który monituje użytkowników, aby zapoznać się z ich dostęp. Jeśli użytkownik nie otrzymają wiadomość e-mail, możesz wysłać im instrukcje [jak przeprowadzić przegląd dostępu](pim-resource-roles-perform-access-review.md).

Po zakończeniu okresu przeglądu dostępu lub po wszystkich użytkowników została zakończona w ich własnym Przejrzyj, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądu i wyświetlić wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie przeglądów dostępu
1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Na pulpicie nawigacyjnym wybierz **zasobów platformy Azure** aplikacji.

2. Wybierz zasób.

3. Wybierz **przeglądów dostępu** części pulpitu nawigacyjnego.

    ![Zasoby platformy Azure — przeglądy dostępu dotyczące ról wyświetlanie listy, właściciela, Data rozpoczęcia, Data zakończenia i stan](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Wybierz przeglądu dostępu, które mają być zarządzane.

W bloku szczegółów przeglądu dostępu istnieje kilka opcji związanych z zarządzaniem przeglądu. Dostępne są następujące opcje:

![Opcje zarządzania przeglądu - Stop, Reset, zastosuj i Usuń](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Wszystkie przeglądy dostępu mają datę końcową, ale można użyć **zatrzymać** przycisk, aby zakończyć jej początku. Wszyscy użytkownicy, którzy nie została jeszcze swojego przeglądu po tym czasie nie będzie mógł zakończyć ją po zakończeniu przeglądu. Nie można ponownie uruchomić przeglądu, po jest zatrzymana.

### <a name="reset"></a>Reset
Możesz zresetować przeglądu dostępu, aby usunąć wszystkie decyzje, które składają się na nim. Po zresetowaniu Przegląd dostępu wszystkich użytkowników, są oznaczone jako wykonany ponownie. 

### <a name="apply"></a>Zastosuj
Po zakończeniu przeglądu dostępu za pomocą **Zastosuj** przycisku, zaimplementować wyniku przeglądu. Jeśli w przeglądzie nastąpiła odmowa dostępu użytkownika, ten krok powoduje usunięcie przypisania roli.  

### <a name="delete"></a>Usuwanie
Jeśli nie chcesz w przeglądzie więcej, należy go usunąć. **Usuń** przycisk usuwa przeglądu z poziomu aplikacji PIM.

## <a name="results"></a>Wyniki
Na **wyniki** strony, przeglądanie i pobieranie listy wyników przeglądu. 

![Strona wyników listę użytkowników, wyniki, dlatego przeglądane przez stosowane przez i Zastosuj wynik](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenci
Wyświetlanie i dodawanie recenzentów do Twojej istniejący Przegląd dostępu. Przypomnij osób dokonujących przeglądu, aby ukończyć przeglądów ich.

![Stronę osób dokonujących przeglądu, nazwy i główna nazwa użytkownika](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Kolejne kroki

- [Uruchamianie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-start-access-review.md)
- [Wykonywanie przeglądu dostępu ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-perform-access-review.md)
