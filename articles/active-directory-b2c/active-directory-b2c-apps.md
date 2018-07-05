---
title: Typy aplikacji w usłudze Azure Active Directory B2C | Microsoft Docs
description: Typy aplikacji, które można tworzyć w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu1
ms.component: B2C
ms.openlocfilehash: d306d27f448ab9dd95e891b81f27b69e11f05495
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442070"
---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Usługa Azure Active Directory B2C: typy aplikacji
Usługa Azure Active Directory (Azure AD) B2C obsługuje uwierzytelnianie w wielu nowoczesnych architekturach aplikacji. Wszystkie one są oparte na standardowych protokołach branżowych [OAuth 2.0](active-directory-b2c-reference-protocols.md) lub [OpenID Connect](active-directory-b2c-reference-protocols.md). W tym dokumencie krótko opisano typy aplikacji, które można tworzyć, niezależnie od preferowanego języka lub platformy. Ułatwia on także zrozumienie ogólnych scenariuszy przed [rozpoczęciem tworzenia aplikacji](active-directory-b2c-overview.md).

## <a name="the-basics"></a>Podstawy
Każda aplikacja, która korzysta z usługi Azure AD B2C musi być zarejestrowana w [katalogu usługi B2C](active-directory-b2c-get-started.md) za pośrednictwem witryny [Azure Portal](https://portal.azure.com/). Proces rejestracji aplikacji polega na zgromadzeniu kilku wartości i przypisaniu ich do aplikacji:

* **Identyfikator aplikacji** który w sposób unikatowy identyfikuje aplikację.
* **Identyfikator URI przekierowania**, który może służyć do kierowania odpowiedzi z powrotem do aplikacji.
* Dowolne inne wartości właściwe dla scenariusza. Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](active-directory-b2c-app-registration.md).

Każde żądanie wysłane do usługi Azure AD B2C określa **zasady**. Zasady regulują sposób działania usługi Azure AD. Można również użyć tych punktów końcowych do utworzenia zestawu funkcji środowiska użytkownika, które można w znacznym stopniu dostosowywać. Wspólne zasady obejmują rejestrację, logowanie i edycję profilu. Jeśli nie znasz tych zasad, przed wykonaniem dalszych czynności zapoznaj się z informacjami na temat [rozszerzonej platformy zasad](active-directory-b2c-reference-policies.md) usługi Azure AD B2C.

Interakcja każdej aplikacji jest realizowana według następującego ogólnego schematu:

1. Aplikacja przekierowuje użytkownika do punktu końcowego v2.0, aby wykonać [zasady](active-directory-b2c-reference-policies.md).
2. Użytkownik wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja odbiera określonego rodzaju token zabezpieczający z punktu końcowego v2.0.
4. Aplikacja używa tokenu zabezpieczającego, aby uzyskać dostęp do chronionych informacji lub chronionego zasobu.
5. Serwer zasobów weryfikuje token zabezpieczający, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token zabezpieczający.

<!-- TODO: Need a page for libraries to link to --> Te kroki mogą różnić się nieco w zależności od typu aplikacji, które tworzysz.

## <a name="web-apps"></a>Aplikacje internetowe
W przypadku aplikacji internetowych (w tym .NET, PHP, Java, Ruby, Python i Node.js), które są hostowane na serwerze i dostępne za pośrednictwem przeglądarki, usługa Azure AD B2C obsługuje protokół [OpenID Connect](active-directory-b2c-reference-protocols.md) w odniesieniu wszystkich funkcji środowiska użytkownika. Obejmuje to logowanie, rejestrację i zarządzanie profilami. Podczas wdrażania protokołu OpenID Connect w usłudze Azure AD B2C aplikacja internetowa inicjuje te funkcje środowiska użytkownika, wysyłając żądania uwierzytelniania do usługi Azure AD. Wynikiem żądania jest token `id_token`. Ten token zabezpieczający reprezentuje tożsamość użytkownika. Zawiera także informacje o użytkowniku w formie oświadczeń:

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

Aby uzyskać więcej informacji o typach tokenów i oświadczeń dostępnych dla aplikacji, zobacz [informacje o tokenach usługi B2C](active-directory-b2c-reference-tokens.md).

W aplikacji internetowej każde wykonanie [zasad](active-directory-b2c-reference-policies.md) obejmuje następujące ogólne kroki:

![Obraz ścieżek aplikacji internetowej](./media/active-directory-b2c-apps/webapp.png)

Sprawdzenie tokenu `id_token` przy użyciu publicznego klucza podpisywania otrzymanego z usługi Azure AD jest wystarczające do zweryfikowania tożsamości użytkownika. Powoduje ono również ustawienie pliku cookie sesji, którego można użyć do identyfikacji użytkownika w żądaniach na kolejnych stronach.

Aby zapoznać się z tym scenariuszem w praktyce, wypróbuj jedną z próbek kodu logowania aplikacji internetowej w [sekcji Wprowadzenie](active-directory-b2c-overview.md).

