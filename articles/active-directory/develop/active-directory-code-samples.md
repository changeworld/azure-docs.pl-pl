---
title: Przykłady kodu w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zawiera przykłady kodu, uporządkowane według scenariusza indeksu usługi Azure Active Directory (punkt końcowy v1).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 046d633239b09ebdf21b5383b08684d81199d026
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Przykłady kodu w usłudze Azure Active Directory (punkt końcowy V1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) umożliwia dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsów API sieci web.

Ta sekcja zawiera łącza do przykłady, których można użyć, aby dowiedzieć się więcej na temat punktu końcowego usługi Azure AD w wersji 1. Te przykłady pokazują, jak jest wykonywane wraz z wstawki kodu, których można użyć w aplikacji. Na stronie przykładowy kod znajdują się szczegółowe odczytu-mnie tematy ułatwiające wymagania dotyczące instalacji i konfiguracji. I kod jest oznaczone jako ułatwią zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię przykłady kodu usługi Azure AD w wersji 2, zobacz [v2.0 przykłady kodu w scenariuszu](active-directory-v2-code-samples.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).

Można także współtworzyć nasze przykłady w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentacja](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplikacje klienta publicznego wersje Desktop i mobile wywołanie Microsoft Graph lub interfejs API sieci Web

Poniższe przykłady Pokaż publicznego klienta aplikacje (aplikacje desktop/mobile), które uzyskują dostęp do programu Microsoft Graph i interfejsu API sieci Web, nazwę użytkownika.

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołuje interfejs API programu ASP.NET lub sieci Web platformy ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Pulpit (WPF)           | .NET / C# | Interaktywne | [DotNet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet native pulpitu](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet webapi — Podręcznik jwt weryfikacji](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .NET / C#  | Interaktywne | [DotNet-native-platformy uniwersalnej systemu Windows wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [DotNet windows magazynu](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (pojedynczej dzierżawy interfejsu API sieci Web) </p> [DotNet webapi wielodostępnej windows magazynu](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (wielodostępne interfejsu API sieci Web)|
Mobile (Android, iOS, platformy uniwersalnej systemu Windows)   | .NET / C# (Xamarin) | Interaktywne | [DotNet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | Android/Java | Interaktywne |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | iOS/Objective C | Interaktywne |   [nativeClient iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Pulpit (Konsola)          | .NET / C# | Nazwa użytkownika / hasło </p> Zintegrowane uwierzytelnianie systemu Windows | | [DotNet-native bezobsługowe](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Pulpit (Konsola)           | Oprogramowanie .NET core / C# | Profil urządzenia | | [DotNet deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Aplikacje sieci Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplikacje sieci Web logowanie użytkowników, wywołanie Microsoft Graph lub interfejs API sieci Web o tożsamości użytkownika

 Platforma | Użytkownikom zalogowanie się tylko | Wywołania programu Microsoft Graph lub Graph usługi AAD| Wywołuje inny ASP.NET lub interfejsu API platformy ASP.NET Core 2.0 sieci Web
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [Aplikacja sieci Web openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [Aplikacja sieci Web WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet — aplikacji sieci Web wielodostępnej openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (wykres AAD) |
Platformy ASP.NET Core 2.0 | [DotNet — aplikacji sieci Web openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [Aplikacja sieci Web webapi wielodostępnej openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (wykres AAD) | [DotNet — aplikacji sieci Web webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [DotNet — aplikacji sieci Web webapi-oauth2-tożsamości użytkownika](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [Python-aplikacji sieci Web graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [openidconnect-Java-aplikacji sieci Web](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [PHP graphapi sieci web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplikacje sieci Web prezentacja kontroli dostępu opartej na rolach (autoryzacja)

Poniższe przykłady pokazują, jak wdrażanie kontroli dostępu opartej na rolach, który jest używany do ograniczania uprawnień określonych funkcji aplikacji sieci web do określonych użytkowników. Użytkownicy są autoryzowane w zależności od tego, czy należą do grupy usługi Azure AD lub roli.

Platforma | Sample | Opis
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [groupclaims — aplikacji sieci Web DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Aplikacja sieci web .NET 4.5 MVC, który używa usługi Azure AD **grup** autoryzacji
ASP.NET 4.5 | [roleclaims — aplikacji sieci Web DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Aplikacja sieci web .NET 4.5 MVC, który używa usługi Azure AD **ról** autoryzacji

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Demon aplikacji (Uzyskiwanie dostępu do interfejsów API sieci Web z tożsamości aplikacji)

Poniższe przykłady pokazują pulpitu lub sieci web aplikacji, które uzyskują dostęp do programu Microsoft Graph lub składnika web API z nie użytkowników (przy użyciu tożsamości aplikacji).

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołuje interfejs API programu ASP.NET lub sieci Web platformy ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Demon aplikacji (Konsola)          | .NET / C#  | Poświadczenia klienta z klucz tajny aplikacji lub certyfikat | | [demon DotNet](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet demon certyfikat credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Demon aplikacji (Konsola)         | Oprogramowanie .NET core / C# | Poświadczenia klienta z certyfikatem| | [dotnetcore demon certyfikat credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Klasyczna            | Java | Poświadczenia klienta |   [Java-native bezobsługowe](https://github.com/azure-samples/active-directory-java-native-headless) |
Aplikacja sieci Web ASP.NET  | .NET / C# | Poświadczenia klienta |    | [DotNet — aplikacji sieci Web webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Interfejsy API sieci Web

### <a name="web-api-protected-by-azure-active-directory"></a>Chronione przez usługę Azure Active Directory interfejsu API sieci Web

Poniższy przykład przedstawia sposób ochrony sieci web node.js interfejsu API za pomocą usługi Azure AD.

Platforma | Sample | Opis
 -------- | ------------------- | ---------------------
Node.js | [webapi węzła](https://github.com/Azure-Samples/active-directory-node-webapi) |  Interfejs API, który jest zabezpieczone przy użyciu usługi Azure AD sieci web NodeJS i tokenów dostępu protokołu OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Wywoływanie Microsoft Graph lub innego interfejsu API sieci Web interfejsu API sieci Web

Poniższe przykłady pokazują, web API, który wywołuje innego interfejsu API sieci web. Drugi przykład przedstawia sposób obsługi dostępu warunkowego.

 Platforma |  Wywołania programu Microsoft Graph | Wywołuje inny ASP.NET lub interfejsu API platformy ASP.NET Core 2.0 sieci Web
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Aplikacje jednej strony

Ten przykład przedstawia sposób tworzenia aplikacji jednej strony zabezpieczonego za pomocą usługi Azure AD.

 Platforma |  Wywołania programu Microsoft Graph | Wywołuje własnego interfejsu API
 -------- |  --------------------- | -------------------------
JavaScript (kątowego) / ASP.NET 4.x |  | [angularjs singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>Inne przykłady Microsoft Graph

Aby uzyskać przykłady i samouczki, które przedstawiają wzorców użycia różnych dla interfejsu API Graph firmy Microsoft, łącznie z uwierzytelniania za pomocą usługi Azure AD, zobacz [Microsoft Graph społeczności przykłady i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

[Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API koncepcyjne i odwołania](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteka pomocnik interfejsu API Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
