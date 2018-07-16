---
title: Rozwiązywanie problemów z weryfikacji dwuetapowej — usługa Azure AD | Dokumentacja firmy Microsoft
description: W tym dokumencie będzie zapewniać użytkownikom informacje o tym, co należy zrobić, jeśli działają wystąpił problem z usługą Azure Multi-Factor Authentication.
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
ms.openlocfilehash: deb75c2601fa55f7cdb1681d8f73e94d6b01310a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060075"
---
# <a name="get-help-with-two-step-verification"></a>Uzyskaj pomoc dotyczącą weryfikacji dwuetapowej
Ten artykuł zawiera odpowiedzi na często zadawane pytania, które osoby poprosić o weryfikacji dwuetapowej.

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Dlaczego trzeba wykonywać weryfikację dwuetapową? Można go wyłączyć?

Weryfikacja dwuetapowa była to funkcja zabezpieczeń, który Twoja organizacja wybrany do użycia w celu ochrony Twojego konta. Jest bezpieczniejszy niż tylko hasła, ponieważ opiera się na dwóch metod uwierzytelniania: coś, co wiesz i coś, co masz Tobie. Coś, co wiesz jest hasło. Jest coś, co masz Tobie, telefonu lub urządzenia, które często mają Tobie. Jeśli Twoje konto jest chroniony za pomocą weryfikacji dwuetapowej, oznacza to, czy złośliwym hakerom nie może zalogować się jako użytkownik Jeśli uzyskują hasła jakiś sposób, ponieważ nie miał dostępu na Twój telefon, za.

Firma Microsoft oferuje weryfikacji dwuetapowej, ale Twoja organizacja zdecydował się użyć funkcji. Nie można wycofać się, jeśli dział pomocy technicznej Twojej firmy wymaga elementu, tak samo, jak nie możesz zrezygnować z przy użyciu hasła, aby chronić swoje konto.

Jeśli masz weryfikację dwuetapową dla Twojego osobistego konta Microsoft i chcesz zmienić ustawienia, przeczytaj [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) zamiast tego.

## <a name="i-dont-have-my-phone-with-me-today"></a>Mój telefon nie występuje już dziś ze mną

Kilku dni, które pozostaną Telefon w domu, ale nadal musisz się zarejestrować w miejscu pracy. Pierwszą rzeczą, jaką należy dążyć jest logowanie przy użyciu innej metody weryfikacji. Po zarejestrowaniu weryfikacji dwuetapowej, czy konfigurowania więcej niż jeden numer telefonu? Aby wypróbować, logowanie się przy użyciu innej metody, wykonaj następujące kroki:

