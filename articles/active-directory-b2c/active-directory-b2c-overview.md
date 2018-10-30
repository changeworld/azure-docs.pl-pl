---
title: Co to jest usługa Azure Active Directory B2C? | Microsoft Docs
description: Dowiedz się, jak utworzyć środowisko logowania w aplikacji i zarządzać nim przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/23/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fdb856f92bf790df0065cfc74ce5896f1d10c47b
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954110"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co to jest usługa Azure Active Directory B2C?

Usługa Azure Active Directory (Azure AD) B2C jest usługą zarządzania tożsamościami, która pozwala na dostosowywanie i kontrolowanie sposobu, w jaki klienci korzystają z Twojej aplikacji. Wykonywane działania obejmują tworzenie konta, logowanie się i zarządzanie profilami, gdy klienci korzystają z aplikacji. Możesz wybrać aplikacje m.in. dla systemu iOS i Android oraz platformy .NET. Usługa Azure AD B2C umożliwia wykonywanie tych akcji przy jednoczesnej ochronie tożsamości klientów.

Aplikację zarejestrowaną w usłudze Azure AD B2C należy skonfigurować tak, aby wykonywała wiele zadań zarządzania tożsamościami. Przykłady to:

- Umożliwienie klientom rejestrowania się w celu korzystania z zarejestrowanej aplikacji
- Umożliwienie zarejestrowanym klientom zalogowania się i rozpoczęcia korzystania z aplikacji
- Umożliwienie zarejestrowanym klientom edytowania swoich profili
- Umożliwienie zastosowania uwierzytelniania wieloskładnikowego w aplikacji
- Umożliwienie klientom rejestrowania się i logowania za pomocą określonych dostawców tożsamości
- Udzielenie dostępu do tworzonych interfejsów API z poziomu aplikacji
- Dostosowanie wyglądu i sposobu działania procesu rejestracji i logowania
- Zarządzanie sesjami rejestracji jednokrotnej w aplikacji

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Co należy wziąć pod uwagę przed rozpoczęciem korzystania z usługi Azure AD B2C?

- Jak powinna wyglądać interakcja klienta z moją aplikacją?
- Jaki interfejs użytkownika chcę zapewnić klientom?
- Których dostawców tożsamości chcę udostępnić klientom w mojej aplikacji?
- Czy proces logowania wymaga uruchomienia dodatkowych interfejsów API?

### <a name="customer-interaction"></a>Interakcje klientów

