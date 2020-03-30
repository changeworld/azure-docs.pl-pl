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
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189399"
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

### <a name="identity-providers-tokens-protocols"></a>Dostawcy tożsamości, tokeny, protokoły

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Na przykład Google+.  |
| IDP-OAUTH2 |  |  | X | Na przykład Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Na przykład Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Nieobsługiwane |
| IDP-SAML |  |   | X | Na przykład Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Strona uzależniająca OAUTH1 |  |  |  | Bez pomocy technicznej. |
| Strona uzależniająca OAUTH2 |  |  | X |  |
| Jednostka uzależniona OIDC |  |  | X |  |
| Saml jednostki uzależniającej |  |X  |  |  |
| Strona uzależniona WSFED | X |  |  |  |
| INTERFEJS API REST z auth podstawowy i certyfikat |  |  | X | Na przykład usługi Azure Logic Apps. |

### <a name="component-support"></a>Obsługa składników

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Uwierzytelnianie wieloskładnikowe platformy Azure |  |  | X |  |
| Usługa Azure Active Directory jako katalog lokalny |  |  | X |  |
| Podsystem poczty e-mail platformy Azure do weryfikacji poczty e-mail |  |  | X |  |
| Obsługa wielu języków|  |  | X |  |
| Sprawdzanie poprawności predykatu |  |  | X | Na przykład złożoność hasła. |
| Korzystanie z usług zewnętrznych dostawców usług poczty e-mail |  |X  |  |  |

### <a name="content-definition"></a>Definicja zawartości

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Strona błędu, api.error |  |  | X |  |
| Strona wyboru dostawcy usług IDP, api.idpselections |  |  | X |  |
| Wybór dostawcy tożsamości do rejestracji, api.idpselections.signup |  |  | X |  |
| Zapomniałeś hasła, api.localaccountpasswordreset |  |  | X |  |
| Logowanie do konta lokalnego, api.localaccountsignin |  |  | X |  |
| Rejestracja konta lokalnego, api.localaccountsignup |  |  | X |  |
| Strona usługi MFA, api.phonefactor |  |  | X |  |
| Samodzielnie dochodzi do rejestracji konta społecznościowego, api.selfasserted |  |  | X |  |
| Samodzielnie potwierdzona aktualizacja profilu, api.selfasserted.profileupdate |  |  | X |  |
| Ujednolicona strona rejestracji lub logowania api.signuporsignin z parametrem "disableSignup" |  |  | X |  |
| JavaScript / Układ strony |  | X |  |  |

### <a name="app-ief-integration"></a>Integracja z aplikacją IEF

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametr ciągu kwerendy domain_hint |  |  | X | Dostępne jako oświadczenie, mogą być przekazywane do IDP. |
| Parametr ciągu kwerendy login_hint |  |  | X | Dostępne jako oświadczenie, mogą być przekazywane do IDP. |
| Włóż JSON do UserJourney za pośrednictwem client_assertion | X |  |  | Zostanie przestarzały. |
| Wstaw JSON do userjourney jako id_token_hint |  | X |  | Podejście do przodu, aby przekazać JSON. |
| Przekazywanie tokenu IDP do aplikacji |  | X |  | Na przykład z Facebooka do aplikacji. |

### <a name="session-management"></a>Zarządzanie sesjami

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Dostawca sesji SSO |  |  | X |  |
| Dostawca zewnętrznej sesji logowania |  |  | X |  |
| Dostawca sesji SSO SAML |  |  | X |  |
| Domyślny dostawca sesji sytłewu |  |  | X |  |

### <a name="security"></a>Zabezpieczenia

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klucze zasad — generowanie, ręczne przesyłanie |  |  | X |  |
| Klucze zasad- RSA/Cert, Tajemnice |  |  | X |  |
| Przekazywanie zasad |  |  | X |  |

### <a name="developer-interface"></a>Interfejs dewelopera

| Funkcja | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Środowisko użytkownika Usługi Azure Portal-IEF |  |  | X |  |
| Application Insights UserJourney Logs |  | X |  | Służy do rozwiązywania problemów podczas tworzenia.  |
| Dzienniki zdarzeń usługi Application Insights (za pomocą kroków aranżacji) |  | X |  | Służy do monitorowania przepływów użytkowników w produkcji. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zasadach niestandardowych i różnicach z przepływami użytkowników](custom-policy-overview.md).
