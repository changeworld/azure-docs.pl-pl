---
title: Zakończ przegląd dostępu do ról usługi Azure AD w usłudze PIM — usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak ukończyć przegląd ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM) i wyświetlić wyniki
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022278"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Ukończ przegląd ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Administratorzy ról uprzywilejowanych mogą przeglądać dostęp uprzywilejowany po [rozpoczęciu przeglądu dostępu](pim-how-to-start-security-review.md).  Zarządzanie tożsamościami uprzywilejowanymi (PIM) automatycznie wyśle wiadomość e-mail do użytkowników w organizacji usługi Azure Active Directory (Azure AD), z prośbą o sprawdzenie ich dostępu. Jeśli użytkownik nie otrzymał wiadomości e-mail, można wysłać mu instrukcje [dotyczące sposobu przeprowadzania przeglądu dostępu](pim-how-to-perform-security-review.md).

Po zakończeniu okresu przeglądu dostępu lub wszyscy użytkownicy zakończyli samodzielną weryfikację, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądem i zobaczyć wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu

1. Przejdź do [witryny Azure portal](https://portal.azure.com/) i wybierz usługę Azure AD **Privileged Identity Management** na pulpicie nawigacyjnym.
1. Wybierz sekcję **Przeglądy programu Access** na pulpicie nawigacyjnym.
1. Wybierz recenzję dostępu, którą chcesz zarządzać.

Na bloku szczegółów przeglądu dostępu istnieje wiele opcji zarządzania tym przeglądem.

![Przyciski przeglądu dostępu do uprzywilejowanego zarządzania tożsamościami — zrzut ekranu](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Przypomnieć

Jeśli przegląd dostępu jest skonfigurowany tak, aby użytkownicy przeglądali siebie, przycisk **Przypomnij** wysyła powiadomienie.

### <a name="stop"></a>Stop

Wszystkie recenzje dostępu mają datę zakończenia, ale możesz użyć przycisku **Zatrzymaj,** aby zakończyć ją wcześniej. Jeśli którykolwiek z użytkowników nie został sprawdzony w tym czasie, nie będzie mógł po zatrzymaniu recenzji. Nie można ponownie uruchomić recenzji po jej zatrzymaniu.

### <a name="apply"></a>Zastosuj

Po zakończeniu przeglądu dostępu, ponieważ osiągnięto datę zakończenia lub zatrzymano go ręcznie, **Przycisk Zastosuj** implementuje wynik przeglądu. Jeśli dostęp użytkownika został odrzucony w recenzji, jest to krok, który spowoduje usunięcie ich przypisania roli.  

### <a name="export"></a>Eksportowanie

Jeśli chcesz zastosować wyniki przeglądu dostępu ręcznie, można wyeksportować recenzję. Przycisk **Eksportuj** rozpocznie pobieranie pliku CSV. Można zarządzać wynikami w programie Excel lub innych programach, które otwierają pliki CSV.

### <a name="delete"></a>Usuń

Jeśli nie jesteś zainteresowany przeglądem dalej, usuń go. Przycisk **Usuń** usuwa recenzję z usługi Zarządzanie tożsamościami uprzywilejowanymi.

> [!IMPORTANT]
> Nie będziesz musiał potwierdzać tej destrukcyjnej zmiany, więc sprawdź, czy chcesz usunąć tę recenzję.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie przeglądu dostępu dla ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-start-security-review.md)
- [Wykonywanie przeglądu dostępu do moich ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-perform-security-review.md)
