---
title: Zarządzanie dostępem organizacji do aplikacji & grupami — Azure AD
description: Dowiedz się, jak przeprowadzić przegląd dostępu, aby zarządzać dostępem do zabezpieczeń aplikacji i grup organizacji z portalu Moje aplikacje.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062386"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Wykonywanie przeglądu dostępu z portalu Moje aplikacje

Za pomocą konta służbowego możesz korzystać z internetowego portalu **Moje aplikacje,** aby uzyskać recenzje dostępu do aplikacji i grup. Przeglądy dostępu ułatwiają zarządzanie nieaktualny dostęp lub zmienianie wymagań dotyczących dostępu oraz zapewniają ich przegląd i zaktualizowanie.

Jeśli nie masz dostępu do portalu **Moje aplikacje,** skontaktuj się z działem pomocy technicznej, aby uzyskać pozwolenie.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników **aplikacji Moje aplikacje.** Jeśli jesteś administratorem, więcej informacji na temat konfigurowania aplikacji opartych na chmurze i zarządzania nimi można znaleźć w [dokumentacji zarządzania aplikacjami](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu

Jeśli administrator udzielił Ci uprawnień do wykonywania własnych recenzji dostępu, możesz zarządzać dostępem do grup lub aplikacji z kafelka **Przeglądy programu Access** na stronie **Portal Moje aplikacje.**

>[!Note]
>Jeśli nie widzisz kafelka **Przeglądy programu Access,** oznacza to, że nie masz uprawnień do wykonywania recenzji dostępu lub że nie masz żadnych oczekujących recenzji oczekujących na zatwierdzenie. Jeśli uważasz, że powinieneś mieć dostęp do kafelka, skontaktuj się z działem pomocy technicznej, aby uzyskać pomoc.

## <a name="to-perform-your-access-reviews"></a>Aby wykonać przeglądy dostępu

1. Zaloguj się na swoje konto służbowe.

2. Otwórz przeglądarkę internetową https://myapps.microsoft.comi przejdź do strony , lub użyj łącza dostarczonego przez organizację. Na przykład może być przekierowany do dostosowanej strony https://myapps.microsoft.com/contoso.comdla organizacji, takich jak .

    Zostanie wyświetlona strona **Aplikacje** z wyświetlonymi wszystkimi aplikacjami w chmurze należącymi do organizacji i dostępnymi do użycia.

    ![Strona Aplikacje w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Wybierz kafelek **Przeglądy programu Access,** aby wyświetlić listę przeglądów dostępu oczekujących na zatwierdzenie.

    ![Strona Przeglądy programu Access z oczekującymi recenzjami dostępu dla instytucji](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Wybierz **rozpocznij weryfikację,** aby rozpocząć przegląd dostępu.

5. Przejrzyj swój dostęp i określ, czy jest to nadal konieczne.

    ![Strona recenzji programu Access ze szczegółami recenzji](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Jeśli jesteś administratorem i możesz przeglądać dostęp organizacji do grup i aplikacji, zobaczysz inną stronę. Aby uzyskać więcej informacji na temat przeglądania grup lub aplikacji w organizacji, zobacz [Przeglądanie dostępu do grup lub aplikacji w recenzjach usługi Azure AD Access](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Wybierz **opcję Tak,** aby zachować dostęp lub **nie,** aby usunąć dostęp.

    Jeśli wybierzesz **tak,** może być konieczne określenie justowania w polu **Przyczyna.**

    ![Strona recenzji programu Access z polem Przyczyna z przykładowym tekstem](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Wybierz pozycję **Prześlij**.

    Twoja recenzja dostępu została zakończona i wrócisz do portalu **Moje aplikacje.**

    >[!Note]
    >Dostęp można zmienić w dowolnym momencie do czasu zakończenia okresu przeglądu dostępu. Jeśli usuniesz dostęp do aplikacji lub grupy, nie zostanie on natychmiast usunięty. Usunięcie następuje po zakończeniu okresu przeglądu dostępu lub gdy administrator zamyka przegląd.

## <a name="next-steps"></a>Następne kroki

- [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](my-apps-portal-end-user-access.md)
- [Zmienianie informacji o profilu](my-apps-portal-end-user-update-profile.md)
- [Wyświetlanie i aktualizowanie informacji związanych z grupami](my-apps-portal-end-user-groups.md)