Usługa Azure AD B2C obsługuje protokół [OpenID Connect](https://openid.net/connect/) niezależnie od środowiska, jakiego używa klient. Podczas wdrażania protokołu OpenID Connect w usłudze Azure AD B2C aplikacja rozpoczyna podróż użytkownika, wysyłając żądania uwierzytelniania do usługi Azure AD B2C. Wynikiem żądania jest token `id_token`. Ten token zabezpieczający definiuje tożsamość klienta.

Każda aplikacja, która używa usługi Azure AD B2C, musi być zarejestrowana w dzierżawie usługi Azure AD B2C za pomocą witryny Azure Portal. Proces rejestracji polega na zgromadzeniu wartości i przypisaniu ich do aplikacji. Te wartości obejmują unikatowy identyfikator aplikacji. Jest zdefiniowany identyfikator URI przekierowania umożliwiający kierowanie odpowiedzi z powrotem do aplikacji.

Interakcja każdej aplikacji jest realizowana według następującego ogólnego schematu:

1. Aplikacja zwraca się do klienta o uruchomienie zasady.
2. Klient wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja odbiera token zabezpieczający.
4. Aplikacja używa tokenu zabezpieczającego, aby podjąć próbę uzyskania dostępu do chronionego zasobu.
5. Serwer zasobów weryfikuje token zabezpieczający, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token zabezpieczający.

Te kroki nieznacznie różnią się w zależności od typu tworzonej aplikacji.

Usługa Azure AD B2C wchodzi w interakcję z dostawcami tożsamości, klientami i innymi systemami oraz z katalogiem lokalnym w sekwencji, aby zakończyć zadanie związane z tożsamością. Na przykład zalogować klienta, zarejestrować nowego klienta lub zresetować hasło. Podstawowa platforma, która ustanawia relację zaufania wielu jednostek zależnych i wykonuje poszczególne czynności, nosi nazwę struktury środowiska tożsamości. Ta struktura i zasady (nazywane również podróżą użytkownika lub zasadami struktury zaufania) jawnie definiuje aktorów, akcje, protokoły i sekwencje kroków wymagające ukończenia.

Usługa Azure AD B2C chroni aplikacje przed atakami typu „odmowa usługi” i próbami złamania hasła. Usługa Azure AD B2C używa technik wykrywania i łagodzenia skutków ataków typu „odmowa usługi”, takich jak pliki cookie SYN oraz limity szybkości i połączenia. Techniki łagodzenia są również stosowane w przypadku ataków siłowych i słownikowych ukierunkowanych na złamanie hasła.

#### <a name="built-in-policies"></a>Wbudowane zasady

Każde żądanie wysłane do usługi Azure AD B2C określa zasady. Zasady sterują interakcjami aplikacji z usługą Azure AD B2C. Wbudowane zasady są wstępnie zdefiniowane w przypadku większości typowych zadań związanych z tożsamością, takich jak rejestrowanie, logowanie i edytowanie profilu.  Na przykład zasada rejestracji pozwala na kontrolowanie zachowania dzięki skonfigurowaniu następujących ustawień:

- Konta społecznościowe, których klient używa do zarejestrowania się w aplikacji
- Dane zbierane od klientów, takie jak imię i nazwisko lub kod pocztowy
- Uwierzytelnianie wieloskładnikowe
- Wygląd i działanie wszystkich stron rejestracji
- Informacje zwrócone do aplikacji

#### <a name="custom-policies"></a>Zasady niestandardowe 

[Zasady niestandardowe](active-directory-b2c-overview-custom.md) to pliki konfiguracji definiujące zachowanie [platformy środowiska tożsamości](trustframeworkpolicy.md) w dzierżawie usługi Azure AD B2C. Zasady niestandardowe mogą być modyfikowane, dzięki czemu mogą wykonywać wiele zadań. Niestandardowe zasady są to jeden lub kilka plików w formacie XML, które odwołują się do siebie nawzajem zgodnie z łańcuchem hierarchii. 

W razie potrzeby w dzierżawie usługi Azure AD B2C są używane zasady niestandardowe różnego typu. Można użyć ich ponownie w innych aplikacjach. Ta elastyczność umożliwia definiowanie i modyfikowanie obsługi tożsamości klienta przy zerowych lub jedynie minimalnych zmianach w kodzie. Aby użyć zasad, dodaj określony parametr zapytania do żądań uwierzytelnienia HTTP.

Zasady niestandardowe służą do kontrolowania podróży użytkownika w następujący sposób:

- Definiując interakcje z interfejsami API w celu przechwycenia dodatkowych informacji, sprawdzając oświadczenia dostarczane przez klienta lub wyzwalając zewnętrzne procesy.
- Zmieniając zachowanie na podstawie oświadczeń interfejsów API lub oświadczeń w katalogu, takim jak *migrationStatus*.
- Każdy przepływ pracy nieobjęty wbudowanymi zasadami. Na przykład zbieranie informacji podanych przez klienta podczas procesu logowania i sprawdzanie autoryzacji w celu uzyskania dostępu do zasobu.

### <a name="identity-providers"></a>Dostawcy tożsamości

Dostawca tożsamości to usługa służąca do uwierzytelniania tożsamości klienta i wystawiania tokenów zabezpieczających. W usłudze Azure AD B2C należy skonfigurować różnych dostawców tożsamości w dzierżawie, na przykład [konto Microsoft](active-directory-b2c-setup-msa-app.md), [Facebook](active-directory-b2c-setup-fb-app.md) lub [Amazon](active-directory-b2c-setup-amzn-app.md). 

Aby skonfigurować dostawcę tożsamości w dzierżawie usługi Azure AD B2C, należy zarejestrować identyfikator aplikacji lub identyfikator klienta oraz hasło lub klucz tajny klienta z poziomu tworzonej aplikacji dostawcy tożsamości. Ten identyfikator i hasło są następnie używane do konfigurowania aplikacji.

### <a name="user-interface-experience"></a>Środowisko interfejsu użytkownika

Większość zawartości HTML i CSS przekazywanej klientom może być kontrolowana. Za pomocą funkcji dostosowywania strony interfejsu użytkownika można dostosować wygląd i sposób działania dowolnych zasad. Ta funkcja dostosowywania pozwala zapewnić spójność wizerunku marki i wrażeń wizualnych między aplikacją i usługą Azure AD B2C.

Usługa Azure AD B2C uruchamia kod w przeglądarce klienta i wykorzystuje nowoczesne podejście nazywane współużytkowaniem zasobów między źródłami (cross-origin resource sharing — CORS). Najpierw należy określić adres URL w zasadach z dostosowaną zawartością HTML. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę klientowi.

Parametry są wysłane do usługi Azure AD B2C w postaci ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartości strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Jak rozpocząć pracę z usługą Azure AD B2C?

W usłudze Azure AD B2C dzierżawa reprezentuje Twoją organizację i jest katalogiem użytkowników. Każda dzierżawa usługi Azure AD B2C jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD B2C. Dzierżawa zawiera informacje dotyczące klientów, którzy utworzyli konto w celu korzystania z aplikacji. Na przykład hasła, dane profilu i uprawnienia.

W celu uzyskania pełnej funkcjonalności i dokonania opłat za zużycie połącz dzierżawę usługi Azure AD B2C z subskrypcją platformy Azure. Aby umożliwić klientom logowanie się do aplikacji, zarejestruj ją w dzierżawie usługi Azure AD B2C.

Przed rozpoczęciem konfigurowania aplikacji do korzystania z usługi Azure AD B2C należy utworzyć dzierżawę usługi Azure AD B2C i zarejestrować aplikację. Aby zarejestrować aplikację, wykonaj kroki opisane w artykule [Tutorial: Register an application to enable sign-up and sign-in using Azure AD B2C (Samouczek: rejestrowanie aplikacji w celu umożliwienia przeprowadzenia procesu rejestracji i logowania przy użyciu usługi Azure AD B2C)](tutorial-register-applications.md).
  
Jeśli jesteś deweloperem aplikacji internetowych platformy ASP.NET, skonfiguruj aplikację do uwierzytelniania kont, wykonując kroki opisane w artykule [Samouczek: włączanie uwierzytelniania aplikacji internetowej przy użyciu kont w usłudze Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Jeśli jesteś deweloperem aplikacji klasycznych, skonfiguruj aplikację do uwierzytelniania kont, wykonując kroki opisane w artykule [Samouczek: włączanie uwierzytelniania aplikacji klasycznej przy użyciu kont w usłudze Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Jeśli jesteś deweloperem aplikacji jednostronicowych i korzystasz ze środowiska Node.js, skonfiguruj aplikację do uwierzytelniania kont, wykonując kroki opisane w artykule [Samouczek: włączanie uwierzytelniania aplikacji jednostronicowej przy użyciu kont w usłudze Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Następne kroki

Rozpocznij konfigurowanie aplikacji dla funkcji rejestracji i logowania, przechodząc do samouczka.

> [!div class="nextstepaction"]
> [Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C](tutorial-create-tenant.md)
