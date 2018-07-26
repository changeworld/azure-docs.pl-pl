---
title: Usługa Azure Active Directory haseł szczegółowe dane
description: Jak samoobsługowego resetowania haseł pracy
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: efc62243370ff2cc5214a4ae235139bdb5965486
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248223"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to działa: Usługa Azure AD samoobsługowego resetowania haseł

Jak samoobsługowego resetowania haseł (SSPR) pracy? Co oznacza tej opcji w interfejsie? Kontynuuj czytanie, aby dowiedzieć się więcej na temat samoobsługowego resetowania HASEŁ usługi Azure Active Directory (Azure AD).

|     |
| --- |
| Powiadomienia aplikacji mobilnej i kodu aplikacji mobilnej jako metody dla hasła usługi Azure AD z samoobsługowego resetowania są w publicznej wersji zapoznawczej funkcji usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>Jak działa portal resetowania haseł?

Gdy użytkownik przechodzi do portalu resetowania haseł, przepływ pracy rozpocznie się kompilowanie, aby określić:

   * Jak powinien być zlokalizowany strony?
   * Konto użytkownika jest prawidłowy?
   * Jakie organizacji czy użytkownik należy do?
   * Gdy odbywa się hasło użytkownika
   * Użytkownik jest licencji na korzystanie z funkcji?

Strona resetowania odczytu przez następujące kroki, aby dowiedzieć się więcej na temat logiki stojącej za hasło:

