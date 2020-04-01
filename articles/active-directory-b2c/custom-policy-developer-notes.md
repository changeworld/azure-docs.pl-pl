---
title: Uwagi deweloperów dotyczące zasad niestandardowych
titleSuffix: Azure AD B2C
description: Uwagi dla deweloperów dotyczące konfigurowania i utrzymywania usługi Azure AD B2C przy zachowaniu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408717"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Uwagi dewelopera dotyczące zasad niestandardowych w usłudze Azure Active Directory B2C

Niestandardowa konfiguracja zasad w usłudze Azure Active Directory B2C jest teraz ogólnie dostępna. Ta metoda konfiguracji jest przeznaczona dla zaawansowanych deweloperów tożsamości budujących złożone rozwiązania tożsamości. Zasady niestandardowe udostępniają zasilanie struktury środowiska tożsamości w dzierżawach usługi Azure AD B2C.
Deweloperzy zaawansowanych tożsamości przy użyciu zasad niestandardowych należy zaplanować zainwestować trochę czasu na ukończenie przejść i czytanie dokumentów referencyjnych.

Chociaż większość dostępnych opcji zasad niestandardowych jest teraz ogólnie dostępna, istnieją podstawowe możliwości, takie jak typy profilów technicznych i interfejsy API definicji zawartości, które znajdują się na różnych etapach cyklu życia oprogramowania. Nadchodzi ich o wiele więcej. Poniższa tabela określa poziom dostępności na poziomie bardziej szczegółowym.

## <a name="features-that-are-generally-available"></a>Funkcje, które są ogólnie dostępne

- Tworzenie i przekazywanie podróży użytkowników uwierzytelniania niestandardowego przy użyciu zasad niestandardowych.
    - Opis podróży użytkownika krok po kroku jako wymiany między dostawcami oświadczeń.
    - Zdefiniuj rozgałęzienie warunkowe w podróżach użytkowników.
- Współpraca z usługami obsługującymi interfejs API REST w środowiskach pozyskiwania użytkowników uwierzytelniania niestandardowego.
- Zgodność z dostawcami tożsamości zgodnymi z protokołem OpenIDConnect.
- Skonserwuj się z dostawcami tożsamości, którzy przestrzegają protokołu SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Obowiązki niestandardowych programistów zestawu funkcji zasad

Ręczna konfiguracja zasad zapewnia dostęp niższego poziomu do podstawowej platformy usługi Azure AD B2C i powoduje utworzenie unikatowej struktury zaufania. Wiele możliwych permutacji dostawców tożsamości niestandardowych, relacje zaufania, integracje z usługami zewnętrznymi i przepływy pracy krok po kroku wymagają metodycznego podejścia do projektowania i konfiguracji.

Deweloperzy, którzy stosują niestandardowy zestaw funkcji zasad, powinni przestrzegać następujących wskazówek:

- Zapoznaj się z językiem konfiguracji zasad niestandardowych i zarządzania kluczami/wpisami tajnymi. Aby uzyskać więcej informacji, zobacz [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Przejmij na siebie scenariusze i niestandardowe integracje. Dokumentuj swoją pracę i poinformuj o tym organizację witryny na żywo.
- Wykonaj metodyczne testowanie scenariuszy.
- Postępuj zgodnie z programami i przemieszczania najlepszych praktyk z co najmniej jednego środowiska rozwoju i testowania i jednego środowiska produkcyjnego.
- Bądź na bieżąco z nowymi rozwiązaniami od dostawców tożsamości i usług, z których się integrujesz. Na przykład śledzić zmiany w wpisach tajnych i zaplanowanych i nieplanowanych zmian w usłudze.
- Skonfiguruj aktywne monitorowanie i monitoruj czas reakcji środowisk produkcyjnych. Aby uzyskać więcej informacji na temat integracji z usługą Application Insights, zobacz [Usługa Azure Active Directory B2C: Zbieranie dzienników](analytics-with-application-insights.md).
- Utrzymuj aktualne adresy e-mail kontaktów w ramach subskrypcji platformy Azure i bądź na bieżąco z wiadomościami e-mail zespołu usługi Microsoft.
- Podejmij terminowe działania, gdy zaleci to zespół lokacji na żywo firmy Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Terminy dotyczące funkcji w publicznej wersji zapoznawczej

- Zachęcamy do korzystania z publicznych funkcji podglądu wyłącznie do celów oceny.
- Umowy o gwarantowanym poziomie usług (SLA) nie mają zastosowania do publicznych funkcji w wersji zapoznawczej.
- Żądania pomocy technicznej dotyczące publicznych funkcji w wersji zapoznawczej można składać za pośrednictwem regularnych kanałów pomocy technicznej.

## <a name="features-by-stage-and-known-issues"></a>Funkcje według etapu i znanych problemów

Niestandardowe funkcje frameworka zasad/tożsamości są stale i szybko rozwijane. Poniższa tabela zawiera indeks funkcji i dostępności składników.


### <a name="protocols-and-authorization-flows"></a>Protokoły i przepływy autoryzacji

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Kod autoryzacji OAuth2](authorization-code-flow.md) |  |  | X |  |
| Kod autoryzacji OAuth2 z PKCE |  |  | X | Tylko aplikacje mobilne  |
| [Przepływ niejawny OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Poświadczenia hasła właściciela zasobu OAuth2](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2 (polski)](connect-with-saml-service-providers.md)  |  |X  |  | Powiązania POST i Redirect. |
| OAuth1 |  |  |  | Bez pomocy technicznej. |
| WSFED (WSFED) | X |  |  |  |

