---
title: Słownik dewelopera usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Lista warunków dla często używanych koncepcje dla deweloperów usługi Azure Active Directory i funkcji.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 12c1a4b2b1f3e433721b9c8a335c6b55de746643
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-developer-glossary"></a>Słownik dewelopera usługi Azure Active Directory
Ten artykuł zawiera definicje dla niektórych podstawowe koncepcje dla deweloperów usługi Azure Active Directory (AD), które są przydatne podczas nauki opracowywanie aplikacji dla usługi Azure AD.

## <a name="access-token"></a>token dostępu
Typ [tokenu zabezpieczającego](#security-token) wystawiony przez [serwera autoryzacji](#authorization-server)i używane przez [aplikacji klienckiej](#client-application) Aby uzyskać dostęp do [chroniony serwer zasobów](#resource-server). Zwykle w postaci [tokenu Web JSON (JWT)][JWT], token stanowią umocowania do klienta przez [właściciel zasobu](#resource-owner), żądany poziom dostępu. Token zawiera wszystkich odpowiednich [oświadczeń](#claim) związane z tym tematem, włączanie aplikacji klienckiej używać go jako formularza poświadczenia podczas uzyskiwania dostępu do zasobu. Eliminuje to potrzebę właściciela zasobów do udostępnienia poświadczeń do klienta.

Tokeny dostępu są czasami określane jako "Użytkownik + aplikacja" lub "App tylko", w zależności od reprezentowanego poświadczenia. Na przykład, jeśli aplikacja kliencka używa:

* ["Kod autoryzacji" udzielania autoryzacji](#authorization-grant), użytkownik uwierzytelnia najpierw jako właściciela zasobu, delegowanie autoryzacji do klienta dostępu do zasobu. Klient uwierzytelnia później podczas uzyskiwania tokenu dostępu. Tokenu można czasami określane dokładniej jako tokenu "Użytkownik + aplikacji", ponieważ reprezentuje on zarówno wobec użytkownika, które uprawnienia aplikacji klienckiej i aplikacji.
* [Udzielania autoryzacji "Poświadczeń klienta"](#authorization-grant), klient zawiera wyłącznie uwierzytelnianie działa bez właściciela zasobu uwierzytelniania/autoryzacji, więc tokenu można czasami określane jako token "Tylko do aplikacji".

Zobacz [odwołania do usługi Azure AD tokenu] [ AAD-Tokens-Claims] więcej szczegółów.

## <a name="application-id-client-id"></a>Identyfikator aplikacji (identyfikator klienta)
Unikatowy identyfikator usługi Azure AD problemy rejestracji aplikacji, który identyfikuje określoną aplikację i skojarzony konfiguracji. Ten identyfikator aplikacji ([identyfikator klienta](https://tools.ietf.org/html/rfc6749#page-15)) jest używany podczas uwierzytelniania żądań i jest dostarczane do bibliotek uwierzytelniania w czasie opracowywania. Identyfikator aplikacji (identyfikator klienta) nie jest klucz tajny. 

## <a name="application-manifest"></a>Manifest aplikacji
Funkcja dostarczonych przez [portalu Azure][AZURE-portal], która tworzy reprezentację JSON Konfiguracja tożsamości aplikacji, używane jako mechanizm aktualizacji skojarzone [ Aplikacja] [ AAD-Graph-App-Entity] i [ServicePrincipal] [ AAD-Graph-Sp-Entity] jednostek. Zobacz [opis manifestu aplikacji usługi Azure Active Directory] [ AAD-App-Manifest] więcej szczegółów.

## <a name="application-object"></a>Obiekt aplikacji
Gdy użytkownik rejestru/aktualizacji aplikacji w [portalu Azure][AZURE-portal], portalu utworzeń/aktualizacji obiekt aplikacji i odpowiadające mu [obiekt główny usługi](#service-principal-object) dla tego dzierżawcy. Obiekt application *definiuje* aplikacji do konfiguracji tożsamości globalnie (za pośrednictwem wszystkich dzierżaw który ma dostęp), zapewniając szablonu, z którego są odpowiednie obiekty główne usługi  *pochodnych* do użytku lokalnie w czasie wykonywania (w określonym dzierżawcy).

Zobacz [aplikacji i nazwy głównej usługi obiektów] [ AAD-App-SP-Objects] Aby uzyskać więcej informacji.

## <a name="application-registration"></a>Rejestracja aplikacji
Aby można było zezwolić aplikacji na integrują się z i delegować funkcje Zarządzanie tożsamościami i dostępem do usługi Azure AD, musi być zarejestrowana w usłudze Azure AD [dzierżawy](#tenant). Podczas rejestrowania aplikacji z usługą Azure AD, czy podajesz konfiguracji tożsamości aplikacji, dzięki któremu można zintegrować z usługą Azure AD i korzystać z funkcji takich jak:

* Niezawodne funkcje zarządzania z logowaniem jednokrotnym przy użyciu usługi Azure AD Identity Management i [OpenID Connect] [ OpenIDConnect] implementacji protokołu
* Obsługiwane przez brokera dostęp do [chronione zasoby](#resource-server) przez [aplikacje klienckie](#client-application), za pomocą usługi Azure AD OAuth 2.0 [serwera autoryzacji](#authorization-server) implementacji
* [Framework zgody](#consent) do zarządzania dostępem klientów do chronionych zasobów, w oparciu o autoryzacji właściciela zasobów.

Zobacz [Integrowanie aplikacji z usługą Azure Active Directory] [ AAD-Integrating-Apps] więcej szczegółów.

## <a name="authentication"></a>uwierzytelnianie
Czynność wyzwanie strona uzasadnionych poświadczeń, podając podstawę tworzenia podmiotu zabezpieczeń do użycia dla tożsamości i kontroli dostępu. Podczas [udzielania autoryzacji OAuth2](#authorization-grant) na przykład strona uwierzytelniania jest wypełnianie rolę albo [właściciel zasobu](#resource-owner) lub [aplikacji klienckiej](#client-application), w zależności od Udziel używane.

## <a name="authorization"></a>Autoryzacji
Czynność udzielenia uprawnienia głównego uwierzytelnionego zabezpieczeń coś zrobić. Istnieją dwa przypadki użycia podstawowego modelu programowania usługi Azure AD:

* Podczas [udzielania autoryzacji OAuth2](#authorization-grant) przepływu: podczas [właściciel zasobu](#resource-owner) przyznaje autoryzacji do [aplikacji klienckiej](#client-application), co pozwala klientowi dostęp do zasobu właściciela zasobów.
* Podczas dostępu do zasobów przez klienta: zaimplementowanego przez [serwer zasobów](#resource-server)za pomocą [oświadczeń](#claim) wartości znajdujących się w [token dostępu](#access-token) Aby sterować dostępem na ich podstawie.

## <a name="authorization-code"></a>Kod autoryzacji
Krótko 'token' dostarczony do [aplikacji klienckiej](#client-application) przez [punktu końcowego autoryzacji](#authorization-endpoint), jako część przepływu "Kod autoryzacji", za pomocą jednej z czterech OAuth2 [przyznajeautoryzacji](#authorization-grant). Kod jest zwracana do aplikacji klienckiej w odpowiedzi na uwierzytelnianie [właściciel zasobu](#resource-owner), wskazujący właściciela zasobów przekazała autoryzacji dostępu do żądanych zasobów. W ramach przepływu, kod później zrealizowane dla [token dostępu](#access-token).

## <a name="authorization-endpoint"></a>Punkt końcowy autoryzacji
Jeden z punktów końcowych, implementowane przez [serwera autoryzacji](#authorization-server), używana do interakcji z [właściciel zasobu](#resource-owner) zapewnić [udzielania autoryzacji](#authorization-grant) podczas OAuth2 Przepływ grant autoryzacji. W zależności od przepływ grant autoryzacji używany, rzeczywistego przyznania podane mogą się różnić, łącznie z [kod autoryzacji](#authorization-code) lub [tokenu zabezpieczającego](#security-token).

Zobacz specyfikację OAuth2 [typy przydziałów autoryzacji] [ OAuth2-AuthZ-Grant-Types] i [punktu końcowego autoryzacji] [ OAuth2-AuthZ-Endpoint] sekcje i [Specyfikacji OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] więcej szczegółów.

## <a name="authorization-grant"></a>udzielania autoryzacji
Poświadczenie przedstawiający [właściciel zasobu](#resource-owner) [autoryzacji](#authorization) można uzyskać dostępu do chronionych zasobów, przyznane [aplikacji klienckiej](#client-application). Aplikacja kliencka można użyć jednej z [przyznać cztery typy zdefiniowane przez platformę autoryzacji OAuth2] [ OAuth2-AuthZ-Grant-Types] uzyskanie grant, w zależności od wymagania typu klienta: "udzielania kodu autoryzacji", "client poświadczenia umożliwiają","niejawne Przyznaj"i"przyznać poświadczenia hasła właściciela zasobu". Poświadczenia do klienta zwracany jest [token dostępu](#access-token), lub [kod autoryzacji](#authorization-code) (wymieniane później, aby uzyskać token dostępu), w zależności od typu udzielania autoryzacji używany.

## <a name="authorization-server"></a>Serwer autoryzacji
Zgodnie z definicją w [Framework autoryzacji OAuth2][OAuth2-Role-Def], odpowiedzialne za wystawianie dostępu do serwera tokenów do [klienta](#client-application) po pomyślnym uwierzytelnieniu [właściciel zasobu](#resource-owner) i uzyskanie jego autoryzacji. A [aplikacji klienckiej](#client-application) współdziała z serwera autoryzacji w czasie wykonywania za pośrednictwem jego [autoryzacji](#authorization-endpoint) i [tokenu](#token-endpoint) zdefiniowanychpunktówkońcowych,zgodniezOAuth2[przyznaje autoryzacji](#authorization-grant).

W przypadku integracji aplikacji usługi Azure AD, Azure AD implementuje rolę serwera autoryzacji dla aplikacji usługi Azure AD i usługi firmy Microsoft interfejsy API, na przykład [Microsoft Graph API][Microsoft-Graph].

## <a name="claim"></a>oświadczenie
A [tokenu zabezpieczającego](#security-token) zawiera oświadczenia, które zapewniają potwierdzenia jednostek o jednym (takich jak [aplikacji klienckiej](#client-application) lub [właściciel zasobu](#resource-owner)) do innej jednostki (na przykład [serwer zasobów](#resource-server)). Oświadczenia są pary nazwa/wartość, które przekazywania informacji o token podmiotu (na przykład przez został uwierzytelniony podmiot zabezpieczeń [serwera autoryzacji](#authorization-server)). Token zawiera danego oświadczenia są zależne od kilku zmiennych, takich jak typ tokenu, typ poświadczenia używany do uwierzytelniania na temat konfiguracji aplikacji, itp.

Zobacz [odwołania do tokenu usługi Azure AD] [ AAD-Tokens-Claims] więcej szczegółów.

## <a name="client-application"></a>Aplikacja kliencka
Zgodnie z definicją w [Framework autoryzacji OAuth2][OAuth2-Role-Def], aplikacji, która sprawia, że chroniony zasób żądania w imieniu [właściciel zasobu](#resource-owner). Termin "klient" oznacza wszystkie właściwości wykonania konkretnego sprzętu (na przykład, czy aplikacja wykonuje na serwerze, pulpitu lub innych urządzeniach). 

Aplikacja kliencka żąda [autoryzacji](#authorization) od właściciela zasobu, aby uczestniczyć w [udzielania autoryzacji OAuth2](#authorization-grant) przepływu i może uzyskać dostępu do interfejsów API/danych w imieniu właściciela zasobów. Framework autoryzacji OAuth2 [definiują dwa typy klientów][OAuth2-Client-Types]"poufne dane" i "public", oparte na możliwość zachowania poufności swoich poświadczeń klienta. Aplikacje można wdrożyć [klienta sieci web (poufne)](#web-client) na serwerze sieci web, które są uruchamiane [aplikacja native client (publicznej)](#native-client) zainstalowana na urządzeniu, lub [na podstawie agent użytkownika klienta (publicznej)](#user-agent-based-client)które są uruchamiane w przeglądarce urządzenia.

## <a name="consent"></a>Zgody
Proces [właściciel zasobu](#resource-owner) autoryzacji do przyznawania [aplikacji klienckiej](#client-application), aby uzyskiwać dostęp do chronionych zasobów w obszarze określonej [uprawnienia](#permissions), zastępczy właściciel zasobu. W zależności od uprawnień żądany przez klienta administrator lub użytkownik zostanie poproszony o zgodę na dostęp do swoich danych organizacji/osoba odpowiednio. Uwaga: w [wielodostępne](#multi-tenant-application) scenariusza, aplikacji [nazwy głównej usługi](#service-principal-object) jest zarejestrowany w dzierżawie consenting użytkownika.

## <a name="id-token"></a>Identyfikator tokenu
[OpenID Connect] [ OpenIDConnect-ID-Token] [tokenu zabezpieczającego](#security-token) dostarczonych przez [serwera autoryzacji](#authorization-server) [punktu końcowego autoryzacji](#authorization-endpoint), który zawiera [oświadczeń](#claim) odnoszące się do uwierzytelniania użytkownika końcowego [właściciel zasobu](#resource-owner). Podobnie jak tokenu dostępu, tokeny Identyfikatora również są przedstawiane jako cyfrowo podpisanych [tokenu Web JSON (JWT)][JWT]. W przeciwieństwie do tokenu dostępu, token identyfikator oświadczenia nie są używane do celów związanych z dostępem do zasobów i specjalnie kontroli dostępu.

Zobacz [odwołania do tokenu usługi Azure AD] [ AAD-Tokens-Claims] więcej szczegółów.

## <a name="multi-tenant-application"></a>wielodostępne aplikacji
Klasy aplikacji, która umożliwia logowanie i [zgody](#consent) przez użytkowników udostępniane w dowolnym usługi Azure AD [dzierżawy](#tenant), tym dzierżaw innego niż ten, w którym klient jest zarejestrowany. [Aplikacja Native client](#native-client) aplikacje są wielodostępne domyślnie, natomiast [klienta sieci web](#web-client) i [zasobów/interfejs API sieci web](#resource-server) aplikacje mają możliwość wybrania między jednym lub wieloma dzierżawcami. Z drugiej strony aplikacji sieci web w zarejestrowany jako pojedynczego dzierżawcy tylko umożliwia logowania z kont użytkowników obsługiwana w tej samej dzierżawy co rejestracji aplikacji.

Zobacz [jak zarejestrować każdy użytkownik usługi Azure AD przy użyciu wzorca wielodostępnych aplikacji] [ AAD-Multi-Tenant-Overview] więcej szczegółów.

## <a name="native-client"></a>Aplikacja Native client
Typ [aplikacji klienckiej](#client-application) oryginalnie zainstalowana na urządzeniu. Ponieważ cały kod jest wykonywane na urządzeniu, jest traktowane jako "public" klienta z powodu jego brakiem do przechowywania poświadczeń prywatnie/jako poufne. Zobacz [OAuth2 klienta typów i profile] [ OAuth2-Client-Types] więcej szczegółów.

## <a name="permissions"></a>uprawnienia
A [aplikacji klienckiej](#client-application) uzyskuje dostęp do [serwer zasobów](#resource-server) przez zadeklarowanie żądanych uprawnień. Dostępne są dwa typy:

* "Delegowane" uprawnienia, które określają [zakresu](#scopes) uzyskiwać dostęp za pomocą autoryzacji delegowania od zalogowanego [właściciel zasobu](#resource-owner), są widoczne dla zasobów w czasie wykonywania jako ["punkt połączenia usługi" oświadczenia](#claim) na komputerze klienckim [token dostępu](#access-token).
* Uprawnienia "Aplikacja", które określają [opartej na rolach](#roles) uzyskiwać dostęp za pomocą poświadczeń/tożsamości aplikacji klienta, są widoczne dla zasobów w czasie wykonywania jako [oświadczenia "role"](#claim) na komputerze klienckim token dostępu.

Również powierzchni, podczas [zgody](#consent) procesu zapoznania się z administratorem lub właściciel zasobu grant/odrzucanie dostęp klienta do zasobów w swojej dzierżawy.

Żądania uprawnień są skonfigurowane na "Aplikacji" / "Ustawienia" karcie w [portalu Azure][AZURE-portal], w obszarze "Wymagane uprawnienia", wybierając odpowiednie "Delegowane uprawnienia" i "aplikacji Uprawnienia"(drugie wymaga członkostwa w roli administratora globalnego). Ponieważ [publicznego klienta](#client-application) nie może bezpiecznie Obsługa poświadczeń, może zażądać jedynie delegowane uprawnienia podczas [poufne klienta](#client-application) ma możliwość żądania aplikacją i delegowanego uprawnienia. Klienta [obiektu aplikacji](#application-object) przechowuje zadeklarowane uprawnienia w jego [właściwości requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>właściciel zasobu
Zgodnie z definicją w [Framework autoryzacji OAuth2][OAuth2-Role-Def], możliwość przyznania dostępu do chronionego zasobu jednostki. Jeśli właściciel zasobu jest osobą, ona jest określana jako użytkownik końcowy. Na przykład, jeśli [aplikacji klienckiej](#client-application) chce dostęp do skrzynek pocztowych użytkowników za pośrednictwem [interfejsu API programu Microsoft Graph][Microsoft-Graph], wymaga uprawnień od właściciela zasobu Skrzynka pocztowa.

## <a name="resource-server"></a>serwer zasobów
Zgodnie z definicją w [Framework autoryzacji OAuth2][OAuth2-Role-Def], ochronę zasobów mogą akceptować i reagowanie na hostach serwera chronionego zasobu żądania przez [klienta aplikacje](#client-application) tego obecnie [token dostępu](#access-token). Znanej także jako serwer chronionych zasobów lub zasobów aplikacji.

Serwer zasobów udostępnia interfejsy API i wymusza dostęp do jej zasobów chronionych za pomocą [zakresy](#scopes) i [ról](#roles), za pomocą środowiska autoryzacji OAuth 2.0. Przykładami interfejsu API Azure AD Graph, która zapewnia dostęp do danych dzierżawy usługi Azure AD i Office 365 interfejsów API służących do zapewnienia dostępu do danych, takich jak Poczta i kalendarz. Oba te są również dostępne za pośrednictwem [interfejsu API programu Microsoft Graph][Microsoft-Graph]. 

Podobnie jak aplikacja kliencka, konfiguracja tożsamości aplikacji zasobów zostanie nawiązane za pośrednictwem [rejestracji](#application-registration) w dzierżawie usługi Azure AD, zapewniając aplikacji i obiekt główny usługi. Niektóre API firmy Microsoft, takich jak Azure AD Graph API, wstępnie zarejestrowane nazwy główne usług dostępnych w wszystkich dzierżawców podczas inicjowania obsługi.

## <a name="roles"></a>role
Podobnie jak [zakresy](#scopes), role umożliwiają [serwer zasobów](#resource-server) dotyczące dostępu do chronionych zasobów. Istnieją dwa typy: "rola" implementuje kontroli dostępu opartej na rolach dla użytkowników/grupy, które wymagają dostępu do zasobu, podczas gdy rolę "aplikacji" implementuje takie same dla [aplikacje klienckie](#client-application) wymagających dostępu.

Role są zdefiniowane zasobów ciągów (na przykład "osoba zatwierdzająca wydatków", "Tylko do odczytu", "Directory.ReadWrite.All"), są zarządzane w [portalu Azure] [ AZURE-portal] za pośrednictwem zasobu [aplikacji Manifest](#application-manifest)i przechowywane w zasobie [właściwości appRoles][AAD-Graph-Sp-Entity]. Azure portal służy również do przypisywania użytkowników do ról "użytkownika" i skonfiguruj klienta [uprawnienia aplikacji](#permissions) do roli "aplikacji".

Szczegółowe omówienie ról aplikacji udostępnianych przez interfejs API programu Graph usługi Azure AD, zobacz [zakresy uprawnień interfejsu API Graph][AAD-Graph-Perm-Scopes]. Na przykład implementacji krok po kroku, zobacz [oparta na rolach kontrola dostępu w aplikacji w chmurze przy użyciu usługi Azure AD][Duyshant-Role-Blog].

## <a name="scopes"></a>Zakresy
Podobnie jak [ról](#roles), zakresy umożliwiają [serwer zasobów](#resource-server) dotyczące dostępu do chronionych zasobów. Zakresy są używane do implementowania [zakresu] [ OAuth2-Access-Token-Scopes] kontroli dostępu, aby uzyskać [aplikacji klienckiej](#client-application) że została podana dostęp delegowany do zasobu przez jego właściciela.

Zakresy są zdefiniowane zasobów ciągów (na przykład "Mail.Read", "Directory.ReadWrite.All"), zarządzane w [portalu Azure] [ AZURE-portal] za pośrednictwem zasobu [manifest aplikacji](#application-manifest)i przechowywane w zasobie [właściwości oauth2Permissions][AAD-Graph-Sp-Entity]. Portalu Azure umożliwia również skonfigurowanie aplikacji klienta [delegowane uprawnienia](#permissions) do zakresu dostępu.

Najlepsze praktyki konwencji nazewnictwa, ma mieć format "resource.operation.constraint". Szczegółowe omówienie zakres udostępniany przez interfejs API programu Graph usługi Azure AD, zobacz [zakresy uprawnień interfejsu API Graph][AAD-Graph-Perm-Scopes]. Dla zakresów udostępnianych przez usługi Office 365, zobacz [odwołania uprawnień interfejsu API usługi Office 365][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpieczający
Podpisanego dokumentu zawierające te oświadczenia, takich jak tokenu OAuth2 lub potwierdzenia SAML 2.0. Dla OAuth2 [udzielania autoryzacji](#authorization-grant), [token dostępu](#access-token) (OAuth2) i [Token Identyfikatora](http://openid.net/specs/openid-connect-core-1_0.html#IDToken) są typy tokenów zabezpieczających, które są zaimplementowane jako [JSON Sieci Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>Obiekt główny usługi
Gdy użytkownik rejestru/aktualizacji aplikacji w [portalu Azure][AZURE-portal], portalu utworzeń/aktualizacji zarówno [obiektu aplikacji](#application-object) i odpowiedni obiekt główny usługi dla tego dzierżawcy. Obiekt application *definiuje* Konfiguracja tożsamości aplikacji globalnie (za pośrednictwem wszystkich dzierżaw gdzie skojarzonej aplikacji udzielono dostępu), i szablon, z którego jest jego odpowiedniej nazwy głównej usługi obiekty są *pochodnych* do użytku lokalnie w czasie wykonywania (w określonym dzierżawcy).

Zobacz [aplikacji i nazwy głównej usługi obiektów] [ AAD-App-SP-Objects] Aby uzyskać więcej informacji.

## <a name="sign-in"></a>logowanie
Proces [aplikacji klienckiej](#client-application) inicjowanie uwierzytelniania użytkownika końcowego i przechwytywanie stanu na potrzeby pobierania powiązane [tokenu zabezpieczającego](#security-token) i określania zakresu sesji aplikacji do tego stanu. Stanu może zawierać artefaktów na przykład informacje o profilu użytkownika i informacji pochodzących z tokenu oświadczeń.

Funkcja logowania aplikacji jest zwykle używane do implementacji-jednokrotnej (SSO). Go może być poprzedzone funkcję "zapisywania do" jako punkt wejścia dla użytkownika końcowego w celu uzyskania dostępu do aplikacji (na po w wypadku pierwszego logowania). Funkcja tworzenia konta służy do zbierania i utrwalić stanu dodatkowe specyficzne dla użytkownika i może wymagać [zgody użytkownika](#consent).

## <a name="sign-out"></a>wylogowanie
Proces odinstalowywania uwierzytelniania użytkownika końcowego, odłączenie stanu użytkownika skojarzony z [aplikacji klienckiej](#client-application) sesji podczas [logowania](#sign-in)

## <a name="tenant"></a>dzierżawa
Wystąpienie katalogu usługi Azure AD jest określana jako dzierżawa usługi Azure AD. Zapewnia kilka funkcji, takich jak:

* Usługa rejestru dla zintegrowanych aplikacji
* Uwierzytelnianie konta użytkowników i zarejestrowanych aplikacji
* Punkty końcowe REST wymagane do obsługi różnych protokołów, w tym OAuth2 i SAML, łącznie z [punktu końcowego autoryzacji](#authorization-endpoint), [punktu końcowego tokena](#token-endpoint) i "typowych" punktowi końcowemu używanemu przez [ aplikacje wielodostępne](#multi-tenant-application).

Dzierżaw usługi Azure AD są tworzone lub skojarzyć za pomocą subskrypcji platformy Azure i usługi Office 365 podczas tworzenia konta, zapewniając funkcje tożsamość i zarządzanie dostępem dla subskrypcji. Administratorzy subskrypcji platformy Azure można również utworzyć dodatkowych dzierżaw usługi Azure AD za pomocą portalu Azure. Zobacz [jak uzyskać dzierżawę usługi Azure Active Directory] [ AAD-How-To-Tenant] szczegółowe informacje na temat sposobów możesz uzyskać dostęp do dzierżawy. Zobacz [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory] [ AAD-How-Subscriptions-Assoc] szczegółowe informacje na temat relacji między subskrypcjami i dzierżawa usługi Azure AD.

## <a name="token-endpoint"></a>Token punktu końcowego
Jeden z punktów końcowych, implementowane przez [serwera autoryzacji](#authorization-server) do obsługi protokołu OAuth2 [przyznaje autoryzacji](#authorization-grant). W zależności od grant, może służyć do uzyskania [token dostępu](#access-token) (i powiązanych tokenu "Odśwież") do [klienta](#client-application), lub [token Identyfikatora](#ID-token) w przypadku użycia z [OpenID Połącz] [ OpenIDConnect] protokołu.

## <a name="user-agent-based-client"></a>Na podstawie agent użytkownika klienta
Typ [aplikacji klienckiej](#client-application) pobierania kodu z serwera sieci web i wykonuje w agent użytkownika (na przykład przeglądarki sieci web), takich jak jednej strony aplikacji JEDNOSTRONICOWEJ. Ponieważ cały kod jest wykonywane na urządzeniu, jest traktowane jako "public" klienta z powodu jego brakiem do przechowywania poświadczeń prywatnie/jako poufne. Zobacz [OAuth2 klienta typów i profile] [ OAuth2-Client-Types] więcej szczegółów.

## <a name="user-principal"></a>głównej nazwy użytkownika
Podobnie jak obiekt główny usługi jest używana do reprezentowania wystąpienia aplikacji, obiekt główny użytkownik jest innego typu podmiot zabezpieczeń, który reprezentuje użytkownika. Azure AD Graph [jednostki użytkownika] [ AAD-Graph-User-Entity] definiuje schemat dla obiekt użytkownika, w tym właściwości skojarzone z użytkownikiem, takie jak imię i nazwisko, główna nazwa użytkownika, członkostwo w roli katalogu itp. Zapewnia to konfiguracja tożsamości użytkownika dla usługi Azure AD ustanowić podmiot zabezpieczeń użytkownika w czasie wykonywania. Głównej nazwy użytkownika jest używana do reprezentowania uwierzytelnionego użytkownika dla logowania jednokrotnego, rejestrowanie [zgody](#consent) delegowania, co decyzje dotyczące kontroli dostępu, itd.

## <a name="web-client"></a>Klient sieci Web
Typ [aplikacji klienckiej](#client-application) , który jest wykonywany całego kodu na serwerze sieci web i może działać jako klient "poufne dane" przez bezpieczne przechowywanie swoich poświadczeń na serwerze. Zobacz [OAuth2 klienta typów i profile] [ OAuth2-Client-Types] więcej szczegółów.

## <a name="next-steps"></a>Kolejne kroki
[Przewodnik dewelopera usługi Azure AD] [ AAD-Dev-Guide] jest strony docelowej, można użyć dla wdrożenia usługi Azure AD wszystkich powiązanych tematów, w tym omówienie [integracji aplikacji] [ AAD-How-To-Integrate] i podstawy [uwierzytelniania usługi Azure AD i scenariusze obsługiwane uwierzytelniania][AAD-Auth-Scenarios]. Możesz również znaleźć przykłady kodu i samouczki dotyczące sposobu uzyskiwania szybkiego skonfigurowania i uruchomienia na [Github](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Użyj poniższej sekcji komentarzy, aby wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość, włącznie z żądaniami dostępność nowych definicji lub aktualizowania istniejących!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ../active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
