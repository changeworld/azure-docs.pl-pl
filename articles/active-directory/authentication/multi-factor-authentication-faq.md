---
title: Uwierzytelniania wieloskładnikowego platformy Azure — często zadawane pytania dotyczące usługi Azure Active Directory
description: Często zadawane pytania i odpowiedzi związane z uwierzytelnianiem wieloskładnikowym platformy Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a622245a7431058582131d9ba224ddfb676d8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425139"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Często zadawane pytania dotyczące usługi Azure Multi-Factor Authentication

To często zadawane pytania dotyczy często zadawanych pytań dotyczących uwierzytelniania wieloskładnikowego platformy Azure i korzystania z usługi uwierzytelniania wieloskładnikowego. Jest on podzielony na pytania dotyczące usługi w ogóle, modeli rozliczeń, środowiska użytkownika i rozwiązywania problemów.

## <a name="general"></a>Ogólne

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.
> 
> Licencjonowanie oparte na zużyciu nie jest już dostępne dla nowych klientów od 1 września 2018 r.
> Od 1 września 2018 r. nowi dostawcy er mogą już nie być tworzoni. Istniejący dostawcy er mogą być nadal używane i aktualizowane. Uwierzytelnianie wieloskładnikowe będzie nadal dostępne w licencjach usługi Azure AD Premium.

> [!NOTE]
> Informacje udostępnione poniżej dotyczące serwera uwierzytelniania wieloskładnikowego platformy Azure mają zastosowanie tylko do użytkowników, którzy mają już uruchomiony serwer usługi MFA.

**Pyt.: W jaki sposób serwer uwierzytelniania wieloskładnikowego platformy Azure obsługuje dane użytkowników?**

Za pomocą serwera uwierzytelniania wieloskładnikowego dane użytkownika są przechowywane tylko na serwerach lokalnych. Żadne trwałe dane użytkowników nie są przechowywane w chmurze. Gdy użytkownik wykonuje weryfikację dwuetapową, serwer uwierzytelniania wieloskładnikowego wysyła dane do usługi w chmurze uwierzytelniania wieloskładnikowego azure do uwierzytelniania. Komunikacja między serwerem uwierzytelniania wieloskładnikowego a usługą w chmurze uwierzytelniania wieloskładnikowego korzysta z protokołu SSL (Secure Sockets Layer) lub Transport Layer Security (TLS) za pomocą portu 443 wychodzącego.

Gdy żądania uwierzytelniania są wysyłane do usługi w chmurze, dane są zbierane do raportów uwierzytelniania i użycia. Pola danych zawarte w dziennikach weryfikacji dwuetapowej są następujące:

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

**P: Jakie krótkie kody SMS służą do wysyłania wiadomości SMS do moich użytkowników?**

W Stanach Zjednoczonych firma Microsoft używa następujących krótkich kodów SMS:

   * 97671
   * 69829
   * 51789
   * 99399

W Kanadzie firma Microsoft używa następujących krótkich kodów SMS:

   * 759731 
   * 673801

Firma Microsoft nie gwarantuje spójnego dostarczania przez program SMS lub voice-based Multi-Factor Authentication za pomocą tego samego numeru. W interesie naszych użytkowników firma Microsoft może w dowolnym momencie dodawać lub usuwać krótkie kody, gdy dokonujemy zmian tras w celu poprawy dostarczania wiadomości SMS. Firma Microsoft nie obsługuje krótkich kodów dla krajów/regionów poza Stanami Zjednoczonymi i Kanadą.

## <a name="billing"></a>Rozliczenia

Na większość pytań dotyczących rozliczeń można odpowiedzieć, odwołując się do [strony cennik uwierzytelniania wieloskładnikowego](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) lub dokumentacji dotyczącej [sposobu uzyskania uwierzytelniania wieloskładnikowego platformy Azure.](concept-mfa-licensing.md)

**Pyt.: Czy moja organizacja jest obciążona opłatą za wysyłanie połączeń telefonicznych i wiadomości tekstowych, które są używane do uwierzytelniania?**

