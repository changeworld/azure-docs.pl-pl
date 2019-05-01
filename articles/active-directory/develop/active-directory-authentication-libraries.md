---
title: Biblioteki uwierzytelniania usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Usługa Azure AD Authentication Library (ADAL) umożliwia deweloperom łatwe uwierzytelnianie użytkowników w chmurze klienta lub lokalnej usługi Active Directory (AD) i następnie uzyskiwanie tokenów dostępu do zabezpieczania wywołań interfejsu API.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf54e2d660e44bfb06f6c0ccb56fd5c7aea907e3
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917260"
---
# <a name="azure-active-directory-authentication-libraries"></a>Biblioteki uwierzytelniania usługi Azure Active Directory

V1.0 interfejsów Azure Active Directory Authentication Library (ADAL) umożliwia deweloperom aplikacji do uwierzytelniania użytkowników w chmurze lub lokalnej usługi Active Directory (AD) i uzyskiwania tokenów do zabezpieczania wywołań interfejsu API. Biblioteka ADAL ułatwia uwierzytelnianie dla deweloperów za pośrednictwem funkcji takich jak:

- Można skonfigurować pamięć podręczną tokenów, magazynów tokeny dostępu i tokenów odświeżania
- Automatyczne odświeżanie tokenu po wygaśnięciu token dostępu i token odświeżania jest dostępny
- Obsługa wywołania metody asynchronicznej

> [!NOTE]
> Szukasz bibliotek v2.0 usługi Azure AD (MSAL)? Wyewidencjonuj [instrukcją biblioteki MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienckie obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| Klient modelu .NET, Windows Store, platformy uniwersalnej systemu Windows, platformy Xamarin dla systemów iOS i Android |.NET biblioteki ADAL v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikacja klasyczna](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Dokumentacja](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| .NET Client, Windows Store, Windows Phone 8.1 |.NET biblioteki ADAL v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplikacja klasyczna](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplikacje z jedną stroną](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplikacja systemu iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Dokumentacja](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Centralne repozytorium](https://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikacja dla systemu android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplikacja internetowa środowiska Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Dokumentacja](https://docs.microsoft.com/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplikacje internetowe w języku Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Dokumentacja](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplikacja internetowa w języku Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Dokumentacja](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Biblioteki Server obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN dla usługi Azure AD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplikacja MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN dla OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Aplikacja internetowa](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN dla protokołu WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Aplikacja internetowa MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozszerzenia protokołu tożsamości dla platformy .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Rozszerzenie JWT Handler dla platformy .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Usługa Azure AD usługi Passport. |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Interfejs API sieci Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono trzy typowe scenariusze za pomocą biblioteki ADAL w kliencie, który uzyskuje dostęp do zasobu zdalnego:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Uwierzytelnianie użytkowników natywnej aplikacji klienckiej na urządzeniu

W tym scenariuszu Deweloper ma klientów urządzeń przenośnych lub aplikacja komputerowa, która wymaga dostępu do zdalnego zasobu, takich jak interfejs API sieci web. Internetowy interfejs API nie zezwala na połączenia anonimowe i musi zostać wywołana w kontekście uwierzytelnionego użytkownika. Internetowy interfejs API jest wstępnie skonfigurowana pod kątem zaufania tokeny dostępu wystawione przez konkretną dzierżawy usługi Azure AD. Usługa Azure AD jest wstępnie skonfigurowany do wystawiania tokenów dostępu dla tego zasobu. Do wywołania interfejsu API sieci web z klienta, deweloper używa biblioteki ADAL w celu ułatwienia uwierzytelniania za pomocą usługi Azure AD. Najbezpieczniejszą metodą używa biblioteki ADAL polega na uzyskaniu renderowania interfejsu użytkownika do zbierania poświadczeń użytkownika (renderowany jako okno przeglądarki).

Biblioteka ADAL ułatwia uwierzytelnianie użytkownika, Uzyskaj token dostępu i token odświeżania z usługi Azure AD, a następnie wywołaj internetowy interfejs API przy użyciu dostępu tokenu.

Aby uzyskać przykładowy kod, który demonstruje ten scenariusz przy użyciu uwierzytelniania usługi Azure AD, zobacz [natywną aplikację kliencką WPF, interfejsu API sieci Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Uwierzytelnianie aplikacji zawierających poufne dane klienta uruchomiony na serwerze sieci web

W tym scenariuszu Deweloper ma aplikację działającą na serwerze, który wymaga dostępu do zdalnego zasobu, takich jak interfejs API sieci web. Wywołuje internetowy interfejs API nie zezwala na anonimowe, więc musi ona zostać wywołana z autoryzowaną usługę. Internetowy interfejs API jest wstępnie skonfigurowana pod kątem zaufania tokeny dostępu wystawione przez konkretną dzierżawy usługi Azure AD. Usługa Azure AD jest wstępnie skonfigurowany do wystawiania tokenów dostępu dla tego zasobu z usługą przy użyciu poświadczeń klienta (identyfikator klienta i klucz tajny). Biblioteka ADAL ułatwia uwierzytelnianie usługi z usługą Azure AD, które zwraca token dostępu, który może służyć do wywołania interfejsu API sieci web. Biblioteka ADAL obsługuje również zarządzanie okresem istnienia tokenu dostępu, buforowanie ich i odnawianie go zgodnie z potrzebami. Aby uzyskać przykładowy kod, który pokazuje, w tym scenariuszu, zobacz [demona konsoli aplikacji interfejsu API sieci Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Uwierzytelnianie aplikacji zawierających poufne dane klienta, działa na serwerze, w imieniu użytkownika

W tym scenariuszu Deweloper ma aplikacji internetowej działającej na serwerze, który wymaga dostępu do zdalnego zasobu, takich jak interfejs API sieci web. Internetowy interfejs API nie zezwala na wywołania anonimowe, więc musi ona zostać wywołana z autoryzowanych usług w imieniu uwierzytelnionego użytkownika. Internetowy interfejs API jest wstępnie skonfigurowana zaufania tokeny dostępu wystawione przez określone usługi Azure AD do dzierżawy, a usługą Azure AD jest wstępnie skonfigurowana do wystawiania tokenów dostępu dla tego zasobu z usługą przy użyciu poświadczeń klienta. Po uwierzytelnieniu użytkownika w aplikacji sieci web, aplikacja może uzyskać kodu autoryzacji dla użytkownika z usługi Azure AD. Aplikacja sieci web, następnie można użyć biblioteki ADAL do uzyskania tokenu dostępu i odświeżenia tokenu w imieniu użytkownika za pomocą kodu i klient poświadczenia autoryzacji skojarzone z aplikacją z usługi Azure AD. Gdy aplikacji sieci web jest w posiadaniu tokenu dostępu, można wywołać interfejsu API sieci web, do momentu wygaśnięcia ważności tokenu. Po wygaśnięciu ważności tokenu aplikacji sieci web można użyć biblioteki ADAL można uzyskać nowy token dostępu za pomocą odświeżenia tokenu, który wcześniej został odebrany. Aby uzyskać przykładowy kod, który pokazuje, w tym scenariuszu, zobacz [klientami do interfejsu API sieci Web do internetowego interfejsu API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dewelopera usługi Azure Active Directory](v1-overview.md)
- [Scenariusze uwierzytelniania dla usługi Azure Active directory](authentication-scenarios.md)
- [Przykłady kodu usługi Azure Active Directory](sample-v1-code.md)
