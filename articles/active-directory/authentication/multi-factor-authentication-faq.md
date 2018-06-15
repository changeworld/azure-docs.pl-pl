---
title: Uwierzytelnianie wieloskładnikowe platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania i odpowiedzi dotyczące usługi Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 226400c6da5db589db6ce2f1887c3e96cf9e1f14
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869873"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Często zadawane pytania dotyczące usługi Azure Multi-Factor Authentication

Często zadawane pytania odpowiedzi na często zadawane pytania dotyczące usługi Azure Multi-Factor Authentication i korzystanie z usługi Multi-Factor Authentication. Go dzieli się na pytania dotyczące usługi ogólnie rzecz biorąc, rozliczeń modeli, możliwości użytkowników i rozwiązywania problemów.

## <a name="general"></a>Ogólne
**Pytanie: jak serwera usługi Azure Multi-Factor Authentication obsługuje dane użytkownika?**

Serwer Multi-Factor Authentication tylko na serwerach lokalnych są przechowywane dane użytkownika. Żadne trwałe dane użytkowników nie są przechowywane w chmurze. Gdy użytkownik przeprowadza weryfikację dwuetapową, serwer Multi-Factor Authentication wysyła dane do usługi w chmurze Azure Multi-Factor Authentication do uwierzytelniania. Komunikacja między aplikacji serwer Multi-Factor Authentication i usługą w chmurze usługi Multi-Factor Authentication używa protokołu Secure Sockets Layer (SSL) lub zabezpieczeń TLS (Transport Layer) za pośrednictwem portu 443 dla ruchu wychodzącego.

Podczas żądania uwierzytelniania są wysyłane do usługi w chmurze, dane są zbierane w celu uwierzytelniania i użycia raporty. Pola danych zawarte w dziennikach weryfikacji dwuetapowej są następujące:

* **Unikatowy identyfikator** (albo nazwę lub lokalnymi usługi Multi-Factor Authentication serwera identyfikator użytkownika)
* **Pierwszy i nazwisko** (opcjonalnie)
* **Adres e-mail** (opcjonalnie)
* **Numer telefonu** (w przypadku używania połączenie głosowe lub uwierzytelniania programu SMS)
* **Token urządzenia** (w przypadku używania uwierzytelniania aplikacji mobilnej)
* **Tryb uwierzytelniania**
* **Wynik uwierzytelniania**
* **Nazwa serwera usługi Multi-Factor Authentication**
* **Serwer usługi Multi-Factor Authentication IP**
* **Klient IP** (jeśli jest dostępny)

Można skonfigurować pola opcjonalne w aplikacji serwer Multi-Factor Authentication.

Wynik weryfikacji (powodzenie lub odmowę) i dlatego, jeśli odmówiono, są przechowywane wraz z danymi uwierzytelniania. Te dane są dostępne w sekcji uwierzytelnianie i raportów użycia.

**Pytanie: jakie krótkich kodów programu SMS są używane do wysyłania wiadomości SMS do użytkowników?**

W Stanach Zjednoczonych firma Microsoft używa następujących krótkich kodów SMS:

   * 97671
   * 69829
   * 51789
   * 99399

W programie Microsoft Kanada używa następujących krótkich kodów programu SMS:

   * 759731 
   * 673801

Microsoft nie gwarantuje spójne dostarczania monitu SMS lub uwierzytelniania wieloskładnikowego opartego na głos przez ten sam numer. Dla użytkowników Microsoft może dodać lub usunąć krótkich kodów w dowolnym momencie, jak możemy dostosować trasy zwiększające deliverability programu SMS. Firma Microsoft obsługuje krótkich kodów krajów poza Stanami Zjednoczonymi i Kanadą

