---
title: Przykłady kodu platformy tożsamości firmy Microsoft | Dokumentacja firmy Microsoft
description: Dostarcza indeks tożsamości firmy Microsoft dostępne przykłady kodu platformy (punktu końcowego v2.0), uporządkowane według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceae71766c3e51a84cd09e8ac88740353e783bea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482512"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Przykłady kodu platformy tożsamości firmy Microsoft (punktu końcowego v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Możesz użyć platformy tożsamości firmy Microsoft, aby:

- Dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsy API sieci web.
- Wymagaj tokenu dostępu do uzyskania dostępu do chronionego internetowego interfejsu API.

W tym artykule krótko opisano oraz zawiera łącza do przykładów dla punktu końcowego platformy tożsamości firmy Microsoft. Te przykłady pokazują, jak to zrobić i udostępniają fragmenty kodu, których można używać w aplikacjach. Na stronie przykładowy kod znajdziesz szczegółowe readme tematów, w których pomoc dotycząca wymagania, instalacji i konfiguracji. Komentarze w kodzie ułatwiają zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię wersja 1.0 przykłady, zobacz [przykładów kodu usługi Azure AD (punkt końcowy w wersji 1.0)](sample-v1-code.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [typów aplikacji dla programu Microsoft platformy tożsamości z punktu końcowego](v2-app-types.md).

Ponadto można przekazywać przykłady w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentację](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplikacje jednej strony

Te przykłady przedstawiają sposób pisania aplikacji jednej strony, zabezpieczone z platformą Microsoft identity. Te przykłady, użyj jednej z odmian MSAL.js.

| Platforma | Opis | Łącze |
| -------- | --------------------- | -------- |
| ![Ta ilustracja przedstawia JavaScript logo](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Wywołania programu Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Ta ilustracja przedstawia JavaScript logo](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Calls B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Ta ilustracja przedstawia JavaScript logo](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Własnych wywołań interfejsu API sieci web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Ta ilustracja przedstawia logo szablonów Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Wywołania programu Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Ta ilustracja przedstawia logo szablonów Angular](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Wywołania programu Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Aplikacje internetowe

Poniższe przykłady ilustrują, aplikacje sieci web, które logują się użytkownicy. Niektóre przykłady pokazują również aplikacji wywołanie programu Microsoft Graph lub własnego interfejsu API sieci web przy użyciu tożsamości użytkownika.

| Platforma | Użytkownikom zalogowanie się tylko | Użytkownikom zalogowanie się i wymaga programu Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Ta ilustracja przedstawia logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Samouczek logowania użytkowników podstawowych aplikacji sieci Web platformy ASP.NET](https://aka.ms/aspnetcore-webapp-sign-in) | Przykładowe takie same w [aplikację sieci Web platformy ASP.NET Core wymaga programu Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fazy |
| ![Ta ilustracja przedstawia ASP.NET logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Przewodnik Szybki Start platformy ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Ta ilustracja przedstawia logo środowiska Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Przewodnik Szybki Start platformy node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Ta ilustracja przedstawia logo języka Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplikacje komputerowe i mobilne publicznych klienta

Poniższe przykłady pokazują publicznych klienta aplikacji (komputerze lub urządzeniu przenośnym aplikacje), uzyskujących dostęp do interfejsu API programu Microsoft Graph lub własnego interfejsu API sieci web nazwę użytkownika. Wszystkie te aplikacje klienckie korzystają Microsoft Authentication Library (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołania internetowego interfejsu API platformy ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Pulpitu (WPF)      | ![Ta ilustracja przedstawia .NET /C# logo](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Pulpitu (Konsola)   | ![Ta ilustracja przedstawia .NET /C# logo (komputer stacjonarny)](media/sample-v2-code/logo_NET.png) | [Uwierzytelnianie zintegrowane Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Pulpitu (Konsola)   | ![Ta ilustracja przedstawia .NET /C# logo (komputer stacjonarny)](media/sample-v2-code/logo_NETcore.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobile (Android, iOS, UWP)   | ![Ta ilustracja przedstawia .NET /C# logo (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Ten obraz zawiera dla systemu iOS/Objective-C lub Swift.](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Mobile (Android)   | ![Ta ilustracja przedstawia logo systemu Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Aplikacje demona

Poniższe przykłady pokazują aplikację, która uzyskuje dostęp do interfejsu API programu Microsoft Graph przy użyciu własnej tożsamości (nie użytkownika).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsola | ![Ta ilustracja przedstawia logo platformy .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplikacja internetowa | ![Ta ilustracja przedstawia ASP.NET logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Bezobsługowe aplikacji

Poniższy przykład pokazuje aplikacji publicznych klienta, na urządzeniu bez przeglądarki sieci web. Aplikacja może być narzędziem wiersza polecenia aplikacji uruchomionej w systemie Linux lub Mac lub aplikacji IoT. Przykład funkcji aplikacji dostęp do interfejsu API Microsoft Graph, nazwę użytkownika, który logowania interakcyjnego na innym urządzeniu (np. telefonu komórkowego). Ta aplikacja kliencka używa Microsoft Authentication Library (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Pulpitu (Konsola)   | ![Ta ilustracja przedstawia .NET /C# logo (komputer stacjonarny)](media/sample-v2-code/logo_NETcore.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Interfejsy API sieci Web

Poniższe przykłady pokazują, jak chronić internetowy interfejs API z punktem końcowym platforma tożsamości firmy Microsoft i jak wywoływanie podrzędnego interfejsu API z interfejsu API sieci web.

| Platforma | Sample |
| -------- | ------------------- |
| ![Ta ilustracja przedstawia logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | Platforma ASP.NET Core interfejsu API sieci web (usługa) z [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ta ilustracja przedstawia ASP.NET logo](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Interfejs API (usługa) dla sieci Web [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Inne przykłady z programu Microsoft Graph

Aby dowiedzieć się więcej na temat [przykłady](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczków, które przedstawiają różnych wzorców użycia dla interfejsu API programu Graph firmy Microsoft, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [przykłady przygotowane przez społeczność programu Microsoft Graph i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

- [Przewodnik dewelopera usługi Azure Active Directory (w wersji 1.0)](v1-overview.md)
- [Funkcji Azure AD Graph pojęć związanych z interfejsu API i dokumentacja](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Biblioteka pomocnicza interfejsu API programu Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
