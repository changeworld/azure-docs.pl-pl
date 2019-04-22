---
title: Przegląd dostępu wszystkich użytkowników do grup lub aplikacji w - przeglądów dostępu w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeglądać dostęp do elementów członkowskich grupy lub dostęp do aplikacji w przeglądów dostępu w usłudze Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42fe972b79ecd9bcee65d0664c5d13da02d2238
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496031"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Przegląd dostępu wszystkich użytkowników do grup lub przeglądów dostępu dla aplikacji w usłudze Azure AD

Azure Active Directory (Azure AD) upraszcza przedsiębiorstw zarządzanie dostępem do grup i aplikacji w usłudze Azure AD i przeglądy innych Microsoft Online Services, przy użyciu funkcji o nazwie dostępu usługi Azure AD.

W tym artykule opisano, jak wyznaczone osoby dokonującej przeglądu wykonuje przeglądu dostępu dla członków grupy lub użytkownicy z dostępem do aplikacji.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Pierwszym krokiem do wykonania przeglądu dostępu jest znaleźć i otworzyć Przegląd dostępu.

1. Poszukaj wiadomości e-mail od firmy Microsoft, która prosi o przegląd dostępu wszystkich użytkowników. Poniżej przedstawiono przykładową wiadomość e-mail na przeprowadzić przegląd dostępu dla grupy.

    ![Przejrzyj dostęp do poczty e-mail](./media/perform-access-review/access-review-email.png)

1. Kliknij przycisk **rozpocząć Przegląd** link umożliwiający otworzenie Przegląd dostępu.

Jeśli nie masz wiadomości e-mail można znaleźć, przeglądy dostępu w usłudze usługi oczekujące, wykonując następujące kroki.

1. Zaloguj się do portalu MyApps pod adresem [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portalu MyApps](./media/perform-access-review/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla Twoją nazwę i domyślną organizację. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

1. Kliknij przycisk **przeglądów dostępu** Kafelek, aby wyświetlić listę przeglądów dostępu oczekujące.

    Jeśli kafelek nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista przeglądów dostępu](./media/perform-access-review/access-reviews-list.png)

1. Kliknij przycisk **Rozpocznij Przegląd** link dla przeglądu dostępu, którą chcesz wykonać.

## <a name="perform-the-access-review"></a>Przeprowadzenie przeglądu dostępu

Po otwarciu przeglądu dostępu, zobaczysz nazwy użytkowników, którzy muszą być analizowane.

Jeśli żądanie ma przeglądu własnego dostępu, strona będzie wyglądała różne. Aby uzyskać więcej informacji, zobacz [Przegląd dostępu wszystkich użytkowników dla siebie do grupy lub aplikacji](review-your-access.md).

![Przeprowadzenie przeglądu dostępu](./media/perform-access-review/perform-access-review.png)

Istnieją dwa sposoby, można zatwierdzić lub odmowa dostępu:

- Możesz mogli zatwierdzać lub odrzucać każde żądanie pojedynczo, lub
- Można zaakceptować zalecenia systemu określone, co jest najprostszym i najszybszy sposób.

### <a name="approve-or-deny-access-for-each-request"></a>Zatwierdź lub Odrzuć dostęp dla każdego żądania

1. Przejrzyj listę użytkowników, aby zdecydować, czy Zatwierdź lub Odrzuć ich przedłużenia dostępu.

1. Aby zatwierdzić lub odrzucić każdego żądania, kliknij wiersz, aby otworzyć okno, aby określić akcję do wykonania.

1. Kliknij przycisk **zatwierdzić** lub **Odmów**. Jeśli nie masz, możesz kliknąć **nie znasz**. To spowoduje użytkownika, jego dostępu, ale zaznaczenia zostaną odzwierciedlone w dziennikach inspekcji.

    ![Przeprowadzenie przeglądu dostępu](./media/perform-access-review/approve-deny.png)

1. Jeśli to konieczne, podaj przyczynę w **Przyczyna** pole.

    Administrator przeglądu dostępu może wymagać podania przyczyny zatwierdzenie przedłużenia dostępu oraz członkostwa w grupie.

1. Po określeniu akcję do wykonania, kliknij przycisk **Zapisz**.

    Jeśli chcesz zmienić odpowiedzi, zaznacz wiersz i zaktualizuj odpowiedzi. Na przykład możesz zatwierdzić wcześniej zabronionych użytkownika lub Odmów użytkownikowi zatwierdzonego wcześniej. Twoja odpowiedź można zmienić w dowolnym momencie do czasu przeglądu dostępu została zakończona.

    W przypadku wielu recenzentów, są rejestrowane ostatnia odpowiedź przesłane. Rozważmy przykład, jeśli administrator wyznacza dwóch osób dokonujących przeglądu — Alice i Bob. Alicja najpierw otworzy Przegląd dostępu i zatwierdza dostępu. Przed zakończeniem przeglądu Robert otwiera Przegląd dostępu i nie zezwala na dostęp. Ostatni Odmów, co to jest rejestrowany jest odpowiedź.

    > [!NOTE]
    > Jeśli użytkownikowi odmówiono dostępu, nie są one usuwane natychmiast. Zostaną usunięte po zakończeniu przeglądu lub administrator zatrzymania przeglądu.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Zatwierdź lub Odrzuć dostęp na podstawie zaleceń

Aby wprowadzić łatwiejsze i szybsze przeglądów dostępu dla Ciebie, oferujemy również zalecenia, które mogą akceptować za pomocą jednego kliknięcia. Zalecenia są generowane na podstawie aktywności logowania użytkownika.

1. Niebieski pasek u dołu strony, kliknij **Zaakceptuj zalecenia**.

    ![Zaakceptuj zalecenia](./media/perform-access-review/accept-recommendations.png)

    Zostanie wyświetlone podsumowanie zalecanych akcji.

    ![Zaakceptuj zalecenia podsumowania](./media/perform-access-review/accept-recommendations-summary.png)

1. Kliknij przycisk **Ok** na przyjęcie zaleceń.

## <a name="next-steps"></a>Kolejne kroki

- [Kończenie przeglądu dostępu grup lub aplikacji](complete-access-review.md)
