---
title: Uzyskaj pomoc dotyczącą portalu Moje aplikacje — Usługa Azure Active Directory| Dokumenty firmy Microsoft
description: Uzyskaj pomoc dotyczącą logowania się do witryny i wykonywania typowych zadań w portalu Moje aplikacje.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253158"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Rozwiązywanie problemów z portalem Moje aplikacje

Jeśli występują problemy z logowaniem się do portalu **Moje aplikacje** lub korzystaniem z niego, wypróbuj te wskazówki dotyczące rozwiązywania problemów, zanim skontaktujesz się z działem pomocy technicznej lub administratorem w celu uzyskania pomocy.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mam problem z zainstalowaniem rozszerzenia logowania moje aplikacje bezpieczne

Jeśli masz problemy z zainstalowaniem rozszerzenia logowania moje aplikacje bezpieczne:

- Upewnij się, że korzystasz z obsługiwanej przeglądarki, w tym:

    - **Microsoft Edge.** Działa w systemie Windows 10 Anniversary Edition lub nowszym.

    - **Google Chrome.** Działa w systemie Windows 7 lub nowszym oraz w systemie Mac OS X lub nowszym.

    - **Mozilla Firefox 26.0 lub nowsza.** Działa w systemie Windows XP z dodatkiem SP2 lub nowszym oraz w systemie Mac OS X 10.6 lub nowszym.

    - **Program Internet Explorer 11.** Działa w systemie Windows 7 lub nowszym (ograniczona obsługa).

- Upewnij się, że ustawienia rozszerzenia przeglądarki są włączone.

- Spróbuj ponownie uruchomić przeglądarkę i ponownie zalogować się do portalu **Moje aplikacje.**

- Spróbuj wyczyścić pliki cookie przeglądarki, a następnie uruchom ponownie i zaloguj się ponownie w portalu **Moje aplikacje.**

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nie mogę zalogować się do portalu **Moje aplikacje**

Jeśli masz problemy z zalogowaniem się do portalu **Moje aplikacje,** możesz wypróbować następujące rozwiązania:

- Upewnij się, że używasz odpowiedniego adresu URL. Powinna to https://myapps.microsoft.com być lub dostosowana strona dla https://myapps.microsoft.com/contoso.comtwojej organizacji, na przykład .

- Upewnij się, że hasło jest poprawne i nie wygasło. Aby uzyskać więcej informacji, zobacz [Resetowanie hasła służbowego lub szkolnego](active-directory-passwords-update-your-own-password.md).

- Upewnij się, że informacje weryfikacyjne są aktualne i dokładne. Aby uzyskać więcej informacji, zobacz Co oznacza dla [Changing your security info methods and information](security-info-add-update-methods-overview.md) [mnie uwierzytelnianie wieloskładnikowe platformy Azure?](multi-factor-authentication-end-user.md)

- Dodaj adres URL **portalu moja aplikacja** do ustawienia Właściwości internetowe > zabezpieczenia > **zaufane witryny.**

- Wyczyść pamięć podręczną przeglądarki i spróbuj zalogować się ponownie.

## <a name="my-password-isnt-working"></a>Moje hasło nie działa

Jeśli nie pamiętasz hasła, nigdy nie otrzymałeś go od organizacji, nie masz konta lub chcesz zmienić hasło, zobacz [Pomoc, nie pamiętam hasła usługi Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Chcę mieć możliwość zresetowania własnego hasła

Aby móc zresetować własne hasło, administrator musi najpierw włączyć tę funkcję w organizacji, a następnie zaktualizować i zweryfikować wymagane metody weryfikacji. Aby uzyskać więcej informacji na temat aktualizowania metod weryfikacji, zobacz [Rejestrowanie samoobsługowego resetowania hasła](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Podczas uruchamiania aplikacji owiaduję odmową dostępu

Jeśli po uruchomieniu aplikacji z portalu **Moja aplikacja** jest wyświetlany komunikat **Odmowa dostępu,** możesz wypróbować następujące rozwiązanie:

- Upewnij się, że zainstalowano [rozszerzenie moje aplikacje bezpieczne logowanie](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) i że używasz [obsługiwanej przeglądarki](my-apps-portal-end-user-access.md#supported-browsers).

- Upewnij się, że używasz odpowiedniego adresu URL aplikacji i że adres URL znajduje się na liście **Właściwości internetowe > Zabezpieczenia > Zaufane witryny.**

- Upewnij się, że hasło jest poprawne i nie wygasło. Aby uzyskać więcej informacji, zobacz [Resetowanie hasła służbowego lub szkolnego](active-directory-passwords-update-your-own-password.md).

- Upewnij się, że informacje weryfikacyjne są aktualne i dokładne. Aby uzyskać więcej informacji, zobacz Co oznacza dla [Changing your security info methods and information](security-info-add-update-methods-overview.md) [mnie uwierzytelnianie wieloskładnikowe platformy Azure?](multi-factor-authentication-end-user.md)

- Wyczyść pamięć podręczną przeglądarki i spróbuj zalogować się ponownie.

Jeśli po wypróbowaniu tych czynności nadal nie możesz uzyskać dostępu do aplikacji, skontaktuj się z pomocą techniczną organizacji.

## <a name="next-steps"></a>Następne kroki

Po zalogowaniu się do portalu **Moje aplikacje** możesz również zaktualizować informacje o profilu i koncie, informacje o grupie i informacje o recenzji dostępu (jeśli masz uprawnienia).

- [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](my-apps-portal-end-user-access.md).

- [Zmień informacje profilowe](my-apps-portal-end-user-update-profile.md).

- [Wyświetlanie i aktualizowanie informacji związanych z grupami](my-apps-portal-end-user-groups.md).

- [Wykonywanie własnych recenzji dostępu](my-apps-portal-end-user-access-reviews.md).
