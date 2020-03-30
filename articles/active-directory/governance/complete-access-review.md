---
title: Ukończ przegląd dostępu grup & aplikacji — Usługa Azure AD
description: Dowiedz się, jak ukończyć przegląd dostępu członków grupy lub dostępu do aplikacji w przeglądach dostępu usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932541"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Ukończ przegląd dostępu grup i aplikacji w recenzjach dostępu usługi Azure AD

Jako administrator [tworzysz przegląd dostępu grup lub aplikacji,](create-access-review.md) a recenzenci [przeprowadzają przegląd dostępu](perform-access-review.md). W tym artykule opisano, jak wyświetlić wyniki przeglądu dostępu i zastosować wyniki.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — warstwa Premium P2
- Administrator globalny, administrator użytkownika, administrator zabezpieczeń lub czytnik zabezpieczeń

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Wyświetlanie recenzji dostępu

Możesz śledzić postępy, gdy recenzenci dokończą swoje recenzje.

1. Zaloguj się do witryny Azure portal i otwórz [stronę zarządzania tożsamościami](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. W menu po lewej stronie kliknij pozycję **Przeglądy programu Access**.

1. Na liście kliknij recenzję dostępu.

    Aby wyświetlić serię przeglądów dostępu, przejdź do przeglądu dostępu, a nadchodzące wystąpienia znajdziesz w zaplanowanych recenzjach.

    Na **stronie Przegląd** można zobaczyć postęp. Żadne prawa dostępu nie są zmieniane w katalogu, dopóki przegląd nie zostanie ukończony.

    ![Postęp przeglądów programu Access](./media/complete-access-review/overview-progress.png)

1. Jeśli chcesz zatrzymać przegląd dostępu przed osiągnięciem zaplanowanej daty zakończenia, kliknij przycisk **Zatrzymaj.**

    Po zatrzymaniu recenzji recenzenci nie będą już mogli udzielać odpowiedzi. Nie można ponownie uruchomić recenzji po jej zatrzymaniu.

1. Jeśli nie jesteś już zainteresowany recenzją dostępu, możesz ją usunąć, klikając przycisk **Usuń.**

## <a name="apply-the-changes"></a>Stosowanie zmian

Jeśli **automatyczne stosowanie wyników do zasobu** zostało włączone i na podstawie wybranych opcji w **ustawieniach Po zakończeniu,** automatyczne stosowanie zostanie wykonane po dacie zakończenia przeglądu lub po ręcznym zatrzymaniu recenzji.

Jeśli **automatyczne stosowanie wyników do zasobu** nie zostało włączone dla przeglądu, kliknij przycisk **Zastosuj,** aby ręcznie zastosować zmiany. Jeśli dostęp użytkownika został odrzucony w recenzji, po **kliknięciu przycisku Zastosuj**usługa Azure AD spowoduje usunięcie ich przypisania członkostwa lub aplikacji.

![Stosowanie zmian w przeglądzie dostępu](./media/complete-access-review/apply-changes.png)

Stan przeglądu zmieni się z **Zakończone** przez stany **pośrednie,** takie jak Zastosowanie i na koniec stan **Wynik zastosowany**. Należy oczekiwać, aby zobaczyć odmówiono użytkowników, jeśli istnieje, są usuwane z członkostwa w grupie lub przypisania aplikacji w ciągu kilku minut.

Skonfigurowane automatyczne stosowanie recenzji lub wybranie opcji **Zastosuj** nie ma wpływu na grupę pochodzącą z katalogu lokalnego lub grupy dynamicznej. Jeśli chcesz zmienić grupę, która pochodzi lokalnie, pobierz wyniki i zastosuj te zmiany do reprezentacji grupy w tym katalogu.

## <a name="retrieve-the-results"></a>Pobieranie wyników

Aby wyświetlić wyniki dla jednorazowej recenzji dostępu, kliknij stronę **Wyniki.** Aby wyświetlić tylko dostęp użytkownika, w polu Wyszukiwania wpisz nazwę wyświetlaną lub główną nazwę użytkownika, którego dostęp został zweryfikowany.

![Pobieranie wyników do przeglądu dostępu](./media/complete-access-review/retrieve-results.png)

Aby wyświetlić postęp cyklicznej recenzji aktywnego dostępu, kliknij stronę **Wyniki.**

Aby wyświetlić wyniki ukończonego wystąpienia przeglądu dostępu, które jest cykliczne, kliknij pozycję **Przejrzyj historię**, a następnie wybierz konkretne wystąpienie z listy zakończonych wystąpień przeglądu dostępu na podstawie daty rozpoczęcia i zakończenia wystąpienia. Wyniki tego wystąpienia można uzyskać ze strony **Wyniki.**

Aby pobrać wszystkie wyniki przeglądu dostępu, kliknij przycisk **Pobierz.** Wynikowy plik CSV można przeglądać w programie Excel lub w innych programach, które otwierają zakodowane pliki CSV UTF-8.

## <a name="remove-users-from-an-access-review"></a>Usuwanie użytkowników z recenzji dostępu

 Domyślnie usunięty użytkownik pozostaje usunięty w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby.  Po 30 dniach użytkownik jest trwale usuwany.  Ponadto za pomocą portalu usługi Azure Active Directory administrator globalny może jawnie [trwale usunąć niedawno usuniętego użytkownika](../fundamentals/active-directory-users-restore.md) przed osiągnięciem tego okresu.  Gdy użytkownik zostanie trwale usunięty, nowe dane dotyczące tego użytkownika będą usuwane z aktywnych przeglądów dostępu.  Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD](manage-user-access-with-access-reviews.md)
- [Zarządzanie dostępem gości za pomocą przeglądów dostępu w usłudze Azure AD](manage-guest-access-with-access-reviews.md)
- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
