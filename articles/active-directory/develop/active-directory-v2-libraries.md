---
title: Biblioteki uwierzytelniania v2.0 w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zgodny klient biblioteki i bibliotek oprogramowania pośredniczącego serwera i powiązane biblioteki, źródła i łączy próbek, dla punktu końcowego v2.0 usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0d9e2831f9d8676eb3e7fac91c58f3977f2e0f32
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Biblioteki uwierzytelniania v2.0 w usłudze Azure Active Directory
[Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) obsługuje standardowych protokołów uwierzytelniania OAuth 2.0 i OpenID Connect 1.0. Można użyć różnych bibliotek firmy Microsoft i innych organizacjach z punktem końcowym v2.0.

Podczas kompilowania aplikacji korzystającej z punktem końcowym v2.0, zalecane jest użycie bibliotek, które są zapisywane przez ekspertów domeny protokołu, Obserwujący metodologii Security Development Lifecycle (SDL), są tak samo, jak [co następuje Microsoft][Microsoft-SDL]. Jeśli zdecydujesz się kodu strony Obsługa protokołów, firma Microsoft zaleca wykonaj metodologii SDL i zwracać szczególną uwagę na zagadnienia dotyczące zabezpieczeń w specyfikacji standardów dla każdego protokołu.

> [!NOTE]
> Szukasz bibliotek w wersji 1.0 usługi Azure AD (ADAL)? Wyewidencjonowanie [przewodnik bibliotekę ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Rodzaje bibliotek
Punktu końcowego v2.0 usługi Azure AD współpracuje z dwóch typów biblioteki:

* **Bibliotek klienckich**. Natywny klienci i serwery umożliwia pobieranie tokenów dostępu do wywoływania zasobów, takich jak Microsoft Graph bibliotek klienckich.
* **Biblioteki oprogramowania pośredniczącego serwera**. Aplikacje sieci Web korzystać bibliotek oprogramowania pośredniczącego serwera dla logowania użytkownika. Interfejsy API sieci Web umożliwia sprawdzania poprawności tokenów, które są wysyłane przez klientów natywnych lub przez inne serwery bibliotek oprogramowania pośredniczącego serwera.

## <a name="library-support"></a>Obsługa biblioteki
Ponieważ można wybrać wszystkie biblioteki zgodny ze standardami, korzystając z punktem końcowym v2.0, należy wiedzieć, gdzie przejdź do pomocy technicznej. Problemy i żądania funkcji w kodzie biblioteki skontaktuj się z właścicielem biblioteki. Funkcja żądania w implementacji protokołu po stronie serwera i problemów skontaktuj się z firmy Microsoft.

Biblioteki są dostępne w dwóch kategorii pomocy technicznej:

* **Obsługiwane przez firmę Microsoft**. Firma Microsoft oferuje poprawki dla bibliotek i wykonaniu SDL termin starannością na tych bibliotek.
* **Zgodne**. Microsoft przetestowała te biblioteki w podstawowe scenariusze i potwierdza, że działają z punktem końcowym v2.0. Firma Microsoft zapewnia poprawki dotyczące biblioteki i nie przeprowadził Przegląd te biblioteki. Problemy i żądania funkcji powinny być kierowane do biblioteki projektu open source.

Lista bibliotek, które współpracują z punktem końcowym v2.0 zobacz sekcje dalej w tym artykule.


## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienta obsługiwane przez firmę Microsoft

> [!IMPORTANT]
> Biblioteki Podgląd MSAL są odpowiednie do użycia w środowisku produkcyjnym. Udostępniamy tego samego poziomu wsparcia produkcji dla bibliotek, tak jak skorzystać z bibliotek produkcji bieżącego (ADAL). W wersji zapoznawczej firma Microsoft może zmieniać MSAL API, format wewnętrznej pamięci podręcznej i innych mechanizmów te biblioteki bez powiadomienia, które będzie wymagane do wykonania wraz z poprawki lub ulepszenia funkcji. Może to mieć wpływ na aplikację. Na przykład zmianę formatu pamięci podręcznej może mieć wpływ na użytkowników, np. wymaganie je, aby zalogować się ponownie. Zmiany interfejsu API może wymagać zaktualizuj kod. Podając wersji ogólnodostępnej firma Microsoft będzie wymagać aktualizacji do wersji ogólnodostępnej w ciągu sześciu miesięcy, jako aplikacje napisane przy użyciu podglądu wersji biblioteki mogą przestać działać.

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Sklep Windows, platformy uniwersalnej systemu Windows, Xamarin iOS i Android | MSAL .NET (wersja zapoznawcza) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikacja klasyczna](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (wersja zapoznawcza) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Jednej strony aplikacji](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (wersja zapoznawcza) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikacja systemu iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (wersja zapoznawcza) | [Centralne repozytorium](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikacji systemu android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Biblioteki oprogramowania pośredniczącego serwera obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect oprogramowania pośredniczącego |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikacji MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Oprogramowanie pośredniczące elementu nośnego OAuth OWIN do AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | Program obsługi tokenów JWT dla platformy .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Oprogramowanie pośredniczące ASP.NET OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Zabezpieczenia programu ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplikacji MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Oprogramowanie pośredniczące elementu nośnego OAuth ASP.NET |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Zabezpieczenia programu ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Program obsługi tokenów JWT dla platformy .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD usługi Passport. |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplikacja sieci Web](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Zgodny klient biblioteki
| Platforma | Nazwa biblioteki | Wersja przetestowany | Kod źródłowy | Sample |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Przykładowej aplikacji natywnej](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Przykładowej aplikacji natywnej](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribejava Sekretarz Java](https://github.com/scribejava/scribejava) | [Wersja 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [PHP Ligi oauth2 klienta](https://github.com/thephpleague/oauth2-client) | [Wersja 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [Klient protokołu oauth2](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
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
