---
title: Uwagi dla deweloperów dotyczące zasad niestandardowych
titleSuffix: Azure AD B2C
description: Uwagi dla deweloperów dotyczące konfigurowania i konserwowania Azure AD B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edbd31434715c380badf15118b0779885aed700f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949759"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Uwagi dla deweloperów dotyczące zasad niestandardowych w Azure Active Directory B2C

Konfiguracja zasad niestandardowych w Azure Active Directory B2C jest teraz ogólnie dostępna. Ta metoda konfiguracji jest przeznaczona dla deweloperów zaawansowanej tożsamości, tworząc złożone rozwiązania do tworzenia tożsamości. Zasady niestandardowe sprawiają, że możliwości programu Identity platform są dostępne w dzierżawach Azure AD B2C.
Zaawansowani deweloperzy tożsamości korzystający z zasad niestandardowych powinny planować inwestowanie w czasie wykonywania instruktażu i odczytywania dokumentów referencyjnych.

Chociaż większość dostępnych opcji zasad niestandardowych jest teraz ogólnie dostępna, istnieją podstawowe możliwości, takie jak typy profilów technicznych i interfejsy API definicji zawartości, które znajdują się na różnych etapach cyklu życia oprogramowania. Trwa wiele innych. W poniższej tabeli określono poziom dostępności na bardziej szczegółowym poziomie.

## <a name="features-that-are-generally-available"></a>Funkcje, które są ogólnie dostępne

- Tworzenie i przekazywanie niestandardowych podróży użytkowników uwierzytelniania przy użyciu zasad niestandardowych.
    - Opisz przedziały użytkownika krok po kroku jako wymianę między dostawcami oświadczeń.
    - Definiowanie rozgałęziania warunkowego w podróży użytkownika.
- Współdziałanie z usługami z obsługą interfejsu API REST w przypadku niestandardowych podróży użytkowników uwierzytelniania.
- Sfederować z dostawcami tożsamości, które są zgodne z protokołem OpenIDConnect.
- Sfederować z dostawcami tożsamości, które są zgodne z protokołem SAML 2,0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Obowiązki zasad niestandardowych — Ustawianie deweloperów

Ręczna konfiguracja zasad zapewnia dostęp na niższym poziomie do podstawowej platformy Azure AD B2C i skutkuje tworzeniem unikatowej struktury zaufania. Wiele możliwych permutacji niestandardowych dostawców tożsamości, relacji zaufania, integracji z usługami zewnętrznymi i przepływów pracy krok po kroku wymagają założenia strategii do projektowania i konfigurowania.

Deweloperzy korzystający z zestawu funkcji zasad niestandardowych powinni przestrzegać następujących wytycznych:

