---
title: Słownik deweloperów platforma tożsamości firmy Microsoft | Azure
description: Lista warunków dla często używane pojęcia dla deweloperów platformy tożsamości firmy Microsoft i funkcji.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/21/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 314d7a8e8cf6837e2b22446ba23fee03d539bf35
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235355"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Słownik deweloperów platforma tożsamości firmy Microsoft

Ten artykuł zawiera definicje niektóre podstawowe pojęcia dla deweloperów i terminologia, które są przydatne, gdy poznawania opracowywanie aplikacji przy użyciu platformy tożsamości firmy Microsoft.

## <a name="access-token"></a>token dostępu

Rodzaj [token zabezpieczający](#security-token) wystawiony przez [serwera autoryzacji](#authorization-server)i używane przez [aplikacja kliencka](#client-application) celu uzyskania dostępu do [chroniony serwer zasobów](#resource-server). Zwykle w formie [tokenu Web JSON (JWT)][JWT], token stanowią autoryzacji udzielone do klienta przez [właściciel zasobu](#resource-owner), dla żądanego poziomu dostępu. Token zawiera wszystkich odpowiednich [oświadczeń](#claim) związane z tym tematem, umożliwiając aplikacji klienckiej używać go jako formę poświadczenia podczas uzyskiwania dostępu do danego zasobu. Eliminuje to potrzebę właściciel zasobu do udostępnienia poświadczeń do klienta.

Tokeny dostępu są czasami określane jako "Użytkownik + App" lub "Tylko dla aplikacji", w zależności od poświadczenia reprezentowanego. Na przykład, gdy aplikacja kliencka używa:

* ["Kod autoryzacji" autoryzację](#authorization-grant), użytkownik końcowy uwierzytelnia się najpierw jako właściciela zasobu, delegowanie autoryzacji do klienta dostępu do zasobu. Klient uwierzytelnia się później, podczas uzyskiwania tokenu dostępu. Token może czasami określane dokładniej jako token "Użytkownik + aplikacja", ponieważ reprezentuje on zarówno wobec użytkownika który autoryzowanych aplikacji klienckiej i aplikacji.
* ["Klient poświadczenia" autoryzację](#authorization-grant), klient zawiera wyłącznie uwierzytelnianie działa bez właściciel zasobu uwierzytelniania/autoryzacji, więc token może czasami określane jako token "Tylko do aplikacji".

Zobacz [platforma tożsamości usługi Microsoft odwołania do tokenu] [ AAD-Tokens-Claims] Aby uzyskać więcej informacji.

## <a name="application-id-client-id"></a>Identyfikator aplikacji (identyfikator klienta)

Problemy Unikatowy identyfikator usługi Azure AD do rejestracji aplikacji, identyfikująca określoną aplikację i skojarzony konfiguracji. Tego Identyfikatora aplikacji ([identyfikator klienta](https://tools.ietf.org/html/rfc6749#page-15)) jest używany podczas uwierzytelniania żądań i jest dostarczane do bibliotek uwierzytelniania w czasie projektowania. Identyfikator aplikacji (identyfikator klienta) nie jest klucz tajny.

## <a name="application-manifest"></a>manifest aplikacji

Funkcja dostarczone przez [witryny Azure portal][AZURE-portal], który tworzy reprezentację JSON Konfiguracja tożsamości aplikacji, używana jako mechanizm aktualizacji związanych z nią [ Aplikacja] [ AAD-Graph-App-Entity] i [ServicePrincipal] [ AAD-Graph-Sp-Entity] jednostek. Zobacz [opis manifestu aplikacji usługi Azure Active Directory] [ AAD-App-Manifest] Aby uzyskać więcej informacji.

## <a name="application-object"></a>Obiekt aplikacji

Po użytkownik rejestru/Zaktualizuj aplikację w [witryny Azure portal][AZURE-portal], portalu/aktualizacji obiektu aplikacji i odpowiadający mu [obiektu jednostki usługi](#service-principal-object) dla tej dzierżawy. Obiekt aplikacji *definiuje* aplikacji w konfiguracji tożsamość w globalnie (we wszystkich dzierżawach który ma dostęp), zapewniając szablonu, z której mają odpowiednie obiekty nazwy głównej usługi  *pochodne* do użytku lokalnie w czasie wykonywania (w określonej dzierżawy).

Aby uzyskać więcej informacji, zobacz [aplikacji i obiekty nazwę głównej usługi][AAD-App-SP-Objects].

## <a name="application-registration"></a>Rejestracja aplikacji

Aby umożliwić aplikacji Integracja z usługą i delegować funkcje zarządzania tożsamościami i dostępem do usługi Azure AD, musi być zarejestrowana przy użyciu usługi Azure AD [dzierżawy](#tenant). Po zarejestrowaniu aplikacji z usługą Azure AD, udostępniasz Konfiguracja tożsamości dla aplikacji, co pozwala na integrację z usługą Azure AD i używać funkcji takich jak:

* Niezawodne funkcje zarządzania z logowania jednokrotnego przy użyciu usługi Azure AD Identity Management i [OpenID Connect] [ OpenIDConnect] implementacja protokołu
* Obsługiwane przez brokera dostęp do [zasobów chronionych](#resource-server) przez [aplikacje klienckie](#client-application), za pomocą protokołu OAuth 2.0 [serwera autoryzacji](#authorization-server)
* [Platformy wyrażania zgody](#consent) w celu zarządzania dostępem klientów do chronionych zasobów, w oparciu o autoryzacji właściciela zasobów.

Zobacz [Integrowanie aplikacji z usługą Azure Active Directory] [ AAD-Integrating-Apps] Aby uzyskać więcej informacji.

## <a name="authentication"></a>Uwierzytelnianie

Czynność wyzwaniem strona legalnych poświadczeń, zapewniając podstawą do utworzenia podmiotu zabezpieczeń ma być używany dla tożsamości i kontroli dostępu. Podczas [przydziału autoryzacji OAuth2](#authorization-grant) na przykład strona uwierzytelniania jest wypełnianie roli albo [właściciel zasobu](#resource-owner) lub [aplikacja kliencka](#client-application), w zależności od Przydział używany.

## <a name="authorization"></a>Autoryzacja

Czynność przyznania uprawnienia podmiotu zabezpieczeń uwierzytelnionego zabezpieczeń, na coś zrobić. Istnieją dwa główne przypadki użycia w modelu programowania w usłudze Azure AD:

* Podczas [przydziału autoryzacji OAuth2](#authorization-grant) przepływu: podczas [właściciel zasobu](#resource-owner) przyznaje autoryzacji [aplikacja kliencka](#client-application), co pozwala klientowi uzyskać dostęp do zasobu właściciela zasobów.
* Podczas dostępu do zasobów przez klienta: zaimplementowanego przez [serwer zasobów](#resource-server)przy użyciu [oświadczenia](#claim) wartości są obecne w [token dostępu](#access-token) do podejmowania decyzji dotyczących kontroli dostępu na ich podstawie.

## <a name="authorization-code"></a>Kod autoryzacji

Krótki czas życia 'token' udostępniane [aplikacja kliencka](#client-application) przez [punkt końcowy autoryzacji](#authorization-endpoint), jako część przepływu "Kod autoryzacji", jeden z czterech OAuth2 [autoryzacji przyznaje](#authorization-grant). Kod jest zwracana do aplikacji klienckiej w odpowiedzi na uwierzytelnianie [właściciel zasobu](#resource-owner), wskazując właściciel zasobu ma delegowane autoryzacji dostępu do żądanych zasobów. W ramach przepływu, ten kod jest później wymienić [token dostępu](#access-token).

## <a name="authorization-endpoint"></a>punkt końcowy autoryzacji

Jeden z punktów końcowych implementowany przez [serwera autoryzacji](#authorization-server), który jest używany do interakcji z [właściciel zasobu](#resource-owner) zapewnić [autoryzację](#authorization-grant) podczas OAuth2 Autoryzacja przyznają przepływu. W zależności od przepływu Udziel autoryzacji używany rzeczywistego przyznania podane mogą się różnić, w tym [kod autoryzacji](#authorization-code) lub [token zabezpieczający](#security-token).

Zobacz specyfikację OAuth2 [typy przydziałów autoryzacji] [ OAuth2-AuthZ-Grant-Types] i [punkt końcowy autoryzacji] [ OAuth2-AuthZ-Endpoint] sekcje i [Specyfikacji OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] Aby uzyskać więcej informacji.

## <a name="authorization-grant"></a>autoryzację

Poświadczenie przedstawiający [właściciel zasobu](#resource-owner) [autoryzacji](#authorization) chronionych zasobów, udzielenie dostępu do [aplikacja kliencka](#client-application). Aplikacja kliencka może użyć jednej z [udzielić cztery typy zdefiniowana przez strukturę autoryzacji OAuth2] [ OAuth2-AuthZ-Grant-Types] uzyskać przydział, w zależności od typu/wymagania dotyczące klienta: "przyznawania kodu autoryzacji", "client przyznawanie poświadczeń","przyznawanie niejawne"i"przyznanie poświadczeń hasła właściciela zasobu". Poświadczenie zwracana do klienta jest [token dostępu](#access-token), lub [kod autoryzacji](#authorization-code) (wymiana później, aby uzyskać token dostępu), w zależności od typu autoryzację używane.

## <a name="authorization-server"></a>Serwer autoryzacji

Zgodnie z definicją [ramy autoryzacji OAuth2][OAuth2-Role-Def], odpowiedzialne za wystawianie dostępu do serwera tokenów do [klienta](#client-application) po pomyślnym uwierzytelnieniu [właściciel zasobu](#resource-owner) i uzyskanie autoryzację. A [aplikacja kliencka](#client-application) wchodzi w interakcję z serwera autoryzacji w czasie wykonywania za pomocą jego [autoryzacji](#authorization-endpoint) i [tokenu](#token-endpoint) punktów końcowych, zgodnie z OAuth2 zdefiniowane [przydziałów autoryzacji](#authorization-grant).

W przypadku integracji aplikacji platforma tożsamości firmy Microsoft, platforma tożsamości usługi Microsoft implementuje rolę serwera autoryzacji dla aplikacji usługi Azure AD i usługi firmy Microsoft interfejsów API, na przykład [interfejsów API programu Microsoft Graph] [Microsoft-Graph].

## <a name="claim"></a>Oświadczenia

A [token zabezpieczający](#security-token) zawiera oświadczenia, zapewniający potwierdzenia około jednej jednostki (takie jak [aplikacja kliencka](#client-application) lub [właściciel zasobu](#resource-owner)) do innej jednostki (takie jak [serwer zasobów](#resource-server)). Oświadczenia są pary nazwa/wartość, które przekazywania informacji o tokenu podmiotu (na przykład, podmiot zabezpieczeń został uwierzytelniony przez [serwera autoryzacji](#authorization-server)). Oświadczenia obecne w danym tokenie są zależne od kilku zmiennych, takich jak typ tokenu, typ poświadczenia używane do uwierzytelniania podmiotu, konfigurację aplikacji, itp.

Zobacz [odwołanie tokenu programu Microsoft identity platformy] [ AAD-Tokens-Claims] Aby uzyskać więcej informacji.

## <a name="client-application"></a>Aplikacja kliencka

Zgodnie z definicją [ramy autoryzacji OAuth2][OAuth2-Role-Def], aplikacji, która sprawia, że chroniony zasób żądania w imieniu osób [właściciel zasobu](#resource-owner). Termin "klient" oznacza, wszelkie cechy implementacji konkretnego sprzętu (na przykład tego, czy aplikacja wykonuje na serwer, pulpitu lub innych urządzeniach).

Aplikacja kliencka żąda [autoryzacji](#authorization) od właściciela zasobu, aby uczestniczyć w [OAuth2 autoryzację](#authorization-grant) przepływu i mogą uzyskiwać dostęp do interfejsów API/danych w imieniu właściciela zasobów. Ramy autoryzacji OAuth2 [definiuje dwa typy klientów][OAuth2-Client-Types]"poufne" i "publiczna", zależnie od możliwości klienta do zachowania poufności ich poświadczeń. Aplikacje można wdrażać [klienta sieci web (poufne)](#web-client) na serwerze sieci web, które są uruchamiane [klienta natywnego (publiczny)](#native-client) zainstalowana na urządzeniu, lub [oparte na agentach użytkownika klienta (publiczny)](#user-agent-based-client)który działa w przeglądarce na urządzeniu.

## <a name="consent"></a>Wyrażenie zgody

Proces [właściciel zasobu](#resource-owner) udzielania autoryzacji [aplikacja kliencka](#client-application), aby uzyskiwać dostęp do chronionych zasobów w ramach określonych [uprawnienia](#permissions), w imieniu osób właściciel zasobu. W zależności od uprawnień żądany przez klienta administrator lub użytkownik zostanie poproszony o zgodę na dostęp do swoich danych organizacji/osoba odpowiednio. Uwaga: w [wielodostępnych](#multi-tenant-application) scenariusza, aplikacja [nazwy głównej usługi](#service-principal-object) jest zarejestrowany w dzierżawie consenting użytkownika.

Zobacz [platformy wyrażania zgody](consent-framework.md) Aby uzyskać więcej informacji.

## <a name="id-token"></a>Identyfikator tokenu

[OpenID Connect] [ OpenIDConnect-ID-Token] [token zabezpieczający](#security-token) dostarczone przez [serwera autoryzacji](#authorization-server) [punkt końcowy autoryzacji](#authorization-endpoint), który zawiera [oświadczeń](#claim) odnoszących się do uwierzytelniania użytkownika końcowego [właściciel zasobu](#resource-owner). Takich jak token dostępu, tokeny Identyfikatora również są reprezentowane jako cyfrowo podpisanych [tokenu Web JSON (JWT)][JWT]. W przeciwieństwie do tokenu dostępu, identyfikator tokenu oświadczeń nie są używane do celów związanych z dostępu do zasobów i specjalnie kontroli dostępu.

Zobacz [odwołanie tokenu programu Microsoft identity platformy] [ AAD-Tokens-Claims] Aby uzyskać więcej informacji.

## <a name="microsoft-identity-platform"></a>Platforma tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft to unowocześnienie platformy deweloperów i usługi tożsamości Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania programu Microsoft Graph, innych interfejsów API firmy Microsoft lub interfejsów API opracowanych przez deweloperów. Jest w pełni funkcjonalne platforma, która składa się z usługi uwierzytelniania, bibliotek, rejestrowanie aplikacji i konfiguracji, dokumentacji dla deweloperów pełną, przykłady kodu i innej zawartości dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect. Zobacz [platforma tożsamości firmy Microsoft o](about-microsoft-identity-platform.md) Aby uzyskać więcej informacji.

## <a name="multi-tenant-application"></a>aplikacji wielodostępnych

Klasy aplikacji, która umożliwia logowanie i [zgody](#consent) przez użytkowników aprowizowane w dowolnej usłudze Azure AD [dzierżawy](#tenant), tym dzierżaw niż to, gdzie klient jest zarejestrowany. [Natywny klient](#native-client) aplikacje są wielodostępne domyślnie, natomiast [klienta sieci web](#web-client) i [zasobów/interfejs API sieci web](#resource-server) aplikacje mają możliwość wyboru z jednego lub wielu dzierżawców. Z drugiej strony aplikacji sieci web zarejestrowany jako pojedynczej dzierżawy tylko pozwoliłoby logowania przy użyciu kont użytkowników aprowizowane w tej samej dzierżawie, gdzie aplikacja będzie zarejestrowana.

Zobacz [jak zalogować dowolnego użytkownika usługi Azure AD, za pomocą wzorca aplikacji wielodostępnych] [ AAD-Multi-Tenant-Overview] Aby uzyskać więcej informacji.

## <a name="native-client"></a>Klient natywny

Rodzaj [aplikacja kliencka](#client-application) oryginalnie zainstalowana na urządzeniu. Ponieważ cały kod jest wykonywany na urządzeniu, jest uznawany za "publicznej" klienta z powodu jego brakiem, aby przechowywać poświadczenia prywatnie/jako poufne. Zobacz [OAuth2 klienta typów i profili] [ OAuth2-Client-Types] Aby uzyskać więcej informacji.

## <a name="permissions"></a>uprawnienia

A [aplikacja kliencka](#client-application) uzyskuje dostęp do [serwer zasobów](#resource-server) przez zadeklarowanie żądań dotyczących uprawnień. Dostępne są dwa typy:

* "Delegowane" uprawnienia, które określają [zakresu](#scopes) uzyskiwać dostęp za pomocą delegowanej autoryzacji od zalogowanego [właściciel zasobu](#resource-owner), są prezentowane do zasobu w czasie wykonywania jako ["punkt połączenia usługi" oświadczenia](#claim) w kliencie programu [token dostępu](#access-token).
* Uprawnienia "Aplikacja", które określają [opartej na rolach](#roles) uzyskiwać dostęp za pomocą aplikacji klienckiej poświadczenia/tożsamości, są prezentowane do zasobu w czasie wykonywania jako [oświadczenia "role"](#claim) w obiekcie klienta token dostępu.

Również powierzchni, podczas [zgody](#consent) procesu, dzięki czemu administrator lub właściciel zasobu możliwość grant/odmowa dostępu klienta do zasobów w ramach ich dzierżawy.

Żądań dotyczących uprawnień są skonfigurowane na **uprawnienia do interfejsu API** strony dla aplikacji w [witryny Azure portal][AZURE-portal], wybierając żądane "delegowane uprawnienia" i " Uprawnienia aplikacji"(one wymaga członkostwa w roli administratora globalnego). Ponieważ [publicznych klienta](#client-application) nie może bezpiecznie obsługiwać poświadczeń, może zażądać jedynie uprawnień delegowanych, gdy [poufne klienta](#client-application) ma możliwość żądania aplikacją i delegowanego uprawnienia. Klienta [obiekt aplikacji](#application-object) przechowuje uprawnień zadeklarowanych w jego [właściwość requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>właściciel zasobu

Zgodnie z definicją [ramy autoryzacji OAuth2][OAuth2-Role-Def], zdolne do udzielania dostępu do chronionego zasobu jednostki. Gdy właściciel zasobu jest osoba, jego nazywa się użytkownika końcowego. Na przykład, gdy [aplikacja kliencka](#client-application) chce, aby dostęp do skrzynek pocztowych użytkowników za pośrednictwem [interfejsu API Microsoft Graph][Microsoft-Graph], wymaga uprawnień od właściciela zasobu Skrzynka pocztowa.

## <a name="resource-server"></a>serwer zasobów

Zgodnie z definicją [ramy autoryzacji OAuth2][OAuth2-Role-Def], serwera, że hosty chronione zasoby mogą akceptować i reagowanie na chronione żądań zasobów za pomocą [klienta aplikacje](#client-application) przedstawiające [token dostępu](#access-token). Znana także jako serwera chronionego zasobu, lub zasobów aplikacji.

Serwer zasobów udostępnia interfejsy API i wymusza dostęp do swoich zasobów chronionych za pomocą [zakresy](#scopes) i [role](#roles), przy użyciu platformy autoryzacji OAuth 2.0. Przykłady obejmują interfejs API programu Graph usługi Azure AD, który zapewnia dostęp do danych dzierżawy usługi Azure AD i Office 365 interfejsów API, które zapewniają dostęp do danych, takich jak wiadomości e-mail i kalendarz. Oba te są również dostępne za pośrednictwem [interfejsu API Microsoft Graph][Microsoft-Graph].

Podobnie jak aplikacja kliencka, konfiguracja tożsamości zasobów aplikacji zostanie nawiązane za pośrednictwem [rejestracji](#application-registration) w dzierżawie usługi Azure AD, zapewniając aplikacji i obiektu jednostki usługi. Niektóre firmy Microsoft interfejsy API, np. interfejsu API programu Graph usługi Azure AD, zarejestrowano wstępnie udostępniona w wszystkich dzierżaw podczas inicjowania obsługi nazw głównych usług.

## <a name="roles"></a>role

Podobnie jak [zakresy](#scopes), role umożliwiają [serwer zasobów](#resource-server) dotyczące dostępu do jego chronionych zasobów. Istnieją dwa typy: rola "user" implementuje kontroli dostępu opartej na rolach dla użytkowników i grup, które wymagają dostępu do zasobów, podczas gdy rolę "aplikacji" implementuje takie same dla [aplikacje klienckie](#client-application) wymagające dostępu.

Role są ciągami zdefiniowany zasób (na przykład "osoba zatwierdzająca wydatków", "Tylko do odczytu", "Directory.ReadWrite.All"), zarządzane w [witryny Azure portal] [ AZURE-portal] za pośrednictwem zasobu [aplikacji Manifest](#application-manifest)i przechowywane w zasobie [właściwość appRoles][AAD-Graph-Sp-Entity]. Azure portal umożliwia również przypisać użytkowników do ról "user" i skonfiguruj klienta [uprawnienia aplikacji](#permissions) do uzyskania dostępu do roli "aplikacji".

Aby uzyskać szczegółowe omówienie ról aplikacji udostępnianych przez interfejs API programu Graph usługi Azure AD, zobacz [zakresów uprawnień interfejsu API programu Graph][AAD-Graph-Perm-Scopes]. Na przykład krok po kroku wdrożenia, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal][AAD-RBAC].

## <a name="scopes"></a>scopes

Podobnie jak [role](#roles), zakresy umożliwiają [serwer zasobów](#resource-server) dotyczące dostępu do jego chronionych zasobów. Zakresy są używane do implementowania [zakresu] [ OAuth2-Access-Token-Scopes] kontroli dostępu, aby uzyskać [aplikacja kliencka](#client-application) , nadano delegowanego dostępu do zasobu udzielonej przez właściciela.

Zakresy są zdefiniowane zasobów ciągów (na przykład "Mail.Read", "Directory.ReadWrite.All"), zarządzane w [witryny Azure portal] [ AZURE-portal] za pośrednictwem zasobu [manifest aplikacji](#application-manifest)i przechowywane w zasobie [właściwość oauth2Permissions][AAD-Graph-Sp-Entity]. Witryna Azure portal jest również używany do konfigurowania aplikacji klienckiej [delegowane uprawnienia](#permissions) do zakresu dostępu.

Jest najlepszym rozwiązaniem jest konwencję nazewnictwa, aby użyć formatu "resource.operation.constraint". Aby uzyskać szczegółowe omówienie zakresy udostępnianych przez interfejs API programu Graph usługi Azure AD, zobacz [zakresów uprawnień interfejsu API programu Graph][AAD-Graph-Perm-Scopes]. Dla zakresów udostępnianych przez usługi Office 365, zobacz [odwołanie do uprawnień interfejsu API usługi Office 365][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpieczający

Podpisany dokument zawierający oświadczenia, takie jak tokenu protokołu OAuth2 lub asercji SAML 2.0. Oauth2 [autoryzację](#authorization-grant), [token dostępu](#access-token) (OAuth2) i [tokenu Identyfikacyjnego](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) są typy tokenów zabezpieczających, które są implementowane jako [JSON Token (JWT) w sieci Web][JWT].

## <a name="service-principal-object"></a>obiektu jednostki usługi

Po użytkownik rejestru/Zaktualizuj aplikację w [witryny Azure portal][AZURE-portal], portalu/aktualizacji zarówno [obiekt aplikacji](#application-object) i odpowiedniego obiektu jednostki usługi dla tej dzierżawy. Obiekt aplikacji *definiuje* Konfiguracja tożsamości aplikacji globalnie (we wszystkich dzierżawach gdzie skojarzonej aplikacji udzielono dostępu) i jest szablonem, z którego jego odpowiedniej nazwy głównej usługi obiekty są *pochodne* do użytku lokalnie w czasie wykonywania (w określonej dzierżawy).

Aby uzyskać więcej informacji, zobacz [aplikacji i obiekty nazwę głównej usługi][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Proces [aplikacja kliencka](#client-application) inicjowanie uwierzytelnianie użytkowników końcowych i przechwytywanie dotyczące stanu na potrzeby uzyskiwania [token zabezpieczający](#security-token) i określać ich zakresy sesji aplikacji do tego stanu. Stan może zawierać artefakty, takie jak informacje o profilu użytkownika, a informacje pochodzące z oświadczeń tokenu.

Funkcja logowania aplikacji zwykle jest używana do zaimplementowania logowanie jednokrotne (SSO). Go może również być poprzedzony przez funkcję "Zarejestruj się" jako punkt wejścia dla użytkownika końcowego w celu uzyskania dostępu do aplikacji (podczas pierwszego logowania w). Funkcja rejestracji jest używany do zbierania i utrwalanie stanu dodatkowej specyficzne dla użytkownika i może wymagać [zgody użytkownika](#consent).

## <a name="sign-out"></a>wylogowanie

Proces unauthenticating przez użytkownika końcowego, odłączanie stanu użytkownika skojarzony z [aplikacja kliencka](#client-application) sesji podczas [logowania](#sign-in)

## <a name="tenant"></a>tenant

Wystąpienia katalogu usługi Azure AD, jest nazywana dzierżawę usługi Azure AD. Zapewnia kilka funkcji, takich jak:

* Usługa Rejestr w zintegrowanej aplikacji
* Uwierzytelnianie konta użytkowników i aplikacje zarejestrowane
* Punkty końcowe REST wymagane do obsługi różnych protokołów, w tym OAuth2 i SAML, w tym [punkt końcowy autoryzacji](#authorization-endpoint), [punktu końcowego tokenu](#token-endpoint) i punktu końcowego "typowych" używana przez [ aplikacje wielodostępne](#multi-tenant-application).

Dzierżaw usługi Azure AD są tworzone/skojarzonymi z subskrypcjami platformy Azure i usługi Office 365, podczas tworzenia nowych kont i udostępnienie funkcji tożsamość i zarządzanie dostępem dla subskrypcji. Administratorzy subskrypcji platformy Azure można również utworzyć dodatkowych dzierżaw usługi Azure AD w witrynie Azure portal. Zobacz [jak uzyskać dzierżawę usługi Azure Active Directory] [ AAD-How-To-Tenant] szczegółowe informacje dotyczące różnych sposobów możesz uzyskać dostęp do dzierżawy. Zobacz [jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory] [ AAD-How-Subscriptions-Assoc] szczegółowe informacje na temat relacji między subskrypcjami i dzierżawę usługi Azure AD.

## <a name="token-endpoint"></a>punkt końcowy tokenu

Jeden z punktów końcowych implementowany przez [serwera autoryzacji](#authorization-server) do działu pomocy technicznej OAuth2 [przydziałów autoryzacji](#authorization-grant). W zależności od przydział, może służyć do uzyskania [token dostępu](#access-token) (i pokrewnych tokenu "Odśwież") do [klienta](#client-application), lub [tokenu Identyfikacyjnego](#id-token) gdy jest używane z [OpenID Połącz] [ OpenIDConnect] protokołu.

## <a name="user-agent-based-client"></a>Na podstawie agent użytkownika klienta

Rodzaj [aplikacja kliencka](#client-application) , pobierania kodu z serwera sieci web i wykonywane w ramach agenta użytkownika (na przykład przeglądarka sieci web), takie jak aplikacja jednostronicowa (SPA). Ponieważ cały kod jest wykonywany na urządzeniu, jest uznawany za "publicznej" klienta z powodu jego brakiem, aby przechowywać poświadczenia prywatnie/jako poufne. Aby uzyskać więcej informacji, zobacz [OAuth2 klienta typów i profili][OAuth2-Client-Types].

## <a name="user-principal"></a>Nazwa główna użytkownika

W sposób podobny do obiektu jednostki usługi jest używana do reprezentowania wystąpienie aplikacji, obiekt nazwy głównej użytkownika jest innego typu podmiot zabezpieczeń, który reprezentuje użytkownika. Azure AD Graph [jednostki użytkownika] [ AAD-Graph-User-Entity] definiuje schemat dla obiektu użytkownika, w tym właściwości związane z użytkownikiem, takie jak imię i nazwisko, nazwa główna użytkownika, członkostwo w rolach katalogu itp. Zapewnia to konfiguracja tożsamości użytkownika dla usługi Azure AD można ustanowić podmiot zabezpieczeń użytkownika w czasie wykonywania. Nazwa główna użytkownika jest używana do reprezentowania uwierzytelnionego użytkownika dla logowania jednokrotnego, rejestrowanie [zgody](#consent) delegowania, dzięki czemu decyzji dotyczących kontroli dostępu, itp.

## <a name="web-client"></a>Klient sieci Web

Rodzaj [aplikacja kliencka](#client-application) , który jest wykonywany całego kodu na serwerze sieci web i może działać jako "poufne" klienta przez bezpieczne przechowywanie swoich poświadczeń na serwerze. Aby uzyskać więcej informacji, zobacz [OAuth2 klienta typów i profili][OAuth2-Client-Types].

## <a name="next-steps"></a>Kolejne kroki

[Podręcznik dewelopera Microsoft tożsamości platformy] [ AAD-Dev-Guide] jest stroną docelową dla wszystkich Microsoft tożsamości platformy związanych z opracowywaniem tematy, w tym omówienie [aplikacji Integracja] [ AAD-How-To-Integrate] oraz znasz podstawy [uwierzytelnianie platforma tożsamości firmy Microsoft i scenariuszy uwierzytelniania obsługiwanych][AAD-Auth-Scenarios]. Możesz również znaleźć przykłady kodu i samouczki dotyczące sposobu uzyskania szybko uruchamiać wdrożenia na [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Aby przekazać opinie i pomoc do analizy i połącz kształt tej zawartości, w tym realizowania żądań dostępność nowych definicji lub aktualizowanie istniejących, należy użyć następujących sekcji komentarzy!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
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
