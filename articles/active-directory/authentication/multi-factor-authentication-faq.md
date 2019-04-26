---
title: Uwierzytelnianie wieloskładnikowe systemu Azure — często zadawane pytania — usługa Azure Active Directory
description: Często zadawane pytania i odpowiedzi dotyczące usługi Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0277180f0d768bbcc83a0d8d05fbc4b8b25301e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357481"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Często zadawane pytania dotyczące usługi Azure Multi-Factor Authentication

Tych często zadawanych PYTAŃ, odpowiedzi na często zadawane pytania dotyczące usługi Azure Multi-Factor Authentication i przy użyciu usługi Multi-Factor Authentication. Jego jest podzielony na pytania dotyczące usługi ogólnie rzecz biorąc, rozliczenia modelami, środowiska użytkowników i rozwiązywania problemów.

## <a name="general"></a>Ogólne

**Pyt.: Jak serwer usługi Azure Multi-Factor Authentication obsługuje dane użytkownika?**

Z serwera Multi-Factor Authentication dane użytkownika są przechowywane tylko na serwerach lokalnych. Żadne trwałe dane użytkowników nie są przechowywane w chmurze. Gdy użytkownik przeprowadza weryfikację dwuetapową, serwer usługi Multi-Factor Authentication wysyła dane do usługi Azure Multi-Factor Authentication w chmurze do uwierzytelniania. Komunikację między serwerem Multi-Factor Authentication i usługi Multi-Factor Authentication w chmurze używa protokołu Secure Sockets Layer (SSL) lub zabezpieczeń TLS (Transport Layer) za pośrednictwem portu 443 wychodzących.

Podczas żądania uwierzytelniania są wysyłane do usługi w chmurze, dane są zbierane do uwierzytelniania i użycie raportów. Pola danych znajdujące się w dziennikach weryfikacji dwuetapowej, są następujące:

* **Unikatowy identyfikator** (albo nazwa użytkownika lub lokalnej usługi Multi-Factor Authentication uwierzytelnianie serwera identyfikator)
* **Pierwszy i nazwisko** (opcjonalnie)
* **Adres e-mail** (opcjonalnie)
* **Numer telefonu** (podczas korzystania z uwierzytelniania za pomocą połączenia głosowego lub wiadomości SMS)
* **Token urządzenia** (w przypadku używania uwierzytelniania aplikacji mobilnej)
* **Tryb uwierzytelniania**
* **Wynik uwierzytelniania**
* **Nazwa serwera usługi Multi-Factor Authentication**
* **Serwer Multi-Factor Authentication adresu IP**
* **Klient IP** (jeśli jest dostępny)

Opcjonalne pola można skonfigurować w serwer usługi Multi-Factor Authentication.

Wynik weryfikacji (powodzenie lub odmowę) i przyczynę, jeśli go nie powiodło się, są przechowywane dane uwierzytelniania. Te dane są dostępne w sekcji uwierzytelnianie i raporty użycia.

**Pyt.: Jakie krótkich kodów programu SMS są używane do wysyłania wiadomości SMS do moich użytkowników?**

W Stanach Zjednoczonych firmy Microsoft są używane następujące kody krótkie wiadomości SMS:

   * 97671
   * 69829
   * 51789
   * 99399

W Kanadzie, firma Microsoft używa następujących krótkich kodów SMS:

   * 759731 
   * 673801

Microsoft nie gwarantuje spójne dostarczania monitu wiadomości SMS lub uwierzytelniania wieloskładnikowego opartego na głos przez ten sam numer. W celu poprawy naszych użytkowników firmy Microsoft można dodać lub usunąć krótkich kodów w dowolnym momencie, jak możemy dostosować trasy zwiększyć skuteczność programu SMS. Firma Microsoft obsługuje krótkie kody krajów poza USA i Kanady

## <a name="billing"></a>Rozliczenia

