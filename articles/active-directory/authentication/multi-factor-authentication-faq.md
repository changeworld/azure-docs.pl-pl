---
title: Uwierzytelniania wieloskładnikowego platformy Azure — często zadawane pytania dotyczące usługi Azure Active Directory
description: Często zadawane pytania i odpowiedzi związane z uwierzytelnianiem wieloskładnikowym platformy Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271407"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Często zadawane pytania dotyczące usługi Azure Multi-Factor Authentication

To często zadawane pytania dotyczy często zadawanych pytań dotyczących uwierzytelniania wieloskładnikowego platformy Azure i korzystania z usługi uwierzytelniania wieloskładnikowego. Jest on podzielony na pytania dotyczące usługi w ogóle, modeli rozliczeń, środowiska użytkownika i rozwiązywania problemów.

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.
>
> Informacje udostępnione poniżej dotyczące serwera uwierzytelniania wieloskładnikowego platformy Azure mają zastosowanie tylko do użytkowników, którzy mają już uruchomiony serwer usługi MFA.
>
> Licencjonowanie oparte na zużyciu nie jest już dostępne dla nowych klientów od 1 września 2018 r.
> Od 1 września 2018 r. nowi dostawcy er mogą już nie być tworzoni. Istniejący dostawcy er mogą być nadal używane i aktualizowane. Uwierzytelnianie wieloskładnikowe będzie nadal dostępne w licencjach usługi Azure AD Premium.

## <a name="general"></a>Ogólne

