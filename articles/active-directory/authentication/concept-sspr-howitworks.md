---
title: Samoobsługowe resetowanie haseł jak to działa - usługi Azure Active Directory
description: Usługa Azure AD samoobsługowego resetowania haseł szczegółowe dane
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: f2d0b009c4451a4108222ac7aa7954ba6dd86699
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869011"
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Samoobsługowe resetowanie haseł w szczegółowe dane w usłudze Azure AD

Jak samoobsługowego resetowania haseł (SSPR) pracy? Co oznacza tej opcji w interfejsie? Kontynuuj czytanie, aby dowiedzieć się więcej na temat samoobsługowego resetowania HASEŁ usługi Azure Active Directory (Azure AD).

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
2. Użytkownik wprowadza identyfikator użytkownika i przekazuje captcha.
3. Usługa Azure AD sprawdza, czy użytkownik będzie mógł korzystać z tej funkcji, wykonując następujące testy:
   * Sprawdza, czy użytkownik ma włączenia tej funkcji i usługi Azure AD ma przypisaną licencję.
     * Jeśli użytkownik nie ma włączenia tej funkcji lub nie mieć przypisaną licencję, użytkownik jest proszony o administratora do zresetowania swojego hasła.
   * Sprawdza, czy użytkownik ma prawo rzuć wyzwanie dane zdefiniowane na swoje konto zgodnie z zasadami administratora.
     * Jeśli zasady są wymagane tylko w jednym z wyzwań, następnie gwarantuje, że użytkownik ma odpowiednie dane, które są zdefiniowane dla co najmniej jedno z wyzwań wynikające z zasadami administratora.
       * Jeśli nie skonfigurowano żądanie użytkownika, użytkownik jest zalecana do kontaktowania się z do zresetowania swojego hasła administratora.
     * Jeśli zasady wymagają dwóch wyzwań, następnie gwarantuje, że użytkownik ma odpowiednie dane, które są zdefiniowane dla co najmniej dwóch wyzwania wynikające z zasadami administratora.
       * Jeśli nie skonfigurowano żądanie użytkownika, użytkownik jest zalecana do kontaktowania się z do zresetowania swojego hasła administratora.
   * Sprawdza, czy hasło użytkownika jest zarządzane lokalnie (federacyjnych, przekazywanego uwierzytelniania lub skrótu hasła synchronizowane).
     * Jeśli wdrożono zapisywanie zwrotne hasła są zarządzane lokalnie, następnie użytkownik będzie mógł przejść do uwierzytelniania i zresetować swoje hasło.
     * Jeśli nie wdrożono zapisywanie zwrotne hasła są zarządzane lokalnie, użytkownik jest proszony o administratora do zresetowania swojego hasła.
4. Jeśli okaże się, że użytkownik jest w stanie pomyślnie zresetowania hasła, użytkownik jest przeprowadzany przez procedurę resetowania.

## <a name="authentication-methods"></a>Metody uwierzytelniania

Jeśli samoobsługowego resetowania HASŁA jest włączona, należy wybrać co najmniej jeden z następujących opcji dla metod uwierzytelniania. Czasami słyszysz te opcje określane jako "bramy". Zdecydowanie zaleca się, wybierz co najmniej dwóch metod uwierzytelniania, tak aby użytkownicy mają większą elastyczność.

* Email
* Telefon komórkowy
* Telefon biurowy
* Pytania zabezpieczające