- Zapoznaj się z językiem konfiguracji zasad niestandardowych oraz zarządzania kluczami i wpisami tajnymi. Aby uzyskać więcej informacji, zobacz [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Przejmij na własność scenariusze i integracje niestandardowe. Zadokumentuj swoją służbową i poinformuj swoją organizację o działającej witrynie.
- Wykonaj testy scenariusza metodyczne.
- Postępuj zgodnie z najważniejszymi rozwiązaniami dotyczącymi tworzenia oprogramowania i przemieszczania, które są co najmniej jedno środowisko deweloperskie i testowe oraz jedno środowisko produkcyjne.
- Bądź na bieżąco z informacjami dotyczącymi nowych osiągnięć od dostawców tożsamości i usług, z którymi integrujesz się. Na przykład Śledź zmiany w kluczach tajnych i zaplanowanych i nieplanowanych zmianach w usłudze.
- Skonfiguruj aktywne monitorowanie i monitoruj czas odpowiedzi w środowiskach produkcyjnych. Aby uzyskać więcej informacji na temat integrowania z Application Insights, zobacz [Azure Active Directory B2C: zbieranie dzienników](active-directory-b2c-custom-guide-eventlogger-appins.md).
- Kontynuuj kontaktowe adresy e-mail z bieżącą subskrypcją platformy Azure i Kontynuuj pracę z wiadomościami e-mail zespołu w witrynie Microsoft Live.
- Wykonaj działania w odpowiednim czasie, gdy jest to zalecane przez zespół Microsoft Live site.

## <a name="terms-for-features-in-public-preview"></a>Warunki dotyczące funkcji w publicznej wersji zapoznawczej

- Zachęcamy do korzystania z funkcji publicznej wersji zapoznawczej tylko do celów ewaluacyjnych.
- Umowy dotyczące poziomu usług (umowy SLA) nie mają zastosowania do funkcji publicznej wersji zapoznawczej.
- Żądania pomocy technicznej dotyczące funkcji publicznej wersji zapoznawczej można zgłosić za pomocą zwykłych kanałów pomocy technicznej.

## <a name="features-by-stage-and-known-issues"></a>Funkcje według etapów i znanych problemów

Niestandardowe i szybkie opracowywanie możliwości struktury środowiska obsługi tożsamości są stałe. Poniższa tabela zawiera indeks funkcji i dostępności składników.

### <a name="identity-providers-tokens-protocols"></a>Dostawcy tożsamości, tokeny, protokoły

| Funkcja | Tworzenie oprogramowania | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Na przykład Google +.  |
| IDP-OAUTH2 |  |  | X | Na przykład w serwisie Facebook.  |
| DOSTAWCY tożsamości — OAUTH1 (Twitter) |  | X |  | Na przykład serwis Twitter. |
| DOSTAWCY tożsamości-OAUTH1 (np. do usługi Twitter) |  |  |  | Brak obsługi |
| IDP-SAML |  |   | X | Na przykład usługi Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| OAUTH1 jednostki uzależnionej |  |  |  | Nieobsługiwane. |
| OAUTH2 jednostki uzależnionej |  |  | X |  |
| OIDC jednostki uzależnionej |  |  | X |  |
| Element SAML jednostki uzależnionej | X |  |  |  |
| WSFED jednostki uzależnionej | X |  |  |  |
| Interfejs API REST z uwierzytelnianiem Basic i certyfikatem |  |  | X | Na przykład Azure Logic Apps. |

### <a name="component-support"></a>Obsługa składników

| Funkcja | Tworzenie oprogramowania | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Usługa Azure Multi-Factor Authentication |  |  | X |  |
| Azure Active Directory jako katalog lokalny |  |  | X |  |
| Weryfikacja podsystemu wiadomości E-mail platformy Azure do wiadomości e-mail |  |  | X |  |
| Obsługa wielu języków|  |  | X |  |
| Walidacje predykatu |  |  | X | Na przykład złożoność hasła. |
| Korzystanie z dostawców usług poczty e-mail innych firm | X |  |  |  |

### <a name="content-definition"></a>Definicja zawartości

| Funkcja | Tworzenie oprogramowania | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Strona błędu, interfejs API. błąd |  |  | X |  |
| Strona wyboru dostawcy tożsamości, interfejs API. idpselections |  |  | X |  |
| DOSTAWCY tożsamości wybór do rejestracji, API. idpselections. signup |  |  | X |  |
| Zapomniane hasło, API. localaccountpasswordreset |  |  | X |  |
| Logowanie do konta lokalnego, interfejs API. localaccountsignin |  |  | X |  |
| Rejestracja konta lokalnego, interfejs API. localaccountsignup |  |  | X |  |
| Strona usługi MFA, API. PhoneFactor |  |  | X |  |
| Konto społecznościowe z własnym potwierdzeń, interfejs API. selfasserted |  |  | X |  |
| Aktualizacja profilu z własnym potwierdzeń, API. selfasserted. profileupdate |  |  | X |  |
| Ujednolicona Strona rejestracji lub logowania, API. signuporsignin, z parametrem "disableSignup" |  |  | X |  |
| Układ JavaScript/Page |  | X |  |  |

### <a name="app-ief-integration"></a>Integracja App-IEF

| Funkcja | Tworzenie oprogramowania | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Domain_hint parametru ciągu zapytania |  |  | X | Dostępne jako roszczeń mogą być przesyłane do dostawcy tożsamości. |
| Login_hint parametru ciągu zapytania |  |  | X | Dostępne jako roszczeń mogą być przesyłane do dostawcy tożsamości. |
| Wstaw kod JSON do UserJourney przez client_assertion | X |  |  | Będzie przestarzałe. |
| Wstaw kod JSON do UserJourney jako id_token_hint |  | X |  | Podejście do przodu do przekazywania kodu JSON. |
| Przekaż TOKEN dostawcy tożsamości do aplikacji |  | X |  | Na przykład z usługi Facebook do aplikacji. |

### <a name="session-management"></a>Zarządzanie sesjami

| Funkcja | Tworzenie oprogramowania | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Dostawca sesji logowania jednokrotnego |  |  | X |  |
| Dostawca sesji logowania zewnętrznego |  |  | X |  |
| Dostawca sesji SSO protokołu SAML |  |  | X |  |
| Domyślny dostawca sesji SSO |  |  | X |  |

### <a name="security"></a>Zabezpieczenia

| Funkcja | Tworzenie oprogramowania | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klucze zasad — generowanie, ręczne, przekazywanie |  |  | X |  |
| Klucze zasad — RSA/certyfikat, wpisy tajne |  |  | X |  |
| Przekazywanie zasad |  |  | X |  |

### <a name="developer-interface"></a>Interfejs dewelopera

| Funkcja | Tworzenie oprogramowania | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Witryna Azure Portal — środowisko IEF |  |  | X |  |
| Application Insights dzienników UserJourney |  | X |  | Używane do rozwiązywania problemów podczas opracowywania.  |
| Application Insights dzienników zdarzeń (za pośrednictwem kroków aranżacji) |  | X |  | Służy do monitorowania przepływów użytkowników w środowisku produkcyjnym. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat zasad niestandardowych i różnic dotyczących przepływów użytkowników](active-directory-b2c-overview-custom.md).
