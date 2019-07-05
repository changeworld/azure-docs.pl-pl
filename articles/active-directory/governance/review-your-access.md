---
title: Przegląd dostępu wszystkich użytkowników dla siebie do grupy lub aplikacjami w programie - przeglądów dostępu w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeglądać dostępu do grup lub aplikacji w przeglądów dostępu w usłudze Azure Active Directory.
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
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22f12255bf14ca424a8a79107e7ca8e403552b62
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471744"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Przegląd dostępu wszystkich użytkowników dla siebie do grupy lub przeglądów dostępu dla aplikacji w usłudze Azure AD

Azure Active Directory (Azure AD) upraszcza przedsiębiorstw zarządzanie dostępem do grup ani aplikacji w usłudze Azure AD i przeglądy innych Microsoft Online Services, przy użyciu funkcji o nazwie dostępu usługi Azure AD.

W tym artykule opisano, jak przejrzeć swój własny dostęp do grupy lub aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — warstwa Premium P2

Aby uzyskać więcej informacji, zobacz [użytkowników, którzy muszą mieć licencje?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Pierwszym krokiem do wykonania przeglądu dostępu jest znaleźć i otworzyć Przegląd dostępu.

1. Poszukaj wiadomości e-mail od firmy Microsoft, która prosi o przegląd dostępu wszystkich użytkowników. Poniżej przedstawiono przykładową wiadomość e-mail przejrzeć swój dostęp do grupy.

    ![Przykładową wiadomością e-mail od firmy Microsoft, aby przejrzeć swój dostęp do grupy](./media/review-your-access/access-review-email.png)

1. Kliknij przycisk **Przegląd dostępu wszystkich użytkowników** link umożliwiający otworzenie Przegląd dostępu.

Jeśli nie masz wiadomości e-mail można znaleźć, przeglądy dostępu w usłudze usługi oczekujące, wykonując następujące kroki.

1. Zaloguj się do portalu MyApps pod adresem [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portalu MyApps, wyświetlanie listy aplikacji, do których masz uprawnienia do](./media/review-your-access/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla Twoją nazwę i domyślną organizację. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

1. W prawej części strony kliknij **przeglądów dostępu** Kafelek, aby wyświetlić listę przeglądów dostępu oczekujące.

    Jeśli kafelek nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista przeglądów dostępu oczekujące dla aplikacji i grup](./media/review-your-access/access-reviews-list.png)

1. Kliknij przycisk **Rozpocznij Przegląd** link dla przeglądu dostępu, którą chcesz wykonać.

## <a name="perform-the-access-review"></a>Przeprowadzenie przeglądu dostępu

Po otwarciu przeglądu dostępu, możesz zobaczyć dostępu.

1. Przejrzeć swój dostęp i zdecyduj, czy nadal potrzebują dostępu.

    Jeśli żądanie ma przeprowadzić przegląd dostępu dla innych osób, strona będzie wyglądała różne. Aby uzyskać więcej informacji, zobacz [Przegląd dostępu wszystkich użytkowników do grup ani aplikacji](perform-access-review.md).

    ![Przegląd otwartego dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/perform-access-review.png)

1. Kliknij przycisk **tak** Aby zachować dostęp do usługi, lub kliknij przycisk **nie** o usunięcie Twojego dostępu.

1. Jeśli klikniesz **tak**, może być konieczne określenie uzasadnienie w **Przyczyna** pole.

    ![Ukończono przeglądu dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/perform-access-review-submit.png)

1. Kliknij przycisk **Submit** (Prześlij).

    Wybór zostanie przesłany i powrót do portalu MyApps.

    Jeśli chcesz zmienić odpowiedzi, ponownym otwarciu strony przeglądów dostępu i zaktualizować odpowiedzi. Twoja odpowiedź można zmienić w dowolnym momencie do czasu przeglądu dostępu została zakończona.

    > [!NOTE]
    > Wskazano, już nie potrzebujesz dostępu, możesz nie zostaną usunięte natychmiast. Zostaną usunięte po zakończeniu przeglądu lub administrator zatrzymania przeglądu.

## <a name="next-steps"></a>Kolejne kroki

- [Kończenie przeglądu dostępu grup lub aplikacji](complete-access-review.md)
