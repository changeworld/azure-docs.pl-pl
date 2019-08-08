---
title: Scenariusze uwierzytelniania dla platformy tożsamości firmy Microsoft | Azure
description: Poznaj przepływy uwierzytelniania i scenariusze aplikacji dla platformy tożsamości firmy Microsoft. Poznaj różne typy aplikacji, które mogą uwierzytelniać tożsamości, uzyskiwać tokeny i wywoływać chronione interfejsy API.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946eb0343c2858e275e40b7a581fd48e1713accd
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853512"
---
# <a name="authentication-flows-and-application-scenarios"></a>Przepływy uwierzytelniania i scenariusze aplikacji

Punkt końcowy Microsoft Identity platform (v 2.0) obsługuje uwierzytelnianie dla różnych nowoczesnych architektur aplikacji, wszystkie z nich oparte na standardach branżowych [OAuth 2,0 lub OpenID Connect Connect](active-directory-v2-protocols.md).  Przy użyciu [bibliotek uwierzytelniania](reference-v2-libraries.md)aplikacje uwierzytelniają tożsamości i uzyskują tokeny umożliwiające dostęp do chronionych interfejsów API. W tym artykule opisano różne przepływy uwierzytelniania i scenariusze aplikacji, które są używane w programie.  Ten artykuł zawiera również listę [scenariuszy aplikacji oraz obsługiwane przepływy uwierzytelniania](#scenarios-and-supported-authentication-flows) i [scenariusze aplikacji oraz obsługiwane platformy i języki](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Kategorie aplikacji

Tokeny mogą być uzyskiwane z wielu typów aplikacji: Aplikacje sieci Web, aplikacje mobilne lub klasyczne, interfejsy API sieci Web i aplikacje działające na urządzeniach, które nie mają przeglądarki (lub iOT). Aplikacje można klasyfikować według następujących:

- [Chronione zasoby a aplikacje klienckie](#protected-resources-vs-client-applications). Niektóre scenariusze dotyczą ochrony zasobów (Web Apps lub interfejsów API sieci Web), a inne dotyczą uzyskiwania tokenu zabezpieczającego do wywoływania chronionego internetowego interfejsu API.
- [Z użytkownikami lub bez użytkowników](#with-users-or-without-users). Niektóre scenariusze obejmują zalogowanego użytkownika, a inne nie obejmują użytkownika (scenariusze demona).
- [Aplikacje jednostronicowe, publiczne aplikacje klienckie i poufne aplikacje klienckie](#single-page-applications-public-client-applications-and-confidential-client-applications). Są to trzy duże kategorie typów aplikacji. Biblioteki i obiekty używane do manipulowania nimi będą się różnić.
- [Odbiorcy logowania](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). Niektóre przepływy uwierzytelniania są niedostępne dla niektórych odbiorców logowania. Niektóre przepływy są dostępne tylko dla kont służbowych, a niektóre z nich są dostępne dla kont służbowych i osobistych kont Microsoft. Odbiorcy mogą zależeć od przepływów uwierzytelniania.
- [Obsługiwane przepływy protokołu OAuth 2,0](#scenarios-and-supported-authentication-flows).  Przepływy uwierzytelniania są używane do implementowania scenariuszy aplikacji żądających tokenów.  Nie istnieje mapowanie jeden do jednego między scenariuszami aplikacji i przepływami uwierzytelniania.
- [Obsługiwane platformy](#scenarios-and-supported-platforms-and-languages). Nie wszystkie scenariusze aplikacji są dostępne dla każdej platformy.

### <a name="protected-resources-vs-client-applications"></a>Chronione zasoby a aplikacje klienckie

Scenariusze uwierzytelniania obejmują dwa działania:

- **Uzyskiwanie tokenów zabezpieczających** dla chronionego internetowego interfejsu API. Firma Microsoft zaleca używanie [bibliotek uwierzytelniania](reference-v2-libraries.md#microsoft-supported-client-libraries) do uzyskiwania tokenów, w szczególności rodziny bibliotek uwierzytelniania firmy Microsoft (MSAL)
- **Ochrona internetowego interfejsu API** (lub aplikacja sieci Web). Jednym z wyzwań związanych z ochroną zasobów (aplikacji sieci Web lub interfejsu API sieci Web) jest zweryfikowanie tokenu zabezpieczającego. Oferty firmy Microsoft na niektórych platformach — w przypadku [bibliotek oprogramowania pośredniczącego](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Z użytkownikami lub bez użytkowników

Większość scenariuszy uwierzytelniania uzyskuje tokeny w imieniu **użytkownika**(zalogowanym).

![scenariusze z użytkownikami](media/scenarios/scenarios-with-users.svg)

Istnieją jednak również scenariusze (aplikacje demonów), w których aplikacje będą uzyskiwać tokeny w imieniu siebie (bez użytkownika).

![aplikacje demona](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>Aplikacje jednostronicowe, publiczne aplikacje klienckie i poufne aplikacje klienckie

Tokeny zabezpieczające można pobrać z wielu typów aplikacji. Aplikacje są podzielone na trzy kategorie:

- **Aplikacje** jednostronicowe (SPA) to forma aplikacji sieci Web, w których tokeny są uzyskiwane z aplikacji działającej w przeglądarce (w języku JavaScript lub TypeScript). Wiele nowoczesnych aplikacji ma fronton aplikacji jednostronicowej, który jest przeznaczony głównie do pisania w języku JavaScript. Często aplikacja jest zapisywana przy użyciu struktury, takiej jak kątowy, reaguje lub Vue. MSAL. js jest jedyną biblioteką uwierzytelniania firmy Microsoft obsługującą aplikacje jednostronicowe.

- **Publiczne aplikacje klienckie** zawsze logują się użytkownika. Są to następujące aplikacje:
  - Aplikacje klasyczne wywołujące interfejsy API sieci Web w imieniu zalogowanego użytkownika.
  - Aplikacje mobilne.
  - Trzecia kategoria aplikacji działająca na urządzeniach, na których nie ma przeglądarki (aplikacje bez przeglądarki, uruchomione w usłudze iOT dla wystąpienia).

  Są one reprezentowane przez klasę MSAL o nazwie [PublicClientApplication](msal-client-applications.md).

- **Poufne aplikacje klienckie**
  - Aplikacje sieci Web wywołujące internetowy interfejs API
  - Interfejsy API sieci Web wywołujące internetowy interfejs API
  - Aplikacje demona (nawet zaimplementowane jako usługa konsolowa, taka jak demon w systemie Linux lub usługa systemu Windows)
 
  Te typy aplikacji używają [ConfidentialClientApplication](msal-client-applications.md)

## <a name="application-scenarios"></a>Scenariusze aplikacji

Punkt końcowy platformy tożsamości firmy Microsoft obsługuje uwierzytelnianie dla różnych architektur aplikacji: aplikacje jednostronicowe, aplikacje sieci Web, interfejsy API sieci Web, aplikacje mobilne i natywne oraz demony i aplikacje po stronie serwera.  Aplikacje używają różnych przepływów uwierzytelniania do logowania użytkowników i uzyskiwania tokenów do wywoływania chronionych interfejsów API.

### <a name="single-page-application"></a>Aplikacja jednostronicowa

Wiele nowoczesnych aplikacji sieci Web jest zbudowanych jako aplikacje jednostronicowe po stronie klienta, które są zapisywane przy użyciu języka JavaScript lub środowiska SPA, takiego jak kątowy, Vue. js i reaguje. js. Aplikacje te działają w przeglądarce internetowej i mają różne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera. Platforma tożsamości firmy Microsoft umożliwia aplikacjom jednostronicowym Logowanie użytkowników i uzyskiwanie tokenów w celu uzyskania dostępu do usług zaplecza lub interfejsów API sieci Web.

![Aplikacja jednostronicowa](media/scenarios/spa-app.svg)

Aby uzyskać więcej informacji, Przeczytaj [aplikacje](scenario-spa-overview.md)jednostronicowe.

### <a name="web-application-signing-in-a-user"></a>Logowanie aplikacji sieci Web użytkownika

![Znaki aplikacji sieci Web w użytkownikach](media/scenarios/scenario-webapp-signs-in-users.svg)

Aby **chronić aplikację sieci Web** (podpisywanie użytkownika), należy użyć:

- W środowisku .NET World, ASP.NET lub ASP.NET Core za pomocą programu ASP.NET Open ID Connect oprogramowanie pośredniczące. Pod okapem ochrona zasobów obejmuje sprawdzanie poprawności tokenu zabezpieczającego, który jest wykonywany przez [rozszerzenia IdentityModel dla biblioteki .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a nie biblioteki MSAL

- W przypadku tworzenia oprogramowania w programie Node. js będziesz używać usługi Passport. js.

Aby uzyskać więcej informacji, zapoznaj [się z aplikacją internetową, która loguje się do użytkowników](scenario-web-app-sign-user-overview.md).

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Logowanie aplikacji sieci Web użytkownika i wywoływanie internetowego interfejsu API w imieniu użytkownika

![Interfejsy API sieci Web wywołań aplikacji sieci Web](media/scenarios/web-app.svg)

W aplikacji sieci Web, aby **wywoływać internetowy interfejs API** w imieniu użytkownika, należy użyć `ConfidentialClientApplication`MSAL. Będziesz używać przepływu kodu autoryzacji, przechowując uzyskany token w pamięci podręcznej tokenów. Następnie kontroler uzyskuje tokeny dyskretnie z pamięci podręcznej w razie konieczności. MSAL odświeża token w razie konieczności.

Aby uzyskać więcej informacji, zapoznaj się z [witryną internetową interfejsów API wywołań aplikacji sieci Web](scenario-web-app-call-api-overview.md)

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>Aplikacja klasyczna wywołująca internetowy interfejs API w imieniu zalogowanego użytkownika

Aby wywołać interfejs API sieci Web z aplikacji klasycznej, która loguje użytkowników, należy użyć metod pozyskiwania tokenów MSAL PublicClientApplication's Interactive. Te interaktywne metody pozwalają kontrolować środowisko logowania użytkownika. Aby włączyć tę interakcję, MSAL wykorzystuje przeglądarkę internetową.

![Pulpitu](media/scenarios/desktop-app.svg)

W przypadku aplikacji hostowanych w systemie Windows uruchomionych na komputerach przyłączonych do domeny systemu Windows lub usługi AAD dołączono inną możliwość. Aplikacje te mogą uzyskać token dyskretnie przy użyciu [zintegrowanego uwierzytelniania systemu Windows](https://aka.ms/msal-net-iwa).

Aplikacje uruchomione na urządzeniu bez przeglądarki nadal będą mogły wywołać interfejs API w imieniu użytkownika. W celu uwierzytelnienia użytkownik będzie musiał zalogować się na innym urządzeniu, na którym jest zainstalowana przeglądarka sieci Web. Aby włączyć ten scenariusz, musisz użyć [przepływu kodu urządzenia](https://aka.ms/msal-net-device-code-flow)

![Przepływ kodu urządzenia](media/scenarios/device-code-flow-app.svg)

Mimo że nie jest to zalecane, możesz użyć [nazwy użytkownika/hasła](https://aka.ms/msal-net-up) w publicznych aplikacjach klienckich. Ten przepływ jest nadal wymagany w niektórych scenariuszach (na przykład DevOps), ale uważaj, że jego użycie spowoduje nałożenie ograniczeń dotyczących aplikacji. Na przykład aplikacje używające tego przepływu nie będą mogły zalogować się do użytkownika, który musi przeprowadzić uwierzytelnianie wieloskładnikowe (dostęp warunkowy). Nie pozwoli to aplikacji na korzystanie z logowania jednokrotnego. Uwierzytelnianie przy użyciu nazwy użytkownika/hasła dotyczy zasad nowoczesnego uwierzytelniania i jest dostępne tylko w przypadku starszych przyczyn.

Jeśli chcesz, aby pamięć podręczna tokenów była trwała w aplikacjach klasycznych, należy [dostosować serializację pamięci podręcznej tokenu](https://aka.ms/msal-net-token-cache-serialization). Można nawet włączyć pamięć podręczną tokenów zgodnych z poprzednimi i progresywnymi przy użyciu wcześniejszych generacji bibliotek uwierzytelniania (ADAL.NET 3. x i 4. x) przez implementację [podwójnego serializacji pamięci](https://aka.ms/msal-net-dual-cache-serialization)podręcznej tokenów.

Aby uzyskać więcej informacji, zapoznaj się z [aplikacją klasyczną, która wywołuje interfejsy API sieci Web](scenario-desktop-overview.md).

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>Aplikacja mobilna wywołująca internetowy interfejs API w imieniu użytkownika, który jest zalogowany interaktywnie

Podobnie jak w przypadku aplikacji klasycznych, aplikacja mobilna będzie używać metod pozyskiwania tokenów MSAL PublicClientApplication's Interactive, aby uzyskać token do wywoływania interfejsu API sieci Web.

![Urządzenia przenośne](media/scenarios/mobile-app.svg)

MSAL iOS i MSAL Android, domyślnie Użyj przeglądarki sieci Web systemu. Można jednak również skierować go do korzystania z osadzonego widoku sieci Web. Istnieją pewne informacje zależne od platformy mobilnej: (Platformy UWP, iOS, Android).

Niektóre scenariusze, w tym dostęp warunkowy powiązany z IDENTYFIKATORem urządzenia lub zarejestrowane urządzenie, wymagają, aby [Broker](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) został zainstalowany na urządzeniu. Przykłady brokerów to portal firmy Microsoft (w systemie Android), Microsoft Authenticator (Android i iOS). MSAL może teraz współdziałać z brokerami.

> [!NOTE]
> Aplikacja mobilna (przy użyciu MSAL. iOS, MSAL. Do systemu Android lub MSAL.NET/Xamarin) mogą być stosowane zasady ochrony aplikacji (na przykład uniemożliwienie użytkownikowi kopiowania tekstu chronionego). Jest on [zarządzany przez usługę Intune](https://docs.microsoft.com/intune/app-sdk) i uznawany przez usługę Intune jako zarządzaną aplikację. [Zestaw SDK usługi Intune](https://docs.microsoft.com/intune/app-sdk-get-started) jest oddzielony od bibliotek MSAL i jednocześnie komunikuje się z usługą AAD.

Aby uzyskać więcej informacji, zapoznaj się [z aplikacją mobilną, która wywołuje interfejsy API sieci Web](scenario-mobile-overview.md).

### <a name="protected-web-api"></a>Chroniony internetowy interfejs API

Możesz użyć punktu końcowego platformy tożsamości firmy Microsoft do zabezpieczenia usług sieci Web, takich jak internetowy interfejs API aplikacji RESTful. Chroniony internetowy interfejs API jest wywoływany przy użyciu tokenu dostępu w celu zabezpieczania danych i uwierzytelniania żądań przychodzących. Obiekt wywołujący internetowy interfejs API dołącza token dostępu w nagłówku autoryzacji żądania HTTP. Jeśli chcesz chronić ASP.NET internetowy interfejs API lub ASP.NET Core, musisz sprawdzić poprawność tokenu dostępu. W tym celu należy użyć oprogramowania pośredniczącego JWT ASP.NET. Sprawdzanie poprawności jest wykonywane przez [rozszerzenia IdentityModel dla biblioteki .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a nie MSAL.NET

Aby uzyskać więcej informacji, Przeczytaj [chroniony internetowy interfejs API](scenario-protected-web-api-overview.md).

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>Interfejs API sieci Web wywołuje inny podrzędny interfejs API sieci Web w imieniu użytkownika, dla którego został wywołany

Jeśli ponadto chcesz, aby interfejs API sieci Web ASP.NET lub ASP.NET Core był wywoływany przez inny internetowy interfejs API w imieniu użytkownika, aplikacja będzie musiała uzyskać token dla podrzędnego interfejsu API sieci Web przy użyciu metody ConfidentialClientApplication pobierającej token w imieniu [ Użytkownik](https://aka.ms/msal-net-on-behalf-of). Jest to również nazwa usługi dla wywołań usług.
Interfejsy API sieci Web wywołujące inny internetowy interfejs API również muszą zapewnić niestandardową serializację pamięci podręcznej

  ![Interfejs API sieci Web](media/scenarios/web-api.svg)

Aby uzyskać więcej informacji, Przeczytaj [internetowy interfejs API, który wywołuje interfejsy API sieci Web](scenario-web-api-call-api-overview.md).

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>Aplikacja klasyczna/usługa lub demon sieci Web wywołujący internetowy interfejs API bez użytkownika (we własnym imieniu)

Aplikacje, które mają długotrwałe procesy lub działają bez interakcji z użytkownikiem, muszą również mieć dostęp do zabezpieczonych interfejsów API sieci Web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji, a nie delegowanej tożsamości użytkownika. Udowadniają one swoją tożsamość przy użyciu klucza tajnego klienta lub certyfikatu.
Można napisać takie aplikacje (aplikację demona), która pobiera token dla aplikacji na górze przy użyciu metod pozyskiwania [poświadczeń klienta](https://aka.ms/msal-net-client-credentials) MSAL ConfidentialClientApplication's. Załóżmy, że aplikacja zarejestrowała wcześniej wpis tajny (hasło aplikacji lub certyfikat lub potwierdzenie klienta) z usługą Azure AD, która następnie jest udostępniana z tym wywołaniem.

![Aplikacja demona](media/scenarios/daemon-app.svg)

Aby uzyskać więcej informacji, Odczytaj [aplikację demona, która wywołuje interfejsy API sieci Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenariusze i obsługiwane przepływy uwierzytelniania

Scenariusze, które obejmują pobieranie tokenów, również są mapowane na przepływy uwierzytelniania OAuth 2,0 opisane szczegółowo w temacie [Protokoły Microsoft Identity platform](active-directory-v2-protocols.md)

|Scenariusz | Szczegółowy opis scenariusza | Przepływ OAuth 2,0/Grant | Odbiorcy |
|--|--|--|--|
| [![Aplikacja jednostronicowa](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | [Aplikacje z jedną stroną](scenario-spa-overview.md) | [Konwersja](v2-oauth2-implicit-grant-flow.md) | Konta służbowe i konta osobiste, B2C
| [![Aplikacja internetowa, która loguje użytkowników](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | [Aplikacja sieci Web, która loguje użytkowników](scenario-web-app-sign-user-overview.md) | [Kod autoryzacji](v2-oauth2-auth-code-flow.md) | Konta służbowe i konta osobiste, B2C |
| [![Aplikacja sieci Web, która wywołuje interfejsy API sieci Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | [Aplikacja sieci Web, która wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md) | [Kod autoryzacji](v2-oauth2-auth-code-flow.md) | Konta służbowe i konta osobiste, B2C |
| [![Aplikacja klasyczna, która wywołuje interfejsy API sieci Web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) | [Aplikacja klasyczna wywołująca internetowe interfejsy API](scenario-desktop-overview.md)| Interactive ([kod autoryzacji](v2-oauth2-auth-code-flow.md) z PKCE) | Konta służbowe i konta osobiste, B2C |
| | | Zintegrowane okna | Konta służbowe |
| | | [Hasło właściciela zasobu](v2-oauth-ropc.md)  | Konta służbowe, B2C |
| ![Przepływ kodu urządzenia](media/scenarios/device-code-flow-app.svg)| [Aplikacja klasyczna wywołująca internetowe interfejsy API](scenario-desktop-overview.md) | [Kod urządzenia](v2-oauth2-device-code.md)  | Konta służbowe * |
| [![Aplikacja mobilna, która wywołuje interfejsy API sieci Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | [Aplikacja mobilna, która wywołuje interfejsy API sieci Web](scenario-mobile-overview.md) | Interactive ([kod autoryzacji](v2-oauth2-auth-code-flow.md) z PKCE)  |   Konta służbowe i konta osobiste, B2C
| | | Hasło właściciela zasobu  | Konta służbowe, B2C |
| [![Aplikacja demona](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | [Aplikacja demona](scenario-daemon-overview.md) | [Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md)  |   Uprawnienia tylko do aplikacji (bez użytkownika) tylko w organizacjach usługi AAD
| [![Internetowy interfejs API, który wywołuje interfejsy API sieci Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | [Internetowy interfejs API, który wywołuje interfejsy API sieci Web](scenario-web-api-call-api-overview.md)| [W imieniu](v2-oauth2-on-behalf-of-flow.md) | Konta służbowe i konta osobiste |

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenariusze i obsługiwane platformy i Języki

Nie każdy typ aplikacji jest dostępny na każdej platformie. Możesz również użyć różnych języków do kompilowania aplikacji. Biblioteki uwierzytelniania firmy Microsoft obsługują wiele **platform** (JavaScript, .NET Framework, .NET Core, Windows 10/platformy UWP, Xamarin. iOS, Xamarin. Android, Native iOS, Native Android, Java, Python)

|Scenariusz  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplikacje z jedną stroną](scenario-spa-overview.md) <br/>[![Aplikacja jednostronicowa](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js
| [Aplikacja sieci Web, która loguje użytkowników](scenario-web-app-sign-user-overview.md) <br/>[![Aplikacja internetowa, która loguje użytkowników](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png)</br> ASP.NET ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core
| [Aplikacja sieci Web, która wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md) <br/> [![Aplikacja sieci Web, która wywołuje interfejsy API sieci Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png) </br> ASP.NET + MSAL.NET </br> ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Kolba + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Kolba + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Kolba + MSAL Python
| [Aplikacja klasyczna wywołująca internetowe interfejsy API](scenario-desktop-overview.md) <br/> [Aplikacja klasyczna, która wywołuje przepływ kodu urządzenia interfejsów API sieci Web ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![](media/scenarios/device-code-flow-app.svg) | ![MSAL.NET](media/sample-v2-code/logo_NET.png)  MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [Aplikacja mobilna, która wywołuje interfejsy API sieci Web](scenario-mobile-overview.md) <br/> [![Aplikacja mobilna, która wywołuje interfejsy API sieci Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![Platforma UWP](media/sample-v2-code/logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/logo_xamarin.png) MSAL.NET | | | ![iOS/cel C lub SWIFT](media/sample-v2-code/logo_iOS.png) MSAL. iOS | ![Android](media/sample-v2-code/logo_Android.png) MSAL. Systemów
| [Aplikacja demona](scenario-daemon-overview.md) <br/> [![Aplikacja demona](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [Internetowy interfejs API, który wywołuje interfejsy API sieci Web](scenario-web-api-call-api-overview.md) <br/> [![Internetowy interfejs API, który wywołuje interfejsy API sieci Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) <br/> ASP.NET + MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core i MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core i MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png)<br/> ASP.NET Core i MSAL.NET

Zobacz również [biblioteki obsługiwane przez firmę Microsoft według systemu operacyjnego/języka](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [podstaw uwierzytelniania](authentication-scenarios.md) i [tokenów dostępu](access-tokens.md).