## <a name="billing"></a>Rozliczenia
Większość pytań związanych z rozliczeniami można odpowiedzi, odnosząc się do jednego [stronie cennika usługi Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) lub dokumentację dotyczącą [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Pyt.: czy Moja organizacja naliczona opłata za wysyłanie połączenia telefoniczne i wiadomości tekstowych, które są używane do uwierzytelniania?**

Nie, możesz nie pobiera dla poszczególnych połączeń telefonicznych umieszczone lub tekst wiadomości wysyłanych do użytkowników za pomocą usługi Azure Multi-Factor Authentication. Jeśli używasz dostawcy usługi MFA dla uwierzytelniania są rozliczane podczas każdego uwierzytelniania, ale nie dla metodę.

Użytkownicy mogą naliczona opłata za połączenia telefoniczne i wiadomości tekstowych, które otrzymują, zgodnie z ich osobistych telefoniczna.

**Pytanie: czy modelu rozliczeń na użytkownika naliczają opłaty dla wszystkich aktywnych użytkowników lub tylko te, które są wykonywane weryfikację dwuetapową?**

Karta opiera się na liczbę użytkowników skonfigurowanych do używania uwierzytelniania wieloskładnikowego, niezależnie od tego, czy one przeprowadzone weryfikacji dwuetapowej miesiąca.

**Pytanie: jak działa rozliczanie uwierzytelnianie wieloskładnikowe?**

Podczas tworzenia dostawcy usługi MFA dla poszczególnych użytkowników lub uwierzytelnienia w organizacji subskrypcji platformy Azure jest rozliczane co miesiąc na podstawie użycia. Tego modelu rozliczeń jest podobny do sposobu Azure rachunków użycia maszyn wirtualnych i witryn sieci Web.

Po zakupie subskrypcji dla usługi Azure Multi-Factor Authentication organizacji tylko płaci opłata licencji dla każdego użytkownika. Licencje MFA i usługi Office 365, Azure AD Premium lub pakietu Enterprise Mobility + pakietów zabezpieczeń są rozliczane w ten sposób. 

Dowiedz się więcej na temat opcji znajduje się w [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Pytanie: istnieje już bezpłatna wersja Azure Multi-Factor Authentication?**

W niektórych przypadkach tak.

Uwierzytelnianie wieloskładnikowe dla administratorów usługi Azure oferuje podzbiór funkcji usługi Azure MFA bezpłatnie do uzyskiwania dostępu do usług online firmy Microsoft, łącznie z portali administratora platformy Azure i usługi Office 365. Ta oferta dotyczy tylko administratorzy globalni w wystąpień usługi Azure Active Directory, które nie mają pełną wersję usługi Azure MFA za pośrednictwem licencji usługi MFA, pakietu lub dostawcę autonomiczny zużycia. Jeśli administratorów bezpłatną wersję, a następnie Kup pełną wersję usługi Azure MFA, następnie wszystkim administratorom globalnym zostaną podniesione do wersji płatnej automatycznie.

Uwierzytelnianie wieloskładnikowe dla użytkowników usługi Office 365 oferuje podzbiór funkcji usługi Azure MFA bezpłatnie do uzyskiwania dostępu do usługi Office 365, w tym usługi Exchange Online i SharePoint Online. Ta oferta dotyczy użytkowników, którzy mają licencję usługi Office 365 przypisany, gdy tego odpowiednie wystąpienia usługi Azure Active Directory nie pełną wersję usługi Azure MFA za pośrednictwem licencji usługi MFA, pakietu lub dostawcę autonomiczny zużycia.

**Pytanie: czy Moja organizacja przełączać się między na użytkownika na uwierzytelnianie zużycie rozliczeń modele i w dowolnym momencie?**

Jeśli Twoja organizacja zakupi MFA jako usługi autonomicznej z rozliczania opartego na zużycie, wybierz modelu rozliczeń podczas tworzenia dostawcy usługi MFA. Nie można zmienić modelu rozliczeń, po utworzeniu dostawca usługi MFA. Jednak można usunąć dostawcy usługi MFA, a następnie utworzyć jeden z innego modelu rozliczeń.

Podczas tworzenia dostawcy usługi MFA, może być połączony z usługą Azure Active Directory (alias "dzierżawy usługi Azure AD"). W przypadku bieżącego dostawcy usługi MFA jest połączony z dzierżawy usługi Azure AD, można bezpiecznie usunąć dostawcę usługi MFA i Utwórz klucz, który jest połączony z tej samej dzierżawy usługi Azure AD. Alternatywnie, jeśli masz zakupionych dostatecznie dużo licencji MFA, Azure AD Premium lub Enterprise Mobility + Security (EMS), aby objęły one wszystkich użytkowników włączonych do usługi MFA, możesz zupełnie usunąć dostawcę usługi MFA.

W przypadku dostawcy usługi MFA *nie* połączone z dzierżawy usługi Azure AD, lub możesz połączyć nowy dostawca usługi MFA do innej usługi Azure AD dzierżawcy, użytkownika i opcje konfiguracji nie są przenoszone. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu ich połączenia z nowym dostawcą usługi MFA nie wpływa na uwierzytelnianie za pośrednictwem połączeń telefonicznych i wiadomości SMS, ale powiadomienia w aplikacji mobilnej przestaną działać dla wszystkich użytkowników do momentu, w którym ponownie aktywują aplikację mobilną.

Dowiedz się więcej o dostawców usługi MFA w [wprowadzenie do korzystania z dostawcy uwierzytelniania wieloskładnikowego Azure](concept-mfa-authprovider.md).

**Pytanie: czy Moja organizacja przełączać się między rozliczeń i subskrypcji (na podstawie licencji modelu) na podstawie zużycia w dowolnym momencie?**

W niektórych przypadkach tak.

Jeśli katalog zawiera *użytkownika* dostawcy usługi Azure Multi-Factor Authentication można dodać licencji usługi MFA. Użytkownicy mający licencje nie są zliczane w rozliczania opartego na użycie na użytkownika. Użytkownicy nie mają licencji można nadal włączone dla usługi MFA za pośrednictwem dostawcy usługi MFA. Jeśli zakupu i przypisać licencje dla wszystkich użytkowników skonfigurowane do korzystania z usługi Multi-Factor Authentication, należy usunąć dostawcę usługi Azure Multi-Factor Authentication. Zawsze można utworzyć innego dostawcy usługi MFA dla poszczególnych użytkowników, jeśli masz więcej użytkowników niż liczba licencji w przyszłości.

Jeśli katalog zawiera *na uwierzytelnianie* dostawcy usługi Azure Multi-Factor Authentication, są zawsze rozliczane podczas każdego uwierzytelniania tak długo, jak dostawca usługi MFA jest połączony z subskrypcją. Możesz przypisać licencje usługi MFA dla użytkowników, ale nadal zostaną naliczone przy każdym żądaniu weryfikacji dwuetapowej, czy pochodzi ona z ktoś z licencją MFA lub nie.

**Pytanie: czy Moja organizacja ma używać do synchronizacji tożsamości do użycia usługi Azure Multi-Factor Authentication?**

Jeśli Twoja organizacja korzysta z modelu rozliczeń na podstawie zużycia, Azure Active Directory jest opcjonalny, ale nie jest wymagane. Dostawcy usługi MFA nie jest połączony z dzierżawy usługi Azure AD, można wdrażać tylko lokalne serwera usługi Azure Multi-Factor Authentication.

Ponieważ licencje są dodawane do dzierżawy usługi Azure AD, gdy zakupu i przypisać je do użytkowników w katalogu usługi Azure Active Directory jest wymagana dla modelu licencji.

## <a name="manage-and-support-user-accounts"></a>Zarządzanie i obsługę kont użytkowników

**Pytanie: co mam powiedzieć Moi użytkownicy zrobić, jeśli nie otrzymują odpowiedź na telefon lub nie masz telefon z nimi?**

Miejmy nadzieję, że wszyscy użytkownicy skonfigurowany więcej niż jednej metody weryfikacji. Poproś ich o ponowne zalogowanie przy użyciu innej metody weryfikacji wybieranej na stronie logowania.

Można wskazać użytkowników, aby [przewodnik rozwiązywania problemów dla użytkowników końcowych](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md).


**Pytanie: co należy zrobić, jeśli jeden z użytkowników nie można pobrać ich koncie?**

Konto użytkownika można zresetować je ponownie przechodzić przez proces rejestracji. Dowiedz się więcej o [Zarządzanie ustawieniami użytkownika i urządzenia w usłudze Azure Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md).

**Pytanie: co należy zrobić, jeśli jeden z użytkowników utraci telefon jest korzystanie z haseł aplikacji?**

Aby uniemożliwić nieautoryzowany dostęp, Usuń wszystkie użytkownika hasła aplikacji. Po użytkownik ma urządzenia zastępczego, można ponownie utworzyć hasła. Dowiedz się więcej o [Zarządzanie ustawieniami użytkownika i urządzenia w usłudze Azure Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md).

**Pytanie: co zrobić, jeśli użytkownik nie może zalogować się do aplikacji niekorzystających z przeglądarki?**

Jeśli Twoja organizacja nadal korzysta starszych klientów, a [dozwolone stosowanie haseł aplikacji](howto-mfa-mfasettings.md#app-passwords), a następnie użytkownicy nie logują się do tych starszych klientów z swoją nazwę użytkownika i hasło. Zamiast tego należy [skonfigurować hasła aplikacji](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md). Użytkownikom należy wyczyścić (Usuń) informacje logowania, uruchom ponownie aplikację, a następnie zaloguj się przy użyciu swoją nazwę użytkownika i *hasła aplikacji* zamiast ich prawidłowe hasło.

Jeśli Twoja organizacja nie ma starszych klientów, należy nie zezwalaj użytkownikom na tworzenie haseł aplikacji.

> [!NOTE]
> Nowoczesne uwierzytelnianie dla klientów Office 2013
>
> Hasła aplikacji są tylko niezbędne dla aplikacji, które nie obsługują nowoczesnego uwierzytelniania. Klienci Office 2013 obsługuje protokoły nowoczesnego uwierzytelniania, ale muszą być skonfigurowane. Nowszych wersji klientów Office automatycznie obsługują protokoły nowoczesnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [anonsowania publicznej wersji zapoznawczej nowoczesnego uwierzytelniania pakietu Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Pytanie: Moi użytkownicy mówią, że czasami nie otrzymują wiadomości tekstowej, lub odpowiedzi wiadomości tekstowych dwukierunkowe, ale Weryfikacja upłynie limit czasu.**

Dostarczenie wiadomości tekstowych i otrzymania odpowiedzi w dwukierunkowe programu SMS nie ma gwarancji, ponieważ istnieją fluktuacje czynników, które mogłyby wpłynąć na niezawodność usługi. Czynniki te obejmują kraju przeznaczenia, operatora telefon komórkowy i siła sygnału.

Jeżeli użytkownicy często występują problemy z niezawodnie odbieranie wiadomości tekstowych, poinformuj go, aby zamiast tego użyj metody przenośnych aplikacji lub połączeń telefonicznych. Aplikacja mobilna może odbierać powiadomienia zarówno za pośrednictwem połączenia sieci Wi-Fi i sieci komórkowej. Ponadto aplikacji mobilnej można generować kodów weryfikacyjnych nawet wtedy, gdy dla urządzenia nie ma w ogóle nie ma sygnału. Jest dostępna dla aplikacji Microsoft Authenticator [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073), i [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Należy użyć wiadomości tekstowych, zaleca się przy użyciu jednokierunkowego SMS zamiast dwukierunkowe programu SMS, kiedy to możliwe. Jednokierunkowe SMS są bardziej niezawodne i uniemożliwia użytkownikom naliczania globalne opłat programu SMS z odpowiadaniu na wiadomość tekstowa, która została wysłana z innego kraju.

**Pytanie: czy można zmienić czas Moi użytkownicy muszą wprowadzić kod weryfikacji z wiadomość SMS, zanim upłynie limit czasu systemu?**

W niektórych przypadkach tak. 

Dla jednokierunkowe SMS z serwera usługi Azure MFA v7.0 lub nowszym można skonfigurować limit czasu ustawienie przez ustawienie klucza rejestru. Po usługi MFA w chmurze wysyła wiadomość SMS, zwracany jest kod weryfikacyjny (lub jednorazowy kod dostępu) do serwera usługi MFA. Serwer usługi MFA przechowuje kod w pamięci przez 300 sekund domyślnie. Jeśli użytkownik nie wprowadź kod, zanim upłynie 300 sekund, ich uwierzytelnianie zostanie odrzucone. Aby zmienić domyślne ustawienie limitu czasu, wykonaj następujące kroki:

1. Przejdź do HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Utwórz klucz rejestru DWORD o nazwie **pfsvc_pendingSmsTimeoutSeconds** i ustaw czas w sekundach, które mają serwera usługi Azure MFA do przechowywania haseł jednorazowych.

>[!TIP] 
>Jeśli masz wiele serwerów usługi MFA, tylko ten, który oryginalnego żądania uwierzytelniania przetwarzane zna kod weryfikacyjny, który został wysłany do użytkownika. Gdy użytkownik musi wprowadzić kod, należy wysłać żądanie uwierzytelnienia do zweryfikowania go do tego samego serwera. Jeśli Weryfikacja kodu jest przesyłany do innego serwera, uwierzytelnianie zostanie odrzucone. 

Dla dwukierunkowe SMS z serwera usługi Azure MFA można skonfigurować ustawienia limitu czasu w portalu zarządzania usługi MFA. Jeśli użytkownicy nie odpowiada SMS w zdefiniowanych limicie czasu, odmówiono uwierzytelniania. 

Dla jednokierunkowe SMS za pomocą usługi Azure MFA w chmurze (w tym adapter AD FS lub rozszerzenie serwera zasad sieciowych) nie można skonfigurować ustawienia limitu czasu. Usługi Azure AD przechowuje kod weryfikacyjny 180 sekund. 

**Pytanie: czy można używać tokeny sprzętowe z serwera usługi Azure Multi-Factor Authentication?**

Jeśli korzystasz z serwera usługi Azure Multi-Factor Authentication, można zaimportować tokeny na podstawie czasu, jednorazowe hasła (TOTP) otwarte uwierzytelnianie (OATH) innej firmy, a następnie używać ich na potrzeby weryfikacji dwuetapowej.

Możesz użyć tokenów ActiveIdentity, które są tokenami OATH TOTP, jeśli umieścić klucz tajny w pliku CSV i zaimportować do serwera usługi Azure Multi-Factor Authentication. Można użyć tokenów OATH z Active Directory Federation Services (ADFS), uwierzytelnianie oparte na formularzach Internet Information Server (IIS) i usługi usługi użytkowników zdalnego uwierzytelniania (RADIUS), tak długo, jak systemu klienta może akceptować dane wejściowe użytkownika.

Możesz zaimportować tokeny OATH TOTP innych firm z następujących formatów:  

- Przenośne kontener klucza symetrycznego (PSKC)  
- CSV, jeśli plik zawiera numer seryjny, klucz tajny w formacie Base-32 i przedział czasu  

**Pytanie: czy można użyć serwera usługi Azure Multi-Factor Authentication do zabezpieczania usług terminalowych?**

Tak, ale jeśli używasz systemu Windows Server 2012 R2 lub później tylko zabezpieczania usług terminalowych przy użyciu bramy usług pulpitu zdalnego (RD Gateway).

Zmiany zabezpieczeń w systemie Windows Server 2012 R2 zmienić sposób serwera usługi Azure Multi-Factor Authentication nawiązuje połączenie do pakietu zabezpieczeń urzędu zabezpieczeń lokalnych (LSA) w systemie Windows Server 2012 i starszych wersji. W przypadku wersji usług terminalowych w systemie Windows Server 2012 lub starszym, możesz [zabezpieczenia aplikacji przy użyciu uwierzytelniania systemu Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Jeśli korzystasz z systemu Windows Server 2012 R2, należy bramy usług pulpitu zdalnego.

**Pytanie: czy mogę skonfigurować identyfikator wywołującego na serwerze usługi MFA, ale Moi użytkownicy nadal otrzymywać połączeń usługi Multi-Factor Authentication anonimowego obiektu wywołującego.**

Podczas wywoływania usługi Multi-Factor Authentication za pośrednictwem publicznej sieci telefonicznej, czasami routingu operatora, który nie obsługuje identyfikatora obiektu wywołującego. W związku z tym identyfikator wywołującego nie jest gwarantowana, mimo że zawsze wysyła system Multi-Factor Authentication.

**Pytanie: Dlaczego Moi użytkownicy są monit, aby zarejestrować swoje informacje o zabezpieczeniach?**
Istnieje kilka przyczyn, czy użytkownicy można jednak monitowany o zarejestrowanie swoich informacji o zabezpieczeniach:

- Użytkownik został włączony dla usługi MFA przez administratora w usłudze Azure AD, ale nie ma informacji o zabezpieczeniach jeszcze zarejestrowana dla swojego konta.
- Użytkownik został włączony dla własnym resetowania haseł w usłudze Azure AD. Informacje dotyczące zabezpieczeń może pomóc w ich resetowania hasła w przyszłości, jeśli ich zapomniane go.
- Użytkownikowi dostęp do aplikacji, która ma zasady dostępu warunkowego, aby wymagać uwierzytelniania MFA i nie wcześniej zarejestrowane w usłudze MFA.
- Użytkownik rejestruje urządzenia z usługą Azure AD (w tym Azure AD Join), Twoja organizacja wymaga usługi MFA dla rejestracji urządzeń, a użytkownik nie został wcześniej zarejestrowany dla usługi MFA.
- Użytkownik generuje Windows Hello dla firm w systemie Windows 10, (które wymagają usługi MFA) i nie wcześniej zarejestrowane w usłudze MFA.
- Organizacja ma utworzone i włączone zasady rejestracji usługi MFA, która została zastosowana do użytkownika.
- Użytkownik wcześniej zarejestrowane w usłudze MFA, ale wybrano metodę weryfikacji, ponieważ wyłączonego administrator. Użytkownik musi przejść w związku z tym rejestracji usługi MFA ponownie, aby wybrać nowy domyślną metodę weryfikacji.


## <a name="errors"></a>Błędy
**Pytanie: co należy zrobić użytkownicy, jeśli wyświetlany jest komunikat "żądanie uwierzytelnienia nie jest dotyczy uaktywnionego konta", używając powiadomienia z aplikacji mobilnej?**

Poinformuj go, aby wykonać tę procedurę, aby usunąć swoje konto z aplikacji mobilnej, a następnie dodaj go ponownie:

1. Przejdź do [profilu portalu Azure](https://account.activedirectory.windowsazure.com/profile/) i zaloguj się przy użyciu konta organizacyjnego.
2. Wybierz **dodatkowej weryfikacji zabezpieczeń**.
3. Usunięcia istniejącego konta z poziomu aplikacji mobilnej.
4. Kliknij przycisk **Konfiguruj**, a następnie postępuj zgodnie z instrukcjami, aby ponownie skonfigurować aplikację mobilną.

**Pytanie: co należy zrobić użytkownicy, jeśli widzą komunikat o błędzie 0x800434D4L podczas logowania do aplikacji korzystających z przeglądarki?**

Błąd 0x800434D4L występuje, gdy próbują zalogować się w przypadku aplikacji korzystających z przeglądarki zainstalowane na komputerze lokalnym, który nie działa z konta, które wymagają weryfikacji dwuetapowej.

Obejście dla tego błędu jest osobnym użytkowników kont związanych z administratora i operacje bez uprawnień administratora. Później możesz połączyć skrzynek pocztowych między konta administratora i konta bez uprawnień administratora, aby zalogować się do programu Outlook przy użyciu konta bez uprawnień administratora. Aby uzyskać więcej informacji o tym rozwiązaniu, Dowiedz się, jak [przybliżyć możliwość otwierania i przeglądania zawartości skrzynki pocztowej użytkownika](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie jest tutaj odpowiedzi na pytanie, pozostaw tę wartość w komentarze u dołu strony. Lub, poniżej przedstawiono kilka dodatkowych opcji uzyskiwanie pomocy:

* Wyszukiwanie [wiedzy pomocy technicznej firmy Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) dla rozwiązania typowych problemów technicznych.
* Wyszukaj i Przeglądaj pytania i odpowiedzi od społeczności lub Zadaj pytanie w [fora usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Jeśli jesteś starszej wersji klienta PhoneFactor i masz pytania lub potrzebujesz pomocy resetowania hasła, użyj [resetowania hasła](mailto:phonefactorsupport@microsoft.com) łącze, aby otworzyć sprawę pomocy technicznej.
* Skontaktuj się z pracownikiem pomocy technicznej za pośrednictwem [obsługi serwera usługi Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Podczas kontaktowania się z nami, jest przydatne, jeśli te informacje mogą obejmować o problem jak to możliwe. Informacje, które można podać obejmują strony, na którym był wyświetlany błąd, kod błędu identyfikator określonej sesji i identyfikator użytkownika, który był wyświetlany błąd.
