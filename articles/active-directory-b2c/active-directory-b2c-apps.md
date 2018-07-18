---
title: Typy aplikacji, które mogą być używane w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat typów aplikacji, których można używać w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: davidmu1
ms.component: B2C
ms.openlocfilehash: 0055da12689fcbe14de9dd537db2b20358a7273b
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113457"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikacji, które mogą być używane w Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C obsługuje uwierzytelnianie w różnych architektur nowoczesnych aplikacji. Wszystkie one są oparte na standardowych protokołach branżowych [OAuth 2.0](active-directory-b2c-reference-protocols.md) lub [OpenID Connect](active-directory-b2c-reference-protocols.md). W tym dokumencie opisano typy aplikacji, które można tworzyć, niezależnie od języka lub platformy wolisz. Ułatwia on także zrozumienie ogólnych scenariuszy przed rozpoczęciem tworzenia aplikacji.

Każda aplikacja, która używa usługi Azure AD B2C musi być zarejestrowana w Twojej [dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md) przy użyciu [witryny Azure Portal](https://portal.azure.com/). Proces rejestracji aplikacji służy do zbierania i przypisuje wartości, takich jak:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację.
* A **identyfikator URI przekierowania** który może służyć do kierowania odpowiedzi z powrotem do aplikacji.

Każde żądanie wysłane do usługi Azure AD B2C określa **zasady**. Zasady regulują sposób działania usługi Azure AD. Można również użyć tych punktów końcowych do utworzenia zestawu funkcji środowiska użytkownika, które można w znacznym stopniu dostosowywać. Wspólne zasady obejmują rejestrację, logowanie i edycję profilu. Jeśli nie znasz tych zasad, przed wykonaniem dalszych czynności zapoznaj się z informacjami na temat [rozszerzonej platformy zasad](active-directory-b2c-reference-policies.md) usługi Azure AD B2C.

Interakcja każdej aplikacji jest realizowana według następującego ogólnego schematu:

1. Aplikacja przekierowuje użytkownika do punktu końcowego v2.0 do wykonania [zasad](active-directory-b2c-reference-policies.md).
2. Użytkownik wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja odbiera token zabezpieczający z punktu końcowego v2.0.
4. Aplikacja używa tokenu zabezpieczającego uzyskać dostęp do chronionych informacji lub chronionego zasobu.
5. Serwer zasobów weryfikuje token zabezpieczający, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token zabezpieczający.

Te kroki mogą różnić się nieco w zależności od typu aplikacji, które tworzysz.

## <a name="web-applications"></a>Aplikacje internetowe

Dla aplikacji sieci web (w tym .NET, PHP, Java, Ruby, Python i Node.js), które są hostowane na serwerze i dostępne za pośrednictwem przeglądarki, usługa Azure AD B2C obsługuje [OpenID Connect](active-directory-b2c-reference-protocols.md) dla użytkownika wszystkich środowisk. Obejmuje to logowanie, rejestrację i zarządzanie profilami. W implementacji usługi Azure AD B2C protokołu OpenID Connect aplikacji sieci web inicjuje te funkcje środowiska użytkownika, wysyłając żądania uwierzytelniania do usługi Azure AD. Wynikiem żądania jest token `id_token`. Ten token zabezpieczający reprezentuje tożsamość użytkownika. Zawiera także informacje o użytkowniku w formie oświadczeń:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Dowiedz się więcej o typach tokenów i oświadczeń dostępnych dla aplikacji w [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

W aplikacji sieci web Każde wykonanie [zasad](active-directory-b2c-reference-policies.md) obejmuje następujące ogólne kroki:

![Obraz ścieżek aplikacji internetowej](./media/active-directory-b2c-apps/webapp.png)

Sprawdzenie tokenu `id_token` przy użyciu publicznego klucza podpisywania otrzymanego z usługi Azure AD jest wystarczające do zweryfikowania tożsamości użytkownika. Powoduje ono również ustawienie pliku cookie sesji, którego można użyć do identyfikacji użytkownika w żądaniach na kolejnych stronach.

Aby wyświetlić tym scenariuszem w praktyce, wypróbuj jedną z próbek kodu logowania aplikacji sieci web w naszym [sekcji wprowadzenie](active-directory-b2c-overview.md).

Oprócz ułatwienia prostego logowania, aplikacja serwera sieci web może być konieczne również dostępu do usługi sieci web zaplecza. W takim przypadku aplikacja sieci web może wykonać nieco inny [przepływ protokołu OpenID Connect](active-directory-b2c-reference-oidc.md) i uzyskać tokeny przy użyciu kodów autoryzacji i tokenów odświeżania. Ten scenariusz jest opisany w [sekcji dotyczącej interfejsów API sieci Web](#web-apis) poniżej.

## <a name="web-apis"></a>Interfejsy API sieci Web

Za pomocą usługi Azure AD B2C do zabezpieczania usług sieci web, takie jak internetowy interfejs API RESTful aplikacji. Interfejsy API sieci Web mogą zabezpieczać swoje dane za pomocą protokołu OAuth 2.0, uwierzytelniając żądania przychodzące HTTP przy użyciu tokenów. Element wywołujący interfejs API sieci Web dołącza token w nagłówku autoryzacji żądania HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Interfejs API sieci Web może następnie użyć tego tokenu do zweryfikowania tożsamości elementu wywołującego interfejs API oraz do wyodrębnienia informacji o elemencie wywołującym z oświadczeń zakodowanych w tokenie. Aby uzyskać więcej informacji o typach tokenów i oświadczeń dostępnych dla aplikacji, zobacz [informacje o tokenach usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> Usługa Azure AD B2C obsługuje obecnie tylko interfejsy API sieci Web, do których dostęp uzyskują ich własne dobrze znane aplikacje klienckie. Na przykład Pełna aplikacja może obejmować aplikację systemu iOS, aplikacji systemu Android i zaplecza internetowego interfejsu API. Taka architektura jest w pełni obsługiwana. Zezwalanie klientowi partnera, np. innej aplikacji dla systemu iOS na dostęp do tego samego internetowego interfejsu API nie jest obecnie obsługiwane. Wszystkie składniki usługi kompletnej aplikacji muszą współdzielić jeden identyfikator aplikacji.
>
>

Interfejs API sieci web może odbierać tokeny od wielu typów klientów, w tym aplikacji sieci web, pulpitu i aplikacje mobilne, aplikacje jednej strony, demonów po stronie serwera i innych interfejsów API sieci web. Oto przykład pełnego przepływu aplikacji sieci web, która wywołuje interfejs API sieci web:

![Obraz ścieżek internetowego interfejsu API aplikacji internetowej](./media/active-directory-b2c-apps/webapi.png)

Aby dowiedzieć się więcej o kodach autoryzacji, tokenach odświeżania i krokach uzyskiwania tokenów, zapoznaj się z informacjami na temat [protokołu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu usługi Azure AD B2C, zapoznaj się z samouczkami dotyczącymi interfejsu API sieci Web w [sekcji Wprowadzenie](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Mobilnych i aplikacji natywnych

Aplikacje, które są zainstalowane na urządzeniach, takich jak w przypadku aplikacji mobilnych i komputerowych często muszą uzyskać dostęp do usług zaplecza lub interfejsów API sieci web w imieniu użytkowników. Możesz dodać niestandardowe środowiska zarządzania tożsamością do aplikacji natywnych i bezpiecznie wywoływać usługi zaplecza przy użyciu usługi Azure AD B2C i [przepływ kodu autoryzacji OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

W tym przepływie aplikacja wykonuje [zasady](active-directory-b2c-reference-policies.md) i odbiera `authorization_code` z usługi Azure AD po wypełnieniu zasad przez użytkownika. `authorization_code` Reprezentuje aplikacji uprawnień do wywoływania usług zaplecza w imieniu użytkownika, który jest aktualnie zalogowany. Aplikacja może następnie wymienić `authorization_code` w tle dla `id_token` i `refresh_token`.  Aplikacja może używać `id_token` do uwierzytelniania internetowego interfejsu API zaplecza w żądaniach HTTP. Może również użyć tokenu `refresh_token` do pobrania nowego tokenu `id_token`, gdy wygaśnie stary.

> [!NOTE]
> Usługa Azure AD B2C obsługuje obecnie tylko tokeny, które umożliwiają dostęp do usługi sieci web zaplecza w aplikacji. Na przykład Pełna aplikacja może obejmować aplikacji systemu iOS, aplikacji systemu Android i zaplecza internetowego interfejsu API. Taka architektura jest w pełni obsługiwana. Zezwalanie aplikacji systemu iOS na dostęp do interfejsu API sieci web partnera przy użyciu tokenów dostępu protokołu OAuth 2.0 nie jest obecnie obsługiwane. Wszystkie składniki usługi kompletnej aplikacji muszą współdzielić jeden identyfikator aplikacji.
>
>

![Obraz ścieżek aplikacji natywnej](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Bieżące ograniczenia

Usługa Azure AD B2C nie obsługuje obecnie poniższych typów aplikacji, ale są one w planach. 

### <a name="daemonsserver-side-applications"></a>Demony/po stronie serwera aplikacji

Aplikacje, które zawierają procesy długotrwałe lub niewymagające obecności użytkownika również potrzebują sposobu dostępu do zabezpieczonych zasobów takich jak interfejsy API sieci web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji (zamiast delegowanej tożsamości użytkownika) i za pomocą klienta OAuth 2.0 przepływ poświadczeń. Przepływ poświadczeń klienta nie jest taka sama jak w imieniu użytkownika — przepływ i w imieniu użytkownika — przepływ nie powinny być używane do uwierzytelniania serwera do serwera.

Mimo że przepływ poświadczeń klienta nie jest obecnie obsługiwane przez usługę Azure AD B2C, możesz skonfigurować przepływ poświadczeń klienta przy użyciu usługi Azure AD. Dzierżawy usługi Azure AD B2C udostępni niektóre funkcje usługi Azure AD enterprise dzierżawcy.  Przepływ poświadczeń klienta jest obsługiwana, korzystając z funkcji usługi Azure AD dla dzierżawy usługi Azure AD B2C. 

Aby skonfigurować przepływ poświadczeń klienta, zobacz [klienta OAuth 2.0 i usługi Azure Active Directory w wersji 2.0 credentials flow](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Po pomyślnym uwierzytelnieniu powoduje przyjęcie token sformatowane tak, aby może służyć przez usługę Azure AD zgodnie z opisem w [odwołania do tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="web-api-chains-on-behalf-of-flow"></a>Łańcuchy interfejsu API sieci Web (przepływ „w imieniu”)

Wiele architektur obejmuje interfejs API sieci Web, który musi wywołać inny podrzędny interfejs API sieci Web, przy czym oba interfejsy są zabezpieczane przez usługę Azure AD B2C. Ten scenariusz jest często spotykany w klientach natywnych, którzy mają zaplecza interfejsu API sieci Web. Następnie następuje wywołanie usługi online firmy Microsoft, np. interfejsu API Azure AD Graph.

Ten scenariusz obejmujący łańcuch interfejsów API sieci Web może być obsługiwany przy użyciu przyznania poświadczeń elementu nośnego OAuth 2.0 JWT, określanego również jako przepływ „w imieniu”.  Jednak przepływ „w imieniu” nie jest obecnie wdrażany w usłudze Azure AD B2C.
