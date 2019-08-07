---
title: Często zadawane pytania dotyczące usługi Azure MFA Authentication — Azure Active Directory
description: Często zadawane pytania i odpowiedzi dotyczące uwierzytelniania wieloskładnikowego systemu Azure.
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
ms.openlocfilehash: 46abe367c9047616174a1e43dffd57861e6278e8
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68811830"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Często zadawane pytania dotyczące usługi Azure MFA Authentication

Często zadawane pytania dotyczą często spotykanych pytań dotyczących uwierzytelniania wieloskładnikowego systemu Azure i korzystania z usługi uwierzytelniania wieloskładnikowego. Jest on podzielony na pytania dotyczące usługi, ogólnie rzecz biorąc, modele rozliczeń, środowisko użytkownika i rozwiązywanie problemów.

## <a name="general"></a>Ogólne

**Pyt.: Jak serwer usługi Azure MFA obsługuje dane użytkowników?**

W przypadku serwera usługi MFA dane użytkownika są przechowywane tylko na serwerach lokalnych. Żadne trwałe dane użytkowników nie są przechowywane w chmurze. Gdy użytkownik przeprowadza weryfikację dwuetapową, serwer uwierzytelniania wieloskładnikowego wysyła dane do usługi Azure MFA Authentication w celu uwierzytelnienia. Komunikacja między serwerem usługi uwierzytelniania wieloskładnikowego a usługą uwierzytelniania wieloskładnikowego używa SSL (SSL) lub Transport Layer Security (TLS) przez port 443 wychodzący.

Gdy żądania uwierzytelniania są wysyłane do usługi w chmurze, dane są zbierane do raportów dotyczących uwierzytelniania i użycia. Pola danych zawarte w dziennikach weryfikacji dwuetapowej są następujące:

* **Unikatowy identyfikator** (nazwa użytkownika lub lokalny identyfikator serwera usługi uwierzytelniania wieloskładnikowego)
* **Imię i** nazwisko obowiązkowe
* **Adres e-mail** obowiązkowe
* **Numer telefonu** (w przypadku korzystania z połączenia głosowego lub uwierzytelniania SMS)
* **Token urządzenia** (w przypadku korzystania z uwierzytelniania aplikacji mobilnych)
* **Tryb uwierzytelniania**
* **Wynik uwierzytelniania**
* **Nazwa serwera usługi MFA**
* **Adres IP serwera z uwierzytelnianiem wieloskładnikowym**
* **Adres IP klienta** (jeśli jest dostępny)

Opcjonalne pola można skonfigurować na serwerze usługi uwierzytelniania wieloskładnikowego.

Wynik weryfikacji (sukces lub odmowa) i powód, jeśli został odrzucony, jest przechowywany w danych uwierzytelniania. Te dane są dostępne w raportach dotyczących uwierzytelniania i użycia.

**Pyt.: Jakie krótkie kody programu SMS są używane do wysyłania wiadomości SMS do użytkowników?**

W Stany Zjednoczone firma Microsoft używa następujących krótkich kodów programu SMS:

   * 97671
   * 69829
   * 51789
   * 99399

W Kanadzie firma Microsoft używa następujących krótkich kodów programu SMS:

   * 759731 
   * 673801

Firma Microsoft nie gwarantuje spójnego dostarczania SMS lub głosowego usługi uwierzytelniania wieloskładnikowego na podstawie tej samej liczby. W interesie naszych użytkowników firma Microsoft może dodawać lub usuwać krótkie kody w dowolnym momencie, gdy wprowadzimy zmiany trasy w celu poprawy możliwości dostarczania wiadomości SMS. Firma Microsoft nie obsługuje krótkich kodów dla krajów/regionów poza Stany Zjednoczone i Kanadę.

## <a name="billing"></a>Rozliczenia

W przypadku większości pytań dotyczących rozliczeń można odpowiedzieć na [stronie](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) z cennikiem usługi MFA lub z dokumentacją dotyczącą [sposobu uzyskiwania uwierzytelniania wieloskładnikowego Azure](concept-mfa-licensing.md).

**Pyt.: Czy Moja organizacja nalicza opłaty za wysyłanie połączeń telefonicznych i wiadomości SMS, które są używane do uwierzytelniania?**

