---
title: Przykłady kodu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dostarcza indeks usługi Azure Active Directory (punkt końcowy w wersji 1) przykładów kodu, uporządkowane według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bd71124e9134fc4d5692bb3b95a1673f111ccbde
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444389"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Przykłady kodu usługi Azure Active Directory (punkt końcowy V1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) umożliwia dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsów API sieci web.

Ta sekcja zawiera łącza do przykładów, których można użyć, aby dowiedzieć się więcej na temat punktu końcowego usługi Azure AD w wersji 1. Te przykłady pokazują, jak to się robi wraz z fragmentów kodu, których można używać w aplikacjach. Na stronie kod przykładowy można znaleźć szczegółowe odczytu-mnie tematy, które pomagają wymagań, instalacji i konfiguracji. I kod jest ujęty w ułatwiające zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię przykładów kodu usługi Azure AD w wersji 2, zobacz [przykłady kodu w wersji 2.0 według scenariusza](active-directory-v2-code-samples.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).

Również może przyczynić się do naszych przykładów w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentację](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplikacje komputerowe i mobilne publicznych klienta wywołanie programu Microsoft Graph lub interfejs API sieci Web

Poniższe przykłady pokazują publicznych klienta aplikacji (aplikacji desktop/mobile), które dostęp do programu Microsoft Graph i interfejsu API sieci Web, nazwę użytkownika.

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołuje interfejs API platformy ASP.NET lub sieci Web platformy ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Pulpitu (WPF)           | .NET / C# | Interaktywne | [multitarget-DotNet-native](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Polecenia DotNet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [aspnetcore-DotNet-native](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet webapi-manual-jwt weryfikacji](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Telefon komórkowy (UWP)            | .NET / C#  | Interaktywne | [polecenia DotNet-native-platformy uniwersalnej systemu Windows — wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [polecenia DotNet ze sklepu windows](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (pojedyncza dzierżawa interfejsu API sieci Web) </p> [DotNet-webapi wielodostępnej — — sklep windows](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (z wieloma dzierżawami interfejsu API sieci Web)|
Telefon komórkowy (Android, iOS i platformy uniwersalnej systemu Windows)   | .NET / C# (Xamarin) | Interaktywne | [multitarget-DotNet-native](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Telefon komórkowy (Android)           | Android/Java | Interaktywne |   [System android](https://github.com/Azure-Samples/active-directory-android) |
Telefon komórkowy (iOS)           | dla systemu iOS/Objective C | Interaktywne |   [nativeClient iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Pulpitu (Konsola)          | .NET / C# | Nazwa użytkownika / hasło </p> Zintegrowane uwierzytelnianie systemu Windows | | [polecenia DotNet-native bezobsługowego](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Pulpitu (Konsola)           | .NET core / C# | Profil urządzenia | | [DotNet deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Aplikacje sieci Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplikacje sieci Web logowania użytkowników, wywołanie programu Microsoft Graph lub interfejsu API sieci Web, przy użyciu tożsamości użytkownika

 Platforma | Użytkownikom zalogowanie się tylko | Wywołania programu Microsoft Graph lub programu AAD Graph| Wywołuje innego ASP.NET lub ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [Aplikacja sieci Web openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [Aplikacja sieci Web WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet-aplikacji sieci Web — wielodostępna openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
Platforma ASP.NET Core 2.0 | [polecenia DotNet-aplikacji sieci Web openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [Aplikacja sieci Web webapi wielodostępnej openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [polecenia DotNet-aplikacji sieci Web webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [polecenia DotNet-aplikacji sieci Web webapi-oauth2-tożsamości użytkownika](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [graphapi-Python-aplikacji sieci Web](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [Aplikacja sieci Web Java-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [sieci web graphapi PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplikacje sieci Web, prezentacja kontroli dostępu opartej na rolach (autoryzacja)

Poniższe przykłady pokazują sposób implementacji kontroli dostępu opartej na rolach, która jest używana do ograniczania uprawnienia określonymi funkcjami aplikacji sieci web w taki sposób, aby niektórych użytkowników. Użytkownicy są autoryzowane w zależności od tego, czy należą do roli lub grupy usługi Azure AD.

Platforma | Sample | Opis
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [groupclaims-DotNet-aplikacji sieci Web](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Aplikacja sieci web platformy .NET 4.5 MVC używa usługi Azure AD **grup** autoryzacji
ASP.NET 4.5 | [roleclaims-DotNet-aplikacji sieci Web](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Aplikacja sieci web platformy .NET 4.5 MVC używa usługi Azure AD **role** autoryzacji

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Demon aplikacji (dostęp do interfejsów API sieci Web przy użyciu tożsamości aplikacji)

Poniższe przykłady pokazują pulpitu lub aplikacji sieci web, do których dostęp internetowy interfejs API bez użytkownika (przy użyciu tożsamości aplikacji) lub Microsoft Graph.

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołuje interfejs API platformy ASP.NET lub sieci Web platformy ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Demon aplikacji (Konsola)          | .NET / C#  | Poświadczenia klienta za pomocą klucza tajnego aplikacji lub certyfikat | | [demon DotNet](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [polecenia DotNet demona certyfikatu credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Demon aplikacji (Konsola)         | .NET core / C# | Poświadczenia klienta przy użyciu certyfikatu| | [dotnetcore demona certyfikatu credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Klasyczna            | Java | Poświadczenia klienta |   [Java-native bezobsługowego](https://github.com/azure-samples/active-directory-java-native-headless) |
Aplikacja sieci Web platformy ASP.NET  | .NET / C# | Poświadczenia klienta |    | [polecenia DotNet-aplikacji sieci Web webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Interfejsy API sieci Web

### <a name="web-api-protected-by-azure-active-directory"></a>Internetowy interfejs API chroniony przez usługę Azure Active Directory

Poniższy przykład pokazuje, jak zapewnić ochronę środowiska node.js interfejsu API sieci web z usługą Azure AD.

Platforma | Sample | Opis
 -------- | ------------------- | ---------------------
Node.js | [węzeł webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  NodeJS internetowego interfejsu API, która jest zabezpieczony za pomocą usługi Azure AD i tokenów dostępu protokołu OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Wywołanie programu Microsoft Graph lub innego interfejsu API sieci Web interfejsu API sieci Web

Poniższe przykłady pokazują, internetowy interfejs API, który wywołuje innego interfejsu API sieci web. Drugi przykład pokazuje sposób obsługi dostępu warunkowego.

 Platforma |  Wywołania programu Microsoft Graph | Wywołuje innego ASP.NET lub ASP.NET Core 2.0 Web API
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [polecenia DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[polecenia DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [polecenia DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[polecenia DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Ten przykład pokazuje sposób pisania aplikacji jednostronicowej zabezpieczony za pomocą usługi Azure AD.

 Platforma |  Wywołania programu Microsoft Graph | Wywołuje jej własnego interfejsu API | Wywołuje innego interfejsu API sieci Web
 -------- |  --------------------- | ------------------ | ----------------
JavaScript / ASP.NET 4.x |  | [singlepageapp języka JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) / ASP.NET 4.x |  | [angularjs singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) / ASP.NET 4.x |  |  | [Moduł angularjs-singlepageapp-mechanizmu cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Inne przykłady z programu Microsoft Graph

Aby uzyskać przykłady i samouczki, które przedstawiają różnych wzorców użycia dla interfejsu API programu Graph firmy Microsoft, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [przykłady przygotowane przez społeczność usługi Microsoft Graph i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

[Przewodnik dewelopera usługi Azure Active Directory](azure-ad-developers-guide.md)

[Funkcji Azure AD Graph pojęć związanych z interfejsu API i dokumentacja](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteka pomocnicza interfejsu API programu Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
