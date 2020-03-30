---
title: Zakończ przegląd dostępu do ról zasobów platformy Azure w usłudze PIM — Usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak ukończyć przegląd dostępu ról zasobów platformy Azure Zarządzanie tożsamościami uprzywilejowanych w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021994"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Ukończ przegląd ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Administratorzy ról uprzywilejowanych mogą przeglądać dostęp uprzywilejowany po [rozpoczęciu przeglądu dostępu](pim-resource-roles-start-access-review.md). Zarządzanie tożsamością uprzywilejowaną (PIM) w usłudze Azure Active Directory (Azure AD) automatycznie wysyła wiadomość e-mail z monitem o sprawdzenie ich dostępu. Jeśli użytkownik nie otrzyma wiadomości e-mail, możesz wysłać mu instrukcje [dotyczące sposobu przeprowadzania przeglądu dostępu](pim-resource-roles-perform-access-review.md).

Po zakończeniu okresu przeglądu dostępu lub po zakończeniu przez wszystkich użytkowników samodzielnego przeglądu, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądem i zobaczyć wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu

1. Przejdź do [witryny Azure portal](https://portal.azure.com/). Na pulpicie nawigacyjnym wybierz usługę **zasobów platformy Azure.**

2. Wybierz zasób.

3. Wybierz sekcję **Przeglądy programu Access** na pulpicie nawigacyjnym.

    ![Zasoby platformy Azure — lista opinii programu Access z rolą, właścicielem, datą rozpoczęcia, datą zakończenia i stanem](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Wybierz recenzję dostępu, którą chcesz zarządzać.

Na stronie szczegółów przeglądu dostępu istnieje wiele opcji zarządzania tą recenzją. Dostępne są następujące opcje:

![Opcje zarządzania recenzją — zatrzymanie, zresetowanie, zastosowanie, usunięcie](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop

Wszystkie recenzje dostępu mają datę końcową. Wybierz **Zatrzymaj,** aby zakończyć go wcześniej. Użytkownicy, którzy nie zakończyli recenzji do tego czasu, nie będą mogli jej ukończyć po zatrzymaniu recenzji. Nie można ponownie uruchomić recenzji po jej zatrzymaniu.

### <a name="reset"></a>Reset

Możesz zresetować przegląd dostępu, aby usunąć wszystkie decyzje, które są na niej podejmowane. Po zresetowaniu przeglądu dostępu wszyscy użytkownicy są oznaczani jako nieucenione ponownie.

### <a name="apply"></a>Zastosuj

Po zakończeniu przeglądu dostępu **wybierz** zastosuj, aby zaimplementować wynik przeglądu. Jeśli dostęp użytkownika został odrzucony w recenzji, ten krok usuwa przypisanie roli.  

### <a name="delete"></a>Usuń

Jeśli nie jesteś już zainteresowany recenzją, usuń ją. Wybierz **pozycję Usuń** yo usuń recenzję z usługi Zarządzanie tożsamościami uprzywilejowanymi.

## <a name="results"></a>Wyniki

Na stronie **Wyniki** wyświetl i pobierz listę wyników recenzji.

![Strona wyników z listą użytkowników, wynik, przyczyna, sprawdzone przez, zastosowane przez, i zastosować wynik](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenci

Wyświetlanie i dodawanie recenzentów do istniejącej recenzji dostępu. Przypomnij recenzentom, aby uzupełnili swoje recenzje.

![Lista nazw strony recenzentów i głównego użytkownika](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie przeglądu dostępu dla ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-start-access-review.md)
- [Wykonywanie przeglądu dostępu do ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-perform-access-review.md)
