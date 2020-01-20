---
title: Przykłady kodu dla platformy tożsamości firmy Microsoft | Microsoft Docs
description: Zawiera indeks dostępnych przykładów kodu platformy Microsoft Identity platform (Endpoint), zorganizowanych według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6a3ed104e6e8d79fe629b9b97c8a4ec0678d2a7
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271039"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Przykłady kodu platformy tożsamości firmy Microsoft (punkt końcowy v 2.0)

Platformy tożsamości firmy Microsoft można używać do:

- Dodawanie uwierzytelniania i autoryzacji do aplikacji sieci Web i interfejsów API sieci Web.
- Wymagaj tokenu dostępu w celu uzyskania dostępu do chronionego internetowego interfejsu API.

W tym artykule krótko opisano i przedstawiono linki do przykładów dla punktu końcowego platformy tożsamości firmy Microsoft. Te przykłady pokazują, jak to zrobiono, a także zawierają fragmenty kodu, których można używać w aplikacjach. Na stronie przykładowy kod znajdziesz szczegółowe tematy dotyczące pliku Readme, które ułatwiają wymagania, instalację i konfigurację. Komentarze w kodzie pomagają zrozumieć sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię przykłady w wersji 1.0, zobacz [przykłady kodu usługi Azure AD (punkt końcowy w wersji 1.0)](sample-v1-code.md).

Aby zrozumieć podstawowy scenariusz dla każdego typu przykładu, zobacz [typy aplikacji dla punktu końcowego platformy tożsamości firmy Microsoft](v2-app-types.md).

Możesz również współtworzyć przykłady w witrynie GitHub. Aby dowiedzieć się, jak to zrobić, zobacz [Microsoft Azure Active Directory przykłady i dokumentacja](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Te przykłady przedstawiają sposób pisania aplikacji jednostronicowej zabezpieczonej za pomocą platformy tożsamości firmy Microsoft. W tych przykładach użyto jednego z wersji MSAL. js.

| Platforma | Opis | Link |
| -------- | --------------------- | -------- |
| ![ten obraz pokazuje logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Microsoft Graph wywołań |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![ten obraz pokazuje logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Wywołania B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![ten obraz pokazuje logo JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Wywołuje własny internetowy interfejs API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![ten obraz pokazuje logo kątowe JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Microsoft Graph wywołań  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![ten obraz pokazuje logo kątowe](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL kątowy)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Microsoft Graph wywołań  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Aplikacje internetowe

W poniższych przykładach przedstawiono aplikacje sieci Web, które logują użytkowników. Niektóre przykłady przedstawiają również Microsoft Graph wywoływania aplikacji lub własnego interfejsu API sieci Web przy użyciu tożsamości użytkownika.

| Platforma | Tylko oznaki użytkowników | Logowanie użytkowników i wywołań Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Ten obraz zawiera logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Samouczek dotyczący ASP.NET Core logowania użytkowników](https://aka.ms/aspnetcore-webapp-sign-in) | Ten sam przykład w [Microsoft Graph fazie ASP.NET Core aplikacji sieci Web](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Ten obraz pokazuje logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET — szybki start](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png)  |                   | [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ten obraz pokazuje logo środowiska Node. js](media/sample-v2-code/logo_nodejs.png)  |                   | [Node. js — Szybki Start](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Ten obraz pokazuje logo języka Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Publiczne aplikacje dla komputerów stacjonarnych i mobilnych

W poniższych przykładach przedstawiono publiczne aplikacje klienckie (aplikacje klasyczne lub mobilne), które uzyskują dostęp do interfejsu API Microsoft Graph lub własnego internetowego interfejsu API w imieniu użytkownika. Wszystkie te aplikacje klienckie korzystają z biblioteki Microsoft Authentication Library (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Microsoft Graph wywołań | Wywołuje interfejs API sieci Web w ASP.NET Core 2,0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Pulpit (WPF)      | ![Ten obraz pokazuje .NET/C# logo](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Zintegrowane uwierzytelnianie systemu Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[dotnetcore — wersja 2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Python-Desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Urządzenia przenośne (Android, iOS, platformy UWP)   | ![Ten obraz pokazuje logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[Xamarin-Native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Urządzenia przenośne (iOS)       | ![Ten obraz przedstawia system iOS/cel-C lub SWIFT](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[iOS-SWIFT-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS-Native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [interactive](msal-authentication-flows.md#interactive) |[macOS-SWIFT-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![Ten obraz pokazuje logo systemu Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [system Android — Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Kotlin)   | ![Ten obraz pokazuje logo systemu Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [Android — Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplikacje demona

W poniższych przykładach przedstawiono aplikację, która uzyskuje dostęp do interfejsu API Microsoft Graph przy użyciu własnej tożsamości (bez użytkownika).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Microsoft Graph wywołań |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Ten obraz pokazuje logo platformy .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnetcore-demon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplikacja internetowa | ![Ten obraz pokazuje logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java — demon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png) | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [MS-Identity-Python-demona](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Aplikacje bezobsługowe

Poniższy przykład pokazuje publiczną aplikację kliencką działającą na urządzeniu bez przeglądarki sieci Web. Aplikacja może być narzędziem wiersza polecenia, aplikacją działającą na komputerach z systemem Linux lub Mac lub aplikacją IoT. Przykładowa funkcja umożliwia aplikacji dostęp do interfejsu API Microsoft Graph, w imieniu użytkownika, który loguje się interaktywnie na innym urządzeniu (na przykład na telefonie komórkowym). Ta aplikacja kliencka używa biblioteki uwierzytelniania firmy Microsoft (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Microsoft Graph wywołań |
| ------------------ | -------- |  ----------| ---------- |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[MS-Identity-Python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Interfejsy API sieci Web

W poniższych przykładach pokazano, jak chronić internetowy interfejs API za pomocą punktu końcowego platformy tożsamości firmy Microsoft oraz jak wywołać podrzędny interfejs API z internetowego interfejsu API.

| Platforma | Przykład |
| -------- | ------------------- |
| ![Ten obraz zawiera logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core Web API (usługa) elementu [dotnet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ten obraz pokazuje logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Internetowy interfejs API (usługa) [MS-Identity-ASPNET-WebAPI-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | Interfejs API sieci Web (usługa) [MS-Identity-Java-WebAPI](https://github.com/Azure-Samples/ms-identity-java-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions jako interfejsy API sieci Web

W poniższych przykładach pokazano, jak chronić funkcję platformy Azure przy użyciu programu HttpTrigger i uwidaczniać internetowy interfejs API za pomocą punktu końcowego platformy tożsamości firmy Microsoft oraz jak wywołać podrzędny interfejs API z internetowego interfejsu API.

| Platforma | Przykład |
| -------- | ------------------- |
| ![Ten obraz zawiera logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core Web API (usługa) Azure Function of [dotnet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Ten obraz pokazuje logo środowiska Node. js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Web API (Service) of [NodeJS i Passport-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png)</p>Python | Internetowy interfejs API (usługa) języka [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Ten obraz pokazuje logo środowiska Node. js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Internetowy interfejs API (usługa) [NodeJS i paszport-Azure-AD przy użyciu w imieniu](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Inne przykłady Microsoft Graph

Aby dowiedzieć się więcej na temat [przykładów](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczków demonstrujących różne wzorce użycia dla interfejsu API Microsoft Graph, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [Microsoft Graph przykłady społeczności & samouczków](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

- [Przewodnik dewelopera Azure Active Directory (v 1.0)](v1-overview.md)
- [Informacje koncepcyjne i dokumentacja usługi Azure AD interfejs API programu Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Biblioteka pomocnika interfejs API programu Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
