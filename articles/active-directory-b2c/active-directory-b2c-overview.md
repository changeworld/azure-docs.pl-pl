---
title: Co to jest usługa Azure Active Directory B2C? | Microsoft Docs
description: Dowiedz się, jak tworzyć środowiska tożsamości i nimi zarządzać, na przykład środowiska tworzenia kont i logowania oraz zarządzania profilami w aplikacji, za pomocą usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cceac260979b4322d41843038eab0998c8e8ba4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509746"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co to jest usługa Azure Active Directory B2C?

Usługa Azure Active Directory (Azure AD) B2C to usługa zarządzania tożsamościami firma klient. Usługa ta umożliwia dostosowywanie i kontrolowanie bezpiecznych interakcji użytkowników z aplikacjami internetowymi, klasycznymi, mobilnymi i jednostronicowymi. Za pomocą usługi Azure AD B2C użytkownicy mogą tworzyć konta, logować się, resetować hasła i edytować profile. W usłudze Azure AD B2C zaimplementowano formę protokołów OpenID Connect i OAuth 2.0. Kluczowym elementem implementacji tych protokołów są tokeny zabezpieczające i ich oświadczenia, które umożliwiają bezpieczny dostęp do zasobów.

*Podróż użytkownika* to żądanie, które określa zasady sterujące interakcjami użytkownika i aplikacji z usługą Azure AD B2C. Na potrzeby zdefiniowania podróży użytkownika w usłudze Azure AD B2C są dostępne Dwie ścieżki. 

Jeśli jesteś twórcą aplikacji i masz wiedzę na temat obsługi tożsamości lub jej nie masz, możesz zdefiniować typowe przepływy użytkownika tożsamości za pomocą witryny Azure Portal. Jeśli jesteś specjalistą w zakresie obsługi tożsamości, integratorem systemów, konsultantem lub członkiem wewnętrznego zespołu ds. tożsamości, wiesz, jak korzystać z przepływów OpenID Connect, i rozumiesz działanie dostawców tożsamości oraz uwierzytelniania opartego na oświadczeniach, możesz stosować zasady niestandardowe oparte na języku XML.

Przed rozpoczęciem definiowania podróży użytkownika musisz utworzyć dzierżawę usługi Azure AD B2C i zarejestrować w niej aplikację oraz i interfejs API. Po zakończeniu tych zadań możesz rozpocząć definiowanie podróży użytkownika za pomocą przepływów użytkownika lub zasad niestandardowych. Możesz również opcjonalnie dodać lub zmienić dostawców tożsamości lub dostosować środowiska użytkownika w ramach podróży.

## <a name="protocols-and-tokens"></a>Protokoły i tokeny

Na potrzeby podróży użytkownika usługa Azure AD B2C obsługuje [protokoły OpenID Connect i OAuth 2.0](active-directory-b2c-reference-protocols.md). Podczas wdrażania protokołu OpenID Connect w usłudze Azure AD B2C aplikacja rozpoczyna podróż użytkownika, wysyłając żądania uwierzytelniania do usługi Azure AD B2C. 

Wynikiem żądania skierowanego do usługi Azure AD B2C jest token zabezpieczający, taki jak [token identyfikatora lub token dostępu](active-directory-b2c-reference-tokens.md). Ten token zabezpieczający definiuje tożsamość użytkownika. Tokeny są odbierane w punktach końcowych usługi Azure AD B2C, takich jak punkt końcowy `/token` lub `/authorize`. Z poziomu tych tokenów można uzyskać dostęp do oświadczeń, za pomocą których można zweryfikować tożsamości i zezwolić na dostęp do bezpiecznych zasobów.

## <a name="tenants-and-applications"></a>Dzierżawy i aplikacje

W usłudze Azure AD B2C *dzierżawa* reprezentuje Twoją organizację i jest katalogiem użytkowników. Każda dzierżawa usługi Azure AD B2C jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD B2C. Być może masz już dzierżawę usługi Azure Active Directory, ale dzierżawa usługi Azure AD B2C to osobna dzierżawa. Dzierżawa zawiera informacje dotyczące użytkowników, którzy utworzyli konto w celu korzystania z aplikacji. Na przykład hasła, dane profilu i uprawnienia. Aby uzyskać więcej informacji, zobacz [Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C](tutorial-create-tenant.md).

Przed rozpoczęciem konfigurowania w aplikacji korzystania z usługi Azure AD B2C należy najpierw zarejestrować ją w dzierżawie za pomocą witryny Azure Portal. Proces rejestracji polega na zgromadzeniu wartości i przypisaniu ich do aplikacji. Wartości te obejmują identyfikator aplikacji, który identyfikuje określoną aplikację, oraz identyfikator URI przekierowania służący do kierowania odpowiedzi z powrotem do aplikacji.

