---
title: Uwagi dla deweloperów na temat używania zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Uwagi dla deweloperów na skonfigurowaniu i utrzymywaniu z niestandardowych zasad usługi Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ce323972dcdbf673311b407f427bc452fbe6dc3a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709730"
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Informacje o wersji dla publicznej wersji zapoznawczej usługi Azure Active Directory B2C zasad niestandardowych
Zestaw funkcji niestandardowych zasad jest teraz dostępna w wersji ewaluacyjnej w publicznej wersji zapoznawczej dla wszystkich usługi Azure Active Directory B2C (Azure AD B2C) klientów. Ten zestaw funkcji jest przeznaczona dla deweloperów zaawansowane tożsamości tworzenia najbardziej złożonych rozwiązań tożsamości.  

Obecnie ten zestaw funkcji wymaga deweloperom skonfigurowania struktury obsługi tożsamości bezpośrednio za pomocą edycji pliku XML. Ta metoda konfiguracji jest wydajny i złożone. Zaawansowane tożsamości deweloperzy przy użyciu platformy obsługi tożsamości należy zaplanować inwestycji pewien czas ukończenia przewodników i odczytywanie dokumentów odwołania. 

## <a name="features-included-in-this-public-preview"></a>Funkcje zawarte w tym publicznej wersji zapoznawczej
Z nowych funkcji wprowadzonych w publicznej wersji zapoznawczej deweloperzy mogą wykonywać następujące zadania:<br>

* Tworzenie i przekazywanie niestandardowe uwierzytelnianie użytkownika podróże za pomocą niestandardowych zasad. 
   * Opisano krok po kroku jako wymiany podróże użytkownika od dostawcy oświadczeń. 
   * Zdefiniuj rozgałęzień warunkowych w podróży użytkownika. 
* Integracja usług korzystających z interfejsu API REST w podróży użytkownika z uwierzytelniania niestandardowego.  
* Dodaj federacji z dostawców tożsamości, zgodnych ze standardowych OpenIDConnect. <br>
* Dodaj federacji z dostawców tożsamości, które jest zgodna z protokołu SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Warunki publicznej wersji zapoznawczej

* Firma Microsoft zachęca do korzystania z nowych funkcji wyłącznie do celów ewaluacyjnych.<br>
* Nowe funkcje nie są przeznaczone do użytku w środowisku produkcyjnym.<br>
* Nowe funkcje nie dotyczą umów dotyczących poziomu usług (SLA). <br>
* Żądania pomocy technicznej można złożyć za pośrednictwem kanałów regularne pomocy technicznej. <br>
* Brak bez uzgodnionej daty dla ogólnej dostępności.<br>
* Nasze uznania z dowolnej przyczyny Microsoft może Flaga i odrzucić lub ograniczyć scenariuszy i podróże użytkownika, które wykraczają poza karty produktu usługi Azure AD B2C do służy jako platforma zarządzania (CIAM) tożsamości i dostępu klienta.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Obowiązki deweloperów zestaw funkcji zasad niestandardowych
Konfiguracja zasad ręczne udziela dostępu niższego poziomu podstawowej platformy Azure AD B2C i spowoduje powstanie framework unikatowy zaufania można swobodnie dostosowywać. Możliwych kombinacji dostawców tożsamości niestandardowej, relacje zaufania, integracji z usługami zewnętrznych i przepływy pracy krok po kroku umieścić większa zapotrzebowanie na zaawansowanych deweloperów korzystających z nich.

Aby w pełni skorzystać z publicznej wersji zapoznawczej, zalecamy deweloperom korzystanie z niestandardowych zasad zestaw funkcji jest zgodna z następującymi zasadami:
* Zapoznanie się z języka konfiguracji Framework obsługi tożsamości i klucz/klucze tajne zarządzania.
* Przejmowanie na własność scenariuszy i niestandardowej integracji.
* Testowanie metodyczny scenariusza.
* Wykonaj rozwoju oprogramowania i przejściowych najlepszych rozwiązań z co najmniej jeden programowanie i środowiska testowego i jednego środowiska produkcyjnego.
* Aktualne informacje o nowych rozwiązań z dostawców tożsamości i usługi, którą można zintegrować z. Na przykład śledzenie pracy w kluczy tajnych i planowanych i nieplanowanych zmiany w usłudze.
* Konfigurowanie aktywnego monitorowania i monitorowanie czasu reakcji środowisk produkcyjnych.
* Aktualizuj adresy e-mail kontaktów w subskrypcji platformy Azure i pozostać odbierać wiadomości e-mail na żywo witryny zespołu firmy Microsoft.
* Podjąć działania odpowiednim przypadku zaleca się, aby to zrobić przez zespół live witryny firmy Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Funkcje w etapie i znane problemy
Niestandardowe zasady/Identity Framework środowisko możliwości opracowywane są stałe i szybkie.  Ta tabela jest indeks dostępność funkcji/składników.

