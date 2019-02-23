---
title: Przegląd dostępu wszystkich użytkowników dla siebie do grupy lub aplikacji w przeglądach dostępu usługi Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeglądać dostępu do grup lub aplikacji na platformie Azure przeglądy dostępu w Active Directory.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f90a5aa3f201fa5d1578ac63526be26377aedf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731212"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Przegląd dostępu wszystkich użytkowników dla siebie do grupy lub aplikacji w przeglądach dostępu usługi Azure AD

Azure Active Directory (Azure AD) upraszcza sposób przedsiębiorstw zarządzanie dostępem do grup ani aplikacji w usłudze Azure AD i Microsoft Online Services przy użyciu funkcji brzmią przeglądów dostępu usługi Azure AD.

W tym artykule opisano, jak przejrzeć swój własny dostęp do grupy lub aplikacji.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Pierwszym krokiem do wykonania przeglądu dostępu jest znaleźć i otworzyć Przegląd dostępu.

1. Poszukaj wiadomości e-mail od firmy Microsoft, która prosi o przegląd dostępu wszystkich użytkowników. Poniżej przedstawiono przykładową wiadomość e-mail przejrzeć swój dostęp do grupy.

    ![Przejrzyj dostęp do poczty e-mail](./media/review-your-access/access-review-email.png)

1. Kliknij przycisk **Przegląd dostępu wszystkich użytkowników** link umożliwiający otworzenie Przegląd dostępu.

Jeśli nie masz wiadomości e-mail można znaleźć, przeglądy dostępu w usłudze usługi oczekujące, wykonując następujące kroki.

1. Zaloguj się do portalu MyApps pod adresem [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portalu MyApps](./media/review-your-access/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla nazwę i domyślny organizacji. Jeśli więcej niż jednej z organizacji jest wymieniony, wybierz organizacji, który zażądał przeglądu dostępu.

1. W prawej części strony kliknij **przeglądów dostępu** Kafelek, aby wyświetlić listę przeglądów dostępu oczekujące.

    Jeśli Kafelek nie jest widoczna, nie Brak sprawdzeń dostępu do przeprowadzenia tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista przeglądów dostępu](./media/review-your-access/access-reviews-list.png)

1. Kliknij przycisk **Rozpocznij Przegląd** link dla przeglądu dostępu, którą chcesz wykonać.

## <a name="perform-the-access-review"></a>Przeprowadzenie przeglądu dostępu

Po otwarciu przeglądu dostępu, możesz zobaczyć dostępu.

1. Przejrzeć swój dostęp i zdecyduj, czy nadal potrzebują dostępu.

    Jeśli żądanie ma przeprowadzić przegląd dostępu dla innych osób, strona będzie wyglądała różne. Aby uzyskać więcej informacji, zobacz [Przegląd dostępu wszystkich użytkowników do grup ani aplikacji](perform-access-review.md).

    ![Przeprowadzenie przeglądu dostępu](./media/review-your-access/perform-access-review.png)

1. Kliknij przycisk **tak** Aby zachować dostęp do usługi, lub kliknij przycisk **nie** o usunięcie Twojego dostępu.

1. Jeśli klikniesz **tak**, może być konieczne określenie uzasadnienie w **Przyczyna** pole.

    ![Przeprowadzenie przeglądu dostępu](./media/review-your-access/perform-access-review-submit.png)

1. Kliknij przycisk **Prześlij**.

    Wybór zostanie przesłany i powrót do portalu MyApps.

    Jeśli chcesz zmienić odpowiedzi, ponownym otwarciu strony przeglądów dostępu i zaktualizować odpowiedzi. Twoja odpowiedź można zmienić w dowolnym momencie do czasu przeglądu dostępu została zakończona.

    > [!NOTE]
    > Wskazano, już nie potrzebujesz dostępu, możesz nie zostaną usunięte natychmiast. Zostaną usunięte po zakończeniu przeglądu lub administrator zatrzymania przeglądu.

## <a name="next-steps"></a>Kolejne kroki

- [Kończenie przeglądu dostępu grup lub aplikacji](complete-access-review.md)
