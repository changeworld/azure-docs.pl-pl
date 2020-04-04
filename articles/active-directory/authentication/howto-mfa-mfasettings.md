---
title: Konfigurowanie uwierzytelniania wieloskładnikowego platformy Azure — usługa Azure Active Directory
description: W tym artykule opisano sposób konfigurowania ustawień uwierzytelniania wieloskładnikowego platformy Azure w witrynie Azure portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: da995afba1dc8eff295c9b724a78da95b9caac85
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653978"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurowanie ustawień uwierzytelniania wieloskładnikowego platformy Azure

Ten artykuł ułatwia zarządzanie ustawieniami uwierzytelniania wieloskładnikowego w witrynie Azure portal. Obejmuje różne tematy, które pomagają w pełni wykorzystać sposób uwierzytelniania wieloskładnikowego platformy Azure. Nie wszystkie funkcje są dostępne w każdej wersji uwierzytelniania wieloskładnikowego platformy Azure.

Dostęp do ustawień związanych z uwierzytelnianiem wieloskładnikowym platformy Azure można uzyskać z witryny Azure Portal, przeglądając usługę **Azure Active Directory** > **Security** > **MFA**.

![Witryna Azure portal — ustawienia uwierzytelniania wieloskładnikowego usługi Azure AD](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Ustawienia

Niektóre z tych ustawień dotyczą serwera usługi MFA, usługi Azure MFA lub obu tych ustawień.

| Funkcja | Opis |
| ------- | ----------- |
| Blokada konta | Tymczasowo zablokuj konta w usłudze uwierzytelniania wieloskładnikowego, jeśli w wierszu jest zbyt wiele prób odmowy uwierzytelnienia. Ta funkcja dotyczy tylko użytkowników, którzy wejdą kod PIN do uwierzytelnienia. (Serwer usługi MFA) |
| [Blokowanie/odblokowywanie użytkowników](#block-and-unblock-users) | Służy do blokowania określonych użytkowników z możliwości odbierania żądań uwierzytelniania wieloskładnikowego. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają zablokowani przez 90 dni od momentu zablokowania. |
| [Alert dotyczący wykrycia oszustwa](#fraud-alert) | Konfigurowanie ustawień związanych z możliwością zgłaszania przez użytkowników fałszywych żądań weryfikacji |
| [Powiadomienia](#notifications) | Włącz powiadomienia o zdarzeniach z serwera usługi MFA. |
| [Tokeny OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Używane w środowiskach usługi Azure MFA w chmurze do zarządzania tokenami OATH dla użytkowników. |
| [Ustawienia połączenia telefonicznego](#phone-call-settings) | Skonfiguruj ustawienia związane z połączeniami telefonicznymi i pozdrowieniami dla środowisk w chmurze i lokalnie. |
| Dostawcy | Spowoduje to wyświetlenie istniejących dostawców uwierzytelniania, którzy mogą być powiązani z twoim kontem. Od 1 września 2018 r. nie można utworzyć nowych dostawców uwierzytelniania |

## <a name="manage-mfa-server"></a>Zarządzanie serwerem usługi MFA

Ustawienia w tej sekcji są przeznaczone tylko dla serwera usługi MFA.

| Funkcja | Opis |
| ------- | ----------- |
| Ustawienia serwera | Pobieranie serwera usługi MFA i generowanie poświadczeń aktywacji w celu zainicjowania środowiska |
| [Jednorazowa obwodnica](#one-time-bypass) | Zezwalaj użytkownikowi na uwierzytelnienie bez przeprowadzania weryfikacji dwuetapowej przez ograniczony czas. |
| [Zasady buforowania](#caching-rules) |  Buforowanie jest używane głównie wtedy, gdy systemy lokalne, takie jak VPN, wysyłają wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia kolejne żądania zakończyć się pomyślnie automatycznie, po użytkownik pomyślnie pierwszej weryfikacji w toku. |
| Stan serwera | Zobacz stan lokalnych serwerów usługi MFA, w tym wersję, stan, adres IP i datę ostatniej komunikacji. |

## <a name="activity-report"></a>Raport z działalności

Raportowanie dostępne w tym miejscu jest specyficzne dla serwera usługi MFA (lokalnie). Raporty usługi Azure MFA (cloud) można znaleźć w raporcie logowania w usłudze Azure AD.

## <a name="block-and-unblock-users"></a>Blokowanie i odblokowywanie użytkowników

Użyj funkcji _bloku i odblokuj użytkowników,_ aby uniemożliwić użytkownikom odbieranie żądań uwierzytelniania. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają zablokowani przez 90 dni od momentu zablokowania.

### <a name="block-a-user"></a>Blokowanie użytkownika

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Przejdź do **przystawki Azure Active Directory** > Security**MFA** > **MFA** > **Block/unblock users**.
3. Wybierz **pozycję Dodaj,** aby zablokować użytkownika.
4. Wybierz **grupę replikacji**. Wprowadź nazwę użytkownika zablokowanego jako **nazwę użytkownika\@domain.com**. Wprowadź komentarz w polu **Przyczyna.**
5. Wybierz **pozycję Dodaj,** aby zakończyć blokowanie użytkownika.

### <a name="unblock-a-user"></a>Odblokowywanie użytkownika

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Przejdź do **przystawki Azure Active Directory** > Security**MFA** > **MFA** > **Block/unblock users**.
3. Wybierz **pozycję Odblokuj** w kolumnie **Akcja** obok użytkownika, aby odblokować.
4. Wprowadź komentarz w polu **Powód odblokowania.**
5. Wybierz **opcję Odblokuj,** aby zakończyć odblokowanie użytkownika.

## <a name="fraud-alert"></a>Alert dotyczący wykrycia oszustwa

Skonfiguruj funkcję _alertu o oszustwach,_ aby użytkownicy mogli zgłaszać fałszywe próby uzyskania dostępu do swoich zasobów. Użytkownicy mogą zgłaszać próby oszustwa za pomocą aplikacji mobilnej lub telefonu.

### <a name="turn-on-fraud-alerts"></a>Włączanie alertów o oszustwach

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Przejdź do**alertu o oszustwach**usługi **Azure Active Directory** > **Security** > **MFA** > .
3. Ustaw ustawienie **Zezwalaj użytkownikom na przesyłanie alertów o oszustwach** na **Włączone**.
4. Wybierz **pozycję Zapisz**.

### <a name="configuration-options"></a>Opcje konfiguracji

* **Blokowanie użytkownika w przypadku zgłoszenia oszustwa:** jeśli użytkownik zgłosi oszustwo, jego konto zostanie zablokowane na 90 dni lub dopóki administrator nie odblokuje swojego konta. Administrator może przeglądać logowania przy użyciu raportu logowania i podejmować odpowiednie działania, aby zapobiec oszustwom w przyszłości. Administrator może następnie [odblokować](#unblock-a-user) konto użytkownika.
* **Kod do zgłaszania oszustw podczas wstępnego powitania:** Gdy użytkownicy odbierają **#** połączenie telefoniczne w celu przeprowadzenia weryfikacji dwuetapowej, zwykle naciskają, aby potwierdzić swoje logowanie. Aby zgłosić oszustwo, użytkownik wpisuje **#** kod przed naciśnięciem przycisku . Ten kod jest domyślnie **0,** ale można go dostosować.

   >[!NOTE]
   >Domyślne powitania głosowe firmy Microsoft instruują użytkowników, aby naciskali **klawisz 0#,** aby przesłać alert o oszustwie. Jeśli chcesz użyć kodu innego niż **0**, nagraj i prześlij własne niestandardowe powitania głosowe z odpowiednimi instrukcjami dla użytkowników.
   >

### <a name="view-fraud-reports"></a>Wyświetlanie raportów o oszustwach

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Logowanie do usługi Azure Active Directory** > **Sign-ins**. Raport o oszustwach jest teraz częścią standardowego raportu logowania usługi Azure AD.

## <a name="notifications"></a>Powiadomienia

Skonfiguruj tutaj adresy e-mail dla użytkowników, którzy otrzymają wiadomości e-mail z alertami o oszustwach.

![Próbka wiadomości e-mail z powiadomieniem o oszustwie](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Ustawienia połączenia telefonicznego

### <a name="caller-id"></a>Identyfikator rozmówcy

**Numer ID dzwoniącego usługi MFA** — jest to numer, który użytkownicy zobaczą na swoim telefonie. Dozwolone są tylko numery z siedzibą w STANACH Zjednoczonych.

>[!NOTE]
>Gdy połączenia uwierzytelniania wieloskładnikowego są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami są one kierowane przez operatora, który nie obsługuje identyfikatora dzwoniącego. Z tego powodu identyfikator wywołującego nie jest gwarantowana, mimo że system uwierzytelniania wieloskładnikowego zawsze go wysyła.

W Stanach Zjednoczonych, jeśli nie skonfigurowano identyfikatora rozmówcy usługi MFA, połączenia głosowe firmy Microsoft pochodzą z następujących numerów: +1 (866) 539 4191, +1 (855) 330 8653 i +1 (877) 668 6536. Jeśli używasz filtrów antyspamowych, pamiętaj o wykluczeniu tych numerów.

### <a name="custom-voice-messages"></a>Niestandardowe wiadomości głosowe

Możesz użyć własnych nagrań lub pozdrowień do weryfikacji dwuetapowej za pomocą niestandardowej funkcji _wiadomości głosowych._ Te komunikaty mogą być używane jako dodatek do lub do zastąpienia nagrań firmy Microsoft.

Zanim zaczniesz, należy pamiętać o następujących ograniczeniach:

* Obsługiwane formaty plików to .wav i .mp3.
* Limit rozmiaru pliku wynosi 1 MB.
* Komunikaty uwierzytelniania powinny być krótsze niż 20 sekund. Wiadomości dłuższe niż 20 sekund mogą spowodować niepowodzenie weryfikacji. Użytkownik może nie odpowiedzieć przed zakończeniem wiadomości i przesunieniem się czasu weryfikacji.

### <a name="custom-message-language-behavior"></a>Niestandardowe zachowanie języka wiadomości

Gdy niestandardowa wiadomość głosowa jest odtwarzana użytkownikowi, język wiadomości zależy od następujących czynników:

* Język bieżącego użytkownika.
  * Język wykryty przez przeglądarkę użytkownika.
  * Inne scenariusze uwierzytelniania mogą zachowywać się inaczej.
* Język wszystkich dostępnych wiadomości niestandardowych.
  * Ten język jest wybierany przez administratora po dodaniu wiadomości niestandardowej.

Na przykład, jeśli istnieje tylko jedna wiadomość niestandardowa, z językiem niemieckim:

* Użytkownik, który uwierzytelnia się w języku niemieckim, usłyszy niestandardową wiadomość niemiecką.
* Użytkownik, który uwierzytelnia się w języku angielskim, usłyszy standardową wiadomość w języku angielskim.

### <a name="set-up-a-custom-message"></a>Konfigurowanie wiadomości niestandardowej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
1. Przejdź do**ustawień połączeń telefonicznych**usługi **Azure Active Directory** > **Security** > **MFA** > .
1. Wybierz **pozycję Dodaj powitanie**.
1. Wybierz typ powitania.
1. Wybierz język.
1. Wybierz plik dźwiękowy .mp3 lub wav do przesłania.
1. Wybierz pozycję **Dodaj**.

### <a name="custom-voice-message-defaults"></a>Niestandardowe ustawienia wiadomości głosowych

Przykładowe skrypty do tworzenia wiadomości niestandardowych.

| Nazwa wiadomości | Skrypt |
| --- | --- |
| Uwierzytelnianie zakończyło się pomyślnie | Twoje logowanie zostało pomyślnie zweryfikowane. Do widzenia. |
| Monit o rozszerzenie | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby kontynuować. |
| Potwierdzenie oszustwa | Przesunięty został wpis dotyczący oszustwa. Aby odblokować konto, skontaktuj się z działem pomocy informatycznej firmy. |
| Powitanie oszustwa (Standard) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. Jeśli nie zainicjowałeś tej weryfikacji, ktoś może próbować uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert o oszustwie. Spowoduje to powiadomienie zespołu IT twojej firmy i zablokowanie dalszych prób weryfikacji. |
| Zgłoszone oszustwo Został przesłany alert o oszustwie. | Aby odblokować konto, skontaktuj się z działem pomocy informatycznej firmy. |
| Aktywacja | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Odmowa ponownego uwierzytelnienia | Odmowa weryfikacji. |
| Ponów próbę (standardowa) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Powitanie (standard) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Powitanie (PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź kod PIN, a następnie klucz funta, aby zakończyć weryfikację. |
| Powitanie oszustwa (PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft.  Wprowadź kod PIN, a następnie klucz funta, aby zakończyć weryfikację. Jeśli nie zainicjowałeś tej weryfikacji, ktoś może próbować uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert o oszustwie. Spowoduje to powiadomienie zespołu IT twojej firmy i zablokowanie dalszych prób weryfikacji. |
| Ponów próbę (kod PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź kod PIN, a następnie klucz funta, aby zakończyć weryfikację. |
| Monit o rozszerzenie po cyfrach | Jeśli już w tym rozszerzeniu, naciśnij klawisz funta, aby kontynuować. |
| Odmowa uwierzytelnienia | Przykro mi, nie możemy cię w tej chwili podpisać. Spróbuj ponownie później. |
| Powitanie aktywacji (standard) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Ponowna aktywacja (standard) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Powitanie aktywacji (PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź kod PIN, a następnie klucz funta, aby zakończyć weryfikację. |
| Monit o rozszerzenie przed cyframi | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Proszę przenieść to połączenie do rozszerzenia ... |

## <a name="one-time-bypass"></a>Jednorazowa obwodnica

Funkcja _jednorazowego pomijania_ umożliwia użytkownikowi uwierzytelnienie jednokrotnego bez przeprowadzania weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund. W sytuacjach, gdy aplikacja mobilna lub telefon nie odbiera powiadomienia lub połączenia telefonicznego, można zezwolić na jednorazowe obejście, aby użytkownik mógł uzyskać dostęp do żądanego zasobu.

### <a name="create-a-one-time-bypass"></a>Tworzenie jednorazowego obejścia

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Przejdź do jednorazowego**obejścia** **usługi Azure Active Directory** > **Security** > **MFA** > .
3. Wybierz pozycję **Dodaj**.
4. W razie potrzeby wybierz grupę replikacji dla obejścia.
5. Wprowadź nazwę użytkownika jako **nazwę użytkownika\@domain.com**. Wprowadź liczbę sekund, przez jaką obejście ma trwać. Wprowadź przyczynę obejścia.
6. Wybierz pozycję **Dodaj**. Termin wchodzi w życie natychmiast. Użytkownik musi zalogować się przed wygaśnięciem pomijania jednorazowego.

### <a name="view-the-one-time-bypass-report"></a>Wyświetlanie raportu jednorazowego obejścia

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do jednorazowego**obejścia** **usługi Azure Active Directory** > **Security** > **MFA** > .

## <a name="caching-rules"></a>Zasady buforowania

Można ustawić okres czasu, aby zezwolić na próby uwierzytelniania po uwierzytelnieniu użytkownika przy użyciu funkcji _buforowania._ Kolejne próby uwierzytelnienia dla użytkownika w określonym przedziale czasu zakończyć się pomyślnie automatycznie. Buforowanie jest używane głównie wtedy, gdy systemy lokalne, takie jak VPN, wysyłają wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia kolejne żądania zakończyć się pomyślnie automatycznie, po użytkownik pomyślnie pierwszej weryfikacji w toku.

>[!NOTE]
>Funkcja buforowania nie jest przeznaczona do logowania do usługi Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Konfigurowanie buforowania

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Przejdź do**reguł buforowania**usługi Azure **Active Directory** > **Security** > **MFA** > .
3. Wybierz pozycję **Dodaj**.
4. Wybierz **typ pamięci podręcznej** z listy rozwijanej. Wprowadź maksymalną liczbę **sekund pamięci podręcznej**.
5. W razie potrzeby wybierz typ uwierzytelniania i określ aplikację.
6. Wybierz pozycję **Dodaj**.

## <a name="mfa-service-settings"></a>Ustawienia usługi usługi MFA

Ustawienia haseł aplikacji, zaufanych usług IP, opcji weryfikacji i zapamiętania uwierzytelniania wieloskładnikowego dla uwierzytelniania wieloskładnikowego platformy Azure można znaleźć w ustawieniach usługi. Dostęp do ustawień usługi można uzyskać z **witryny** > Azure Portal, przeglądając usługę Azure Active Directory**Security** > **MFA** > **Wprowadzenie** > **Konfigurowanie** > dodatkowych ustawień usługi**MFA w chmurze**.

![Ustawienia usługi uwierzytelniania wieloskładnikowego platformy Azure](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

Zakresy zaufanych adresów IP mogą być prywatne lub publiczne.

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje, takie jak Office 2010 lub starsze i Apple Mail przed systemem iOS 11, nie obsługują weryfikacji dwuetapowej. Aplikacje nie są skonfigurowane do akceptowania drugiej weryfikacji. Aby korzystać z tych aplikacji, skorzystaj z funkcji _haseł aplikacji._ Zamiast tradycyjnego hasła możesz użyć hasła aplikacji, aby umożliwić aplikacji ominięcie weryfikacji dwuetapowej i kontynuowanie pracy.

Nowoczesne uwierzytelnianie jest obsługiwane dla klientów pakietu Microsoft Office 2013 i nowszych. Klienci pakietu Office 2013, w tym program Outlook, obsługują nowoczesne protokoły uwierzytelniania i mogą być włączeni do pracy z weryfikacją dwuetapową. Po włączeniu klienta hasła aplikacji nie są wymagane dla klienta.

>[!NOTE]
>Hasła aplikacji nie działają z zasadami uwierzytelniania wieloskładnikowego opartymi na dostępie warunkowym i nowoczesnym uwierzytelnianiem.

### <a name="considerations-about-app-passwords"></a>Zagadnienia dotyczące haseł aplikacji

Podczas korzystania z haseł aplikacji należy wziąć pod uwagę następujące ważne kwestie:

* Hasła aplikacji są wprowadzane tylko raz na aplikację. Użytkownicy nie muszą śledzić haseł ani wprowadzać ich za każdym razem.
* Rzeczywiste hasło jest generowane automatycznie i nie jest dostarczane przez użytkownika. Automatycznie wygenerowane hasło jest trudniejsze do odgadnięcia przez osobę atakującą i jest bezpieczniejsze.
* Istnieje limit 40 haseł na użytkownika.
* Aplikacje, które buforują hasła i używają ich w scenariuszach lokalnych, mogą zacząć działać niepowodzeniem, ponieważ hasło aplikacji nie jest znane poza kontem służbowym. Przykładem tego scenariusza jest wiadomości e-mail programu Exchange, które są lokalne, ale zarchiwizowana poczta znajduje się w chmurze. W tym scenariuszu to samo hasło nie działa.
* Po włączeniu uwierzytelniania wieloskładnikowego na koncie użytkownika hasła aplikacji mogą być używane z większością klientów innych niż przeglądarki, takich jak Outlook i Microsoft Skype dla firm. Akcji administracyjnych nie można wykonać przy użyciu haseł aplikacji za pośrednictwem aplikacji innych niż przeglądarki, takich jak Windows PowerShell. Akcji nie można wykonać nawet wtedy, gdy użytkownik ma konto administracyjne. Aby uruchomić skrypty programu PowerShell, utwórz konto usługi z silnym hasłem i nie włączaj konta do weryfikacji dwuetapowej.

>[!WARNING]
>Hasła aplikacji nie działają w środowiskach hybrydowych, w których klienci komunikują się z punktami końcowymi automatycznego odnajdywania lokalnego i w chmurze. Hasła domeny są wymagane do uwierzytelniania lokalnego. Hasła aplikacji są wymagane do uwierzytelniania w chmurze.

### <a name="guidance-for-app-password-names"></a>Wskazówki dotyczące nazw haseł aplikacji

Nazwy haseł aplikacji powinny odzwierciedlać urządzenie, na którym są używane. Jeśli masz laptopa, który ma aplikacje inne niż przeglądarki, takie jak Outlook, Word i Excel, utwórz jedno hasło aplikacji o nazwie **Laptop** dla tych aplikacji. Utwórz inne hasło aplikacji o nazwie **Pulpit** dla tych samych aplikacji, które są uruchamiane na komputerze stacjonarnym.

>[!NOTE]
>Zaleca się utworzenie jednego hasła aplikacji na urządzenie, a nie jednego hasła aplikacji na aplikację.

### <a name="federated-or-single-sign-on-app-passwords"></a>Hasła do aplikacji federacyjne lub logowania jednokrotnego

Usługa Azure AD obsługuje federację lub logowanie jednokrotne (Logowanie jednokrotne) z lokalnymi Usługami domenowymi Active Directory systemu Windows Server (AD DS). Jeśli Twoja organizacja jest sfederowana z usługą Azure AD i używasz uwierzytelniania wieloskładnikowego platformy Azure, należy wziąć pod uwagę następujące kwestie dotyczące haseł aplikacji.

>[!NOTE]
>Poniższe punkty dotyczą tylko klientów sfederowanych (SSO).

* Hasła aplikacji są weryfikowane przez usługę Azure AD i w związku z tym pomiń federację. Federacja jest aktywnie używana tylko podczas konfigurowania haseł aplikacji.
* Dostawca tożsamości (IdP) nie jest kontaktowany dla użytkowników federacyjnych (SSO), w przeciwieństwie do przepływu pasywnego. Hasła aplikacji są przechowywane na koncie służbowym. Jeśli użytkownik opuści firmę, informacje użytkownika przepływa do konta służbowego przy użyciu **programu DirSync** w czasie rzeczywistym. Wyłączenie/usunięcie konta może potrwać do trzech godzin, aby zsynchronizować, co może opóźnić wyłączenie/usunięcie hasła aplikacji w usłudze Azure AD.
* Lokalne ustawienia kontroli dostępu klienta nie są honorowane przez funkcję haseł aplikacji.
* Nie jest dostępna lokalna funkcja rejestrowania/inspekcji uwierzytelniania lokalnego do użytku z funkcją haseł aplikacji.
* Niektóre zaawansowane architektury wymagają kombinacji poświadczeń do weryfikacji dwuetapowej z klientami. Te poświadczenia mogą zawierać nazwę użytkownika konta służbowego i hasła oraz hasła do aplikacji. Wymagania zależą od sposobu uwierzytelniania. Dla klientów, którzy uwierzytelniają się w infrastrukturze lokalnej, nazwa użytkownika konta służbowego lub szkolnego i hasło wymagane. Dla klientów, którzy uwierzytelniają się za pomocą usługi Azure AD, hasło aplikacji jest wymagane.

  Załóżmy na przykład, że masz następującą architekturę:

  * Lokalne wystąpienie usługi Active Directory jest sfederowane z usługą Azure AD.
  * Korzystasz z programu Exchange online.
  * Korzystasz z programu Skype dla firm lokalnie.
  * Używasz uwierzytelniania wieloskładnikowego platformy Azure.

  W tym scenariuszu używasz następujących poświadczeń:

  * Aby zalogować się do programu Skype dla firm, użyj nazwy użytkownika i hasła do konta służbowego.
  * Aby uzyskać dostęp do książki adresowej z klienta programu Outlook, który łączy się z programem Exchange online, użyj hasła aplikacji.

### <a name="allow-users-to-create-app-passwords"></a>Zezwalaj użytkownikom na tworzenie haseł aplikacji

Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Funkcja haseł aplikacji musi być włączona. Aby dać użytkownikom możliwość tworzenia haseł aplikacji, należy skorzystać z następującej procedury:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję**Użytkownicy** **usługi Azure Active Directory** > .
3. Wybierz pozycję **Uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz **pozycję Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** wybierz opcję **Zezwalaj użytkownikom na tworzenie haseł aplikacji, aby zalogować się do aplikacji innych niż przeglądarki.**

### <a name="create-app-passwords"></a>Tworzenie haseł aplikacji

Użytkownicy mogą tworzyć hasła aplikacji podczas wstępnej rejestracji. Użytkownik ma możliwość tworzenia haseł aplikacji na końcu procesu rejestracji.

Użytkownicy mogą również tworzyć hasła aplikacji po rejestracji. Aby uzyskać więcej informacji i szczegółowe kroki dla użytkowników, zobacz [Co to są hasła aplikacji w usłudze Azure Uwierzytelnianie wieloskładnikowe?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Zaufane adresy IP

Funkcja _Zaufanych usług IP_ uwierzytelniania wieloskładnikowego platformy Azure jest używana przez administratorów dzierżawy zarządzanej lub federacyjnej. Funkcja pomija weryfikację dwuetapową dla użytkowników, którzy logują się z firmowego intranetu. Ta funkcja jest dostępna z pełną wersją uwierzytelniania wieloskładnikowego platformy Azure, a nie z bezpłatną wersją dla administratorów. Aby uzyskać szczegółowe informacje na temat sposobu uzyskania pełnej wersji uwierzytelniania wieloskładnikowego platformy Azure, zobacz [Uwierzytelnianie wieloskładnikowe platformy Azure.](multi-factor-authentication.md)

> [!NOTE]
> Zaufane adresy IPA i lokalizacje o nazwie Dostęp warunkowy działają tylko z adresami IPV4.

Jeśli organizacja wdraża rozszerzenie NPS, aby zapewnić usługi MFA aplikacjom lokalnym, należy pamiętać, że źródłowy adres IP zawsze będzie serwerem NPS, przez który przepływa próba uwierzytelnienia.

| Typ dzierżawy usługi Azure AD | Opcje funkcji zaufanych usług IP |
|:--- |:--- |
| Zarządzani |**Określony zakres adresów IP:** Administratorzy określają zakres adresów IP, które mogą pomijać weryfikację dwuetapową dla użytkowników logujących się z firmowego intranetu. Można skonfigurować maksymalnie 50 zakresów zaufanych adresów IP.|
| Federacyjni |**Wszyscy użytkownicy federowani:** Wszyscy użytkownicy federowani, którzy logują się od wewnątrz organizacji, mogą ominąć weryfikację dwuetapową. Użytkownicy pomijają weryfikację przy użyciu oświadczenia wystawionego przez usługi federacyjne Active Directory (AD FS).<br/>**Określony zakres adresów IP:** Administratorzy określają zakres adresów IP, które mogą pomijać weryfikację dwuetapową dla użytkowników logujących się z firmowego intranetu. |

Pomostowanie zaufanych usług IP działa tylko z wnętrza intranetu firmy. Jeśli **wybierzesz opcję Wszyscy użytkownicy federowani** i użytkownik zaloguje się spoza firmowego intranetu, użytkownik musi uwierzytelnić się przy użyciu weryfikacji dwuetapowej. Proces jest taki sam, nawet jeśli użytkownik przedstawia ad fs oświadczenia. 

### <a name="end-user-experience-inside-of-corpnet"></a>Doświadczenie użytkownika końcowego wewnątrz corpnet

Gdy funkcja Zaufanych usług IP jest wyłączona, weryfikacja dwuetapowa jest wymagana dla przepływów przeglądarki. Hasła aplikacji są wymagane dla starszych rozbudowanych aplikacji klienckich.

Gdy funkcja Zaufanych usług IP jest włączona, weryfikacja dwuetapowa *nie* jest wymagana dla przepływów przeglądarki. Hasła aplikacji *nie* są wymagane dla starszych rozbudowanych aplikacji klienckich, pod warunkiem że użytkownik nie utworzył hasła aplikacji. Po użyciu hasła aplikacji hasło pozostaje wymagane. 

### <a name="end-user-experience-outside-corpnet"></a>Środowisko użytkownika końcowego poza corpnet

Niezależnie od tego, czy funkcja zaufanych usług IP jest włączona, weryfikacja dwuetapowa jest wymagana dla przepływów przeglądarki. Hasła aplikacji są wymagane dla starszych rozbudowanych aplikacji klienckich.

### <a name="enable-named-locations-by-using-conditional-access"></a>Włączanie nazwanych lokalizacji przy użyciu dostępu warunkowego

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję Lokalizacje**nazwanych****nazwanych usług dostępu warunkowego** > **zabezpieczeń** >  **usługi Azure Active Directory** > .
3. Wybierz **pozycję Nowa lokalizacja**.
4. Wprowadź nazwę lokalizacji.
5. Wybierz **pozycję Oznacz jako zaufaną lokalizację**.
6. Wprowadź zakres IP w notacji CIDR jak **192.168.1.1/24**.
7. Wybierz **pozycję Utwórz**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Włączanie funkcji Zaufane punkty ip przy użyciu dostępu warunkowego

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję Lokalizacje**nazwanych****nazwanych usług dostępu warunkowego** > **zabezpieczeń** >   **usługi Azure Active Directory** > .
3. Wybierz **pozycję Konfiguruj zaufane usługi IP usługi MFA**.
4. Na stronie **Ustawienia usługi** w obszarze **Zaufane wiadomości IP**wybierz jedną z następujących dwóch opcji:

   * **W przypadku żądań użytkowników federowanych pochodzących z mojego intranetu:** Aby wybrać tę opcję, zaznacz to pole wyboru. Wszyscy użytkownicy federowani, którzy logują się z sieci firmowej, omijają weryfikację dwuetapową przy użyciu oświadczenia wystawionego przez usługi AD FS. Upewnij się, że usługi AD FS ma regułę, aby dodać oświadczenie intranetu do odpowiedniego ruchu. Jeśli reguła nie istnieje, utwórz następującą regułę w u usług AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu publicznych adresów IP:** Aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * Dla adresów IP, które znajdują się w zakresie od xxx.xxx.xxx.1 do xxx.xxx.xxx.254, użyj notacji, takiej jak **xxx.xxx.xxx.0/24**.
      * W przypadku pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź do 50 zakresów adresów IP. Użytkownicy, którzy logują się z tych adresów IP, pomijają weryfikację dwuetapową.

5. Wybierz **pozycję Zapisz**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Włączanie funkcji Zaufane usługi IP przy użyciu ustawień usługi

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję**Użytkownicy** **usługi Azure Active Directory** > .
3. Wybierz pozycję **Uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz **pozycję Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** w obszarze **Zaufane wiadomości IP**wybierz jedną (lub obie) z następujących dwóch opcji:

   * **W przypadku żądań od użytkowników federowanych w moim intranecie:** Aby wybrać tę opcję, zaznacz to pole wyboru. Wszyscy użytkownicy federowani, którzy logują się z sieci firmowej, omijają weryfikację dwuetapową przy użyciu oświadczenia wystawionego przez usługi AD FS. Upewnij się, że usługi AD FS ma regułę, aby dodać oświadczenie intranetu do odpowiedniego ruchu. Jeśli reguła nie istnieje, utwórz następującą regułę w u usług AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu podsieci adresów IP:** Aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * Dla adresów IP, które znajdują się w zakresie od xxx.xxx.xxx.1 do xxx.xxx.xxx.254, użyj notacji, takiej jak **xxx.xxx.xxx.0/24**.
      * W przypadku pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź do 50 zakresów adresów IP. Użytkownicy, którzy logują się z tych adresów IP, pomijają weryfikację dwuetapową.

6. Wybierz **pozycję Zapisz**.

## <a name="verification-methods"></a>Metody weryfikacji

Możesz wybrać metody weryfikacji, które są dostępne dla użytkowników. Poniższa tabela zawiera krótki przegląd metod.

Gdy użytkownicy rejestrują swoje konta dla uwierzytelniania wieloskładnikowego platformy Azure, wybierają preferowaną metodę weryfikacji z włączonych opcji. Wskazówki dotyczące procesu rejestracji użytkownika znajdują się w części [Konfigurowanie konta do weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metoda | Opis |
|:--- |:--- |
| Zadzwoń na telefon |Umieszcza automatyczne połączenie głosowe. Użytkownik odbiera połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. Numer telefonu nie jest synchronizowany z lokalną usługą Active Directory. |
| Wiadomość SMS na telefon |Wysyła wiadomość tekstową zawierającą kod weryfikacyjny. Użytkownik jest monitowany o wprowadzenie kodu weryfikacyjnego do interfejsu logowania. Proces ten jest nazywany jednostronnym SMS-em. Dwukierunkowy sms oznacza, że użytkownik musi tekst z powrotem określonego kodu. Dwukierunkowy sms jest przestarzały i nie jest obsługiwany po 14 listopada 2018 r. Administratorzy powinni włączyć inną metodę dla użytkowników, którzy wcześniej korzystali z dwukierunkowego programu SMS.|
| Powiadomienie za pośrednictwem aplikacji mobilnej |Wysyła powiadomienie push do telefonu lub zarejestrowanego urządzenia. Użytkownik wyświetla powiadomienie i wybiera **opcję Sprawdź,** aby zakończyć weryfikację. Aplikacja Microsoft Authenticator jest dostępna dla [systemów Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |Aplikacja Microsoft Authenticator generuje nowy kod weryfikacyjny OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny do interfejsu logowania. Aplikacja Microsoft Authenticator jest dostępna dla [systemów Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

### <a name="enable-and-disable-verification-methods"></a>Włączanie i wyłączanie metod weryfikacji

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję**Użytkownicy** **usługi Azure Active Directory** > .
3. Wybierz pozycję **Uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz **pozycję Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** w obszarze **Opcje weryfikacji**wybierz/usuń zaznaczenie metod, które mają być dostępne dla użytkowników.
6. Kliknij przycisk **Zapisz**.

Dodatkowe szczegóły dotyczące korzystania z metod uwierzytelniania można znaleźć w artykule [Jakie są metody uwierzytelniania.](concept-authentication-methods.md)

## <a name="remember-multi-factor-authentication"></a>Zapamiętywanie uwierzytelniania wieloskładnikowego

Funkcja _zapamiętywania uwierzytelniania wieloskładnikowego_ dla urządzeń i przeglądarek, które są zaufane przez użytkownika, jest bezpłatną funkcją dla wszystkich użytkowników uwierzytelniania wieloskładnikowego. Użytkownicy mogą ominąć kolejne weryfikacje przez określoną liczbę dni po pomyślnym zalogowaniu się na urządzeniu przy użyciu usługi Multi-Factor Authentication. Ta funkcja zwiększa użyteczność, minimalizując liczbę operacji weryfikacji dwuetapowej na tym samym urządzeniu.

>[!IMPORTANT]
>Jeśli konto lub urządzenie zostanie naruszone, zapamiętywanie uwierzytelniania wieloskładnikowego dla zaufanych urządzeń może mieć wpływ na bezpieczeństwo. Jeśli konto firmowe zostanie przejęte lub zaufane urządzenie zostanie utracone lub skradzione, należy [odwołać sesje usługi MFA](howto-mfa-userdevicesettings.md).
>
>Akcja przywracania odwołuje stan zaufany ze wszystkich urządzeń, a użytkownik jest zobowiązany do ponownego przeprowadzenia weryfikacji dwuetapowej. Można również poinstruować użytkowników, aby przywracali uwierzytelnianie wieloskładnikowe na własnych urządzeniach, korzystając z instrukcji w sekcji [Zarządzanie ustawieniami weryfikacji dwuetapowej.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>Jak działa ta funkcja

Funkcja zapamiętywania uwierzytelniania wieloskładnikowego ustawia trwały plik cookie w przeglądarce, gdy użytkownik wybierze opcję **Nie pytaj ponownie o X dni** podczas logowania. Użytkownik nie jest ponownie monitowany o uwierzytelnianie wieloskładnikowe z tej samej przeglądarki, dopóki plik cookie nie wygaśnie. Jeśli użytkownik otworzy inną przeglądarkę na tym samym urządzeniu lub wyczyści pliki cookie, zostanie ponownie wyświetlony monit o zweryfikowanie.

Opcja **Nie pytaj ponownie dla X dni** nie jest wyświetlana w aplikacjach innych niż przeglądarki, niezależnie od tego, czy aplikacja obsługuje nowoczesne uwierzytelnianie. Te aplikacje używają _tokenów odświeżania,_ które zapewniają nowe tokeny dostępu co godzinę. Po zatwierdzeniu tokenu odświeżania usługa Azure AD sprawdza, czy ostatnia weryfikacja dwuetapowa wystąpiła w ciągu określonej liczby dni.

Funkcja zmniejsza liczbę uwierzytelniania w aplikacjach internetowych, które zwykle monitują za każdym razem. Funkcja zwiększa liczbę uwierzytelnień dla nowoczesnych klientów uwierzytelniania, które zwykle monitują co 90 dni. Może również zwiększyć liczbę uwierzytelniania w połączeniu z zasadami dostępu warunkowego.

>[!IMPORTANT]
>Funkcja **zapamiętywania uwierzytelniania wieloskładnikowego** nie jest zgodna z funkcją **keep me signed w** usłudze AD FS, gdy użytkownicy przeprowadzają weryfikację dwuetapową dla usług AD FS za pośrednictwem serwera uwierzytelniania wieloskładnikowego platformy Azure lub rozwiązania uwierzytelniania wieloskładnikowego innej firmy.
>
>Jeśli użytkownicy **wybiorą opcję zachowaj zalogowanie się w** u usług AD FS, a także oznaczyć swoje urządzenie jako zaufane w przypadku uwierzytelniania wieloskładnikowego, użytkownik nie zostanie automatycznie zweryfikowany po upływie zapamiętywania liczby dni **uwierzytelniania wieloskładnikowego.** Usługa Azure AD żąda ponownej weryfikacji dwuetapowej, ale usługi AD FS zwracają token z oryginalnym oświadczeniem i datą uwierzytelniania wieloskładnikowego, zamiast ponownie przeprowadzać weryfikację dwuetapową. **Ta reakcja ustawia pętlę weryfikacji między usługą Azure AD i ad fs.**
>
>Funkcja **zapamiętywania uwierzytelniania wieloskładnikowego** nie jest zgodna z użytkownikami B2B i nie będzie widoczna dla użytkowników B2B podczas logowania się do zaproszonych dzierżaw.
>

### <a name="enable-remember-multi-factor-authentication"></a>Włącz zapamiętywanie uwierzytelniania wieloskładnikowego

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję**Użytkownicy** **usługi Azure Active Directory** > .
3. Wybierz pozycję **Uwierzytelnianie wieloskładnikowe**.
4. W obszarze Uwierzytelnianie wieloskładnikowe wybierz **pozycję Ustawienia usługi**.
5. Na **stronie Ustawienia usługi** **zarządzaj uwierzytelnianiem wieloskładnikowym**, wybierz opcję **Zezwalaj użytkownikom na zapamiętywanie uwierzytelniania wieloskładnikowego na zaufanych urządzeniach.**
6. Ustaw liczbę dni, aby umożliwić zaufanym urządzeniom ominięcie weryfikacji dwuetapowej. Wartość domyślna to 14 dni.
7. Wybierz **pozycję Zapisz**.

### <a name="mark-a-device-as-trusted"></a>Oznaczanie urządzenia jako zaufanego

Po włączeniu funkcji zapamiętywania uwierzytelniania wieloskładnikowego użytkownicy mogą oznaczyć urządzenie jako zaufane podczas logowania, wybierając pozycję **Nie pytaj ponownie.**

## <a name="next-steps"></a>Następne kroki

[Modyfikowanie znakowania strony logowania usługi Azure AD](../fundamentals/customize-branding.md)
