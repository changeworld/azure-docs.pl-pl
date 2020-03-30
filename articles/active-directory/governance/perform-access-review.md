---
title: Przeglądanie dostępu do grup & aplikacji w recenzjach dostępu — Usługa Azure AD
description: Dowiedz się, jak przeglądać dostęp członków grupy lub dostępu do aplikacji w recenzjach dostępu usługi Azure Active Directory.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128447"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Przeglądanie dostępu do grup i aplikacji w recenzjach dostępu usługi Azure AD

Usługa Azure Active Directory (Azure AD) upraszcza sposób zarządzania dostępem przedsiębiorstw do grup i aplikacji w usłudze Azure AD i innych usługach online firmy Microsoft za pomocą funkcji o nazwie Przeglądy dostępu usługi Azure AD.

W tym artykule opisano, jak wyznaczony recenzent wykonuje przegląd dostępu dla członków grupy lub użytkowników z dostępem do aplikacji.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Pierwszym krokiem do przeprowadzenia przeglądu dostępu jest znalezienie i otwarcie przeglądu dostępu.

1. Poszukaj wiadomości e-mail od firmy Microsoft z prośbą o sprawdzenie dostępu. Oto przykładowy adres e-mail do przejrzenia dostępu dla grupy.

    ![Przykładowy adres e-mail od firmy Microsoft w celu sprawdzenia dostępu do grupy](./media/perform-access-review/access-review-email.png)

1. Kliknij łącze **Rozpocznij przegląd,** aby otworzyć przegląd dostępu.

Jeśli nie masz wiadomości e-mail, możesz znaleźć oczekujące opinie o dostępie, wykonując następujące kroki.

1. Zaloguj się do portalu MyApps pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portal MyApps z listą aplikacji, do których masz uprawnienia](./media/perform-access-review/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla Twoją nazwę i domyślną organizację. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

1. Kliknij kafelek **Przeglądy programu Access,** aby wyświetlić listę oczekujących przeglądów dostępu.

    Jeśli kafelek nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista oczekujących recenzji dostępu dla aplikacji i grup](./media/perform-access-review/access-reviews-list.png)

1. Kliknij łącze **Rozpocznij recenzowania** dla przeglądu dostępu, który chcesz wykonać.

## <a name="perform-the-access-review"></a>Wykonywanie przeglądu dostępu

Po otwarciu przeglądu dostępu zobaczysz nazwy użytkowników, którzy muszą zostać przejrzeni.

Jeśli żądanie ma na celu sprawdzenie własnego dostępu, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [Przeglądanie dostępu dla siebie do grup lub aplikacji](review-your-access.md).

![Przegląd otwartego dostępu z listą użytkowników, którzy muszą zostać przejrzeni](./media/perform-access-review/perform-access-review.png)

Istnieją dwa sposoby zatwierdzania lub odrzucania dostępu:

- Możesz zatwierdzić lub odmówić dostępu jednemu lub większej liczbie użytkowników, lub
- Można zaakceptować zalecenia systemowe, które są najprostszym i najszybszym sposobem.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Zatwierdzanie lub odmawianie dostępu jednemu lub większej liczbie użytkowników

1. Przejrzyj listę użytkowników, aby zdecydować, czy zatwierdzić, czy odmówić im ciągłego dostępu.

1. Aby zatwierdzić lub odmówić dostępu jednemu użytkownikowi, kliknij wiersz, aby otworzyć okno, aby określić akcję do podjęcia. Aby zatwierdzić lub odmówić dostępu wielu użytkownikom, dodaj znaczniki wyboru obok użytkowników, a następnie kliknij przycisk **Przejrzyj użytkowników X,** aby otworzyć okno, aby określić akcję do podjęcia.

1. Kliknij **przycisk Zatwierdź** lub **Odmów**. Jeśli nie masz pewności, możesz kliknąć przycisk **Nie wiem**. Spowoduje to, że użytkownik zachowa dostęp, ale wybór zostanie odzwierciedlony w dziennikach inspekcji.

    ![Okno akcji z opcjami Zatwierdzanie, Odmów i Nie znam](./media/perform-access-review/approve-deny.png)

1. W razie potrzeby wprowadź przyczynę w polu **Przyczyna.**

    Administrator przeglądu dostępu może wymagać podania przyczyny zatwierdzenia dalszego dostępu lub członkostwa w grupie.

1. Po określeniu akcji do podjęcia kliknij przycisk **Zapisz**.

    Jeśli chcesz zmienić odpowiedź, wybierz wiersz i zaktualizuj odpowiedź. Na przykład można zatwierdzić wcześniej odrzuconego użytkownika lub odmówić wcześniej zatwierdzonemu użytkownikowi. Odpowiedź można zmienić w dowolnym momencie do czasu zakończenia przeglądu dostępu.

    Jeśli istnieje wiele recenzentów, ostatnia przesłana odpowiedź jest rejestrowana. Rozważmy przykład, w którym administrator wyznacza dwóch recenzentów — Alicji i Roberta. Alicja najpierw otwiera przegląd dostępu i zatwierdza dostęp. Przed zakończeniem przeglądu, Robert otwiera przegląd dostępu i odmawia dostępu. Ostatnia odpowiedź odmowy jest to, co jest rejestrowane.

    > [!NOTE]
    > Jeśli użytkownikowi odmówiono dostępu, nie zostaną one natychmiast usunięte. Są one usuwane po zakończeniu przeglądu lub gdy administrator zatrzymuje przegląd.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Zatwierdzanie lub odmawianie dostępu na podstawie zaleceń

Aby ułatwić i przyspieszyć dostęp do opinii, udostępniamy również rekomendacje, które możesz zaakceptować za pomocą jednego kliknięcia. Zalecenia są generowane na podstawie działania logowania użytkownika.

1. Na niebieskim pasku u dołu strony kliknij pozycję **Zaakceptuj rekomendacje**.

    ![Lista recenzji otwartego dostępu z przyciskiem Zaakceptuj rekomendacje](./media/perform-access-review/accept-recommendations.png)

    Zobaczysz podsumowanie zalecanych akcji.

    ![Okno wyświetlaujące podsumowanie zalecanych akcji](./media/perform-access-review/accept-recommendations-summary.png)

1. Kliknij **przycisk Ok,** aby zaakceptować zalecenia.

## <a name="next-steps"></a>Następne kroki

- [Ukończ przegląd dostępu grup lub aplikacji](complete-access-review.md)
