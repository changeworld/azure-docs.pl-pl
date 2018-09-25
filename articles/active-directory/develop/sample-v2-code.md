---
title: Przykłady kodu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dostarcza indeks dostępne usługi Azure Active Directory (punkt końcowy w wersji 2), przykłady kodu, uporządkowane według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e76097c0d0cbaf14f2fc2b1a407bc2d320a2091d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964410"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Przykłady kodu usługi Azure Active Directory (punktu końcowego V2)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Microsoft Azure Active Directory (Azure AD) służy do:

- Dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsy API sieci web.
- Wymagaj tokenu dostępu do uzyskania dostępu do chronionego internetowego interfejsu API.

W tym artykule krótko opisano oraz zawiera łącza do przykładów dla punktu końcowego usługi Azure AD w wersji 2. Te przykłady pokazują, jak to się robi, wraz z fragmentów kodu, których można używać w aplikacjach. Na stronie przykładowy kod znajdziesz szczegółowe readme tematów, w których pomocy z wymaganiami, instalacji i konfigurowania. Komentarze w kodzie, istnieją ułatwiające zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię V1 przykładów, zobacz [przykładów kodu usługi Azure AD (punkt końcowy w wersji 1)](sample-v1-code.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory](v2-app-types.md).

Ponadto można przekazywać przykłady w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentację](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Aplikacji jednostronicowej (SPA)

W tym przykładzie pokazano, jak napisać aplikację jednostronicowych zabezpieczony za pomocą usługi Azure AD. Te przykłady, użyj jednej z odmian MSAL.js: [Biblioteka Microsoft Authentication Library dla języka JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core), [Biblioteka Microsoft Authentication Library dla platformy Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular), [Biblioteka Microsoft Authentication Library bibliotekę angularjs](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 Platforma |  Wywołania programu Microsoft Graph
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [JavaScript — graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (msal AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (msal Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Aplikacje internetowe

Poniższe przykłady ilustrują, aplikacje sieci web, które logują się użytkownicy. Niektóre przykłady pokazują również aplikacji wywołanie programu Microsoft Graph lub własnego interfejsu API sieci web przy użyciu tożsamości użytkownika.

 Platforma | Użytkownikom zalogowanie się tylko | Użytkownikom zalogowanie się i wymaga programu Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [polecenia DotNet-aplikacji sieci Web — openidconnect — v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>Platforma ASP.NET Core 2.0 | [aspnetcore-aplikacji sieci Web — openidconnect — v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2-aplikacji sieci Web — OpenIDConnect — nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>Aplikacje komputerowe i mobilne publicznych klienta

Poniższe przykłady pokazują publicznych klienta aplikacji (aplikacji desktop/mobile), uzyskujących dostęp do programu Microsoft Graph lub własnego interfejsu API sieci Web, nazwę użytkownika, za pomocą logowania interaktywnego. Wszystkie te aplikacje klienckie korzystają bibliotek uwierzytelniania firmy MicroSoft (MSAL)

Aplikacja kliencka | Platforma | Wywołania programu Microsoft Graph | Wywołania internetowego interfejsu API platformy ASP.NET Core 2.0
------------------ | -------- |  -------------------- | -------------------------
Pulpitu (WPF)      | ![.NET / C#](media/sample-v2-code/logo_NET.png) | [DotNet-desktop-msgraph — v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin ograniczone — zakresy — v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Telefon komórkowy (UWP)   | ![.NET / C# (UWP)](media/sample-v2-code/logo_windows.png) | [polecenia DotNet-native-platformy uniwersalnej systemu Windows — v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Telefon komórkowy (Android, iOS i platformy uniwersalnej systemu Windows)   | ![.NET / C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Telefon komórkowy (iOS)       | ![dla systemu iOS / Objective C lub swift.](media/sample-v2-code/logo_iOS.png) | [IOS — swift-native — v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS-native-nxoauth2 — v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Telefon komórkowy (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) |   [android — native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Aplikacje demona

Następujące przykładowe aplikację, która uzyskuje dostęp do programu Microsoft Graph przy użyciu własnej tożsamości (nie użytkownika).

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph
------------------ | -------- | ---------- | --------------------
Aplikacja internetowa | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Poświadczenia klienta | [demon DotNet-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

> Przykład przedstawiający aplikację pulpitu demon jest na liście prac.

## <a name="web-apis"></a>Interfejsy API sieci Web

Poniższy przykład pokazuje, jak chronić internetowy interfejs API z punktem końcowym usługi Azure AD w wersji 2. Ten interfejs API jest wykonywane przez aplikację WPF (ale naprawdę może być wywoływana przez dowolną aplikację)

Platforma | Sample
 -------- | -------------------
![.NET / C#](media/sample-v2-code/logo_NET.png) | [DotNet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Inne przykłady z programu Microsoft Graph

Aby dowiedzieć się więcej na temat [przykłady](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczków, które przedstawiają różnych wzorców użycia dla interfejsu API programu Graph firmy Microsoft, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [przykłady przygotowane przez społeczność programu Microsoft Graph i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

[Przewodnik dewelopera usługi Azure Active Directory](azure-ad-developers-guide.md)

[Funkcji Azure AD Graph pojęć związanych z interfejsu API i dokumentacja](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteka pomocnicza interfejsu API programu Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
