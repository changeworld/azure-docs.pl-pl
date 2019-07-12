---
title: Opis świecie bez hasła w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Ten przewodnik ułatwi naczelnych, dyrektorów działów IT, CISOs, dyrektor tożsamości architektów, architektów w przedsiębiorstwach i zabezpieczeń i decydenci IT odpowiedzialny za wybór metody uwierzytelniania bez hasła, ich wdrażania usługi Azure Active Directory.
services: active-directory
keywords: bez hasła, usługi Azure AD
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723443"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>World bez hasła w usłudze Azure Active Directory

Nadszedł czas, aby rozbić relacji z hasła. Hasła były dobre dla nas w przeszłości, ale w miejscu pracy cyfrowego współczesnych stają się stosunkowo łatwa ataku hakerom. Przed hakerami dobrze znanych haseł i nie jest trudne zobaczyć, dlaczego po zastanowieniu się nad, najczęściej odrzuconych hasła w usłudze Azure Active Directory (Azure AD) zawierają terminy, takie jak rok, miesiąc, sezonu lub lokalnym sports łączą. Ponadto [badania wykazały](https://aka.ms/passwordguidance) czy tradycyjne zalecenia dotyczące zarządzania hasłami, takich jak wymagania dotyczące długości i wymagań dotyczących złożoności, częstotliwości zmian unikają szkodliwych dla produkcji różnych powodów odnoszą się do w ludzkiej naturze.

Są trzy typy ataków, często używane do naruszenia bezpieczeństwa kont użytkowników osłony hasło Wyłudzanie informacji i breach oparte na metodzie powtórzeń. Funkcje usługi Azure AD, takie jak [inteligentnej blokady](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [zakazanych haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), i [ochrony hasłem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) może pomóc w ochronie przed tego rodzaju ataki. Podobnie, implementowanie [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA), lub Weryfikacja dwuetapowa zapewnia dodatkowe zabezpieczenia, wymagając od drugiej formy uwierzytelniania. Ale w perspektywie długoterminowej, najlepszym rozwiązaniem dla zapewnienia najbezpieczniejszą metodą uwierzytelniania jest rozwiązania bez hasła.

Ten artykuł stanowi początek swoją podróż, aby pomóc Ci zrozumieć i zaimplementować bez hasła rozwiązań firmy Microsoft i pomoc, której można wybrać co najmniej jeden z następujących opcji:

* **Funkcja Windows Hello dla firm**. W systemie Windows 10 Windows Hello dla firm zastępuje hasła przy użyciu silnego uwierzytelniania dwuskładnikowego na komputerach i urządzeniach przenośnych. To uwierzytelnianie składa się z nowym typem poświadczeń użytkownika, który jest powiązany z urządzeniem i używa biometryczne lub numer PIN.

* **Logowanie bez hasła z programem Microsoft Authenticator**. Aplikacja Microsoft Authenticator może służyć do logowania się do konta usługi Azure AD bez użycia hasła. Podobnie jak w technologii Windows Hello dla firm, Microsoft Authenticator korzysta z uwierzytelniania opartego na kluczach umożliwia poświadczeń użytkownika, który jest powiązany z urządzeniem i używa biometryczne lub numer PIN.

* **Klucze zabezpieczeń FIDO2**. FIDO2 podaje poświadczenia logowania usług kryptograficznych, są unikatowe w obrębie każdej witryny sieci Web, które są przechowywane na urządzeniu lokalnym, takich jak Windows Hello lub kluczy zabezpieczeń zewnętrznych. Te klucze zabezpieczeń są odporne na ryzyko wyłudzania informacji, kradzieży hasła i powtarzaniu. W połączeniu z weryfikacją użytkowników za pośrednictwem dane biometryczne lub numer PIN, rozwiązanie jest dwóch potrzeby w zakresie zabezpieczeń nowoczesnych współczynnik Weryfikacja meeting.

## <a name="the-future-of-passwordless-authentication"></a>Przyszłość uwierzytelnianie bez hasła

Te dni, banki, firm karty kredytowej i innych organizacji i usług online, konieczne będzie zweryfikowanie Twojej tożsamości dwa razy często chronić swoje konto: raz przy użyciu hasła, następnie ponownie przez telefon, tekst lub aplikacji. Podczas uwierzytelniania wieloskładnikowego usuwa lukę w zabezpieczeniach haseł, które są udostępniane, kradzieży lub złamać, nie odnosi się ich zapamiętanie współczynnik niedogodności. Użytkownicy i organizacje mają w bieżącej ery usług w chmurze jest metod uwierzytelniania bez hasła, które są bardzo bezpieczne *i* wygodne.

![Jako udogodnienie vs zabezpieczeń](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello dla firmy, bez hasła Zaloguj się przy użyciu Microsoft Authenticator i FIDO2 kluczy zabezpieczeń wszystkie mają prostą, typowe architektury, które zapewniają użytkownikom przy użyciu metody uwierzytelniania, która jest wysoce bezpieczne i łatwe w użyciu. Wszystkie trzy są oparte na technologii kluczy publicznych/prywatnych, potrzebę gest lokalnych, takich jak biometryczne lub numer PIN i kluczy prywatnych powiązany do jednego urządzenia i bezpiecznie przechowywane i nigdy nie udostępnili.

## <a name="windows-hello-for-business"></a>Windows Hello dla firm

W systemie Windows 10 Windows Hello dla firm zastępuje hasła przy użyciu silnego uwierzytelniania dwuskładnikowego na komputerach i urządzeniach. Uwierzytelnianie składa się z nowym typem poświadczeń użytkownika, który jest powiązany z urządzeniem i używa gest biometryczne lub numer PIN, który umożliwia użytkownikom na uwierzytelnianie w usłudze Azure AD oraz usługi Active Directory w środowisku lokalnym. Po prostu podpisywania do urządzenia z systemem Windows Hello dla firm jest prosty w użyciu. Możesz użyć numeru PIN lub gest biometryczne, takich jak rozpoznawanie odcisku palca lub twarzy.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello dla różnych scenariuszy biznesowych

Windows Hello dla firm jest idealnym rozwiązaniem dla pracowników przetwarzających informacje, którzy mają swoje własne wyznaczonym Komputerem Windows. Struktura biometryczna i poświadczenia są bezpośrednio powiązane z komputera użytkownika, co uniemożliwia dostęp z każdego z wyjątkiem właściciela. Za pomocą integracji infrastruktury kluczy publicznych i wbudowaną obsługę logowania jednokrotnego (SSO) Windows Hello dla firm oferuje proste i wygodne metody bezproblemowo dostępu do zasobów firmy lokalnie i w chmurze.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello dla firm zagadnienia związane z wdrażaniem

Windows Hello dla firm to Rozproszony system korzystającą z kilku składników w celu wykonania rejestracji urządzeń, inicjowanie obsługi administracyjnej i uwierzytelniania. W związku z tym wdrożenie wymaga odpowiednie planowanie wielu zespołów w organizacji. Windows Hello dla firm [przewodnik planowania](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) można ułatwić podjęcie decyzji na typ usługi Windows Hello dla firm wdrożenia i opcji, należy wziąć pod uwagę.

W przypadku wdrożeń hybrydowych i lokalnych oczekuje się, że masz:

* Dobrze połączone sieci pracy

* Dostęp do Internetu

* Serwer usługi Multi-Factor Authentication do obsługi usługi MFA podczas Windows Hello dla firm inicjowania obsługi administracyjnej

* Poprawnego rozpoznawania nazw, nazw wewnętrznych i zewnętrznych

* Usługi Active Directory i odpowiednia liczba kontrolerów domeny w każdej lokacji, aby zapewnić obsługę uwierzytelniania

* Usługi certyfikatów Active Directory 2012 lub nowszy

* Co najmniej jeden komputer stacji roboczej z systemem Windows 10, wersja 1903

Ponieważ wszystkie typy usługi Windows Hello wdrożeń biznesowych do używania certyfikatów wystawionych przez przedsiębiorstwa dla kontrolerów domeny jako elementu głównego zaufania, można automatycznie Odnów wygasłe certyfikaty, [Konfigurowanie automatycznej rejestracji dla użytkowników i serwera Certyfikaty](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment). W przypadku wdrożeń w środowisku lokalnym dostawcą tożsamości jest na lokalnym serwerze pełniącym rolę systemu Windows Server Active Directory Federation Services (AD FS). Systemy federacyjnego zwykle wymagają tablicą ze zrównoważonym obciążeniem serwerów, znane jako farmy. Tej farmy został skonfigurowany w sieci wewnętrznej i topologii sieci obwodowej, aby zapewnić wysoką dostępność dla żądań uwierzytelniania.

Po ustawieniu zasad grupy, aby wymagać Windows Hello dla firm w miejscu pracy, można przygotować użytkownicy w Twojej organizacji, wyjaśniające, jak korzystanie z usługi Windows Hello. Na przykład, gdy ktoś skonfiguruje nowe urządzenie, są monitowani o wybór między **to urządzenie należy do mojej organizacji** lub **to własnego urządzenia osobistego**. W przypadku urządzeń firmowych ich wybierz pierwsza. Użytkownicy będą także potrzebować tak dowiedzieć się, która opcja połączenia, użytkownik powinien wybrać: **Przyłączenia do usługi Azure AD** lub **Konfigurowanie konta lokalnego (później przyłączenie do domeny)** .

Jest to typowe dla użytkowników, którzy są przyzwyczajeni do korzystania z gest biometryczne do uwierzytelniania dzień po dzień po pewnym czasie zapomni swój kod PIN. Aby uniknąć wywołania do działu pomocy technicznej, zalecane jest, aby zapewnić użytkownikom możliwość resetowania zapomniane [haseł](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) i [numerów PIN](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset).

Istnieje wiele opcji, spośród których dokonać wyboru podczas wdrażania Windows Hello dla firm. Udostępnia wiele opcji gwarantuje, że niemal każda organizacja może wdrażać Windows Hello dla firm. Należy wziąć pod uwagę następujące typy wdrożeń, które są obsługiwane:

* [Hybrydowej usługi Azure AD przyłączone do wdrożenia zaufania klucza](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hybrydowej usługi Azure AD przyłączone do wdrożenia zaufania certyfikatu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Przewodniki dotyczące programu Azure AD Join wdrażania rejestracji jednokrotnej](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [We wdrożeniu kluczy zaufania lokalnego](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Lokalnego wdrożenia zaufania certyfikatu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Podając wiele opcji sprawia, że wdrożenia są wyświetlane złożone. Jednak większość organizacji prawdopodobnie określi, że został już zaimplementowany większość infrastruktury, na którym się Windows Hello dla firm wdrożenia jest zależny. Niezależnie od tego ważne jest zrozumienie, że Windows Hello dla firm to Rozproszony system i odpowiednie planowanie jest zalecane.

Zalecamy przeczytanie [planowania Windows Hello dla firm wdrożenia](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) ułatwią podjęcie decyzji o od modelu wdrażania, które najlepiej nadaje się do danej organizacji. Następnie, oparte na wprowadzeniu planowania, zapoznaj się [Windows Hello dla firm Deployment Guide](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) do zapewnienia pomyślnego wdrożenia usługi Windows Hello dla firm w istniejącym środowiskiem.

### <a name="how-windows-hello-for-business-works"></a>Windows Hello dla firm działania

#### <a name="user-sets-up-windows-hello-for-business"></a>Użytkownik skonfiguruje Windows Hello dla firm

Po początkowej weryfikacji dwuetapowej użytkownika podczas rejestracji Windows Hello jest skonfigurowany na urządzeniu użytkownika, a Windows pyta użytkownika, aby ustawić gest, który może być biometrycznych, takich jak rozpoznawanie odcisku palca lub twarzy lub numeru PIN. Po ustawieniu użytkownik udostępnia gest, aby zweryfikować swoją tożsamość. Windows następnie używa Witaj Windows do uwierzytelniania użytkowników.

Oparte na możliwości urządzenia z systemem Windows 10, albo masz wbudowanych enklawy bezpieczny, znane jako sprzętowego modułu TPM (Trusted platform) lub oprogramowania modułu TPM. Moduł TPM przechowuje klucz prywatny, która wymaga usługi rozpoznawania twarzy, odcisku palca lub numeru PIN do jego odblokowania. Danymi biometrycznymi nie są przekazywane i nigdy nie są wysyłane do zewnętrznych urządzeń lub serwerów. Nie istnieje żaden punkt jednej kolekcji, które osoba atakująca może naruszyć do kradzieży danych biometrycznych, ponieważ Windows Hello są przechowywane dane identyfikacyjne biometrycznego na urządzeniu.

> [!TIP]
> Na powierzchni czuje numeru PIN, takich jak hasła, ale jest faktycznie bezpieczniejsze. Jedną istotną różnicą między hasła i numeru PIN to, czy numer PIN jest powiązany z określonego urządzenia, na którym ją został ustawiony. Osoba, która dokonuje kradzieży hasła może zalogować się do konta z dowolnego miejsca. Ale jeśli one wykraść twój numer PIN, zostałyby zbyt kradzież urządzenia fizycznego! Ponadto ponieważ lokalne na urządzeniu jest numer PIN, go nie jest przesyłane dowolnym miejscu dzięki czemu nie został przechwycony podczas transmisji lub skradziony z serwera.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Za pomocą Windows Hello dla firm dla logowania użytkownika

Windows Hello dla firm biometria i numerów PIN używają asymetrycznych (klucz publiczny/prywatny) szyfrowania na potrzeby uwierzytelniania. Podczas uwierzytelniania szyfrowanie jest powiązany klucza sesji protokołu TLS, które zabezpiecza procesu uwierzytelniania, dzięki czemu atak typu man-in--middle (MiTM) nie kradzieży powstały token zabezpieczeń lub artefaktu i odtworzyć je ponownie w innych miejscach.

Windows Hello dla firm zapewnia wygodne logowanie uwierzytelniający użytkownika do usługi Azure AD i zasobów usługi Active Directory. Urządzenia przyłączone do usługi AD Azure uwierzytelniania na platformie Azure podczas logowania i opcjonalnie mógł uwierzytelniać się w usłudze Active Directory. Urządzenia przyłączone do usługi Azure Active Directory hybrydowego uwierzytelniania usługi Active Directory podczas logowania i uwierzytelniania usługi Azure Active Directory w tle.

![Zobacz obraz źródłowy](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

Poniższe kroki ilustrują uwierzytelniania logowania w usłudze Azure Active Directory.

![Ekran blokady systemu Windows 10](./media/azure-ad/azure-ad-pwdless-image3.png)

1. Zalogowaniu się użytkownika do Windows za pomocą biometryczne lub numer PIN, gest. Gest odblokowuje się Windows Hello dla firm klucza prywatnego i są wysyłane do dostawcy obsługi zabezpieczeń uwierzytelniania w chmurze, w określonych jako dostawca chmury AP.

2. Dostawca chmury AP żądań identyfikatora jednorazowego z usługi Azure Active Directory.

3. Usługa Azure AD zwraca identyfikator jednorazowy, który jest ważny przez 5 minut.

4. Dostawca chmury wschodni Region Azji i rejestruje identyfikator jednorazowy przy użyciu klucza prywatnego użytkownika i zwraca podpisem identyfikator jednorazowy z usługą Azure Active Directory.

5. Usługa Azure Active Directory weryfikuje podpisem (tymczasowo) przy użyciu bezpiecznie zarejestrowane klucz publiczny użytkownika względem nonce podpisu. Po weryfikacji podpisu, a następnie usługa Azure AD weryfikuje zwrócony identyfikator jednorazowy podpisem. Po upewnieniu się, identyfikator jednorazowy, usługi Azure AD tworzy PRT przy użyciu klucza sesji, który jest szyfrowany do klucza transportu urządzenia i zwraca go do dostawcy chmury AP.

6. Dostawca chmury AP odbiera PRT zaszyfrowane przy użyciu klucza sesji. Przy użyciu klucza prywatnego transportu urządzenia, dostawcy chmury AP odszyfruje klucz sesji i chroni klucz sesji przy użyciu urządzenia modułu TPM.

7. Dostawca chmury wschodni Region Azji i zwraca odpowiedź pomyślnego uwierzytelnienia do Windows, po upływie którego użytkownik może uzyskać dostępu, Windows, jak również w chmurze i lokalnych aplikacji bez konieczności uwierzytelniania ponownie (SSO).

Aby dowiedzieć się więcej na ten proces uwierzytelniania w innych scenariuszach obejmujących Windows Hello dla firm, zobacz [Windows Hello dla firm i uwierzytelniania](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Zarządzane przez ich Windows Hello dla firm poświadczeń

[Microsoft PIN Resetowanie usługi](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) to funkcja w usłudze Azure AD, który umożliwia użytkownikom Resetowanie numeru PIN, w razie potrzeby. Za pomocą zasad grupy, Microsoft Intune lub MDM zgodne, administrator może skonfigurować urządzenia systemu Windows 10, aby bezpiecznie używać usługa resetowania PIN firmy Microsoft, która umożliwia użytkownikom Resetowanie zapomnianych numer PIN przy zastosowaniu ustawień lub nad ekranem blokady bez konieczności ponownej rejestracji.

Czasami użytkownicy musieli wrócić do korzystania z haseł. [Samoobsługowe resetowanie haseł](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) jest inna funkcja usługi Azure AD, która umożliwia użytkownikom Resetowanie swoich haseł bez konieczności skontaktuj się z pracownikami działu IT. Użytkownicy musi zarejestrować na potrzeby lub być zarejestrowany, samodzielnie resetując hasło przed rozpoczęciem korzystania z usługi. Podczas rejestracji użytkownik wybiera jeden lub więcej metod uwierzytelniania, włączony przez organizację. Samoobsługowe Resetowanie HASEŁ umożliwia użytkownikom szybko uzyskać odblokowane i kontynuować pracę, niezależnie od tego, gdzie się znajdują lub porze dnia. Pozwalając użytkownikom na odblokowywanie samodzielnie, Twoja organizacja może zmniejszyć czas przestojów i kosztów obsługi wysokiej najbardziej typowe problemy związane z hasłami.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Logowanie bez hasła z programem Microsoft Authenticator

Logowanie bez hasła z programem Microsoft Authenticator jest inne rozwiązanie bez hasła, który może służyć do logowania do konta usługi Azure AD za pomocą logowanie za pomocą telefonu. Nadal należy zweryfikować swoją tożsamość, zapewniając coś, co należy wiedzieć, jak i coś, co ma, ale phone logowania można pominąć, wprowadzając hasło i przeprowadza weryfikację tożsamości na swoim urządzeniu przenośnym przy użyciu linii papilarnych, powierzchni lub numeru PIN.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Scenariusze bez hasła Microsoft Authenticator

Aplikacja Microsoft Authenticator umożliwia użytkownikom zweryfikować swoją tożsamość i Uwierzytelnij się do swojego konta firmowego lub osobistego. Jego można również rozszerzyć hasła z jednorazowym kodem dostępu lub powiadomienie push lub całkowicie wyeliminować potrzebę hasła. Zamiast używać hasła, użytkowników potwierdzenia tożsamości za pomocą swojego telefonu komórkowego za pomocą odcisku palca skanowania, rozpoznawanie twarzy lub iris lub numeru PIN. Oparta na technologii zabezpieczeń, które są podobne do czego Witaj Windows używa, to narzędzie znajduje się w pakiecie prostą aplikację na urządzeniu przenośnym, dzięki czemu wygodną opcją dla użytkowników. Aplikacja Microsoft Authenticator jest dostępna dla systemów Android i iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Zagadnienia dotyczące wdrażania programu Microsoft Authenticator

Następujące wymagania wstępne dotyczące korzystania z aplikacji Microsoft Authenticator w celu logowania bez hasła usłudze Azure AD:

* Użytkownicy końcowi są włączone dla usługi Azure Multi-Factor Authentication

* Możliwości dla użytkowników zarejestrować swoje urządzenia za pomocą programu Microsoft Intune lub rozwiązania do zarządzania (urządzeniami przenośnymi MDM) urządzeniami przenośnymi innej firmy

Zakładając, że te wymagania, Administratorzy włączyć logowanie telefonem bez hasła w dzierżawie za pomocą [programu Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Gdy logowanie za pomocą telefonu jest włączona w dzierżawie, użytkownicy końcowi mogą wybrać Zaloguj się przy użyciu telefonu, wybierając swoje konto służbowe lub szkolne na **kont** ekran aplikacji, a następnie wybierając pozycję **Włącz logowanie za pomocą telefonu** .

Przy założeniu, że logowanie bez hasła jest włączona przez administratora, użytkownicy końcowi muszą spełniać następujące wymagania:

* Zarejestrowane w usłudze Azure Multi-Factor Authentication

* Najnowszą wersję programu Microsoft Authenticator zainstalowanych na urządzeniach z systemem iOS 8.0 lub nowszej lub system Android 6.0 lub nowszej

* Konto służbowe lub szkolne, za pomocą powiadomień push dodana do aplikacji

Aby uniknąć potencjalnych możliwości uzyskiwania zablokowane z kontem lub konieczności ponownego tworzenia konta na nowym urządzeniu, zalecane jest, że używasz programu Microsoft Authenticator, aby [utworzyć kopię zapasową poświadczeń konta](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) do chmury. Po utworzeniu kopii zapasowej, można użyć jej do odzyskania danych na nowym urządzeniu potencjalnie unikanie wprowadzenie zablokowane ze specyfikatorem out lub konieczności ponownego tworzenia konta.

Ponieważ większość użytkowników są przyzwyczajeni do korzystania z tylko hasła do uwierzytelniania, ważne jest, że Twoja organizacja informuje użytkowników w zakresie tego procesu. Rozpoznawanie może zmniejszyć prawdopodobieństwo, że użytkownicy wywołać pomocy technicznej dla każdego [problemów](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) związanych z logowaniem przy użyciu aplikacji Microsoft Authenticator.

> [!NOTE]
> Potencjalny punkt awarii dla tego rozwiązania jest podczas roamingu użytkownika znajduje się w lokalizacji w przypadku, gdy brak połączenia internetowego. FIDO2 kluczy zabezpieczeń i Windows Hello dla firm nie podlegają tym samym ograniczeniom.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Jak logowanie bez hasła przy użyciu Microsoft Authenticator działa

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Użytkownik skonfiguruje logowanie bez hasła z programem Microsoft Authenticator

Zanim aplikacja Microsoft Authenticator może służyć jako rozwiązanie bez hasła do logowania się na konto usługi Azure AD, kroki muszą być wykonywane przez administratora i użytkowników końcowych.

Po pierwsze, administrator będzie musiał [umożliwić korzystanie z aplikacji jako poświadczenie](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) w dzierżawie za pomocą programu Windows PowerShell. Administrator należy również użytkownikom końcowym usługi Azure Multi-Factor Authentication (Azure MFA) i skonfiguruj aplikację Microsoft Authenticator jako jedną z [metod weryfikacji](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Użytkownicy końcowi będą musieli [Pobierz i zainstaluj](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) aplikacji Microsoft Authenticator i [skonfigurować swoje konto](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) do korzystania z aplikacji Microsoft Authenticator jako jednej z metod weryfikacji.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Przy użyciu Microsoft Authenticator logowanie bez hasła użytkownika

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Gdy na ekranie blokady systemu Windows 10 nie zawiera aplikacji Microsoft Authenticator jako opcji logowania, użytkownicy mogą nadal wprowadzić swoją nazwę użytkownika i następnie otrzymaj powiadomienie wypychane na swoim urządzeniu przenośnym, aby sprawdzić obecność. Użytkownicy potwierdzają obecność dopasowania numer na ekranie logowania, a następnie podając skanowania rozpoznawania twarzy, odcisk palca lub numer PIN w celu odblokowania klucza prywatnego i wykonania uwierzytelnienia. Ta metoda weryfikacji wieloskładnikowej jest bezpieczniejsze niż hasła i łatwiej niż wprowadzenie hasła i kodu.

![Wystawca uwierzytelnienia logowania](./media/azure-ad/azure-ad-pwdless-image4.png)

Uwierzytelnianie bez hasła, przy użyciu Microsoft Authenticator jest zgodna z ten sam wzorcem podstawowe jak Windows Hello dla firm, ale jest nieco bardziej skomplikowane, ponieważ użytkownik musi posiadać, aby odnaleźć przez usługę Azure AD jest wersja aplikacji Microsoft Authenticator używane.

![Proces uwierzytelniania](./media/azure-ad/azure-ad-pwdless-image5.png)

1. Użytkownik musi wprowadzić swoją nazwę użytkownika.

2. Usługa Azure AD wykrywa użytkownik silnych poświadczeń i uruchamia przepływ silnych poświadczeń.

3. Powiadomienie jest wysyłane do aplikacji za pośrednictwem firmy Apple Push Notification Service (APNS) na urządzeniach z systemem iOS lub przy użyciu usługi Firebase Cloud Messaging (FCM) na urządzeniach z systemem Android.

4. Użytkownik otrzyma powiadomienie wypychane i otwiera aplikację.

5. Aplikacja wywołuje usługę Azure AD i otrzymuje żądanie dowód Point of presence i identyfikatora jednorazowego.

6. Użytkownik kończy wyzwanie, wprowadzając ich biometryczne lub PIN do odblokowania klucza prywatnego.

7. Identyfikator jednorazowy jest podpisany przy użyciu klucza prywatnego i wysyłane z powrotem do usługi Azure AD.

8. Usługa Azure AD przeprowadza weryfikację kluczy publiczny/prywatny i zwraca token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Zarządzane przez ich logowanie bez hasła przy użyciu poświadczeń Microsoft Authenticator

Za pomocą [połączone rejestracji](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), użytkownicy mogą zarejestrować i Uzyskaj korzyści z usługi Azure Multi-Factor Authentication i Samoobsługowe resetowanie haseł. Użytkownikom rejestrować i zarządzać tymi ustawieniami, przechodząc do ich [strony Mój profil](https://aka.ms/mysecurityinfo). Oprócz włączenia funkcji samoobsługowego resetowania HASEŁ, połączone rejestracji obsługuje wiele metod uwierzytelniania i akcje.

## <a name="fido2-security-keys"></a>FIDO2 kluczy zabezpieczeń

FIDO2 jest najnowsza wersja standard FIDO Alliance i ma dwa składniki — standardowe uwierzytelnianie w sieci Web (WebAuthN) w formacie W3C i odpowiedni protokół klienta do uwierzytelniania FIDO Alliance (CTAP2). Standardy FIDO2 umożliwiają użytkownikom na wykorzystanie sprzętu - mobile- i na podstawie biometria wystawców uwierzytelnienia w łatwy sposób uwierzytelniania za pomocą wielu aplikacji i witryn internetowych w środowiskach mobilnych i klasycznych.

Firmy Microsoft i partnerów z branży pracują wspólnie na urządzeniach zabezpieczeń FIDO2 usługi Windows Hello umożliwia łatwe i bezpieczne uwierzytelnianie na udostępnionych urządzeniach. FIDO2 kluczy zabezpieczeń pozwalają wykonać swoje poświadczenia z Tobą i bezpiecznego uwierzytelniania [usługi Azure AD](https://aka.ms/azuread418)— urządzenia dołączonego do systemu Windows 10 jest częścią Twojej organizacji.

WebAuthN definiuje interfejs API, który umożliwia projektowania i implementacji silne, uwierzytelnianie bez hasła przy użyciu aplikacji sieci web i usług. Protokół CTAP umożliwia urządzeń zewnętrznych, takich jak zgodne FIDO kluczy zabezpieczeń do pracy z WebAuthN i służyć jako wystawcy uwierzytelnienia. Przy użyciu uwierzytelniania w sieci Web użytkownicy mogą się logować do usług online przy użyciu rozpoznawania twarzy, odcisku palca, numeru PIN lub przenośne FIDO2 kluczy zabezpieczeń, wykorzystując silnych poświadczeń klucza publicznego, zamiast hasła. Obecnie WebAuthN jest obsługiwana w programie Microsoft Edge i pomoc techniczna dla programu Chrome i Firefox jest w trakcie opracowywania.

Urządzenia i tokenów, zgodne z protokołów FIDO2, WebAuthN i CTAP doprowadzić do rozwiązania dla wielu platform z silnego uwierzytelniania bez użycia hasła. Partnerzy firmy Microsoft działają na różnych czynników formularza klucza zabezpieczeń, takie jak klucze zabezpieczeń USB i włączone NFC kart inteligentnych.

### <a name="fido2-security-keys-scenarios"></a>Scenariusze kluczy zabezpieczeń FIDO2

FIDO2 kluczy zabezpieczeń może służyć do logowania do usługi Azure AD, wybierając klawisz zabezpieczeń jako dostawca poświadczeń na ekranie blokady systemu Windows 10. Nazwa użytkownika lub hasło nie jest wymagane, co czyni go idealnym rozwiązaniem dla pierwszego wiersza pracowników, którzy udostępniania wielu użytkownikom komputerów z systemem. Są one również opcję doskonałą uwierzytelniania, gdy zasady firmowe określają, że poświadczenia użytkownika musi być fizycznie oddzielony od swoich urządzeń. Użytkownicy mogą też zalogować się do witryny sieci web przy użyciu FIDO2 klucz zabezpieczeń w przeglądarce Microsoft Edge w systemie Windows 10 w wersji 1809 lub nowszej.

### <a name="fido2-security-keys-deployment-considerations"></a>Zagadnienia dotyczące wdrażania kluczy zabezpieczeń FIDO2

Administratorzy mogą włączyć obsługę FIDO2 w usłudze Azure AD i przypisania możliwości użytkownikom lub grupom. Zasady można również utworzyć dla jak klucze są aprowizowane i skonfigurować ograniczenia, takie jak zezwalanie lub blokowanie określonego zestawu kluczy zabezpieczeń sprzętowych. Klucze muszą być fizycznie rozproszone dla użytkowników końcowych.

**Wymagania dotyczące włączania bez hasła, zaloguj się w usłudze Azure AD i witryn sieci web przy użyciu kluczy zabezpieczeń FIDO2 są następujące:**

* Azure AD

* Azure Multi-Factor Authentication

* Połączone rejestracji (wersja zapoznawcza)

* FIDO2 zabezpieczeń klucza (wersja zapoznawcza) wymaga klucza zabezpieczeń FIDO2 zgodne

* Uwierzytelnianie w sieci Web (WebAuthN) wymaga programu Microsoft Edge w systemie Windows 10 w wersji 1809 lub wyższej

* Oparte na Windows logowanie wymaga usługi Azure AD FIDO2 przyłączone do systemu Windows 10 w wersji 1809 lub nowszej (najlepsze środowisko jest w systemie Windows 10 w wersji 1903 lub nowszej)

Standardy zgodności FIDO2 obejmują USB, NFC i Bluetooth urządzenia. Firma Microsoft zaleca, możesz wybrać wygląd formularza, który spełnia Twoje wymagania określone od niektórych platform i przeglądarek nie są jeszcze zgodne FIDO2.

Zalecamy również każda organizacja utworzyć protokołu dla użytkowników i administratorów ścisłego powinien zabezpieczeń klucza są zapominane lub wykradane. Użytkownikom należy zgłaszać klucz zgubione lub skradzione, tak, aby usunąć ich kluczy zabezpieczeń z profilu użytkownika i aprowizować nowe administratorów lub użytkowników.

### <a name="how-fido2-security-keys-works"></a>Jak działa FIDO2 kluczy zabezpieczeń

#### <a name="user-sets-up-fido2-security-key"></a>Użytkownik skonfiguruje FIDO2 klucz zabezpieczeń

Gdy administratorzy mogą [ręcznie umożliwić klucze](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) i przekaż je użytkownikom końcowym, obsługa administracyjna oraz włączenie Dostawca poświadczeń FIDO2 na ekranie blokady systemu Windows 10 będą obsługiwane za pośrednictwem [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Administratorzy muszą używać [witryny Azure portal](https://portal.azure.com/) umożliwiające tokenu urządzenia jako metodę uwierzytelniania bez hasła.

Wdrażanie kluczy zabezpieczeń FIDO2 wymaga również czy użytkownikom rejestrowanie swoich kluczy przy użyciu [połączone rejestracji](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Łączna rejestracja w usłudze Użytkownicy Zarejestruj jeden raz i Uzyskaj korzyści z usługi Azure Multi-Factor Authentication i resetowania haseł rejestracji jednokrotnej (SSPR).

Wybierz opcję token sprzętowy jako domyślną metodą uwierzytelniania wieloskładnikowego, zalecane jest również wybrać opcję dodatkowa weryfikacja.

* Microsoft Authenticator — powiadomienie

* Aplikacja Authenticator lub sprzętu token — kod:

* Połączenie telefoniczne

* Wiadomość SMS

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Użytkownik korzystający z FIDO2 klucz zabezpieczeń podczas logowania

FIDO2 zapewnia warstwę abstrakcji między współczynnika postaci on używany jako wystawcy uwierzytelnienia i kryptografii kluczy publicznych/prywatnych umożliwiające wystawców uwierzytelnienia wbudowanych platformy takie jak klucze Windows Hello i zabezpieczeń do rozwiązania do klucza prywatnego i dostarczania klucza publicznego dostęp do zasobów zewnętrznych, można użyć jako identyfikatora. Kluczy zabezpieczeń FIDO2 wyposażone są w ich własnych wbudowanych enklawy bezpieczny, zapisuje klucz prywatny, która wymaga biometryczne lub PIN do jego odblokowania. Poświadczenia nie można użyć ponownie odtworzone, lub współdzielić w ramach usług i nie podlegają wyłudzanie informacji i ataki MiTM lub serwer naruszeń.

![FIDO2 logowania](./media/azure-ad/azure-ad-pwdless-image6.png)

Klucze zabezpieczeń FIDO2 zapewnienia bezpiecznego uwierzytelniania, niezależnie od współczynnika postaci. Klucz zabezpieczeń przechowuje poświadczenia i powinny być chronione przy użyciu dodatkowy czynnik drugi, np. odcisk palca (zintegrowane klucz zabezpieczeń) lub numeru PIN ma zostać nawiązane przy logowaniu Windows. Partnerzy firmy Microsoft działają na wielu różnych rozmiarach klucza zabezpieczeń. Niektóre przykłady kluczy zabezpieczeń USB i NFC włączona kart inteligentnych.

> [!NOTE]
> Klucz zabezpieczeń musi implementować niektórych funkcji i rozszerzeń z protokół FIDO2 CTAP [zgodny z programem Microsoft](https://aka.ms/fido2securitykeys). Firma Microsoft przetestowała te rozwiązania na potrzeby utrzymywania zgodności z systemem Windows 10 i usługi Azure Active Directory.

![Proces logowania FIDO2](./media/azure-ad/azure-ad-pwdless-image9.png)

1. Użytkownik urządzenia FIDO2 należy podłączyć do komputera.

2. Windows wykrywa FIDO2 klucza zabezpieczeń.

3. Windows wysyła żądanie uwierzytelnienia.

4. Usługa Azure AD wysyła z powrotem identyfikatora jednorazowego.

5. Użytkownik kończy swoje gestu do odblokowania klucza prywatnego, przechowywane w bezpiecznej enklawy FIDO2 klucz zabezpieczeń.

6. Klucz zabezpieczeń FIDO2 podpisuje identyfikator jednorazowy z kluczem prywatnym.

7. Żądania tokenu PRT z podpisem (tymczasowo) są wysyłane do usługi Azure AD.

8. Usługa Azure AD sprawdza podpisany przy użyciu klucza publicznego FIDO2 identyfikatora jednorazowego.

9. Usługa Azure AD zwraca PRT, aby umożliwić dostęp do zasobów lokalnych.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Zarządzane przez ich FIDO2 poświadczeń kluczy zabezpieczeń

Podobnie jak w aplikacji Microsoft Authenticator, Zarządzanie poświadczeniami dla kluczy zabezpieczeń FIDO2 opiera się na rejestracji połączone środowisko dla użytkowników końcowych.

## <a name="deciding-a-passwordless-method"></a>Przy wyborze rozwiązania bez hasła — metoda

Wybieranie między te trzy opcje bez hasła zależy od tego, zabezpieczeń, platformy i wymagania dotyczące aplikacji firmy.

Poniżej przedstawiono kilka czynników, które należy wziąć pod uwagę przy wyborze technologii programu Microsoft bez hasła:

||**Windows Hello dla firm**|**Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator**|**FIDO2 kluczy zabezpieczeń**|
|:-|:-|:-|:-|
|**Warunek wstępny**| Windows 10, wersja 1809 lub nowsza<br>Usługa Azure Active Directory| Aplikacja Microsoft Authenticator<br>Telefon (z systemem iOS i Android urządzenia z systemem Android 6.0 lub nowszej.)|Windows 10, wersja 1809 lub nowsza<br>Usługa Azure Active Directory|
|**Tryb**|Platforma|Oprogramowanie|Sprzęt|
|**Systemów i urządzeń**|Komputer przy użyciu wbudowanych Trusted Platform Module (TPM)<br>Rozpoznawanie kodu PIN i dane biometryczne |Rozpoznawanie kodu PIN i dane biometryczne na telefonie|FIDO2 zabezpieczeń urządzeń, które są zgodne z firmy Microsoft|
|**Środowisko użytkownika**|Zaloguj się przy użyciu numeru PIN lub biometrycznych rozpoznawania (twarzy, iris lub odcisk palca) z urządzeniami Windows.<br>Uwierzytelnianie Windows Hello jest powiązany z urządzeniem; użytkownik musi zarówno urządzenie, jak i składnik logowania, takie jak numer PIN lub biometrycznych współczynnik dostęp do zasobów firmy.|Zaloguj się przy użyciu telefonu komórkowego z rozpoznawaniem skanowania, twarzy lub iris odcisku palca lub numer PIN.<br>Logowania z osobistym kontem z ich Komputerami lub telefonu komórkowego.|Zaloguj się przy użyciu urządzenia zabezpieczeń FIDO2 (biometria, numer PIN i NFC)<br>Użytkownik może dostęp do urządzenia, na podstawie organizacji kontrolek i uwierzytelniania na podstawie numeru PIN, za pomocą urządzeń, takich jak kluczy zabezpieczeń USB i włączone NFC kart inteligentnych, klucze lub wearables dane biometryczne.|
|**Scenariusze włączone**| Bez hasła środowisko z urządzenia Windows.<br>Dotyczy dedykowanych Komputerami roboczymi przy użyciu możliwości logowania jednokrotnego do urządzeń i aplikacji.|Bez hasła dowolnym rozwiązania przy użyciu telefonu komórkowego.<br>Stosowane do uzyskiwania dostępu do służbowego lub osobistego aplikacji w sieci web z dowolnego urządzenia.|Bez hasła środowisko dla pracowników przy użyciu biometria, numer PIN i NFC.<br>Ma zastosowanie do udostępnionych komputerów i gdzie telefonu komórkowego nie jest opłacalny (na przykład wyraźnej personelowi pomocy technicznej, kiosku publicznego lub szpitali zespołu)|

Skorzystaj z poniższej tabeli, aby wybrać, którego metoda będzie spełniać wymagania i użytkowników.

|Osoby|Scenariusz|Środowisko|Technologia bez hasła|
|:-|:-|:-|:-|
|**Administrator**|Bezpieczny dostęp do urządzenia dla zadań zarządzania|Przypisanych urządzeń z systemem Windows 10|Windows Hello dla firm i/lub FIDO2 klucz zabezpieczeń|
|**Administrator**|Zadania zarządzania na urządzeniach innych niż Windows| Telefon komórkowy lub -windows urządzenia|Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator|
|**Pracownik przetwarzający informacje**|Wydajność pracy|Przypisanych urządzeń z systemem Windows 10|Windows Hello dla firm i/lub FIDO2 klucz zabezpieczeń|
|**Pracownik przetwarzający informacje**|Wydajność pracy| Telefon komórkowy lub -windows urządzenia|Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator|
|**Proces roboczy w zapewnianiu pierwszej linii**|Kioski we wpisie fabryki, fabryk, sprzedaży detalicznej lub danych|Urządzenia udostępnione systemu Windows 10|Klucze FIDO2 zabezpieczeń|

## <a name="getting-started"></a>Wprowadzenie

Uwierzytelnianie bez hasła jest rzutu przyszłość i ścieżkę do bardziej bezpieczne środowisko. Zalecane jest, że organizacjom rozpocząć planowanie tę zmianę i zmniejszyć ich zależności na hasłach. Aby rozpocząć pracę, należy wziąć pod uwagę następujące cele:

* Użytkownicy usługi MFA + Microsoft aplikacji Authenticator + dostępu warunkowego.

* Ochrona haseł usługi Azure AD wdrożenia + aktualizacji zasad.

* Włącz użytkowników do samoobsługowego resetowania HASEŁ łączna rejestracja w usłudze.

* Wdrażanie aplikacji Microsoft Azure Authenticator dla mobilności.

* Wdrażanie Windows Hello dla firm (1903: bądź na bieżąco).

* Wdrażanie urządzenia FIDO2 dla użytkowników, którzy nie mogą używać telefonów.

* Jeśli to możliwe, należy wyłączyć uwierzytelnianie oparte na hasłach.

Aby osiągnąć te cele, zalecamy następujące podejście:

1. Włączanie usługi Azure Active Directory, w pełni wykorzystać funkcje, takie jak usługi Azure MFA i dostępu warunkowego.

2. Włącz uwierzytelnianie wieloskładnikowe zapewnia dodatkową ochronę.

3. Włączenie samoobsługowego resetowania hasła w przypadku, gdy użytkownicy musieli wrócić korzystania z hasła.

4. Wdróż Microsoft Authenticator logowanie za pomocą telefonu dodano mobilności.

5. Wdróż Windows Hello dla firm do wszystkich urządzeń z systemem Windows 10.

6. Przygotuj FIDO2 kluczy zabezpieczeń.

> [!NOTE]
> Zapoznaj się z usługą Azure Active Directory [licencjonowania strony](https://azure.microsoft.com/pricing/details/active-directory/) szczegółowe informacje na temat wymagania licencyjne dla metod bez hasła.

## <a name="conclusion"></a>Wniosek

W ciągu ostatnich kilku lat Microsoft zaprojektowała swoje zobowiązanie do włączania świecie bez hasła. W systemie Windows 10 firma Microsoft wprowadziła Windows Hello dla firm, silne, sprzęt chronione poświadczeń two-Factor Authentication, która umożliwia logowania jednokrotnego do usługi Azure Active Directory i usługi Active Directory. Podobnie jak w technologii Windows Hello dla firm, aplikacji Microsoft Authenticator korzysta z uwierzytelniania opartego na kluczach umożliwia poświadczeń użytkownika, który jest powiązany z urządzenia przenośnego, a następnie używa biometryczne lub numer PIN. Teraz FIDO2 kluczy zabezpieczeń umożliwiają wykonać swoje poświadczenia z Tobą i zaloguj się do usługi Azure AD, wybierając klawisz zabezpieczeń jako dostawca poświadczeń na ekranie blokady systemu Windows 10. Wszystkie trzy te rozwiązania bez hasła zmniejszyć ryzyko wyłudzania informacji, osłony hasła i ataki przez powtórzenie i zapewni użytkownikom bardzo bezpieczny i wygodny sposób logować się i uzyskać dostęp do danych z dowolnego miejsca.

Przyjęcie nowoczesnego uwierzytelniania wieloskładnikowego technologii, takich jak dane biometryczne i kryptografii klucza publicznego w urządzeniach szeroko dostępna jest jednym z największy czynności, które mogą znacząco ograniczyć ryzyko związane z tożsamościami firmy. Pracę bez hasła jest to długoterminowe podejście do bezpiecznego uwierzytelniania, a wciąż ewoluuje. Biorąc pod uwagę wymagania pojawiających się, organizacje mogą przygotowanie się poprzez planu, aby zacząć przenosić do technologii bez hasła.

## <a name="next-steps"></a>Następne kroki

* Omówienie [co to jest bez hasła?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Jak włączyć bez hasła w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
