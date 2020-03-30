---
title: Co to jest usługa Azure Active Directory B2C?
description: Dowiedz się, jak używać usługi Azure Active Directory B2C do obsługi tożsamości zewnętrznych w aplikacjach, w tym logowania społecznościowego za pomocą Facebooka, Google i innych dostawców tożsamości.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 61b6d5ea903d00519c58556bc99da7065741a6e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78184064"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co to jest usługa Azure Active Directory B2C?

Usługa Azure Active Directory B2C zapewnia tożsamość między klientami jako usługą. Klienci korzystają z preferowanych tożsamości kont społecznościowych, korporacyjnych lub lokalnych, aby uzyskać dostęp do logowania jednokrotnego aplikacji i interfejsów API.

![Infografika dostawców tożsamości usługi Azure AD B2C i aplikacji podrzędnych](./media/overview/azureadb2c-overview.png)

Usługa Azure Active Directory B2C (Azure AD B2C) to rozwiązanie do zarządzania dostępem do tożsamości klienta (CIAM), które może obsługiwać miliony użytkowników i miliardy uwierzytelniania dziennie. Zajmuje się skalowanie i bezpieczeństwo platformy uwierzytelniania, monitorowania i automatycznego obsługi zagrożeń, takich jak odmowa usługi, spray hasło, lub ataki siłowe.

## <a name="custom-branded-identity-solution"></a>Niestandardowe rozwiązanie tożsamościowe

Usługa Azure AD B2C to rozwiązanie uwierzytelniania typu white-label. Możesz dostosować środowisko użytkownika z marką tak, aby płynnie łączyła się z aplikacjami internetowymi i mobilnymi.

Dostosuj każdą stronę wyświetlaną przez usługę Azure AD B2C, gdy użytkownicy zarejestrują się, zalogują się i zmodyfikują swoje informacje o profilu. Dostosuj html, CSS i JavaScript w procesach podróży użytkownika, tak aby środowisko usługi Azure AD B2C wyglądało i wydawało się, że jest natywną częścią aplikacji.

