---
title: Logowanie bez hasła usługi Azure Active Directory (wersja zapoznawcza)
description: Dowiedz się więcej o opcjach logowania bez hasła do usługi Azure Active Directory przy użyciu kluczy zabezpieczeń FIDO2 lub aplikacji Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 402eaecbf03fd52fbb5e871fdd196da2bc9a3e1f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743535"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opcje uwierzytelniania bez hasła dla usługi Azure Active Directory

Uwierzytelnianie wieloskładnikowe (MFA) to świetny sposób na zabezpieczenie organizacji, ale użytkownicy często są sfrustrowani dodatkową warstwą zabezpieczeń, która musi zapamiętywać swoje hasła. Metody uwierzytelniania bez hasła są wygodniejsze, ponieważ hasło jest usuwane i zastępowane czymś, co masz, plus coś, czym jesteś lub coś, co znasz.

|   | Coś, co masz | Coś, co jesteś lub wiesz |
| --- | --- | --- |
| Logowanie bez hasła | Windows 10 Urządzenie, telefon lub klucz zabezpieczeń | Dane biometryczne lub PIN |

Każda organizacja ma inne potrzeby, jeśli chodzi o uwierzytelnianie. Firma Microsoft oferuje następujące trzy opcje uwierzytelniania bez hasła, które integrują się z usługą Azure Active Directory (Azure AD):

- Windows Hello dla firm
- Aplikacja Microsoft Authenticator
- Klucze zabezpieczeń FIDO2

