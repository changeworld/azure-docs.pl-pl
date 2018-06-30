---
title: Rozwiązywanie problemów z weryfikacji dwuetapowej — usługi Azure AD | Dokumentacja firmy Microsoft
description: Ten dokument dostarcza użytkowników informacji o tym, co należy zrobić, jeśli są uruchamiane na problem z usługi Azure Multi-Factor Authentication.
services: multi-factor-authentication
keywords: Klient uwierzytelniania wieloskładnikowego, problem z uwierzytelnianiem, identyfikator korelacji
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: lizross
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: f778215f4ae962ac6e9eede9952e757dbdda4745
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132981"
---
# <a name="get-help-with-two-step-verification"></a>Uzyskaj pomoc dotyczącą weryfikacji dwuetapowej
Ten artykuł zawiera odpowiedzi na często zadawane pytania, które osoby poproś o weryfikacji dwuetapowej.

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Dlaczego należy przeprowadzić weryfikację dwuetapową? Można go wyłączyć?

Weryfikacja dwuetapowa to funkcja zabezpieczeń, który organizacja wybrany do użycia w celu ochrony Twojego konta. Jest bezpieczniejsza niż tylko hasła, ponieważ opiera się na dwóch metod uwierzytelniania: coś znasz i coś masz Tobie. Coś, co wiesz jest Twoje hasło. Element, który ma Tobie jest telefonu lub urządzenia, czy masz często Tobie. Jeśli Twoje konto jest chronione przy użyciu weryfikacji dwuetapowej, oznacza to, czy złośliwym hakerom nie może zalogować się jako użytkownik Jeśli otrzymują hasła jakieś operacji, ponieważ nie ma dostępu do Twojego telefonu zbyt.

Firma Microsoft oferuje weryfikacji dwuetapowej, ale organizacji zdecydował się użyć funkcji. Nie można wycofać się, jeśli działem pomocy technicznej firmy wymaga od Ciebie, podobnie jak nie mogą zrezygnować z używania hasła, aby chronić swoje konto.

Jeśli masz weryfikacji dwuetapowej włączona dla swojego osobistego konta Microsoft i chcesz zmienić ustawienia, przeczytaj [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) zamiast tego.

## <a name="i-dont-have-my-phone-with-me-today"></a>Nie mam Mój telefon przy użyciu elementu me dzisiaj

Niektóre dni, które pozostanie telefonu w domu, ale nadal trzeba zalogować w miejscu pracy. W pierwszej kolejności należy spróbować loguje się przy użyciu innej metody weryfikacji. Podczas rejestracji na potrzeby weryfikacji dwuetapowej, czy można skonfigurować więcej niż jeden numer telefonu? Aby wypróbować, logowanie się przy użyciu innej metody, wykonaj następujące kroki:

