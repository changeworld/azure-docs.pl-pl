---
title: Usługa Azure AD dla deweloperów (wersja 1.0) | Azure
description: Poznaj podstawowe informacje o uwierzytelniania dla usługi Azure AD dla deweloperów (wersja 1.0), takie jak model aplikacji, interfejs API, inicjowanie obsługi administracyjnej i najbardziej typowe scenariusze uwierzytelniania.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 36b39f3706db615e40ebfadebf36be4d8b29c33e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154733"
---
# <a name="what-is-authentication"></a>Co to jest uwierzytelnianie?

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

*Uwierzytelnianie* to wezwanie strony do podania wiarygodnych poświadczeń. Stanowi to podstawę utworzenia podmiotu zabezpieczeń używanego do obsługi tożsamości i kontroli dostępu. Mówiąc prościej, jest to proces udowadniania, że jest się tym, za kogo się podaje. Uwierzytelnianie jest czasami określane terminem AuthN.

*Autoryzacja* to przyznanie uprawnienia uwierzytelnionemu podmiotowi zabezpieczeń do wykonania jakiegoś zadania. Autoryzacja określa, do jakich danych można uzyskać dostęp oraz jakie zadania można na nich wykonać. Autoryzacja jest czasami określana terminem AuthZ.

Usługa Azure Active Directory dla deweloperów (w wersji 1.0) (Azure AD) upraszcza uwierzytelnianie dla deweloperów aplikacji, zapewniając tożsamość jako usługę, obsługując protokoły zgodne ze standardami branżowymi, takie jak OAuth 2.0 i OpenID Connect, a także biblioteki typu open source dla różnych platform, które pomogą Ci szybko rozpocząć kodowanie.

Istnieją dwa główne przypadki użycia w modelu programowania w usłudze Azure AD:

* Podczas przepływu autoryzacji OAuth 2.0 — gdy właściciel zasobu przyznaje autoryzację do aplikacji klienckiej, co umożliwia klientowi uzyskanie dostępu do zasobów właściciela zasobu.
* Podczas dostępu do zasobu przez klienta — zgodnie z implementacją serwera zasobów, przy użyciu wartości oświadczeń obecnych w tokenie dostępu na potrzeby podejmowania na ich podstawie decyzji dotyczących kontroli dostępu.

## <a name="authentication-basics-in-azure-ad"></a>Podstawowe informacje o uwierzytelnianiu w usłudze Azure AD

Rozpatrzymy tutaj najprostszy scenariusz, w którym wymagana jest tożsamość: użytkownik w przeglądarce internetowej musi się uwierzytelnić w aplikacji internetowej. Na poniższym diagramie przedstawiono ten scenariusz:

