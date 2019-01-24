---
title: Uwagi dla deweloperów na temat używania zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Uwagi dla deweloperów, od konfigurowania i konserwowania usługi Azure AD B2C za pomocą zasad niestandardowych.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e0a3a295350fc75915cd839d4ac0da8e03866907
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844980"
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Informacje o wersji dla publicznej wersji zapoznawczej usługi Azure Active Directory B2C zasad niestandardowych
Zestaw funkcji niestandardowych zasad jest teraz dostępna w wersji ewaluacyjnej w publicznej wersji zapoznawczej dla wszystkich usługi Azure Active Directory B2C (Azure AD B2C) klientów. Ten zestaw funkcji jest przeznaczona dla deweloperów tożsamości zaawansowanego tworzenia najbardziej złożonych rozwiązań tożsamości.  

Obecnie ten zestaw funkcji wymaga deweloperom konfigurowanie w struktura środowiska tożsamości bezpośrednio przez edytowanie plików XML. Tej metody konfiguracji jest zaawansowanych i złożonych. Zaawansowane tożsamości deweloperzy korzystający z platformy środowiska tożsamości należy zaplanować inwestować trochę czasu na ukończenie przewodników i odczytywanie dokumenty referencyjne. 

## <a name="features-included-in-this-public-preview"></a>Funkcje zawarte w tej publicznej wersji zapoznawczej
Za pomocą nowych funkcji wprowadzonych w publicznej wersji zapoznawczej deweloperzy mogą wykonywać następujące zadania:<br>

*, Autor, a następnie przekaż podróży użytkownika uwierzytelniania niestandardowego za pomocą zasad niestandardowych. 
   * Opisz podróży użytkownika krok po kroku, jak wymiany między dostawców oświadczeń. 
   * Zdefiniuj rozgałęzienia warunkowe w podróży użytkownika. * Integracja usług korzystających z interfejsu API REST w swojej podróży użytkownika uwierzytelniania niestandardowego.  
* Dodaj Federację z dostawcami tożsamości, które są zgodne ze standardowych OpenIDConnect. <br>
* Dodaj Federację z dostawcami tożsamości, zgodne z protokołu SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Warunki publicznej wersji zapoznawczej

* Zachęcamy do korzystania z nowych funkcji wyłącznie do celów oceny.<br>
* Nowe funkcje nie są przeznaczone do użytku w środowisku produkcyjnym.<br>
* Umowy dotyczące poziomu usług (SLA) nie są stosowane do nowych funkcji. <br>
* Żądania pomocy technicznej można zgłosić za pośrednictwem kanałów pomocy technicznej regularne. <br>
* Jest Brak daty uzgodnionego za usługi ogólnie dostępne.<br>
* Uznania i z jakiegokolwiek powodu Microsoft może Flaga i odrzucić lub ograniczyć scenariuszy i podróży użytkownika, które wykraczają poza zakres karty produktu usługi Azure AD B2C ma pełnić rolę klienta tożsamościami i dostępem (CIAM) platforma do zarządzania.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Obowiązki deweloperom zestaw funkcji zasad niestandardowych
Konfiguracja zasad ręczne udziela niższego poziomu dostępu dla podstawowej platformy usługi Azure AD B2C i spowoduje powstanie framework unikatowy zaufania można swobodnie dostosowywać. Możliwych kombinacji niestandardowych dostawców tożsamości, relacje zaufania, integracji z usługami zewnętrznymi i instrukcje krok po kroku przepływami pracy umieścić większe zapotrzebowanie na zaawansowanych deweloperów korzystanie z nich.

Aby w pełni korzystać z publicznej wersji zapoznawczej, zaleca się, czy deweloperom korzystanie z zestawu funkcji niestandardowych zasad przestrzegać następujących wytycznych:
* Zapoznanie się z języka konfiguracji platformy środowiska tożsamości i zarządzania klucza lub kluczy tajnych.
* Przejęcie na własność scenariuszy i niestandardowych integracji.
* Testowanie metodyczny scenariusza.
* Postępuj zgodnie z tworzenia oprogramowania i przemieszczania najlepsze rozwiązania z co najmniej jednego środowiska deweloperskiego i testowego oraz jednego środowiska produkcyjnego.
* Aktualne informacje na temat nowych rozwiązań od dostawcy tożsamości i usług, z których możesz zintegrować z usługą. Na przykład śledzenie pracy w wpisów tajnych i planowanych i nieplanowanych zmian wprowadzonych w usłudze.
* Konfigurowanie aktywnego monitorowania i monitorować czas reakcji w środowisku produkcyjnym.
* Aktualizuj adresy e-mail kontaktu w subskrypcji platformy Azure i przygotuj reagują na wiadomości e-mail z działającej witryny zespołu firmy Microsoft.
* Podejmij działanie aktualnych przypadku zaleca się, aby to zrobić przez zespół aktywnej witryny firmy Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Funkcje według etapów i znane problemy
Niestandardowe funkcje platformy środowiska tożsamości/zasady opracowywane są szybkie i stałych.  Ta tabela to indeks dostępności funkcji/składników.

