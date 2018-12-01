---
title: Tokenów, sesji i konfiguracji pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Tokenów, sesji i konfiguracji pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 456e32e2f5194417f004f80feef1852dd3d0befd
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723282"
---
# <a name="token-session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Tokenów, sesji i konfiguracji pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C

Ta funkcja umożliwia szczegółową kontrolę na [konkretnych użytkowników, przepływ](active-directory-b2c-reference-policies.md), programu:

- Okresy istnienia tokenów zabezpieczających wyemitowane przez usługi Azure Active Directory (Azure AD) B2C.
- Okres istnienia sesji aplikacji sieci web zarządzanych przez usługę Azure AD B2C.
- Formaty ważne oświadczenia w tokeny zabezpieczające emitowane przez usługę Azure AD B2C.
- Pojedynczy zachowanie logowania jednokrotnego (SSO) między wieloma aplikacje i przepływy użytkownika w dzierżawie usługi Azure AD B2C.

Można użyć tej funkcji, z każdym typem zasad, ale w tym przykładzie pokazano, jak korzystać z funkcji przy użyciu przepływu rejestracji lub logowania użytkownika. Przepływy użytkownika umożliwia tej funkcji w katalogu usługi Azure AD B2C w następujący sposób:

1. Kliknij przycisk **przepływy użytkownika**.
2. Otwórz przepływ użytkownika, klikając go. Na przykład kliknąć **B2C_1_SiUpIn**.
3. Kliknij pozycję **Właściwości**.
4. W obszarze **ustawień zgodności tokenów**, wprowadź żądane zmiany. Więcej informacji na temat dostępnych właściwości w kolejnych sekcjach.
5. Kliknij przycisk **Zapisz** górnej części menu.

## <a name="token-lifetimes-configuration"></a>Konfiguracja okresów istnienia tokenu

Usługa Azure AD B2C obsługuje [Protokół autoryzacji OAuth 2.0](active-directory-b2c-reference-protocols.md) umożliwiające bezpieczny dostęp do chronionych zasobów. Aby zaimplementować tę obsługę, usługi Azure AD B2C emituje różnych [tokenów zabezpieczających](active-directory-b2c-reference-tokens.md). 

Następujące właściwości są używane do zarządzania okresy istnienia tokenów zabezpieczających emitowane przez usługę Azure AD B2C:

- **Dostęp czas życia tokenu Identyfikatora (w minutach)** — okres istnienia tokenu elementu nośnego OAuth 2.0, które są używane do uzyskiwania dostępu do chronionego zasobu.
    - Domyślne = 60 minut.
    - Minimum (włącznie) = 5 minut.
    - Maksymalna (włącznie) = 1440 minut.
- **Czas życia tokenu odświeżania (dni)** — maksymalny okres, przed którym token odświeżania może służyć do uzyskania nowego dostępu lub tokenu Identyfikacyjnego (i opcjonalnie, nowego tokena odświeżania, jeśli aplikacja została udzielona `offline_access` zakresu).
    - Domyślne = 14 dni.
    - Minimum (włącznie) = 1 dzień.
    - Maksymalna (włącznie) = 90 dni.
- **Czas życia okna przewijania tokenu odświeżania (dni)** — po tym czasie upłynięciu tego okresu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od ostatniego okresu ważności tokenu odświeżania uzyskanego przez aplikację. Może być udostępniony tylko, jeśli przełącznik jest równa **powiązana**. Musi być większa lub równa **czas życia tokenu odświeżania (dni)** wartość. Jeśli przełącznik jest równa **niepowiązane**, nie może dostarczyć określonej wartości.
    - Domyślna = 90 dni.
    - Minimum (włącznie) = 1 dzień.
    - Maksymalna (włącznie) = 365 dni.

Następujące przypadki użycia są włączone, przy użyciu tych właściwości:

- Umożliwia użytkownikowi pozostanie w stanie zalogowania do aplikacji mobilnej na czas nieokreślony, tak długo, jak użytkownik jest ciągle aktywne w aplikacji. Możesz ustawić **przesuwanego okna czas życia tokenu odświeżania (dni)** do **niepowiązane** w przepływie logowania użytkownika.
- Spełnia wymagania dotyczące zgodności w branży zabezpieczeń i ustawiając okresów istnienia tokenu odpowiedni dostęp.

Te ustawienia nie są dostępne w przypadku resetowania haseł przepływy użytkownika. 

## <a name="token-compatibility-settings"></a>Ustawienia zgodności tokenu

Następujące właściwości umożliwiają klientom zgodzić się na odpowiednio do potrzeb:

