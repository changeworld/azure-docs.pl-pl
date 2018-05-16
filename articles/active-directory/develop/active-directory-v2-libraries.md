---
title: Biblioteki uwierzytelniania v2.0 w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zgodny klient biblioteki i bibliotek oprogramowania pośredniczącego serwera i powiązane biblioteki, źródła i łączy próbek, dla punktu końcowego v2.0 usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8fe3db09acbdec606f25d0bc81300bc4f5e87411
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Biblioteki uwierzytelniania v2.0 w usłudze Azure Active Directory

[Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) obsługuje standardowych protokołów uwierzytelniania OAuth 2.0 i OpenID Connect 1.0. Biblioteka uwierzytelniania firmy Microsoft (MSAL) jest przeznaczona do pracy z punktem końcowym v2.0 usługi Azure AD. Istnieje również możliwość użycia bibliotek open source, które obsługują OAuth 2.0 i OpenID Connect 1.0.

Zalecane jest użycie biblioteki napisane przez ekspertów domeny protokołu, Obserwujący metodologii Security Development Lifecycle (SDL), są tak samo, jak [co następuje Microsoft][Microsoft-SDL]. Jeśli zdecydujesz się kodu strony Obsługa protokołów, wykonaj metodologii, jak SDL firmy Microsoft i płatności zamknąć uwagę zagadnienia dotyczące zabezpieczeń w specyfikacji standardów dla każdego protokołu.

> [!NOTE]
> Szukasz bibliotek w wersji 1.0 usługi Azure AD (ADAL)? Wyewidencjonowanie [przewodnik bibliotekę ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Rodzaje bibliotek

Punktu końcowego v2.0 usługi Azure AD współpracuje z dwóch typów biblioteki:

* **Bibliotek klienckich**. Natywny klienci i serwery umożliwia pobieranie tokenów dostępu do wywoływania zasobów, takich jak Microsoft Graph bibliotek klienckich.
* **Biblioteki oprogramowania pośredniczącego serwera**. Aplikacje sieci Web korzystać bibliotek oprogramowania pośredniczącego serwera dla logowania użytkownika. Interfejsy API sieci Web umożliwia sprawdzania poprawności tokenów, które są wysyłane przez klientów natywnych lub przez inne serwery bibliotek oprogramowania pośredniczącego serwera.

## <a name="library-support"></a>Obsługa biblioteki

Ponieważ można wybrać wszystkie biblioteki zgodny ze standardami, korzystając z punktem końcowym v2.0, należy wiedzieć, gdzie przejdź do pomocy technicznej. Problemy i żądania funkcji w kodzie biblioteki skontaktuj się z właścicielem biblioteki. Funkcja żądania w implementacji protokołu po stronie serwera i problemów skontaktuj się z firmy Microsoft. [Plik żądanie funkcji](https://feedback.azure.com/forums/169401-azure-active-directory) dodatkowych funkcji, które chcesz zobaczyć w protokole. [Utwórz żądanie obsługi](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request) Jeśli napotkasz problem gdzie punktu końcowego v2.0 usługi Azure AD nie jest zgodne z OAuth 2.0 lub OpenID Connect 1.0.

Biblioteki są dostępne w dwóch kategorii pomocy technicznej:

* **Obsługiwane przez firmę Microsoft**. Firma Microsoft oferuje poprawki dla bibliotek i wykonaniu SDL termin starannością na tych bibliotek.
* **Zgodne**. Microsoft przetestowała te biblioteki w podstawowe scenariusze i potwierdza, że działają z punktem końcowym v2.0. Firma Microsoft zapewnia poprawki dotyczące biblioteki i nie przeprowadził Przegląd te biblioteki. Problemy i żądania funkcji powinny być kierowane do biblioteki projektu open source.

Lista bibliotek, które współpracują z punktem końcowym v2.0 zobacz sekcje dalej w tym artykule.

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienta obsługiwane przez firmę Microsoft

> [!IMPORTANT]
> Biblioteki Podgląd MSAL są odpowiednie do użycia w środowisku produkcyjnym. Firma Microsoft zapewnia obsługę poziomu tego samego produkcji te biblioteki jako bieżący bibliotek produkcji (ADAL). W wersji zapoznawczej oczekiwać, że zmiany do interfejsu API MSAL, format wewnętrznej pamięci podręcznej i innych mechanizmów te biblioteki bez powiadomienia, które będzie wymagane do wykonania wraz z poprawki lub ulepszenia funkcji. Może to mieć wpływ na aplikację. Na przykład zmianę formatu pamięci podręcznej może wymagać użytkowników, aby zalogować się ponownie. Zmiany interfejsu API może wymagać zaktualizuj kod. Po udostępnieniu wersji dostępności ogólne (GA), wszystkie aplikacje korzystające z wersji zapoznawczej biblioteki należy zaktualizować w ciągu sześciu miesięcy lub ich mogą przestać działać.

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Sklep Windows, platformy uniwersalnej systemu Windows, Xamarin iOS i Android | MSAL .NET (wersja zapoznawcza) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikacja klasyczna](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (wersja zapoznawcza) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Jednej strony aplikacji](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (wersja zapoznawcza) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikacja systemu iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (wersja zapoznawcza) | [Centralne repozytorium](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikacji systemu android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Biblioteki oprogramowania pośredniczącego serwera obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect oprogramowania pośredniczącego |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[Aplikacji MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Oprogramowanie pośredniczące elementu nośnego OAuth OWIN do AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | Program obsługi tokenów JWT dla platformy .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Oprogramowanie pośredniczące ASP.NET OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Zabezpieczenia programu ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplikacji MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Oprogramowanie pośredniczące elementu nośnego OAuth ASP.NET |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Zabezpieczenia programu ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Program obsługi tokenów JWT dla platformy .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD usługi Passport. |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplikacja sieci Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>Zgodny klient biblioteki

| Platforma | Nazwa biblioteki | Wersja przetestowany | Kod źródłowy | Sample |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Przykładowej aplikacji natywnej](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Przykładowej aplikacji natywnej](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribejava Sekretarz Java](https://github.com/scribejava/scribejava) | [Wersja 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [PHP Ligi oauth2 klienta](https://github.com/thephpleague/oauth2-client) | [Wersja 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [Klient protokołu oauth2](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Zawartość pokrewna
Aby uzyskać więcej informacji dotyczących punktu końcowego v2.0 usługi Azure AD, zobacz [usługi Azure AD app model v2.0 omówienie][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
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

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
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
