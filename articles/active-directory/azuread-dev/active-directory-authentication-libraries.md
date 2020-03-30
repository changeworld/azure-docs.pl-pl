---
title: Biblioteki uwierzytelniania usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Biblioteka uwierzytelniania usługi Azure AD (ADAL) umożliwia deweloperom aplikacji klienckich łatwe uwierzytelnianie użytkowników w chmurze lub lokalnej usłudze Active Directory (AD), a następnie uzyskiwanie tokenów dostępu do zabezpieczania wywołań interfejsu API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 32375c14d95dc9e100cbf496c8550f0a195a11dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154614"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Libraries (Biblioteki Azure Active Directory Authentication Libraries)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Biblioteka uwierzytelniania usługi Azure Active Directory (ADAL) w wersji 1.0 umożliwia deweloperom aplikacji uwierzytelnianie użytkowników w chmurze lub lokalnej usłudze Active Directory (AD) i uzyskiwanie tokenów do zabezpieczania wywołań interfejsu API. ADAL ułatwia uwierzytelnianie deweloperom za pomocą takich funkcji, jak:

- Konfigurowalna pamięć podręczna tokenów przechowująca tokeny dostępu i odświeżania tokenów
- Automatyczne odświeżanie tokenu po wygaśnięciu tokenu dostępu i dostępności tokenu odświeżania
- Obsługa wywołań metod asynchronicznych

> [!NOTE]
> Szukasz bibliotek usługi Azure AD w wersji 2.0 (MSAL)? Wyewidencjonuj [przewodnik po bibliotece MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klientów obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Sample | Tematy pomocy
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Sklep Windows, platformy uniwersalnej systemu Windows, platformy Xamarin iOS i Android |ADAL .NET w wersji 3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikacja klasyczna](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Tematy pomocy](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplikacja jednostronicowa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplikacja na iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Tematy pomocy](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikacja na Androida](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs ( JavaDocs )](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplikacja internetowa środowiska Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Tematy pomocy](https://docs.microsoft.com/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J ( ADAL4J ) |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplikacje internetowe w języku Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Tematy pomocy](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplikacja internetowa języka Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Tematy pomocy](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Biblioteki serwerów obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Sample | Tematy pomocy
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN dla usługi AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplikacja MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN dla OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Aplikacja internetowa](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN dla federacji WS |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Aplikacja MVC Web](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozszerzenia protokołu tożsamości dla platformy .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Uchwyt JWT dla .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Interfejs API sieci Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scenariusze

Oto trzy typowe scenariusze używania usługi ADAL na kliencie, który uzyskuje dostęp do zasobu zdalnego:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Uwierzytelnianie użytkowników natywnej aplikacji klienckiej uruchomionej na urządzeniu

W tym scenariuszu deweloper ma klienta mobilnego lub aplikacji klasycznej, która musi uzyskać dostęp do zasobu zdalnego, takich jak internetowy interfejs API. Internetowy interfejs API nie zezwala na anonimowe wywołania i musi być wywoływany w kontekście uwierzytelnionego użytkownika. Internetowy interfejs API jest wstępnie skonfigurowany do ufania tokenom dostępu wystawionym przez określoną dzierżawę usługi Azure AD. Usługa Azure AD jest wstępnie skonfigurowana do wystawiania tokenów dostępu dla tego zasobu. Aby wywołać internetowy interfejs API z klienta, deweloper używa usługi ADAL w celu ułatwienia uwierzytelniania za pomocą usługi Azure AD. Najbezpieczniejszym sposobem użycia adal jest renderowanie interfejsu użytkownika do zbierania poświadczeń użytkownika (renderowane jako okno przeglądarki).

Usługa ADAL ułatwia uwierzytelnianie użytkownika, uzyskiwanie tokenu dostępu i odświeżanie tokenu z usługi Azure AD, a następnie wywoływanie internetowego interfejsu API przy użyciu tokenu dostępu.

Aby uzyskać przykładowy kod, który pokazuje ten scenariusz przy użyciu uwierzytelniania w usłudze Azure AD, zobacz [Natywna aplikacja WPF WPF do interfejsu API sieci Web.](https://github.com/azureadsamples/nativeclient-dotnet)

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Uwierzytelnianie poufnej aplikacji klienckiej uruchomionej na serwerze sieci Web

W tym scenariuszu deweloper ma aplikację uruchomieni na serwerze, który musi uzyskać dostęp do zasobu zdalnego, takich jak internetowy interfejs API. Internetowy interfejs API nie zezwala na anonimowe połączenia, więc musi być wywoływany z autoryzowanej usługi. Internetowy interfejs API jest wstępnie skonfigurowany do ufania tokenom dostępu wystawionym przez określoną dzierżawę usługi Azure AD. Usługa Azure AD jest wstępnie skonfigurowana do wystawiania tokenów dostępu dla tego zasobu do usługi z poświadczeniami klienta (identyfikator klienta i klucz tajny). ADAL ułatwia uwierzytelnianie usługi za pomocą usługi Azure AD zwraca token dostępu, który może służyć do wywoływania internetowego interfejsu API. ADAL obsługuje również zarządzanie okresem istnienia tokenu dostępu przez buforowanie go i odnawianie go w razie potrzeby. Aby zapoznać się z przykładem kodu, który pokazuje ten scenariusz, zobacz [Konsola demona Aplikacja do interfejsu API sieci Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Uwierzytelnianie poufnej aplikacji klienckiej uruchomionej na serwerze w imieniu użytkownika

W tym scenariuszu deweloper ma aplikację sieci web uruchomieni na serwerze, który musi uzyskać dostęp do zasobu zdalnego, takich jak internetowy interfejs API. Internetowy interfejs API nie zezwala na anonimowe połączenia, więc musi być wywoływany z autoryzowanej usługi w imieniu uwierzytelnionego użytkownika. Internetowy interfejs API jest wstępnie skonfigurowany do ufania tokenom dostępu wystawionym przez określoną dzierżawę usługi Azure AD, a usługa Azure AD jest wstępnie skonfigurowana do wystawiania tokenów dostępu dla tego zasobu do usługi z poświadczeniami klienta. Gdy użytkownik jest uwierzytelniony w aplikacji sieci web, aplikacja może uzyskać kod autoryzacji dla użytkownika z usługi Azure AD. Aplikacja sieci web może następnie użyć usługi ADAL, aby uzyskać token dostępu i odświeżyć token w imieniu użytkownika przy użyciu kodu autoryzacji i poświadczeń klienta skojarzonych z aplikacją z usługi Azure AD. Gdy aplikacja sieci web jest w posiadaniu tokenu dostępu, można wywołać interfejsu API sieci web, aż do wygaśnięcia tokenu. Po wygaśnięciu tokenu aplikacja sieci web może użyć usługi ADAL, aby uzyskać nowy token dostępu przy użyciu tokenu odświeżania, który został wcześniej odebrany. Aby uzyskać przykładowy kod, który pokazuje ten scenariusz, zobacz [Natywny klient do interfejsu API sieci Web do interfejsu API sieci Web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dla deweloperów usługi Azure Active Directory](v1-overview.md)
- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](v1-authentication-scenarios.md)
- [Przykłady kodu usługi Azure Active Directory](sample-v1-code.md)