Nie, nie są naliczone opłaty za poszczególne połączenia telefoniczne lub wiadomości SMS wysyłane do użytkowników za pomocą uwierzytelniania wieloskładnikowego systemu Azure. W przypadku korzystania z dostawcy usługi MFA dla uwierzytelniania, opłaty są naliczane za każde uwierzytelnianie, ale nie dla używanej metody.

Użytkownicy mogą być obciążani opłatami za rozmowy telefoniczne lub wiadomości SMS, które odbierają, zgodnie z ich osobistą usługą telefoniczną.

**Pyt.: Czy model rozliczeń dla poszczególnych użytkowników jest odpowiedzialny za wszystkich włączonych użytkowników, czy tylko te, które przeprowadzono weryfikację dwuetapową?**

Opłaty są naliczane na podstawie liczby użytkowników skonfigurowanych do korzystania z uwierzytelniania wieloskładnikowego, bez względu na to, czy przeprowadzono weryfikację dwuetapową tego miesiąca.

**Pyt.: Jak działa rozliczanie za uwierzytelnianie wieloskładnikowe?**

Podczas tworzenia dostawcy usługi MFA dla użytkownika lub uwierzytelniania w ramach subskrypcji platformy Azure w organizacji jest naliczana miesięczna stawka na podstawie użycia. Ten model rozliczeń jest podobny do tego, jak na platformie Azure są naliczane opłaty za użycie maszyn wirtualnych i witryn sieci Web.

W przypadku zakupienia subskrypcji usługi Azure MFA w organizacji jest naliczana roczna opłata za licencję dla każdego użytkownika. Licencje usługi MFA i pakiety Office 365, Azure AD — wersja Premium lub Enterprise Mobility + Security są rozliczane w ten sposób. 

Dowiedz się więcej na temat opcji dotyczących [uzyskiwania uwierzytelniania wieloskładnikowego Azure](concept-mfa-licensing.md).

**Pyt.: Czy istnieje bezpłatna wersja usługi Azure wieloskładnikowe Authentication?**

W niektórych przypadkach tak.