1. Użytkownik wybierze **nie może uzyskać dostępu do konta** połączenia lub przechodzi bezpośrednio do [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * Oparte na ustawienia regionalne przeglądarki, proces jest renderowany w odpowiednim języku. Proces resetowania hasła jest zlokalizowana na te same języki, które obsługuje usługi Office 365.
   * Aby wyświetlić resetowania hasła portalu w innej zlokalizowanego języka należy dołączyć "? mkt =" na końcu hasło URL resetowania z przykładu, który następuje po lokalizowanie na język hiszpański [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. Użytkownik wprowadza identyfikator użytkownika i przekazuje captcha.
3. Usługa Azure AD sprawdza, czy użytkownik będzie mógł korzystać z tej funkcji, wykonując następujące testy:
   * Sprawdza, czy użytkownik ma włączenia tej funkcji i usługi Azure AD ma przypisaną licencję.
     * Jeśli użytkownik nie ma włączenia tej funkcji lub nie mieć przypisaną licencję, użytkownik jest proszony o administratora do zresetowania swojego hasła.
   * Sprawdza, czy użytkownik ma metody prawo uwierzytelniania zdefiniowane na swoje konto zgodnie z zasadami administratora.
     * Jeśli zasady są wymagane tylko jedną metodę, następnie gwarantuje, że użytkownik ma odpowiednie dane, które są zdefiniowane dla co najmniej jednej z metod uwierzytelniania, korzystając z zasadami administratora.
       * Jeśli nie skonfigurowano metod uwierzytelniania, użytkownik jest zalecana do kontaktowania się z do zresetowania swojego hasła administratora.
     * Jeśli zasady są wymagane dwie metody, następnie gwarantuje, że użytkownik ma odpowiednie dane, które są zdefiniowane dla co najmniej dwóch metod uwierzytelniania, korzystając z zasadami administratora.
       * Jeśli nie skonfigurowano metod uwierzytelniania, użytkownik jest zalecana do kontaktowania się z do zresetowania swojego hasła administratora.
   * Sprawdza, czy hasło użytkownika jest zarządzane lokalnie (federacyjnych, przekazywanego uwierzytelniania lub skrótu hasła synchronizowane).
     * Jeśli wdrożono zapisywanie zwrotne hasła są zarządzane lokalnie, następnie użytkownik będzie mógł przejść do uwierzytelniania i zresetować swoje hasło.
     * Jeśli nie wdrożono zapisywanie zwrotne hasła są zarządzane lokalnie, użytkownik jest proszony o administratora do zresetowania swojego hasła.
4. Jeśli okaże się, że użytkownik jest w stanie pomyślnie zresetowania hasła, użytkownik jest przeprowadzany przez procedurę resetowania.

## <a name="authentication-methods"></a>Metody uwierzytelniania

Jeśli samoobsługowego resetowania HASŁA jest włączona, należy wybrać co najmniej jeden z następujących opcji dla metod uwierzytelniania. Czasami słyszysz te opcje określane jako "bramy". Zdecydowanie zalecamy możesz **wybierz dwa lub więcej metod uwierzytelniania** tak, aby użytkownicy mają większą elastyczność w przypadku, gdy są one nie można uzyskać dostępu do jednego, kiedy ich potrzebują.

* Powiadomienia w aplikacji mobilnej (wersja zapoznawcza)
* Kod aplikacji mobilnej (wersja zapoznawcza)
* Email
* Telefon komórkowy
* Telefon biurowy
* Pytania zabezpieczające

Użytkownicy mogą zresetować swoje hasło tylko, jeśli mają one dane już obecne we metod uwierzytelniania, która została włączona przez administratora.

![Uwierzytelnianie][Authentication]

### <a name="number-of-authentication-methods-required"></a>Liczba metod uwierzytelniania, wymagany

Ta opcja określa minimalną liczbę dostępne metody uwierzytelniania lub bram, które użytkownik musi przejść do resetowania lub odblokowywania ich haseł. Można ustawić na jeden lub dwa.

Użytkownicy mogą wybrać podać więcej metod uwierzytelniania, jeśli administrator włącza tej metody uwierzytelniania.

Jeśli użytkownik nie ma zarejestrowanych metod wymagane minimalne, zobaczy strony błędu, który kieruje je do żądania, aby administrator zresetować swoje hasło.

#### <a name="mobile-app-and-sspr-preview"></a>Aplikacja mobilna i samoobsługowego resetowania HASEŁ (wersja zapoznawcza)

Przy użyciu aplikacji mobilnej, takie jak aplikacja Microsoft Authenticator jako metodę do resetowania haseł użytkowników należy pamiętać o następujących. Samoobsługowego resetowania haseł podczas tylko jedną z metod jest wymagane do resetowania kod weryfikacyjny jest jedyną opcją, dostępne dla użytkowników. Gdy wymagane są dwie metody użytkownicy będą mogli resetować przy użyciu **albo** powiadomień **lub** kod weryfikacyjny, oprócz innych włączone metody.

| Liczba metod wymaganych do zresetowania | jeden | Dwa |
| :---: | :---: | :---: |
| Dostępne funkcje aplikacji mobilnej | Kod | Kod i powiadomienia |

Użytkownicy nie będą mieli możliwość zarejestrowania aplikacji mobilnej, gdy rejestrowanie na potrzeby samoobsługowego resetowania haseł. Zamiast tego użytkownicy mogą rejestrować w aka.ms/mfasetup lub w wersji zapoznawczej rejestracji informacji zabezpieczeń w aka.ms/setupsecurityinfo aplikacji mobilnej. 

### <a name="change-authentication-methods"></a>Zmiana metody uwierzytelniania

Po uruchomieniu zasady z tylko jedną wymagane metodę uwierzytelniania dla zresetować lub odblokować zarejestrowane i zmienisz, że do dwóch metod, co się dzieje?

| Liczba zarejestrowanych metod | Liczba metod wymaganych | Wynik |
| :---: | :---: | :---: |
| co najmniej 1 | 1 | **Możliwe** możliwość resetowania lub odblokowywania |
| 1 | 2 | **Nie można** możliwość resetowania lub odblokowywania |
| co najmniej 2 | 2 | **Możliwe** możliwość resetowania lub odblokowywania |

Jeśli zmienisz typy metod uwierzytelniania, które użytkownik może używać, może przypadkowo przestać użytkownicy nie mogli użyć funkcji samoobsługowego resetowania HASEŁ, jeśli nie mają minimalną ilość danych dostępnych.

Przykład:
1. Oryginalnych zasad jest skonfigurowany z dwóch metod uwierzytelniania, wymagany. Używa tylko numer telefonu biurowego i pytania zabezpieczające. 
2. Administrator zmiany zasad nie jest już Użyj pytań zabezpieczających, ale umożliwia korzystanie z telefonu komórkowego i alternatywny adres e-mail.
3. Użytkownicy bez telefonu komórkowego i alternatywny adres e-mail pól nie można zresetować swoje hasła.

## <a name="registration"></a>Rejestracja

### <a name="require-users-to-register-when-they-sign-in"></a>Wymagaj od użytkowników rejestrowania się podczas logowania

Włączenie tej opcji wymaga od użytkownika ukończyć rejestracji resetowania hasła, po zalogowaniu się do wszystkich aplikacji korzystających z usługi Azure AD. Obejmuje to następujące aplikacje:

* Office 365
* Azure Portal
* Panel dostępu
* Aplikacje federacyjne
* Aplikacje niestandardowe, za pomocą usługi Azure AD

Gdy wymagają rejestracji jest wyłączone, użytkownicy będą mogli ręcznie zarejestrować. Mogą oni albo odwiedź [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) lub wybierz **rejestrowanie na potrzeby resetowania hasła** łącze w obszarze **profilu** kartę w panelu dostępu.

> [!NOTE]
> Użytkownicy mogą odrzucić portalu rejestracji resetowania haseł, wybierając **anulować** lub przez zamknięcie okna. Jednak użytkownicy są monitowani o rejestru, każdym logowaniu do czasu zakończenia rejestracji.
>
> Nie przerywa połączenie użytkownika, jeśli nastąpiło już zalogowanie w.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ustaw liczbę dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania

Ta opcja określa odstęp czasu między ustawienie i reconfirming informacje dotyczące uwierzytelniania i jest dostępna tylko wtedy, gdy włączysz **wymagać od użytkowników rejestrowania się podczas logowania** opcji.

Prawidłowe wartości to 0 do 730 dni, ciągiem "0", co oznacza, że użytkownicy nigdy nie zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania.

## <a name="notifications"></a>Powiadomienia

### <a name="notify-users-on-password-resets"></a>Czy powiadamiać użytkowników o resetowaniu hasła?

Jeśli ta opcja jest ustawiona na **tak**, a następnie resetowania hasła użytkownikom otrzymywać wiadomość e-mail z powiadomieniem, że ich hasło zostało zmienione. Wiadomości e-mail są wysyłane za pośrednictwem portalu samoobsługowego resetowania HASEŁ w odniesieniu do ich podstawowy i alternatywny adres e-mail, które znajdują się w pliku w usłudze Azure AD. Nikt inny nie jest powiadamiany o zdarzeniach, resetowanego.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasła

Jeśli ta opcja jest ustawiona na **tak**, następnie *wszystkich administratorów* otrzymywać wiadomości e-mail na swój podstawowy adres e-mail w pliku w usłudze Azure AD. Adres e-mail powiadomienia że inny administrator zmieniła swoje hasło za pomocą funkcji samoobsługowego resetowania HASEŁ.

Przykład: Istnieją cztery Administratorzy w środowisku. Administrator, A Resetuje hasła, korzystając z funkcji samoobsługowego resetowania HASEŁ. Administratorzy B, C i D będą otrzymywać wiadomości e-mail powiadamiające ich resetowania hasła.

## <a name="on-premises-integration"></a>Integracja lokalna

Jeśli zainstalować, skonfigurować i włączyć program Azure AD Connect, masz następujące dodatkowe opcje integracji środowiska lokalnego. Jeśli te opcje są wygaszone, następnie zapisywania zwrotnego nie został poprawnie skonfigurowany. Aby uzyskać więcej informacji, zobacz [Konfigurowanie funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md).

![Zapisywanie zwrotne][Writeback]

Ta strona zawiera szybkie stan lokalnego klienta zapisywania zwrotnego, wyświetlony zostanie jeden z następujących komunikatów na podstawie bieżącej konfiguracji:

* Usługi w środowisku lokalnym klientem zapisywania zwrotnego jest uruchomiony.
* Usługa Azure AD jest w trybie online i jest połączona z lokalnego klienta zapisywania zwrotnego. Jednak prawdopodobnie zainstalowana wersja programu Azure AD Connect jest nieaktualna. Należy wziąć pod uwagę [uaktualnienie usługi Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) aby upewnić się, że masz najnowszych funkcji łączności i ważnych poprawek błędów.
* Niestety nie możemy sprawdzić swój status klienta zapisywania zwrotnego w środowisku lokalnym, ponieważ zainstalowana wersja programu Azure AD Connect jest nieaktualna. [Uaktualnij program Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) Aby móc sprawdzić stan połączenia.
* Niestety prawdopodobnie nie można nawiązać połączenia lokalnego klienta zapisywania zwrotnego teraz. [Rozwiązywanie problemów z usługi Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) Aby przywrócić połączenie.
* Niestety, firma Microsoft nie może nawiązać lokalnego klienta zapisywania zwrotnego, ponieważ funkcja zapisywania zwrotnego haseł nie zostało poprawnie skonfigurowane. [Konfigurowanie zapisywania zwrotnego haseł](howto-sspr-writeback.md) Aby przywrócić połączenie.
* Niestety prawdopodobnie nie można nawiązać połączenia lokalnego klienta zapisywania zwrotnego teraz. Może to być spowodowane przez tymczasowe problemy po naszej stronie. Jeśli problem będzie się powtarzać, [Rozwiązywanie problemów z programu Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) Aby przywrócić połączenie.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapisywać zwrotnie hasła do katalogu lokalnego

Ten formant określa, czy funkcja zapisywania zwrotnego haseł jest włączony dla tego katalogu. Jeśli funkcję zapisywania zwrotnego jest włączona, wskazuje stan usługi zapisywania zwrotnego w środowisku lokalnym. Jest to przydatne, jeśli chcesz tymczasowo wyłączyć funkcję zapisywania zwrotnego haseł, bez konieczności ponownie skonfigurować program Azure AD Connect.

* Jeśli przełącznik jest równa **tak**, następnie włączone zapisywanie zwrotne, i federacyjnych, przekazywanego uwierzytelniania lub użytkownicy synchronizację skrótów haseł mogą resetować swoje hasła.
* Jeśli przełącznik jest równa **nie**, zapisywania zwrotnego jest wyłączone i nie będą mogli resetować swoje hasła uwierzytelniania federacyjnego, przekazywania lub użytkownikom synchronizację skrótów haseł.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła

Ten formant wskazuje, czy użytkownicy odwiedzający portal resetowania haseł należy skorzystać z opcji odblokowanie ich kont usługi Active Directory w środowisku lokalnym bez konieczności, zresetować swoje hasło. Domyślnie usługa Azure AD umożliwia odblokowanie konta podczas resetowania hasła. To ustawienie umożliwia oddzielić te dwie operacje. 

* Jeśli ustawiona na **tak**, a następnie użytkownicy otrzymują możliwość zresetowania swojego hasła i odblokowania konta lub odblokowania konta bez resetowania hasła.
* Jeśli ustawiono **nie**, następnie użytkownicy są tylko mogli przeprowadzić resetowanie hasła połączone i operacji odblokowania konta.

## <a name="password-reset-for-b2b-users"></a>Resetowanie haseł dla użytkowników B2B

Resetowanie hasła i zmiany są w pełni obsługiwane na wszystkich konfiguracji z business-to-business (B2B). Resetowanie hasła użytkownika B2B jest obsługiwane w następujących trzech przypadkach:

   * **Użytkownicy z organizacji partnerskiej z istniejącej dzierżawy usługi Azure AD**: Jeśli organizacja nawiązaliśmy partnerstwo z firmą korzysta z istniejącej dzierżawy usługi Azure AD, firma Microsoft *przestrzegać, niezależnie od zasady resetowania hasła są włączone dla tej dzierżawy*. Do resetowania hasła, aby pracować organizacji partnerskiej po prostu musi upewnij się, że włączono usługi Azure AD SSPR. Nie ma dodatkowych opłat dla klientów usługi Office 365 i można ją włączyć, wykonując kroki opisane w naszym [wprowadzenie do zarządzania hasłami](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) przewodnik.
   * **Użytkownicy, którzy utworzą konto za pomocą** samoobsługowej: w przypadku organizacji możesz nawiązaliśmy partnerstwo z firmą używanych [samoobsługowej](../users-groups-roles/directory-self-service-signup.md) są wyposażone w dzierżawie, umożliwialiśmy je zresetować hasło za pomocą adresu e-mail one zarejestrowane.
   * **Użytkowników B2B**: żadnych nowych użytkowników B2B utworzone za pomocą nowego [możliwości usługi Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) będą również mogli resetować swoje hasła przy użyciu adresu e-mail zarejestrowani w procesie zaproszenia.

Do przetestowania tego scenariusza, przejdź do http://passwordreset.microsoftonline.com przy użyciu jednego z tych użytkowników z firm partnerskich. Jeśli mają alternatywny adres e-mail lub adres e-mail uwierzytelniania zdefiniowane resetowania haseł działa zgodnie z oczekiwaniami.

> [!NOTE]
> Konta Microsoft, którym przyznano dostęp gościa do dzierżawy usługi Azure AD, takich jak Hotmail.com, Outlook.com lub innych osobistych adresów e-mail, nie są możliwe do użycia usługi Azure AD SSPR. Potrzebują do zresetowania swojego hasła, korzystając z informacji znajdujących się w [Jeśli nie możesz zalogować się na koncie Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.

## <a name="next-steps"></a>Kolejne kroki

Poniższe artykuły zawierają dodatkowe informacje dotyczące resetowania haseł za pomocą usługi Azure AD:

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "W środowisku lokalnym konfiguracji integracji z funkcji zapisywania zwrotnego haseł i informacje dotyczące rozwiązywania problemów"
