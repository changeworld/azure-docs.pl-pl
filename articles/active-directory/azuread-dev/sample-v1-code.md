---
title: Przykłady kodu dla usługi Azure Active Directory w wersji 1.0 | Dokumenty firmy Microsoft
description: Zawiera indeks przykładów kodu usługi Azure Active Directory (punktu końcowego w wersji 1.0), uporządkowany według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ae283529abb3b71ee50fc710dd1ebe0d17a12be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154835"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Przykłady kodu usługi Azure Active Directory (punkt końcowy w wersji 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Za pomocą usługi Microsoft Azure Active Directory (Azure AD) można dodać uwierzytelnianie i autoryzację do aplikacji sieci Web i interfejsów API sieci Web.

Ta sekcja zawiera łącza do przykładów, których można użyć, aby dowiedzieć się więcej o punkcie końcowym usługi Azure AD w wersji 1.0. Te przykłady pokazują, jak to zrobić wraz z fragmentami kodu, które można użyć w aplikacjach. Na przykładowej stronie kodu znajdziesz szczegółowe tematy programu read-me, które pomagają w wymaganiach, instalacji i konfiguracji. Kod jest komentowany, aby pomóc Ci zrozumieć krytyczne sekcje.

> [!NOTE]
> Jeśli jesteś zainteresowany przykładami kodu usługi Azure AD V2, zobacz [przykłady kodu wersji 2.0 według scenariusza.](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

Aby zrozumieć podstawowy scenariusz dla każdego przykładowego typu, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD](v1-authentication-scenarios.md).