Oprócz ułatwienia prostego logowania aplikacja serwera sieci Web może również potrzebować dostępu do usługi sieci Web zaplecza. W takim przypadku aplikacja internetowa może wykonać nieco inny [przepływ protokołu OpenID Connect](active-directory-b2c-reference-oidc.md) i uzyskać tokeny przy użyciu kodów autoryzacji i tokenów odświeżania. Ten scenariusz jest opisany w [sekcji dotyczącej interfejsów API sieci Web](#web-apis) poniżej.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Interfejsy API sieci Web
Usługi Azure AD B2C można użyć do zabezpieczania usług sieci Web, np. interfejsu API sieci Web RESTful aplikacji. Interfejsy API sieci Web mogą zabezpieczać swoje dane za pomocą protokołu OAuth 2.0, uwierzytelniając żądania przychodzące HTTP przy użyciu tokenów. Element wywołujący interfejs API sieci Web dołącza token w nagłówku autoryzacji żądania HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Interfejs API sieci Web może następnie użyć tego tokenu do zweryfikowania tożsamości elementu wywołującego interfejs API oraz do wyodrębnienia informacji o elemencie wywołującym z oświadczeń zakodowanych w tokenie. Aby uzyskać więcej informacji o typach tokenów i oświadczeń dostępnych dla aplikacji, zobacz [informacje o tokenach usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> Usługa Azure AD B2C obsługuje obecnie tylko interfejsy API sieci Web, do których dostęp uzyskują ich własne dobrze znane aplikacje klienckie. Na przykład pełna aplikacja może obejmować aplikację systemu iOS, aplikację systemu Android i interfejs API sieci Web zaplecza. Taka architektura jest w pełni obsługiwana. Zezwalanie klientowi partnera, np. innej aplikacji systemu iOS, na dostęp do tego samego interfejsu API sieci Web nie jest obecnie obsługiwane. Wszystkie składniki pełnej aplikacji muszą współdzielić jeden identyfikator aplikacji.
>
>

Internetowy interfejs API może odbierać tokeny od wielu typów klientów, m.in. aplikacji internetowych, klasycznych, mobilnych i jednostronicowych, demonów po stronie serwera oraz innych internetowych interfejsów API. Oto przykład pełnego przepływu aplikacji internetowej, która wywołuje internetowy interfejs API:

![Obraz ścieżek internetowego interfejsu API aplikacji internetowej](./media/active-directory-b2c-apps/webapi.png)

Aby dowiedzieć się więcej o kodach autoryzacji, tokenach odświeżania i krokach uzyskiwania tokenów, zapoznaj się z informacjami na temat [protokołu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu usługi Azure AD B2C, zapoznaj się z samouczkami dotyczącymi interfejsu API sieci Web w [sekcji Wprowadzenie](active-directory-b2c-overview.md).

## <a name="mobile-and-native-apps"></a>Aplikacje mobilne i natywne
Aplikacje, które są zainstalowane na urządzeniach, takie jak aplikacje mobilne i klasyczne, często muszą uzyskiwać dostęp do usług zaplecza lub interfejsów API sieci Web w imieniu użytkowników. Możesz dodać niestandardowe środowiska zarządzania tożsamością do aplikacji natywnych i bezpiecznie wywoływać usługi zaplecza przy użyciu usługi Azure AD B2C i [przepływu kodu autoryzacji protokołu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

W tym przepływie aplikacja wykonuje [zasady](active-directory-b2c-reference-policies.md) i odbiera kod `authorization_code` od usługi Azure AD po wypełnieniu zasad przez użytkownika. Kod `authorization_code` reprezentuje zezwolenie aplikacji na wywołanie usług zaplecza w imieniu użytkownika, który jest aktualnie zalogowany. Aplikacja może następnie wymienić kod `authorization_code` w tle na tokeny `id_token` i `refresh_token`.  Aplikacja może używać tokenu `id_token` do uwierzytelniania interfejsu API sieci Web zaplecza w żądaniach HTTP. Może również użyć tokenu `refresh_token` do pobrania nowego tokenu `id_token`, gdy wygaśnie stary.

> [!NOTE]
> Usługa Azure AD B2C obsługuje obecnie tylko tokeny, które umożliwiają dostęp do własnej usługi sieci Web zaplecza aplikacji. Na przykład pełna aplikacja może obejmować aplikację systemu iOS, aplikację systemu Android i interfejs API sieci Web zaplecza. Taka architektura jest w pełni obsługiwana. Zezwalanie aplikacji systemu iOS na dostęp do interfejsu API sieci Web partnera przy użyciu tokenów dostępu protokołu OAuth 2.0 nie jest obecnie obsługiwane. Wszystkie składniki pełnej aplikacji muszą współdzielić jeden identyfikator aplikacji.
>
>

![Obraz ścieżek aplikacji natywnej](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Bieżące ograniczenia
Usługa Azure AD B2C nie obsługuje obecnie poniższych typów aplikacji, ale są one w planach. 

### <a name="daemonsserver-side-apps"></a>Demony/aplikacje po stronie serwera
Aplikacje obejmujące procesy długotrwałe lub niewymagające obecności użytkownika również potrzebują sposobu uzyskiwania dostępu do zabezpieczonych zasobów, np. interfejsów API sieci Web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji (zamiast delegowanej tożsamości użytkownika) oraz przy użyciu przepływu poświadczeń klienta OAuth 2.0.

Ten przepływ nie jest obecnie obsługiwany w usłudze Azure AD B2C. Te aplikacje mogą uzyskiwać tokeny tylko po wystąpieniu interaktywnego przepływu użytkownika.

### <a name="web-api-chains-on-behalf-of-flow"></a>Łańcuchy interfejsu API sieci Web (przepływ „w imieniu”)
Wiele architektur obejmuje interfejs API sieci Web, który musi wywołać inny podrzędny interfejs API sieci Web, przy czym oba interfejsy są zabezpieczane przez usługę Azure AD B2C. Ten scenariusz jest często spotykany w klientach natywnych, którzy mają zaplecza interfejsu API sieci Web. Następnie następuje wywołanie usługi online firmy Microsoft, np. interfejsu API Azure AD Graph.

Ten scenariusz obejmujący łańcuch interfejsów API sieci Web może być obsługiwany przy użyciu przyznania poświadczeń elementu nośnego OAuth 2.0 JWT, określanego również jako przepływ „w imieniu”.  Jednak przepływ „w imieniu” nie jest obecnie wdrażany w usłudze Azure AD B2C.
