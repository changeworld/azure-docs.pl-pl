---
title: Kończenie przeglądu dostępu grup lub aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zakończyć Przegląd dostępu członków grupy lub dostęp do aplikacji w przeglądów dostępu w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec3909ffbb624284f999360140b7454098643062
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473370"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Kończenie przeglądu dostępu grup lub przeglądów dostępu dla aplikacji w usłudze Azure AD

Jako administrator możesz [tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md) i recenzentów [przeprowadzenie przeglądu dostępu](perform-access-review.md). W tym artykule opisano, jak wyświetlić wyniki przeglądu dostępu i Zastosuj wyniki.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — warstwa Premium P2
- Administrator globalny, administrator użytkowników, administrator zabezpieczeń lub czytelnika zabezpieczeń

Aby uzyskać więcej informacji, zobacz [użytkowników, którzy muszą mieć licencje?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Wyświetl przeglądu dostępu

Postęp można śledzić, jak recenzenci wykonać ich przeglądów.

1. Zaloguj się w witrynie Azure portal i Otwórz [strony zarządzania tożsamościami oraz](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. W menu po lewej stronie kliknij **przeglądów dostępu**.

1. Na liście kliknij przycisk przeglądu dostępu.

    Aby wyświetlić szereg dostępu recenzje, przejdź do przeglądu dostępu, i znajdziesz kolejnych wystąpień w przeglądach zaplanowane.

    Na **Przegląd** stronie wyświetlany jest postęp. Nie prawa dostępu są zmieniane w katalogu, do momentu ukończenia przeglądu.

    ![Przeglądy dostępu w toku](./media/complete-access-review/overview-progress.png)

1. Jeśli chcesz zatrzymać przeglądu dostępu przed osiągnęła ona planowanej dacie zakończenia, kliknij pozycję **zatrzymać** przycisku.

    Gdy zatrzymania przeglądu, osób dokonujących przeglądu nie będą już mogli udzielać odpowiedzi. Nie można ponownie uruchomić przeglądu, po jej zatrzymaniu.

1. Jeśli interesuje Cię już przeglądu dostępu, możesz go usunąć, klikając **Usuń** przycisku.

## <a name="apply-the-changes"></a>Zastosuj zmiany

Jeśli **automatycznie Zastosuj wyniki do zasobu** został włączony i na podstawie dokonanego wyboru w **ustawienia działań po zakończeniu**, automatycznie — Zastosuj będzie wykonywany po Data zakończenia przeglądu lub jeśli ręcznie zatrzymaj Przegląd.

Jeśli **automatycznie Zastosuj wyniki do zasobu** nie został włączony dla przeglądu, kliknij przycisk **Zastosuj** ręcznie zastosować zmiany. Jeśli w przeglądzie, nastąpiła odmowa dostępu użytkownika, po kliknięciu **Zastosuj**, usługi Azure AD spowoduje usunięcie ich przypisania członkostwa lub aplikacji.

![Zastosuj zmiany przeglądu dostępu](./media/complete-access-review/apply-changes.png)

Stan przeglądu ulegnie zmianie z **Ukończono** za pośrednictwem pośrednich stanów, takie jak **stosowanie** a na koniec do stanu **zastosowano wynik**. Należy się spodziewać się zablokowani użytkownicy ewentualnej usuwany z przypisania grupy członkostwa lub aplikacji w ciągu kilku minut.

Automatycznie skonfigurowany, zastosowanie przeglądu lub wybierając **Zastosuj** nie ma wpływu na grupy, który pochodzi z katalogu lokalnego lub grupy dynamicznej. Jeśli chcesz zmienić grupę, która pochodzi w środowisku lokalnym, Pobierz wyniki i stosowanie tych zmian na reprezentację w postaci grupy, w tym katalogu.

## <a name="retrieve-the-results"></a>Pobieranie wyników

Aby wyświetlić wyniki do przeglądu dostępu jednorazowe, kliknij przycisk **wyniki** strony. Aby wyświetlić tylko dostępu użytkownika, w polu wyszukiwania, wpisz nazwę wyświetlaną lub główną nazwę użytkownika, do których dostęp został zrecenzowany.

![Pobieranie wyników przeglądu dostępu](./media/complete-access-review/retrieve-results.png)

Aby wyświetlić postęp przeglądu dostępu aktywny, która ma charakter cykliczny, wybierz polecenie **wyniki** strony.

Aby wyświetlić wyniki ukończonemu wystąpieniu przeglądu dostępu, który ma charakter cykliczny, kliknij przycisk **przeglądać historię**, a następnie wybierz konkretne wystąpienie z listy wystąpień przeglądów dostępu ukończone, oparte na wystąpienie rozpoczęcia i Data zakończenia. Wyniki tego wystąpienia można uzyskać z **wyniki** strony.

Aby pobrać wyniki przeglądu dostępu, kliknij pozycję **Pobierz** przycisku. Wynikowy plik CSV można wyświetlać w programie Excel lub w innych programach, które otwierają UTF-8 zakodowane pliki CSV.

## <a name="remove-users-from-an-access-review"></a>Usuwanie użytkowników z przeglądu dostępu

 Domyślnie usunięty użytkownik pozostaje usunięty w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby.  Po 30 dniach użytkownik jest trwale usuwany.  Ponadto przy użyciu portalu usługi Azure Active Directory administrator globalny może jawnie [trwale usunąć niedawno usuniętego użytkownika](../fundamentals/active-directory-users-restore.md) przed upłynięciem tego okresu.  Gdy użytkownik zostanie trwale usunięty, nowe dane dotyczące tego użytkownika będą usuwane z aktywnych przeglądów dostępu.  Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD](manage-user-access-with-access-reviews.md)
- [Zarządzanie dostępem gości za pomocą przeglądów dostępu w usłudze Azure AD](manage-guest-access-with-access-reviews.md)
- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