Opublikuj pytania na przepełnienie stosu w [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Dostawcy tokenów, protokoły
Interfejsy o składnikach zewnętrznych i aplikacji

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | na przykład Google + |
| IDP OAUTH2 |  | x |  | na przykład usługi Facebook  |
| IDP OAUTH1 |  | x |  | na przykład w usłudze Twitter |
| IDP-SAML |  | x |  | na przykład Salesforce, usług AD FS |
| IDP-WSFED | x |  |  |  |
| Jednostki uzależnionej firm OAUTH |  | x |  |  |
| Jednostki uzależnionej OIDC firm |  | x |  |  |
| Jednostki uzależnionej firm SAML | x |  |  |  |
| Jednostki uzależnionej WSFED firm | x |  |  |  |
| Interfejs API REST z uwierzytelniania basic i certyfikatu. należy |  | x |  | na przykład usługi Azure Functions |


### <a name="component-support"></a>Obsługa składników


| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------------------------------------------|-------------|---------|----|-------|
| Usługa Azure Multi-Factor Authentication |  | x |  |  |
| Usługa Azure Active Directory jako katalogu lokalnego |  | x |  |  |
| Azure podsystemu 2FA poczty E-mail |  | x |  |  |
| Obsługa wielu języków|  | x |  |  |
| Złożoność hasła | x |  |  |  |


### <a name="content-definition"></a>Definicja zawartości

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Strona błędu, api.error |  | x |  |  |
|   Strona wybierania IDP, api.idpselections |  | x |  |  |
|   Wybór dostawców tożsamości dla rejestracji, api.idpselections.signup |  | x |  |  |
|   Nie pamiętasz hasła, api.localaccountpasswordreset |  | x |  |  |
|   Lokalne konto logowania, api.localaccountsignin |  | x |  |  |
|   Api.localaccountsignup rejestrację konta lokalnego |  | x |  |  |
|   Strona usługi MFA, api.phonefactor |  | x |  |  |
|   Własnym potwierdzone — na przykład api.selfasserted sig wzbudzania społecznościowych konta |  | x |  |  |
|   Własnym potwierdzone aktualizacji profilu api.selfasserted.profileupdate |  | x |  |  |
|   Ujednolicone signup lub strony logowania, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integracja aplikacji IEF
| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Id_token_hint parametr ciągu zapytania | x |  |  |  |
| Domain_hint parametr ciągu zapytania |  | x |  | dostępne, ponieważ oświadczenia, mogą zostać przekazane do IDP |
| Login_hint parametr ciągu zapytania |  | x |  | dostępne, ponieważ oświadczenia, mogą zostać przekazane do IDP |
| Wstaw JSON do UserJourney za pośrednictwem client_assertion | x |  |  | zostaną wycofane |
| Wstawianie JSON do UserJourney jako id_token_hint | x |  |  | podejście do przodu przejdź do przekazania JSON |


### <a name="session-management"></a>Zarządzanie sesjami

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------|-------------|---------|----|-------|
| Dostawca sesji rejestracji Jednokrotnej |  | x |  |  |
| Dostawcy sesji logowania zewnętrznego |  | x |  |  |
| Dostawca sesji logowania jednokrotnego SAML |  | x |  |  |


### <a name="security"></a>Bezpieczeństwo
| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------------------|-------------|---------|----|-------|
| Zasady kluczy generowania, ręczne przekazywanie |  | x |  |  |
| Zasady kluczy - RSA/certyfikatów, kluczy tajnych |  | x |  |  |


### <a name="developer-interface"></a>Interfejs Developer
| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|---------------------------------------------|-------------|---------|----|-------|
| Portal Azure — IEF UX |  | x |  |  |
| Application Insights UserJourney dzienniki  |  | x |  |  |
| Dzienniki zdarzeń aplikacji Insights |x|  |  |  |



## <a name="next-steps"></a>Kolejne kroki
[Wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).
