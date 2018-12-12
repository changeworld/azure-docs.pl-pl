---
title: Uzyskaj pomoc dotyczącą dostęp i w usłudze Azure Active Directory za pomocą portalu MyApps | Dokumentacja firmy Microsoft
description: Pomoc dotycząca logowania się w usłudze i wykonywanie typowych zadań w panelu dostępu.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: japere
ms.openlocfilehash: b4229ca7ff96a9806ac82cf9452de496d858f6b4
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705688"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-myapps-portal"></a>Rozwiązywanie problemów z usługą dostępu do i korzystania z portalu MyApps

Jeśli masz problemy z logowaniem do lub korzystanie z portalu MyApps, wypróbuj te wskazówki dotyczące rozwiązywania problemów, zanim można skontaktuj się z pomocą techniczną lub administratorem, aby uzyskać pomoc.

## <a name="i-am-having-trouble-signing-into-the-myapps-portal"></a>Występują problemy podczas logowania się do portalu MyApps

Korzystając z poniższych ogólnych porad:

- Pierwszy, sprawdzanie poprawności, aby zobaczyć, czy używasz prawidłowego adresu URL [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Spróbuj dodać adres URL do zaufanych witryn w przeglądarce.
- Upewnij się, że hasło jest poprawna i czy nie wygasł. Aby uzyskać więcej informacji, zobacz [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).
- Sprawdź, upewnij się, że Twoje informacje kontaktowe uwierzytelniania jest aktualny i nie blokuje dostęp do usługi. Aby uzyskać więcej informacji, zobacz [co usługi Azure Multi-Factor Authentication oznacza dla mnie?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Usuń pliki cookie w przeglądarce, a następnie spróbuj się ponownie zalogować.

Jeśli nadal występują problemy podczas próby zarejestrowania się, skontaktuj się z administratorem.

## <a name="i-seem-to-be-having-password-issues"></a>Mogę mieć problemy hasła

Jeśli nie pamiętasz hasła nigdy nie otrzyma jeden z działu IT, zablokowanym dostępem do konta lub chcesz zmienić hasło, zobacz [pamiętam mojego hasła usługi Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Należy zarejestrować w celu zresetowania hasła

Można zresetować hasło lub odblokować konto bez konieczności Porozmawiaj z kimś przy użyciu samoobsługowego resetowania haseł (SSPR). Przed użyciem tej funkcji, musisz Zarejestruj swoje metody uwierzytelniania lub potwierdzić wstępnie zdefiniowane metody uwierzytelniania, które z wymaganiami Twojego administratora. Aby uzyskać więcej informacji, zobacz [rejestrowanie na potrzeby samoobsługowego resetowania haseł](active-directory-passwords-reset-register.md).

## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mam problemy z zainstalowaniem Moje zabezpieczenia aplikacji logowania rozszerzenia

Portalu MyApps wymaga przeglądarki, która obsługuje język JavaScript i CSS została włączona. W przypadku korzystania z opartego na hasłach pojedynczego logowania aplikacji towarzyszący rozszerzenia musi być zainstalowany także. To rozszerzenie jest automatycznie pobierany po uruchomieniu aplikacji, która jest skonfigurowana dla opartego na hasłach pojedynczego logowania aplikacji.

Zaznacz, aby upewnić się, że spełniasz następujące wymagania dotyczące przeglądarki:

- **Krawędź**: Windows 10 Anniversary Edition lub nowszy.
- **Dla programu Chrome**: Windows 7 lub nowszy i w systemie Mac OS X lub później.
- **Firefox 26.0 lub nowsze**: na Windows XP z dodatkiem SP2 lub nowszym i Mac OS X 10.6 lub później.
- **Internet Explorer 11**: Windows 7 lub nowszy (ograniczona obsługa).

Możesz również pobrać rozszerzenia bezpośrednio z następujących witryn:

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Jeśli zainstalowano rozszerzenie i nadal występują problemy, spróbuj wykonać następujące czynności:

- Sprawdź ustawienia rozszerzenia przeglądarki, aby upewnić się, że rozszerzenie jest włączona.
- Uruchom ponownie przeglądarkę i zaloguj się do portalu MyApps.
- Wyczyść pliki cookie w przeglądarce i zaloguj się do portalu MyApps.
- Aby uzyskać dostęp do narzędzia do diagnostyki i instrukcje krok po kroku dotyczące konfigurowania rozszerzenia dla programu Internet Explorer, zobacz [Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Użyj mojej aplikacji bezpiecznego logowania rozszerzenia
* Jeśli używasz Mój adres URL aplikacji innych niż `https://myapps.microsoft.com`, skonfigurować domyślny adres URL, wykonując następujące czynności:
   1. Gdy jesteś *nie* logowanie się do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
   2. W menu, wybierz **adres URL aplikacji Mój**.
   3. Wybierz domyślny adres URL.
   4. Wybierz ikonę rozszerzenia.
   5. Aby zalogować się do rozszerzenia, wybierz **Zaloguj się rozpocząć**.

* Aby zalogować się bezpośrednio do aplikacji w przeglądarce, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia należy zalogować się w niej, wybierając **Zaloguj się rozpocząć**.
   2. Zaloguj się do aplikacji za pomocą adresu URL logowania jednokrotnego.  
       Adres URL logowania jest zwykle adres URL aplikacji, który wyświetla formularz logowania.
      Rozszerzenie powinno zmiany stanu i powiadomienie Cię o tym, że hasło jest dostępny.
   3. Aby się zarejestrować, wybierz ikonę rozszerzenia.

* Aby uruchomić aplikację na podstawie rozszerzenia, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia należy zalogować się w niej, wybierając **Zaloguj się rozpocząć**.
   2. Wybierz ikonę rozszerzenia, aby otworzyć jego menu.
   3. Wyszukaj aplikację, która jest dostępna w portalu MyApps.
   4. Na liście wyników wyszukiwania wybierz aplikację.  
       Ostatnie trzy aplikacje, które wykorzystano są wyświetlane na **ostatnio używane** listy skrótów.

> [!NOTE]
> Te opcje są dostępne tylko dla przeglądarki Microsoft Edge, Chrome i Firefox.

## <a name="how-do-i-add-a-new-app"></a>Jak dodać nową aplikację?

1.  Na **aplikacje** wybierz opcję **Dodaj aplikację**.
2.  Wyszukaj aplikację, którą chcesz dodać, a następnie wybierz **Dodaj**.

   > [!NOTE]
   > * Tej opcji są dostępne tylko wtedy, gdy administrator włączył go dla swojego konta.
   > * Jeśli aplikacja wymaga uprawnień, może być konieczne czekać na zatwierdzenie przez administratora.

## <a name="how-do-i-manage-my-group-memberships"></a>Jak zarządzać Moje członkowstwa w grupach?

Wybierz **grup** kafelka, a następnie wykonaj jedną z następujących czynności:
* Aby utworzyć grupę, w obszarze **grup własnym**, wybierz opcję **Utwórz grupę**, a następnie postępuj zgodnie z instrukcjami.
* Dołączenia do grupy, w obszarze **grup jestem w**, wybierz opcję **dołączanie do grupy**, a następnie postępuj zgodnie z instrukcjami.

   > [!NOTE]
   > * Tej opcji są dostępne tylko wtedy, gdy administrator włączył go dla swojego konta.
   > * Jeśli jesteś członkiem grupy, można wyświetlić szczegóły i pozostawić grupę.
   > * Jeśli jesteś właścicielem grupy, można wyświetlić szczegóły, dodać lub usunąć członków i pozostawić grupę.