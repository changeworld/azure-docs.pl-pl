---
title: Typy aplikacji dla platformy tożsamości firmy Microsoft | Azure
description: Typy aplikacji i scenariuszy obsługiwanych przez punkt końcowy programu Microsoft identity platformy (w wersji 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd7055794860398ecc6296d0174a691e7fac4450
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235249"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Typy aplikacji dla platformy tożsamości firmy Microsoft

Punkt końcowy platformy (w wersji 2.0) tożsamości firmy Microsoft obsługuje uwierzytelnianie w wielu nowoczesnych architekturach aplikacji, wszystkie z nich oparte na standardowych protokołach [OAuth 2.0 lub OpenID Connect](active-directory-v2-protocols.md). W tym artykule opisano typy aplikacji, które można tworzyć przy użyciu platformy tożsamości firmy Microsoft, niezależnie od preferowanego języka lub platformy. Zaprojektowano informacje ułatwiające zrozumienie ogólnych scenariuszy przed [rozpocząć pracę z kodem](v2-overview.md#getting-started).

> [!NOTE]
> Punkt końcowy platforma tożsamości firmy Microsoft nie obsługuje wszystkie scenariusze usługi Azure Active Directory (Azure AD) i funkcje. Aby ustalić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Podstawy

Musisz się zarejestrować, każdej aplikacji korzystającej z punktem końcowym platforma tożsamości firmy Microsoft w nowym [portalu rejestracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908). Proces rejestracji aplikacji służy do zbierania i przypisuje te wartości dla swojej aplikacji:

* **Identyfikator aplikacji (klienta)** , który jednoznacznie identyfikuje aplikację
* A **identyfikator URI przekierowania** używanego do kierowania odpowiedzi z powrotem do aplikacji
* Kilka innych typów kont takich jak obsługiwane wartości specyficzne dla scenariusza

Aby uzyskać szczegółowe informacje, Dowiedz się, jak [rejestrowanie aplikacji](quickstart-register-app.md).

Po zarejestrowaniu aplikacji Aplikacja komunikuje się z platformą Microsoft identity, wysyłając żądania do punktu końcowego. Firma Microsoft zapewnia struktur open source i bibliotek, które obsługują szczegóły tych żądań. Istnieje również możliwość implementacji logiki uwierzytelniania, samodzielnie tworząc żądania do tych punktów końcowych:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplikacje jednej strony (JavaScript)

Wiele nowoczesnych aplikacji ma aplikacja jednostronicowa fronton, który jest napisany głównie w języku JavaScript. Często są zapisywane przy użyciu struktury, takich jak Angular, React czy Vue. Punkt końcowy platforma tożsamości firmy Microsoft obsługuje te aplikacje przy użyciu [niejawnego przepływu OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

W tym przepływie aplikacja odbiera tokenów, bezpośrednio z platformą Microsoft identity punktu końcowego, bez żadnych wymiany serwera serwera autoryzacji. Wszystkie logiki uwierzytelniania i obsługi przyjmuje sesji należy umieścić w całości klienta JavaScript, bez dodatkowych Strona przekierowania.

![Przepływ uwierzytelniania niejawne](./media/v2-app-types/convergence-scenarios-implicit.svg)

Się z tym scenariuszem w praktyce, wypróbuj jedną z próbek kodu aplikacji jednostronicowej w [platforma tożsamości usługi Microsoft wprowadzenie](v2-overview.md#getting-started) sekcji.

## <a name="web-apps"></a>Aplikacje internetowe

W przypadku aplikacji sieci web (.NET, PHP, Java, Ruby, Python, węzeł), które użytkownik uzyskuje dostęp przez przeglądarkę, można użyć [OpenID Connect](active-directory-v2-protocols.md) podczas logowania użytkownika. W OpenID Connect aplikacji sieci web, otrzymuje identyfikator tokenu. Identyfikator tokenu jest token zabezpieczający, weryfikuje tożsamość użytkownika, który zawiera informacje o użytkowniku w formie oświadczeń:

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

Dalsze szczegóły różnego rodzaju tokeny używane w punkcie końcowym platforma tożsamości firmy Microsoft są dostępne w [token dostępu](access-tokens.md) odwołania i [id_token odwołania](id-tokens.md)

W aplikacji serwera sieci web przepływ logowania uwierzytelniania obejmuje następujące ogólne kroki:

![Przepływ uwierzytelniania w aplikacji sieci Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Aby zapewnić tożsamość użytkownika, sprawdzanie poprawności tokenu Identyfikacyjnego przy użyciu publicznego klucza podpisywania otrzymanego od firmy Microsoft platformy tożsamości z punktu końcowego. Plik cookie sesji jest ustawiony, który może służyć do identyfikowania użytkownika w odpowiedzi na żądania następną stronę.

Aby wyświetlić tym scenariuszem w praktyce, wypróbuj jedną z próbek kodu logowania aplikacji sieci web w [wprowadzenie platforma tożsamości usługi Microsoft](v2-overview.md#getting-started) sekcji.

Oprócz prostego logowania aplikacja serwera sieci web może być konieczne dostęp do innej usługi sieci web, takich jak interfejs API REST. W takim przypadku aplikacja serwera sieci web angażuje się w połączony przepływ protokołu OpenID Connect i OAuth 2.0, za pomocą [przepływ kodu autoryzacji OAuth 2.0](active-directory-v2-protocols.md). Aby uzyskać więcej informacji na temat tego scenariusza, przeczytaj temat [wprowadzenie do aplikacji sieci web i interfejsów API sieci Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Interfejsy API sieci Web

Microsoft platformy tożsamości z punktu końcowego służy do zabezpieczania usług sieci web, takich jak interfejs API sieci Web RESTful aplikacji. Zamiast tokeny Identyfikatora i pliki cookie z sesji interfejs API sieci Web używa tokenu dostępu OAuth 2.0 zabezpieczyć swoje dane i uwierzytelniają żądania przychodzące. Obiekt wywołujący interfejs API sieci Web dołącza token dostępu w nagłówku autoryzacji żądania HTTP w następujący sposób:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Interfejs API sieci Web używa tokenu dostępu, aby zweryfikować tożsamość elementu wywołującego interfejs API i wyodrębnić informacji o obiekcie wywołującym z oświadczeń zakodowanych w tokenie dostępu. Dalsze szczegóły różnego rodzaju tokeny używane w punkcie końcowym platforma tożsamości firmy Microsoft są dostępne w [token dostępu](access-tokens.md) odwołania i [id_token odwołania](id-tokens.md)

Interfejs API sieci Web użytkownikom można przyznać uprawnienia do zgadzaj lub zrezygnować z określonych funkcji lub danych, udostępnianie uprawnienia, znany także jako [zakresy](v2-permissions-and-consent.md). W przypadku wywoływania aplikacji można uzyskać uprawnień do zakresu użytkownik musi wyrazić zgodę zakres podczas przepływu. Punkt końcowy platforma tożsamości firmy Microsoft prosi użytkownika o zgodę, a następnie rejestruje uprawnień w wszystkie tokeny dostępu, które odbiera interfejsu API sieci Web. Internetowy interfejs API weryfikuje tokeny dostępu odbiera przy każdym wywołaniu i wykonuje sprawdzanie autoryzacji.

Interfejs API sieci Web może odbierać tokeny dostępu ze wszystkich typów aplikacji, w tym aplikacje serwera sieci web, pulpitu i aplikacji mobilnych, aplikacji jednej strony, demonów po stronie serwera i nawet w innych interfejsów API sieci Web. Ogólny przepływ dla internetowego interfejsu API wygląda następująco:

![Przepływ uwierzytelniania interfejsu API sieci Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu tokenów dostępu protokołu OAuth2, zapoznaj się z przykładów kodu interfejsu API sieci Web w [platforma tożsamości usługi Microsoft wprowadzenie](v2-overview.md#getting-started) sekcji.

W wielu przypadkach interfejsów API sieci web również konieczne wychodzących żądań inne podrzędne interfejsów API zabezpieczony przez platforma tożsamości usługi Microsoft w sieci web. Aby to zrobić, interfejsy API sieci web mogą korzystać z **w imieniu z** przepływ, który umożliwia interfejsu API sieci web do programu exchange tokenu dostępu przychodzącego o inny token dostępu ma być używany w żądaniach wychodzących. Aby uzyskać więcej informacji, zobacz [platforma tożsamości firmy Microsoft i przepływ OAuth 2.0 "w imieniu"](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplikacje mobilne i natywne

Aplikacje zainstalowane przez urządzenia, np. w przypadku aplikacji mobilnych i klasycznych często muszą uzyskać dostęp do usług zaplecza lub interfejsów API sieci Web, które przechowują dane i wykonywać funkcje w imieniu użytkownika. Te aplikacje można dodać logowania i autoryzacji do usług zaplecza za pomocą [przepływ kodu autoryzacji OAuth 2.0](v2-oauth2-auth-code-flow.md).

W tym przepływie aplikacja odbiera kod autoryzacji z punktu końcowego platformy tożsamości firmy Microsoft, po użytkownik loguje się. Kod autoryzacji reprezentuje zezwolenie aplikacji na wywołanie usług zaplecza w imieniu użytkownika, który jest zalogowany. Aplikację można wymienić kod autoryzacji w tle dla tokenu dostępu OAuth 2.0 i token odświeżania. Aplikacja może wykorzystanie tokenu dostępu do interfejsów API sieci Web uwierzytelniania w żądaniach HTTP i pobieranie nowych tokenów dostępu po wygaśnięciu starszych tokenów dostępu przy użyciu tokenu odświeżania.

![Przebieg uwierzytelniania w aplikacji natywnej](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Demony i aplikacji po stronie serwera

Aplikacje, które mają procesy długotrwałe lub niewymagające interakcji z użytkownikiem również muszą mieć dostęp do zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji, a nie użytkownika delegowana tożsamość przy użyciu przepływu poświadczeń klienta OAuth 2.0. Możesz udowodnić tożsamość aplikacji przy użyciu klucza tajnego klienta lub certyfikat. Aby uzyskać więcej informacji, zobacz [uwierzytelniania w usłudze Microsoft platformy tożsamości w aplikacje demona z certyfikatami](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

W tym przepływie aplikacja współpracuje bezpośrednio z `/token` punktu końcowego w celu uzyskania dostępu:

![Przepływu uwierzytelnianie aplikacji demona](./media/v2-app-types/convergence-scenarios-daemon.svg)

Aby skompilować aplikację demona, zobacz [dokumentacji poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md), lub spróbuj [.NET przykładową aplikację](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