![Przegląd logowania do aplikacji internetowej](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Oto, co musisz wiedzieć o różnych składnikach pokazanych na diagramie:

* Usługa Azure AD jest dostawcą tożsamości. Dostawca tożsamości jest odpowiedzialny za sprawdzanie tożsamości użytkowników i aplikacji, które istnieją w katalogu organizacji i wystawia tokeny zabezpieczające po pomyślnym uwierzytelnieniu tych użytkowników i aplikacji.
* Aplikacja, która chce zlecić uwierzytelnianie do usługi Azure AD, musi być zarejestrowana w usłudze Azure Active Directory (Azure AD). Usługa Azure AD rejestruje i unikatowo identyfikuje aplikację w katalogu.
* Deweloperzy mogą używać bibliotek uwierzytelniania typu open-source w usłudze Azure AD, aby uprościć proces uwierzytelniania dzięki automatycznej obsłudze szczegółów dotyczących protokołu. Aby uzyskać więcej informacji, zobacz Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft [w wersji 2.0](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) i [biblioteki uwierzytelniania w wersji 1.0](active-directory-authentication-libraries.md).
* Po uwierzytelnieniu użytkownika aplikacja musi zweryfikować token zabezpieczający użytkownika, aby upewnić się, że uwierzytelnianie zakończyło się pomyślnie. Procesy zachodzące w aplikacji zostały opisane w różnych przewodnikach Szybki start, samouczkach i przykładach kodu w różnych językach i strukturach.
  * Aby szybko utworzyć aplikację i dodać do niej funkcje, takie jak między innymi uzyskiwanie tokenów, odświeżanie tokenów, logowanie użytkownika i wyświetlanie informacji o użytkowniku, zapoznaj się z sekcją **Przewodniki Szybki start** w dokumentacji.
  * Aby uzyskać szczegółowe, oparte na scenariuszach procedury zadań dla deweloperów uwierzytelniania, takich jak uzyskiwanie tokenów dostępu i korzystanie z nich w wywołaniach interfejsu API programu Microsoft Graph i innych interfejsów API, implementowanie logowania firmy Microsoft przy użyciu tradycyjnej aplikacji internetowej opartej na przeglądarce za pomocą protokołu OpenID Connect i innych, zapoznaj się z sekcją **Samouczki** w dokumentacji.
  * Aby pobrać przykłady kodu, przejdź do witryny [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Przepływ żądań i odpowiedzi dotyczących procesu uwierzytelniania jest określony przez używany protokół uwierzytelniania, taki jak OAuth 2.0, OpenID Connect, WS-Federation lub SAML 2.0. Aby uzyskać więcej informacji na temat protokołów, zobacz **pojęcia > protokół uwierzytelniania** sekcji dokumentacji.

W przykładowym scenariuszu powyżej aplikacje można sklasyfikować według tych dwóch ról:

* Aplikacje, które wymagają bezpiecznego dostępu do zasobów.
* Aplikacje, które same pełnią rolę zasobu.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody

W zależności od sposobu zbudowanego klienta można użyć jednego (lub kilku) przepływów uwierzytelniania obsługiwanych przez usługę Azure AD. Przepływy te mogą tworzyć różne tokeny (id_tokens, odświeżanie tokenów, tokeny dostępu), a także kody autoryzacji i wymagają różnych tokenów, aby je działały. Ten wykres zawiera omówienie:

|Ruch | Wymaga | id_token | token dostępu | token odświeżania | kod autoryzacyjny | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Przepływ kodu autoryzacji](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Niejawny przepływ](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrydowy przepływ OIDC](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Odświeżanie wykorzystania tokenu](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | token odświeżania | x | x | x| |
|[Przepływ „w imieniu”](v1-oauth2-on-behalf-of-flow.md) | token dostępu| x| x| x| |
|[Poświadczenia klienta](v1-oauth2-client-creds-grant-flow.md) | | | x (tylko aplikacja)| | |

Tokeny wystawione w trybie niejawnym mają ograniczenie długości ze względu na `response_mode` `query` przekazywanie z powrotem do przeglądarki za pośrednictwem adresu URL (gdzie jest lub `fragment`).  Niektóre przeglądarki mają limit rozmiaru adresu URL, który można umieścić na pasku przeglądarki i zakończyć się niepowodzeniem, gdy jest zbyt długi.  W związku z tym `groups` tokeny te nie mają lub `wids` roszczeń. 

Teraz, po zapoznaniu się z podstawami, kontynuuj czytanie, aby zrozumieć model oraz interfejs API aplikacji tożsamości, poznać sposób działania aprowizacji w usłudze Azure AD i uzyskać linki do szczegółowych informacji na temat typowych scenariuszy obsługiwanych przez usługę Azure AD.

## <a name="application-model"></a>Model aplikacji

Usługa Azure AD reprezentuje aplikacje działające według określonego modelu, który został zaprojektowany w celu pełnienia dwóch głównych funkcji:

* **Identyfikowanie aplikacji zgodnie z obsługiwanymi protokołami uwierzytelniania** — obejmuje to wyliczanie wszystkich identyfikatorów, adresów URL, wpisów tajnych i powiązanych informacji, które są wymagane podczas uwierzytelniania. Tutaj usługa Azure AD pełni następujące funkcje:

    * Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania.
    * Przechowuje wszystkie dane konieczne do zdecydowania, jakich zasobów może wymagać aplikacja w celu uzyskania dostępu oraz czy konkretne żądanie powinno zostać spełnione i pod jakimi warunkami.
    * Udostępnia infrastrukturę do implementowania aprowizacji aplikacji w ramach dzierżawy dewelopera aplikacji i dowolnej innej dzierżawy usługi Azure AD.

* **Obsługa zgody użytkownika podczas żądania tokenu i umożliwienie dynamicznej aprowizacji aplikacji między dzierżawami** — tutaj usługa Azure AD pełni następujące funkcje:

    * Umożliwia użytkownikom i administratorom dynamiczne wyrażanie zgody lub odmawianie zgody dla aplikacji na uzyskiwanie przez nią dostępu do zasobów w ich imieniu.
    * Umożliwia administratorom ostateczne zdecydowanie, jakie działania mogą podejmować aplikacje, którzy użytkownicy mogą używać określonych aplikacji i w jaki sposób uzyskiwany jest dostęp do zasobów katalogu.

W usłudze Azure AD **obiekt aplikacji** opisuje aplikację jako jednostkę abstrakcyjną. Deweloperzy pracują z aplikacjami. Podczas wdrażania usługa Azure AD używa danego obiektu aplikacji jako konspektu do utworzenia **jednostki usługi**, która reprezentuje konkretne wystąpienie aplikacji w katalogu lub dzierżawie. To właśnie jednostka usługi definiuje, jakie działania może w rzeczywistości wykonywać aplikacja w konkretnym katalogu docelowym, kto może z niej korzystać, do jakich zasobów ma ona dostęp itd. Usługa Azure AD tworzy jednostkę usługi na podstawie obiektu aplikacji poprzez wyrażenie **zgody**.

Na poniższym diagramie przedstawiono uproszczony przepływ aprowizowania usługi Azure AD sterowany poprzez wyrażenie zgody.  W nim istnieje dwóch dzierżawców (A i B), gdzie dzierżawca A jest właścicielem aplikacji, a dzierżawa B jest tworzenie wystąpienia aplikacji za pośrednictwem jednostki usługi.  

![Uproszczony przepływ aprowizowania sterowany poprzez wyrażenie zgody](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

1. Użytkownik z dzierżawy B próbuje zalogować się za pomocą aplikacji, punkt końcowy autoryzacji żąda tokenu dla aplikacji.
1. Poświadczenia użytkownika są nabywane i weryfikowane w celu uwierzytelnienia
1. Użytkownik jest monitowany o wyrażenie zgody na uzyskanie przez aplikację dostępu do dzierżawy B
1. Usługa Azure AD używa obiektu aplikacji w dzierżawie A jako planu do tworzenia jednostki usługi w dzierżawie B
1. Użytkownik otrzymuje żądany token.

Ten proces może być powtarzany dowolną liczbę razy dla innych dzierżaw (C, D itd.). Dzierżawa A zachowuje plan aplikacji (obiekt aplikacji). Użytkownicy i administratorzy wszystkich pozostałych dzierżaw, dla których aplikacja otrzymała zgodę, zachowują kontrolę nad tym, jakie działania może wykonywać aplikacja przy użyciu odpowiedniego obiektu jednostki usługi w każdej dzierżawie. Aby uzyskać więcej informacji, zobacz [Obiekty głównej aplikacji i usługi na platformie tożsamości firmy Microsoft](../develop/app-objects-and-service-principals.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="claims-in-azure-ad-security-tokens"></a>Oświadczenia w tokenach zabezpieczających usługi Azure AD

Tokeny zabezpieczające (tokeny dostępu i identyfikatora) wydane przez usługę Azure AD zawierają oświadczenia lub potwierdzenia informacji o podmiocie, który został uwierzytelniony. Aplikacje mogą używać oświadczeń do wykonywania różnych zadań, takich jak:

* Sprawdzanie poprawności tokenu
* Identyfikowanie dzierżawy katalogu podmiotu
* Wyświetlanie informacji o użytkowniku
* Określanie autoryzacji podmiotu

Oświadczenia obecne w dowolnym tokenie zabezpieczającym są zależne od typu tokenu, typu poświadczenia użytego do uwierzytelnienia użytkownika i konfiguracji aplikacji.

W poniższej tabeli znajduje się krótki opis każdego typu oświadczenia emitowanego przez usługę Azure AD. Aby uzyskać bardziej szczegółowe informacje, zobacz [tokeny dostępu](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) i [tokeny identyfikatorów wystawione](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) przez usługę Azure AD.

| Claim | Opis |
| --- | --- |
| Identyfikator aplikacji | Określa aplikację, która korzysta z tokenu. |
| Grupy odbiorców | Identyfikuje zasób odbiorcy, dla którego jest przeznaczony token. |
| Application Authentication Context Class Reference | Wskazuje, jak klient został uwierzytelniony (klient publiczny lub klient poufny). |
| Błyskawiczne uwierzytelnianie | Rejestruje datę i godzinę wystąpienia uwierzytelniania. |
| Metoda uwierzytelniania | Wskazuje, jak podmiot tokenu został uwierzytelniony (hasło, certyfikat itp.). |
| Imię | Udostępnia imię użytkownika w postaci, w jakiej jest ono ustawione w usłudze Azure AD. |
| Grupy | Zawiera identyfikatory obiektów grup usługi Azure AD, których członkiem jest użytkownik. |
| Dostawca tożsamości | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. |
| Wystawiony o | Rejestruje godzinę, o której został wystawiony token (często używane w celu odświeżenia tokenu). |
| Wystawca | Identyfikuje usługę STS, która emitowała token, a także dzierżawę usługi Azure AD. |
| Nazwisko | Udostępnia nazwisko użytkownika w postaci, w jakiej jest ono ustawione w usłudze Azure AD. |
| Nazwa | Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. |
| Identyfikator obiektu | Zawiera niezmienny, unikatowy identyfikator podmiotu w usłudze Azure AD. |
| Role | Zawiera przyjazne nazwy ról aplikacji usługi Azure AD, które udzielono użytkownikowi. |
| Zakres | Wskazuje uprawnienia przyznane aplikacji klienckiej. |
| Podmiot | Określa podmiot zabezpieczeń, dla którego token określa informacje. |
| Identyfikator dzierżawy | Zawiera niezmienny, unikatowy identyfikator dzierżawy katalogu, który wystawił token. |
| Czas życia tokenu | Definiuje przedział czasu, przez który token jest prawidłowy. |
| Nazwa główna użytkownika | Zawiera nazwę główną użytkownika podmiotu. |
| Wersja | Zawiera numer wersji tokenu. |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [typach aplikacji i scenariuszach obsługiwanych na platformie tożsamości firmy Microsoft](app-types.md)
