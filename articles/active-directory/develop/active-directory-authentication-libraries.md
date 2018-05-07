---
title: Biblioteki uwierzytelniania usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Azure AD Authentication Library (ADAL) umożliwia deweloperom łatwe uwierzytelnianie użytkowników w chmurze aplikacji klienta lub lokalnej usługi Active Directory (AD) i uzyskać tokeny dostępu dla zabezpieczanie interfejsu API.
services: active-directory
documentationcenter: ''
author: SaeedAkhter-MSFT
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1e82fa62c86c80b1e68fea995ca6f90861688d5c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Biblioteki uwierzytelniania usługi Azure Active Directory

V1.0 interfejsów Azure Active Directory Authentication Library (ADAL) umożliwia deweloperom aplikacji uwierzytelniania użytkowników w chmurze lub lokalnej usługi Active Directory (AD) i uzyskać tokeny dla zabezpieczanie interfejsu API. Biblioteka ADAL ułatwia uwierzytelniania dla deweloperów przy użyciu funkcji takich jak:

- Można skonfigurować pamięć podręczną tokenów czy magazyny tokenów dostępu i tokenów odświeżania
- automatyczne odświeżanie tokenu po wygaśnięciu tokenu dostępu i token odświeżania jest dostępny
- Obsługa dla wywołań metod asynchronicznych

> [!NOTE]
> Szukasz bibliotek v2.0 usługi Azure AD (MSAL)? Wyewidencjonowanie [MSAL biblioteki przewodnik](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienta obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Sklep Windows, platformy uniwersalnej systemu Windows, Xamarin iOS i Android |ADAL .NET w wersji 3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikacja klasyczna](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Dokumentacja](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET klienta, Sklep Windows, Windows Phone 8.1 |ADAL .NET w wersji 2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplikacja klasyczna](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Jednej strony aplikacji](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplikacja systemu iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Dokumentacja](https://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[Centralne repozytorium](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikacji systemu android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplikacja sieci Web środowiska Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)| |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplikacje sieci Web w języku Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplikacja sieci web języka Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

## <a name="microsoft-supported-server-libraries"></a>Biblioteki Server obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN dla AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikacji MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN dla OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikacja sieci Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN dla protokołu WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikacja sieci Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozszerzenia protokołu tożsamości dla platformy .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Program obsługi tokenów JWT dla platformy .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD usługi Passport. |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Interfejs API sieci Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono trzy typowe scenariusze dotyczące używania biblioteki ADAL na kliencie, który uzyskuje dostęp do zasobu zdalnego:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Uwierzytelnianie użytkowników aplikację native client, uruchamianie na urządzeniu

W tym scenariuszu projektant ma klientów urządzeń przenośnych lub aplikacja komputerowa, która potrzebuje dostępu do zdalnego zasobu, np. interfejsu API sieci web. Interfejs API sieci web nie zezwala na połączenia anonimowe i musi zostać wywołany w kontekście uwierzytelnionego użytkownika. Interfejs API sieci web jest wstępnie skonfigurowana do zaufania tokenów dostępu wystawiony przez określony dzierżawy usługi Azure AD. Usługi Azure AD jest wstępnie skonfigurowana do wystawiania tokenów dostępu dla tego zasobu. Do wywołania interfejsu API sieci web z klienta, deweloper używa biblioteki ADAL do umożliwienia uwierzytelniania za pomocą usługi Azure AD. Najbezpieczniejszym sposobem użycia ADAL jest ona renderowania interfejsu użytkownika do zbierania poświadczeń użytkownika (renderowane jako okna przeglądarki).

Biblioteka ADAL ułatwia uwierzytelniania użytkownika, Uzyskaj token dostępu i token odświeżania z usługi Azure AD, a następnie wywołać interfejsu API przy użyciu programu dostęp w sieci web token.

Aby uzyskać przykładowy kod, który demonstruje ten scenariusz przy użyciu uwierzytelniania usługi Azure AD, zobacz [natywnych aplikacji WPF klienta do interfejsu API sieci Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Uwierzytelniania aplikacji klienckiej poufne uruchomiony na serwerze sieci web

W tym scenariuszu projektant ma aplikacji uruchomionej na serwerze, który ma uzyskać dostęp do zasobów zdalnego, takich jak interfejsu API sieci web. Wywołuje sieci web interfejsu API nie zezwalaj na anonimowe, dlatego musi zostać wywołany z autoryzowaną usługę. Interfejs API sieci web jest wstępnie skonfigurowana do zaufania tokenów dostępu wystawiony przez określony dzierżawy usługi Azure AD. Usługi Azure AD jest wstępnie skonfigurowana do wystawiania tokenów dostępu dla tego zasobu do usługi przy użyciu poświadczeń klienta (identyfikator klienta i klucz tajny). Biblioteka ADAL umożliwia uwierzytelnianie usługi z usługą Azure AD, zwracając tokenu dostępu, który może służyć do wywołania interfejsu API sieci web. Biblioteka ADAL obsługuje również zarządzanie okresem istnienia tokenu dostępu, buforowanie ich i odnawianie go odpowiednio do potrzeb. Aby uzyskać przykładowy kod, który demonstruje tego scenariusza, zobacz [konsoli demon aplikacji interfejsu API sieci Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Uwierzytelniania aplikacji klienckiej poufne uruchomiony na serwerze, w imieniu użytkownika

W tym scenariuszu Projektant jest aplikacja sieci web na serwerze, który ma uzyskać dostęp do zasobów zdalnego, takich jak interfejsu API sieci web. Interfejs API sieci web nie zezwala na anonimowe wywołania, więc musi zostać wywołany z autoryzowaną usługę imieniu uwierzytelnionego użytkownika. Interfejs API sieci web jest wstępnie skonfigurowana zaufania dostępu tokenów wystawionych przez określone usługi Azure AD dzierżawy i Azure AD jest wstępnie skonfigurowana do wystawiania tokenów dostępu dla tego zasobu do usługi przy użyciu poświadczeń klienta. Po uwierzytelnieniu użytkownika w aplikacji sieci web, aplikacja może pobrać kod autoryzacji dla użytkownika z usługi Azure AD. Aplikacji sieci web można następnie użyć ADAL uzyskać token dostępu i odświeżać token w imieniu użytkownika za pomocą kodu i klienta poświadczenia autoryzacji skojarzone z aplikacją z usługi Azure AD. Gdy aplikacja sieci web ma tokenu dostępu, można wywołać interfejsu API sieci web, do momentu wygaśnięcia tokenu. Po wygaśnięciu tokenu aplikacji sieci web można użyć biblioteki ADAL, aby uzyskać nowy token dostępu za pomocą odświeżanie tokenu wcześniej odebrany. Aby uzyskać przykładowy kod, który demonstruje tego scenariusza, zobacz [Native client do interfejsu API sieci Web interfejsu API sieci Web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md)
- [Scenariusze uwierzytelniania dla usługi Azure Active directory](active-directory-authentication-scenarios.md)
- [Przykłady kodu w usłudze Azure Active Directory](active-directory-code-samples.md)
