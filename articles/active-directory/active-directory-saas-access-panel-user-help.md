---
title: Uzyskaj pomoc dotyczącą uzyskiwanie dostępu i korzystania z portalu Moje aplikacje w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Uzyskaj pomoc dotyczącą logowanie do i wykonywanie typowych zadań w panelu dostępu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 681831dcc65fa74d8d2e26f58849140498843c49
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Rozwiązywanie problemów z uzyskiwanie dostępu i korzystania z portalu Moje aplikacje

Jeśli występują problemy dotyczące logowania się lub przy użyciu portalu Moje aplikacje, spróbuj te wskazówki dotyczące rozwiązywania problemów, zanim zostanie skontaktuj się z pomocą techniczną lub administratorem Aby uzyskać pomoc.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Mam problemy z logowaniem do portalu Moje aplikacje

Wypróbuj te ogólne wskazówki:

- Po pierwsze, wyboru, aby zobaczyć, czy używasz poprawny adres URL [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Spróbuj dodać do zaufanych witryn w przeglądarce adres URL.
- Upewnij się, że hasło jest poprawna i czy nie wygasł. Aby uzyskać więcej informacji, zobacz [zresetować hasło lub szkoły](active-directory-passwords-update-your-own-password.md).
- Sprawdź, czy informacje kontaktowe dotyczące uwierzytelniania jest aktualny i nie blokują dostęp. Aby uzyskać więcej informacji, zobacz [co oznacza Azure Multi-Factor Authentication dla mnie?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Wyczyść pliki cookie w przeglądarce, a następnie spróbuj się ponownie zalogować.

Jeśli nadal wystąpią problemy podczas próby zarejestrowania się, skontaktuj się z administratorem.


## <a name="i-seem-to-be-having-password-issues"></a>Mogę się problemy hasła

Jeśli nie pamiętasz hasła nigdy nie otrzymanych z działu IT, są zablokowane konta lub chcesz zmienić swoje hasło, zobacz [pomocy, pamiętam hasła usługi Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Należy zarejestrować do resetowania hasła

Możesz zresetować hasło lub odblokować konto bez konieczności Porozmawiaj z kimś przy użyciu samodzielnego resetowania hasła (SSPR). Przed użyciem tej funkcji, musisz zarejestrować metody uwierzytelniania lub potwierdź metody uwierzytelniania wstępnie zdefiniowane, które wymaga od administratora. Aby uzyskać więcej informacji, zobacz [rejestrowanie się w celu samodzielnego resetowania hasła](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Problemy dotyczące instalowania Moje aplikacje bezpiecznego logowania rozszerzenia

Portal Moje aplikacje wymaga przeglądarki obsługującej JavaScript i włączył CSS. Jeśli używasz hasła aplikacji opartych na jednym logowania jednokrotnego towarzyszący rozszerzenia musi być zainstalowany także. To rozszerzenie jest pobierany automatycznie podczas uruchamiania aplikacji, która jest skonfigurowana dla hasła aplikacji opartych na jednym logowania jednokrotnego.

Sprawdź, upewnij się, że spełniają następujące wymagania dotyczące przeglądarki:
- **Krawędź**: w systemie Windows 10 Anniversary Edition lub nowszy.
- **Chrome**: w systemie Windows 7 lub nowszym i w systemie Mac OS X lub później.
- **Firefox 26.0 lub nowsze**: w systemie Windows XP SP2 lub nowszy i Mac OS X 10.6 lub później.
- **Internet Explorer 11**: w systemie Windows 7 lub nowszy (obsługa ograniczona).

Możesz również pobrać rozszerzenia dla programu Chrome i krawędzi bezpośrednio z następujących witryn:

- [Rozszerzenia programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozszerzenie krawędzi](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Jeśli zainstalowano rozszerzenia i nadal występują problemy, wypróbuj następujące metody:

- Sprawdź ustawienia rozszerzenia przeglądarki, aby upewnić się, że rozszerzenie jest włączona.
- Uruchom ponownie przeglądarkę, a następnie zaloguj się do portalu Moje aplikacje.
- Wyczyszczenie plików cookie w przeglądarce i zaloguj się do portalu Moje aplikacje.
- Aby uzyskać dostęp do narzędzia diagnostycznego i instrukcje krok po kroku dotyczące konfigurowania rozszerzenia dla programu Internet Explorer, zobacz [Rozwiązywanie problemów z rozszerzeniem Panel dostępu dla programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Użyj mojej aplikacji bezpiecznego logowania rozszerzenia
* Jeśli używasz Mój adres URL aplikacji innych niż `https://myapps.microsoft.com`, skonfiguruj adres URL domyślnej przez wykonanie następujących czynności:
   1. Podczas *nie* zalogowany do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
   2. W menu, wybierz **adres URL aplikacji Mój**.
   3. Wybierz adres URL domyślnej.
   4. Wybierz ikonę rozszerzenia.
   5. Aby zalogować się do rozszerzenia, wybierz **Zaloguj się rozpocząć**.

* Aby zalogować się bezpośrednio do aplikacji z przeglądarki, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia, zaloguj się do niego, wybierając **Zaloguj się rozpocząć**.
   2. Zaloguj się do aplikacji z adresem URL logowania jednokrotnego.  
       Adres URL logowania jest zwykle adres URL aplikacji, która wyświetla formularz logowania.
      Rozszerzenia należy zmienić stan i informacją o tym, że hasło jest dostępny.
   3. Aby się zarejestrować, wybierz ikonę rozszerzenia.

* Aby uruchomić aplikację z rozszerzenia, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia, zaloguj się do niego, wybierając **Zaloguj się rozpocząć**.
   2. Wybierz ikonę rozszerzenia, aby otworzyć menu.
   3. Wyszukaj aplikację, która jest dostępna w portalu Moje aplikacje.
   4. Na liście wyników wyszukiwania wybierz aplikację.  
       Ostatnie trzy aplikacje używane są wyświetlane w **ostatnio używane** listy skrótów.

> [!NOTE]
> Te opcje są dostępne tylko w przypadku krawędzi, Chrome i Firefox.

## <a name="how-do-i-add-a-new-app"></a>Jak dodać nową aplikację?

1.  Na **aplikacje** wybierz pozycję **Dodaj aplikację**.
2.  Wyszukaj aplikację, którą chcesz dodać, a następnie wybierz **Dodaj**.

   > [!NOTE]
   > * Ta opcja jest dostępne tylko wtedy, gdy administrator włączył go dla Twojego konta.
   > * Jeśli aplikacja wymaga uprawnień, konieczne może czekać na zatwierdzenie przez administratora.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>Jak zarządzać członkostwa w grupach?

Wybierz **grup** Kafelek, a następnie wykonaj jedną z następujących czynności: 
* Aby utworzyć grupę, w obszarze **grup własnym**, wybierz pozycję **Utwórz grupę**, a następnie postępuj zgodnie z instrukcjami.
* Do dołączenia do grupy, w obszarze **grup w**, wybierz pozycję **dołączanie do grupy**, a następnie postępuj zgodnie z instrukcjami.

   > [!NOTE]
   > * Ta opcja jest dostępne tylko wtedy, gdy administrator włączył go dla Twojego konta.
   > * Jeśli jesteś członkiem grupy, możesz wyświetlić szczegóły i pozostawić grupę.
   > * Jeśli jesteś właścicielem grupy, można wyświetlić szczegóły, dodać lub usunąć członków i pozostawić grupę.
   >


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o rozwiązywaniu problemów, zobacz [problemów za pomocą aplikacji dostęp do panelu witryny sieci Web lub aplikacji mobilnej](active-directory-application-access-panel-content-map.md).