![Uwierzytelnianie][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Jakie pola są używane w katalogu danych uwierzytelniania?

* **Telefon biurowy**: odnosi się do telefonu biurowego.
    * Użytkownicy nie będą mogli samodzielnie Ustaw to pole. Musi być zdefiniowany przez administratora.
* **Telefon komórkowy**: odpowiada numer telefonu uwierzytelniania (nie publicznie widoczne) lub telefonu komórkowego (publicznie widoczne).
    * Usługa szuka numer telefonu uwierzytelniania najpierw i następnie powróci do telefonu komórkowego, jeśli numer telefonu uwierzytelniania nie istnieje.
* **Alternatywny adres e-mail**: odpowiada adres e-mail uwierzytelniania (nie publicznie widoczne) lub alternatywny adres e-mail.
    * Usługa najpierw szuka adres e-mail uwierzytelniania, a następnie powrót po awarii do alternatywny adres e-mail.

Domyślnie tylko telefon biurowy atrybutów w chmurze i telefon komórkowy są synchronizowane z katalogiem chmury z katalogu lokalnego dla danych uwierzytelniania.

Użytkownicy mogą zresetować swoje hasło tylko, jeśli mają one dane już obecne we metod uwierzytelniania, która administratora została włączona i wymaga.

Jeśli użytkownicy nie chcą numeru telefonu komórkowego, która będzie widoczna w katalogu, ale mimo to chcą z niej korzystać w celu zresetowania hasła, wypełnij Administratorzy powinna nie w katalogu. Użytkownicy, następnie należy wypełnić ich **numer telefonu uwierzytelniania** atrybutu za pośrednictwem [portalu rejestracji resetowania haseł](https://aka.ms/ssprsetup). Administratorzy mogą zobaczyć te informacje w profilu użytkownika, ale nie jest publikowany gdzie indziej.

### <a name="the-number-of-authentication-methods-required"></a>Liczba metod uwierzytelniania, wymagany

Ta opcja określa minimalną liczbę dostępne metody uwierzytelniania lub bram, które użytkownik musi przejść do resetowania lub odblokowywania ich haseł. Można ustawić na jeden lub dwa.

Użytkownicy mogą wybrać podać więcej metod uwierzytelniania, jeśli administrator włącza tej metody uwierzytelniania.

Jeśli użytkownik nie ma zarejestrowanych metod wymagane minimalne, zobaczy strony błędu, który kieruje je do żądania, aby administrator zresetować swoje hasło.

#### <a name="change-authentication-methods"></a>Zmiana metody uwierzytelniania

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

### <a name="how-secure-are-my-security-questions"></a>Jak bezpieczne są moje pytania zabezpieczeń?

Jeśli używasz pytań zabezpieczających, firma Microsoft zaleca używanie ich razem z innej metody. Pytania zabezpieczeń może być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania innego użytkownika.

> [!NOTE] 
> Pytania zabezpieczające są przechowywane przez użytkowników i bezpiecznie w obiekcie użytkownika w katalogu, a tylko można uzyskać przez użytkowników podczas rejestracji. Nie ma możliwości dla administratora odczytać lub zmodyfikować pytań i odpowiedzi użytkownika.
>

### <a name="security-question-localization"></a>Lokalizacja pytania zabezpieczeń

Wszystkie wstępnie zdefiniowane pytania, na które należy wykonać są zlokalizowane w pełen zestaw języków usługi Office 365 i opierają się na ustawienia regionalne użytkownika przeglądarki:

* W jakim mieście poznałeś/poznałaś swoją pierwszą współmałżonkę lub partnerkę albo swojego pierwszego współmałżonka lub partnera?
* W jakim mieście spotkali się Twoi rodzice?
* Jakie jest najbliższe miasto, w którym mieszka Twoje rodzeństwo?
* W jakim mieście urodził się Twój ojciec?
* W jakim mieście podjąłeś/podjęłaś swoją pierwszą pracę?
* W jakim mieście urodziła się Twoja matka?
* W jakim mieście byłaś/eś w dniu Nowego Roku 2000?
* Jak miał na nazwisko Twój ulubiony nauczyciel w szkole średniej?
* Na jaką uczelnię próbowałeś/próbowałaś się dostać, ale się nie udało?
* Gdzie odbyło się Twoje pierwsze wesele?
* Jak ma na drugie imię Twój ojciec?
* Jaka jest Twoja ulubiona potrawa?
* Jak ma na imię i nazwisko Twoja babcia od strony matki?
* Jakie jest drugie imię Twojej matki?
* Co to jest urodzin miesiąca i roku Twojego najstarszego rodzeństwa? (na przykład listopad 1985)
* Jak ma na drugie imię Twój najstarszy brat/siostra?
* Jak miał na imię i nazwisko Twój dziadek od strony ojca?
* Jakie jest drugie imię Twojego najmłodszego rodzeństwa?
* Jak nazywała się Twoja szkoła podstawowa?
* Jak miał na imię i nazwisko Twój najlepszy przyjaciel w dzieciństwie?
* Jak miała na imię i nazwisko Twoja pierwsza poważna sympatia?
* Jak miał na nazwisko Twój ulubiony nauczyciel w szkole podstawowej?
* Jaka była marka i model Twojego pierwszego samochodu lub motocykla?
* Jak się nazywała Twoja pierwsza szkoła?
* Jak nazywał się szpital Twoich narodzin?
* Przy jakiej ulicy znajdował się Twój pierwszy dom z dzieciństwa?
* Jak się nazywał Twój bohater z dzieciństwa?
* Jak się nazywała Twoja ulubiona maskotka?
* Jak się wabiło Twoje pierwsze zwierzę domowe?
* Jaki był Twój pseudonim w dzieciństwie?
* Jaki był Twój ulubiony sport w szkole średniej?
* Jaka była Twoja pierwsza praca?
* Jakie były cztery ostatnie cyfry Twojego numeru telefonu z dzieciństwa?
* Kim chciałeś/chciałaś zostać w dzieciństwie, gdy dorośniesz?
* Jak się nazywa najpopularniejsza poznana przez Ciebie osoba?

### <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Niestandardowe pytania zabezpieczające nie są zlokalizowane dla różnych ustawień regionalnych. Wszystkie niestandardowe pytania są wyświetlane w tym samym języku wprowadzoną w interfejsie użytkownika administracyjnego, nawet wtedy, gdy ustawienia regionalne przeglądarki użytkownika jest inny. Jeśli potrzebujesz zlokalizowanych pytania, należy użyć wstępnie zdefiniowane pytania.

Maksymalna długość zapytania niestandardowe zabezpieczeń to 200 znaków.

Aby wyświetlić portal resetowania hasła i pytania w różnych zlokalizowanego języka Dołącz "? mkt =<Locale>" na końcu hasło URL resetowania z przykładu, który następuje po lokalizowanie na język hiszpański [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).

### <a name="security-question-requirements"></a>Wymagania dotyczące pytania zabezpieczeń

* Odpowiedź minimalny limit znaków to trzy znaki.
* Odpowiedź maksymalny limit znaków to 40 znaków.
* Użytkownicy nie może odebrać tego samego zapytania więcej niż jeden raz.
* Użytkownicy nie można podać tej samej odpowiedzi na więcej niż jedno pytanie.
* Dowolny zestaw znaków może służyć do definiowania pytań i odpowiedzi, w tym znaki Unicode.
* Liczba pytań zdefiniowanych przez musi być większa lub równa liczbie pytań, które były potrzebne do zarejestrowania.

## <a name="registration"></a>Rejestracja

### <a name="require-users-to-register-when-they-sign-in"></a>Wymagaj od użytkowników rejestrowania się podczas logowania

Aby włączyć tę opcję, użytkownik, który jest włączona w celu zresetowania hasła musi ukończyć rejestracji resetowania hasła w przypadku logowania się do aplikacji przy użyciu usługi Azure AD. Obejmuje to następujące czynności:

* Office 365
* Azure Portal
* Panel dostępu
* Aplikacje federacyjne
* Niestandardowe aplikacje przy użyciu usługi Azure AD

Po wyłączeniu wymagają rejestracji użytkownicy mogą nadal ręcznie rejestrować informacje kontaktowe. Mogą oni albo odwiedź [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) lub wybierz **rejestrowanie na potrzeby resetowania hasła** łącze w obszarze **profilu** kartę w panelu dostępu.

> [!NOTE]
> Użytkownicy mogą odrzucić portalu rejestracji resetowania haseł, wybierając **anulować** lub przez zamknięcie okna. Jednak użytkownicy są monitowani o rejestru, każdym logowaniu do czasu zakończenia rejestracji.
>
> Nie przerywa połączenie użytkownika, jeśli nastąpiło już zalogowanie w.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ustaw liczbę dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania

Ta opcja określa odstęp czasu między ustawienie i reconfirming informacje dotyczące uwierzytelniania i jest dostępna tylko wtedy, gdy włączysz **wymagać od użytkowników rejestrowania się podczas logowania** opcji.

Prawidłowe wartości to 0 do 730 dni, ciągiem "0", co oznacza, że użytkownicy nigdy nie zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania.

## <a name="notifications"></a>Powiadomienia

### <a name="notify-users-on-password-resets"></a>Czy powiadamiać użytkowników o resetowaniu hasła?

Jeśli ta opcja jest ustawiona na **tak**, a następnie użytkownik, który jest zresetowania hasła otrzymuje wiadomość e-mail z powiadomieniem, że ich hasło zostało zmienione. Wiadomości e-mail są wysyłane za pośrednictwem portalu samoobsługowego resetowania HASEŁ w odniesieniu do ich podstawowy i alternatywny adres e-mail, które znajdują się w pliku w usłudze Azure AD. Nikt inny nie jest powiadamiany o resetowania zdarzeń.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasła

Jeśli ta opcja jest ustawiona na **tak**, następnie *wszystkich administratorów* otrzymywać wiadomości e-mail na swój podstawowy adres e-mail w pliku w usłudze Azure AD. Adres e-mail powiadomienia że inny administrator zmieniła swoje hasło za pomocą funkcji samoobsługowego resetowania HASEŁ.

Przykład: Istnieją cztery Administratorzy w środowisku. Administrator, A Resetuje hasła, korzystając z funkcji samoobsługowego resetowania HASEŁ. Administratorzy, B, C i D będą otrzymywać wiadomości e-mail z alertem resetowania hasła.

## <a name="on-premises-integration"></a>Integracja lokalna

Jeśli zainstalować, skonfigurować i włączyć program Azure AD Connect, masz następujące dodatkowe opcje integracji środowiska lokalnego. Jeśli te opcje są wygaszone, następnie zapisywania zwrotnego nie został poprawnie skonfigurowany. Aby uzyskać więcej informacji, zobacz [Konfigurowanie funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md#configure-password-writeback).

![Zapisywanie zwrotne][Writeback]

Ta strona zawiera szybkie stan w środowisku lokalnym klientem zapisywania zwrotnego jedną z następujących komunikatów o błędach są wyświetlane na podstawie bieżącej konfiguracji:

* Usługi w środowisku lokalnym klientem zapisywania zwrotnego jest uruchomiony.
* Usługa Azure AD jest w trybie online i jest połączona z lokalnego klienta zapisywania zwrotnego. Jednak prawdopodobnie zainstalowana wersja programu Azure AD Connect jest nieaktualna. Należy wziąć pod uwagę [uaktualnienie usługi Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) aby upewnić się, że masz najnowszych funkcji łączności i ważnych poprawek błędów.
* Niestety nie możemy sprawdzić swój status klienta zapisywania zwrotnego w środowisku lokalnym, ponieważ zainstalowana wersja programu Azure AD Connect jest nieaktualna. [Uaktualnij program Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) Aby móc sprawdzić stan połączenia.
* Niestety prawdopodobnie nie można nawiązać połączenia lokalnego klienta zapisywania zwrotnego teraz. [Rozwiązywanie problemów z usługi Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) Aby przywrócić połączenie.
* Niestety, firma Microsoft nie może nawiązać lokalnego klienta zapisywania zwrotnego, ponieważ funkcja zapisywania zwrotnego haseł nie zostało poprawnie skonfigurowane. [Konfigurowanie zapisywania zwrotnego haseł](howto-sspr-writeback.md#configure-password-writeback) Aby przywrócić połączenie.
* Niestety prawdopodobnie nie można nawiązać połączenia lokalnego klienta zapisywania zwrotnego teraz. Może to być spowodowane przez tymczasowe problemy po naszej stronie. Jeśli problem będzie się powtarzać, [Rozwiązywanie problemów z programu Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) Aby przywrócić połączenie.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapisywać zwrotnie hasła do katalogu lokalnego

Ten formant określa, czy funkcja zapisywania zwrotnego haseł jest włączony dla tego katalogu. Jeśli funkcję zapisywania zwrotnego jest włączona, wskazuje stan usługi zapisywania zwrotnego w środowisku lokalnym. Jest to przydatne, jeśli chcesz tymczasowo wyłączyć funkcję zapisywania zwrotnego haseł, bez konieczności ponownie skonfigurować program Azure AD Connect.

* Jeśli przełącznik jest równa **tak**, następnie włączone zapisywanie zwrotne, i federacyjnych, przekazywanego uwierzytelniania lub użytkownicy synchronizację skrótów haseł mogą resetować swoje hasła.
* Jeśli przełącznik jest równa **nie**, zapisywania zwrotnego jest wyłączone i nie będą mogli resetować swoje hasła uwierzytelniania federacyjnego, przekazywania lub użytkownikom synchronizację skrótów haseł.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła

Ten formant wskazuje, czy użytkownicy odwiedzający portal resetowania haseł należy skorzystać z opcji odblokowanie ich kont usługi Active Directory w środowisku lokalnym bez konieczności, zresetować swoje hasło. Domyślnie usługa Azure AD umożliwia odblokowanie konta podczas resetowania hasła. To ustawienie umożliwia oddzielić te dwie operacje. 

* Jeśli ustawiona na **tak**, a następnie użytkownicy otrzymują możliwość zresetowania swojego hasła i odblokowania konta lub odblokowania konta bez resetowania hasła.
* Jeśli ustawiono **nie**, następnie użytkownicy są tylko mogli przeprowadzić resetowanie hasła połączone i operacji odblokowania konta.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Jak działa resetowania haseł dla użytkowników B2B?
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
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md)
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