![Uwierzytelnianie: bezpieczeństwo a wygoda](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello dla firm

Funkcja Windows Hello dla firm jest idealna dla pracowników zajmujących się informacjami, którzy mają własny, wyznaczony komputer z systemem Windows. Dane biometryczne i PIN są bezpośrednio powiązane z komputerem użytkownika, co uniemożliwia dostęp nikomu innemu niż właściciel. Dzięki integracji infrastruktury kluczy publicznych (PKI) i wbudowanej obsłudze logowania jednokrotnego (SSO) funkcja Windows Hello dla firm zapewnia wygodną metodę bezproblemowego uzyskiwania dostępu do zasobów firmowych lokalnie i w chmurze.

![Przykład logowania użytkownika za pomocą funkcji Windows Hello dla firm](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Poniższe kroki pokazują, jak proces logowania współpracuje z usługą Azure Active Directory.

![Diagram przedstawiający kroki związane z logowaniem użytkownika za pomocą funkcji Windows Hello dla firm](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Użytkownik loguje się do systemu Windows za pomocą gestu biometrycznego lub pinowego. Gest odblokowuje klucz prywatny Windows Hello dla firm i jest wysyłany do dostawcy pomocy technicznej zabezpieczeń uwierzytelniania w chmurze, określanego jako *dostawca usługi Cloud AP*.
1. Dostawca usługi CLOUD AP żąda nonce z usługi Azure AD.
1. Usługa Azure AD zwraca wartość ściąłą przez 5 minut.
1. Dostawca usługi Cloud AP podpisuje wartość ścięcie przy użyciu klucza prywatnego użytkownika i zwraca podpisaną wartość nonce do usługi Azure AD.
1. Usługa Azure AD sprawdza poprawność podpisanego identyfikatora przy użyciu bezpiecznie zarejestrowanego klucza publicznego użytkownika względem podpisu niebędąc.ego. Po sprawdzeniu poprawności podpisu usługa Azure AD sprawdza poprawność zwróconego podpisanego umowy nonce. Po weryfikacji nonce usługi Azure AD tworzy podstawowy token odświeżania (PRT) z kluczem sesji, który jest zaszyfrowany do klucza transportu urządzenia i zwraca go do dostawcy usługi Cloud AP.
1. Dostawca usługi Cloud AP odbiera zaszyfrowany PRT z kluczem sesji. Korzystając z prywatnego klucza transportu urządzenia, dostawca usługi Cloud AP odszyfrowuje klucz sesji i chroni klucz sesji przy użyciu modułu TPM (Trusted Platform Module) urządzenia.
1. Dostawca usługi Cloud AP zwraca pomyślną odpowiedź uwierzytelniania do systemu Windows. Użytkownik może następnie uzyskać dostęp do systemu Windows, a także aplikacji w chmurze i lokalnych bez konieczności ponownego uwierzytelniania (SSO).

[Przewodnik planowania](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) funkcji Windows Hello dla firm może pomóc w podejmowaniu decyzji dotyczących typu wdrożenia usługi Windows Hello dla firm i opcji, które należy wziąć pod uwagę.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Zezwalaj na to, aby telefon pracownika stał się metodą uwierzytelniania bez hasła. Być może oprócz hasła jest już używane aplikacja Microsoft Authenticator jako wygodna opcja uwierzytelniania wieloskładnikowego. Możesz również użyć aplikacji Authenticator jako opcji bez hasła.

![Logowanie się do przeglądarki Microsoft Edge za pomocą aplikacji Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Aplikacja Authenticator zamienia dowolny telefon z systemem iOS lub Android w silne, bez hasła poświadczenia. Użytkownicy mogą zalogować się na dowolnej platformie lub przeglądarce, uzyskując powiadomienie na swój telefon, dopasowując numer wyświetlany na ekranie do numeru w telefonie, a następnie używając ich biometrycznego (dotyk lub twarz) lub kodu PIN do potwierdzenia. Szczegółowe informacje dotyczące instalacji można znaleźć w witrynie [Pobierz i zainstaluj aplikację Microsoft Authenticator.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)

Uwierzytelnianie bez hasła przy użyciu aplikacji Authenticator jest zgodne z tym samym podstawowym wzorcem, co Windows Hello dla firm. Jest to nieco bardziej skomplikowane, ponieważ użytkownik musi zostać zidentyfikowany, aby usługa Azure AD mogła znaleźć używaną wersję aplikacji Microsoft Authenticator:

![Diagram przedstawiający kroki związane z logowaniem użytkownika za pomocą aplikacji Microsoft Authenticator](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. Użytkownik wprowadzi swoją nazwę użytkownika.
1. Usługa Azure AD wykrywa, że użytkownik ma silne poświadczenia i uruchamia przepływ silnych poświadczeń.
1. Powiadomienie jest wysyłane do aplikacji za pośrednictwem usługi apple push notification service (APNS) na urządzeniach z systemem iOS lub za pośrednictwem firebase cloud messaging (FCM) na urządzeniach z systemem Android.
1. Użytkownik otrzymuje powiadomienie wypychane i otwiera aplikację.
1. Aplikacja wywołuje usługę Azure AD i odbiera wyzwanie weryfikacji obecności i nonce.
1. Użytkownik wypełnia wyzwanie, wprowadzając swój kod biometryczny lub PIN, aby odblokować klucz prywatny.
1. Element niebędący jest podpisany za pomocą klucza prywatnego i odesłany do usługi Azure AD.
1. Usługa Azure AD wykonuje sprawdzanie poprawności klucza publicznego/prywatnego i zwraca token.

## <a name="fido2-security-keys"></a>Klucze zabezpieczeń FIDO2

Klucze bezpieczeństwa FIDO2 to niesafinowana metoda uwierzytelniania bez hasła oparta na standardach, która może być w dowolnym rozmiarze. Fast Identity Online (FIDO) to otwarty standard uwierzytelniania bez hasła. FIDO umożliwia użytkownikom i organizacjom korzystanie ze standardu w celu zalogowania się do swoich zasobów bez nazwy użytkownika lub hasła przy użyciu zewnętrznego klucza zabezpieczeń lub klucza platformy wbudowanego w urządzenie.

W przypadku publicznej wersji zapoznawczej pracownicy mogą używać kluczy zabezpieczeń do logowania się na urządzeniach z systemem Windows 10 przy użyciu usługi Azure AD lub hybrydowej usługi Azure AD i podpisywania się z jednym znakiem w swoich zasobach w chmurze i lokalnie. Użytkownicy mogą również logować się do obsługiwanych przeglądarek. Klucze bezpieczeństwa FIDO2 są doskonałym rozwiązaniem dla przedsiębiorstw, które są bardzo wrażliwe na bezpieczeństwo lub mają scenariusze lub pracowników, którzy nie chcą lub nie mogą korzystać z telefonu jako drugiego czynnika.

![Logowanie się do przeglądarki Microsoft Edge przy za pomocą klucza zabezpieczeń](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Następujący proces jest używany, gdy użytkownik loguje się za pomocą klucza zabezpieczeń FIDO2:

![Diagram przedstawiający kroki związane z logowaniem użytkownika za pomocą klucza zabezpieczeń FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. Użytkownik podłącza klucz zabezpieczeń FIDO2 do swojego komputera.
2. System Windows wykrywa klucz zabezpieczeń FIDO2.
3. System Windows wysyła żądanie uwierzytelnienia.
4. Usługa Azure AD wysyła z powrotem nonce.
5. Użytkownik kończy swój gest, aby odblokować klucz prywatny przechowywany w bezpiecznej enklawie klucza bezpieczeństwa FIDO2.
6. Klucz zabezpieczeń FIDO2 podpisuje element podrzędny za pomocą klucza prywatnego.
7. Żądanie tokenu tokenu pierwotnego odświeżania (PRT) z podpisanym identyfikatorem umowy nonce jest wysyłane do usługi Azure AD.
8. Usługa Azure AD weryfikuje podpisane umowynie z wykorzystaniem klucza publicznego FIDO2.
9. Usługa Azure AD zwraca PRT, aby umożliwić dostęp do zasobów lokalnych.

Chociaż istnieje wiele kluczy, które są CERTYFIKOWANE FIDO2 przez FIDO Alliance, Firma Microsoft wymaga niektórych opcjonalnych rozszerzeń fido2 client-to-Authenticator Protocol (CTAP) specyfikacji mają być zaimplementowane przez dostawcę w celu zapewnienia maksymalnego bezpieczeństwa i najlepsze środowisko.

Klucz zabezpieczeń **MUSI** implementować następujące funkcje i rozszerzenia z protokołu CTAP FIDO2, aby były zgodne z firmą Microsoft:

| # | Funkcja / Zaufanie rozszerzenia | Dlaczego ta funkcja lub rozszerzenie jest wymagane? |
| --- | --- | --- |
| 1 | Klucz rezydenta | Ta funkcja umożliwia przenośne przenoszenie klucza zabezpieczeń, w którym poświadczenia są przechowywane na kluczu zabezpieczeń. |
| 2 | Pin klienta | Ta funkcja umożliwia ochronę poświadczeń za pomocą drugiego czynnika i ma zastosowanie do kluczy zabezpieczeń, które nie mają interfejsu użytkownika. |
| 3 | hmac-tajne | To rozszerzenie gwarantuje, że możesz zalogować się do urządzenia, gdy jest ono wyłączone lub w trybie samolotowym. |
| 4 | Wiele kont na RP | Ta funkcja zapewnia, że można używać tego samego klucza zabezpieczeń w wielu usługach, takich jak konto Microsoft i usługa Azure Active Directory. |

Następujący dostawcy oferują klucze bezpieczeństwa FIDO2 o różnych rozmiarach, o których wiadomo, że są zgodne z funkcją bez hasła. Zachęcamy do oceny właściwości zabezpieczeń tych kluczy, kontaktując się ze sprzedawcą, a także FIDO Alliance.

| Dostawca | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian (Feitian) | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| Hid | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Zapewnienie | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend (AuthenTrend) | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Grupa Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Sp. z o.o. | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Jeśli kupujesz i planujesz używać kluczy bezpieczeństwa opartych na technologii NFC, potrzebujesz obsługiwanego czytnika NFC dla klucza zabezpieczeń. Czytnik NFC nie jest wymaganiem ani ograniczeniem platformy Azure. Aby uzyskać listę obsługiwanych czytników NFC, skontaktuj się z dostawcą, aby uzyskać klucz zabezpieczeń oparty na technologii NFC.

Jeśli jesteś dostawcą i chcesz, aby urządzenie było na tej [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)liście obsługiwanych urządzeń, skontaktuj się z nim .

## <a name="what-scenarios-work-with-the-preview"></a>Jakie scenariusze działają z podglądem?

- Administratorzy mogą włączyć metody uwierzytelniania bez hasła dla swojej dzierżawy
- Administratorzy mogą kierować reklamy do wszystkich użytkowników lub wybierać użytkowników/grup w dzierżawie dla każdej metody
- Użytkownicy końcowi mogą rejestrować te metody uwierzytelniania bez hasła i zarządzać nimi w portalu kont
- Użytkownicy końcowi mogą logować się przy użyciu tych metod uwierzytelniania bez hasła
   - Aplikacja Microsoft Authenticator: działa w scenariuszach, w których jest używane uwierzytelnianie usługi Azure AD, w tym we wszystkich przeglądarkach, podczas konfiguracji systemu Windows 10 Out Of Box (OOBE) oraz ze zintegrowanymi aplikacjami mobilnymi w dowolnym systemie operacyjnym.
   - Klucze zabezpieczeń: Pracuj na ekranie blokady systemu Windows 10 i w sieci Web w obsługiwanych przeglądarkach, takich jak Microsoft Edge (zarówno starsza, jak i nowa krawędź).

## <a name="choose-a-passwordless-method"></a>Wybierz metodę bez hasła

Wybór między tymi trzema opcjami bez hasła zależy od wymagań firmy dotyczących zabezpieczeń, platformy i aplikacji.

Oto kilka czynników, które należy wziąć pod uwagę przy wyborze technologii bez hasła firmy Microsoft:

||**Windows Hello dla firm**|**Logowanie bez hasła za pomocą aplikacji Microsoft Authenticator**|**Klucze zabezpieczeń FIDO2**|
|:-|:-|:-|:-|
|**Warunek wstępny**| Windows 10, wersja 1809 lub nowsza<br>Usługa Azure Active Directory| Aplikacja Microsoft Authenticator<br>Telefon (urządzenia z systemem iOS i Android z systemem Android 6.0 lub wyższym).|Windows 10, wersja 1809 lub nowsza<br>Usługa Azure Active Directory|
|**Tryb**|Platforma|Oprogramowanie|Sprzęt|
|**Systemy i urządzenia**|Komputer z wbudowanym modułem tpm (Trusted Platform Module)<br>Rozpoznawanie kodu PIN i biometrii |Rozpoznawanie kodu PIN i biometrii w telefonie|Urządzenia zabezpieczające FIDO2 zgodne z firmą Microsoft|
|**Środowisko użytkownika**|Zaloguj się przy użyciu numeru PIN lub rozpoznawania biometrycznego (twarzy, tęczówki lub odcisku palca) za pomocą urządzeń z systemem Windows.<br>Uwierzytelnianie funkcji Windows Hello jest powiązane z urządzeniem; użytkownik potrzebuje zarówno urządzenia, jak i składnika logowania, takiego jak kod PIN lub współczynnik biometryczny, aby uzyskać dostęp do zasobów firmowych.|Zaloguj się za pomocą telefonu komórkowego za pomocą skanowania linii papilarnych, rozpoznawania twarzy lub tęczówki lub numeru PIN.<br>Użytkownicy logują się do konta służbowego lub osobistego na komputerze lub telefonie komórkowym.|Zaloguj się przy użyciu urządzenia zabezpieczającego FIDO2 (biometria, kod PIN i NFC)<br>Użytkownik może uzyskać dostęp do urządzenia na podstawie kontroli organizacji i uwierzytelniać się na podstawie numeru PIN, danych biometrycznych przy użyciu urządzeń, takich jak klucze zabezpieczeń USB i karty inteligentne obsługujące technologię NFC, klucze lub urządzenia do noszenia.|
|**Włączone scenariusze**| Bez hasła środowisko z urządzeniem z systemem Windows.<br>Dotyczy dedykowanego komputera służbowego z możliwością logowania jednokrotnego na urządzeniu i aplikacje.|Bez hasła rozwiązanie w dowolnym miejscu za pomocą telefonu komórkowego.<br>Ma zastosowanie do uzyskiwania dostępu do aplikacji służbowych lub osobistych w internecie z dowolnego urządzenia.|Korzystanie z funkcji bez hasła dla pracowników korzystających z danych biometrycznych, numeru PIN i nfc.<br>Dotyczy współdzielonych komputerów i gdy telefon komórkowy nie jest realną opcją (na przykład dla personelu punktu pomocy technicznej, kiosku publicznego lub zespołu szpitalnego)|

Skorzystaj z poniższej tabeli, aby wybrać metodę, która będzie obsługiwać twoje wymagania i użytkowników.

|Persona|Scenariusz|Środowisko|Technologia bez hasła|
|:-|:-|:-|:-|
|**Administracja**|Bezpieczny dostęp do urządzenia do zadań zarządzania|Przypisane urządzenie z systemem Windows 10|Klucz zabezpieczeń Windows Hello dla firm i/lub FIDO2|
|**Administracja**|Zadania zarządzania na urządzeniach innych niż Windows| Urządzenie mobilne lub inne niż windows|Logowanie bez hasła za pomocą aplikacji Microsoft Authenticator|
|**Pracownik informacji**|Praca z wydajnością|Przypisane urządzenie z systemem Windows 10|Klucz zabezpieczeń Windows Hello dla firm i/lub FIDO2|
|**Pracownik informacji**|Praca z wydajnością| Urządzenie mobilne lub inne niż windows|Logowanie bez hasła za pomocą aplikacji Microsoft Authenticator|
|**Pracownik linii frontu**|Kioski w fabryce, zakładzie, sprzedaży detalicznej lub wprowadzaniu danych|Udostępnione urządzenia z systemem Windows 10|Klucze zabezpieczeń FIDO2|

## <a name="next-steps"></a>Następne kroki

[Włączanie opcji bez hasła bez hasła klucza zabezpieczeń FIDO2 w organizacji](howto-authentication-passwordless-security-key.md)

[Włączanie opcji bez hasła w organizacji opartych na telefonie](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Linki zewnętrzne

[Sojusz FIDO](https://fidoalliance.org/)

[Fido2 CTAP specyfikacja](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
