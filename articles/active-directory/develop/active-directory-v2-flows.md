---
title: Typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Typy aplikacji i scenariusze obsługiwane przez punktu końcowego v2.0 usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 134199c879f6793cb4ed0a88cf0593786341f6d8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory
Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD) obsługuje uwierzytelnianie dla różnych architektur nowoczesnych aplikacji, wszystkie z nich oparte na standardowych protokołach [OAuth 2.0 lub OpenID Connect](active-directory-v2-protocols.md). W tym artykule opisano typy aplikacji, które można tworzyć przy użyciu usługi Azure AD w wersji 2.0, niezależnie od tego, czy z preferowanego języka lub platformy. Informacje przedstawione w tym artykule zaprojektowano w celu ułatwić zrozumienie ogólnych scenariuszy przed [rozpocząć pracę z kodem](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> Punktu końcowego v2.0 nie obsługuje wszystkich scenariuszy Azure Active Directory i funkcji. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Podstawy
Należy zarejestrować każdej aplikacji, który korzysta z punktu końcowego v2.0 w [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com). Proces rejestracji aplikacji i przypisaniu tych wartości dla aplikacji:

* **Identyfikator aplikacji** który unikatowo identyfikuje aplikację
* A **identyfikator URI przekierowania** używanego do kierowania odpowiedzi z powrotem do aplikacji
* Kilka wartości specyficzne dla scenariusza

Aby uzyskać szczegółowe informacje, Dowiedz się, jak [rejestracji aplikacji](active-directory-v2-app-registration.md).

Po zarejestrowaniu aplikacji Aplikacja komunikuje się z usługą Azure AD, wysyłając żądania do punktu końcowego v2.0 usługi Azure AD. Udostępniamy open source struktury i biblioteki, które obsłużyć szczegóły te żądania. Istnieje również możliwość wdrożyć logikę uwierzytelniania, samodzielnie przez utworzenie żądań z tymi punktami końcowymi:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Aplikacje sieci Web
W przypadku aplikacji sieci web (.NET, PHP, Java, Ruby, Python, węzeł), które użytkownik uzyskuje dostęp do za pośrednictwem przeglądarki, można użyć [OpenID Connect](active-directory-v2-protocols.md) dla logowania użytkownika. W OpenID Connect aplikacji sieci web otrzymuje token Identyfikatora. Identyfikator tokenu jest token zabezpieczający, weryfikuje tożsamość użytkownika, który zawiera informacje o użytkowniku w formie oświadczeń:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Informacje na temat wszystkie typy tokenów i oświadczeń dostępnych dla aplikacji, zobacz [v2.0 tokeny odwołanie](active-directory-v2-tokens.md).

W aplikacji serwera sieci web przebieg uwierzytelniania logowania obejmuje następujące ogólne kroki:

![Przepływ uwierzytelniania aplikacji sieci Web](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Aby zapewnić tożsamości użytkownika, sprawdzania poprawności tokenu identyfikator z publicznego klucza podpisywania otrzymanego z punktu końcowego v2.0. Plik cookie sesji jest ustawiona, które mogą służyć do identyfikowania użytkownika na stronie kolejnych żądań.

Aby wyświetlić tym scenariuszem w praktyce, wypróbuj jedną z próbek kodu logowania aplikacji sieci web w naszym v2.0 [wprowadzenie](active-directory-appmodel-v2-overview.md#getting-started) sekcji.

Oprócz prostego logowania aplikacja serwera sieci web może być konieczne dostęp do innej usługi sieci web, takich jak interfejsu API REST. W takim przypadku aplikacji serwera sieci web uczestniczy w scalonej przepływ protokołu OpenID Connect i OAuth 2.0, za pomocą [przepływu kodu autoryzacji protokołu OAuth 2.0](active-directory-v2-protocols.md). Aby uzyskać więcej informacji na temat tego scenariusza, przeczytaj o [wprowadzenie do korzystania z aplikacji sieci web i interfejsów API sieci Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Interfejsy API sieci Web
Punktu końcowego v2.0 służy do zabezpieczania usług sieci web, takich jak interfejsu API sieci Web RESTful aplikacji. Zamiast tokeny Identyfikatora i pliki cookie z sesji interfejs API sieci Web używa tokenu dostępu protokołu OAuth 2.0 do zabezpieczania danych i do uwierzytelniania żądań przychodzących. Element wywołujący interfejs API sieci Web dołącza token dostępu w nagłówku autoryzacji żądania HTTP w następujący sposób:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Interfejs API sieci Web używa tokenu dostępu, aby sprawdzić tożsamość obiektu wywołującego interfejsu API i można wyodrębnić informacji o elemencie wywołującym z oświadczeń zakodowanych w tokenie dostępu. Aby poznać wszystkie typy tokenów i oświadczeń dostępnych dla aplikacji, zobacz temat [v2.0 tokeny odwołanie](active-directory-v2-tokens.md).

Interfejs API sieci Web umożliwiają użytkownikom uprawnienia do uczestnictwa w lub zrezygnować z określonych funkcji lub danych, nazywane również udostępnianie uprawnienia [zakresy](active-directory-v2-scopes.md). Wywołanie aplikacji można uzyskać uprawnień do zakresu użytkownik musi wyrazić zgodę zakres podczas przepływu. Punktu końcowego v2.0 monituje użytkownika o uprawnienia, a następnie rejestruje uprawnienia w wszystkich tokenów dostępu, które otrzymuje interfejsu API sieci Web. Interfejs API sieci Web weryfikuje tokeny dostępu odbierze przy każdym wywołaniu i sprawdza autoryzacji.

Interfejs API sieci Web może odbierać tokeny dostępu z niektórych typów aplikacji, w tym aplikacji serwera sieci web, pulpitu i aplikacji mobilnych, aplikacji jednej strony, demonów po stronie serwera i nawet innych interfejsów API sieci Web. Ogólny przepływ dla interfejsu API sieci Web wygląda następująco:

![Przepływ uwierzytelniania interfejsu API sieci Web](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu tokenów dostępu protokołu OAuth2, zapoznaj się z próbek kodu interfejsu API sieci Web w naszym [wprowadzenie](active-directory-appmodel-v2-overview.md#getting-started) sekcji.

W wielu przypadkach interfejsów API sieci web muszą również wprowadzić inne podrzędne interfejsów API zabezpieczonej przez usługi Azure Active Directory w sieci web żądania wychodzącego. Aby to zrobić, interfejsów API sieci web można korzystać z usługi Azure AD **w imieniu z** przepływu, dzięki czemu interfejsu API sieci web do programu exchange przychodzący token dostępu o inny token dostępu ma być używana podczas żądania wychodzącego. Punktem końcowym v2.0 w imieniu przepływu jest opisany w [szczegółowo opisano w tym miejscu](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Aplikacje mobilne i natywne
Aplikacje zainstalowane urządzenie, takie jak aplikacji mobilnych i klasycznych, często muszą uzyskiwać dostęp do usług zaplecza lub interfejsów API sieci Web, które przechowują dane i funkcji w imieniu użytkownika. Tych aplikacji można dodać logowania i autoryzacji do usług zaplecza przy użyciu [przepływu kodu autoryzacji protokołu OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

W tym przepływie aplikacja odbiera kod autoryzacji z punktem końcowym v2.0, gdy użytkownik się zaloguje. Kod autoryzacji reprezentuje zezwolenie aplikacji na wywołanie usług zaplecza w imieniu użytkownika, który jest zalogowany. Aplikację można wymienić kod autoryzacji w tle dla tokenu dostępu protokołu OAuth 2.0 i token odświeżania. Aplikację można użyć tokenu dostępu do interfejsów API sieci Web uwierzytelniania w żądaniach HTTP i użyj token odświeżania, aby uzyskać nowe tokeny dostępu po wygaśnięciu starsze tokenów dostępu.

![Przepływ uwierzytelniania aplikacji natywnej](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Aplikacje jednej strony (JavaScript)
Wiele nowoczesnych aplikacji ma jednostronicowej aplikacji fronton głównie napisany w języku JavaScript. Często są zapisywane przez przy użyciu platformy takie jak AngularJS, Ember.js lub Durandal.js. Punktu końcowego v2.0 usługi Azure AD obsługuje te aplikacje przy użyciu [niejawnego przepływu OAuth 2.0](active-directory-v2-protocols-implicit.md).

W tym przepływie aplikacja odbiera tokeny bezpośrednio z v2.0 punktu końcowego, bez żadnych wymiany serwera serwera autoryzacji. Wszystkie logika uwierzytelniania i sesji obsługi ma umieścić w całości klienta JavaScript, bez dodatkowej strony przekierowania.

![Przepływ uwierzytelniania niejawne](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Aby wyświetlić ten scenariusz, w akcji, wypróbuj jedną z próbek kodu aplikacji jednostronicowej w naszych [wprowadzenie](active-directory-appmodel-v2-overview.md#getting-started) sekcji.

## <a name="daemons-and-server-side-apps"></a>Demony i aplikacji po stronie serwera
Aplikacje, które mają procesy długotrwałe lub niewymagające interakcji z użytkownikiem również muszą mieć dostęp do zabezpieczonych zasobów, takich jak interfejsów API sieci Web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji, a nie użytkownika delegowane tożsamość o przepływ poświadczeń klienta OAuth 2.0.

W tym przepływie aplikacja współpracuje bezpośrednio z `/token` punktu końcowego, aby uzyskać punktów końcowych:

![Demon przebieg uwierzytelniania w aplikacji](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Aby utworzyć aplikację demona, zobacz [dokumentacji poświadczeń klienta](active-directory-v2-protocols-oauth-client-creds.md), lub spróbuj [.NET Przykładowa aplikacja](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