- **Oświadczenie wystawcy (iss)** — ta właściwość identyfikuje dzierżawy usługi Azure AD B2C, który wystawił token.
    - `https://<domain>/{B2C tenant GUID}/v2.0/` — Jest to wartość domyślna.
    - `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` — Ta wartość obejmuje identyfikatory dla dzierżawy usługi B2C i przepływ użytkownika używane w żądania tokenu. Jeśli aplikacji lub biblioteki musi być zgodne z usługi Azure AD B2C [specyfikacji protokołu OpenID Connect 1.0 odnajdywania](http://openid.net/specs/openid-connect-discovery-1_0.html), użyj tej wartości.
- **Oświadczenia podmiotu (pod)** — ta właściwość identyfikuje jednostkę, dla której token określa informacje.
    - **Identyfikator obiektu** — właściwość ta jest wartością domyślną. Wypełnia identyfikator obiektu użytkownika w katalogu do `sub` oświadczenia w tokenie.
    - **Nieobsługiwane** — właściwość ta jest świadczona wyłącznie dla zgodności z poprzednimi wersjami, a firma Microsoft zaleca, aby przełączyć się do **ObjectID** jak tylko można.
- **Oświadczenie reprezentujące identyfikator zasad** — ta właściwość identyfikuje typ oświadczenia, do którego jest wypełniana identyfikator zasad używany w żądania tokenu.
    - **tfp** — właściwość ta jest wartością domyślną.
    - **rejestru Azure container Registry** — właściwość ta jest świadczona wyłącznie dla zgodności z poprzednimi wersjami.

## <a name="session-behavior"></a>Działanie sesji

Usługa Azure AD B2C obsługuje [protokołu uwierzytelniania OpenID Connect](active-directory-b2c-reference-oidc.md) włączania bezpiecznego logowania do aplikacji sieci web. Do zarządzania sesjami aplikacji sieci web, można użyć następujących właściwości:

- **Aplikacja sieci Web okres istnienia sesji (w minutach)** — okres istnienia przechowywanych w przeglądarce użytkownika po pomyślnym uwierzytelnieniu pliku cookie sesji usługi Azure AD B2C.
    - Domyślne = 1440 minut.
    - Minimum (włącznie) = 15 minut.
    - Maksymalna (włącznie) = 1440 minut.
- **Limit czasu sesji aplikacji sieci Web** — w przypadku wybrania tej opcji **bezwzględne**, użytkownik jest zmuszony do uwierzytelniania ponownie po czasie określonym przez **aplikacji sieci Web okres istnienia sesji (w minutach)** upływa. Jeśli ten przełącznik jest równa **stopniowe** (ustawienie domyślne), użytkownik pozostaje zalogowany, tak długo, jak użytkownik jest stale aktywny w aplikacji sieci web.

Następujące przypadki użycia są włączone, przy użyciu tych właściwości:

- Spełnia wymagania dotyczące zabezpieczeń i zgodności w branży, ustawiając sesji aplikacji sieci web odpowiednią okresy istnienia.
- Wymuszanie uwierzytelniania ustalonym okresem podczas interakcji z części o wysokim poziomie zabezpieczeń aplikacji sieci web. 

Te ustawienia nie są dostępne w przypadku resetowania haseł przepływy użytkownika.

## <a name="single-sign-on-sso-configuration"></a>Konfiguracja logowania jednokrotnego (SSO)

Jeśli masz wiele aplikacji i przepływów użytkownika w dzierżawie B2C, możesz zarządzać interakcje użytkownika między nimi przy użyciu **konfiguracji rejestracji jednokrotnej** właściwości. Właściwość można ustawić na jeden z następujących ustawień:

- **Dzierżawy** — to ustawienie jest ustawieniem domyślnym. Przy użyciu tego ustawienia umożliwia wielu aplikacji i użytkownika przepływy w swojej dzierżawy usługi B2C do udostępniania tej samej sesji użytkownika. Na przykład po użytkownik zaloguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego jeden, farmacji firmy Contoso, podczas uzyskiwania dostępu do jej.
- **Aplikacja** — to ustawienie pozwala zachować sesję użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz to użytkownikowi logowanie się farmacji Contoso (przy użyciu tych samych poświadczeń), nawet wtedy, gdy użytkownik jest zalogowany w Contoso zakupów, inną aplikację na tym samym B2C dzierżawy. 
- **Zasady** — to ustawienie pozwala zachować sesję użytkownika wyłącznie na przepływ użytkownika, niezależnie od aplikacji korzystania z niego. Na przykład jeśli użytkownik ma już zalogowany i ukończyć krok multi factor authentication (MFA), użytkownik może mieć dostęp do lepsze zabezpieczenia części wielu aplikacji tak długo, jak sesji powiązane z przepływem użytkownika nie wygasa.
- **Wyłączone** — to ustawienie wymusza na użytkowniku, do uruchamiania za pośrednictwem przepływu całego użytkownika przy każdym wykonaniu zasad. Na przykład ta opcja pozwala wielu użytkowników zarejestrować się do aplikacji (w przypadku klasycznych udostępnionego), nawet podczas jeden użytkownik pozostaje zalogowany przez cały czas.

Te ustawienia nie są dostępne w przypadku resetowania haseł przepływy użytkownika. 