* [W jaki sposób serwer uwierzytelniania wieloskładnikowego platformy Azure obsługuje dane użytkowników?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Jakie krótkie kody SMS służą do wysyłania wiadomości SMS do moich użytkowników?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>W jaki sposób serwer uwierzytelniania wieloskładnikowego platformy Azure obsługuje dane użytkowników?

Za pomocą serwera uwierzytelniania wieloskładnikowego dane użytkownika są przechowywane tylko na serwerach lokalnych. Żadne trwałe dane użytkowników nie są przechowywane w chmurze. Gdy użytkownik wykonuje weryfikację dwuetapową, serwer uwierzytelniania wieloskładnikowego wysyła dane do usługi w chmurze uwierzytelniania wieloskładnikowego azure do uwierzytelniania. Komunikacja między serwerem uwierzytelniania wieloskładnikowego a usługą w chmurze uwierzytelniania wieloskładnikowego korzysta z protokołu SSL (Secure Sockets Layer) lub Transport Layer Security (TLS) za pomocą portu 443 wychodzącego.

Gdy żądania uwierzytelniania są wysyłane do usługi w chmurze, dane są zbierane do raportów uwierzytelniania i użycia. Następujące pola danych są uwzględniane w dziennikach weryfikacji dwuetapowej:

* **Unikatowy identyfikator** (nazwa użytkownika lub identyfikator serwera uwierzytelniania wieloskładnikowego lokalnie)
* **Imię i nazwisko** (opcjonalnie)
* **Adres e-mail** (opcjonalnie)
* **Numer telefonu** (podczas korzystania z połączenia głosowego lub uwierzytelniania SMS)
* **Token urządzenia** (podczas korzystania z uwierzytelniania aplikacji mobilnej)
* **Tryb uwierzytelniania**
* **Wynik uwierzytelniania**
* **Nazwa serwera uwierzytelniania wieloskładnikowego**
* **Adres IP serwera uwierzytelniania wieloskładnikowego**
* **Adres IP klienta** (jeśli jest dostępny)

Pola opcjonalne można skonfigurować na serwerze uwierzytelniania wieloskładnikowego.

Wynik weryfikacji (powodzenie lub odmowa) i przyczyna, jeśli odmówiono, jest przechowywany z danymi uwierzytelniania. Te dane są dostępne w raportach uwierzytelniania i użycia.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Jakie krótkie kody SMS służą do wysyłania wiadomości SMS do moich użytkowników?

W Stanach Zjednoczonych używamy następujących krótkich kodów SMS:

* *97671*
* *69829*
* *51789*
* *99399*

W Kanadzie używamy następujących krótkich kodów SMS:

* *759731*
* *673801*

Nie ma gwarancji spójnego sms lub głosowego uwierzytelniania wieloskładnikowego szybkiego dostarczania przez ten sam numer. W interesie naszych użytkowników, możemy dodać lub usunąć krótkie kody w dowolnym momencie, jak możemy dokonać korekty trasy w celu poprawy dostarczania WIADOMOŚCI SMS. Nie obsługujemy krótkich kodów dla krajów lub regionów, oprócz Stanów Zjednoczonych i Kanady.

## <a name="billing"></a>Rozliczenia

Na większość pytań dotyczących rozliczeń można odpowiedzieć, odwołując się do [strony cennik uwierzytelniania wieloskładnikowego](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) lub dokumentacji [dotyczące wersji uwierzytelniania wieloskładnikowego platformy Azure i planów zużycia.](concept-mfa-licensing.md)

* [Czy moja organizacja jest obciążona opłatą za wysyłanie połączeń telefonicznych i wiadomości tekstowych, które są używane do uwierzytelniania?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Czy model rozliczeń na użytkownika pobiera ode mnie opłaty za wszystkich włączonych użytkowników, czy tylko te, które przeprowadziły weryfikację dwuetapową?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Jak działa rozliczanie uwierzytelniania wieloskładnikowego?](#how-does-multi-factor-authentication-billing-work)
* [Czy istnieje bezpłatna wersja uwierzytelniania wieloskładnikowego platformy Azure?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [Czy moja organizacja może przełączać się między modelami rozliczeń zużycia na użytkownika i na uwierzytelnianie w dowolnym momencie?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Czy moja organizacja może w dowolnym momencie przełączać się między rozliczeniami opartymi na zużyciu a subskrypcjami (model oparty na licencji)?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Czy moja organizacja musi używać i synchronizować tożsamości, aby używać uwierzytelniania wieloskładnikowego platformy Azure?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Czy moja organizacja jest obciążona opłatą za wysyłanie połączeń telefonicznych i wiadomości tekstowych, które są używane do uwierzytelniania?

Nie, opłaty nie są naliczane za pojedyncze połączenia telefoniczne lub wiadomości tekstowe wysyłane do użytkowników za pośrednictwem uwierzytelniania wieloskładnikowego platformy Azure. Jeśli używasz dostawcy usługi MFA dla uwierzytelniania, są naliczane dla każdego uwierzytelniania, ale nie dla metody używane.

Użytkownicy mogą być obciążani opłatami za połączenia telefoniczne lub wiadomości tekstowe, które otrzymują, zgodnie z ich osobistą usługą telefoniczną.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Czy model rozliczeń na użytkownika pobiera ode mnie opłaty za wszystkich włączonych użytkowników, czy tylko te, które przeprowadziły weryfikację dwuetapową?

Rozliczenia są oparte na liczbie użytkowników skonfigurowanych do używania uwierzytelniania wieloskładnikowego, niezależnie od tego, czy przeprowadzili weryfikację dwuetapową w tym miesiącu.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Jak działa rozliczanie uwierzytelniania wieloskładnikowego?

Podczas tworzenia dostawcy usługi MFA dla użytkownika lub uwierzytelniania subskrypcja platformy Azure organizacji jest rozliczana co miesiąc na podstawie użycia. Ten model rozliczeń jest podobny do sposobu, w jaki platforma Azure rozlicza się z użycia maszyn wirtualnych i aplikacji sieci Web.

W przypadku zakupu subskrypcji uwierzytelniania wieloskładnikowego platformy Azure organizacja płaci tylko roczną opłatę licencyjną dla każdego użytkownika. Licencje usługi MFA oraz pakiety usługi Office 365, Usługi Azure AD Premium lub Enterprise Mobility + Security są rozliczane w ten sposób.

Aby uzyskać więcej informacji, zobacz [Jak uzyskać uwierzytelnianie wieloskładnikowe platformy Azure](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Czy istnieje bezpłatna wersja uwierzytelniania wieloskładnikowego platformy Azure?

Domyślne zabezpieczenia można włączyć w warstwie Bezpłatna usługa Azure AD. W przypadku wartości domyślnych zabezpieczeń wszyscy użytkownicy są włączeni do uwierzytelniania wieloskładnikowego przy użyciu aplikacji Microsoft Authenticator. Nie ma możliwości używania wiadomości tekstowych lub weryfikacji telefonicznej z domyślnymi ustawieniami zabezpieczeń, tylko aplikacja Microsoft Authenticator.

Aby uzyskać więcej informacji, zobacz [Co to są domyślne zabezpieczenia?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Czy moja organizacja może przełączać się między modelami rozliczeń zużycia na użytkownika i na uwierzytelnianie w dowolnym momencie?

Jeśli organizacja kupuje usługi MFA jako autonomiczną usługę z rozliczeniami opartymi na zużyciu, podczas tworzenia dostawcy usługi MFA należy wybrać model rozliczeń. Nie można zmienić modelu rozliczeń po utworzeniu dostawcy usługi MFA. 

Jeśli dostawca usługi MFA *nie* jest połączony z dzierżawą usługi Azure AD lub połączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu ich połączenia z nowym dostawcą usługi MFA nie wpływa na uwierzytelnianie za pośrednictwem połączeń telefonicznych i wiadomości SMS, ale powiadomienia w aplikacji mobilnej przestaną działać dla wszystkich użytkowników do momentu, w którym ponownie aktywują aplikację mobilną.

Dowiedz się więcej o dostawcach usługi MFA w [programach Wprowadzenie do usługi Azure Multi-Factor Auth Provider](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Czy moja organizacja może w dowolnym momencie przełączać się między rozliczeniami opartymi na zużyciu a subskrypcjami (model oparty na licencji)?

W niektórych przypadkach tak.

Jeśli katalog ma dostawcę uwierzytelniania wieloskładnikowego platformy Azure *dla użytkownika,* można dodać licencje usługi MFA. Użytkownicy z licencjami nie są liczone w rozliczeniach opartych na zużycie na użytkownika. Użytkownicy bez licencji nadal można włączyć dla usługi MFA za pośrednictwem dostawcy usługi MFA. Jeśli zakup i przypisanie licencji dla wszystkich użytkowników skonfigurowanych do używania uwierzytelniania wieloskładnikowego można usunąć dostawcę uwierzytelniania wieloskładnikowego platformy Azure. Zawsze można utworzyć innego dostawcę usługi MFA dla użytkownika, jeśli masz więcej użytkowników niż licencji w przyszłości.

Jeśli twój katalog ma dostawcy uwierzytelniania Azure Multi-Factor *Authentication,* zawsze są naliczane opłaty za każde uwierzytelnianie, o ile dostawca usługi MFA jest połączony z subskrypcją. Licencje usługi MFA można przypisać użytkownikom, ale nadal będą naliczane naliczane płatności za każde żądanie weryfikacji dwuetapowej, niezależnie od tego, czy pochodzą od osoby z przypisaną licencją usługi MFA, czy nie.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>Czy moja organizacja musi używać i synchronizować tożsamości, aby używać uwierzytelniania wieloskładnikowego platformy Azure?

Jeśli twoja organizacja korzysta z modelu rozliczeń opartego na zużyciu, usługa Azure Active Directory jest opcjonalna, ale nie jest wymagana. Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD, można wdrożyć tylko serwer uwierzytelniania wieloskładnikowego platformy Azure lokalnie.

Usługa Azure Active Directory jest wymagana dla modelu licencji, ponieważ licencje są dodawane do dzierżawy usługi Azure AD podczas zakupu i przypisywania ich do użytkowników w katalogu.

## <a name="manage-and-support-user-accounts"></a>Manage and support user accounts (Zarządzanie kontami użytkowników i związana z nimi pomoc techniczna)

* [Co powinienem powiedzieć moim użytkownikom, aby nie otrzymywali odpowiedzi w telefonie?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Co zrobić, jeśli jeden z moich użytkowników nie może wejść na swoje konto?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Co należy zrobić, jeśli jeden z moich użytkowników utraci telefon używający haseł aplikacji?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Co zrobić, jeśli użytkownik nie może zalogować się do aplikacji innych niż przeglądarki?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Moi użytkownicy twierdzą, że czasami nie otrzymują wiadomości tekstowej lub przesunie się termin weryfikacji.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Czy mogę zmienić czas, przez jaki użytkownicy muszą wprowadzić kod weryfikacyjny z wiadomości tekstowej przed limitem czasu pracy systemu?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Czy mogę używać tokenów sprzętowych za pomocą serwera uwierzytelniania wieloskładnikowego platformy Azure?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Czy mogę używać serwera uwierzytelniania wieloskładnikowego platformy Azure do zabezpieczania usług terminalowych?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Skonfigurowano identyfikator dzwoniącego na serwerze usługi MFA, ale moi użytkownicy nadal odbierają połączenia uwierzytelniania wieloskładnikowego od anonimowego rozmówcy.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Dlaczego moi użytkownicy są monitowani o zarejestrowanie swoich informacji zabezpieczających?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Co powinienem powiedzieć moim użytkownikom, aby nie otrzymywali odpowiedzi w telefonie?

Niech użytkownicy spróbują do pięciu razy w ciągu 5 minut, aby uzyskać połączenie telefoniczne lub SMS do uwierzytelnienia. Firma Microsoft używa wielu dostawców do dostarczania połączeń i wiadomości SMS. Jeśli to podejście nie zadziała, otwórz przypadek pomocy technicznej, aby rozwiązać dalsze problemy.

Aplikacje zabezpieczające innych firm mogą również blokować wiadomość tekstową kodu weryfikacyjnego lub połączenie telefoniczne. Jeśli używasz aplikacji zabezpieczeń innej firmy, spróbuj wyłączyć ochronę, a następnie zażądaj innego kodu weryfikacyjnego usługi MFA.

Jeśli powyższe kroki nie działają, sprawdź, czy użytkownicy są skonfigurowani dla więcej niż jednej metody weryfikacji. Spróbuj zalogować się ponownie, ale wybierz inną metodę weryfikacji na stronie logowania.

Aby uzyskać więcej informacji, zobacz [podręcznik rozwiązywania problemów z użytkownikiem końcowym](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Co zrobić, jeśli jeden z moich użytkowników nie może wejść na swoje konto?

Możesz zresetować konto użytkownika, czyniąc go, aby przejść przez proces rejestracji ponownie. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą uwierzytelniania wieloskładnikowego platformy Azure w chmurze](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Co należy zrobić, jeśli jeden z moich użytkowników utraci telefon używający haseł aplikacji?

Aby zapobiec nieautoryzowanemu dostępowi, usuń wszystkie hasła aplikacji użytkownika. Po użytkownik ma urządzenie zastępcze, mogą odtworzyć hasła. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą uwierzytelniania wieloskładnikowego platformy Azure w chmurze](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Co zrobić, jeśli użytkownik nie może zalogować się do aplikacji innych niż przeglądarki?

Jeśli twoja organizacja nadal korzysta ze starszych klientów i [zezwalasz na używanie haseł aplikacji,](howto-mfa-mfasettings.md#app-passwords)użytkownicy nie mogą zalogować się do tych starszych klientów przy użyciu ich nazwy użytkownika i hasła. Zamiast tego muszą [skonfigurować hasła aplikacji](../user-help/multi-factor-authentication-end-user-app-passwords.md). Użytkownicy muszą wyczyścić (usunąć) informacje logowania, ponownie uruchomić aplikację, a następnie zalogować się przy użyciu nazwy użytkownika i *hasła aplikacji* zamiast zwykłego hasła.

Jeśli twoja organizacja nie ma starszych klientów, nie należy zezwalać użytkownikom na tworzenie haseł aplikacji.

> [!NOTE]
> **Nowoczesne uwierzytelnianie dla klientów pakietu Office 2013**
>
> Hasła aplikacji są niezbędne tylko w przypadku aplikacji, które nie obsługują nowoczesnego uwierzytelniania. Klienci pakietu Office 2013 obsługują nowoczesne protokoły uwierzytelniania, ale muszą być skonfigurowane. Nowoczesne uwierzytelnianie jest dostępne dla każdego klienta z uruchomieniem aktualizacji z marca 2015 r. lub nowszej dla pakietu Office 2013. Aby uzyskać więcej informacji, zobacz wpis w blogu [Zaktualizowano nowoczesne uwierzytelnianie usługi Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Moi użytkownicy twierdzą, że czasami nie otrzymują wiadomości tekstowej lub przesunie się termin weryfikacji.

Dostarczanie wiadomości SMS nie są gwarantowane, ponieważ istnieją niekontrolowane czynniki, które mogą mieć wpływ na niezawodność usługi. Czynniki te obejmują kraj lub region docelowy, operatora telefonii komórkowej i siłę sygnału.

Aplikacje zabezpieczające innych firm mogą również blokować wiadomość tekstową kodu weryfikacyjnego lub połączenie telefoniczne. Jeśli używasz aplikacji zabezpieczeń innej firmy, spróbuj wyłączyć ochronę, a następnie zażądaj innego kodu weryfikacyjnego usługi MFA.

Jeśli użytkownicy często mają problemy z niezawodnym odbieraniem wiadomości tekstowych, powiedz im, aby zamiast tego używali aplikacji Microsoft Authenticator lub metody połączenia telefonicznego. Microsoft Authenticator może otrzymywać powiadomienia zarówno za pośrednictwem połączenia komórkowego, jak i Wi-Fi. Ponadto aplikacja mobilna może generować kody weryfikacyjne, nawet jeśli urządzenie nie ma sygnału. Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) i [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Czy mogę zmienić czas, przez jaki użytkownicy muszą wprowadzić kod weryfikacyjny z wiadomości tekstowej przed limitem czasu pracy systemu?

W niektórych przypadkach tak.

W przypadku programu SMS w jedną stronę z usługą Azure MFA Server w wersji 7.0 lub nowszej można skonfigurować ustawienie limitu czasu, ustawiając klucz rejestru. Po wysłaniu przez usługę chmury usługi MFA kod weryfikacyjny (lub jednorazowy kod dostępu) jest zwracany do serwera usługi MFA. Serwer usługi MFA domyślnie przechowuje kod w pamięci przez 300 sekund. Jeśli użytkownik nie wprowadzi kodu przed upływem 300 sekund, ich uwierzytelnianie zostanie odrzucone. Wykonaj następujące czynności, aby zmienić domyślne ustawienie limitu czasu:

1. Przejdź do pozycji `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor` (Plik > Nowy > Inny).
2. Utwórz klucz rejestru **DWORD** o nazwie *pfsvc_pendingSmsTimeoutSeconds* i ustaw czas w sekundach, w których serwer usługi Azure MFA ma przechowywać jednorazowe kody dostępu.

>[!TIP]
>
> Jeśli masz wiele serwerów usługi MFA, tylko ten, który przetworzył oryginalne żądanie uwierzytelnienia zna kod weryfikacyjny, który został wysłany do użytkownika. Gdy użytkownik wprowadzi kod, żądanie uwierzytelnienia, aby sprawdzić jego poprawność, musi zostać wysłane na ten sam serwer. Jeśli sprawdzanie poprawności kodu jest wysyłany do innego serwera, uwierzytelnianie jest odrzucane.

Jeśli użytkownicy nie odpowiadają na sms w zdefiniowanym okresie limitu czasu, ich uwierzytelnianie zostanie odrzucone.

W przypadku jednokierunkowego programu SMS z usługą Azure MFA w chmurze (w tym karty usług AD FS lub rozszerzenia serwera zasad sieciowych) nie można skonfigurować ustawienia limitu czasu. Usługa Azure AD przechowuje kod weryfikacyjny przez 180 sekund.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Czy mogę używać tokenów sprzętowych za pomocą serwera uwierzytelniania wieloskładnikowego platformy Azure?

Jeśli używasz serwera uwierzytelniania wieloskładnikowego platformy Azure, możesz zaimportować tokeny oparte na czasie jednorazowego hasła (TOTP) innej firmy, a następnie użyć ich do weryfikacji dwuetapowej.

Tokeny ActiveIdentity, które są tokenami OATH TOTP, można użyć, jeśli umieścisz klucz tajny w pliku CSV i zaimportujesz go na serwer uwierzytelniania wieloskładnikowego platformy Azure. Tokeny OATH można używać z usługami federacyjnymi Active Directory (ADFS), uwierzytelnianiem opartym na formularzach internet information server (IIS) i usługą użytkownika zdalnego uwierzytelniania telefonicznego (RADIUS), o ile system kliencki może zaakceptować dane wejściowe użytkownika.

Tokeny OATH TOTP innych firm można importować w następujących formatach:  

- Przenośny symetryczny pojemnik na klucze (PSKC)
- CSV, jeśli plik zawiera numer seryjny, klucz tajny w formacie Base 32 i przedział czasu

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Czy mogę używać serwera uwierzytelniania wieloskładnikowego platformy Azure do zabezpieczania usług terminalowych?

Tak, ale jeśli używasz systemu Windows Server 2012 R2 lub nowszego, usługi terminalowe można zabezpieczyć tylko za pomocą bramy usług pulpitu zdalnego.

Zmiany zabezpieczeń w systemie Windows Server 2012 R2 zmieniły sposób łączenia się serwera uwierzytelniania wieloskładnikowego platformy Azure z pakietem zabezpieczeń LSA w systemie Windows Server 2012 i wcześniejszych wersjach. W przypadku wersji usług terminalowych w systemie Windows Server 2012 lub nowszym można [zabezpieczyć aplikację za pomocą uwierzytelniania systemu Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Jeśli używasz systemu Windows Server 2012 R2, potrzebujesz bramy usług pulpitu zdalnego.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Skonfigurowano identyfikator dzwoniącego na serwerze usługi MFA, ale moi użytkownicy nadal odbierają połączenia uwierzytelniania wieloskładnikowego od anonimowego rozmówcy.

Gdy połączenia uwierzytelniania wieloskładnikowego są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami są one kierowane przez operatora, który nie obsługuje identyfikatora dzwoniącego. Z powodu tego zachowania operatora identyfikator wywołującego nie jest gwarantowany, mimo że system uwierzytelniania wieloskładnikowego zawsze go wysyła.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Dlaczego moi użytkownicy są monitowani o zarejestrowanie swoich informacji zabezpieczających?

Istnieje kilka powodów, dla których użytkownicy mogą zostać poproszeni o zarejestrowanie swoich informacji zabezpieczających:

- Użytkownik został włączony dla usługi MFA przez administratora w usłudze Azure AD, ale nie ma jeszcze informacji o zabezpieczeniach zarejestrowanych dla swojego konta.
- Użytkownik został włączony do samoobsługowego resetowania hasła w usłudze Azure AD. Informacje zabezpieczające pomogą im zresetować hasło w przyszłości, jeśli kiedykolwiek o tym zapomną.
- Użytkownik uzyskał dostęp do aplikacji, która ma zasady dostępu warunkowego wymagają mfa i nie został wcześniej zarejestrowany dla usługi MFA.
- Użytkownik rejestruje urządzenie w usłudze Azure AD (w tym usługi Azure AD Join), a organizacja wymaga usługi MFA do rejestracji urządzenia, ale użytkownik nie został wcześniej zarejestrowany dla usługi MFA.
- Użytkownik generuje Windows Hello for Business w systemie Windows 10 (który wymaga usługi MFA) i nie został wcześniej zarejestrowany dla usługi MFA.
- Organizacja utworzyła i włączyła zasadę rejestracji usługi MFA, która została zastosowana do użytkownika.
- Użytkownik wcześniej zarejestrowany dla usługi MFA, ale wybrał metodę weryfikacji, która od tego czasu jest wyłączona przez administratora. W związku z tym użytkownik musi przejść przez rejestrację usługi MFA ponownie, aby wybrać nową metodę weryfikacji domyślnej.

## <a name="errors"></a>Errors

* [Co powinni zrobić użytkownicy, jeśli podczas korzystania z powiadomień aplikacji mobilnych jest wyświetlany komunikat o błędzie "Żądanie uwierzytelniania nie dotyczy aktywowanego konta"?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Co należy zrobić, jeśli podczas logowania się do aplikacji bez przeglądarki pojawi się komunikat o błędzie 0x800434D4L?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Co powinni zrobić użytkownicy, jeśli podczas korzystania z powiadomień aplikacji mobilnych jest wyświetlany komunikat o błędzie "Żądanie uwierzytelniania nie dotyczy aktywowanego konta"?

Poproś użytkownika o wykonanie następującej procedury usunięcia konta z usługi Microsoft Authenticator, a następnie dodaj je ponownie:

1. Przejdź do [profilu portalu Azure](https://account.activedirectory.windowsazure.com/profile/) i zaloguj się przy za pomocą konta instytucji.
2. Wybierz **opcję Dodatkowa weryfikacja zabezpieczeń**.
3. Usuń istniejące konto z aplikacji Microsoft Authenticator.
4. Kliknij **pozycję Konfiguruj**, a następnie postępuj zgodnie z instrukcjami, aby ponownie skonfigurować wystawę uwierzytelniającego firmy Microsoft.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Co należy zrobić, jeśli podczas logowania się do aplikacji bez przeglądarki pojawi się komunikat o błędzie 0x800434D4L?

Błąd *0x800434D4L* występuje podczas próby zalogowania się do aplikacji nie przeglądarki zainstalowanej na komputerze lokalnym, która nie działa z kontami wymagającymi weryfikacji dwuetapowej.

Obejście tego błędu polega na konieczności osobnego konta użytkowników dla operacji związanych z administratorami i operacji innych niż administrator. Później możesz połączyć skrzynki pocztowe między kontem administratora a kontem niebędącym administratorem, aby zalogować się do programu Outlook przy użyciu konta niebędącego administratorem. Aby uzyskać więcej informacji na temat tego rozwiązania, dowiedz się, jak [dać administratorowi możliwość otwierania i wyświetlania zawartości skrzynki pocztowej użytkownika](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Następne kroki

Jeśli na twoje pytanie nie ma odpowiedzi, dostępne są następujące opcje pomocy technicznej:

* Wyszukaj w bazie [wiedzy Microsoft Support Knowledge Base](https://support.microsoft.com) rozwiązania typowych problemów technicznych.
* Wyszukaj i przejrzyj pytania techniczne i odpowiedzi społeczności lub zadaj własne pytanie w [usłudze Azure Active Directory Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html).
* Skontaktuj się z firmą Microsoft professional za pośrednictwem [pomocy technicznej serwera uwierzytelniania wieloskładnikowego platformy Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Kontaktując się z nami, warto, jeśli możesz podać jak najwięcej informacji o swoim problemie. Informacje, które można podać, obejmują stronę, na której wystąpił błąd, określony kod błędu, określony identyfikator sesji i identyfikator użytkownika, który widział błąd.
* Jeśli jesteś starszym klientem PhoneFactor i masz pytania lub potrzebujesz pomocy [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) przy resetowaniu hasła, użyj adresu e-mail, aby otworzyć sprawę pomocy technicznej.