Nie, opłaty nie są naliczane za pojedyncze połączenia telefoniczne lub wiadomości tekstowe wysyłane do użytkowników za pośrednictwem uwierzytelniania wieloskładnikowego platformy Azure. Jeśli używasz dostawcy usługi MFA dla uwierzytelniania, są naliczane dla każdego uwierzytelniania, ale nie dla metody używane.

Użytkownicy mogą być obciążani opłatami za połączenia telefoniczne lub wiadomości tekstowe, które otrzymują, zgodnie z ich osobistą usługą telefoniczną.

**Pyt.: Czy model rozliczeń na użytkownika pobiera ode mnie opłaty za wszystkich włączonych użytkowników, czy tylko te, które przeprowadziły weryfikację dwuetapową?**

Rozliczenia są oparte na liczbie użytkowników skonfigurowanych do używania uwierzytelniania wieloskładnikowego, niezależnie od tego, czy przeprowadzili weryfikację dwuetapową w tym miesiącu.

**Pyt.: Jak działa rozliczanie uwierzytelniania wieloskładnikowego?**

Podczas tworzenia dostawcy usługi MFA dla użytkownika lub uwierzytelniania subskrypcja platformy Azure organizacji jest rozliczana co miesiąc na podstawie użycia. Ten model rozliczeń jest podobny do sposobu, w jaki platforma Azure rozlicza się z użycia maszyn wirtualnych i witryn sieci Web.

W przypadku zakupu subskrypcji uwierzytelniania wieloskładnikowego platformy Azure organizacja płaci tylko roczną opłatę licencyjną dla każdego użytkownika. Licencje usługi MFA oraz pakiety usługi Office 365, Usługi Azure AD Premium lub Enterprise Mobility + Security są rozliczane w ten sposób. 

Dowiedz się więcej o opcjach w [aplikacji Jak uzyskać uwierzytelnianie wieloskładnikowe platformy Azure](concept-mfa-licensing.md).

**Pyt.: Czy istnieje bezpłatna wersja uwierzytelniania wieloskładnikowego platformy Azure?**

W niektórych przypadkach tak.

