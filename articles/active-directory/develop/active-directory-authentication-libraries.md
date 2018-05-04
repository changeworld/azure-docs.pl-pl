---
title: Biblioteki uwierzytelniania usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Azure AD Authentication Library (ADAL) umożliwia deweloperom łatwe uwierzytelnianie użytkowników w chmurze aplikacji klienta lub lokalnej usługi Active Directory (AD) i uzyskać tokeny dostępu dla zabezpieczanie interfejsu API.
services: active-directory
documentationcenter: ''
author: bryanla
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: e2c77b2e47ce3dd900b1b277e802a07cfed84702
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Biblioteki uwierzytelniania usługi Azure Active Directory
Azure Active Directory Authentication Library (ADAL) umożliwia deweloperom aplikacji uwierzytelniania użytkowników w chmurze lub lokalnej usługi Active Directory (AD) i uzyskać tokeny dla zabezpieczanie interfejsu API. Biblioteka ADAL ułatwia uwierzytelniania dla deweloperów przy użyciu funkcji takich jak:
 - Można skonfigurować pamięć podręczną tokenów czy magazyny tokenów dostępu i tokenów odświeżania
 - automatyczne odświeżanie tokenu po wygaśnięciu tokenu dostępu i token odświeżania jest dostępny
 - Obsługa dla wywołań metod asynchronicznych
 - itp.

> [!NOTE]
> Szukasz bibliotek v2.0 usługi Azure AD (MSAL)? Wyewidencjonowanie [MSAL biblioteki przewodnik](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

### <a name="client-libraries"></a>Biblioteki klienckie

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Sklep Windows, platformy uniwersalnej systemu Windows, Xamarin iOS i Android |ADAL .NET w wersji 3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikacja klasyczna](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Dokumentacja](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET klienta, Sklep Windows, Windows Phone 8.1 |ADAL .NET w wersji 2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplikacja klasyczna](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Jednej strony aplikacji](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplikacja systemu iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Dokumentacja](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[Centralne repozytorium](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikacji systemu android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplikacje sieci Web w języku Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplikacja sieci web języka Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

### <a name="server-libraries"></a>Serwer biblioteki

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN dla AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikacji MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN dla OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikacja sieci Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD usługi Passport. |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Interfejs API sieci Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN dla protokołu WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikacja sieci Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozszerzenia protokołu tożsamości dla platformy .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Program obsługi tokenów JWT dla platformy .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Bibliotek klienta w wersji 2.0 (MSAL)

[Punktu końcowego v2.0 usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) łączy usługi Azure AD i Accounts Microsoft za jeden punkt końcowy. Aby uzyskać dostęp do tego punktu końcowego, deweloperzy mogą używać [biblioteki MSAL obsługiwane w środowisku produkcyjnym Podgląd](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) zamiast biblioteki ADAL.

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Sklep Windows, platformy uniwersalnej systemu Windows, Xamarin iOS i Android |MSAL dla platformy .NET (wersja zapoznawcza) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikacja klasyczna](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Dokumentacja](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) |
| JavaScript |MSAL dla języka JavaScript (wersja zapoznawcza) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Jednej strony aplikacji](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Dokumentacja](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) |
| iOS |MSAL dla systemu iOS (wersja zapoznawcza) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikacja systemu iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Dokumentacja](https://azuread.github.io/docs/objc/) |
| Android |MSAL dla systemu Android (wersja zapoznawcza) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikacji systemu android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Dokumentacja](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono trzy typowe scenariusze, w których ADAL może służyć do uwierzytelniania klienta, który uzyskuje dostęp do zasobu zdalnego:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Uwierzytelnianie użytkowników aplikację native client, uruchamianie na urządzeniu

W tym scenariuszu projektant ma WPF aplikacji klienckiej, który ma uzyskać dostęp do zasobów zdalnego zabezpieczone przez usługę Azure AD, takie jak interfejsu API sieci web. On ma subskrypcji platformy Azure, potrafi do wywołania interfejsu API sieci web podrzędne i zna dzierżawy usługi Azure AD, która używa interfejsu API sieci web. W związku z tym może użyć ADAL w celu umożliwienia uwierzytelniania za pomocą usługi Azure AD, przez pełni delegowanie środowisko uwierzytelniania do biblioteki ADAL lub jawną obsługę poświadczeń użytkownika. ADAL umożliwia łatwe do uwierzytelnienia użytkownika, Uzyskaj token dostępu i token odświeżania z usługi Azure AD, a następnie użyć tokenu dostępu aby żądania do interfejsu API sieci web.

Aby uzyskać przykładowy kod, który demonstruje ten scenariusz przy użyciu uwierzytelniania usługi Azure AD, zobacz [natywnych aplikacji WPF klienta do interfejsu API sieci Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Uwierzytelniania aplikacji klienckiej poufne uruchomiony na serwerze sieci web

W tym scenariuszu projektant ma aplikacji uruchomionej na serwerze, który ma uzyskać dostęp do zasobów zdalnego zabezpieczone przez usługę Azure AD, takie jak interfejsu API sieci web. On ma subskrypcji platformy Azure, potrafi do wywołania usługi podrzędne i wie, że korzysta z dzierżawy usługi Azure AD, interfejsu API sieci web. W związku z tym może użyć ADAL w celu ułatwienia uwierzytelniania za pomocą usługi Azure AD przez jawną obsługę poświadczenia aplikacji. ADAL umożliwia łatwe do pobrania tokenu z usługi Azure AD przy użyciu poświadczeń klienta aplikacji, a następnie użyć tokenu, aby żądania do interfejsu API sieci web. Biblioteka ADAL obsługuje również zarządzanie okresem istnienia tokenu dostępu, buforowanie ich i odnawianie go odpowiednio do potrzeb. Aby uzyskać przykładowy kod, który demonstruje tego scenariusza, zobacz [konsoli demon aplikacji interfejsu API sieci Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Uwierzytelniania aplikacji klienckiej poufne uruchomiony na serwerze, w imieniu użytkownika

W tym scenariuszu projektant ma aplikacji uruchomionej na serwerze, który ma uzyskać dostęp do zasobów zdalnego zabezpieczone przez usługę Azure AD, takie jak interfejsu API sieci web. Żądanie musi być wprowadzone w imieniu użytkownika usługi Azure AD. On ma subskrypcji platformy Azure, potrafi do wywołania interfejsu API w sieci web podrzędne i zna usługi Azure AD używane przez usługę dzierżawa. Po uwierzytelnieniu użytkownika do aplikacji sieci web, aplikacja może pobrać kod autoryzacji dla użytkownika z usługi Azure AD. Aplikacji sieci web można następnie użyć ADAL uzyskać token dostępu i odświeżać token w imieniu użytkownika za pomocą kodu i klienta poświadczenia autoryzacji skojarzone z aplikacją z usługi Azure AD. Gdy aplikacja sieci web ma tokenu dostępu, można wywołać interfejsu API sieci web, do momentu wygaśnięcia tokenu. Po wygaśnięciu tokenu aplikacji sieci web można użyć biblioteki ADAL, aby uzyskać nowy token dostępu za pomocą odświeżanie tokenu wcześniej odebrany. Aby uzyskać przykładowy kod, który demonstruje tego scenariusza, zobacz [Native client do interfejsu API sieci Web interfejsu API sieci Web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md)
- [Scenariusze uwierzytelniania dla usługi Azure Active directory](active-directory-authentication-scenarios.md)
- [Przykłady kodu w usłudze Azure Active Directory](active-directory-code-samples.md)
