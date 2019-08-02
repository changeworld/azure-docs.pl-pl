---
title: Informacje o świecie bez hasła Azure Active Directory | Microsoft Docs
description: Ten przewodnik pomaga CEOs, dyrektorzy działu informatyki, CISOs, starszym architektom tożsamości, architektom przedsiębiorstwa oraz zabezpieczeniom i organom IT odpowiedzialnym za wybór metody uwierzytelniania bezhasła dla ich implementacji Azure Active Directory.
keywords: bezhasło, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 30c2eb2964f11b729b0ffaf58c532d5189cdc2cd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727771"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Świat bez hasła przy użyciu Azure Active Directory

Jest to czas na rozdzielenie relacji z hasłami. Hasła były dobre dla nas w przeszłości, ale w dzisiejszej, cyfrowej miejscu pracy stało się stosunkowo łatwym sposobem ataku na ataki hakerów. Hakerzy lubią hasła i nie są trudne do sprawdzenia, dlaczego należy wziąć pod uwagę, że najczęściej odrzucone hasła w usłudze Azure Active Directory (Azure AD) zawierają warunki, takie jak rok, miesiąc, sezon lub lokalny zespół sportowy. Ponadto [badania](https://aka.ms/passwordguidance) wykazały, że tradycyjne zalecenia dotyczące zarządzania hasłami, takie jak wymagania dotyczące długości, wymagania dotyczące złożoności i częstotliwości zmian, są counterproductive z różnych powodów związanych z charakterem ludzkim.

Trzy typy ataków często używanych do naruszenia zabezpieczeń kont użytkowników to rozpylanie, phishing i ponowne odtworzenie naruszenia. Funkcje usługi Azure AD, takie jak [inteligentna blokada](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [zabronione hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)i [Ochrona hasłem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) , mogą pomóc chronić przed atakami tego typu. Podobnie, implementacja [uwierzytelniania](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) wieloskładnikowego (MFA) lub dwuetapowa weryfikacja zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania. Jednak w długim czasie, rozwiązanie bezhasło to najlepsze rozwiązanie do zapewnienia najbezpieczniejszej metody uwierzytelniania.

Ten artykuł jest początek podróży, aby pomóc Ci zrozumieć i wdrożyć rozwiązania bezhaseł firmy Microsoft, a także wybrać jedną z następujących opcji:

* Funkcja **Windows Hello dla firm**. W systemie Windows 10 funkcja Windows Hello dla firm zastępuje hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach i urządzeniach przenośnych. To uwierzytelnianie składa się z nowego typu poświadczeń użytkownika, który jest powiązany z urządzeniem i używa biometrycznych lub PIN.

* **Logowanie bez hasła przy użyciu Microsoft Authenticator**. Aplikacja Microsoft Authenticator może służyć do logowania się do konta usługi Azure AD bez użycia hasła. Podobnie jak w przypadku technologii Windows Hello dla firm, Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby umożliwić poświadczenie użytkownika powiązanego z urządzeniem i używa biometrycznych lub PIN.

* **FIDO2 klucze zabezpieczeń**. FIDO2 udostępnia kryptograficzne poświadczenia logowania, które są unikatowe w każdej witrynie sieci Web i są przechowywane na urządzeniu lokalnym, takim jak Windows Hello lub zewnętrzne klucze zabezpieczeń. Te klucze zabezpieczeń są odporne na ryzyko wyłudzania informacji, kradzieży hasła i ataki metodą powtórzeń. W połączeniu z weryfikacją użytkownika za pośrednictwem biometrii lub numerem PIN rozwiązanie jest dwuetapową weryfikacją spełniającą nowoczesne potrzeby w zakresie zabezpieczeń.

## <a name="the-future-of-passwordless-authentication"></a>Przyszłość uwierzytelniania bezhasła

Te dni, banki, firmy kart kredytowych i inne organizacje i Usługi online często chronią Twoje konto, wymagając zweryfikowania tożsamości dwa razy: przy użyciu hasła, a następnie za pomocą telefonu, tekstu lub aplikacji. Podczas gdy usługa uwierzytelniania wieloskładnikowego rozwiązuje problem związany z bezpieczeństwem haseł, które są udostępniane, skradzione lub odporne, nie dotyczy czynnika niedogodności, który próbuje je zapamiętać. Co widzą Użytkownicy i organizacje w dzisiejszej firmie w chmurze era metody uwierzytelniania bez hasła, które są wysoce bezpieczne *i* wygodne.

![Wygoda a zabezpieczenia](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Funkcja Windows Hello dla firm, logowanie bez hasła przy użyciu Microsoft Authenticator i klucze zabezpieczeń FIDO2 współdzielą prostą, wspólną architekturę, która zapewnia użytkownikom metodę uwierzytelniania, która jest silnie bezpieczna i wygodnie do użycia. Wszystkie trzy są oparte na technologii Public/Private-Key, potrzebują lokalnego gestu, takiego jak biometryczna lub PIN, oraz kluczy prywatnych powiązanych z jednym urządzeniem i bezpiecznie przechowywane i nigdy nie udostępniane.

## <a name="windows-hello-for-business"></a>Windows Hello dla firm

W systemie Windows 10 funkcja Windows Hello dla firm zastępuje hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach i urządzeniach. Uwierzytelnianie obejmuje nowy typ poświadczeń użytkownika, który jest powiązany z urządzeniem i używa gestu biometrycznego lub numeru PIN, który umożliwia użytkownikom uwierzytelnianie w usłudze Azure AD, a także Active Directory lokalnych. Proste logowanie się do urządzenia przy użyciu funkcji Windows Hello dla firm jest łatwe. Używasz gestu numeru PIN lub biometrycznego, takiego jak odcisk palca lub rozpoznawanie twarzy.

### <a name="windows-hello-for-business-scenarios"></a>Scenariusze usługi Windows Hello dla firm

Funkcja Windows Hello dla firm jest idealna dla pracowników przetwarzających informacje, którzy mają własne Wyznaczeni komputery z systemem Windows. Biometryczne i poświadczenia są bezpośrednio powiązane z komputerem użytkownika, co uniemożliwia dostęp od nikogo innego niż właściciel. Dzięki integracji infrastruktury PKI i wbudowanej obsłudze logowania jednokrotnego (SSO) usługa Windows Hello dla firm zapewnia prostą i wygodną metodę bezproblemowego uzyskiwania dostępu do zasobów firmy lokalnie i w chmurze.

### <a name="windows-hello-for-business-deployment-considerations"></a>Zagadnienia dotyczące wdrażania usługi Windows Hello dla firm

Usługa Windows Hello dla firm to system rozproszony, który używa kilku składników do wykonywania rejestracji, aprowizacji i uwierzytelniania urządzeń. W związku z tym wdrożenie wymaga prawidłowego planowania dla wielu zespołów w organizacji. [Przewodnik planowania](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) usługi Windows Hello dla firm może służyć do podejmowania decyzji dotyczących typu wdrożenia usługi Windows Hello dla firm i opcji, które należy wziąć pod uwagę.

Istnieje wiele opcji, które można wybrać podczas wdrażania usługi Windows Hello dla firm. Udostępnienie wielu opcji gwarantuje, że prawie każda organizacja będzie mogła wdrożyć usługę Windows Hello dla firm. Należy wziąć pod uwagę następujące typy wdrożeń, które są obsługiwane:

* [Wdrożenie hybrydowego zaufania klucza przyłączonego do usługi Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Wdrożenie hybrydowego zaufania certyfikatu połączonego z usługą Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Przewodnik wdrażania logowania jednokrotnego w usłudze Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Wdrożenie zaufania klucza lokalnego](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Wdrożenie zaufania z certyfikatem lokalnym](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Udostępnienie wielu opcji powoduje, że wdrożenie jest złożone. Jednak większość organizacji będzie prawdopodobnie określać, że zostały już zaimplementowane większość infrastruktury, w której zależą wdrożenie usługi Windows Hello dla firm. Bez względu na to ważne jest, aby zrozumieć, że usługa Windows Hello dla firm jest systemem rozproszonym i zalecane jest odpowiednie planowanie.

Zalecamy zapoznanie się z artykułem [Planowanie wdrożenia usługi Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) , aby ułatwić podjęcie decyzji dotyczącej modelu wdrażania najlepiej dopasowanego do danej organizacji. Następnie zapoznaj się z planem [wdrożenia usługi Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) , aby pomóc w zapewnieniu pomyślnego wdrożenia usługi Windows Hello dla firm w istniejącym środowisku.

### <a name="how-windows-hello-for-business-works"></a>Jak działa funkcja Windows Hello dla firm

#### <a name="user-sets-up-windows-hello-for-business"></a>Użytkownik konfiguruje usługi Windows Hello dla firm

Po wstępnej weryfikacji dwuetapowej użytkownika podczas rejestracji funkcja Windows Hello jest skonfigurowana na urządzeniu użytkownika, a system Windows prosi użytkownika o ustawienie gestu, który może być biometryczny, na przykład odcisku palca lub rozpoznawania twarzy lub numerem PIN. Po ustawieniu użytkownik będzie posłużyć gestu do zweryfikowania jego tożsamości. System Windows użyje Windows Hello do uwierzytelniania użytkowników.

W oparciu o możliwości urządzenia z systemem Windows 10 użytkownik będzie miał wbudowaną enklawy Secure, znaną jako sprzętowy moduł TPM (Trusted Platform Module) lub moduł TPM oprogramowania. Moduł TPM przechowuje klucz prywatny, który wymaga do jego odblokowania Twojego kroju, odcisku palca lub numeru PIN. Dane biometryczne nie są przenoszone i nigdy nie są wysyłane do zewnętrznych urządzeń lub serwerów. Nie istnieje pojedynczy punkt kolekcji, osoba atakująca może złamać dane biometryczne, ponieważ funkcja Windows Hello zapisuje dane identyfikacji biometrycznej na urządzeniu.

> [!TIP]
> Na powierzchni numer PIN jest uważany za hasło, ale jest to faktycznie bezpieczniejsze. Ważna różnica między hasłem a numerem PIN polega na tym, że numer PIN jest powiązany z określonym urządzeniem, na którym został skonfigurowany. Ktoś, który kradzież hasła może zalogować się na konto z dowolnego miejsca. Ale w przypadku kradzieży numeru PIN należy również ukraść urządzenie fizyczne. Ponadto, ponieważ numer PIN jest lokalny dla urządzenia, nie jest przesyłany w dowolnym miejscu, dlatego nie można go przechwycić ani skradziony z serwera.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Użytkownik korzystający z funkcji Windows Hello dla firm do logowania

Funkcja Windows Hello dla biometrii i numery PIN używają klucza asymetrycznego (publicznego/prywatnego) do uwierzytelniania. Podczas uwierzytelniania, szyfrowanie jest powiązane z kluczem sesji protokołu TLS, który zabezpiecza proces uwierzytelniania, dzięki czemu atak typu man-in-the-Middle (MiTM) nie może wykraść tego tokenu lub artefaktu zabezpieczeń ani powtórzyć go z innych miejsc.

Funkcja Windows Hello dla firm zapewnia wygodne środowisko logowania, które uwierzytelnia użytkownika w usłudze Azure AD i Active Directory zasobów. Urządzenia przyłączone do usługi Azure AD uwierzytelniają się na platformie Azure podczas logowania i opcjonalnie mogą uwierzytelniać się w Active Directory. Azure Active Directory urządzeń przyłączonych do hybrydowego uwierzytelniania do Active Directory podczas logowania i uwierzytelniania w Azure Active Directory w tle.

![Zobacz obraz źródła](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

Poniższe kroki ilustrują uwierzytelnianie logowania do Azure Active Directory.

![Ekran blokady systemu Windows 10](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. Użytkownik loguje się do systemu Windows za pomocą gestu biometrycznego lub numeru PIN. Gest odblokowuje klucz prywatny usługi Windows Hello dla firm i jest wysyłany do dostawcy obsługi zabezpieczeń uwierzytelniania w chmurze, zwanego dostawcą AP usługi Cloud.

2. Dostawca AP w chmurze żąda identyfikatora jednorazowego z Azure Active Directory.

3. Usługa Azure AD zwraca identyfikator jednorazowy ważny przez 5 minut.

4. Dostawca AP w chmurze podpisuje identyfikator jednorazowy przy użyciu klucza prywatnego użytkownika i zwraca podpisany identyfikator jednorazowy do Azure Active Directory.

5. Azure Active Directory sprawdza poprawność podpisanego identyfikatora nonce przy użyciu bezpiecznie zarejestrowanego klucza publicznego użytkownika w odniesieniu do sygnatury jednorazowej. Po sprawdzeniu podpisu usługa Azure AD następnie weryfikuje zwrócony podpisany identyfikator jednorazowy. Po zweryfikowaniu identyfikatora jednorazowego usługa Azure AD tworzy PRT z kluczem sesji zaszyfrowanym do klucza transportu urządzenia i zwraca go do dostawcy AP w chmurze.

6. Dostawca AP usług w chmurze odbiera zaszyfrowany PRT za pomocą klucza sesji. Korzystając z prywatnego klucza transportu urządzenia, dostawca AP usług w chmurze odszyfrowuje klucz sesji i chroni klucz sesji przy użyciu modułu TPM urządzenia.

7. Dostawca AP w chmurze zwraca pomyślną odpowiedź uwierzytelniania do systemu Windows, po którym użytkownik może uzyskać dostęp do systemu Windows, a także aplikacji lokalnych i w chmurze bez konieczności ponownego uwierzytelniania (SSO).

Aby lepiej poznać proces uwierzytelniania w innych scenariuszach obejmujących usługi Windows Hello dla firm, zobacz Funkcja [Windows Hello dla firm i uwierzytelnianie](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Użytkownik zarządza poświadczeniami usługi Windows Hello dla firm

[Usługi resetowania numerów PIN firmy Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) to funkcja w usłudze Azure AD, która umożliwia użytkownikom Resetowanie numerów PIN w razie potrzeby. Za pomocą zasad grupy, Microsoft Intune lub zgodnego zarządzania urządzeniami przenośnymi, administrator może skonfigurować urządzenia z systemem Windows 10, aby bezpiecznie używały usługi resetowania numerów PIN firmy Microsoft, która umożliwia użytkownikom resetowanie zapomnianego numeru PIN za pośrednictwem ustawień lub ponad ekranu blokady bez konieczności ponowna rejestracja.

Czasami użytkownicy muszą wrócić do korzystania z haseł. [Samoobsługowe resetowanie hasła](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) to inna funkcja usługi Azure AD, która umożliwia użytkownikom Resetowanie swoich haseł bez konieczności kontaktowania się z pracownikami działu IT. Przed skorzystaniem z usługi użytkownicy muszą zarejestrować się w usłudze lub zostać zarejestrowani do samoobsługowego resetowania hasła. Podczas rejestracji użytkownik wybiera jedną lub więcej metod uwierzytelniania włączonych przez ich organizację. SSPR umożliwia użytkownikom szybkie uzyskanie odblokowania i kontynuowanie pracy niezależnie od miejsca, w którym są lub pory dnia. Dzięki umożliwieniu użytkownikom samodzielnego odblokowania, organizacja może ograniczyć czas nieproduktywny i wysokie koszty obsługi typowych problemów związanych z hasłami.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Logowanie bez hasła przy użyciu Microsoft Authenticator

Logowanie bezhasłem za pomocą Microsoft Authenticator jest innym rozwiązaniem bez hasła, które może służyć do logowania się do kont usługi Azure AD przy użyciu logowania za pomocą telefonu. Nadal musisz zweryfikować swoją tożsamość, podając coś, czego znasz, ale zalogowanie za pomocą telefonu pozwala pominąć wprowadzanie hasła i przeprowadzanie wszystkich weryfikacji tożsamości na urządzeniu przenośnym przy użyciu odcisku palca, kroju lub numeru PIN.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator scenariusze bezhasło

Aplikacja Microsoft Authenticator umożliwia użytkownikom weryfikowanie tożsamości i uwierzytelnianie na koncie służbowym lub osobistym. Można go również użyć do rozszerzenia hasła przy użyciu jednorazowego kodu dostępu lub powiadomienia wypychanego lub zamienić w ogóle potrzebę hasła. Zamiast korzystać z hasła użytkownicy potwierdzają swoją tożsamość przy użyciu telefonu komórkowego za pośrednictwem skanowania odcisków palców, rozpoznawania twarzy lub tęczówki lub numeru PIN. Utworzone w oparciu o bezpieczną technologię podobną do używanej przez usługi Windows Hello, to narzędzie jest spakowane w prostej aplikacji na urządzeniu przenośnym, co sprawia, że użytkownicy. Aplikacja Microsoft Authenticator jest dostępna dla systemów Android i iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Zagadnienia dotyczące wdrażania Microsoft Authenticator

Wymagania wstępne dotyczące używania aplikacji Microsoft Authenticator do logowania bez hasła do usługi Azure AD są następujące:

* Użytkownicy końcowi są włączeni do uwierzytelniania wieloskładnikowego platformy Azure

* Możliwość rejestrowania urządzeń przez użytkowników za pomocą Microsoft Intune lub rozwiązania do zarządzania urządzeniami przenośnymi (MDM) innej firmy

Przy założeniu, że te wymagania są spełnione, Administratorzy włączają logowanie za pomocą [programu Windows PowerShell](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) bezhasłem i w dzierżawie. Po włączeniu logowania w dzierżawie użytkownicy końcowi mogą zdecydować się na zalogowanie się przy użyciu telefonu, wybierając konto służbowe na ekranie **konta** aplikacji, a następnie wybierając pozycję **Włącz logowanie za pomocą telefonu**.

Przy założeniu, że logowanie bezhasło jest włączone przez administratora, użytkownicy końcowi muszą spełniać następujące wymagania:

* Zarejestrowano w usłudze Azure MFA Authentication

* Najnowsza wersja Microsoft Authenticator zainstalowana na urządzeniach z systemem iOS 8,0 lub nowszym albo systemem Android 6,0 lub nowszym

* Konto służbowe z powiadomieniami wypychanymi dodane do aplikacji

Aby uniknąć potencjalnego zablokowania konta lub konieczności ponownego tworzenia kont na nowym urządzeniu, zaleca się używanie Microsoft Authenticator do [tworzenia kopii zapasowych poświadczeń konta](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) do chmury. Po utworzeniu kopii zapasowej można również użyć aplikacji do odzyskania informacji na nowym urządzeniu, co może uniknąć zablokowania lub konieczności ponownego tworzenia kont.

Ze względu na to, że większość użytkowników jest przyzwyczajonych do korzystania tylko z haseł do uwierzytelniania, ważne jest, aby organizacja przeanalizuje użytkowników dotyczące tego procesu. Dzięki świadomości może zmniejszyć prawdopodobieństwo, że użytkownicy będą mogli skontaktować się z działem pomocy technicznej w celu uzyskania [problemów](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) związanych z logowaniem się przy użyciu aplikacji Microsoft Authenticator.

> [!NOTE]
> Potencjalnym punktem awarii tego rozwiązania jest to, że użytkownik mobilny znajduje się w lokalizacji, w której nie ma łączności z Internetem. Klucze zabezpieczeń FIDO2 i funkcja Windows Hello dla firm nie podlegają tym samym ograniczeniom.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Jak działa logowanie bezhasłem przy użyciu Microsoft Authenticator

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Użytkownik konfiguruje logowanie bez hasła przy użyciu Microsoft Authenticator

Aby można było zalogować się do konta usługi Azure AD za pomocą aplikacji Microsoft Authenticator, należy wykonać czynności wykonywane przez administratora i użytkowników końcowych.

Najpierw administrator musi [włączyć korzystanie z aplikacji jako poświadczenia](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) w dzierżawie przy użyciu programu Windows PowerShell. Administrator musi również umożliwić użytkownikom końcowym uwierzytelnianie wieloskładnikowe systemu Azure (Azure MFA) i skonfigurować aplikację Microsoft Authenticator jako jedną z [metod weryfikacji](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Użytkownicy końcowi będą musieli [pobrać i zainstalować](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) aplikację Microsoft Authenticator i [skonfigurować swoje konto](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) do korzystania z aplikacji Microsoft Authenticator jako jednej z metod weryfikacji.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Użytkownik korzystający z Microsoft Authenticator do logowania bezhasła

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Gdy ekran blokady systemu Windows 10 nie zawiera aplikacji Microsoft Authenticator jako opcji logowania, użytkownicy nadal mogą wprowadzić swoją nazwę użytkownika, a następnie odebrać Powiadomienie wypychane na urządzeniu przenośnym w celu sprawdzenia obecności. Użytkownicy potwierdzają swoją obecność, dopasowując liczbę na ekranie logowania, a następnie dostarczając do nich skanowanie, odcisk palca lub kod PIN, aby odblokować klucz prywatny i zakończyć uwierzytelnianie. Ta metoda weryfikacji wieloskładnikowej jest bezpieczniejsza niż hasło i jest bardziej wygodna niż wprowadzanie hasła i kodu.

![Logowanie przy użyciu uwierzytelniania](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Uwierzytelnianie bez hasła przy użyciu Microsoft Authenticator jest zgodne z tym samym wzorcem podstawowym co usługa Windows Hello dla firm, ale jest nieco bardziej skomplikowane, ponieważ należy zidentyfikować użytkownika, aby umożliwić usłudze Azure AD znalezienie Microsoft Authenticatorj wersji aplikacji służyć.

![Proces uwierzytelniania](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. Użytkownik wprowadza nazwę użytkownika.

2. Usługa Azure AD wykryje, że użytkownik ma silne poświadczenie i uruchamia strumień silnego poświadczenia.

3. Powiadomienia są wysyłane do aplikacji za pośrednictwem Apple Push Notification Service (APNS) na urządzeniach z systemem iOS lub za pośrednictwem usługi Firebase Cloud Messaging (FCM) na urządzeniach z systemem Android.

4. Użytkownik odbiera Powiadomienie wypychane i otwiera aplikację.

5. Aplikacja wywołuje usługę Azure AD i odbiera żądanie potwierdzenia obecności oraz identyfikator jednorazowy.

6. Użytkownik kończy wyzwanie przez wprowadzenie ich biometrycznych lub numerów PIN w celu odblokowania klucza prywatnego.

7. Identyfikator jednorazowy jest podpisywany z kluczem prywatnym i wysyłany z powrotem do usługi Azure AD.

8. Usługa Azure AD wykonuje walidację klucza publicznego/prywatnego i zwraca token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Użytkownik zarządza logowaniem bez hasła przy użyciu poświadczeń Microsoft Authenticator

Dzięki [połączeniu](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)z rejestracją użytkownicy mogą rejestrować i korzystać z zalet uwierzytelniania wieloskładnikowego platformy Azure oraz samoobsługowego resetowania hasła. Użytkownicy rejestrują te ustawienia i zarządzają nimi, przechodząc do [strony mój profil](https://aka.ms/mysecurityinfo). Oprócz włączenia SSPR, rejestracja łączona obsługuje wiele metod i akcji uwierzytelniania.

## <a name="fido2-security-keys"></a>FIDO2 klucze zabezpieczeń

FIDO2 to Najnowsza wersja programu FIDO Alliance Standard i ma dwa składniki-W3C's Web Authentication (WebAuthN) standard i odpowiedni protokół FIDO Alliance Client-to-Authenticator (CTAP2). Standardy FIDO2 umożliwiają użytkownikom korzystanie z wystawców opartych na sprzęcie, urządzeniach przenośnych i biometrycznych w celu łatwego uwierzytelniania w wielu aplikacjach i witrynach sieci Web w środowiskach mobilnych i klasycznych.

Partnerzy firmy Microsoft i branży współpracują ze sobą na urządzeniach zabezpieczeń FIDO2 dla usługi Windows Hello, aby umożliwić łatwe i bezpieczne uwierzytelnianie na urządzeniach udostępnionych. Klucze zabezpieczeń FIDO2 umożliwiają przeprowadzenie poświadczeń i bezpieczne uwierzytelnienie na urządzeniu z systemem Windows 10 przyłączonym do [usługi Azure AD](https://aka.ms/azuread418), które jest częścią organizacji.

Polecenie WebAuthN definiuje interfejs API, który umożliwia tworzenie i wdrażanie mocnego uwierzytelniania bezhasła przez aplikacje i usługi sieci Web. Protokół CTAP umożliwia urządzeniom zewnętrznym, takim jak klucze zabezpieczeń zgodne z FIDO, współdziałanie z WebAuthN i pełnienie jako wystawców. Korzystając z uwierzytelniania w sieci Web, użytkownicy mogą logować się do Usługi online przy użyciu silnych poświadczeń klucza publicznego zamiast haseł. Obecnie pakiet WebAuthN jest obsługiwany w przeglądarce Microsoft Edge, a obsługa programów Chrome i Firefox jest w trakcie opracowywania.

Urządzenia i tokeny, które są zgodne z protokołami FIDO2, WebAuthN i CTAP, zapewniają międzyplatformowe rozwiązanie silnego uwierzytelniania bez używania haseł. Partnerzy firmy Microsoft pracują nad różnymi czynnikami opartymi na kluczach zabezpieczeń, takimi jak klucze zabezpieczeń USB i karty inteligentne obsługujące NFC.

### <a name="fido2-security-keys-scenarios"></a>Scenariusze dotyczące kluczy zabezpieczeń FIDO2

Klucze zabezpieczeń FIDO2 mogą służyć do logowania się do usługi Azure AD przez wybranie klucza zabezpieczeń jako dostawcy poświadczeń na ekranie blokady systemu Windows 10. Nazwa użytkownika lub hasło nie jest wymagane, co sprawia, że jest to idealne rozwiązanie dla pracowników pierwszego wiersza, którzy współdzielą komputery między wieloma użytkownikami. Są one również doskonałą opcją uwierzytelniania, gdy zasady firmowe określają, że poświadczenia użytkownika muszą być fizycznie oddzielone od ich urządzenia. Użytkownicy mogą również zalogować się w witrynach sieci Web przy użyciu klucza zabezpieczeń FIDO2 w przeglądarce Microsoft Edge w systemie Windows 10 w wersji 1809 lub nowszej.

### <a name="fido2-security-keys-deployment-considerations"></a>Zagadnienia dotyczące wdrażania kluczy zabezpieczeń FIDO2

Administratorzy mogą włączyć obsługę FIDO2 w usłudze Azure AD i przypisać możliwość do użytkowników lub grup. Zasady mogą być również tworzone na potrzeby aprowizacji kluczy i konfigurowania ograniczeń, takich jak umożliwienie lub blokowanie określonego zestawu kluczy zabezpieczeń sprzętowych. Klucze muszą być fizycznie dystrybuowane do użytkowników końcowych.

**Wymagania dotyczące włączania logowania bezhasła do usługi Azure AD i witryn sieci Web przy użyciu kluczy zabezpieczeń FIDO2 obejmują następujące elementy:**

* Azure AD

* Azure Multi-Factor Authentication

* Podgląd rejestracji połączonej

* Wersja zapoznawcza klucza zabezpieczeń FIDO2 wymaga zgodnego klucza zabezpieczeń FIDO2

* Uwierzytelnianie w sieci Web (WebAuthN) wymaga przeglądarki Microsoft Edge w systemie Windows 10 w wersji 1809 lub nowszej

* Logowanie systemu Windows opartego na FIDO2 wymaga usługi Azure AD przyłączonej do systemu Windows 10 w wersji 1809 lub nowszej (najlepsze środowisko to w systemie Windows 10 w wersji 1903 lub nowszej)

FIDO2 są zgodne z urządzeniami USB, NFC i Bluetooth. Zalecamy wybranie współczynnika formularza spełniającego określone potrzeby, ponieważ niektóre platformy i przeglądarki nie są jeszcze zgodne z FIDO2.

Zalecamy również, aby każda organizacja utworzyła protokół dla użytkowników i administratorów, w przypadku których zostanie utracony lub skradziony klucz zabezpieczeń. Użytkownicy powinni zgłosić zgubiony lub skradziony klucz, aby administratorzy lub użytkownik mogli usunąć swoje klucze zabezpieczeń z profilu użytkownika i udostępnić nowy.

### <a name="how-fido2-security-keys-works"></a>Jak działają klucze zabezpieczeń FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>Użytkownik konfiguruje klucz zabezpieczeń FIDO2

Administratorzy mogą [ręcznie zainicjować obsługę kluczy](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) i przekazać je do użytkowników końcowych, aprowizacji i włączeniu dostawcy poświadczeń FIDO2 na ekranie blokady systemu Windows 10 będą obsługiwane za pomocą usługi [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Administratorzy muszą również użyć [Azure Portal](https://portal.azure.com/) , aby włączyć urządzenia tokenów sprzętowych jako metodę uwierzytelniania bezhaseł.

Wdrożenie kluczy zabezpieczeń FIDO2 wymaga również, aby użytkownicy rejestrowali swoje klucze przy użyciu [połączonej rejestracji](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Dzięki połączeniu z rejestracją użytkownicy rejestrują się raz i uzyskują korzyści wynikające z uwierzytelniania wieloskładnikowego platformy Azure i resetowania hasła logowania jednokrotnego (SSPR).

Oprócz wybierania tokenu sprzętowego jako domyślnej metody uwierzytelniania wieloskładnikowego zaleca się również wybranie opcji dodatkowej weryfikacji.

* Microsoft Authenticator — powiadomienie

* Aplikacja lub token sprzętowy uwierzytelniania — kod

* Połączenie telefoniczne

* Wiadomość SMS

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Użytkownik korzystający z klucza zabezpieczeń FIDO2 do logowania

FIDO2 zapewnia warstwę abstrakcji między współczynnikiem formularza używanym jako wystawcą uwierzytelnienia a kryptografią klucza publicznego/prywatnego w celu włączenia wbudowanych wystawców uwierzytelniania platformy, takich jak Windows Hello i klucze zabezpieczeń, aby rozpoznać klucz prywatny i dostarczyć klucz publiczny który może być używany jako identyfikator dostępu do zasobów zewnętrznych. Klucze zabezpieczeń FIDO2 są wyposażone w własne wbudowane bezpieczne enklawy, które przechowują klucz prywatny i wymagają, aby kod biometryczny lub numer PIN został odblokowany. Poświadczenia nie mogą być ponownie używane, odtwarzane lub udostępniane między usługami i nie podlegają wyłudzaniu informacji ani atakom MiTM ani naruszeniom serwerów.

![Logowanie FIDO2](./media/ad-passwordless/azure-ad-pwdless-image6.png)

Klucze zabezpieczeń FIDO2 zapewniają bezpieczne uwierzytelnianie, niezależnie od współczynnika formularza. Klucz zabezpieczeń zawiera poświadczenia i powinien być chroniony przy użyciu dodatkowego drugiego czynnika, takiego jak odcisk palca (zintegrowany z kluczem zabezpieczeń) lub numeru PIN, który ma zostać wprowadzony podczas logowania do systemu Windows. Partnerzy firmy Microsoft pracują nad różnymi czynnikami w postaci klucza zabezpieczeń. Niektóre przykłady obejmują klucze zabezpieczeń USB i karty inteligentne obsługujące NFC.

> [!NOTE]
> Klucz zabezpieczeń musi implementować niektóre funkcje i rozszerzenia z protokołu CTAP FIDO2 jako zgodne z [firmą Microsoft](https://aka.ms/fido2securitykeys). Firma Microsoft przetestowała te rozwiązania pod kątem zgodności z systemami Windows 10 i Azure Active Directory.

![Proces logowania FIDO2](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. Użytkownik podłącza urządzenie FIDO2 do komputera.

2. System Windows wykrywa klucz zabezpieczeń FIDO2.

3. System Windows wysyła żądanie uwierzytelnienia.

4. Usługa Azure AD wysyła kopię zapasową.

5. Użytkownik wykonuje swój gest, aby odblokować klucz prywatny zapisany w bezpiecznym enklawy klucz zabezpieczeń FIDO2.

6. Klucz zabezpieczeń FIDO2 podpisuje identyfikator jednorazowy kluczem prywatnym.

7. Żądanie tokenu PRT z podpisanym identyfikatorem nonce jest wysyłane do usługi Azure AD.

8. Usługa Azure AD weryfikuje podpisany identyfikator jednorazowy przy użyciu klucza publicznego FIDO2.

9. Usługa Azure AD zwraca PRT, aby umożliwić dostęp do zasobów lokalnych.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Użytkownik zarządza poświadczeniami klucza zabezpieczeń FIDO2

Podobnie jak w przypadku aplikacji Microsoft Authenticator, zarządzanie poświadczeniami dla kluczy zabezpieczeń FIDO2 opiera się na połączeniu ze sobą przy rejestrowaniu użytkowników końcowych.

## <a name="deciding-a-passwordless-method"></a>Decydowanie o metodzie bezhasło

Wybór między tymi trzema opcjami bezhaseł zależy od zabezpieczeń, platformy i wymagań aplikacji firmy.

Oto kilka czynników, które należy wziąć pod uwagę podczas wybierania technologii firmy Microsoft bez hasła:

||**Windows Hello dla firm**|**Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator**|**FIDO2 klucze zabezpieczeń**|
|:-|:-|:-|:-|
|**Wymagania wstępne**| Windows 10, wersja 1809 lub nowsza<br>Usługa Azure Active Directory| Aplikacja Microsoft Authenticator<br>Telefon (urządzenia z systemem iOS i Android z systemem Android 6,0 lub nowszym)|Windows 10, wersja 1809 lub nowsza<br>Usługa Azure Active Directory|
|**Wyst**|Platforma|Oprogramowanie|Sprzęt|
|**Systemy i urządzenia**|KOMPUTER z wbudowaną moduł TPM (TPM)<br>Rozpoznawanie kodu PIN i biometrii |Rozpoznawanie kodu PIN i biometrii na telefonie|FIDO2 urządzenia zabezpieczające zgodne z firmą Microsoft|
|**Środowisko użytkownika**|Zaloguj się przy użyciu kodu PIN lub rozpoznawania biometrycznego (twarzy, Iris lub odcisku palca) z urządzeniami z systemem Windows.<br>Uwierzytelnianie przy użyciu systemu Windows Hello jest powiązane z urządzeniem; Aby uzyskać dostęp do zasobów firmy, użytkownik potrzebuje zarówno urządzenia, jak i składnika logowania, takiego jak kod PIN lub czynnik biometryczny.|Zaloguj się przy użyciu telefonu komórkowego z funkcją skanowania odcisków palców, rozpoznawania twarzy lub tęczówki lub numeru PIN.<br>Użytkownicy logują się do konta służbowego lub osobistego z komputera lub telefonu komórkowego.|Zaloguj się przy użyciu urządzenia zabezpieczeń FIDO2 (biometrii, kod PIN i NFC)<br>Użytkownik może uzyskać dostęp do urządzenia w oparciu o kontrolki organizacji i uwierzytelniać się na podstawie kodu PIN, biometrii przy użyciu urządzeń, takich jak klucze zabezpieczeń USB i karty inteligentne obsługujące NFC, klucze lub noszenia.|
|**Włączone scenariusze**| Korzystanie z urządzenia z systemem Windows bez hasła.<br>Dotyczy dedykowanego komputera służbowego z możliwością logowania jednokrotnego do urządzenia i aplikacji.|Rozwiązanie z obsługą hasła bez miejsca przy użyciu telefonu komórkowego.<br>Dotyczy uzyskiwania dostępu do służbowych aplikacji w sieci Web z dowolnego urządzenia.|Środowisko pracy bez hasła dla pracowników korzystających z biometrii, numeru PIN i NFC.<br>Dotyczy komputerów udostępnionych i lokalizacji, w których telefon komórkowy nie jest żywotną opcją (na przykład dla personelu pomocy technicznej, kiosku publicznego lub zespołu szpitalowego)|

Skorzystaj z poniższej tabeli, aby wybrać metodę, która będzie obsługiwać Twoje wymagania i użytkowników.

|Osoba|Scenariusz|Środowisko|Technologia bezhasło|
|:-|:-|:-|:-|
|**Administratora**|Zabezpieczanie dostępu do urządzenia na potrzeby zadań zarządzania|Przypisane urządzenie z systemem Windows 10|Klucz zabezpieczeń usługi Windows Hello dla firm i/lub FIDO2|
|**Administratora**|Zadania zarządzania na urządzeniach z systemem innym niż Windows| Urządzenie przenośne lub inne niż systemu Windows|Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator|
|**Pracownik przetwarzający informacje**|Praca w produktywności|Przypisane urządzenie z systemem Windows 10|Klucz zabezpieczeń usługi Windows Hello dla firm i/lub FIDO2|
|**Pracownik przetwarzający informacje**|Praca w produktywności| Urządzenie przenośne lub inne niż systemu Windows|Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator|
|**Teraźniejszości proces roboczy**|Kioski w fabryce, zakładzie, sprzedaży detalicznej lub danych|Udostępnione urządzenia z systemem Windows 10|FIDO2 klucze zabezpieczeń|

## <a name="getting-started"></a>Wprowadzenie

Uwierzytelnianie bez hasła to fala przyszłości i ścieżka do bardziej bezpiecznego środowiska. Zaleca się, aby organizacje mogli rozpocząć planowanie tej zmiany i zmniejszyć ich zależności od haseł. Aby rozpocząć, należy wziąć pod uwagę następujące cele:

* Zezwól użytkownikom na uwierzytelnianie MFA + Microsoft Authenticator aplikacji i dostęp warunkowy.

* Wdrażanie ochrony hasłem usługi Azure AD i zasad aktualizacji.

* Umożliwienie użytkownikom SSPR z rejestracją łączoną.

* Wdróż aplikację Microsoft Authenticator na potrzeby mobilności.

* Wdróż usługę Windows Hello dla firm (1903: Zostań aktualną).

* Wdróż urządzenia FIDO2 dla użytkowników, którzy nie mogą korzystać z telefonów.

* Jeśli to możliwe, wyłącz uwierzytelnianie oparte na hasłach.

Aby osiągnąć te cele, zalecamy następujące podejście:

1. Włącz Azure Active Directory, aby w pełni korzystać z funkcji, takich jak Azure MFA i dostęp warunkowy.

2. Włączenie usługi uwierzytelniania wieloskładnikowego w celu zapewnienia dodatkowej ochrony.

3. Włącz Samoobsługowe resetowanie hasła w przypadku, gdy użytkownicy zdarzeń muszą wrócić do korzystania z hasła.

4. Wdróż Microsoft Authenticator logowanie do telefonu w celu dodania mobilności.

5. Wdróż usługę Windows Hello dla firm na wszystkich urządzeniach z systemem Windows 10.

6. Przygotuj się do kluczy zabezpieczeń FIDO2.

> [!NOTE]
> Zapoznaj się ze [stroną licencjonowania](https://azure.microsoft.com/pricing/details/active-directory/) Azure Active Directory, aby uzyskać szczegółowe informacje na temat wymagań dotyczących licencjonowania dla metod bezhaseł.

## <a name="conclusion"></a>Wniosek

W ciągu ostatnich kilku lat firma Microsoft kontynuowała zaangażowanie w włączenie świata bez hasła. W systemie Windows 10 firma Microsoft wprowadziła funkcję Windows Hello dla firm, silne, chronione przez klienta poświadczenie dwuskładnikowe umożliwiające Logowanie jednokrotne do Azure Active Directory i Active Directory. Podobnie jak w przypadku technologii Windows Hello dla firm, aplikacja Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby umożliwić poświadczenie użytkownika powiązanego z urządzeniem przenośnym i użycie biometrycznych lub PIN. Teraz FIDO2 klucze zabezpieczeń umożliwiają przeprowadzenie poświadczeń i zalogowanie się do usługi Azure AD, wybierając klucz zabezpieczeń jako dostawcę poświadczeń na ekranie blokady systemu Windows 10. Wszystkie trzy z tych rozwiązań bez hasła zmniejszają ryzyko wyłudzania informacji, odrozpylania hasła i ponownego rozegrania oraz zapewniania użytkownikom wysoce bezpiecznego i wygodnego sposobu logowania i uzyskiwania dostępu do danych z dowolnego miejsca.

Wdrożenie nowoczesnych technologii usługi uwierzytelniania wieloskładnikowego, takich jak biometry i Kryptografia klucza publicznego w przypadku powszechnie dostępnych urządzeń, jest jednym z najbardziej wpływających czynności, które mogą w znaczący sposób ograniczyć ryzyko związane z tożsamościami firmy. Przechodzenie między innymi hasłami to długoterminowe podejście do bezpiecznego uwierzytelniania i nadal trwające rozwój. Uwzględniając nowe wymagania, organizacje mogą samodzielnie przyczyniać się do rozpoczęcia pracy z technologiami bezhasłem.

## <a name="next-steps"></a>Następne kroki

* Omówienie tego, [co to jest bezhasło?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Jak włączyć bezhasło w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