Interakcja każdej aplikacji jest realizowana według następującego ogólnego schematu:

1. Aplikacja zwraca się do użytkownika o uruchomienie zasady.
2. Użytkownik wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja odbiera token.
4. Aplikacja używa tokenu, aby podjąć próbę uzyskania dostępu do zasobu.
5. Serwer zasobów weryfikuje token, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token.

Aby zarejestrować aplikację internetową, wykonaj kroki opisane w artykule [Samouczek: rejestrowanie aplikacji w celu umożliwienia przeprowadzenia procesu rejestracji i logowania przy użyciu usługi Azure AD B2C](tutorial-register-applications.md). Możesz również [dodać aplikację internetowego interfejsu API do dzierżawy usługi Azure Active Directory B2C](add-web-application.md) lub [dodać natywną aplikację kliencką do dzierżawy usługi Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Podróże użytkownika

Zasady w podróży użytkownika można zdefiniować jako [przepływ użytkownika](active-directory-b2c-reference-policies.md) lub [zasady niestandardowe](active-directory-b2c-overview-custom.md). W witrynie Azure Portal są dostępne wstępnie zdefiniowane przepływy użytkowników dla większości typowych zadań związanych z tożsamością, takich jak tworzenie konta, logowanie się i edytowanie profilu.

Podróże użytkownika umożliwiają sterowanie zachowaniami dzięki skonfigurowaniu następujących ustawień:

- Konta społecznościowe, za pomocą których użytkownik tworzy konta w aplikacji
- Dane zbierane od użytkownika, takie jak imię i nazwisko lub kod pocztowy
- Uwierzytelnianie wieloskładnikowe
- Wygląd i działanie stron
- Informacje zwrócone do aplikacji

Zasady niestandardowe to pliki konfiguracji definiujące zachowanie platformy [Identity Experience Framework](trustframeworkpolicy.md) w dzierżawie usługi Azure AD B2C. Identity Experience Framework to podstawowa platforma, która ustanawia relację zaufania wielu jednostek i finalizuje etapy podróży użytkownika. 

Zasady niestandardowe mogą być modyfikowane, dzięki czemu mogą wykonywać wiele zadań. Niestandardowe zasady są to jeden lub kilka plików w formacie XML, które odwołują się do siebie nawzajem zgodnie z łańcuchem hierarchii. Na potrzeby zasad niestandardowych dostępny jest [pakiet początkowy](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) umożliwiający włączanie typowych zadań związanych z tożsamościami. 

W razie potrzeby w dzierżawie usługi Azure AD B2C są używane zasady niestandardowe lub przepływy użytkowników różnego typu. Można użyć ich ponownie w innych aplikacjach. Ta elastyczność umożliwia definiowanie i modyfikowanie obsługi tożsamości użytkownika przy zerowych lub jedynie minimalnych zmianach w kodzie. Aby użyć zasad, dodaj określony parametr zapytania do żądań uwierzytelnienia HTTP. Aby utworzyć własne zasady niestandardowe, zobacz [Wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Dostawcy tożsamości 

W swoich aplikacjach możesz umożliwić użytkownikom logowanie się za pomocą różnych dostawców tożsamości. *Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. Dostawców tożsamości obsługiwanych przez usługę Azure AD B2C możesz dodać w witrynie Azure Portal. 

Zazwyczaj używa się tylko jednego dostawcy tożsamości w aplikacji, ale istnieje możliwość dodania większej ich liczby. Aby skonfigurować dostawcę tożsamości w dzierżawie usługi Azure AD B2C, najpierw utwórz aplikację w witrynie dewelopera dostawcy tożsamości, a następnie zarejestruj identyfikator aplikacji lub identyfikator klienta oraz hasło lub klucz tajny klienta z poziomu tworzonej aplikacji dostawcy tożsamości. Ten identyfikator i hasło są następnie używane do konfigurowania aplikacji. 

W następujących artykułach opisano kroki dodawania niektórych typowych dostawców tożsamości do przepływów użytkownika:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Konto Microsoft](active-directory-b2c-setup-msa-app.md)

W następujących artykułach opisano kroki dodawania niektórych typowych dostawców tożsamości do zasad niestandardowych:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Konto Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Aby uzyskać więcej informacji, zobacz [Samouczek: dodawanie dostawcy tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Dostosowywanie strony

Większość zawartości HTML i CSS przekazywanej klientom w ramach podróży użytkownika może być kontrolowana. Za pomocą funkcji dostosowywania strony można dostosować wygląd i działanie dowolnych zasad niestandardowych lub przepływu użytkownika. Ta funkcja dostosowywania pozwala zapewnić spójność wizerunku marki i wrażeń wizualnych między aplikacją i usługą Azure AD B2C. 

Usługa Azure AD B2C uruchamia kod w przeglądarce użytkownika i wykorzystuje nowoczesne podejście nazywane współużytkowaniem zasobów między źródłami (CORS, Cross-origin Resource Sharing). Najpierw należy określić adres URL w zasadach z dostosowaną zawartością HTML. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę użytkownikowi.

Parametry są wysłane do usługi Azure AD B2C w postaci ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartości strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie tworzenia konta lub logowania.

Aby dostosować strony w przepływie użytkownika, zobacz [Samouczek: dostosowywanie interfejsu środowisk użytkownika w usłudze Azure Active Directory B2C](tutorial-customize-ui.md). Aby dostosować strony w przypadku zasad niestandardowych, zobacz [Dostosowywanie interfejsu użytkownika aplikacji za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) lub [Konfigurowanie interfejsu użytkownika z zawartością dynamiczną za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Zasoby deweloperów

### <a name="client-applications"></a>Aplikacje klienckie

Możesz wybrać aplikacje m.in. dla systemu [iOS](active-directory-b2c-devquickstarts-ios.md) i [Android](active-directory-b2c-devquickstarts-android.md) oraz platformy .NET. Usługa Azure AD B2C umożliwia wykonywanie tych akcji przy jednoczesnej ochronie tożsamości użytkowników.

Jeśli jesteś deweloperem aplikacji internetowych platformy ASP.NET, skonfiguruj aplikację do uwierzytelniania kont, wykonując kroki opisane w artykule [Samouczek: włączanie uwierzytelniania aplikacji internetowej przy użyciu kont w usłudze Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Jeśli jesteś deweloperem aplikacji klasycznych, skonfiguruj aplikację do uwierzytelniania kont, wykonując kroki opisane w artykule [Samouczek: włączanie uwierzytelniania aplikacji klasycznej przy użyciu kont w usłudze Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Jeśli jesteś deweloperem aplikacji jednostronicowych i korzystasz z technologii Node.js, skonfiguruj aplikację do uwierzytelniania kont, wykonując kroki opisane w artykule [Samouczek: włączanie uwierzytelniania aplikacji jednostronicowej przy użyciu kont w usłudze Azure AD B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>interfejsy API
Jeśli aplikacje internetowe lub klienckie muszą wywoływać interfejsy API, możesz skonfigurować bezpieczny dostęp do tych zasobów w usłudze Azure AD B2C.

Jeśli jesteś deweloperem aplikacji internetowych platformy ASP.NET, skonfiguruj aplikację do wywoływania chronionego interfejsu API, wykonując kroki opisane w artykule [Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Jeśli jesteś deweloperem aplikacji klasycznych, skonfiguruj aplikację do wywoływania chronionego interfejsu API, wykonując kroki opisane w artykule [Samouczek: udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Jeśli jesteś deweloperem aplikacji jednostronicowych i korzystasz z technologii Node.js, skonfiguruj aplikację do uwierzytelniania kont, wykonując kroki opisane w artykule [Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

Do aplikacji w usłudze Azure AD B2C możesz dodać własny kod JavaScript po stronie klienta. Aby skonfigurować kod JavaScript w aplikacji, zdefiniuj [kontrakt strony](page-contract.md) i włącz obsługę języka [JavaScript](javascript-samples.md) w przepływach użytkownika lub zasadach niestandardowych.

### <a name="user-accounts"></a>Konta użytkowników

Wiele typowych zadań zarządzania dzierżawy należy wykonywać programowo. Podstawowym tego przykładem jest zarządzanie użytkownikami. Konieczne może być migrowanie istniejącego magazynu użytkowników do dzierżawy usługi Azure AD B2C. Możesz zdecydować się na hostowanie rejestracji użytkowników na własnej stronie i tworzenie kont użytkowników w katalogu usługi Azure AD B2C w tle. Zadania tego typu wymagają możliwości tworzenia, odczytywania, aktualizowania i usuwania kont użytkowników. Można je wykonywać za pomocą [interfejsu API programu Graph usługi Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij konfigurowanie aplikacji dla funkcji rejestracji i logowania, przechodząc do samouczka.

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C](tutorial-create-tenant.md)
