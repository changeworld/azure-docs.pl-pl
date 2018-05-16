---
title: Przykłady kodu w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zawiera przykłady kodu, uporządkowane według scenariusza indeksu dostępne usługi Azure Active Directory (punkt końcowy V2).
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
ms.openlocfilehash: b7a894ccd27ddcda2ab4b98c69333d37de077863
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Przykłady kodu w usłudze Azure Active Directory (punkt końcowy V2)

Microsoft Azure Active Directory (Azure AD) można użyć do:

- Dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsów API sieci web.
- Wymagają tokenu dostępu do dostępu interfejs API sieci web chronionych.

W tym artykule krótko opisano oraz zawiera łącza do przykładów dla punktu końcowego usługi Azure AD w wersji 2. Te przykłady pokazują, jak jego jest wykonywane, wraz z wstawki kodu, których można użyć w aplikacji. Na stronie przykładowy kod znajdują się szczegółowe readme tematów, w których pomoc wymagania instalacji i konfigurowania. Komentarze w kodzie istnieją ułatwią zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię V1 przykłady, zobacz [przykłady kodu usługi Azure AD (punkt końcowy V1)](active-directory-code-samples.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory](active-directory-v2-flows.md).

Można także współtworzyć próbek w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentacja](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Aplikacje klienta publicznego wersje Desktop i mobile

Poniższe przykłady Pokaż publicznego klienta aplikacje (aplikacje desktop/mobile), które uzyskują dostęp do programu Microsoft Graph i interfejsu API sieci Web, nazwę użytkownika.

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołuje składnik Web API platformy ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Pulpit (WPF)      | .NET / C#  | Interaktywne | [DotNet pulpitu msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin ograniczone — zakresy v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobile (UWP)   | .NET / C# (UWP) | Interaktywne | [DotNet-native-platformy uniwersalnej systemu Windows v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobile (Android, iOS, platformy uniwersalnej systemu Windows)   | .NET / C# (Xamarin) | Interaktywne | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | iOS / Objective C lub swift. | Interaktywne | [IOS — swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS — native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobile (Android)   | Android / Java | Interaktywne |   [android — native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Aplikacje sieci Web

Poniższe przykłady przedstawiają aplikacji sieci web, które Zaloguj użytkowników, wywołanie Microsoft Graph lub wywołanie interfejsu API sieci web o tożsamości użytkownika.

 Platforma | Użytkownikom zalogowanie się tylko | Użytkownikom zalogowanie się i wymaga programu Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2 — aplikacji sieci Web openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet — aplikacji sieci Web openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
Platformy ASP.NET Core 2.0 | [aspnetcore — aplikacji sieci Web openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore — Połącz — przykład](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2 — aplikacji sieci Web OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Demon aplikacji

Poniższe przykłady pokazują pulpitu lub sieci web aplikacji, które dostęp do programu Microsoft Graph i interfejsu API sieci web przy użyciu tożsamości aplikacji (nie użytkownika).

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
Aplikacja internetowa | .NET / C#  | Poświadczenia klienta | [DotNet — demon v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Aplikacje jednej strony (SPA)

Ten przykład przedstawia sposób tworzenia aplikacji jednej strony zabezpieczonego za pomocą usługi Azure AD.

 Platforma |  Wywołania programu Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript — graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [dyrektywy angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript — graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + kątowego 4) | [angular4 — Połącz — przykład](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Interfejsy API sieci Web

### <a name="web-api-protected-by-azure-ad"></a>Chronione przez usługę Azure AD interfejsu API sieci Web

Poniższy przykład przedstawia sposób chronić składnika web API z punktem końcowym usługi Azure AD w wersji 2.

Platforma | Sample | Opis
 -------- | ------------------- | ---------------------
Node.js | [DotNet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Wywołanie interfejsu API platformy ASP.NET Core sieci Web z aplikacji WPF przy użyciu usługi Azure AD w wersji 2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Wywoływanie Microsoft Graph lub innego interfejsu API sieci Web interfejsu API sieci Web

W tym przykładzie nie jest jeszcze dostępna.

## <a name="other-microsoft-graph-samples"></a>Inne przykłady Microsoft Graph

Aby dowiedzieć się więcej o [przykłady](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczków demonstrujących wzorców użycia różnych dla interfejsu API Graph firmy Microsoft, łącznie z uwierzytelniania za pomocą usługi Azure AD, zobacz [przykłady Microsoft Graph społeczności i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

[Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API koncepcyjne i odwołania](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteka pomocnik interfejsu API Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
