---
title: Konfigurowanie usługi Azure MFA Authentication — Azure Active Directory
description: W tym artykule opisano sposób konfigurowania ustawień usługi Azure MFA w Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7011025a1d94a5c99bf2338d9f80c683c2fd7b35
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514978"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurowanie ustawień usługi Azure MFA

Ten artykuł ułatwia zarządzanie ustawieniami uwierzytelniania wieloskładnikowego w Azure Portal. Obejmuje on różne tematy, które ułatwiają maksymalne wykorzystanie uwierzytelniania wieloskładnikowego platformy Azure. Nie wszystkie funkcje są dostępne w każdej wersji usługi Azure MFA Authentication.

Możesz uzyskać dostęp do ustawień związanych z uwierzytelnianiem wieloskładnikowe systemu Azure z Azure Portal, przechodząc do **Azure Active Directory** > **MFA**.

![Azure Portal — ustawienia usługi Azure AD — uwierzytelnianie wieloskładnikowe](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Ustawienia

Niektóre z tych ustawień mają zastosowanie do serwera MFA, usługi Azure MFA lub obu.

| Cecha | Opis |
| ------- | ----------- |
| Blokada konta | Tymczasowe blokowanie kont w usłudze wieloskładnikowe uwierzytelnianie, jeśli w wierszu występuje zbyt wiele nieudanych prób uwierzytelnienia. Ta funkcja ma zastosowanie tylko do użytkowników, którzy wprowadzają kod PIN do uwierzytelnienia. (Serwer MFA) |
| [Blokuj/Odblokuj użytkowników](#block-and-unblock-users) | Służy do blokowania określonym użytkownikom możliwości otrzymywania żądań uwierzytelniania wieloskładnikowego. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają Zablokowani przez 90 dni od momentu zablokowania. |
| [Alert o oszustwie](#fraud-alert) | Konfigurowanie ustawień związanych z możliwościami użytkowników w celu zgłaszania fałszywych żądań weryfikacji |
| [Powiadomienia](#notifications) | Włącz powiadomienia zdarzeń z serwera MFA. |
| [Tokeny OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Używany w środowiskach Azure MFA opartych na chmurze do zarządzania tokenami OATH dla użytkowników. |
| [Ustawienia połączenia telefonicznego](#phone-call-settings) | Konfigurowanie ustawień związanych z połączeniami telefonicznymi i powitami w środowiskach chmurowych i lokalnych. |
| Dostawcy | Spowoduje to wyświetlenie wszystkich istniejących dostawców uwierzytelniania, które mogły zostać skojarzone z Twoim kontem. Nie można utworzyć nowych dostawców uwierzytelniania od 1 września 2018 |

## <a name="manage-mfa-server"></a>Zarządzanie serwerem usługi MFA

Ustawienia w tej sekcji dotyczą tylko serwera MFA.

| Cecha | Opis |
| ------- | ----------- |
| Ustawienia serwera | Pobierz serwer usługi MFA i Wygeneruj poświadczenia aktywacji w celu zainicjowania środowiska |
| [Jednorazowe obejście](#one-time-bypass) | Zezwalaj użytkownikowi na uwierzytelnianie bez przeprowadzania weryfikacji dwuetapowej przez ograniczony czas. |
| [Reguły buforowania](#caching-rules) |  Buforowanie jest używane głównie w przypadku systemów lokalnych, takich jak sieć VPN, Wyślij wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia automatyczne pomyślne wykonanie kolejnych żądań po pomyślnym zakończeniu pierwszej weryfikacji w toku. |
| Stan serwera | Zobacz stan lokalnych serwerów usługi MFA, w tym wersji, stanu, adresu IP i daty ostatniej komunikacji. |

## <a name="activity-report"></a>Raporty dotyczące działań

Raporty dostępne tutaj są specyficzne dla serwera MFA (lokalnego). W przypadku raportów usługi Azure MFA (Cloud) zobacz Raport logowania w usłudze Azure AD.

## <a name="block-and-unblock-users"></a>Blokowanie i odblokowywanie użytkowników

Użyj funkcji _Blokuj i Odblokuj użytkowników_ , aby uniemożliwić użytkownikom otrzymywanie żądań uwierzytelniania. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają Zablokowani przez 90 dni od momentu zablokowania.

### <a name="block-a-user"></a>Zablokuj użytkownika

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **Azure Active Directory** > Zablokuj **/Odblokuj użytkowników**usługi**MFA** > .
3. Wybierz pozycję **Dodaj** , aby zablokować użytkownika.
4. Wybierz **grupę replikacji**. Wprowadź nazwę użytkownika dla zablokowanego użytkownika jako **username\@Domain.com**. Wprowadź komentarz w polu **Przyczyna** .
5. Wybierz pozycję **Dodaj** , aby zakończyć blokowanie użytkownika.

### <a name="unblock-a-user"></a>Odblokuj użytkownika

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **Azure Active Directory** > Zablokuj **/Odblokuj użytkowników**usługi**MFA** > .
3. Wybierz opcję Odblokuj w kolumnie **Akcja** obok użytkownika, aby odblokować.
4. Wprowadź komentarz w przyczynie odblokowania pola.
5. Wybierz opcję Odblokuj, aby zakończyć Odblokowywanie użytkownika.

## <a name="fraud-alert"></a>Alert oszustwa

Skonfiguruj funkcję _alertu oszustwa_ , aby umożliwić użytkownikom zgłaszanie fałszywych prób dostępu do zasobów. Użytkownicy mogą raportować próby oszustwa przy użyciu aplikacji mobilnej lub za pośrednictwem telefonu.

### <a name="turn-on-fraud-alerts"></a>Włącz alerty oszustwa

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **Azure Active Directory** > **alertu oszustwa**usługi**MFA** > .
3. Dla opcji **Zezwalaj użytkownikom na przesyłanie alertów oszustw** ustaw wartość **włączone**.
4. Wybierz pozycję **Zapisz**.

### <a name="configuration-options"></a>Opcje konfiguracji

* **Zablokuj użytkownika w przypadku zgłoszenia oszustwa**: Jeśli użytkownik zgłasza oszustwo, jego konto jest zablokowane przez 90 dni lub do momentu odblokowania konta przez administratora. Administrator może przejrzeć logowania przy użyciu raportu logowania i podjąć odpowiednie działania, aby zapobiec przyszłym oszustwom. Administrator może następnie [odblokować](#unblock-a-user) konto użytkownika.
* **Kod do zgłaszania oszustw podczas początkowego**powitania: Gdy użytkownicy otrzymają połączenie telefoniczne w celu przeprowadzenia weryfikacji dwuetapowej, **#** są zwykle naciskane w celu potwierdzenia ich zalogowania. Aby zgłosić oszustwo, użytkownik wprowadza kod przed naciśnięciem klawisza **#** . Ten kod jest domyślnie **0** , ale można go dostosować.

   >[!NOTE]
   >Domyślne powitanie głosu firmy Microsoft instruuje użytkowników, aby nacisnąć **0 #** w celu przesłania alertu oszustwa. Jeśli chcesz użyć kodu innego niż **0**, zarejestruj i przekaż własne niestandardowe pozdrowienia głosowe z odpowiednimi instrukcjami dla użytkowników.
   >

### <a name="view-fraud-reports"></a>Wyświetlanie raportów o oszustwie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Azure Active Directory** > **logowania**. Raport oszustwa jest teraz częścią standardowego raportu logowania do usługi Azure AD.

## <a name="notifications"></a>Powiadomienia

Skonfiguruj tutaj adresy e-mail dla użytkowników, którzy będą otrzymywać wiadomości e-mail z alertami o oszustwie.

![Przykład wiadomości e-mail z alertem oszustwa powiadomień](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Ustawienia połączenia telefonicznego

### <a name="caller-id"></a>Identyfikator obiektu wywołującego

**Numer identyfikatora rozmówcy usługi MFA** — jest to liczba, którą użytkownicy zobaczą na telefonie. Dozwolone są tylko numery w Stanach Zjednoczonych.

>[!NOTE]
>Gdy połączenia usługi uwierzytelnianie wieloskładnikowe są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami są kierowane przez operatora, który nie obsługuje identyfikatora rozmówcy. W związku z tym identyfikator wywołującego nie jest gwarantowany, mimo że system uwierzytelniania wieloskładnikowego zawsze go wysyła.

### <a name="custom-voice-messages"></a>Niestandardowe wiadomości głosowe

Możesz użyć własnych nagrań lub powitań na potrzeby weryfikacji dwuetapowej przy użyciu funkcji _niestandardowych wiadomości głosowych_ . Te komunikaty mogą służyć jako uzupełnienie lub zastępowanie nagrań firmy Microsoft.

Przed rozpoczęciem należy pamiętać o następujących ograniczeniach:

* Obsługiwane formaty plików to WAV i MP3.
* Limit rozmiaru pliku wynosi 5 MB.
* Komunikaty uwierzytelniania powinny być krótsze niż 20 sekund. Komunikaty dłuższe niż 20 sekund mogą spowodować niepowodzenie weryfikacji. Użytkownik może nie odpowiadać, zanim zakończy się komunikat i zostanie przeprowadzony limit czasu weryfikacji.

### <a name="custom-message-language-behavior"></a>Niestandardowe zachowanie języka komunikatów

Gdy do użytkownika zostanie odtworzony niestandardowy komunikat głosowy, język wiadomości zależy od następujących czynników:

* Język bieżącego użytkownika.
  * Język wykryty przez przeglądarkę użytkownika.
  * Inne scenariusze uwierzytelniania mogą zachowywać się inaczej.
* Język wszystkich dostępnych komunikatów niestandardowych.
  * Ten język jest wybierany przez administratora, gdy zostanie dodany niestandardowy komunikat.

Na przykład jeśli istnieje tylko jeden komunikat niestandardowy z językiem niemieckim:

* Użytkownik, który uwierzytelnia się w języku niemieckim, będzie słyszeć niestandardowy komunikat niemiecki.
* Użytkownik, który uwierzytelnia się w języku angielskim, będzie słyszeć standardowy komunikat w języku angielskim.

### <a name="set-up-a-custom-message"></a>Konfigurowanie wiadomości niestandardowej

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
1. Przejdź do**ustawień połączenia telefonicznego**usługi**MFA** >  **Azure Active Directory** > .
1. Wybierz pozycję **Dodaj**powitanie.
1. Wybierz typ pozdrowienia.
1. Wybierz język.
1. Wybierz plik dźwiękowy MP3 lub WAV, który ma zostać przekazany.
1. Wybierz pozycję **Dodaj**.

### <a name="custom-voice-message-defaults"></a>Niestandardowe wartości domyślne wiadomości głosowych

Przykładowe skrypty do tworzenia komunikatów niestandardowych.

| Nazwa komunikatu | Skrypt |
| --- | --- |
| Uwierzytelnienie pomyślne | Logowanie zostało pomyślnie zweryfikowane. Koniec. |
| Monit numeru wewnętrznego | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz krzyżyk, aby kontynuować. |
| Potwierdzenie oszustwa | Przesłano alert oszustwa. Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT w Twojej firmie. |
| Pozdrowienie oszustwa (metoda standardowa) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. Jeśli weryfikacja nie została zainicjowana, ktoś może próbować uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert oszustwa. Spowoduje to powiadomienie zespołu IT firmy i zablokowanie dalszych prób weryfikacji. |
| Zgłoszono oszustwo informujące o przesłaniu alertu oszustwa. | Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT w Twojej firmie. |
| Aktywacja | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Ponowna próba po odmowie uwierzytelnienia | Odmowa weryfikacji. |
| Ponów próbę (metoda standardowa) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Pozdrowienie (metoda standardowa) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Pozdrowienie (PIN) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. |
| Pozdrowienie oszustwa (PIN) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft.  Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. Jeśli weryfikacja nie została zainicjowana, ktoś może próbować uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert oszustwa. Spowoduje to powiadomienie zespołu IT firmy i zablokowanie dalszych prób weryfikacji. |
| Ponów próbę (PRZYPNIJ) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. |
| Monit numeru wewnętrznego po cyfrach | Jeśli masz już połączenie z tym numerem wewnętrznym, naciśnij krzyżyk, aby kontynuować. |
| Odmowa uwierzytelnienia | Niestety, nie można teraz zalogować. Spróbuj ponownie później. |
| Pozdrowienie aktywacji (metoda standardowa) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Ponowna próba aktywacji (metoda standardowa) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Pozdrowienie aktywacji (PIN) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. |
| Monit numeru wewnętrznego przed cyframi | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Prześlij to wywołanie do rozszerzenia... |

## <a name="one-time-bypass"></a>Jednokrotne obejście

Funkcja _jednorazowe obejście_ umożliwia użytkownikowi uwierzytelnianie pojedynczego czasu bez przeprowadzania weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund. W sytuacjach, gdy aplikacja mobilna lub telefon nie otrzymuje powiadomienia lub połączenia telefonicznego, można zezwolić na jednorazowe obejście, aby użytkownik mógł uzyskać dostęp do żądanego zasobu.

### <a name="create-a-one-time-bypass"></a>Utwórz jednorazowe obejście

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **Azure Active Directory** > **jednorazowe obejście**usługi**MFA** > .
3. Wybierz pozycję **Dodaj**.
4. W razie potrzeby wybierz grupę replikacji dla obejścia.
5. Wprowadź nazwę użytkownika jako **nazwę\@użytkownika Domain.com**. Wprowadź liczbę sekund, przez jaką obejście ma być ostatnie. Wprowadź przyczynę obejścia.
6. Wybierz pozycję **Dodaj**. Limit czasu zaczyna obowiązywać natychmiast. Użytkownik musi się zalogować przed upływem jednorazowego obejścia.

### <a name="view-the-one-time-bypass-report"></a>Wyświetl raport jednorazowego obejścia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **Azure Active Directory** > **jednorazowe obejście**usługi**MFA** > .

## <a name="caching-rules"></a>Reguły buforowania

Można ustawić okres, aby zezwolić na próby uwierzytelniania po uwierzytelnieniu użytkownika przy użyciu funkcji _buforowania_ . Kolejne próby uwierzytelniania dla użytkownika w określonym przedziale czasu powiodło się automatycznie. Buforowanie jest używane głównie w przypadku systemów lokalnych, takich jak sieć VPN, Wyślij wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia automatyczne pomyślne wykonanie kolejnych żądań po pomyślnym zakończeniu pierwszej weryfikacji w toku.

>[!NOTE]
>Funkcja buforowania nie jest przeznaczona do użycia podczas logowania do Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Konfigurowanie buforowania

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **Azure Active Directory** > **reguł buforowania** **MFA** > .
3. Wybierz pozycję **Dodaj**.
4. Z listy rozwijanej wybierz **Typ pamięci** podręcznej. Wprowadź maksymalną liczbę sekund w **pamięci**podręcznej.
5. W razie potrzeby wybierz typ uwierzytelniania i Określ aplikację.
6. Wybierz pozycję **Dodaj**.

## <a name="mfa-service-settings"></a>Ustawienia usługi MFA

Ustawienia dotyczące haseł aplikacji, zaufanych adresów IP, opcji weryfikacji i zapamiętania uwierzytelniania wieloskładnikowego na potrzeby uwierzytelniania wieloskładnikowego systemu Azure można znaleźć w temacie Ustawienia usługi. Dostęp do ustawień usługi można uzyskać z poziomu Azure Portal, przechodząc do **Azure Active Directory** > usługi**MFA** > —**wprowadzenie** > **Konfigurowanie** > **dodatkowej chmury Ustawienia usługi MFA**.

![Ustawienia usługi Azure wieloskładnikowe Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje, takie jak Office 2010 lub wcześniejsza i Apple mail przed iOS 11, nie obsługują weryfikacji dwuetapowej. Aplikacje nie są skonfigurowane do akceptowania drugiej weryfikacji. Aby korzystać z tych aplikacji, Skorzystaj z funkcji _hasła aplikacji_ . Możesz użyć hasła aplikacji zamiast tradycyjnego hasła, aby umożliwić aplikacji pominięcie weryfikacji dwuetapowej i kontynuowanie pracy.

Nowoczesne uwierzytelnianie jest obsługiwane przez klientów Microsoft Office 2013 i nowszych. Klienci pakietu Office 2013, w tym Outlook, obsługują nowoczesne protokoły uwierzytelniania i mogą być włączeni do pracy z weryfikacją dwuetapową. Po włączeniu klienta hasła aplikacji nie są wymagane dla klienta.

>[!NOTE]
>Hasła aplikacji nie działają z zasadami uwierzytelniania wieloskładnikowego opartymi na dostępie warunkowym i nowoczesnego uwierzytelniania.

### <a name="considerations-about-app-passwords"></a>Zagadnienia dotyczące haseł aplikacji

W przypadku korzystania z haseł aplikacji należy wziąć pod uwagę następujące ważne kwestie:

* Hasła aplikacji są wprowadzane tylko raz dla aplikacji. Użytkownicy nie muszą śledzić haseł ani wprowadzać ich za każdym razem.
* Rzeczywista wartość hasła jest generowana automatycznie i nie jest dostarczana przez użytkownika. Automatyczne generowanie hasła jest trudniejsze dla osoby atakującej w celu odgadnięcia i jest bezpieczniejsze.
* Dla każdego użytkownika istnieje limit 40 haseł.
* Aplikacje, które buforują hasła i używają ich w scenariuszach lokalnych, mogą zacząć kończyć się niepowodzeniem, ponieważ hasło aplikacji nie jest znane poza kontem służbowym. Przykładem tego scenariusza są wiadomości e-mail programu Exchange, które są lokalne, ale zarchiwizowana poczta znajduje się w chmurze. W tym scenariuszu to samo hasło nie działa.
* Po włączeniu uwierzytelniania wieloskładnikowego na koncie użytkownika hasła aplikacji mogą być używane z większością klientów nie korzystających z przeglądarki, takich jak Outlook i Microsoft Skype dla firm. Akcje administracyjne nie mogą być wykonywane przy użyciu haseł aplikacji przez aplikacje nie korzystające z przeglądarki, takie jak Windows PowerShell. Akcje nie mogą być wykonywane nawet wtedy, gdy użytkownik ma konto administracyjne. Aby uruchamiać skrypty programu PowerShell, należy utworzyć konto usługi o silnym haśle i nie włączać konta na potrzeby weryfikacji dwuetapowej.

>[!WARNING]
>Hasła aplikacji nie działają w środowiskach hybrydowych, w których klienci komunikują się z punktami końcowymi automatycznego odnajdywania lokalnego i w chmurze. Hasła domeny są wymagane do uwierzytelniania lokalnego. Do uwierzytelniania w chmurze wymagane są hasła aplikacji.

### <a name="guidance-for-app-password-names"></a>Wskazówki dotyczące nazw haseł aplikacji

Nazwy haseł aplikacji powinny odzwierciedlać urządzenie, na którym są używane. Jeśli masz komputer przenośny, który ma aplikacje niekorzystające z przeglądarki, takie jak Outlook, Word i Excel, Utwórz jedno hasło aplikacji o nazwie **laptop** dla tych aplikacji. Utwórz inne hasło aplikacji o nazwie **Desktop** dla tych samych aplikacji, które są uruchamiane na komputerze stacjonarnym.

>[!NOTE]
>Zalecamy utworzenie jednego hasła aplikacji dla każdego urządzenia, a nie jednego hasła aplikacji.

### <a name="federated-or-single-sign-on-app-passwords"></a>Hasła aplikacji federacyjnych lub logowania jednokrotnego

Usługa Azure AD obsługuje Federacji lub Logowanie jednokrotne (SSO) z lokalnym systemem Windows Server Active Directory Domain Services (AD DS). Jeśli Twoja organizacja jest federacyjnym usługą Azure AD i używasz uwierzytelniania wieloskładnikowego platformy Azure, weź pod uwagę następujące kwestie dotyczące haseł aplikacji.

>[!NOTE]
>Poniższe punkty dotyczą tylko klientów federacyjnych (SSO).

* Hasła aplikacji są weryfikowane przez usługę Azure AD i w związku z tym pomijają Federacji. Federacja jest aktywnie używana tylko podczas konfigurowania haseł aplikacji.
* Nie nawiązano kontaktu z dostawcą tożsamości (dostawcy tożsamości) dla użytkowników federacyjnych (SSO), w przeciwieństwie do przepływu pasywnego. Hasła aplikacji są przechowywane na koncie służbowym. Jeśli użytkownik opuści firmę, informacje o użytkowniku są przepływane do konta służbowego przy użyciu narzędzia **DirSync** w czasie rzeczywistym. Synchronizacja z wyłączeniem lub usuwaniem konta może potrwać do 3 godzin, co może opóźnić wyłączenie/usunięcie hasła aplikacji w usłudze Azure AD.
* Lokalne ustawienia Access Control klienta nie są uznawane przez funkcję haseł aplikacji.
* Funkcja rejestrowania/inspekcji lokalnego uwierzytelniania nie jest dostępna do użycia z funkcją haseł aplikacji.
* Niektóre zaawansowane architektury wymagają kombinacji poświadczeń do weryfikacji dwuetapowej z klientami programu. Te poświadczenia mogą zawierać nazwę użytkownika i hasła konta służbowego oraz hasła aplikacji. Wymagania są zależne od tego, jak uwierzytelnianie jest wykonywane. W przypadku klientów, którzy uwierzytelniają się w infrastrukturze lokalnej, wymagana jest nazwa użytkownika i hasło konta służbowego. W przypadku klientów, którzy uwierzytelniają się w usłudze Azure AD, wymagane jest hasło aplikacji.

  Załóżmy na przykład, że masz następującą architekturę:

  * Lokalne wystąpienie Active Directory jest federacyjne z usługą Azure AD.
  * Używasz usługi Exchange Online.
  * Korzystasz z lokalnego programu Skype dla firm.
  * Używasz uwierzytelniania wieloskładnikowego systemu Azure.

  W tym scenariuszu używane są następujące poświadczenia:

  * Aby zalogować się do usługi Skype dla firm, użyj nazwy użytkownika i hasła konta służbowego.
  * Aby uzyskać dostęp do książki adresowej z klienta programu Outlook, który nawiązuje połączenie z usługą Exchange Online, Użyj hasła aplikacji.

### <a name="allow-users-to-create-app-passwords"></a>Zezwól użytkownikom na tworzenie haseł aplikacji

Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Funkcja haseł aplikacji musi być włączona. Aby umożliwić użytkownikom tworzenie haseł aplikacji, należy wykonać poniższą procedurę:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy i grupy** > **Wszyscy użytkownicy**.
3. Wybierz pozycję **uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** wybierz opcję **zezwól użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki** .

### <a name="create-app-passwords"></a>Tworzenie haseł aplikacji

Użytkownicy mogą tworzyć hasła aplikacji podczas ich wstępnej rejestracji. Użytkownik może utworzyć hasła aplikacji na końcu procesu rejestracji.

Użytkownicy mogą również tworzyć hasła aplikacji po rejestracji. Aby uzyskać więcej informacji i szczegółowe instrukcje dla użytkowników, zobacz [co to są hasła aplikacji w usłudze Azure wieloskładnikowe Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Zaufane adresy IP

Funkcja _Zaufane adresy IP_ w ramach uwierzytelniania wieloskładnikowego platformy Azure jest używana przez administratorów dzierżawy zarządzanej lub federacyjnej. Ta funkcja pomija weryfikację dwuetapową dla użytkowników logujących się z firmowej sieci intranet. Ta funkcja jest dostępna w pełnej wersji usługi Azure MFA, a nie w bezpłatnej wersji dla administratorów. Aby uzyskać szczegółowe informacje na temat sposobu uzyskania pełnej wersji usługi Azure MFA Authentication, zobacz [Azure MFA Authentication](multi-factor-authentication.md).

> [!NOTE]
> Zaufane adresy IP i dostęp warunkowy usługi MFA działają tylko z adresami IPV4.

Jeśli w organizacji wdrożono rozszerzenie serwera NPS w celu zapewnienia uwierzytelniania MFA w aplikacjach lokalnych, należy zauważyć, że źródłowy adres IP zawsze będzie serwerem NPS, za pomocą którego nastąpi próba uwierzytelnienia.

| Typ dzierżawy usługi Azure AD | Opcje funkcji Zaufane adresy IP |
|:--- |:--- |
| Zarządzane |**Określony zakres adresów IP**: Administratorzy określają zakres adresów IP, które mogą pomijać weryfikację dwuetapową dla użytkowników logujących się z firmowej sieci intranet.|
| Federacyjne |**Wszyscy użytkownicy zafederacyjnych**: Wszyscy użytkownicy federacyjne, którzy logują się z wewnątrz organizacji, mogą ominąć weryfikację dwuetapową. Użytkownicy pomijają weryfikację przy użyciu roszczeń wydawanych przez Active Directory Federation Services (AD FS).<br/>**Określony zakres adresów IP**: Administratorzy określają zakres adresów IP, które mogą pomijać weryfikację dwuetapową dla użytkowników logujących się z firmowej sieci intranet. |

Obejście zaufanych adresów IP działa tylko z wewnątrz intranetu firmy. W przypadku wybrania opcji **Wszyscy użytkownicy Federacji** , a użytkownik loguje się spoza firmowej sieci intranet, użytkownik musi uwierzytelnić się przy użyciu weryfikacji dwuetapowej. Ten proces jest taki sam, nawet jeśli użytkownik prezentuje AD FS. 

### <a name="end-user-experience-inside-of-corpnet"></a>Środowisko użytkownika końcowego w sieci firmowej

Po wyłączeniu funkcji Zaufane adresy IP w przypadku przepływów przeglądarki wymagana jest weryfikacja dwuetapowa. Hasła aplikacji są wymagane w przypadku starszych aplikacji klienckich.

Po włączeniu funkcji Zaufane adresy IP weryfikacja dwuetapowa *nie* jest wymagana w przypadku przepływów przeglądarki. Hasła aplikacji *nie* są wymagane w przypadku starszych aplikacji klienckich, pod warunkiem, że użytkownik nie utworzył hasła aplikacji. Gdy hasło aplikacji jest w użyciu, hasło pozostaje wymagane. 

### <a name="end-user-experience-outside-corpnet"></a>Środowisko użytkownika końcowego poza siecią Corpnet

Niezależnie od tego, czy funkcja zaufanych adresów IP jest włączona, dla przepływów przeglądarki wymagana jest weryfikacja dwuetapowa. Hasła aplikacji są wymagane w przypadku starszych aplikacji klienckich.

### <a name="enable-named-locations-by-using-conditional-access"></a>Włączanie nazwanych lokalizacji przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **dostępu** > warunkowego**o nazwie lokalizacje**.
3. Wybierz pozycję **Nowa lokalizacja**.
4. Wprowadź nazwę lokalizacji.
5. Wybierz pozycję **Oznacz jako zaufaną lokalizację**.
6. Wprowadź zakres adresów IP w notacji CIDR, np. **192.168.1.1/24**.
7. Wybierz pozycję **Utwórz**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Włączanie funkcji Zaufane adresy IP przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **dostępu** > warunkowego**o nazwie lokalizacje**.
3. Wybierz pozycję **Konfiguruj Zaufane adresy IP usługi MFA**.
4. Na stronie **Ustawienia usługi** w obszarze **Zaufane adresy IP**wybierz jedną z następujących dwóch opcji:

   * **W przypadku żądań od użytkowników federacyjnych pochodzących z mojego intranetu**: Aby wybrać tę opcję, zaznacz pole wyboru. Wszyscy użytkownicy federacyjne, którzy logują się z sieci firmowej, pomijają weryfikację dwuetapową przy użyciu roszczeń wydawanych przez AD FS. Upewnij się, że AD FS ma regułę umożliwiającą dodanie do odpowiedniego ruchu w intranecie. Jeśli reguła nie istnieje, Utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu publicznych adresów IP**: Aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP, które znajdują się w zakresie od xxx. xxx. xxx. 1 do xxx. xxx. xxx. 254, użyj notacji, takiej jak **xxx. xxx. xxx. 0/24**.
      * Dla pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pomijają weryfikację dwuetapową.

5. Wybierz pozycję **Zapisz**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Włączanie funkcji Zaufane adresy IP przy użyciu ustawień usługi

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy**.
3. Wybierz pozycję **uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** w obszarze **Zaufane adresy IP**wybierz jedną z następujących opcji:

   * **W przypadku żądań od użytkowników federacyjnych w moim intranecie**: Aby wybrać tę opcję, zaznacz pole wyboru. Wszyscy użytkownicy federacyjne, którzy logują się z sieci firmowej, pomijają weryfikację dwuetapową przy użyciu roszczeń wydawanych przez AD FS. Upewnij się, że AD FS ma regułę umożliwiającą dodanie do odpowiedniego ruchu w intranecie. Jeśli reguła nie istnieje, Utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu podsieci adresów IP**: Aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP, które znajdują się w zakresie od xxx. xxx. xxx. 1 do xxx. xxx. xxx. 254, użyj notacji, takiej jak **xxx. xxx. xxx. 0/24**.
      * Dla pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pomijają weryfikację dwuetapową.

6. Wybierz pozycję **Zapisz**.

## <a name="verification-methods"></a>Metody weryfikacji

Możesz wybrać metody weryfikacji, które są dostępne dla użytkowników. Poniższa tabela zawiera krótkie omówienie metod.

Gdy użytkownicy rejestrują swoje konta na potrzeby uwierzytelniania wieloskładnikowego systemu Azure, wybierają preferowaną metodę weryfikacji z opcji, które zostały włączone. Wskazówki dotyczące procesu rejestracji użytkownika znajdują się w [sekcji Konfigurowanie mojego konta na potrzeby weryfikacji](../user-help/multi-factor-authentication-end-user-first-time.md)dwuetapowej.

| Metoda | Opis |
|:--- |:--- |
| Połączenie z telefonem |Umieszcza automatyczne połączenie głosowe. Użytkownik odbiera połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. Numer telefonu nie jest zsynchronizowany z Active Directoryami lokalnymi. |
| SMS na telefon |Wysyła wiadomość tekstową zawierającą kod weryfikacyjny. Użytkownik otrzymuje monit o wprowadzenie kodu weryfikacyjnego w interfejsie logowania. Ten proces jest nazywany jednokierunkową wiadomością SMS. Dwukierunkowa wiadomość SMS oznacza, że użytkownik musi wykonać tekst ponownie w określonym kodzie. Dwukierunkowa wiadomość SMS jest przestarzała i nie jest obsługiwana po 14 listopada 2018. Użytkownicy skonfigurowani do dwukierunkowego SMS są automatycznie przełączani w taki sposób, aby wywoływać weryfikację _telefonu_ .|
| Powiadomienie przez aplikację mobilną |Wysyła powiadomienie wypychane na telefon lub zarejestrowane urządzenie. Użytkownik wyświetli powiadomienie i wybierze opcję **Weryfikuj** , aby ukończyć weryfikację. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |Aplikacja Microsoft Authenticator generuje nowy kod weryfikacyjny OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny do interfejsu logowania. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Włączanie i wyłączanie metod weryfikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy i grupy** > **Wszyscy użytkownicy**.
3. Wybierz pozycję **uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** w obszarze **Opcje weryfikacji**wybierz/Usuń zaznaczenie metod, które mają być dostępne dla użytkowników.
6. Kliknij polecenie **Zapisz**.

Dodatkowe szczegóły dotyczące korzystania z metod uwierzytelniania można znaleźć w artykule [co to są metody uwierzytelniania](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Pamiętaj dane usługi Multi-Factor Authentication

Funkcja _Zapamiętaj uwierzytelnianie wieloskładnikowe_ dla urządzeń i przeglądarek, które są zaufane przez użytkownika, to bezpłatna funkcja dla wszystkich użytkowników usługi uwierzytelniania wieloskładnikowego. Użytkownicy mogą ominąć kolejne weryfikacji przez określoną liczbę dni po pomyślnym zalogowaniu się na urządzeniu przy użyciu uwierzytelniania wieloskładnikowego. Ta funkcja zwiększa użyteczność przez zminimalizowanie liczby prób przeprowadzenia weryfikacji dwuetapowej na tym samym urządzeniu.

>[!IMPORTANT]
>W przypadku naruszenia zabezpieczeń konta lub urządzenia zapamiętanie uwierzytelniania wieloskładnikowego dla zaufanych urządzeń może wpłynąć na bezpieczeństwo. Jeśli konto firmowe zostanie złamane lub zostanie utracone lub skradzione, należy [przywrócić uwierzytelnianie wieloskładnikowe na wszystkich urządzeniach](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Akcja Przywróć odwołuje stan zaufany ze wszystkich urządzeń, a użytkownik musi wykonać weryfikację dwuetapową ponownie. Możesz również poinstruować użytkowników, aby przywracali uwierzytelnianie wieloskładnikowe na swoich urządzeniach, korzystając z instrukcji w sekcji [Zarządzanie ustawieniami weryfikacji](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)dwuetapowej.
>

### <a name="how-the-feature-works"></a>Jak działa funkcja

Funkcja Zapamiętaj uwierzytelnianie wieloskładnikowe ustawia trwały plik cookie w przeglądarce, gdy użytkownik wybierze opcję **nie Monituj ponownie dla X dni** podczas logowania. Użytkownik nie będzie ponownie monitowany o uwierzytelnianie wieloskładnikowe z tej samej przeglądarki, dopóki plik cookie nie wygaśnie. Jeśli użytkownik otworzy inną przeglądarkę na tym samym urządzeniu lub wyczyści pliki cookie, zostanie ponownie wyświetlony monit o zweryfikowanie.

Opcja **nie Monituj ponownie dla X dni** nie jest wyświetlana w aplikacjach bez przeglądarki, niezależnie od tego, czy aplikacja obsługuje nowoczesne uwierzytelnianie. Te aplikacje używają _tokenów odświeżania_ , które udostępniają nowe tokeny dostępu co godzinę. Po sprawdzeniu poprawności tokenu odświeżania usługa Azure AD sprawdza, czy Ostatnia weryfikacja dwuetapowa wystąpiła w ciągu określonej liczby dni.

Funkcja zmniejsza liczbę uwierzytelnień w aplikacjach sieci Web, które zwykle monitują się za każdym razem. Ta funkcja zwiększa liczbę uwierzytelnień dla nowoczesnych klientów uwierzytelniania, które zwykle monitują co 90 dni. Może również zwiększyć liczbę uwierzytelnień w połączeniu z zasadami dostępu warunkowego.

>[!IMPORTANT]
>Funkcja **Zapamiętaj uwierzytelnianie wieloskładnikowe** nie jest zgodna z funkcją nie wylogowuj **mnie** AD FS, gdy użytkownicy przeprowadzają weryfikację dwuetapową dla AD FS za pomocą serwera usługi Azure MFA lub wieloskładnikowego Rozwiązanie uwierzytelniania.
>
>Jeśli użytkownik wybierze opcję Nie wylogowuj **mnie** na AD FS, a także oznaczy swoje urządzenie jako zaufane do uwierzytelniania wieloskładnikowego, użytkownik nie zostanie automatycznie zweryfikowany po upływie **następującej liczby dni** . Usługa Azure AD żąda nowej weryfikacji dwuetapowej, ale AD FS zwraca token z pierwotnym wnioskiem usługi MFA i datą usługi uwierzytelniania wieloskładnikowego, a nie ponownie wykonuje weryfikacji dwuetapowej. **Ta reakcja ustawia pętlę weryfikacji między usługą Azure AD a AD FS.**
>

### <a name="enable-remember-multi-factor-authentication"></a>Włącz Zapamiętaj uwierzytelnianie wieloskładnikowe

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy i grupy** > **Wszyscy użytkownicy**.
3. Wybierz pozycję **uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** **Zarządzaj Zapamiętaj uwierzytelnianie wieloskładnikowe**, wybierz opcję **Zezwalaj użytkownikom na zapamiętywanie uwierzytelniania wieloskładnikowego na urządzeniach, które ufają** .
6. Ustaw liczbę dni, przez które zaufane urządzenia mogą pomijać weryfikację dwuetapową. Wartość domyślna to 14 dni.
7. Wybierz pozycję **Zapisz**.

### <a name="mark-a-device-as-trusted"></a>Oznacz urządzenie jako zaufane

Po włączeniu funkcji Zapamiętaj uwierzytelnianie wieloskładnikowe użytkownicy mogą oznaczyć urządzenie jako zaufane podczas logowania, wybierając pozycję **nie Monituj ponownie**.

## <a name="next-steps"></a>Kolejne kroki

[Modyfikuj znakowanie strony logowania usługi Azure AD](../fundamentals/customize-branding.md)
