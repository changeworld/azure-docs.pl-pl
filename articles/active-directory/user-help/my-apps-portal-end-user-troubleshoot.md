---
title: Uzyskaj pomoc dotyczącą portalu Moje aplikacje — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Uzyskaj pomoc dotyczącą logowanie się i wykonywanie typowych zadań w portalu Moje aplikacje.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60475031"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Rozwiązywanie problemów z portalu Moje aplikacje
Jeśli masz problemy z logowaniem do lub korzystanie z **Moje aplikacje** portalu, wypróbuj te wskazówki dotyczące rozwiązywania problemów, zanim można skontaktuj się z pomocą techniczną lub administratorem, aby uzyskać pomoc.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mam problemy z zainstalowaniem Moje zabezpieczenia aplikacji logowania rozszerzenia
Jeśli występują problemy z instalacją Moje zabezpieczenia aplikacji logowania rozszerzenia:

- Upewnij się, że używasz obsługiwanej przeglądarki, w tym:

    - **Microsoft Edge.** Uruchomiony w systemie Windows 10 Anniversary Edition lub nowszym.
    - **Google Chrome.** Systemem Windows 7 lub nowszy i w systemie Mac OS X lub później.
    - **Mozilla Firefox 26.0 lub nowszej.** Uruchomione na Windows XP z dodatkiem SP2 lub nowszym i Mac OS X 10.6 lub później.
    - **Internet Explorer 11.** W systemie Windows 7 lub nowszy (ograniczona obsługa).

- Upewnij się, że ustawienia rozszerzenia przeglądarki są włączone.

- Spróbuj ponownie uruchomić przeglądarkę i zaloguj się do **Moje aplikacje** portal ponownie.

- Spróbuj wyczyszczenie plików cookie w przeglądarce, a następnie uruchom ponownie i zaloguj się do **Moje aplikacje** portal ponownie.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nie można zalogować się do **Moje aplikacje** portalu
Jeśli występują problemy z logowaniem do **Moje aplikacje** portalu, możesz spróbować następujące czynności:

- Upewnij się, że używasz właściwego adresu URL. Należy go https://myapps.microsoft.com lub dostosowanej strony dla całej organizacji, takich jak https://myapps.microsoft.com/contoso.com.

- Upewnij się, hasła jest prawidłowa i nie wygasło. Aby uzyskać więcej informacji, zobacz [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Upewnij się, że Twoje informacje weryfikacji jest aktualne i dokładne. Aby uzyskać więcej informacji, zobacz [co usługi Azure Multi-Factor Authentication oznacza dla mnie?](multi-factor-authentication-end-user.md) lub [zmiana Twoje informacje i metody informacji zabezpieczeń](security-info-add-update-methods-overview.md).

- Dodaj **Moja aplikacja** portal adres URL do **właściwości internetowe > Zabezpieczenia > Zaufane witryny** ustawienie.

- Wyczyść pamięć podręczną przeglądarki i spróbuj zalogować się ponownie.

## <a name="my-password-isnt-working"></a>Hasła nie działa
Jeśli nie pamiętasz hasła nigdy nie otrzyma jeden z Twojej organizacji, z zablokowanym dostępem do konta lub chcesz zmienić hasło, zobacz [pamiętam mojego hasła usługi Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Chcę można było zresetować własnego hasła
Można zresetować własnego hasła, administrator musi włączyć funkcję dla Twojej organizacji, a następnie należy zaktualizować i sprawdź, metody wymaganej weryfikacji. Aby uzyskać więcej informacji o sposobie aktualizowania metody weryfikacji, zobacz [rejestrowanie na potrzeby samoobsługowego resetowania haseł](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Otrzymuję komunikat Odmowa dostępu podczas uruchamiania aplikacji
Jeśli otrzymujesz **odmowa dostępu** komunikat po uruchomieniu aplikacji na podstawie **Moja aplikacja** portalu, możesz spróbować następujące czynności:

- Upewnij się, że zainstalowano [Moje zabezpieczenia aplikacji logowania rozszerzenia](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) i że używasz [obsługiwanej przeglądarki](my-apps-portal-end-user-access.md#supported-browsers).

- Upewnij się, że używasz właściwego adresu URL dla aplikacji, a adres URL znajduje się na swoje **właściwości internetowe > zabezpieczeń > Zaufane witryny** listy.

- Upewnij się, hasła jest prawidłowa i nie wygasło. Aby uzyskać więcej informacji, zobacz [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Upewnij się, że Twoje informacje weryfikacji jest aktualne i dokładne. Aby uzyskać więcej informacji, zobacz [co usługi Azure Multi-Factor Authentication oznacza dla mnie?](multi-factor-authentication-end-user.md) lub [zmiana Twoje informacje i metody informacji zabezpieczeń](security-info-add-update-methods-overview.md).

- Wyczyść pamięć podręczną przeglądarki i spróbuj zalogować się ponownie.

Jeśli po wykonaniu tych czynności nadal nie masz dostępu do aplikacji, możesz skontaktować się Twojej organizacji pomocy technicznej Aby uzyskać pomoc.

## <a name="next-steps"></a>Kolejne kroki
Po zalogowaniu się do **Moje aplikacje** portalu, możesz także zaktualizować swój profil i informacje o koncie, informacje o grupie i dostępu, przejrzyj informacje (Jeśli masz uprawnienia).

- [Dostęp i korzystać z aplikacji w portalu Moje aplikacje](my-apps-portal-end-user-access.md).

- [Zmiana informacji o Twoim profilu](my-apps-portal-end-user-update-profile.md).

- [Wyświetl i zaktualizuj swoje informacje związane z grupami](my-apps-portal-end-user-groups.md).

- [Wykonywać własne przeglądów dostępu](my-apps-portal-end-user-access-reviews.md).