1. Zaloguj się w zwykły sposób.
2. Gdy zostanie otwarta strona weryfikacji dwuetapowej, wybierz pozycję **użyć innej opcji weryfikacji**.

   ![Różne weryfikacji](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Wybierz opcję weryfikacji, który ma być używany.
4. Kontynuuj weryfikacji dwuetapowej.

Jeśli nie widzisz **użyć innej opcji weryfikacji** łącza, a następnie oznacza to, że nie został skonfigurowany alternatywne metody, gdy po raz pierwszy zarejestrowane na potrzeby weryfikacji dwuetapowej. Skontaktuj się z pomocą techniczną Twojej firmy w taki sposób, aby pomocy przy logowaniu do Twojego konta. Gdy użytkownik jest zalogowany, upewnij się, że [zarządzać ustawieniami](multi-factor-authentication-end-user-manage-settings.md) dodawania metod dodatkowej weryfikacji dla następnym razem.

Jeśli widzisz **użyć innej opcji weryfikacji** łącza, ale nie ma dostępu do sieci alternatywne metody albo, skontaktuj się z pomocą firmy obsługuje uzyskanie pomocy przy logowaniu do Twojego konta.

## <a name="i-lost-my-phone-or-got-a-new-number"></a>I utraty Mój telefon lub otrzymano nowy numer
Istnieją dwa sposoby Ci je odzyskać do Twojego konta. Pierwszy jest zalogowanie się przy użyciu uwierzytelniania alternatywny numer telefonu, jeśli zdefiniowano jeden. Drugim jest skontaktuj się z techniczną firmy, aby wyczyścić ustawienia.

Jeśli Twój telefon zostało zgubione lub skradzione, zalecamy również Poinformuj firmę, że pomocy technicznej w celu resetowania haseł aplikacji i wyczyść wszystkie zapamiętanych urządzeniach.

### <a name="use-an-alternate-phone-number"></a>Użyj alternatywnego numeru telefonu
Jeśli skonfigurowano wiele opcji weryfikacji, w tym numer telefonu dodatkowej lub aplikacji uwierzytelniania na innym urządzeniu, można użyć jednego z nich do logowania.

Aby zalogować się przy użyciu alternatywnego numeru telefonu, wykonaj następujące kroki:

1. Zaloguj się w zwykły sposób.
2. Gdy monit o dodatkową weryfikację konta, wybierz **użyć innej opcji weryfikacji**.

   ![Różne weryfikacji](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Wybierz numer telefonu lub urządzenia, czy masz dostęp do.
4. Po Cię wstecz na Twoim koncie [zarządzać ustawieniami](multi-factor-authentication-end-user-manage-settings.md) Aby zmienić numer telefonu uwierzytelniania.

### <a name="clear-your-settings"></a>Wyczyść ustawienia
Jeśli nie skonfigurowano numeru telefonu uwierzytelniania dodatkowego, należy skontaktować się z działem pomocy technicznej firmy, aby uzyskać pomoc. Niech Wyczyść ustawienia, aby przy kolejnym uruchomieniu Zaloguj, użytkownik otrzyma monit [zarejestrować na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) ponownie.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>I nie otrzymuję żadnych tekstu lub zadzwoń na Mój telefon
Istnieje kilka przyczyn, dlaczego może próbować zalogować, ale nie wyświetlany tekst lub połączeń telefonicznych. Jeśli pomyślnie otrzymał teksty lub połączeń telefonicznych na Twój telefon w przeszłości, następnie jest prawdopodobnie problem z dostawcą telefonu, a nie konta. Upewnij się, że dobrej komórce sygnał, a jeśli chcesz otrzymywać wiadomość SMS upewnij się, że może odbierać wiadomości SMS. Poproś friend wywołać tekst lub jako testu.

Jeśli odczekano w ciągu kilku minut SMS lub połączenie, spróbuj użyć innej opcji jest najszybszym sposobem dostęp do konta.

1. Wybierz **użyć innej opcji weryfikacji** na stronie, która oczekuje na weryfikację.

    ![Różne weryfikacji](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Wybierz metodę phone numer lub dostarczania, którego chcesz użyć.

    Jeśli masz wiele kodów weryfikacji, użyj najnowszych z nich.

Jeśli nie masz innej metody skonfigurowane, skontaktuj się z działem pomocy technicznej firmy i poproś o wyczyścić ustawienia. Przy następnym zalogowaniu, użytkownik otrzyma monit [skonfigurować uwierzytelnianie wieloskładnikowe](multi-factor-authentication-end-user-first-time.md) ponownie.

Jeśli masz często opóźnienia z powodu nieprawidłowych komórki sygnału, zalecane jest użycie [aplikacji Microsoft Authenticator](microsoft-authenticator-app-how-to.md) na smartfonie. Aplikacji mogą generować kody zabezpieczeń losowych, których używasz do logowania i kody te nie wymagają żadnych połączenia sygnał lub internet komórki.

## <a name="app-passwords-are-not-working"></a>Hasła aplikacji nie działają.
Najpierw upewnij się, że poprawnie wprowadzono hasła aplikacji. Hasła aplikacji wygenerowane zastępuje normalne hasła, ale tylko w przypadku starszych aplikacji klasycznych, które nie obsługują weryfikacji dwuetapowej. Jeśli nadal nie działa, spróbuj logowanie się i [utworzyć nowe hasło aplikacji](multi-factor-authentication-end-user-app-passwords.md).  Jeśli nadal nie działa, skontaktuj się z działem pomocy technicznej firmy oraz je [usunąć istniejące hasła aplikacji](../../howto-mfa-userdevicesettings.md) , a następnie można utworzyć nowy.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nie można znaleźć odpowiedzi na problem.
Jeśli podjęto następujące kroki rozwiązywania problemów, ale są nadal uruchomione w problemów, skontaktuj się z działem pomocy technicznej firmy. Powinien być może pomóc.

## <a name="related-topics"></a>Powiązane tematy
* [Zarządzanie ustawieniami na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)  
* [Aplikacja Microsoft Authenticator — często zadawane pytania](microsoft-authenticator-app-faq.md)