### <a name="identify-providers-federation"></a>Identyfikowanie federacji dostawców 

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Na przykład Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Na przykład Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Na przykład Twitter. |
| [SAML2 (polski)](saml-technical-profile.md) |  |   | X | Na przykład Salesforce, ADFS. |
| WSFED (WSFED)| X |  |  |  |


### <a name="rest-api-integration"></a>Integracja interfejsu API REST

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| [INTERFEJS API REST z podstawową erą](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [INTERFEJS API REST z auth certyfikatu klienta](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API z auth na okaziciela OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Obsługa komponentów

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Uwierzytelnianie safruszowe telefonu](phone-factor-technical-profile.md) |  |  | X |  |
| [Uwierzytelnianie usługi Azure MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Hasło jednorazowe](one-time-password-technical-profile.md) |  | X |  |  |
| [Usługa Azure Active Directory](active-directory-technical-profile.md) jako katalog lokalny |  |  | X |  |
| Podsystem poczty e-mail platformy Azure do weryfikacji poczty e-mail |  |  | X |  |
| [Zewnętrzni dostawcy usług poczty e-mail](custom-email.md) |  |X  |  |  |
| [Obsługa wielu języków](localization.md)|  |  | X |  |
| [Sprawdzanie poprawności predykatu](predicates.md) |  |  | X | Na przykład złożoność hasła. |
| [Kontrolki wyświetlania](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Wersje układu strony

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [Obsługa języka JavaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>Integracja z aplikacją IEF

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametr ciągu kwerendy`domain_hint` |  |  | X | Dostępne jako oświadczenie, mogą być przekazywane do IDP. |
| Parametr ciągu kwerendy`login_hint` |  |  | X | Dostępne jako oświadczenie, mogą być przekazywane do IDP. |
| Wstaw JSON do podróży użytkownika za pośrednictwem`client_assertion` | X |  |  | Zostanie przestarzały. |
| Wstaw JSON do podróży użytkownika jako`id_token_hint` |  | X |  | Podejście do przodu, aby przekazać JSON. |
| [Przekazywanie tokenu dostawcy tożsamości do aplikacji](idp-pass-through-custom.md) |  | X |  | Na przykład z Facebooka do aplikacji. |

### <a name="session-management"></a>Zarządzanie sesjami

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Domyślny dostawca sesji przyuszeńcowych](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Zewnętrzny dostawca sesji logowania](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Dostawca sesji SSO SAML](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Zabezpieczenia

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klucze zasad — generowanie, ręczne przesyłanie |  |  | X |  |
| Klucze zasad- RSA/Cert, Tajemnice |  |  | X |  |


### <a name="developer-interface"></a>Interfejs dewelopera

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Środowisko użytkownika Usługi Azure Portal-IEF |  |  | X |  |
| Przekazywanie zasad |  |  | X |  |
| [Dzienniki podróży użytkowników usługi Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Służy do rozwiązywania problemów podczas tworzenia.  |
| [Dzienniki zdarzeń usługi Application Insights](application-insights-technical-profile.md) |  | X |  | Służy do monitorowania przepływów użytkowników w produkcji. |


## <a name="next-steps"></a>Następne kroki

- Sprawdź [operacje programu Microsoft Graph dostępne dla usługi Azure AD B2C](microsoft-graph-operations.md)
- Dowiedz się więcej o [zasadach niestandardowych i różnicach z przepływami użytkowników](custom-policy-overview.md).