Publikuj pytania w witrynie Stack Overflow w [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Dostawcy tokenów, protokoły
Interfejsy z zewnętrznych składników i aplikacji

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | na przykład Google + |
| IDP-OAUTH2 |  | x |  | na przykład w serwisie Facebook  |
| IDP-OAUTH1 |  | x |  | na przykład w usłudze Twitter |
| IDP-SAML |  | x |  | na przykład Salesforce, usług AD FS |
| IDP-WSFED | x |  |  |  |
| Jednostki uzależnionej firm OAUTH |  | x |  |  |
| Jednostki uzależnionej firm OIDC |  | x |  |  |
| Jednostki uzależnionej SAML innych firm | x |  |  |  |
| Jednostki uzależnionej firm WSFED | x |  |  |  |
| Interfejs API REST za pomocą uwierzytelniania basic i certyfikat |  | x |  | na przykład usługi Azure Functions |


### <a name="component-support"></a>Obsługa składników


| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------------------------------------------|-------------|---------|----|-------|
| Usługa Azure Multi-Factor Authentication |  | x |  |  |
| Usługa Azure Active Directory jako katalogu lokalnego |  | x |  |  |
| Usługa Azure podsystem poczty E-mail dla funkcji 2FA |  | x |  |  |
| Obsługa wielu języków|  | x |  |  |
| Złożoność hasła | x |  |  |  |


### <a name="content-definition"></a>Definicję zawartości

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Strona błędu, api.error |  | x |  |  |
|   Strona wyboru dostawcy tożsamości, api.idpselections |  | x |  |  |
|   Wybieranie dostawcy tożsamości do rejestracji, api.idpselections.signup |  | x |  |  |
|   Nie pamiętasz hasła, api.localaccountpasswordreset |  | x |  |  |
|   Lokalne konto logowania, api.localaccountsignin |  | x |  |  |
|   Zarejestruj się, api.localaccountsignup konta lokalnego |  | x |  |  |
|   Strona uwierzytelniania Wieloskładnikowego, api.phonefactor |  | x |  |  |
|   Samodzielnie określonych — na przykład api.selfasserted rejestrowania, konta społecznościowego |  | x |  |  |
|   Samodzielnie określonych aktualizacji profilu api.selfasserted.profileupdate |  | x |  |  |
|   Ujednolicone rejestracji lub strony logowania, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integracja aplikacji IEF
| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Id_token_hint parametr ciągu zapytania | x |  |  |  |
| Element domain_hint parametr ciągu zapytania |  | x |  | dostępne jako oświadczenia, mogą być przekazywane do dostawcy tożsamości |
| Login_hint parametr ciągu zapytania |  | x |  | dostępne jako oświadczenia, mogą być przekazywane do dostawcy tożsamości |
| Wstawianie UserJourney JSON za pośrednictwem client_assertion | x |  |  | staną się przestarzałe |
| Wstawianie UserJourney JSON jako id_token_hint | x |  |  | Przejdź na następny podejście do przekazania JSON |


### <a name="session-management"></a>Zarządzanie sesjami

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------|-------------|---------|----|-------|
| Dostawcy sesji logowania jednokrotnego |  | x |  |  |
| Dostawcy sesji logowania zewnętrznego |  | x |  |  |
| Dostawcy sesji logowania jednokrotnego SAML |  | x |  |  |


### <a name="security"></a>Bezpieczeństwo
| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------------------|-------------|---------|----|-------|
| Zasady kluczy — Generowanie, ręczna, przekazywania |  | x |  |  |
| Zasady kluczy - RSA/Cert wpisów tajnych |  | x |  |  |


### <a name="developer-interface"></a>Interfejs dla deweloperów
| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal-IEF UX |  | x |  |  |
| Dzienniki UserJourney Insights aplikacji  |  | x |  |  |
| Dzienniki zdarzeń szczegółowe informacje w aplikacji |x|  |  |  |



## <a name="next-steps"></a>Kolejne kroki
[Wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).
