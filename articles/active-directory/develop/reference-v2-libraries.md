---
title: Biblioteki uwierzytelniania platforma tożsamości firmy Microsoft | Dokumentacja firmy Microsoft
description: Biblioteki klienckie zgodne i bibliotek oprogramowania pośredniczącego serwera i powiązane biblioteki, źródła i łącza przykłady, punktu końcowego platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19718dc884685f6c1e1a588eea6a5de36e446fb1
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545531"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Biblioteki uwierzytelniania platforma tożsamości firmy Microsoft

[Punktu końcowego platformy tożsamości firmy Microsoft](active-directory-v2-compare.md) obsługuje standardowe protokoły OAuth 2.0 i OpenID Connect 1.0. Biblioteka Microsoft Authentication Library (MSAL) jest przeznaczona do pracy z punktem końcowym platforma tożsamości firmy Microsoft. Istnieje również możliwość użycia bibliotek typu open source, które obsługują protokołu OAuth 2.0 i OpenID Connect 1.0.

Zaleca się, że używasz bibliotek napisanych przez protokół domeny eksperci, postępuj zgodnie z metodologii cykl projektowania zabezpieczeń (SDL), takie jak [co następuje Microsoft][Microsoft-SDL]. Jeśli zdecydujesz się kodowi ręcznie dla protokołów, powinien być zgodny metodologii, takie jak SDL i płatności firmy Microsoft należy zamknąć uwagę zagadnienia dotyczące zabezpieczeń w specyfikacji standardów dla każdego protokołu.

> [!NOTE]
> Szukasz Biblioteka Azure AD authentication library (ADAL)? Wyewidencjonuj [przewodnik biblioteki ADAL](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Rodzaje bibliotek

Punkt końcowy platforma tożsamości firmy Microsoft w programach dwa rodzaje biblioteki:

* **Biblioteki klienckie**: Serwery i klientów natywnych użyj biblioteki klienckie uzyskiwanie tokenów dostępu do wywoływania zasobu, np. Microsoft Graph.
* **Biblioteki oprogramowania pośredniczącego serwera**: Aplikacje sieci Web możliwe używanie bibliotek oprogramowania pośredniczącego serwera podczas logowania użytkownika. Interfejsy API sieci Web możliwe używanie bibliotek oprogramowania pośredniczącego serwera do sprawdzania poprawności tokenów, które są wysyłane przez klientów natywnych lub przez inne serwery.

## <a name="library-support"></a>Obsługa bibliotek

Biblioteki są dostępne w dwóch kategoriach pomocy technicznej:

* **Obsługiwane przez firmę Microsoft**: Firma Microsoft oferuje poprawki dla tych bibliotek i wykonaniu SDL należytą starannością na tych bibliotek.
* **Zgodne**: Microsoft przetestowała tych bibliotek w podstawowych scenariuszy i potwierdza, czy działają one z punktem końcowym platforma tożsamości firmy Microsoft. Firma Microsoft udostępnia poprawki dla tych bibliotek i nie zrobił przeglądu tych bibliotek. Problemy i sugestie funkcji powinny być kierowane do biblioteki typu open-source projektu.

Aby uzyskać listę bibliotek, które działają z punktem końcowym platforma tożsamości firmy Microsoft zobacz następne sekcje w tym artykule.

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienckie obsługiwane przez firmę Microsoft

Biblioteki uwierzytelniania klienta są używane do uzyskania tokenu służącego do wywoływania chronionego internetowego interfejsu API

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Sample | Odwołanie | Koncepcyjny dokumentu | Harmonogram działania |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Aplikacje z jedną stroną](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Dokumentacja](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Dokumentacja koncepcyjne](msal-overview.md)| [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | Angular(Preview) biblioteki MSAL | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![Platforma UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | BIBLIOTEKA MSAL .NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikacja klasyczna](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | Biblioteka MSAL Python (wersja zapoznawcza) | [PyPI](https://pypi.org/project/msal) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Przykłady](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | Biblioteka MSAL Java (wersja zapoznawcza) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Przykłady](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![dla systemu iOS / Objective C lub swift.](media/sample-v2-code/logo_iOS.png) | Biblioteka MSAL obj_c (wersja zapoznawcza) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikacja systemu iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Java](media/sample-v2-code/logo_Android.png) | Biblioteka MSAL (wersja zapoznawcza) | [Centralne repozytorium](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikacja dla systemu android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Biblioteki oprogramowania pośredniczącego serwera obsługiwane przez firmę Microsoft

Oprogramowanie pośredniczące biblioteki są używane do ochrony aplikacji sieci Web i interfejsów API sieci Web. Dla aplikacji sieci web lub interfejsu API sieci Web napisane przy użyciu platformy ASP.NET lub ASP.NET Core, biblioteki oprogramowania pośredniczącego są używane przez program ASP.NET / platformy ASP.NET Core

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Sample | Odwołanie
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET Security |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Aplikacja MVC](quickstart-v2-aspnet-webapp.md) |[Dokumentacja interfejsu API platformy ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Rozszerzenia modelu IdentityModel dla platformy .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplikacja MVC](quickstart-v2-aspnet-webapp.md) |[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Usługa Azure AD usługi Passport. |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplikacja sieci Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Biblioteki klienckie zgodne

| Platforma | Nazwa biblioteki | Przetestowanej wersji | Kod źródłowy | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Sekretarz języka Java](https://github.com/scribejava/scribejava) | [Wersja 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Biblioteka Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Wersja 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Biblioteka Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [Wersji 1.2.0 lub nowszej](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [klienta openid](https://github.com/panva/node-openid-client) | [Wersja 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [klienta openid](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP Ligi klienta protokołu oauth2](https://github.com/thephpleague/oauth2-client) | [W wersji 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [Klient protokołu oauth2](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [React Native uwierzytelniania aplikacji](https://github.com/FormidableLabs/react-native-app-auth) | [Wersja 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native uwierzytelniania aplikacji](https://github.com/FormidableLabs/react-native-app-auth) | |

Wszystkie biblioteki zgodnych ze standardami, można użyć punktu końcowego platformy tożsamości firmy Microsoft, dlatego ważne jest, aby wiedzieć, gdzie szukać pomocy technicznej.

* Problemy i sugestie dotyczące nowych funkcji w kodzie biblioteki skontaktuj się z właścicielem biblioteki.
* Problemy i sugestie dotyczące nowych funkcji w implementacji protokołu po stronie usługi skontaktuj się z pomocą firmy Microsoft.
* [Plik wniosek dotyczący funkcji](https://feedback.azure.com/forums/169401-azure-active-directory) dodatkowych funkcji, które chcesz zobaczyć w protokole.
* [Utwórz żądanie obsługi](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Jeśli znajdziesz problem gdzie Microsoft platformy tożsamości z punktu końcowego nie jest zgodne z uwierzytelniania OAuth 2.0 lub OpenID Connect 1.0.

## <a name="related-content"></a>Powiązana zawartość

Aby uzyskać więcej informacji na temat punktu końcowego platformy tożsamości firmy Microsoft, zobacz [Przegląd platforma tożsamości usługi Microsoft][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
