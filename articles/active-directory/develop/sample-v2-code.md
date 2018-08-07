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
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8d2ce5bb2f44d9e21fb12e96f9c68d4ecc0fc501
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581560"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Przykłady kodu usługi Azure Active Directory (punktu końcowego V2)

Microsoft Azure Active Directory (Azure AD) służy do:

- Dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsy API sieci web.
- Wymagaj tokenu dostępu do uzyskania dostępu do chronionego internetowego interfejsu API.

W tym artykule krótko opisano oraz zawiera łącza do przykładów dla punktu końcowego usługi Azure AD w wersji 2. Te przykłady pokazują, jak to się robi, wraz z fragmentów kodu, których można używać w aplikacjach. Na stronie przykładowy kod znajdziesz szczegółowe readme tematów, w których pomocy z wymaganiami, instalacji i konfigurowania. Komentarze w kodzie, istnieją ułatwiające zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię V1 przykładów, zobacz [przykładów kodu usługi Azure AD (punkt końcowy w wersji 1)](sample-v1-code.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory](active-directory-v2-flows.md).

Ponadto można przekazywać przykłady w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentację](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Aplikacje komputerowe i mobilne publicznych klienta

Poniższe przykłady pokazują publicznych klienta aplikacji (aplikacji desktop/mobile), które dostęp do programu Microsoft Graph i interfejsu API sieci Web, nazwę użytkownika.

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołania internetowego interfejsu API platformy ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Pulpitu (WPF)      | .NET / C#  | Interaktywne | [DotNet-desktop-msgraph — v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin ograniczone — zakresy — v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Telefon komórkowy (UWP)   | .NET / C# (UWP) | Interaktywne | [polecenia DotNet-native-platformy uniwersalnej systemu Windows — v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Telefon komórkowy (Android, iOS i platformy uniwersalnej systemu Windows)   | .NET / C# (Xamarin) | Interaktywne | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Telefon komórkowy (iOS)       | dla systemu iOS / Objective C lub swift. | Interaktywne | [IOS — swift-native — v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS-native-nxoauth2 — v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Telefon komórkowy (Android)   | Android / Java | Interaktywne |   [android — native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Aplikacje internetowe

Poniższe przykłady ilustrują, aplikacje sieci web, takich jak logowanie użytkowników, wywoływanie programu Microsoft Graph lub wywoływanie internetowego interfejsu API przy użyciu tożsamości użytkownika.

 Platforma | Użytkownikom zalogowanie się tylko | Użytkownikom zalogowanie się i wymaga programu Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [polecenia DotNet-aplikacji sieci Web — openidconnect — v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
Platforma ASP.NET Core 2.0 | [aspnetcore-aplikacji sieci Web — openidconnect — v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-aplikacji sieci Web — OpenIDConnect — nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Aplikacje demona

Poniższe przykłady pokazują pulpitu lub aplikacji sieci web, do których dostęp internetowy interfejs API przy użyciu tożsamości aplikacji (nie użytkownika) lub Microsoft Graph.

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
Aplikacja internetowa | .NET / C#  | Poświadczenia klienta | [demon DotNet-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Aplikacji jednostronicowej (SPA)

Ten przykład pokazuje sposób pisania aplikacji jednostronicowej zabezpieczony za pomocą usługi Azure AD.

 Platforma |  Wywołania programu Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript — graphapi — v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [platformy angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript — graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4-connect-sample](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Interfejsy API sieci Web

### <a name="web-api-protected-by-azure-ad"></a>Internetowy interfejs API chroniony przez usługę Azure AD

Poniższy przykład pokazuje, jak chronić internetowy interfejs API z punktem końcowym usługi Azure AD w wersji 2.

Platforma | Sample | Opis
 -------- | ------------------- | ---------------------
Node.js | [DotNet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Wywołania interfejsu API sieci Web programu ASP.NET Core z aplikacji WPF przy użyciu usługi Azure AD w wersji 2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Wywołanie programu Microsoft Graph lub innego interfejsu API sieci Web interfejsu API sieci Web

W tym przykładzie nie jest jeszcze dostępna.

## <a name="other-microsoft-graph-samples"></a>Inne przykłady z programu Microsoft Graph

Aby dowiedzieć się więcej na temat [przykłady](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczków, które przedstawiają różnych wzorców użycia dla interfejsu API programu Graph firmy Microsoft, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [przykłady przygotowane przez społeczność programu Microsoft Graph i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

[Przewodnik dewelopera usługi Azure Active Directory](azure-ad-developers-guide.md)

[Funkcji Azure AD Graph pojęć związanych z interfejsu API i dokumentacja](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteka pomocnicza interfejsu API programu Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
