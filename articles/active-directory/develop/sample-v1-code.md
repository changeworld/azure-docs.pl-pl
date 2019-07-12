---
title: Przykłady kodu w wersji 1.0 w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dostarcza indeks usługi Azure Active Directory (punkt końcowy w wersji 1.0) przykładów kodu, uporządkowane według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca03c8d52de1580b755833dd990d1d7671f372ca
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785695"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Przykłady kodu usługi Azure Active Directory (punkt końcowy w wersji 1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Microsoft Azure Active Directory (Azure AD) umożliwia dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsów API sieci web.

Ta sekcja zawiera łącza do przykładów, których można użyć, aby dowiedzieć się więcej na temat punktu końcowego usługi Azure AD w wersji 1.0. Te przykłady pokazują, jak to się robi wraz z fragmentów kodu, których można używać w aplikacjach. Na stronie kod przykładowy można znaleźć szczegółowe odczytu-mnie tematy, które pomagają wymagań, instalacji i konfiguracji. I kod jest ujęty w ułatwiające zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię przykładów kodu usługi Azure AD w wersji 2, zobacz [przykłady kodu w wersji 2.0 według scenariusza](sample-v2-code.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](authentication-scenarios.md).

Również może przyczynić się do naszych przykładów w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentację](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplikacje jednej strony

W tym przykładzie pokazano, jak napisać aplikację jednostronicowych zabezpieczony za pomocą usługi Azure AD.

 Platforma | Wywołuje jej własnego interfejsu API | Wywołuje innego interfejsu API sieci Web
 -------- |  --------------------- | ------------------ 
![Ta ilustracja przedstawia JavaScript logo](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Ta ilustracja przedstawia logo szablonów Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Aplikacje sieci Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplikacje sieci Web logowania użytkowników, wywołanie programu Microsoft Graph lub interfejsu API sieci Web, przy użyciu tożsamości użytkownika

Poniższe przykłady ilustrują, aplikacje sieci Web logowania użytkowników. Niektóre z tych aplikacji również wywołać program Microsoft Graph lub własnego interfejsu API sieci Web, w imieniu zalogowanego użytkownika.

 Platforma | Użytkownikom zalogowanie się tylko | Wywołania programu Microsoft Graph lub programu AAD Graph| Wywołuje innego ASP.NET lub ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
![Ta ilustracja przedstawia ASP.NET logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Ta ilustracja przedstawia ASP.NET logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [webApp-openidconnect-dotnet](quickstart-v1-aspnet-webapp.md) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![Ta ilustracja przedstawia logo języka Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Ten obraz zawiera dziennik języka Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Ta ilustracja przedstawia PHP logo](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplikacje sieci Web, prezentacja kontroli dostępu opartej na rolach (autoryzacja)

Poniższe przykłady pokazują sposób implementacji kontroli dostępu opartej na rolach (RBAC). RBAC jest używana do Ogranicz uprawnienia do pewnych funkcji w aplikacji sieci web w taki sposób, aby niektórych użytkowników. Użytkownicy są autoryzowane w zależności od tego, czy należą one do **grupy usługi Azure AD** lub danej aplikacji **roli**.

Platforma | Sample |
 -------- | ------------------- |
![Ta ilustracja przedstawia ASP.NET logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Aplikacja sieci web platformy .NET 4.5 MVC używa usługi Azure AD **role** autoryzacji

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplikacje komputerowe i mobilne publicznych klienta wywołanie programu Microsoft Graph lub interfejs API sieci Web

Poniższe przykłady ilustrują aplikacje klientem publicznym (deskto/pmobile aplikacje), które dostęp do programu Microsoft Graph i interfejsu API sieci Web, nazwę użytkownika. W zależności od urządzeń i platform aplikacji można logowania użytkowników na różne sposoby (przepływy/przyznaje):

- Interaktywnie
- Dyskretnie (przy użyciu zintegrowanego uwierzytelniania Windows na Windows lub nazwy użytkownika i hasła)
- Przez delegowanie interaktywnego logowania na innym urządzeniu (przepływ kodu urządzenia używane na urządzeniach, które nie zawierają kontrolki sieci web)

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołuje program ASP.NET lub ASP.NET Core 2.x internetowego interfejsu API
------------------ | -------- | ---------- | -------------------- | -------------------------
Pulpitu (WPF)           | ![Ta ilustracja przedstawia .NET /C# logo](media/sample-v2-code/logo_NET.png)  | Interaktywne | Część [multitarget-dotnet-native](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .![Ta ilustracja przedstawia .NET /C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interaktywne | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> W tym przykładzie użyto [WAM](/windows/uwp/security/web-account-manager), a nie [ADAL.NET](https://aka.ms/adalnet)|  [polecenia DotNet ze sklepu windows](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplikacji platformy uniwersalnej systemu Windows przy użyciu ADAL.NET do wywoływania jednej dzierżawy interfejsu API sieci Web) </p> [DotNet-webapi wielodostępnej — — sklep windows](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplikacji platformy uniwersalnej systemu Windows przy użyciu ADAL.NET do wywołania z wieloma dzierżawami interfejsu API sieci Web)|
Mobile (Android, iOS, UWP)   | ![Ta ilustracja przedstawia .NET /C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktywne | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Ta ilustracja przedstawia logo systemu Android](media/sample-v2-code/logo_Android.png) | Interaktywne |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | ![Ta ilustracja przedstawia dla systemu iOS / Objective C lub Swift.](media/sample-v2-code/logo_iOS.png) | Interaktywne |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Pulpitu (Konsola)          | ![Ta ilustracja przedstawia .NET /C# logo](media/sample-v2-code/logo_NET.png) | Nazwa użytkownika / hasło </p>  Uwierzytelnianie zintegrowane Windows | | [polecenia DotNet-native bezobsługowego](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Pulpitu (Konsola)          | ![Ta ilustracja przedstawia logo języka Java](media/sample-v2-code/logo_Java.png) | Nazwa użytkownika / hasło | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Pulpitu (Konsola)           | ![Ta ilustracja przedstawia platformy .NET Core /C# logo](media/sample-v2-code/logo_NETcore.png) | Przepływ kodu urządzenia | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Demon aplikacji (dostęp do interfejsów API sieci web przy użyciu tożsamości aplikacji)

Poniższe przykłady pokazują pulpitu lub aplikacji sieci web, do których dostęp internetowy interfejs API bez użytkownika (przy użyciu tożsamości aplikacji) lub Microsoft Graph.

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołuje interfejs API platformy ASP.NET lub sieci Web platformy ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
Demon aplikacji (Konsola)          | ![Ta ilustracja przedstawia logo platformy .NET](media/sample-v2-code/logo_NETframework.png) | Poświadczenia klienta za pomocą klucza tajnego aplikacji lub certyfikat | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Demon aplikacji (Konsola)         | ![Ta ilustracja przedstawia logo platformy .NET](media/sample-v2-code/logo_NETcore.png) | Poświadczenia klienta przy użyciu certyfikatu| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Aplikacja sieci Web platformy ASP.NET  | ![Ta ilustracja przedstawia logo platformy .NET](media/sample-v2-code/logo_NETframework.png) | Poświadczenia klienta | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Interfejsy API sieci Web

### <a name="web-api-protected-by-azure-active-directory"></a>Internetowy interfejs API chroniony przez usługę Azure Active Directory

Poniższy przykład pokazuje, jak zapewnić ochronę środowiska node.js interfejsu API sieci web z usługą Azure AD.

W poprzednich sekcjach tego artykułu, możesz również znaleźć inne przykłady ilustrujące aplikacja kliencka **wywoływania** ASP.NET lub ASP.NET Core **interfejsu API sieci Web**. Te przykłady nie są ponownie wymienione w tej sekcji, ale możesz je znaleźć w ostatniej kolumnie tabeli powyżej lub poniżej

| Platforma | Sample |
|--------|-------------------|
| ![Ta ilustracja przedstawia logo środowiska Node.js](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Wywołanie programu Microsoft Graph lub innego interfejsu API sieci Web interfejsu API sieci Web

Poniższe przykłady pokazują, internetowy interfejs API, który wywołuje innego interfejsu API sieci web. Drugi przykład pokazuje sposób obsługi dostępu warunkowego.

| Platforma |  Wywołania programu Microsoft Graph | Wywołuje innego ASP.NET lub ASP.NET Core 2.0 Web API |
| -------- |  --------------------- | ------------------------- |
| ![Ta ilustracja przedstawia ASP.NET logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Inne przykłady z programu Microsoft Graph

Aby uzyskać przykłady i samouczki, które przedstawiają różnych wzorców użycia dla interfejsu API programu Graph firmy Microsoft, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [przykłady przygotowane przez społeczność usługi Microsoft Graph i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

[Przewodnik dewelopera usługi Azure Active Directory](v1-overview.md)

[Biblioteki uwierzytelniania usługi Active Directory systemu Azure](active-directory-authentication-libraries.md)

[Funkcji Azure AD Graph pojęć związanych z interfejsu API i dokumentacja](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteka pomocnicza interfejsu API programu Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