1. Zaloguj się w zwykły sposób.
2. Gdy zostanie otwarta strona weryfikacji dwuetapowej, należy wybrać **użyć innej opcji weryfikacji**.

   ![Różne weryfikacji](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Wybierz opcję weryfikacji, który chcesz użyć.
4. Przejdź do weryfikacji dwuetapowej.

Jeśli nie widzisz **użyć innej opcji weryfikacji** połączyć, a następnie oznacza to, że nie zostały skonfigurowane alternatywne metody, gdy po raz pierwszy zarejestrowane do weryfikacji dwuetapowej. Skontaktuj się z działem pomocy technicznej Twojej firmy, aby uzyskać pomoc, logowania się do swojego konta. Gdy użytkownik jest zalogowany, upewnij się, że [zarządzać ustawieniami](multi-factor-authentication-end-user-manage-settings.md) dodawania metod dodatkowa weryfikacja użytku następnym razem.

Jeśli widzisz **użyć innej opcji weryfikacji** łącza, ale nie ma dostępu do swoich alternatywnych metod albo, skontaktuj się z pomocą techniczną Twojej firmy, aby uzyskać pomoc, logowania się do swojego konta.

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Czy mogę utraty Mój telefon lub masz nowy numer
Istnieją dwa sposoby do niej wrócić do swojego konta. Pierwsza to logować się za pomocą usługi alternatywny numer telefonu uwierzytelniania, jeśli skonfigurowano jedną. Drugi cel to poproś pomocy technicznej Twojej firmy, aby wyczyścić ustawienia.

Jeśli Twój telefon, zostało zgubione lub skradzione, zalecamy również poinformować Twojej firmy, że pomocy technicznej, dzięki czemu mogą zresetować swoje hasła aplikacji i wyczyść wszystkie zapamiętanych urządzeniach.

### <a name="use-an-alternate-phone-number"></a>Użyj alternatywnego numeru telefonu
Jeśli skonfigurowano wiele opcji weryfikacji, w tym dodatkowy numer telefonu lub inną aplikację na innym urządzeniu, można użyć jednego z nich do logowania.

Aby zalogować się przy użyciu alternatywny numer telefonu, wykonaj następujące kroki:

1. Zaloguj się w zwykły sposób.
2. Po wyświetleniu monitu o dodatkową weryfikację konta, wybierz **użyć innej opcji weryfikacji**.

   ![Różne weryfikacji](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Wybierz numer telefonu lub urządzenia, które mają dostęp do.
4. Po one ponownie w ramach swojego konta [zarządzać ustawieniami](multi-factor-authentication-end-user-manage-settings.md) Aby zmienić numer telefonu uwierzytelniania.

### <a name="clear-your-settings"></a>Wyczyść ustawienia
Jeśli nie skonfigurowano numeru telefonu uwierzytelniania pomocniczego, masz skontaktuj się z działem pomocy technicznej Twojej firmy w celu uzyskania pomocy. Poproś Wyczyść ustawienia, dzięki czemu przy kolejnym uruchomieniu Zaloguj, zostanie wyświetlony monit do [zarejestrować na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) ponownie.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Czy mogę mnie, nie odbiera tekstu, lub zadzwoń na Mój telefon
Istnieje kilka powodów dlaczego może próbować zalogować, ale nie otrzymywać wiadomość SMS lub połączenie telefonu. Jeśli został pomyślnie odebrany teksty lub połączeń telefonicznych na Twój telefon w przeszłości, następnie jest prawdopodobnie problem z dostawcą telefonu, a nie konta. Upewnij się, że masz sygnał dobrych komórki, a jeśli chcesz otrzymywać wiadomość SMS, upewnij się, że jesteś w stanie odbierać wiadomości SMS. Poproś znajomego do wywołania, użytkownik lub tekst jako test.

Jeśli już oczekiwany w ciągu kilku minut wiadomość SMS lub połączenie, najszybszy sposób dostępu do konta ma inną opcję.

1. Wybierz **użyć innej opcji weryfikacji** na stronie, która oczekuje na weryfikację.

    ![Różne weryfikacji](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Wybierz metodę telefonu numer lub dostarczania, którego chcesz użyć.

    Odebranie wiele kodów weryfikacyjnych, użyj najnowszej.

Jeśli nie masz innej metody skonfigurowane, skontaktuj się z działem pomocy technicznej Twojej firmy i poproś go o wyczyścić ustawienia. Podczas następnego logowania zostanie wyświetlony monit do [skonfigurować uwierzytelnianie wieloskładnikowe](multi-factor-authentication-end-user-first-time.md) ponownie.

Jeśli często opóźnienia z powodu sygnału zły komórki, zalecamy użycie [aplikacji Microsoft Authenticator](microsoft-authenticator-app-how-to.md) na smartfonie. Aplikacja może generować kody zabezpieczeń losowych, których używasz do logowania w i kody te nie wymagają dowolnego połączenia internetowego lub sygnału komórki.

## <a name="app-passwords-are-not-working"></a>Hasła aplikacji nie działają.
Najpierw upewnij się, że poprawnie wprowadzono hasła aplikacji. Hasła aplikacji wygenerowane zastępuje normalne hasła, ale tylko dla starsze aplikacje klasyczne, które nie obsługują weryfikacji dwuetapowej. Jeśli nadal nie działa, spróbuj logowanie się i [Utwórz nowe hasło aplikacji](multi-factor-authentication-end-user-app-passwords.md).  Jeśli to nie zadziała, skontaktuj się z działem pomocy technicznej Twojej firmy, a ich [usunąć istniejące hasła aplikacji](../authentication/howto-mfa-userdevicesettings.md) i następnie utworzyć nowe konto.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nie mogę znaleźć odpowiedzi na problem.
Jeśli próbowałeś te kroki rozwiązywania problemów, ale nadal są uruchomione w problemy, skontaktuj się z działem pomocy technicznej Twojej firmy. Należy je uzyskać pomoc.

## <a name="related-topics"></a>Powiązane tematy
* [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)  
* [Często zadawane pytania na temat aplikacji Microsoft Authenticator](microsoft-authenticator-app-faq.md)
