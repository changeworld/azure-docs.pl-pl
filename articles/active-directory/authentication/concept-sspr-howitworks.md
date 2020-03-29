---
title: Samoobsługowe resetowanie haseł — usługa Azure Active Directory
description: Jak działa samoobsługowe resetowanie haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848582"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to działa: samoobsługowe resetowanie hasła usługi Azure AD

Jak działa samoobsługowe resetowanie haseł (SSPR)? Co oznacza ta opcja w interfejsie? Kontynuuj czytanie, aby dowiedzieć się więcej o sspr usługi Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Jak działa portal resetowania hasła?

Gdy użytkownik przejdzie do portalu resetowania hasła, zostanie rozpoczęty przepływ pracy w celu określenia:

   * Jak należy zlokalizować stronę?
   * Czy konto użytkownika jest prawidłowe?
   * Do jakiej organizacji należy użytkownik?
   * Gdzie jest zarządzane hasło użytkownika?
   * Czy użytkownik ma licencję na korzystanie z tej funkcji?

Zapoznaj się z następującymi krokami, aby dowiedzieć się więcej o logice stojącej za stroną resetowania hasła:

1. Użytkownik wybiera link **Nie można uzyskać dostępu do** konta [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)lub przechodzi bezpośrednio do .
   * Na podstawie ustawień regionalnych przeglądarki środowisko jest renderowane w odpowiednim języku. Środowisko resetowania hasła jest zlokalizowane w tych samych językach, które obsługuje usługa Office 365.
   * Aby wyświetlić portal resetowania hasła w innym zlokalizowanym języku dołącz "?mkt=" na końcu adresu URL [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)resetowania hasła, w przykładzie, który następuje po zlokalizowaniu języka hiszpańskiego .
