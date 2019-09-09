---
title: Dokończ przegląd dostępu ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak wykonać przegląd dostępu ról usługi Azure AD w Azure AD Privileged Identity Management (PIM) i wyświetlić wyniki
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50ccc208219896e89bcc80f40c846f69c759f9b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804400"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Dokończ przegląd dostępu ról usługi Azure AD w usłudze PIM
Administratorzy ról uprzywilejowanych mogą przeglądać dostęp uprzywilejowany po [rozpoczęciu przeglądu dostępu](pim-how-to-start-security-review.md). Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatycznie wyśle wiadomość e-mail z monitem o sprawdzenie dostępu użytkowników. Jeśli użytkownik nie otrzymał wiadomości e-mail, można wysłać im instrukcje w temacie [Jak przeprowadzić przegląd dostępu](pim-how-to-perform-security-review.md).

Po przekroczeniu okresu przeglądu dostępu lub zakończeniu samooceny przez wszystkich użytkowników wykonaj kroki opisane w tym artykule, aby zarządzać przeglądem i zobaczyć wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu
1. Przejdź do [Azure Portal](https://portal.azure.com/) i wybierz aplikację **Azure AD Privileged Identity Management** na pulpicie nawigacyjnym.
2. Wybierz sekcję **przeglądy dostępu** na pulpicie nawigacyjnym.
3. Wybierz przegląd dostępu, który chcesz zarządzać.

W bloku szczegóły przeglądu dostępu dostępne są różne opcje zarządzania tym przeglądem.

![Przyciski przeglądu dostępu PIM — zrzut ekranu](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Przypomnij
Jeśli przegląd dostępu zostanie skonfigurowany tak, aby użytkownicy sami przeglądali, przycisk **Przypomnij** wyśle powiadomienie. 

### <a name="stop"></a>Stop
Wszystkie przeglądy dostępu mają datę końcową, ale możesz użyć przycisku **Zatrzymaj** , aby zakończyć go na początku. Jeśli żaden użytkownik nie został przejrzany w tym czasie, nie będzie mógł po zatrzymywać przeglądu. Po zatrzymaniu nie można ponownie uruchomić recenzji.

### <a name="apply"></a>Zastosuj
Po zakończeniu przeglądu dostępu, ponieważ osiągnięto datę końcową lub została zatrzymana ręcznie, przycisk **Zastosuj** implementuje wynik przeglądu. W przypadku odmowy dostępu użytkownika do przeglądu jest to krok, który spowoduje usunięcie przypisania roli.  

### <a name="export"></a>Eksportowanie
Jeśli chcesz zastosować wyniki przeglądu dostępu ręcznie, możesz wyeksportować przegląd. Przycisk **Eksportuj** rozpocznie pobieranie pliku CSV. Można zarządzać wynikami w programie Excel lub innych programach, które otwierają pliki CSV.

### <a name="delete"></a>Usuwanie
Jeśli nie interesuje Cię przegląd, usuń go. Przycisk **Usuń** usuwa przegląd z aplikacji PIM.

> [!IMPORTANT]
> Przed usunięciem nie zostanie wyświetlone ostrzeżenie, dlatego należy usunąć ten przegląd. 

## <a name="next-steps"></a>Następne kroki

- [Rozpocznij przegląd dostępu dla ról usługi Azure AD w usłudze PIM](pim-how-to-start-security-review.md)
- [Przeprowadzanie przeglądu dostępu do ról usługi Azure AD w usłudze PIM](pim-how-to-perform-security-review.md)
