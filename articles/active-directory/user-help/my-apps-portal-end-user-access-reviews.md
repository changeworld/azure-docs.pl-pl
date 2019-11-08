---
title: Przeprowadzanie przeglądu dostępu z portalu Moje aplikacje — Azure AD
description: Dowiedz się, jak wyświetlać i zarządzać dostępem zabezpieczeń dla aplikacji i grup w organizacji.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf803027aac7c62ea500d1af104ae3234fadf8b5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73804040"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Przeprowadzanie przeglądu dostępu z poziomu portalu Moje aplikacje

Możesz użyć swojego konta służbowego z portalem **Moje aplikacje** oparte na sieci Web, aby wyświetlać i uruchamiać wiele aplikacji opartych na chmurze w organizacji, aktualizować niektóre informacje o profilu i koncie, wyświetlać informacje o **grupach** i wykonywać  **przeglądy dostępu** do aplikacji i grup. Jeśli nie masz dostępu do portalu **Moje aplikacje** , musisz skontaktować się z pomocą techniczną, aby uzyskać odpowiednie uprawnienia.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania i zarządzania aplikacjami opartymi na chmurze w [dokumentacji zarządzania aplikacjami](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu

Jeśli administrator udzielił Ci uprawnień do wykonywania własnych przeglądów dostępu, możesz zarządzać grupami lub aplikacjami dostępnymi na kafelku **przeglądy dostępu** na stronie portalu **Moje aplikacje** .

>[!Note]
>Jeśli nie widzisz kafelka **przeglądy dostępu** , oznacza to, że nie masz uprawnienia do przeprowadzania przeglądów dostępu lub nie masz żadnych oczekujących przeglądów oczekujących na zatwierdzenie. Jeśli uważasz, że masz dostęp do kafelka, skontaktuj się z działem pomocy technicznej w celu uzyskania pomocy.

### <a name="to-perform-your-access-reviews"></a>Aby przeprowadzić przeglądy dostępu

1. Zaloguj się do konta służbowego.

2. Otwórz przeglądarkę internetową i przejdź do https://myapps.microsoft.comlub Użyj linku dostarczonego przez organizację. Na przykład może nastąpić przekierowanie do dostosowanej strony organizacji, takiej jak https://myapps.microsoft.com/contoso.com.

    Zostanie wyświetlona strona **aplikacje** przedstawiająca wszystkie aplikacje oparte na chmurze należące do organizacji i dostępne do użycia.

    ![Strona aplikacje w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Wybierz kafelek **przeglądy dostępu** , aby wyświetlić listę przeglądów dostępu oczekujących na zatwierdzenie.

    ![Strona przeglądy dostępu z oczekującymi przeglądami dostępu dla organizacji](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Wybierz pozycję **Rozpocznij przegląd** , aby rozpocząć przegląd dostępu.

5. Przejrzyj swój dostęp i ustal, czy nadal jest to konieczne.

    ![Strona przeglądu dostępu zawierająca szczegóły przeglądu](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Jeśli jesteś administratorem i masz uprawnienia do przeglądania dostępu do grup i aplikacji w organizacji, zobaczysz inną stronę. Aby uzyskać więcej informacji na temat przeglądania grup lub aplikacji w organizacji, zobacz [Przegląd dostępu do grup lub aplikacji w przeglądach dostępu do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Wybierz pozycję **tak** , aby zachować dostęp, lub przycisk **nie** , aby usunąć swój dostęp.

    Jeśli wybierzesz opcję **tak**, w polu **Przyczyna** może być konieczne określenie uzasadnienia.

    ![Strona przeglądu dostępu pokazująca pole przyczyna z przykładowym tekstem](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Wybierz pozycję **Prześlij**.

    Przegląd dostępu zostanie zakończony i wrócisz do portalu **My Apps** .

    >[!Note]
    >Dostęp można zmienić w dowolnym momencie do momentu zakończenia okresu przeglądu dostępu. Jeśli usuniesz swój dostęp do aplikacji lub grupy, nie zostanie on natychmiast usunięty. Usunięcie odbywa się po zakończeniu okresu przeglądu dostępu lub gdy administrator zamknie przegląd.

## <a name="next-steps"></a>Następne kroki

- [Dostęp do aplikacji i korzystanie z nich w portalu My Apps](my-apps-portal-end-user-access.md).

- [Zmień informacje o profilu](my-apps-portal-end-user-update-profile.md).

- [Wyświetlanie i aktualizowanie informacji dotyczących grup](my-apps-portal-end-user-groups.md).
