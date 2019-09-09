---
title: Dokończ przegląd dostępu ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak wykonać przegląd dostępu ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b9563a4032011b999bf867fc782ba4cbb9c3fac
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804261"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Dokończ przegląd dostępu ról zasobów platformy Azure w usłudze PIM
Administratorzy ról uprzywilejowanych mogą przeglądać dostęp uprzywilejowany po [rozpoczęciu przeglądu dostępu](pim-resource-roles-start-access-review.md). Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatycznie wysyła wiadomość e-mail z prośbą o przegląd dostępu do użytkowników. Jeśli użytkownik nie otrzyma wiadomości e-mail, można wysłać im instrukcje dotyczące [sposobu przeprowadzania przeglądu dostępu](pim-resource-roles-perform-access-review.md).

Po przekroczeniu okresu przeglądu dostępu lub po zakończeniu samooceny przez wszystkich użytkowników wykonaj kroki opisane w tym artykule, aby zarządzać przeglądem i zobaczyć wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu
1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Następnie na pulpicie nawigacyjnym wybierz aplikację **Azure Resources** .

2. Wybierz zasób.

3. Wybierz sekcję **przeglądy dostępu** na pulpicie nawigacyjnym.

    ![Zasoby platformy Azure — lista przeglądów dostępu pokazująca rolę, właściciela, datę rozpoczęcia, datę zakończenia i stan](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Wybierz przegląd dostępu, który chcesz zarządzać.

W bloku szczegółów przeglądu dostępu dostępne są różne opcje zarządzania tym przeglądem. Dostępne są następujące opcje:

![Opcje zarządzania przeglądem — zatrzymywanie, resetowanie, stosowanie, usuwanie](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Wszystkie przeglądy dostępu mają datę końcową, ale możesz użyć przycisku **Zatrzymaj** , aby zakończyć go na początku. Wszyscy użytkownicy, którzy nie ukończyli recenzji przez ten czas, nie będą mogli jej zakończyć po zatrzymaniu przeglądu. Po zatrzymaniu nie można ponownie uruchomić recenzji.

### <a name="reset"></a>Resetowanie
Możesz zresetować przegląd dostępu, aby usunąć wszystkie podejmowane decyzje. Po zresetowaniu przeglądu dostępu wszyscy użytkownicy są domyślnie oznaczeni jako nieprzeglądany. 

### <a name="apply"></a>Zastosuj
Po zakończeniu przeglądu dostępu Użyj przycisku **Zastosuj** , aby zaimplementować wynik przeglądu. W przypadku odmowy dostępu użytkownika do przeglądu ten krok spowoduje usunięcie przypisania roli.  

### <a name="delete"></a>Usuwanie
Jeśli nie chcesz dowiedzieć się więcej, usuń je. Przycisk **Usuń** usuwa przegląd z aplikacji PIM.

## <a name="results"></a>Wyniki
Na stronie **wyniki** Wyświetl i Pobierz listę wyników przeglądu. 

![Strona wyników z listą użytkowników, wyników, przyczyn, zrecenzowanych przez, stosowanych przez i Zastosuj wynik](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenci
Wyświetlanie i Dodawanie recenzentów do istniejących przeglądów dostępu. Przypomnij recenzentom, aby dokończyli swoje przeglądy.

![Strona recenzentów nazwa i nazwa główna użytkownika](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-start-access-review.md)
- [Wykonywanie przeglądu dostępu ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-perform-access-review.md)