Uwierzytelnianie wieloskładnikowe dla administratorów platformy Azure oferuje podzbiór funkcji usługi Azure MFA bez żadnych kosztów w celu uzyskania dostępu do usług online firmy Microsoft, w tym [do portalu Azure](https://portal.azure.com) i centrum [administracyjnego usługi Microsoft 365.](https://admin.microsoft.com) Ta oferta dotyczy tylko administratorów globalnych w wystąpieniach usługi Azure Active Directory, którzy nie mają pełnej wersji usługi Azure MFA za pośrednictwem licencji usługi MFA, pakietu lub autonomicznego dostawcy opartego na zużyciu. Jeśli administratorzy korzystają z bezpłatnej wersji, a następnie zakup pełnej wersji usługi Azure MFA, wszyscy administratorzy globalni są automatycznie wyniesione do wersji płatnej.

Uwierzytelnianie wieloskładnikowe dla użytkowników usługi Office 365 oferuje podzbiór funkcji usługi Azure MFA bez żadnych kosztów w celu uzyskania dostępu do usług Office 365, w tym usługi Exchange Online i usługi SharePoint Online. Ta oferta dotyczy użytkowników, którzy mają przypisaną licencję usługi Office 365, gdy odpowiednie wystąpienie usługi Azure Active Directory nie ma pełnej wersji usługi Azure MFA za pośrednictwem licencji usługi MFA, pakietu lub autonomicznego dostawcy opartego na zużyciu.

**Pyt.: Czy moja organizacja może przełączać się między modelami rozliczeń zużycia na użytkownika i na uwierzytelnianie w dowolnym momencie?**

Jeśli organizacja kupuje usługi MFA jako autonomiczną usługę z rozliczeniami opartymi na zużyciu, podczas tworzenia dostawcy usługi MFA należy wybrać model rozliczeń. Nie można zmienić modelu rozliczeń po utworzeniu dostawcy usługi MFA. 

Jeśli dostawca usługi MFA *nie* jest połączony z dzierżawą usługi Azure AD lub połączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu ich połączenia z nowym dostawcą usługi MFA nie wpływa na uwierzytelnianie za pośrednictwem połączeń telefonicznych i wiadomości SMS, ale powiadomienia w aplikacji mobilnej przestaną działać dla wszystkich użytkowników do momentu, w którym ponownie aktywują aplikację mobilną.

Dowiedz się więcej o dostawcach usługi MFA w [programach Wprowadzenie do usługi Azure Multi-Factor Auth Provider](concept-mfa-authprovider.md).

**Pyt.: Czy moja organizacja może w dowolnym momencie przełączać się między rozliczeniami opartymi na zużyciu a subskrypcjami (model oparty na licencji)?**

W niektórych przypadkach tak.

Jeśli katalog ma dostawcę uwierzytelniania wieloskładnikowego platformy Azure *dla użytkownika,* można dodać licencje usługi MFA. Użytkownicy z licencjami nie są liczone w rozliczeniach opartych na zużycie na użytkownika. Użytkownicy bez licencji nadal można włączyć dla usługi MFA za pośrednictwem dostawcy usługi MFA. Jeśli zakup i przypisanie licencji dla wszystkich użytkowników skonfigurowanych do używania uwierzytelniania wieloskładnikowego można usunąć dostawcę uwierzytelniania wieloskładnikowego platformy Azure. Zawsze można utworzyć innego dostawcę usługi MFA dla użytkownika, jeśli masz więcej użytkowników niż licencji w przyszłości.

Jeśli katalog ma dostawcy uwierzytelniania Azure multi-factor authentication *dla każdego,* zawsze są naliczane opłaty za każde uwierzytelnianie, tak długo, jak dostawca usługi MFA jest połączony z subskrypcją. Licencje usługi MFA można przypisać użytkownikom, ale nadal będą naliczane naliczane płatności za każde żądanie weryfikacji dwuetapowej, niezależnie od tego, czy pochodzą od osoby z przypisaną licencją usługi MFA, czy nie.

**Pyt.: Czy moja organizacja musi używać i synchronizować tożsamości, aby używać uwierzytelniania wieloskładnikowego platformy Azure?**

Jeśli twoja organizacja korzysta z modelu rozliczeń opartego na zużyciu, usługa Azure Active Directory jest opcjonalna, ale nie jest wymagana. Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD, można wdrożyć tylko serwer uwierzytelniania wieloskładnikowego platformy Azure w środowisku lokalnym.

Usługa Azure Active Directory jest wymagana dla modelu licencji, ponieważ licencje są dodawane do dzierżawy usługi Azure AD podczas zakupu i przypisywania ich do użytkowników w katalogu.

## <a name="manage-and-support-user-accounts"></a>Manage and support user accounts (Zarządzanie kontami użytkowników i związana z nimi pomoc techniczna)

**P: Co powinienem powiedzieć moim użytkownikom, aby zrobili, jeśli nie otrzymają odpowiedzi na swój telefon?**

Niech użytkownicy spróbują do 5 razy w ciągu 5 minut, aby uzyskać połączenie telefoniczne lub SMS do uwierzytelnienia. Firma Microsoft używa wielu dostawców do dostarczania połączeń i wiadomości SMS. Jeśli to nie zadziała, otwórz przypadek pomocy technicznej z firmą Microsoft, aby rozwiązać dalsze problemy.

Jeśli powyższe kroki nie działają, miejmy nadzieję, wszyscy użytkownicy skonfigurowali więcej niż jedną metodę weryfikacji. Poproś ich o ponowne zalogowanie przy użyciu innej metody weryfikacji wybieranej na stronie logowania.

Można skierować użytkowników do [przewodnika rozwiązywania problemów z użytkownikiem końcowym](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**P: Co należy zrobić, jeśli jeden z moich użytkowników nie może wejść na swoje konto?**

Możesz zresetować konto użytkownika, czyniąc go, aby przejść przez proces rejestracji ponownie. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą uwierzytelniania wieloskładnikowego platformy Azure w chmurze](howto-mfa-userdevicesettings.md).

**Pyt.: Co należy zrobić, jeśli jeden z moich użytkowników traci telefon, który używa haseł aplikacji?**

Aby zapobiec nieautoryzowanemu dostępowi, usuń wszystkie hasła aplikacji użytkownika. Po użytkownik ma urządzenie zastępcze, mogą odtworzyć hasła. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą uwierzytelniania wieloskładnikowego platformy Azure w chmurze](howto-mfa-userdevicesettings.md).