Większość pytania dotyczące rozliczeń można uzyskać, odwołując się do jednej [stronie cennika usługi Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) lub dokumentacji dotyczącej [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Pyt.: Jest Moja organizacja naliczona opłata za wysyłanie połączeń telefonicznych i wiadomości tekstowych, które są używane do uwierzytelniania?**

Nie, użytkownik nie jest obciążana za poszczególne połączenia telefoniczne lub wiadomości SMS komunikaty wysyłane do użytkowników za pomocą usługi Azure Multi-Factor Authentication. Jeśli używasz dostawcy usługi MFA na uwierzytelnienie, opłaty są naliczane podczas każdego uwierzytelniania, ale nie dla metodę.

Użytkownicy mogą opłata za połączenia telefoniczne lub wiadomości SMS, które otrzymują, zgodnie z ich osobistych smartfona.

**Pyt.: Model rozliczania na użytkownika opłaty za dla wszystkich aktywnych użytkowników lub tylko tych, które są wykonywane weryfikację dwuetapową?**

Rozliczenia jest oparty na liczbie użytkowników skonfigurowanych do używania uwierzytelniania wieloskładnikowego, niezależnie od tego, czy są wykonywane weryfikacji dwuetapowej w danym miesiącu.

**Pyt.: Jak działa rozliczanie usługi uwierzytelnianie wieloskładnikowe?**

Po utworzeniu dostawcy usługi MFA na użytkownika lub wg uwierzytelnienia organizacji subskrypcji platformy Azure jest rozliczana co miesiąc na podstawie użycia. Ten model rozliczeń jest podobny do sposobu w platformie Azure są naliczane za użycie maszyn wirtualnych i witryn sieci Web.

Kupując subskrypcję dla usługi Azure Multi-Factor Authentication, Twoja organizacja płaci tylko opłatą roczną licencji dla każdego użytkownika. Licencji MFA i usługi Office 365, Azure AD Premium lub Enterprise Mobility + Security pakiety są rozliczane w ten sposób. 

Dowiedz się więcej informacji na temat opcji [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Pyt.: Czy istnieje bezpłatna wersja usługi Azure Multi-Factor Authentication?**

W niektórych przypadkach tak.

Uwierzytelnianie wieloskładnikowe dla administratorów systemu Azure oferuje podzestaw funkcji usługi Azure MFA bez opłat, aby uzyskać dostęp do usług online firmy Microsoft, w tym [witryny Azure portal](https://portal.azure.com) i [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com). Ta oferta dotyczy tylko administratorzy globalni w wystąpieniach usługi Azure Active Directory, które nie mają pełną wersję usługi Azure MFA za pomocą licencji usługi MFA, pakietem lub dostawcy na podstawie użycia autonomicznego. Jeśli administratorom korzystać z bezpłatnej wersji, a następnie zakupić pełną wersję usługi Azure MFA, następnie wszystkich administratorów globalnych zostaną podniesione do wersji płatnej automatycznie.

Uwierzytelnianie wieloskładnikowe dla użytkowników usługi Office 365 oferuje podzestaw funkcji usługi Azure MFA bez opłat, aby uzyskać dostęp do usług Office 365, w tym usługi Exchange Online i SharePoint Online. Ta oferta ma zastosowanie do użytkowników, którzy mają licencję usługi Office 365 przypisane, gdy tego odpowiednie wystąpienia usługi Azure Active Directory nie ma pełną wersję usługi Azure MFA za pomocą licencji usługi MFA, pakietem lub dostawcy na podstawie użycia autonomicznego.

**Pyt.: Moja organizacja przełączać się między na użytkownika i wg uwierzytelnienia modelami rozliczeń zużycia w dowolnym momencie?**

Jeśli Twoja organizacja zakupi uwierzytelnianie wieloskładnikowe jako usługa autonomiczna z rozliczeniem na podstawie użycia, możesz wybrać model rozliczeń, po utworzeniu dostawcy usługi MFA. Model rozliczeń nie można zmienić po utworzeniu dostawcy usługi MFA. Można jednak usunąć dostawcę usługi MFA i następnie utworzyć go za pomocą innego modelu rozliczeń.

Po utworzeniu dostawcy usługi MFA może być połączony z usługą Azure Active Directory (zwane również "dzierżawa usługi Azure AD"). Jeśli bieżący dostawca usługi MFA jest połączony z dzierżawą usługi Azure AD, możesz bezpiecznie usunąć dostawcę usługi MFA i utwórz je, który jest połączony z tą samą dzierżawą usługi Azure AD. Alternatywnie, jeśli masz zakupionych dostatecznie dużo licencji MFA, Azure AD Premium lub Enterprise Mobility + Security (EMS), aby objęły one wszystkich użytkowników włączonych do usługi MFA, możesz zupełnie usunąć dostawcę usługi MFA.

Jeśli dostawca usługi MFA *nie* połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA do innej usługi Azure AD dzierżawcy, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu ich połączenia z nowym dostawcą usługi MFA nie wpływa na uwierzytelnianie za pośrednictwem połączeń telefonicznych i wiadomości SMS, ale powiadomienia w aplikacji mobilnej przestaną działać dla wszystkich użytkowników do momentu, w którym ponownie aktywują aplikację mobilną.

Dowiedz się więcej na temat dostawców usługi MFA w [wprowadzenie do dostawcy usługi Azure Multi-Factor Auth](concept-mfa-authprovider.md).

**Pyt.: Moja organizacja przełączać się między Pomoc dotycząca rozliczeń i subskrypcji (na podstawie licencji model) na podstawie użycia w dowolnym momencie?**

W niektórych przypadkach tak.

Jeśli katalog zawiera *użytkownika* dostawcy usługi Azure Multi-Factor Authentication można dodać licencji MFA. Użytkownicy mający licencje nie są liczone w rozliczania na użytkownika na podstawie użycia. Nadal można włączyć użytkowników bez licencji na usługę MFA za pośrednictwem dostawcy usługi MFA. Jeśli zakupu i przypisywania licencji dla wszystkich użytkowników skonfigurowane do używania uwierzytelniania wieloskładnikowego, należy usunąć dostawcę usługi Azure Multi-Factor Authentication. Zawsze możesz utworzyć innego dostawcy usługi MFA na użytkownika, jeśli masz więcej użytkowników niż liczba licencji w przyszłości.

Jeśli katalog zawiera *na uwierzytelnienie* dostawcy usługi Azure Multi-Factor Authentication, są zawsze rozliczane podczas każdego uwierzytelniania tak długo, jak dostawca usługi MFA jest połączone z subskrypcją. Możesz przypisać licencje usługi MFA dla użytkowników, ale nadal będzie naliczana dla każdego żądania weryfikacji dwuetapowej, czy pochodzi ona z ktoś z licencją usługi MFA przypisane, czy nie.

**Pyt.: Moja organizacja ma używać do synchronizacji tożsamości do użycia usługi Azure Multi-Factor Authentication?**

Jeśli Twoja organizacja korzysta z modelu rozliczeń na podstawie użycia, usługi Azure Active Directory jest opcjonalny, ale nie jest wymagane. Dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD, można wdrażać tylko serwera usługi Azure Multi-Factor Authentication w środowisku lokalnym.

Usługa Azure Active Directory jest wymagana dla modelu licencji, ponieważ licencje są dodawane do dzierżawy usługi Azure AD, gdy zakupu i przypisać je do użytkowników w katalogu.

## <a name="manage-and-support-user-accounts"></a>Zarządzanie i obsługę kont użytkowników

**Pyt.: Co mam powiedzieć Moi użytkownicy należy zrobić, jeśli ich nie otrzymasz odpowiedzi na telefon?**

Ma podejmować maksymalnie 5 razy w ciągu 5 minut nawiązać połączenia telefonicznego lub wiadomości SMS dla uwierzytelniania użytkowników. Firma Microsoft używa wielu dostawców dostarczania rozmowy i wiadomości SMS. Jeśli to nie rozwiąże problemu Otwórz zgłoszenie do pomocy technicznej firmy Microsoft, aby kontynuować rozwiązywanie.

Jeśli powyższe kroki nie działają miejmy nadzieję wszyscy użytkownicy skonfigurowani w więcej niż jednej metody weryfikacji. Poproś ich o ponowne zalogowanie przy użyciu innej metody weryfikacji wybieranej na stronie logowania.

Można wskazać użytkownikom [przewodnik rozwiązywania problemów dla użytkowników końcowych](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**Pyt.: Co należy zrobić, jeśli jeden z użytkowników nie można pobrać do swojego konta?**

Konto użytkownika można zresetować je ponownie przejść przez proces rejestracji. Dowiedz się więcej o [Zarządzanie ustawieniami użytkowników i urządzeń za pomocą usługi Azure Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md).

**Pyt.: Co należy zrobić, jeśli jeden z moich użytkowników utraci telefonu, który jest korzystanie z haseł aplikacji?**

Aby uniemożliwić nieautoryzowany dostęp, należy usunąć wszystkich użytkowników, hasła aplikacji. Po użytkownik ma urządzenie zastępcze, można ponownie utworzyć hasła. Dowiedz się więcej o [Zarządzanie ustawieniami użytkowników i urządzeń za pomocą usługi Azure Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md).

**Pyt.: Co zrobić, jeśli użytkownik nie może zalogować się do aplikacji niekorzystających z przeglądarki?**

Jeśli Twoja organizacja nadal używa starszych klientów, a [można było używać hasła aplikacji](howto-mfa-mfasettings.md#app-passwords), a następnie użytkownicy nie logują się do tych starszych klientów przy użyciu nazwy użytkownika i hasła. Zamiast tego muszą [skonfigurować hasła aplikacji](../user-help/multi-factor-authentication-end-user-app-passwords.md). Użytkownicy, należy wyczyścić (Usuń) informacje logowania, uruchom ponownie aplikację, a następnie zaloguj się przy użyciu swoją nazwę użytkownika i *hasła aplikacji* zamiast regularnego hasła.

Jeśli Twoja organizacja nie ma starszych klientów, nie należy zezwalać użytkownikom na tworzenie haseł aplikacji.

> [!NOTE]
> Nowoczesne uwierzytelnianie dla klientów pakietu Office 2013
>
> Hasła aplikacji są tylko niezbędnych dla aplikacji, które nie obsługują nowoczesnego uwierzytelniania. Klienci pakietu Office 2013 obsługuje protokoły nowoczesnego uwierzytelniania, ale muszą być skonfigurowane. Teraz nowoczesnego uwierzytelniania są dostępne dla każdego klienta z aktualizacją update marca 2015 lub nowszego, pakietu Office 2013. Aby uzyskać więcej informacji, zobacz [pakietu Office 2013 publicznej wersji nowoczesnego uwierzytelniania ogłosiła](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Pyt.: Moi użytkownicy mówią, że czasami nie otrzymasz wiadomość SMS lub ich odpowiedzi na wiadomości tekstowe dwukierunkowe, ale Weryfikacja upłynie limit czasu.**

Dostarczanie wiadomości tekstowych i otrzymania odpowiedzi w dwukierunkowa wiadomość SMS nie ma gwarancji, ponieważ istnieją fluktuacje czynniki, które mogłyby wpłynąć na niezawodność usługi. Czynniki te obejmują kraju przeznaczenia, operator telefonii komórkowej i siła sygnału.

Jeśli użytkownicy często mają problemy z niezawodne odbieranie wiadomości SMS, należy poinformować ich, aby zamiast tego użyj przenośnych metody połączeń telefonicznych lub aplikacji. Aplikacja mobilna może otrzymywać powiadomienia, zarówno za pośrednictwem połączenia sieci Wi-Fi i sieci komórkowej. Ponadto aplikacja mobilna może generować kody weryfikacyjne nawet wtedy, gdy urządzenie nie ma w ogóle Brak sygnału. Aplikacja Microsoft Authenticator jest dostępna dla [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073), i [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

Jeśli musisz użyć wiadomości SMS, zaleca się przy użyciu jednokierunkowa wiadomość SMS, a nie dwukierunkowa wiadomość SMS, gdy jest to możliwe. Jednokierunkowa wiadomość SMS jest bardziej niezawodna i uniemożliwia użytkownikom naliczane globalnego programu SMS z odpowiadaniu na wiadomość tekstową, która została wysłana z innego kraju.

**Pyt.: Czy można zmienić czas Moi użytkownicy muszą wprowadzić kod weryfikacyjny z wiadomości SMS, zanim upłynie limit czasu systemu?**

W niektórych przypadkach tak. 

Jednokierunkowa wiadomość SMS, z serwera usługi Azure MFA 7.0 lub nowszego, aby uzyskać można skonfigurować limit czasu ustawienie przez ustawienie klucza rejestru. Po usługi MFA w chmurze, wysyła wiadomość SMS, zwracana jest kod weryfikacyjny (lub jednorazowy kod dostępu) do serwera MFA. Serwer MFA przechowuje kod w pamięci przez 300 sekund domyślnie. Jeśli użytkownik nie wprowadź kod, aby przeszły 300 sekund, ich uwierzytelnianie zostanie odrzucone. Aby zmienić domyślne ustawienie limitu czasu, wykonaj następujące kroki:

1. Przejdź do HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Utwórz klucz rejestru typu DWORD o nazwie **pfsvc_pendingSmsTimeoutSeconds** i ustawić godzinę w ciągu kilku sekund ma serwer usługi Azure MFA do przechowywania haseł jednorazowych.

>[!TIP] 
>Jeśli masz wielu serwerów MFA, tylko ten, który oryginalnego żądania uwierzytelniania przetwarzane wie, że kod weryfikacyjny, który został wysłany do użytkownika. Gdy użytkownik wprowadzi kod, żądania uwierzytelniania, aby zweryfikować, czy należy wysyłane na ten sam serwer. Jeśli sprawdzanie poprawności kodu są wysyłane na inny serwer, uwierzytelnianie zostanie odrzucone. 

Dwukierunkowa wiadomość SMS z serwerem Azure MFA, aby uzyskać w portalu zarządzania usługi MFA można skonfigurować ustawienia limitu czasu. Jeśli użytkownicy nie odpowiada na wiadomość SMS, przed upływem limitu czasu zdefiniowanego, odmówiono uwierzytelniania. 

Aby uzyskać jednokierunkowa wiadomość SMS z usługą Azure MFA w chmurze (w tym adaptera AD FS lub rozszerzenia serwera zasad sieciowych) nie można skonfigurować ustawienia limitu czasu. Usługa Azure AD przechowuje kod weryfikacyjny 180 sekund. 

**Pyt.: Przy użyciu serwera Azure Multi-Factor Authentication można używać tokenów sprzętu?**

Jeśli używasz usługi Azure Multi-Factor Authentication importowanie tokenów na podstawie czasu, jednorazowe hasła (TOTP) otwarte uwierzytelnianie (OATH) innych firm, a następnie używać ich do weryfikacji dwuetapowej.

Można użyć tokenów ActiveIdentity, które są tokeny OATH TOTP, po umieszczeniu klucza tajnego w pliku CSV i zaimportować do usługi Azure Multi-Factor Authentication. Można użyć tokenów OATH z Active Directory Federation Services (ADFS), uwierzytelnianie oparte na formularzach Internet Information Server (IIS) i serwera usługi użytkowników zdalnego uwierzytelniania (RADIUS), tak długo, jak system klienta może akceptować dane wejściowe użytkownika.

Możesz zaimportować tokeny OATH TOTP innych firm przy użyciu następujących formatów:  

- Przenośne kontener klucza symetrycznego (PSKC)  
- CSV, jeśli plik zawiera numer seryjny, klucz tajny w formacie Base-32 oraz przedział czasu  

**Pyt.: Czy można użyć serwera usługi Azure Multi-Factor Authentication, aby zabezpieczyć usługi terminalowe?**

Tak, ale jeśli używasz systemu Windows Server 2012 R2 lub nowszym tylko można zabezpieczyć usług terminalowych przy użyciu bramy usług pulpitu zdalnego (RD Gateway).

Zmiany zabezpieczeń w systemie Windows Server 2012 R2 zmienić, jak serwer usługi Azure Multi-Factor Authentication łączy się pakiet zabezpieczeń urzędu zabezpieczeń lokalnych (LSA) w systemie Windows Server 2012 i wcześniejszych wersji. W przypadku wersji usług terminalowych w systemie Windows Server 2012 lub starszym, może [zabezpieczyć aplikację przy użyciu uwierzytelniania Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Jeśli używasz systemu Windows Server 2012 R2, potrzebujesz bramy usług pulpitu zdalnego.

**Pyt.: Po skonfigurowaniu na serwerze MFA identyfikator wywołującego, ale Moi użytkownicy nadal otrzymywać połączeń usługi Multi-Factor Authentication z anonimowy obiekt wywołujący.**

Podczas wywoływania usługi Multi-Factor Authentication za pośrednictwem publicznej sieci telefonicznej, czasami one są przesyłane za pośrednictwem operatora, który nie obsługuje identyfikatora obiektu wywołującego. W związku z tym identyfikator wywołującego nie jest gwarantowane, mimo że zawsze wysyła system Multi-Factor Authentication.

**Pyt.: Dlaczego moja użytkownicy są wyświetlaniu monitu o zarejestrowanie swoich informacji o zabezpieczeniach?**
Istnieje kilka przyczyn, że użytkownicy mogą monit o zarejestrowanie swoich informacji o zabezpieczeniach:

- Użytkownik został włączony dla uwierzytelniania Wieloskładnikowego przez administratora w usłudze Azure AD, ale nie ma jeszcze zarejestrowany dla swojego konta informacje o zabezpieczeniach.
- Użytkownik został włączony dla samoobsługowego resetowania haseł w usłudze Azure AD. Informacje o zabezpieczeniach pomoże je zresetować swoje hasło w przyszłości, jeśli zapomną kiedykolwiek.
- Użytkownikowi dostęp do aplikacji, która zawiera zasady dostępu warunkowego, aby wymagać uwierzytelniania Wieloskładnikowego i nie zostało wcześniej zarejestrowane dla usługi MFA.
- Użytkownik rejestruje urządzenie z usługą Azure AD (w tym Azure AD Join), Twoja organizacja wymaga uwierzytelniania Wieloskładnikowego dla rejestracji urządzeń, a użytkownik nie zarejestrował wcześniej dla usługi MFA.
- Użytkownik generuje Windows Hello dla firm w systemie Windows 10, (co wymaga MFA) i nie zostało wcześniej zarejestrowane dla usługi MFA.
- Organizacja ma utworzone i włączone zasady rejestracji usługi MFA, która została zastosowana do użytkownika.
- Użytkownik wcześniej zarejestrowany do uwierzytelniania Wieloskładnikowego, ale wybraną metodę weryfikacji, ponieważ administrator wyłączył. Użytkownik w związku z tym musi przejść rejestracji w usłudze MFA ponownie, aby wybrać nowy domyślną metodę weryfikacji.

## <a name="errors"></a>Błędy

**Pyt.: Co powinni zrobić użytkownicy widzą komunikat o błędzie "żądanie uwierzytelnienia jest nie dotyczy uaktywnionego konta", używając powiadomienia z aplikacji mobilnej?**

Powiedz im, aby wykonać tę procedurę, aby usunąć swoje konto z aplikacji mobilnej, a następnie dodaj go ponownie:

1. Przejdź do [profilu portalu Azure](https://account.activedirectory.windowsazure.com/profile/) i zaloguj się przy użyciu konta organizacyjnego.
2. Wybierz **dodatkowej weryfikacji zabezpieczeń**.
3. Usuń istniejące konto z aplikacji mobilnej.
4. Kliknij przycisk **Konfiguruj**, a następnie postępuj zgodnie z instrukcjami, aby ponownie skonfigurować aplikację mobilną.

**Pyt.: Co powinni zrobić użytkownicy widzą komunikat o błędzie 0x800434D4L podczas logowania do aplikacji niekorzystających z przeglądarki?**

Błąd 0x800434D4L występuje, gdy użytkownik próbuje zarejestrować się w przypadku aplikacji niekorzystających z przeglądarki zainstalowane na komputerze lokalnym, który nie działa z konta, które wymagają weryfikacji dwuetapowej.

Obejście tego problemu dla tego błędu jest posiadanie oddzielnego konta dla administratora dotyczące i operacji bez uprawnień administratora. Później możesz połączyć skrzynek pocztowych między konta administratora i konto bez uprawnień administratora, aby zalogować się do programu Outlook przy użyciu swojego konta bez uprawnień administratora. Aby uzyskać więcej informacji o tym rozwiązaniu, Dowiedz się, jak [pozwalają administratorowi na otworzyć i wyświetlić zawartość skrzynki pocztowej użytkownika](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Kolejne kroki

Jeśli Twoje pytanie nie ma tutaj odpowiedzi, pozostaw je w komentarzach u dołu strony. Lub, w tym miejscu są pewne dodatkowe opcje w celu uzyskania pomocy:

* Wyszukiwanie [baza wiedzy pomocy technicznej firmy Microsoft](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) dla rozwiązania typowych problemów technicznych.
* Wyszukiwanie i Przeglądaj techniczne pytań i odpowiedzi od społeczności lub Zadaj pytanie w [Forum usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Jeśli jesteś klientem PhoneFactor starszej wersji, a masz pytania lub potrzebujesz pomocy dotyczącej resetowania hasła, użyj [resetowania hasła](mailto:phonefactorsupport@microsoft.com) link, aby otworzyć zgłoszenie do pomocy technicznej.
* Skontaktuj się z pracownikiem pomocy technicznej za pośrednictwem [pomocy technicznej usługi Azure Multi-Factor Authentication serwer (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Podczas nawiązywania kontaktu z nami, jest przydatne, Jeśli dołączysz jak najwięcej informacji o problemie, jak to możliwe. Możesz podać informacje dotyczące strony, w którym wystąpił błąd, kod błędu, identyfikator określonej sesji i identyfikator użytkownika, który wystąpił błąd.
