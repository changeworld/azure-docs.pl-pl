---
title: Przeglądanie dostępu do grup & aplikacji w recenzjach dostępu — Usługa Azure AD
description: Dowiedz się, jak przejrzeć własny dostęp do grup lub aplikacji w recenzjach dostępu usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422403"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Przeglądanie dostępu do grup lub aplikacji w recenzjach dostępu usługi Azure AD

Usługa Azure Active Directory (Azure AD) upraszcza sposób zarządzania dostępem przedsiębiorstw do grup lub aplikacji w usłudze Azure AD i innych usługach Online firmy Microsoft za pomocą funkcji o nazwie Przeglądy dostępu usługi Azure AD.

W tym artykule opisano sposób przeglądania własnego dostępu do grupy lub aplikacji.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Pierwszym krokiem do przeprowadzenia przeglądu dostępu jest znalezienie i otwarcie przeglądu dostępu.

1. Poszukaj wiadomości e-mail od firmy Microsoft z prośbą o sprawdzenie dostępu. Oto przykładowy adres e-mail, który służy do przeglądania dostępu do grupy.

    ![Przykładowy adres e-mail od firmy Microsoft w celu sprawdzenia dostępu do grupy](./media/review-your-access/access-review-email.png)

1. Kliknij **łącze Przejrzyj dostęp,** aby otworzyć przegląd dostępu.

Jeśli nie masz wiadomości e-mail, możesz znaleźć oczekujące opinie o dostępie, wykonując następujące kroki.

1. Zaloguj się do portalu MyApps pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portal MyApps z listą aplikacji, do których masz uprawnienia](./media/review-your-access/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla Twoją nazwę i domyślną organizację. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

1. Po prawej stronie strony kliknij kafelek **Przeglądy programu Access,** aby wyświetlić listę oczekujących przeglądów dostępu.

    Jeśli kafelek nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista oczekujących recenzji dostępu dla aplikacji i grup](./media/review-your-access/access-reviews-list.png)

1. Kliknij łącze **Rozpocznij recenzowania** dla przeglądu dostępu, który chcesz wykonać.

## <a name="perform-the-access-review"></a>Wykonywanie przeglądu dostępu

Po otwarciu przeglądu dostępu możesz zobaczyć swój dostęp.

1. Sprawdź swój dostęp i zdecyduj, czy nadal potrzebujesz dostępu.

    Jeśli żądanie ma na celu sprawdzenie dostępu dla innych osób, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md).

    ![Przegląd otwartego dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/perform-access-review.png)

1. Kliknij **przycisk Tak,** aby zachować dostęp, lub kliknij przycisk **Nie,** aby usunąć dostęp.

1. Jeśli klikniesz przycisk **Tak,** może być konieczne określenie justowania w polu **Przyczyna.**

    ![Ukończona recenzja dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/perform-access-review-submit.png)

1. Kliknij **przycisk Prześlij**.

    Twój wybór zostanie przesłany i wrócisz do portalu MyApps.

    Jeśli chcesz zmienić odpowiedź, otwórz ponownie stronę recenzji dostępu i zaktualizuj odpowiedź. Odpowiedź można zmienić w dowolnym momencie do czasu zakończenia przeglądu dostępu.

    > [!NOTE]
    > Jeśli wskażesz, że nie potrzebujesz już dostępu, nie zostaniesz natychmiast usunięty. Po zakończeniu przeglądu lub zatrzymaniu recenzji administrator zostanie usunięty.

## <a name="next-steps"></a>Następne kroki

- [Ukończ przegląd dostępu grup lub aplikacji](complete-access-review.md)