2. Użytkownik wprowadza identyfikator użytkownika i przekazuje captcha.
3. Usługa Azure AD sprawdza, czy użytkownik może korzystać z tej funkcji, wykonując następujące kontrole:
   * Sprawdza, czy użytkownik ma włączoną tę funkcję i ma przypisaną licencję usługi Azure AD.
     * Jeśli użytkownik nie ma włączonej tej funkcji lub ma przypisaną licencję, zostanie poproszony o skontaktowanie się z administratorem w celu zresetowania hasła.
   * Sprawdza, czy użytkownik ma odpowiednie metody uwierzytelniania zdefiniowane na swoim koncie zgodnie z zasadami administratora.
     * Jeśli zasady wymagają tylko jednej metody, zapewnia, że użytkownik ma odpowiednie dane zdefiniowane dla co najmniej jednej z metod uwierzytelniania włączonych przez zasady administratora.
       * Jeśli metody uwierzytelniania nie są skonfigurowane, użytkownik powinien skontaktować się z administratorem, aby zresetować hasło.
     * Jeśli zasady wymagają dwóch metod, a następnie zapewnia, że użytkownik ma odpowiednie dane zdefiniowane dla co najmniej dwóch metod uwierzytelniania włączonych przez zasady administratora.
       * Jeśli metody uwierzytelniania nie są skonfigurowane, użytkownik powinien skontaktować się z administratorem, aby zresetować hasło.
     * Jeśli rola administratora platformy Azure jest przypisana do użytkownika, wymusza się silna zasada haseł z dwiema bramkami. Więcej informacji na temat tych zasad można znaleźć w sekcji [Różnice zasad resetowania administratora](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Sprawdza, czy hasło użytkownika jest zarządzane lokalnie (federacyjne, uwierzytelnianie przekazywane lub synchronizacja haszyszu hasła).
     * Jeśli writeback jest wdrożony i hasło użytkownika jest zarządzany lokalnie, użytkownik może przystąpić do uwierzytelniania i resetowania hasła.
     * Jeśli writeback nie zostanie wdrożony, a hasło użytkownika jest zarządzane lokalnie, użytkownik jest proszony o skontaktowanie się z administratorem w celu zresetowania hasła.
4. Jeśli zostanie ustalone, że użytkownik jest w stanie pomyślnie zresetować swoje hasło, użytkownik jest prowadzony przez proces resetowania.

## <a name="authentication-methods"></a>Metody uwierzytelniania

Jeśli funkcja SSPR jest włączona, należy wybrać co najmniej jedną z następujących opcji dla metod uwierzytelniania. Czasami słyszysz te opcje określane jako "bramy". Zdecydowanie zaleca się **wybranie dwóch lub więcej metod uwierzytelniania,** aby użytkownicy mieli większą elastyczność w przypadku, gdy nie mogą uzyskać dostępu do niej, gdy jej potrzebują. Dodatkowe informacje na temat metod wymienionych poniżej można znaleźć w artykule [Co to są metody uwierzytelniania?](concept-authentication-methods.md).

* Powiadomienie aplikacji mobilnej
* Kod aplikacji mobilnej
* Adres e-mail
* Telefon komórkowy
* Telefon służbowy
* Pytania zabezpieczające

Użytkownicy mogą zresetować swoje hasło tylko wtedy, gdy mają dane obecne w metodach uwierzytelniania, które administrator włączył.

> [!IMPORTANT]
> Począwszy od marca 2019 r. opcje połączeń telefonicznych nie będą dostępne dla użytkowników usługi MFA i SSPR w bezpłatnych/próbnych dzierżawach usługi Azure AD. Zmiana ta nie ma wpływu na wiadomości SMS. Połączenie telefoniczne będzie nadal dostępne dla użytkowników płatnych dzierżaw usługi Azure AD. Ta zmiana ma wpływ tylko na bezpłatnych/testowych dzierżaw usług Azure AD.

> [!WARNING]
> Konta przypisane role administratora platformy Azure będą wymagane do korzystania z metod zdefiniowanych w sekcji [Administrator resetować różnice zasad](concept-sspr-policy.md#administrator-reset-policy-differences).

![Wybór metod uwierzytelniania w witrynie Azure portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Liczba wymaganych metod uwierzytelniania

Ta opcja określa minimalną liczbę dostępnych metod uwierzytelniania lub bramek, które użytkownik musi przejść, aby zresetować lub odblokować swoje hasło. Można go ustawić na jeden lub dwa.

Użytkownicy mogą podać więcej metod uwierzytelniania, jeśli administrator włączy tę metodę uwierzytelniania.

Jeśli użytkownik nie ma zarejestrowanych minimalnych wymaganych metod, zobaczy stronę błędu, która kieruje go do żądania, aby administrator zresetował swoje hasło.

#### <a name="mobile-app-and-sspr"></a>Aplikacja mobilna i SSPR

Podczas korzystania z aplikacji mobilnej, takiej jak aplikacja Microsoft Authenticator, jako metody resetowania hasła, należy pamiętać o następujących zastrzeżeniach:

* Jeśli administratorzy wymagają jednej metody, aby zresetować hasło, kod weryfikacyjny jest jedyną dostępną opcją.
* Gdy administratorzy wymagają dwóch metod, aby zresetować hasło, użytkownicy mogą używać kodu weryfikacyjnego powiadomienia **lub** kodu weryfikacyjnego oprócz innych włączonych metod. **EITHER**

| Liczba metod wymaganych do zresetowania | Jeden | Dwa |
| :---: | :---: | :---: |
| Dostępne funkcje aplikacji mobilnej | Code | Kod lub powiadomienie |

Użytkownicy nie mają możliwości zarejestrowania swojej aplikacji mobilnej podczas [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)rejestracji w celu samodzielnego resetowania hasła z . Użytkownicy mogą zarejestrować [https://aka.ms/mfasetup](https://aka.ms/mfasetup)swoją aplikację mobilną w [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)programie , lub w nowej wersji podglądu rejestracji informacji zabezpieczających pod adresem .

> [!WARNING]
> Aby użytkownicy [Converged registration for self-service password reset and Azure Multi-Factor Authentication (Public preview)](concept-registration-mfa-sspr-converged.md) mogli uzyskać dostęp do nowego środowiska w [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)programie .

> [!IMPORTANT]
> Nie można wybrać aplikacji uwierzytelniającej jako jedynej metody uwierzytelniania podczas konfigurowania zasad 1-gate. Podobnie nie można wybrać aplikacji uwierzytelniającej i tylko jednej dodatkowej metody podczas konfigurowania zasad 2-bramkowych.
> Następnie podczas konfigurowania zasad samookapła, które zawierają aplikację uwierzytelniającą jako metodę, należy wybrać co najmniej dodatkową metodę podczas konfigurowania zasad 1-gate i należy wybrać co najmniej dwie dodatkowe metody podczas konfigurowania zasad 2-bramkowych.
> Powodem tego wymagania jest to, że bieżące środowisko rejestracji samookreślenia nie zawiera opcji rejestrowania aplikacji uwierzytelniającego. Opcja rejestracji aplikacji uwierzytelniającego jest dołączona do nowej [rejestracji konwergentnej w celu samodzielnego resetowania hasła i uwierzytelniania wieloskładnikowego platformy Azure (publiczna wersja zapoznawcza).](concept-registration-mfa-sspr-converged.md)
> Zezwalanie na zasady, które korzystają tylko z aplikacji uwierzytelniającej (w przypadku zasad 1-gate) lub aplikacji uwierzytelniającej i tylko jednej dodatkowej metody (dla zasad 2-bramkowych), może prowadzić do zablokowania użytkownikom rejestracji w celu uzyskania odwołania dowolnie, dopóki nie zostaną skonfigurowani do korzystania z nowego doświadczenie rejestracyjne.

### <a name="change-authentication-methods"></a>Zmienianie metod uwierzytelniania

Jeśli zaczniesz od zasady, która ma tylko jedną wymaganą metodę uwierzytelniania do resetowania lub odblokowania zarejestrowaną i zmienisz to na dwie metody, co się stanie?

| Liczba zarejestrowanych metod | Liczba wymaganych metod | Wynik |
| :---: | :---: | :---: |
| 1 lub więcej | 1 | **Możliwość** resetowania lub odblokowywania |
| 1 | 2 | **Nie można** zresetować ani odblokować |
| 2 lub więcej | 2 | **Możliwość** resetowania lub odblokowywania |

Jeśli zmienisz typy metod uwierzytelniania, których użytkownik może używać, możesz przypadkowo uniemożliwić użytkownikom korzystanie z funkcji sspr, jeśli nie mają minimalnej dostępnej ilości danych.

Przykład:
1. Oryginalna zasada jest skonfigurowana przy użyciu dwóch wymaganych metod uwierzytelniania. Używa tylko numeru telefonu biura i pytań zabezpieczających. 
2. Administrator zmienia zasady, aby nie używać już pytań zabezpieczających, ale umożliwia korzystanie z telefonu komórkowego i alternatywnej poczty e-mail.
3. Użytkownicy bez wypełnionych pól telefonu komórkowego lub alternatywnych wiadomości e-mail nie mogą zresetować swoich haseł.

## <a name="registration"></a>Rejestracja

### <a name="require-users-to-register-when-they-sign-in"></a>Wymagaj od użytkowników rejestracji podczas logowania

Włączenie tej opcji wymaga od użytkownika ukończenia rejestracji resetowania hasła, jeśli zaloguje się do dowolnej aplikacji przy użyciu usługi Azure AD. Ten przepływ pracy obejmuje następujące aplikacje:

* Office 365
* Portal Azure
* Panel dostępu
* Aplikacje federacyjne
* Aplikacje niestandardowe korzystające z usługi Azure AD

Gdy wymaganie rejestracji jest wyłączone, użytkownicy mogą ręcznie się zarejestrować. Mogą odwiedzić [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) lub wybrać łącze **Zarejestruj hasło resetowania** w zakładce **Profil** w Panelu dostępu.

> [!NOTE]
> Użytkownicy mogą odrzucić portal rejestracji resetowania hasła, wybierając **anuluj** lub zamykając okno. Ale są monitowani o rejestrację za każdym razem, gdy się zalogują, dopóki nie dokończą rejestracji.
>
> To przerwanie nie powoduje przerwania połączenia użytkownika, jeśli jest on już zalogowany.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ustawianie liczby dni, przez które użytkownicy są proszeni o ponowne potwierdzenie swoich informacji uwierzytelniających

Ta opcja określa okres między ustawianiem i potwierdzaniem informacji uwierzytelniania i jest dostępna tylko wtedy, gdy **włączysz opcję Wymagaj od użytkowników rejestracji podczas logowania.**

Prawidłowe wartości to od 0 do 730 dni, a wartość "0" oznacza, że użytkownicy nigdy nie są proszeni o ponowne potwierdzenie swoich informacji uwierzytelniających.

## <a name="notifications"></a>Powiadomienia

### <a name="notify-users-on-password-resets"></a>Czy powiadamiać użytkowników o resetowaniu hasła?

Jeśli ta opcja jest ustawiona na **Tak,** użytkownicy resetujący hasło otrzymają wiadomość e-mail z powiadomieniem o zmianie hasła. Wiadomość e-mail jest wysyłana za pośrednictwem portalu SSPR na ich podstawowe i alternatywne adresy e-mail, które są w pliku w usłudze Azure AD. Nikt inny nie jest powiadamiany o zdarzeniu resetowania.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Powiadamianie wszystkich administratorów, gdy inni administratorzy resetują hasła

Jeśli ta opcja jest **ustawiona**na Tak, *wszyscy administratorzy* otrzymują wiadomość e-mail na swój podstawowy adres e-mail w pliku w usłudze Azure AD. Wiadomość e-mail powiadamia ich, że inny administrator zmienił hasło przy użyciu funkcji SSPR.

Przykład: W środowisku jest czterech administratorów. Administrator A resetuje swoje hasło przy użyciu funkcji SSPR. Administratorzy B, C i D otrzymują wiadomość e-mail z powiadomieniem o zresetowaniu hasła.

## <a name="on-premises-integration"></a>Integracja lokalna

Jeśli zainstalujesz, skonfigurujesz i włączysz usługę Azure AD Connect, dostępne są następujące dodatkowe opcje integracji lokalnej. Jeśli te opcje są wyszarzone, storc nie został poprawnie skonfigurowany. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapisywania haseł](howto-sspr-writeback.md).

![Sprawdzanie poprawności storszania hasła jest włączone i działa][Writeback]

Ta strona zapewnia szybki stan lokalnego klienta stornowanego, jeden z następujących komunikatów jest wyświetlany na podstawie bieżącej konfiguracji:

* Twój lokalny klient stornuje jest uruchomiony.
* Usługa Azure AD jest w trybie online i jest połączona z lokalnym klientem stornuj. Wygląda jednak na to, że zainstalowana wersja usługi Azure AD Connect jest nieaktualna. Należy rozważyć [uaktualnienie usługi Azure AD Connect,](../hybrid/how-to-upgrade-previous-version.md) aby upewnić się, że masz najnowsze funkcje łączności i ważne poprawki błędów.
* Niestety nie możemy sprawdzić lokalnego stanu klienta stornowania zwrotnego, ponieważ zainstalowana wersja usługi Azure AD Connect jest nieaktualna. [Uaktualnij usługę Azure AD Connect,](../hybrid/how-to-upgrade-previous-version.md) aby móc sprawdzić stan połączenia.
* Niestety, wygląda na to, że nie możemy teraz połączyć się z lokalnym klientem stornuj. [Rozwiązywanie problemów z usługą Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) w celu przywrócenia połączenia.
* Niestety nie możemy połączyć się z lokalnym klientem stornuj, ponieważ zapisywanie zwrotne haseł nie zostało poprawnie skonfigurowane. [Skonfiguruj storament haseł,](howto-sspr-writeback.md) aby przywrócić połączenie.
* Niestety, wygląda na to, że nie możemy teraz połączyć się z lokalnym klientem stornuj. Może to być spowodowane tymczasowymi problemami po naszej stronie. Jeśli problem będzie się powtarzał, [rozwiąż problem z usługą Azure AD Connect,](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) aby przywrócić połączenie.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Odpisy hasła do katalogu lokalnego

Ta kontrolka określa, czy funkcja zapisywania zwrotnego hasła jest włączona dla tego katalogu. Jeśli writeback jest włączony, wskazuje stan lokalnej usługi stornowania zwrotnego. Ten formant jest przydatne, jeśli chcesz tymczasowo wyłączyć zapisywanie haseł bez konieczności ponownego konfigurowania usługi Azure AD Connect.

* Jeśli przełącznik jest ustawiony na **Tak,** a następnie writeback jest włączona i federacyjne, uwierzytelniania przekazywanego lub hash hash hasło zsynchronizowane użytkownicy są w stanie zresetować swoje hasła.
* Jeśli przełącznik jest ustawiony na **Nie,** a następnie writeback jest wyłączona, a federacyjne, uwierzytelnianie przekazywane lub haszysz hasła zsynchronizowane użytkownicy nie są w stanie zresetować swoje hasła.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła

Ten formant określa, czy użytkownicy, którzy odwiedzają portal resetowania hasła, powinni mieć możliwość odblokowania lokalnych kont usługi Active Directory bez konieczności resetowania hasła. Domyślnie usługa Azure AD odblokowuje konta podczas resetowania hasła. To ustawienie służy do oddzielenia tych dwóch operacji.

* Jeśli ustawiona jest na **Tak,** użytkownicy mają możliwość zresetowania hasła i odblokowania konta lub odblokowania konta bez konieczności resetowania hasła.
* Jeśli ustawiono na **Nie,** użytkownicy będą mogli wykonać tylko operację resetowania hasła i odblokowania konta połączoną.

### <a name="on-premises-active-directory-password-filters"></a>Lokalne filtry haseł usługi Active Directory

Samoobsługowe resetowanie hasła usługi Azure AD powoduje wykonanie odpowiednika resetowania hasła zainicjowanego przez administratora w usłudze Active Directory. Jeśli do wymuszania reguł haseł niestandardowych używasz do filtru haseł innych firm i wymagasz, aby ten filtr haseł był sprawdzany podczas samoobsługowego resetowania hasła usługi Azure AD, upewnij się, że rozwiązanie do filtrowania haseł innych firm jest skonfigurowane do stosowania w scenariusz resetowania hasła administratora. [Ochrona hasłem usługi Azure AD dla usługi Windows Server Active Directory](concept-password-ban-bad-on-premises.md) jest domyślnie obsługiwana.

## <a name="password-reset-for-b2b-users"></a>Resetowanie hasła dla użytkowników B2B

Resetowanie i zmiana hasła są w pełni obsługiwane we wszystkich konfiguracjach typu business-to-business (B2B). Resetowanie hasła użytkownika B2B jest obsługiwane w następujących trzech przypadkach:

* **Użytkownicy z organizacji partnerskiej z istniejącą dzierżawą usługi Azure AD:** Jeśli organizacja, z którą współpracujesz, ma istniejącą dzierżawę usługi Azure AD, *szanujemy wszelkie zasady resetowania hasła są włączone w tej dzierżawie.* Aby resetowanie hasła działało, organizacja partnerska musi tylko upewnić się, że jest włączone samookapowanie usług Ad Azure. Klienci usługi Office 365 nie pobierają żadnych dodatkowych opłat i można ją włączyć, wykonując czynności opisane w naszym przewodniku [Wprowadzenie do zarządzania hasłami.](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)
* **Użytkownicy, którzy zarejestrują się za pośrednictwem** samoobsługowej rejestracji: Jeśli organizacja, z którą współpracujesz, korzystała z funkcji [samoobsługowej rejestracji,](../users-groups-roles/directory-self-service-signup.md) aby dostać się do dzierżawy, umożliwiamy im zresetowanie hasła za pomocą zarejestrowanej wiadomości e-mail.
* **Użytkownicy B2B:** Wszyscy nowi użytkownicy B2B utworzone przy użyciu nowych [funkcji usługi Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) będą mogli również zresetować swoje hasła za pomocą adresu e-mail, który zarejestrowali podczas procesu zapraszania.

Aby przetestować ten https://passwordreset.microsoftonline.com scenariusz, przejdź do jednego z tych użytkowników partnerskich. Jeśli mają zdefiniowaną alternatywną wiadomość e-mail lub uwierzytelniania, resetowanie hasła działa zgodnie z oczekiwaniami.

> [!NOTE]
> Konta Microsoft, którym przyznano dostęp gościa do dzierżawy usługi Azure AD, takie jak konta z Hotmail.com, Outlook.com lub innych osobistych adresów e-mail, nie mogą używać samookapów usługi Azure AD. Muszą zresetować swoje hasło przy użyciu informacji znalezionych w [artykule Kiedy nie możesz zalogować się do konta Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

## <a name="next-steps"></a>Następne kroki

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
* [Myślę, że coś jest zepsute. Jak rozwiązać problem z łatem SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Konfiguracja zapisywania zwrotnego i rozwiązywanie problemów z integracją lokalną"
