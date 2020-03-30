---
title: Słowniczek dewelopera platformy tożsamości firmy Microsoft | Azure
description: Lista terminów dla często używanych koncepcji i funkcji dewelopera platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: ce98d2db86c87ac6aa8fa4872bc076714467d32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263051"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Glosariusz dewelopera platformy tożsamości firmy Microsoft

Ten artykuł zawiera definicje niektórych podstawowych pojęć dewelopera i terminologii, które są przydatne podczas nauki o tworzeniu aplikacji przy użyciu platformy tożsamości firmy Microsoft.

## <a name="access-token"></a>token dostępu

Typ [tokenu zabezpieczającego](#security-token) wystawionego przez [serwer autoryzacji](#authorization-server)i używanego przez [aplikację kliencką](#client-application) w celu uzyskania dostępu do [chronionego serwera zasobów](#resource-server). Zazwyczaj w postaci [tokenu JSON Web Token (JWT)][JWT]token uosabia autoryzacji udzielonej klientowi przez [właściciela zasobu,](#resource-owner)dla żądanego poziomu dostępu. Token zawiera wszystkie odpowiednie [oświadczenia](#claim) dotyczące tematu, dzięki czemu aplikacja kliencka używać go jako forma poświadczeń podczas uzyskiwania dostępu do danego zasobu. Eliminuje to również potrzebę właściciela zasobu do udostępnienia poświadczeń do klienta.

Tokeny dostępu są czasami określane jako "Użytkownik + aplikacja" lub "Tylko do aplikacji", w zależności od poświadczeń reprezentowanych. Na przykład, gdy aplikacja kliencka używa:

* [Udzielenie autoryzacji "Kod autoryzacji",](#authorization-grant)użytkownik końcowy uwierzytelnia się najpierw jako właściciel zasobu, delegując klientowi autoryzację dostępu do zasobu. Klient uwierzytelnia się później podczas uzyskiwania tokenu dostępu. Token może czasami być określany bardziej szczegółowo jako token "Użytkownik+aplikacja", ponieważ reprezentuje zarówno użytkownika, który autoryzował aplikację kliencką, jak i aplikację.
* ["Poświadczenia klienta" autoryzacji udzielić,](#authorization-grant)klient zapewnia wyłączne uwierzytelnianie, działając bez uwierzytelniania/autoryzacji właściciela zasobu, więc token może czasami być określane jako token "Tylko do aplikacji".

Aby uzyskać więcej informacji, zobacz [odwołanie do tokenów platformy tożsamości firmy Microsoft.][AAD-Tokens-Claims]

## <a name="application-id-client-id"></a>Identyfikator aplikacji (identyfikator klienta)

Unikatowy identyfikator usługi Azure AD problemy z rejestracją aplikacji, która identyfikuje określonej aplikacji i skojarzonych konfiguracji. Ten identyfikator aplikacji[(identyfikator klienta)](https://tools.ietf.org/html/rfc6749#page-15)jest używany podczas wykonywania żądań uwierzytelniania i jest dostarczany do bibliotek uwierzytelniania w czasie tworzenia. Identyfikator aplikacji (identyfikator klienta) nie jest kluczem tajnym.

## <a name="application-manifest"></a>manifest aplikacji

Funkcja udostępniana przez [portal Azure][AZURE-portal], który tworzy reprezentację JSON konfiguracji tożsamości aplikacji, używany jako mechanizm do aktualizowania skojarzonych [jednostek aplikacji][Graph-App-Resource] i [ServicePrincipal.][Graph-Sp-Resource] Aby uzyskać więcej [informacji, zobacz Opis manifestu aplikacji usługi Azure Active Directory.][AAD-App-Manifest]

## <a name="application-object"></a>obiekt aplikacji

Po zarejestrowaniu/zaktualizowaniu aplikacji w [witrynie Azure portal][AZURE-portal]portal tworzy/aktualizuje zarówno obiekt aplikacji, jak i odpowiedni [obiekt jednostkowy usługi](#service-principal-object) dla tej dzierżawy. Obiekt aplikacji *definiuje* konfigurację tożsamości aplikacji globalnie (we wszystkich dzierżawach, do których ma dostęp), zapewniając szablon, z którego *pochodzą* jej odpowiednie obiekty głównej usługi do użytku lokalnego w czasie wykonywania (w określonej dzierżawie).

Aby uzyskać więcej informacji, zobacz [Obiekty głównej aplikacji i usługi][AAD-App-SP-Objects].

## <a name="application-registration"></a>rejestracja aplikacji

Aby umożliwić aplikacji integrację z funkcjami zarządzania tożsamościami i dostępem i delegowaniem do usługi Azure AD, musi ona być zarejestrowana w [dzierżawie](#tenant)usługi Azure AD. Podczas rejestrowania aplikacji w usłudze Azure AD udostępniasz konfigurację tożsamości dla aplikacji, umożliwiając jej integrację z usługą Azure AD i korzystanie z funkcji, takich jak:

* Niezawodne zarządzanie logiem jednokrotnym przy użyciu usługi Azure AD Identity Management i implementacji protokołu [OpenID Connect][OpenIDConnect]
* Pośredniczył dostęp do [chronionych zasobów](#resource-server) przez [aplikacje klienckie,](#client-application)za pośrednictwem [serwera autoryzacji](#authorization-server) OAuth 2.0
* [Ramy zgody](#consent) na zarządzanie dostępem klienta do chronionych zasobów na podstawie autoryzacji właściciela zasobów.

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory.][AAD-Integrating-Apps]

## <a name="authentication"></a>uwierzytelnianie

Akt zakwestionowania strony dla uzasadnionych poświadczeń, stanowią podstawę do utworzenia podmiotu zabezpieczeń, który ma być używany do kontroli tożsamości i dostępu. Na przykład podczas [udzielania autoryzacji OAuth2](#authorization-grant) uwierzytelnianie strony wypełnia rolę [właściciela zasobu](#resource-owner) lub [aplikacji klienckiej](#client-application), w zależności od użytej dotacji.

## <a name="authorization"></a>autoryzacja

Akt udzielania uwierzytelnionego uprawnienia głównego zabezpieczeń, aby coś zrobić. Istnieją dwa główne przypadki użycia w modelu programowania w usłudze Azure AD:

* Podczas przepływu [udzielania dotacji autoryzacji OAuth2:](#authorization-grant) gdy [właściciel zasobu](#resource-owner) udziela autoryzacji [aplikacji klienckiej,](#client-application)umożliwiając klientowi dostęp do zasobów właściciela zasobu.
* Podczas dostępu do zasobów przez klienta: zgodnie z implementacji przez [serwer zasobów](#resource-server), przy użyciu wartości [oświadczeń](#claim) obecnych w [tokenie dostępu](#access-token) do podejmowania decyzji kontroli dostępu na ich podstawie.

## <a name="authorization-code"></a>kod autoryzacyjny

Krótkotrwały "token" dostarczony do [aplikacji klienckiej](#client-application) przez [punkt końcowy autoryzacji,](#authorization-endpoint)jako część przepływu "kodu autoryzacji", jeden z czterech dotacji [autoryzacji](#authorization-grant)OAuth2. Kod jest zwracany do aplikacji klienckiej w odpowiedzi na uwierzytelnianie [właściciela zasobu,](#resource-owner)wskazując, że właściciel zasobu przekazał autoryzację dostępu do żądanych zasobów. W ramach przepływu kod jest później wymieniany na [token dostępu.](#access-token)

## <a name="authorization-endpoint"></a>punkt końcowy autoryzacji

Jeden z punktów końcowych zaimplementowanych przez [serwer autoryzacji](#authorization-server), używany do interakcji z [właścicielem zasobu](#resource-owner) w celu zapewnienia [dotacji autoryzacji](#authorization-grant) podczas przepływu udzielania autoryzacji OAuth2. W zależności od używanego przepływu dotacji autoryzacji rzeczywista dotacja może się różnić, w tym [kod autoryzacji](#authorization-code) lub [token zabezpieczający.](#security-token)

Zobacz OAuth2 specyfikacji [dotacji i sekcji][OAuth2-AuthZ-Grant-Types] punktów końcowych [autoryzacji][OAuth2-AuthZ-Endpoint] i [OpenIDConnect specyfikacji,][OpenIDConnect-AuthZ-Endpoint] aby uzyskać więcej informacji.

## <a name="authorization-grant"></a>udzielenie zezwolenia

Poświadczenie reprezentujące [autoryzację](#authorization) [właściciela zasobu](#resource-owner) dostępu do chronionych zasobów, udzieloną [aplikacji klienckiej](#client-application). Aplikacja kliencka może użyć jednego z [czterech typów dotacji zdefiniowanych przez platformę autoryzacji OAuth2][OAuth2-AuthZ-Grant-Types] w celu uzyskania dotacji, w zależności od typu klienta/wymagań: "udzielenie kodu autoryzacji", "udzielenie poświadczeń klienta", "niejawne udzielanie dotacji" i "udzielanie poświadczeń hasła właściciela zasobu". Poświadczenie zwrócone do klienta jest [token dostępu](#access-token)lub [kod autoryzacji](#authorization-code) (wymieniane później na token dostępu), w zależności od typu dotacji autoryzacji używane.

## <a name="authorization-server"></a>serwer autoryzacji

Zgodnie z definicją [w ramach autoryzacji OAuth2][OAuth2-Role-Def], serwer odpowiedzialny za wystawianie tokenów dostępu do [klienta](#client-application) po pomyślnym uwierzytelnieniu [właściciela zasobu](#resource-owner) i uzyskaniu jego autoryzacji. [Aplikacja kliencka](#client-application) współdziała z serwerem autoryzacji w czasie wykonywania za pośrednictwem swoich [punktów końcowych autoryzacji](#authorization-endpoint) i [tokenów,](#token-endpoint) zgodnie z [certyfikatem autoryzacji](#authorization-grant)zdefiniowanej OAuth2.

W przypadku integracji aplikacji platformy tożsamości firmy Microsoft platforma tożsamości firmy Microsoft implementuje rolę serwera autoryzacji dla aplikacji usługi Azure AD i interfejsów API usługi Firmy Microsoft, na przykład [interfejsów API programu Microsoft Graph][Microsoft-Graph].

## <a name="claim"></a>Roszczenia

[Token zabezpieczający](#security-token) zawiera oświadczenia, które dostarczają potwierdzeń dotyczących jednej jednostki (takiej jak [aplikacja kliencka](#client-application) lub [właściciel zasobu)](#resource-owner)innej encji (takiej jak [serwer zasobów).](#resource-server) Oświadczenia to pary nazw i wartości, które przekazują fakty dotyczące tematu tokenu (na przykład podmiot zabezpieczeń uwierzytelniony przez [serwer autoryzacji).](#authorization-server) Oświadczenia obecne w danym tokenie są zależne od kilku zmiennych, w tym typu tokenu, typu poświadczenia używanego do uwierzytelniania podmiotu, konfiguracji aplikacji itp.

Zobacz [odwołanie tokenu platformy tożsamości firmy Microsoft, aby][AAD-Tokens-Claims] uzyskać więcej informacji.

## <a name="client-application"></a>aplikacja kliencka

Zgodnie z definicją [w ramach autoryzacji OAuth2][OAuth2-Role-Def], aplikacja, która sprawia, że żądania chronionych zasobów w imieniu [właściciela zasobu](#resource-owner). Termin "klient" nie oznacza żadnych szczególnych cech implementacji sprzętu (na przykład, czy aplikacja jest wykonywana na serwerze, pulpicie lub innych urządzeniach).

Aplikacja kliencka żąda [autoryzacji](#authorization) od właściciela zasobu do udziału w [przepływie udzielania autoryzacji OAuth2](#authorization-grant) i może uzyskiwać dostęp do interfejsów API/danych w imieniu właściciela zasobu. Struktura autoryzacji OAuth2 [definiuje dwa typy klientów][OAuth2-Client-Types], "poufne" i "publiczne", w oparciu o zdolność klienta do zachowania poufności swoich poświadczeń. Aplikacje mogą implementować [klienta sieci web (poufne),](#web-client) który działa na serwerze sieci web, [natywny klient (publiczny)](#native-client) zainstalowany na urządzeniu lub [klient oparty na agencie użytkownika (publiczny),](#user-agent-based-client) który działa w przeglądarce urządzenia.

## <a name="consent"></a>Zgody

Proces uzyskiwania autoryzacji [aplikacji klienckiej](#client-application) [przez właściciela zasobu](#resource-owner) w celu uzyskania dostępu do chronionych zasobów w ramach określonych [uprawnień](#permissions)w imieniu właściciela zasobu. W zależności od uprawnień wymaganych przez klienta, administrator lub użytkownik zostanie poproszony o zgodę na dostęp odpowiednio do ich organizacji/danych indywidualnych. Uwaga: w scenariuszu [wielu dzierżawców](#multi-tenant-application) [jednostki usługi](#service-principal-object) aplikacji jest również rejestrowana w dzierżawie użytkownika wyrażającego zgodę.

Zobacz [ramy zgody, aby](consent-framework.md) uzyskać więcej informacji.

## <a name="id-token"></a>Token identyfikatora

[Token zabezpieczający](#security-token) [OpenID Connect][OpenIDConnect-ID-Token] dostarczony przez punkt końcowy [autoryzacji serwera autoryzacji,](#authorization-server) [authorization endpoint](#authorization-endpoint)który zawiera [oświadczenia](#claim) dotyczące uwierzytelniania [właściciela zasobu](#resource-owner)użytkownika końcowego. Podobnie jak token dostępu, tokeny identyfikatorów są również reprezentowane jako podpisany cyfrowo [token JSON Web Token (JWT).][JWT] W przeciwieństwie do tokenu dostępu jednak oświadczenia tokenu identyfikatora nie są używane do celów związanych z dostępem do zasobów, a w szczególności kontroli dostępu.

Zobacz [odwołanie tokenu platformy tożsamości firmy Microsoft, aby][AAD-Tokens-Claims] uzyskać więcej informacji.

## <a name="microsoft-identity-platform"></a>Platforma tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft to unowocześnienie platformy deweloperów i usługi tożsamości Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania programu Microsoft Graph, innych interfejsów API firmy Microsoft lub interfejsów API opracowanych przez deweloperów. Jest to w pełni funkcjonalna platforma, która składa się z usługi uwierzytelniania, bibliotek, rejestracji i konfiguracji aplikacji, pełnej dokumentacji dewelopera, przykładów kodu i innej zawartości dewelopera. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect. Zobacz [informacje o platformie tożsamości firmy Microsoft,](about-microsoft-identity-platform.md) aby uzyskać więcej informacji.

## <a name="multi-tenant-application"></a>aplikacja wielodostępna

Klasa aplikacji, która umożliwia logowanie i [zgody](#consent) przez użytkowników aprowied w dowolnej [dzierżawy](#tenant)usługi Azure AD, w tym dzierżawy innych niż ten, w którym klient jest zarejestrowany. [Natywne aplikacje klienckie](#native-client) są domyślnie wielodostępne, podczas gdy [klient sieci web](#web-client) i aplikacje [zasób sieci web/interfejs API](#resource-server) mają możliwość wyboru między pojedynczą lub wielodostępną. Z drugiej strony aplikacja sieci web zarejestrowana jako pojedyncza dzierżawa zezwalałaby tylko na logowania z kont użytkowników aprowizowanych w tej samej dzierżawie, co ta, w której aplikacja jest zarejestrowana.

Zobacz [Jak zalogować się dowolny użytkownik usługi Azure AD przy użyciu wzorca aplikacji z wieloma dzierżawami, aby][AAD-Multi-Tenant-Overview] uzyskać więcej informacji.

## <a name="native-client"></a>klient macierzysty

Typ [aplikacji klienckiej,](#client-application) która jest instalowana natywnie na urządzeniu. Ponieważ cały kod jest wykonywany na urządzeniu, jest uważany za klienta "publicznego" ze względu na jego niezdolność do przechowywania poświadczeń prywatnie/poufnie. Zobacz [OAuth2 typy klientów i profile, aby][OAuth2-Client-Types] uzyskać więcej informacji.

## <a name="permissions"></a>Uprawnienia

[Aplikacja kliencka](#client-application) uzyskuje dostęp do [serwera zasobów,](#resource-server) deklarując żądania uprawnień. Dostępne są dwa typy:

* Uprawnienia "Delegowane", które określają dostęp [oparty na zakresie](#scopes) przy użyciu autoryzacji delegowanej od właściciela [zasobu](#resource-owner)zalogowanych, są przedstawiane zasobowi w czasie wykonywania jako [oświadczenia "scp"](#claim) w [tokenie dostępu](#access-token)klienta .
* Uprawnienia "Application", które określają dostęp [oparty na rolach](#roles) przy użyciu poświadczeń/tożsamości aplikacji klienckiej, są przedstawiane zasobowi w czasie wykonywania jako [oświadczenia "role"](#claim) w tokenie dostępu klienta.

Mają one również powierzchnię podczas procesu [zgody,](#consent) dając administratorowi lub właścicielowi zasobu możliwość udzielenia/odmowy klientowi dostępu do zasobów w dzierżawie.

Żądania uprawnień są konfigurowane na stronie **uprawnień interfejsu API** dla aplikacji w [witrynie Azure portal,][AZURE-portal]wybierając żądane "Uprawnienia delegowane" i "Uprawnienia aplikacji" (te ostatnie wymagają członkostwa w roli administratora globalnego). Ponieważ [klient publiczny](#client-application) nie może bezpiecznie obsługiwać poświadczeń, może żądać tylko uprawnień delegowanych, podczas gdy [klient poufny](#client-application) ma możliwość żądania uprawnień delegowanych i aplikacji. [Obiekt aplikacji](#application-object) klienta przechowuje zadeklarowane uprawnienia we [właściwości requiredResourceAccess][Graph-App-Resource].

## <a name="resource-owner"></a>właściciel zasobu

Zgodnie z definicją [w ramach autoryzacji OAuth2][OAuth2-Role-Def], jednostka zdolna do udzielania dostępu do chronionego zasobu. Gdy właścicielem zasobu jest osoba, jest on określany jako użytkownik końcowy. Na przykład, gdy [aplikacja kliencka](#client-application) chce uzyskać dostęp do skrzynki pocztowej użytkownika za pośrednictwem [interfejsu API programu Microsoft Graph,][Microsoft-Graph]wymaga to uprawnień właściciela zasobu skrzynki pocztowej.

## <a name="resource-server"></a>serwer zasobów

Zgodnie z definicją [w ramach autoryzacji OAuth2][OAuth2-Role-Def], serwer, który obsługuje chronione zasoby, zdolne do akceptowania i odpowiadania na żądania chronionych zasobów przez [aplikacje klienckie,](#client-application) które prezentują [token dostępu](#access-token). Znany również jako chroniony serwer zasobów lub aplikacja zasobów.

Serwer zasobów udostępnia interfejsy API i wymusza dostęp do chronionych zasobów za pośrednictwem [zakresów](#scopes) i [ról,](#roles)przy użyciu struktury autoryzacji OAuth 2.0. Przykłady obejmują [interfejs API programu Microsoft Graph,][Microsoft-Graph] który zapewnia dostęp do danych dzierżawy usługi Azure AD i interfejsy API usługi Office 365, które zapewniają dostęp do danych, takich jak poczta i kalendarz. 

Podobnie jak aplikacja kliencka, konfiguracja tożsamości aplikacji zasobów jest ustanawiana za pomocą [rejestracji](#application-registration) w dzierżawie usługi Azure AD, zapewniając obiekt główny aplikacji i usługi. Niektóre interfejsy API dostarczone przez firmę Microsoft, takie jak interfejs API programu Microsoft Graph, mają wstępnie zarejestrowane jednostki usługi udostępnione we wszystkich dzierżawach podczas inicjowania obsługi administracyjnej.

## <a name="roles"></a>role

Podobnie jak [zakresy,](#scopes)role umożliwiają [serwerowi zasobów](#resource-server) regulowanie dostępu do chronionych zasobów. Istnieją dwa typy: rola "użytkownik" implementuje kontrolę dostępu opartą na rolach dla użytkowników/grup, które wymagają dostępu do zasobu, podczas gdy rola "aplikacja" implementuje to samo dla [aplikacji klienckich,](#client-application) które wymagają dostępu.

Role są ciągami zdefiniowanymi przez zasoby (na przykład "Osoba zatwierdzająca wydatek", "Tylko do odczytu", "Directory.ReadWrite.All"), zarządzane w [witrynie Azure portal][AZURE-portal] za pośrednictwem [manifestu aplikacji](#application-manifest)zasobu i przechowywane we właściwości [appRoles][Graph-Sp-Resource]zasobu. Portal Azure jest również używany do przypisywania użytkowników do ról "użytkownik" i konfigurowania [uprawnień aplikacji](#permissions) klienckiej w celu uzyskania dostępu do roli "aplikacji".

Aby uzyskać szczegółowe omówienie ról aplikacji udostępnianych przez interfejs API programu Microsoft Graph, zobacz [Zakresy uprawnień interfejsu API programu Graph][Graph-Perm-Scopes]. Aby zapoznać się z przykładem implementacji krok po kroku, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i witryny Azure portal.][AAD-RBAC]

## <a name="scopes"></a>scopes

Podobnie jak [role,](#roles)zakresy umożliwiają [serwerowi zasobów](#resource-server) regulowanie dostępu do chronionych zasobów. Zakresy są używane do implementowania kontroli dostępu [opartej na zakresie][OAuth2-Access-Token-Scopes] dla [aplikacji klienckiej,](#client-application) która otrzymała delegowany dostęp do zasobu przez jego właściciela.

Zakresy są ciągami zdefiniowanymi przez zasoby (na przykład "Mail.Read", "Directory.ReadWrite.All"), zarządzanych w [portalu Azure][AZURE-portal] za pośrednictwem [manifestu aplikacji](#application-manifest)zasobu i przechowywanych we właściwości [oauth2Permissions zasobu.][Graph-Sp-Resource] Portal Azure jest również używany do konfigurowania aplikacji klienckiej [delegowanych uprawnień](#permissions) dostępu do zakresu.

Najlepszym rozwiązaniem konwencji nazewnictwa, jest użycie formatu "resource.operation.constraint". Aby uzyskać szczegółowe omówienie zakresów udostępnianych przez interfejs API programu Microsoft Graph, zobacz [Zakresy uprawnień interfejsu API programu Graph][Graph-Perm-Scopes]. Aby uzyskać zakresy udostępniane przez usługi Office 365, zobacz [Odwołanie do uprawnień interfejsu API usługi Office 365][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpieczający

Podpisany dokument zawierający oświadczenia, takie jak token OAuth2 lub twierdzenie SAML 2.0. W przypadku [przyznania autoryzacji](#authorization-grant)OAuth2 [token dostępu](#access-token) (OAuth2) i [token identyfikatora](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) są typami tokenów zabezpieczających, które są implementowane jako [token sieci Web JSON (JWT).][JWT]

## <a name="service-principal-object"></a>główny obiekt usługi

Po zarejestrowaniu/zaktualizowaniu aplikacji w [witrynie Azure portal][AZURE-portal]portal tworzy/aktualizuje zarówno [obiekt aplikacji,](#application-object) jak i odpowiedni obiekt jednostkowy usługi dla tej dzierżawy. Obiekt aplikacji *definiuje* konfigurację tożsamości aplikacji globalnie (we wszystkich dzierżawach, do których przyznano dostęp skojarzonej aplikacji) i jest szablonem, z którego *pochodzą odpowiadające* jej obiekty główne usługi do użytku lokalnego w czasie wykonywania (w określonej dzierżawie).

Aby uzyskać więcej informacji, zobacz [Obiekty głównej aplikacji i usługi][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Proces aplikacji [klienckiej](#client-application) inicjującej uwierzytelnianie użytkownika końcowego i przechwytywania powiązanego stanu w celu uzyskania [tokenu zabezpieczającego](#security-token) i określania zakresu sesji aplikacji do tego stanu. Stan może zawierać artefakty, takie jak informacje o profilu użytkownika i informacje pochodzące z oświadczeń tokenu.

Funkcja logowania aplikacji jest zwykle używana do implementacji logowania jednokrotnego.The sign-in function of an application is typically used to implement single-sign-on (SSO). Może być również poprzedzona funkcją "zarejestruj się", jako punkt wejścia dla użytkownika końcowego, aby uzyskać dostęp do aplikacji (przy pierwszym logowaniu). Funkcja rejestracji służy do zbierania i utrwalania dodatkowego stanu specyficznego dla użytkownika i może wymagać [zgody użytkownika.](#consent)

## <a name="sign-out"></a>wylogowanie

Proces nieuwierzycia użytkownika końcowego, odłączanie stanu użytkownika skojarzonego z sesją [aplikacji klienckiej](#client-application) podczas [logowania](#sign-in)

## <a name="tenant"></a>Dzierżawy

Wystąpienie katalogu usługi Azure AD jest określane jako dzierżawa usługi Azure AD. Oferuje on kilka funkcji, w tym:

* usługa rejestru dla zintegrowanych aplikacji
* uwierzytelnianie kont użytkowników i zarejestrowanych aplikacji
* Punkty końcowe REST wymagane do obsługi różnych protokołów, w tym OAuth2 i SAML, w tym [punktu końcowego autoryzacji,](#authorization-endpoint) [tokenu końcowego](#token-endpoint) i "wspólnego" punktu końcowego używanego przez [aplikacje z wieloma dzierżawami.](#multi-tenant-application)

Dzierżawy usługi Azure AD są tworzone/skojarzone z subskrypcjami platformy Azure i usługi Office 365 podczas rejestracji, zapewniając funkcje zarządzania dostępem do tożsamości & dla subskrypcji. Administratorzy subskrypcji platformy Azure mogą również tworzyć dodatkowe dzierżawy usługi Azure AD za pośrednictwem witryny Azure portal. Zobacz [Jak uzyskać dzierżawę usługi Azure Active Directory, aby][AAD-How-To-Tenant] uzyskać szczegółowe informacje na temat różnych sposobów uzyskiwania dostępu do dzierżawy. Zobacz, [jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory,][AAD-How-Subscriptions-Assoc] aby uzyskać szczegółowe informacje na temat relacji między subskrypcjami a dzierżawą usługi Azure AD.

## <a name="token-endpoint"></a>punkt końcowy tokenu

Jeden z punktów końcowych zaimplementowanych przez [serwer autoryzacji](#authorization-server) do obsługi [autoryzacji](#authorization-grant)OAuth2 udziela . W zależności od dotacji może służyć do uzyskania [tokenu dostępu](#access-token) (i powiązanego tokenu "odświeżania") do [klienta](#client-application)lub [tokenu identyfikatora,](#id-token) gdy jest używany z protokołem [OpenID Connect.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Klient oparty na agencie użytkownika

Typ [aplikacji klienckiej,](#client-application) która pobiera kod z serwera sieci web i wykonuje w ramach agenta użytkownika (na przykład przeglądarki sieci web), takich jak aplikacja jednostronicowa (SPA). Ponieważ cały kod jest wykonywany na urządzeniu, jest uważany za klienta "publicznego" ze względu na jego niezdolność do przechowywania poświadczeń prywatnie/poufnie. Aby uzyskać więcej informacji, zobacz [Typy i profile klientów OAuth2][OAuth2-Client-Types].

## <a name="user-principal"></a>główny użytkownik

Podobnie jak obiekt jednostkowy usługi jest używany do reprezentowania wystąpienia aplikacji, główny obiekt użytkownika jest inny typ podmiotu zabezpieczeń, który reprezentuje użytkownika. [Typ zasobu użytkownika][Graph-User-Resource] programu Microsoft Graph definiuje schemat obiektu użytkownika, w tym właściwości związane z użytkownikiem, takie jak imię i nazwisko, nazwa główna użytkownika, członkostwo w roli katalogu itp. Zapewnia to konfigurację tożsamości użytkownika dla usługi Azure AD w celu ustanowienia podmiotu zabezpieczeń użytkownika w czasie wykonywania. Podmiot zabezpieczeń użytkownika jest używany do reprezentowania uwierzytelnionego użytkownika do logowania jednokrotnego, delegowania [zgody](#consent) na nagrywanie, podejmowanie decyzji dotyczących kontroli dostępu itp.

## <a name="web-client"></a>klient internetowy

Typ [aplikacji klienckiej,](#client-application) która wykonuje cały kod na serwerze sieci web i może działać jako klient "poufny" bezpiecznie przechowując swoje poświadczenia na serwerze. Aby uzyskać więcej informacji, zobacz [Typy i profile klientów OAuth2][OAuth2-Client-Types].

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera platformy tożsamości firmy Microsoft][AAD-Dev-Guide] jest stroną docelową używaną do wszystkich tematów związanych z rozwojem platformy tożsamości firmy Microsoft, w tym omówienie [integracji aplikacji][AAD-How-To-Integrate] i podstaw [uwierzytelniania platformy tożsamości firmy Microsoft i obsługiwanych scenariuszy uwierzytelniania.][AAD-Auth-Scenarios] Możesz również znaleźć przykłady kodu & samouczki dotyczące szybkiego uruchamiania na [GitHubie.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)

Skorzystaj z poniższej sekcji komentarzy, aby przekazać opinię i pomóc uściślić i ukształtować tę zawartość, w tym prośby o nowe definicje lub zaktualizowanie istniejących!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