**Pyt.: Co zrobić, jeśli użytkownik nie może zalogować się do aplikacji innych niż przeglądarki?**

Jeśli twoja organizacja nadal korzysta ze starszych klientów i [zezwalasz na używanie haseł aplikacji,](howto-mfa-mfasettings.md#app-passwords)użytkownicy nie mogą zalogować się do tych starszych klientów przy użyciu ich nazwy użytkownika i hasła. Zamiast tego muszą [skonfigurować hasła aplikacji](../user-help/multi-factor-authentication-end-user-app-passwords.md). Użytkownicy muszą wyczyścić (usunąć) informacje logowania, ponownie uruchomić aplikację, a następnie zalogować się przy użyciu nazwy użytkownika i *hasła aplikacji* zamiast zwykłego hasła.

Jeśli twoja organizacja nie ma starszych klientów, nie należy zezwalać użytkownikom na tworzenie haseł aplikacji.

> [!NOTE]
> Nowoczesne uwierzytelnianie dla klientów pakietu Office 2013
>
> Hasła aplikacji są niezbędne tylko w przypadku aplikacji, które nie obsługują nowoczesnego uwierzytelniania. Klienci pakietu Office 2013 obsługują nowoczesne protokoły uwierzytelniania, ale muszą być skonfigurowane. Teraz nowoczesne uwierzytelnianie jest dostępne dla każdego klienta z uruchomieniem aktualizacji z marca 2015 r. lub nowszej dla pakietu Office 2013. Aby uzyskać więcej informacji, zobacz wpis w blogu [Zaktualizowano nowoczesne uwierzytelnianie usługi Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

**P: Moi użytkownicy twierdzą, że czasami nie otrzymują wiadomości tekstowej lub przesunie się termin weryfikacji.**

Dostarczanie wiadomości SMS nie są gwarantowane, ponieważ istnieją niekontrolowane czynniki, które mogą mieć wpływ na niezawodność usługi. Czynniki te obejmują kraj/region docelowy, operatora telefonii komórkowej i siłę sygnału.

Jeśli użytkownicy często mają problemy z niezawodnym odbieraniem wiadomości tekstowych, powiedz im, aby zamiast tego korzystali z aplikacji mobilnej lub metody połączenia telefonicznego. Aplikacja mobilna może otrzymywać powiadomienia zarówno za pośrednictwem sieci komórkowej, jak i połączeń Wi-Fi. Ponadto aplikacja mobilna może generować kody weryfikacyjne, nawet jeśli urządzenie nie ma sygnału. Aplikacja Microsoft Authenticator jest dostępna dla [systemów Android,](https://go.microsoft.com/fwlink/?Linkid=825072) [IOS](https://go.microsoft.com/fwlink/?Linkid=825073)i [Windows Phone.](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)

**Pyt.: Czy mogę zmienić czas, przez jaki użytkownicy muszą wprowadzić kod weryfikacyjny z wiadomości tekstowej przed przekroczem czasu pracy systemu?**

W niektórych przypadkach tak. 

W przypadku programu SMS w jedną stronę z usługą Azure MFA Server w wersji 7.0 lub nowszej można skonfigurować ustawienie limitu czasu, ustawiając klucz rejestru. Po wysłaniu przez usługę chmury usługi MFA kod weryfikacyjny (lub jednorazowy kod dostępu) jest zwracany do serwera usługi MFA. Serwer usługi MFA domyślnie przechowuje kod w pamięci przez 300 sekund. Jeśli użytkownik nie wprowadzi kodu przed upływem 300 sekund, ich uwierzytelnianie zostanie odrzucone. Wykonaj następujące czynności, aby zmienić domyślne ustawienie limitu czasu:

1. Przejdź do strony HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Utwórz klucz rejestru DWORD o nazwie **pfsvc_pendingSmsTimeoutSeconds** i ustaw czas w sekundach, w których serwer usługi Azure MFA ma przechowywać jednorazowe kody dostępu.

>[!TIP] 
>Jeśli masz wiele serwerów usługi MFA, tylko ten, który przetworzył oryginalne żądanie uwierzytelnienia zna kod weryfikacyjny, który został wysłany do użytkownika. Gdy użytkownik wprowadzi kod, żądanie uwierzytelnienia, aby sprawdzić jego poprawność, musi zostać wysłane na ten sam serwer. Jeśli sprawdzanie poprawności kodu jest wysyłany do innego serwera, uwierzytelnianie jest odrzucane. 

Jeśli użytkownicy nie odpowiadają na sms w zdefiniowanym okresie limitu czasu, ich uwierzytelnianie zostanie odrzucone. 

W przypadku jednokierunkowego programu SMS z usługą Azure MFA w chmurze (w tym karty usług AD FS lub rozszerzenia serwera zasad sieciowych) nie można skonfigurować ustawienia limitu czasu. Usługa Azure AD przechowuje kod weryfikacyjny przez 180 sekund. 

**Pyt.: Czy można używać tokenów sprzętowych z serwerem uwierzytelniania wieloskładnikowego platformy Azure?**

Jeśli korzystasz z serwera uwierzytelniania wieloskładnikowego platformy Azure, możesz zaimportować tokeny oparte na czasie jednorazowego hasła (TOTP) innej firmy, a następnie użyć ich do weryfikacji dwuetapowej.

Tokeny ActiveIdentity, które są tokenami OATH TOTP, można użyć, jeśli umieścisz klucz tajny w pliku CSV i zaimportujesz go na serwer uwierzytelniania wieloskładnikowego platformy Azure. Tokeny OATH można używać z usługami federacyjnymi Active Directory (ADFS), uwierzytelnianiem opartym na formularzach internet information server (IIS) i usługą użytkownika zdalnego uwierzytelniania telefonicznego (RADIUS), o ile system kliencki może zaakceptować dane wejściowe użytkownika.

Tokeny OATH TOTP innych firm można importować w następujących formatach:  

- Przenośny symetryczny pojemnik na klucze (PSKC)  
- CSV, jeśli plik zawiera numer seryjny, klucz tajny w formacie Base 32 i przedział czasu  

**Pyt.: Czy można używać usługi Azure Multi-Factor Authentication Server do zabezpieczania usług terminalowych?**

Tak, ale jeśli używasz systemu Windows Server 2012 R2 lub nowszego, usługi terminalowe można zabezpieczyć tylko za pomocą bramy usług pulpitu zdalnego.

Zmiany zabezpieczeń w systemie Windows Server 2012 R2 zmieniły sposób łączenia się serwera uwierzytelniania wieloskładnikowego platformy Azure z pakietem zabezpieczeń LSA w systemie Windows Server 2012 i wcześniejszych wersjach. W przypadku wersji usług terminalowych w systemie Windows Server 2012 lub nowszym można [zabezpieczyć aplikację za pomocą uwierzytelniania systemu Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Jeśli używasz systemu Windows Server 2012 R2, potrzebujesz bramy usług pulpitu zdalnego.

**P: Skonfigurowano identyfikator obiektu wywołującego na serwerze usługi MFA, ale moi użytkownicy nadal odbierają połączenia uwierzytelniania wieloskładnikowego od anonimowego rozmówcy.**

Gdy połączenia uwierzytelniania wieloskładnikowego są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami są one kierowane przez operatora, który nie obsługuje identyfikatora dzwoniącego. Z tego powodu identyfikator wywołującego nie jest gwarantowana, mimo że system uwierzytelniania wieloskładnikowego zawsze go wysyła.

**P: Dlaczego moi użytkownicy są monitowani o zarejestrowanie swoich informacji zabezpieczających?**
Istnieje kilka powodów, dla których użytkownicy mogą zostać poproszeni o zarejestrowanie swoich informacji zabezpieczających:

- Użytkownik został włączony dla usługi MFA przez administratora w usłudze Azure AD, ale nie ma jeszcze informacji o zabezpieczeniach zarejestrowanych dla swojego konta.
- Użytkownik został włączony do samoobsługowego resetowania hasła w usłudze Azure AD. Informacje zabezpieczające pomogą im zresetować hasło w przyszłości, jeśli kiedykolwiek o tym zapomną.
- Użytkownik uzyskał dostęp do aplikacji, która ma zasady dostępu warunkowego wymagają mfa i nie został wcześniej zarejestrowany dla usługi MFA.
- Użytkownik rejestruje urządzenie w usłudze Azure AD (w tym usługi Azure AD Join), a organizacja wymaga usługi MFA do rejestracji urządzenia, ale użytkownik nie zarejestrował się wcześniej dla usługi MFA.
- Użytkownik generuje Windows Hello for Business w systemie Windows 10 (który wymaga usługi MFA) i nie został wcześniej zarejestrowany dla usługi MFA.
- Organizacja utworzyła i włączyła zasadę rejestracji usługi MFA, która została zastosowana do użytkownika.
- Użytkownik wcześniej zarejestrowany dla usługi MFA, ale wybrał metodę weryfikacji, która od tego czasu jest wyłączona przez administratora. W związku z tym użytkownik musi przejść przez rejestrację usługi MFA ponownie, aby wybrać nową metodę weryfikacji domyślnej.

## <a name="errors"></a>Errors

**Pyt.: Co należy zrobić, jeśli widzą komunikat o błędzie "Żądanie uwierzytelniania nie jest dla aktywowanego konta" podczas korzystania z powiadomień aplikacji mobilnych?**

Powiedz im, aby postępowali zgodnie z tą procedurą, aby usunąć swoje konto z aplikacji mobilnej, a następnie dodaj je ponownie:

1. Przejdź do [profilu portalu Azure](https://account.activedirectory.windowsazure.com/profile/) i zaloguj się za pomocą konta instytucji.
2. Wybierz **opcję Dodatkowa weryfikacja zabezpieczeń**.
3. Usuń istniejące konto z aplikacji mobilnej.
4. Kliknij **pozycję Konfiguruj**, a następnie postępuj zgodnie z instrukcjami, aby ponownie skonfigurować aplikację mobilną.

**Pyt.: Co należy zrobić, jeśli podczas logowania się do aplikacji bez przeglądarki użytkownicy mają komunikat o błędzie 0x800434D4L?**

Błąd 0x800434D4L występuje podczas próby zalogowania się do aplikacji nie przeglądarki zainstalowanej na komputerze lokalnym, która nie działa z kontami wymagającymi weryfikacji dwuetapowej.

Obejście tego błędu polega na konieczności osobnego konta użytkowników dla operacji związanych z administratorami i operacji innych niż administrator. Później możesz połączyć skrzynki pocztowe między kontem administratora a kontem niebędącym administratorem, aby zalogować się do programu Outlook przy użyciu konta niebędącego administratorem. Aby uzyskać więcej informacji na temat tego rozwiązania, dowiedz się, jak [dać administratorowi możliwość otwierania i wyświetlania zawartości skrzynki pocztowej użytkownika](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Następne kroki

Jeśli na twoje pytanie nie ma tutaj odpowiedzi, zostaw to w komentarzach u dołu strony. Oto kilka dodatkowych opcji uzyskania pomocy:

* Wyszukaj w bazie [wiedzy Microsoft Support Knowledge Base](https://support.microsoft.com) rozwiązania typowych problemów technicznych.
* Wyszukaj i przejrzyj pytania techniczne i odpowiedzi społeczności lub zadaj własne pytanie na [forach usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Jeśli jesteś starszym klientem PhoneFactor i masz pytania lub potrzebujesz pomocy w zresetowaniu hasła, użyj linku [resetowania hasła,](mailto:phonefactorsupport@microsoft.com) aby otworzyć sprawę pomocy technicznej.
* Skontaktuj się z pracownikiem pomocy technicznej za pośrednictwem [pomocy technicznej usługi Azure Multi-Factor Authentication Server (PhoneFactor).](https://support.microsoft.com/oas/default.aspx?prid=14947) Kontaktując się z nami, warto, jeśli możesz podać jak najwięcej informacji o swoim problemie. Informacje, które można podać, obejmują stronę, na której wystąpił błąd, określony kod błędu, określony identyfikator sesji i identyfikator użytkownika, który widział błąd.