Uwierzytelnianie wieloskładnikowe dla administratorów systemu Azure oferuje podzbiór funkcji usługi Azure MFA bez ponoszenia kosztów dostępu do Usługi online firmy Microsoft, w tym [Azure Portal](https://portal.azure.com) i [Microsoft 365 centrum administracyjnego](https://admin.microsoft.com). Ta oferta dotyczy tylko administratorów globalnych w Azure Active Directory wystąpieniach, które nie mają pełnej wersji usługi Azure MFA za pośrednictwem licencji MFA, pakietu lub autonomicznego dostawcy opartego na użyciu. Jeśli Administratorzy korzystają z wersji bezpłatnej, a następnie kupisz pełną wersję usługi Azure MFA, wszyscy administratorzy globalni są podniesienie poziomu do wersji płatnej automatycznie.

Usługa uwierzytelnianie wieloskładnikowe dla użytkowników pakietu Office 365 oferuje podzestaw funkcji usługi Azure MFA bez żadnych kosztów, aby uzyskać dostęp do usług Office 365, w tym usługi Exchange Online i SharePoint Online. Ta oferta dotyczy użytkowników, którzy mają przypisaną licencję pakietu Office 365, gdy odpowiednie wystąpienie Azure Active Directory nie ma pełnej wersji usługi Azure MFA za pośrednictwem licencji MFA, pakietu lub autonomicznego dostawcy opartego na użyciu.

**Pyt.: Czy Moja organizacja może przełączać się między różnymi modelami rozliczeniowymi dla poszczególnych użytkowników i uwierzytelniania?**

Jeśli organizacja kupuje uwierzytelnianie wieloskładnikowe jako usługę autonomiczną z rozliczeniami opartymi na zużyciu, wybiera się model rozliczeń podczas tworzenia dostawcy usługi MFA. Nie można zmienić modelu rozliczeń po utworzeniu dostawcy usługi MFA. 

Jeśli dostawca MFA *nie* jest połączony z dzierżawą usługi Azure AD lub zostanie połączony nowy Dostawca usługi MFA z inną dzierżawcą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są transferowane. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu ich połączenia z nowym dostawcą usługi MFA nie wpływa na uwierzytelnianie za pośrednictwem połączeń telefonicznych i wiadomości SMS, ale powiadomienia w aplikacji mobilnej przestaną działać dla wszystkich użytkowników do momentu, w którym ponownie aktywują aplikację mobilną.

Dowiedz się więcej o dostawcach MFA w temacie [Rozpoczynanie pracy z dostawcą usługi Azure wieloskładnikowe Authentication](concept-mfa-authprovider.md).

**Pyt.: Czy Moja organizacja może przełączać się między rozliczeniami i subskrypcjami na podstawie użycia (modelem opartym na licencji) w dowolnym momencie?**

W niektórych przypadkach tak.

Jeśli katalog zawiera dostawcę uwierzytelniania wieloskładnikowego platformy Azure *dla użytkownika* , możesz dodać licencje usługi MFA. Użytkownicy z licencjami nie są wliczane do rozliczania opartego na użytkownikach. Użytkownicy bez licencji nadal mogą być włączeni do uwierzytelniania wieloskładnikowego za pomocą dostawcy usługi MFA. Jeśli kupisz i przypiszesz licencje wszystkim użytkownikom skonfigurowanym do korzystania z uwierzytelniania wieloskładnikowego, możesz usunąć dostawcę usługi Azure MFA. Możesz zawsze utworzyć innego dostawcę usługi MFA dla poszczególnych użytkowników, jeśli w przyszłości masz więcej użytkowników niż licencje.

Jeśli katalog zawiera dostawcę uwierzytelniania wieloskładnikowego platformy Azure dla *uwierzytelniania* , opłaty są naliczane za każde uwierzytelnianie, o ile dostawca usługi MFA jest połączony z subskrypcją. Licencje usługi MFA można przypisywać użytkownikom, ale nadal będą naliczane opłaty za każde żądanie weryfikacji dwuetapowej, niezależnie od tego, czy pochodzi ona od kogoś, kto ma przypisaną licencję usługi MFA.

**Pyt.: Czy Moja organizacja musi korzystać z uwierzytelniania wieloskładnikowego systemu Azure i synchronizować tożsamości?**

Jeśli Twoja organizacja korzysta z modelu rozliczania opartego na użyciu, Azure Active Directory jest opcjonalne, ale nie jest wymagane. Jeśli dostawca MFA nie jest połączony z dzierżawą usługi Azure AD, można wdrożyć tylko lokalny serwer usługi Azure Authentication.

Azure Active Directory jest wymagany dla modelu licencji, ponieważ licencje są dodawane do dzierżawy usługi Azure AD podczas zakupu i przypisywania do użytkowników w katalogu.

## <a name="manage-and-support-user-accounts"></a>Zarządzanie kontami użytkowników i ich obsługa

**Pyt.: Co mam zrobić, jeśli nie otrzymasz odpowiedzi na telefonie?**

Użytkownicy mogą próbować uzyskać dostęp do 5 razy w ciągu 5 minut, aby uzyskać połączenie telefoniczne lub wiadomość SMS w celu uwierzytelnienia. Firma Microsoft używa wielu dostawców do dostarczania wywołań i wiadomości SMS. Jeśli to nie zadziała, otwórz sprawę pomocy technicznej z firmą Microsoft, aby kontynuować rozwiązywanie problemów.

Jeśli powyższe kroki nie działają, miejmy nadzieję wszyscy użytkownicy skonfigurowali więcej niż jedną metodę weryfikacji. Poproś ich o ponowne zalogowanie przy użyciu innej metody weryfikacji wybieranej na stronie logowania.

Możesz wskazywać użytkowników do przewodnika [rozwiązywania problemów użytkownika końcowego](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**Pyt.: Co należy zrobić, jeśli nikt z użytkowników nie może uzyskać do swojego konta?**

Możesz zresetować konto użytkownika, przechodząc przez proces rejestracji. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą usługi Azure MFA Authentication w chmurze](howto-mfa-userdevicesettings.md).

**Pyt.: Co należy zrobić, jeśli jeden z użytkowników utraci telefon korzystający z haseł aplikacji?**

Aby zapobiec nieautoryzowanemu dostępowi, Usuń wszystkie hasła aplikacji użytkownika. Gdy użytkownik ma urządzenie zastępcze, może ponownie utworzyć hasła. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą usługi Azure MFA Authentication w chmurze](howto-mfa-userdevicesettings.md).