![Dostosowane strony rejestracji i logowania oraz obraz tła](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Dostęp do logowania jednokrotnego z tożsamością dostarczoną przez użytkownika

Usługa Azure AD B2C używa opartych na standardach protokołów uwierzytelniania, w tym OpenID Connect, OAuth 2.0 i SAML. Integruje się z większością nowoczesnych aplikacji i komercyjnego gotowego oprogramowania.

![Diagram tożsamości innych firm związanych z usługą Azure AD B2C](./media/overview/scenario-singlesignon.png)

Służąc jako centralny urząd uwierzytelniania dla aplikacji sieci web, aplikacji mobilnych i interfejsów API, usługa Azure AD B2C umożliwia tworzenie rozwiązania logowania jednokrotnego (SSO) dla nich wszystkich. Scentralizuj zbieranie informacji o profilu użytkownika i preferencjach oraz przechwytuj szczegółowe analizy dotyczące zachowania logowania i konwersji rejestracji.

## <a name="integrate-with-external-user-stores"></a>Integracja z zewnętrznymi sklepami użytkowników

Usługa Azure AD B2C udostępnia katalog, który może pomieścić 100 atrybutów niestandardowych na użytkownika. Można jednak również zintegrować z systemami zewnętrznymi. Na przykład użyj usługi Azure AD B2C do uwierzytelniania, ale delegować do zewnętrznego zarządzania relacjami z klientami (CRM) lub bazy danych lojalności klientów jako źródła prawdy dla danych klienta.

Innym scenariuszem magazynu użytkowników zewnętrznych jest usługa Azure AD B2C obsługiwać uwierzytelnianie dla aplikacji, ale zintegrować z systemem zewnętrznym, który przechowuje profil użytkownika lub dane osobowe. Na przykład, aby spełnić wymagania dotyczące rezydencji danych, takie jak regionalne lub lokalne zasady przechowywania danych.

![Logiczny diagram usługi Azure AD B2C komunikujący się z magazynem użytkowników zewnętrznych](./media/overview/scenario-remoteprofile.png)

Usługa Azure AD B2C może ułatwić zbieranie informacji od użytkownika podczas rejestracji lub edycji profilu, a następnie przekazać te dane do systemu zewnętrznego. Następnie podczas przyszłych uwierzytelniania usługi Azure AD B2C można pobrać dane z systemu zewnętrznego i, w razie potrzeby, dołączyć go jako część odpowiedzi tokenu uwierzytelniania wysyła do aplikacji.

## <a name="progressive-profiling"></a>Profilowanie progresywne

Inna opcja pozyskiwania użytkowników obejmuje profilowanie progresywne. Profilowanie progresywne pozwala klientom szybko zakończyć pierwszą transakcję, zbierając minimalną ilość informacji. Następnie stopniowo zbieraj więcej danych profilu od klienta w przyszłych logowaniach.

![Wizualne przedstawienie progresywnego profilowania](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Weryfikacja tożsamości i sprawdzanie tożsamości innych firm

Użyj usługi Azure AD B2C, aby ułatwić weryfikację tożsamości i sprawdzanie przez zbieranie danych użytkownika, a następnie przekazywanie ich do systemu innej firmy w celu wykonania sprawdzania poprawności, oceniania zaufania i zatwierdzania tworzenia konta użytkownika.

![Diagram przedstawiający przepływ użytkownika do sprawdzania tożsamości innych firm](./media/overview/scenario-idproofing.png)

To tylko niektóre z rzeczy, które można wykonać za pomocą usługi Azure AD B2C jako platformy tożsamości między firmami a klientami. W poniższych sekcjach tego przeglądu przedstawiono aplikację demonstracyjną korzystającą z usługi Azure AD B2C. Możesz również przejść bezpośrednio do bardziej szczegółowego [przeglądu technicznego usługi Azure AD B2C.](technical-overview.md)

## <a name="example-woodgrove-groceries"></a>Przykład: Artykuły spożywcze WoodGrove

[WoodGrove Groceries][woodgrove] to aplikacja sieci web na żywo stworzona przez firmę Microsoft w celu zademonstrowania kilku funkcji usługi Azure AD B2C. W następnych kilku sekcjach przejrzeć niektóre opcje uwierzytelniania dostarczone przez usługę Azure AD B2C do witryny woodgrove witryny sieci Web.

### <a name="business-overview"></a>Przegląd działalności

WoodGrove to internetowy sklep spożywczy, który sprzedaje artykuły spożywcze zarówno indywidualnym konsumentom, jak i klientom biznesowym. Ich klienci biznesowi kupują artykuły spożywcze w imieniu swojej firmy lub firm, którymi zarządzają.

### <a name="sign-in-options"></a>Opcje logowania

WoodGrove Artykuły spożywcze oferuje kilka opcji logowania w oparciu o relacje swoich klientów ze sklepem:

* **Klienci indywidualni** mogą zarejestrować się lub zalogować się za pomocą indywidualnych kont, takich jak dostawca tożsamości społecznościowych lub adres e-mail i hasło.
* **Klienci biznesowi** mogą zarejestrować się lub zalogować się przy użyciu poświadczeń przedsiębiorstwa.
* **Partnerzy** i dostawcy to osoby, które dostarczają do sklepu spożywczego produkty do sprzedaży. Tożsamość partnera jest dostarczana przez [usługę Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Strony logowania indywidualnego (B2C), biznesowego (B2C) i partnera (B2B)](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Uwierzytelnij klientów indywidualnych

Gdy klient wybierze **opcję Zaloguj się za pomocą konta osobistego,** zostanie przekierowany do dostosowanej strony logowania hostowanego przez usługę Azure AD B2C. Na poniższej ilustracji widać, że dostosowaliśmy interfejs użytkownika (UI), aby wyglądał i czuł się jak na stronie woodgrove groceries. Klienci firmy WoodGrove powinni nie wiedzieć, że środowisko uwierzytelniania jest hostowane i zabezpieczone przez usługę Azure AD B2C.

![Niestandardowa strona logowania WoodGrove hostowana przez usługę Azure AD B2C](./media/overview/sign-in.png)

Firma WoodGrove umożliwia klientom rejestrację i logowanie się przy użyciu kont Google, Facebook lub Microsoft jako dostawcy tożsamości. Mogą też zarejestrować się, używając swojego adresu e-mail i hasła, aby utworzyć tak zwane *konto lokalne.*

Gdy klient wybierze **opcję Zarejestruj się za pomocą konta osobistego,** a następnie Zarejestruj się **teraz,** zostanie wyświetlona niestandardowa strona rejestracji.

![Niestandardowa strona rejestracji WoodGrove hostowana przez usługę Azure AD B2C](./media/overview/sign-up.png)

Po wprowadzeniu adresu e-mail i wybraniu opcji **Wyślij kod weryfikacyjny**usługa Azure AD B2C wysyła im kod. Po wprowadzeniu kodu wybierz **opcję Weryfikuj kod,** a następnie wprowadź inne informacje w formularzu, muszą również zaakceptować warunki korzystania z usługi.

Kliknięcie przycisku **Utwórz** powoduje, że usługa Azure AD B2C przekierowuje użytkownika z powrotem do witryny woodgrove groceries. Podczas przekierowywania usługi Azure AD B2C przekazuje token uwierzytelniania OpenID Connect do aplikacji sieci web WoodGrove. Użytkownik jest teraz zalogowany i gotowy do pracy, a jego nazwa wyświetlana jest wyświetlana w prawym górnym rogu, aby wskazać, że jest zalogowany.

![Nagłówek witryny WoodGrove Groceries pokazujący, że użytkownik jest zalogowany](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Uwierzytelnij klientów biznesowych

Gdy klient wybierze jedną z opcji w obszarze **Klienci biznesowi,** witryna woodgrove groceries wywołuje inną zasadę usługi Azure AD B2C niż dla klientów indywidualnych.

Ta zasada przedstawia użytkownikowi opcję używania poświadczeń firmowych do rejestracji i logowania. W przykładzie WoodGrove użytkownicy są monitowani o zalogowanie się przy dowolnym koncie usługi Office 365 lub usługi Azure AD. Ta zasada używa [aplikacji azure ad z wieloma dzierżawami](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) i punktu końcowego `/common` usługi Azure AD do skon federacji usługi Azure AD B2C z dowolnym klientem usługi Office 365 na świecie.

### <a name="authenticate-partners"></a>Uwierzytelniaj partnerów

**Łącze Zaloguj się za pomocą konta dostawcy** korzysta z funkcji współpracy usługi Azure Active Directory B2B. Usługa Azure AD B2B to rodzina funkcji usługi Azure Active Directory do zarządzania tożsamościami partnerów. Te tożsamości mogą być sfederowane z usługi Azure Active Directory w celu uzyskania dostępu do aplikacji chronionych usługą Azure AD B2C.

Dowiedz się więcej o usłudze Azure AD B2B w [co to jest dostęp użytkownika gościa w usłudze Azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz pojęcia, co jest usługi Azure AD B2C i niektóre scenariusze, które mogą pomóc, kopać nieco głębiej w jego funkcji i aspektów technicznych.

> [!div class="nextstepaction"]
> [Omówienie techniczne usługi Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