Możesz również przyczynić się do naszych próbek na GitHub. Aby dowiedzieć się, jak to zrobić, zobacz [Przykłady i dokumentacja usługi Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

W tym przykładzie pokazano, jak napisać jednostronicową aplikację zabezpieczoną za pomocą usługi Azure AD.

 Platforma | Wywołuje własny interfejs API | Wywołuje inny interfejs API sieci Web
 -------- |  --------------------- | ------------------ 
![Ten obraz eksponuje logo JavaScript](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Ten obraz eksploruje logo Angular JS](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Aplikacje internetowe

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplikacje sieci Web logujące się do użytkowników, wywołujące program Microsoft Graph lub interfejs API sieci Web z tożsamością użytkownika

Poniższe przykłady ilustrują użytkowników podpisujących aplikacje sieci Web. Niektóre z tych aplikacji również wywołać Microsoft Graph lub własny interfejs API sieci Web, w imieniu zalogowanego użytkownika.

 Platforma | Tylko logowanie użytkowników | Wywołuje program Microsoft Graph | Wywołuje inny interfejs API sieci Web ASP.NET lub ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![Ten obraz ekspozycjowy przedstawia logo ASP.NET](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(Wykres AAD) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Ten obraz ekspozycjowy przedstawia logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4,5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (Wykres AAD) |
![Ten obraz eksploruje logo Pythona](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Ten obraz przedstawia dziennik Java](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Ten obraz eksploruje logo PHP](media/sample-v2-code/logo-php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplikacje sieci Web demonstrujące kontrolę dostępu opartą na rolach (autoryzacja)

Poniższe przykłady pokazują, jak zaimplementować kontrolę dostępu opartą na rolach (RBAC). RBAC służy do ograniczania uprawnień niektórych funkcji w aplikacji sieci web do niektórych użytkowników. Użytkownicy są autoryzowani w zależności od tego, czy należą do **grupy usługi Azure AD,** czy mają daną **rolę**aplikacji.

Platforma | Sample |
 -------- | ------------------- |
![Ten obraz ekspozycjowy przedstawia logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4,5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Aplikacja sieci Web .NET 4.5 MVC, która używa **ról** usługi Azure AD do autoryzacji

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Publiczne aplikacje klienckie dla komputerów stacjonarnych i urządzeń przenośnych wywołujące program Microsoft Graph lub interfejs API sieci Web

Poniższe przykłady ilustrują publiczne aplikacje klienckie (aplikacje deskto/pmobile), które uzyskują dostęp do programu Microsoft Graph lub interfejsu API sieci Web w imieniu użytkownika. W zależności od urządzeń i platform aplikacje mogą logować się do użytkowników na różne sposoby (przepływy/dotacje):

- Interaktywnie
- Po cichu (ze zintegrowanym uwierzytelnianiem systemu Windows w systemie Windows lub nazwą użytkownika/hasłem)
- Delegując interaktywne logowanie do innego urządzenia (przepływ kodu urządzenia używany na urządzeniach, które nie zapewniają kontrolek sieci Web)

Aplikacja kliencka | Platforma | Przepływ/dotacja | Wywołuje program Microsoft Graph | Wywołuje ASP.NET lub ASP.NET Core 2.x Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
Pulpit (WPF)           | ![Na tym obrazie jest wyświetlane logo .NET/C#](media/sample-v2-code/logo-net.png)  | Interaktywne | Część [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Urządzenia mobilne (UWP)            | .![Ten obraz przedstawia .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interaktywne | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> W tym przykładzie użyto [wam,](/windows/uwp/security/web-account-manager)nie [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplikacja platformy uniwersalnej systemu Windows przy użyciu ADAL.NET do wywołania interfejsu API sieci Web dla jednej dzierżawy) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplikacja platformy uniwersalnej systemu Windows przy użyciu ADAL.NET do wywołania wielodostępnego interfejsu API sieci Web)|
Urządzenia mobilne (Android, iOS, UWP)   | ![Ten obraz przedstawia .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interaktywne | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Urządzenia mobilne (Android)           | ![Ten obraz eksploruje logo Androida](media/sample-v2-code/logo-android.png) | Interaktywne |   [android](https://github.com/Azure-Samples/active-directory-android) |
Urządzenia mobilne (iOS)           | ![Ten obraz ekspozycji iOS / Cel C lub Swift](media/sample-v2-code/logo-ios.png) | Interaktywne |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Pulpit (konsola)          | ![Na tym obrazie jest wyświetlane logo .NET/C#](media/sample-v2-code/logo-net.png) | Nazwa użytkownika / hasło </p>  Zintegrowane uwierzytelnianie systemu Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Pulpit (konsola)          | ![Ten obraz eksploruje logo Java](media/sample-v2-code/logo-java.png) | Nazwa użytkownika / hasło | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Pulpit (konsola)           | ![Na tym obrazie jest wyświetlane logo .NET Core/C#](media/sample-v2-code/logo-netcore.png) | Przepływ kodu urządzenia | | [profil dotnet-device](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplikacje demonów (uzyskiwanie dostępu do internetowych interfejsów API z tożsamością aplikacji)

Poniższe przykłady przedstawiają aplikacje klasyczne lub internetowe, które uzyskują dostęp do programu Microsoft Graph lub internetowego interfejsu API bez użytkownika (z tożsamością aplikacji).

Aplikacja kliencka | Platforma | Przepływ/dotacja | Wywołuje interfejs API sieci Web ASP.NET lub ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
Aplikacja Daemon (konsola)          | ![Na tym obrazie jest wyświetlane logo .NET](media/sample-v2-code/logo-netframework.png) | Poświadczenia klienta z kluczem tajnym lub certyfikatem aplikacji | [dotnet-demon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-demon-certyfikat-poświadczenie](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplikacja Daemon (konsola)         | ![Na tym obrazie jest wyświetlane logo .NET](media/sample-v2-code/logo-netcore.png) | Poświadczenia klienta z certyfikatem| [dotnetcore-demon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET aplikacja sieci Web  | ![Na tym obrazie jest wyświetlane logo .NET](media/sample-v2-code/logo-netframework.png) | Poświadczenia klienta | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Interfejsy API sieci Web

### <a name="web-api-protected-by-azure-active-directory"></a>Internetowy interfejs API chroniony przez usługę Azure Active Directory

W poniższym przykładzie pokazano, jak chronić internetowy interfejs API node.js za pomocą usługi Azure AD.

W poprzednich sekcjach tego artykułu można również znaleźć inne przykłady ilustrujące aplikację kliencką **wywołującą** ASP.NET lub ASP.NET Core **Web API**. Te próbki nie są wymienione ponownie w tej sekcji, ale znajdziesz je w ostatniej kolumnie tabel powyżej lub poniżej

| Platforma | Sample |
|--------|-------------------|
| ![Na tym obrazie jest wyświetlane logo Node.js](media/sample-v2-code/logo-nodejs.png)  | [węzeł-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Internetowy interfejs API wywołujący program Microsoft Graph lub inny interfejs API sieci Web

W poniższych przykładach pokazano internetowy interfejs API, który wywołuje inny internetowy interfejs API. W drugim przykładzie pokazano, jak obsługiwać dostęp warunkowy.

| Platforma |  Wywołuje program Microsoft Graph | Wywołuje inny interfejs API sieci Web ASP.NET lub ASP.NET Core 2.0 |
| -------- |  --------------------- | ------------------------- |
| ![Ten obraz ekspozycjowy przedstawia logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4,5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Inne przykłady programu Microsoft Graph

Aby uzyskać przykłady i samouczki, które pokazują różne wzorce użycia interfejsu API programu Microsoft Graph, w tym uwierzytelnianie za pomocą usługi Azure AD, zobacz [Przykłady & samouczki społeczności programu Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dla deweloperów usługi Azure Active Directory](v1-overview.md)
- [Biblioteki uwierzytelniania usługi Azure Active Directory](active-directory-authentication-libraries.md)
- [Koncepcyjne i referencyjne interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)