**Pyt.: Co zrobić, jeśli użytkownik nie może się zalogować do aplikacji niekorzystających z przeglądarki?**

Jeśli Twoja organizacja nadal używa starszych klientów i [zezwolono na korzystanie z haseł aplikacji](howto-mfa-mfasettings.md#app-passwords), użytkownicy nie będą mogli zalogować się do tych starszych klientów przy użyciu ich nazwy użytkownika i hasła. Zamiast tego należy [skonfigurować hasła aplikacji](../user-help/multi-factor-authentication-end-user-app-passwords.md). Użytkownicy muszą wyczyścić (usunąć) informacje logowania, ponownie uruchomić aplikację, a następnie zalogować się przy użyciu nazwy użytkownika i *hasła aplikacji* zamiast zwykłego hasła.

Jeśli Twoja organizacja nie ma starszych klientów, nie należy zezwalać użytkownikom na tworzenie haseł aplikacji.

> [!NOTE]
> Nowoczesne uwierzytelnianie dla klientów pakietu Office 2013
>
> Hasła aplikacji są wymagane tylko w przypadku aplikacji, które nie obsługują nowoczesnego uwierzytelniania. Klienci pakietu Office 2013 obsługują nowoczesne protokoły uwierzytelniania, ale muszą być skonfigurowane. Teraz nowoczesne uwierzytelnianie jest dostępne dla każdego klienta z marca 2015 lub nowszą aktualizacją pakietu Office 2013. Aby uzyskać więcej informacji, zobacz wpis w blogu [zaktualizowany nowoczesny pakiet Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

**Pyt.: Moi użytkownicy mówią, że czasami nie otrzymają wiadomości tekstowej lub wysyłają odpowiedzi na dwukierunkową wiadomość tekstową, ale okres weryfikacji jest przeprowadzony.**

Dostarczanie komunikatów tekstowych i otrzymanie odpowiedzi w postaci dwukierunkowej wiadomości SMS nie jest gwarantowane, ponieważ istnieją niekontrolowane czynniki, które mogą mieć wpływ na niezawodność usługi. Te czynniki obejmują docelowy kraj/region, operator telefonu komórkowego i siłę sygnału.

Jeśli użytkownicy często mają problemy z niezawodnymi komunikatami SMS, poinformuj ich o konieczności użycia aplikacji mobilnej lub metody rozmowy telefonicznej. Aplikacja mobilna może odbierać powiadomienia zarówno przez połączenia komórkowe, jak i Wi-Fi. Ponadto aplikacja mobilna może generować kody weryfikacyjne nawet wtedy, gdy urządzenie nie ma żadnego sygnału. Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)i [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

Jeśli musisz używać wiadomości tekstowych, zalecamy używanie jednokierunkowej wiadomości SMS, a nie dwukierunkowego programu SMS, jeśli jest to możliwe. Jednokierunkowa wiadomość SMS jest bardziej niezawodna i uniemożliwia użytkownikom naliczanie opłat za globalne korzystanie z programu SMS, które są wysyłane z innego kraju/regionu.

**Pyt.: Czy mogę zmienić czas, jaki wszyscy użytkownicy muszą wprowadzić kod weryfikacyjny z wiadomości tekstowej przed upływem limitu czasu systemu?**

W niektórych przypadkach tak. 

W przypadku jednokierunkowego SMS z usługą Azure MFA Server w wersji 7.0 lub nowszej można skonfigurować ustawienie limitu czasu, ustawiając klucz rejestru. Po wysłaniu wiadomości tekstowej przez usługę MFA w chmurze kod weryfikacyjny (lub jednorazowy kod dostępu) jest zwracany do serwera usługi MFA. Serwer MFA domyślnie przechowuje kod w pamięci przez 300 sekund. Jeśli użytkownik nie wprowadzi kodu przed upływem 300 sekund, jego uwierzytelnienie zostanie odrzucone. Wykonaj następujące kroki, aby zmienić domyślne ustawienie limitu czasu:

1. Przejdź do HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Utwórz klucz rejestru typu DWORD o nazwie **pfsvc_pendingSmsTimeoutSeconds** i Ustaw czas w sekundach, w ciągu którego serwer usługi Azure MFA ma przechowywać kody dostępu jednorazowego.

>[!TIP] 
>Jeśli masz wiele serwerów MFA, tylko te, które przetworzyły oryginalne żądanie uwierzytelnienia, znają kod weryfikacyjny, który został wysłany do użytkownika. Gdy użytkownik wprowadzi kod, żądanie uwierzytelnienia do weryfikacji musi być wysłane do tego samego serwera. Jeśli Walidacja kodu jest wysyłana na inny serwer, uwierzytelnianie zostanie odrzucone. 

W przypadku dwukierunkowego zarządzania programem SMS przy użyciu serwera usługi Azure MFA można skonfigurować ustawienie limitu czasu w portal zarządzania MFA. Jeśli użytkownicy nie odpowiadają na wiadomość SMS w określonym limicie czasu, uwierzytelnienie zostanie odrzucone. 

W przypadku jednokierunkowej wiadomości SMS z usługą Azure MFA w chmurze (w tym karty AD FS lub rozszerzenia serwera zasad sieciowych) nie można skonfigurować ustawienia limitu czasu. Usługa Azure AD przechowuje kod weryfikacyjny przez 180 sekund. 

**Pyt.: Czy mogę używać tokenów sprzętowych z serwerem usługi Azure MFA?**

W przypadku korzystania z serwera usługi Azure MFA można zaimportować tokeny hasła jednorazowego (TOTP) innej firmy, a następnie użyć ich do weryfikacji dwuetapowej.

Po umieszczeniu klucza tajnego w pliku CSV i zaimportowaniu go do serwera usługi Azure MFA można użyć tokenów ActiveIdentity, które są tokenami TOTP. Tokeny OATH można używać z usługami Active Directory Federation Services (ADFS), uwierzytelnianiem opartym na formularzach programu Internet Information Server (IIS) i usługą telefonujących użytkowników zdalnego uwierzytelniania (RADIUS), o ile system kliencki może zaakceptować dane wejściowe użytkownika.

Można importować tokeny TOTP OATH innych firm o następujących formatach:  

- Przenośny kontener kluczy symetrycznych (PSKC)  
- CSV, jeśli plik zawiera numer seryjny, klucz tajny w podstawowym formacie 32 i przedział czasu  

**Pyt.: Czy można używać serwera usługi Azure MFA do zabezpieczania usług terminalowych?**

Tak, ale jeśli korzystasz z systemu Windows Server 2012 R2 lub nowszego, możesz zabezpieczyć tylko usługi terminalowe przy użyciu bramy Pulpit zdalny (Brama usług pulpitu zdalnego).

Zmiany zabezpieczeń w systemie Windows Server 2012 R2 zmieniono sposób, w jaki serwer usługi Azure MFA nawiązuje połączenie z pakietem zabezpieczeń urzędu zabezpieczeń lokalnych (LSA) w systemie Windows Server 2012 i wcześniejszych wersjach. W przypadku wersji usług terminalowych w systemie Windows Server 2012 lub starszym można [zabezpieczyć aplikację z uwierzytelnianiem systemu Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). W przypadku korzystania z systemu Windows Server 2012 R2 potrzebna jest Brama usług pulpitu zdalnego.

**Pyt.: Identyfikator rozmówcy został skonfigurowany na serwerze usługi MFA, ale wszyscy użytkownicy nadal otrzymują wywołania usługi uwierzytelnianie wieloskładnikowe z anonimowego obiektu wywołującego.**

Gdy połączenia usługi uwierzytelnianie wieloskładnikowe są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami są kierowane przez operatora, który nie obsługuje identyfikatora rozmówcy. W związku z tym identyfikator wywołującego nie jest gwarantowany, mimo że system uwierzytelniania wieloskładnikowego zawsze go wysyła.

**Pyt.: Dlaczego mój użytkownik jest monitowany o zarejestrowanie informacji o zabezpieczeniach?**
Istnieje kilka powodów, dla których użytkownicy mogą uzyskać monit o zarejestrowanie informacji o zabezpieczeniach:

- Użytkownik włączył uwierzytelnianie MFA przez administratora w usłudze Azure AD, ale nie ma jeszcze zarejestrowanych informacji o zabezpieczeniach dla konta.
- Użytkownik włączył funkcję samoobsługowego resetowania hasła w usłudze Azure AD. Informacje o zabezpieczeniach ułatwią im zresetowanie hasła w przyszłości, jeśli kiedykolwiek zapomnisz.
- Użytkownik uzyskał dostęp do aplikacji, która ma zasady dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego i nie została wcześniej zarejestrowana dla usługi MFA.
- Użytkownik rejestruje urządzenie w usłudze Azure AD (w tym za pomocą usługi Azure AD join) i organizacja wymaga uwierzytelniania wieloskładnikowego na potrzeby rejestracji urządzeń, ale użytkownik nie został wcześniej zarejestrowany na potrzeby usługi MFA.
- Użytkownik generuje usługę Windows Hello dla firm w systemie Windows 10 (która wymaga uwierzytelniania MFA) i nie została wcześniej zarejestrowana dla usługi MFA.
- Organizacja utworzyła i włączyła zasady rejestracji usługi MFA, które zostały zastosowane do użytkownika.
- Użytkownik zarejestrował się wcześniej do uwierzytelniania wieloskładnikowego, ale wybiera metodę weryfikacji, która została wyłączona przez administratora. W związku z tym użytkownik musi ponownie wykonać rejestrację usługi MFA, aby wybrać nową domyślną metodę weryfikacji.

## <a name="errors"></a>Błędy

**Pyt.: Co powinni zrobić użytkownicy, jeśli podczas korzystania z powiadomień aplikacji mobilnej zobaczysz komunikat o błędzie "żądanie uwierzytelnienia nie dotyczy aktywowanego konta"?**

Poproś o wykonanie tej procedury, aby usunąć swoje konto z aplikacji mobilnej, a następnie dodaj ją ponownie:

1. Przejdź do [profilu Azure Portal](https://account.activedirectory.windowsazure.com/profile/) i zaloguj się przy użyciu konta organizacyjnego.
2. Wybierz opcję **dodatkowej weryfikacji zabezpieczeń**.
3. Usuń istniejące konto z aplikacji mobilnej.
4. Kliknij przycisk **Konfiguruj**, a następnie postępuj zgodnie z instrukcjami, aby ponownie skonfigurować aplikację mobilną.

**Pyt.: Co powinni zrobić użytkownicy, Jeśli zobaczysz komunikat o błędzie 0x800434D4L podczas logowania do aplikacji nie korzystającej z przeglądarki?**

0x800434D4L błąd występuje podczas próby zalogowania się do aplikacji nie korzystającej z przeglądarki zainstalowanej na komputerze lokalnym, która nie działa z kontami wymagającymi weryfikacji dwuetapowej.

Obejście tego błędu ma na celu oddzielenie kont użytkowników dla operacji związanych z administratorami i nieadministratorami. Później można połączyć skrzynki pocztowe między kontem administratora i kontem nieadministracyjnym, aby można było zalogować się do programu Outlook przy użyciu konta nienależącego do administratora. Aby uzyskać więcej informacji na temat tego rozwiązania, Dowiedz się, jak [dać administratorowi możliwość otwierania i wyświetlania zawartości skrzynki pocztowej użytkownika](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Następne kroki

Jeśli Twoje pytanie nie zostało tutaj odebrane, pozostaw je w komentarzach w dolnej części strony. Lub Oto kilka dodatkowych opcji uzyskiwania pomocy:

* Wyszukaj rozwiązania typowych problemów technicznych w [Pomoc techniczna firmy Microsoft bazie wiedzy](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) .
* Wyszukaj i Przeglądaj pytania techniczne oraz odpowiedzi ze społeczności lub zadawaj swoje pytania na [forach Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Jeśli jesteś starszym klientem programu PhoneFactor i masz pytania lub potrzebujesz pomocy przy resetowaniu hasła, Użyj linku [resetowania hasła](mailto:phonefactorsupport@microsoft.com) , aby otworzyć zgłoszenie do pomocy technicznej.
* Skontaktuj się z pracownikiem pomocy technicznej za pomocą [usługi Azure MFA Server (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Gdy kontaktuje się z nami, jest to przydatne, jeśli możesz dołączyć tyle informacji o problemie, ile to możliwe. Informacje, które można podać, obejmują stronę, na której wydano błąd, konkretny kod błędu, identyfikator konkretnej sesji oraz identyfikator użytkownika, który wykorzystał błąd.
