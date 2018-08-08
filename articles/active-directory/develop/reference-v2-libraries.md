---
title: Biblioteki uwierzytelniania v2.0 usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Biblioteki klienckie zgodne i bibliotek oprogramowania pośredniczącego serwera i powiązane biblioteki, źródła i łącza przykłady punktu końcowego v2.0 usługi Azure Active Directory.
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
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 1450cffca7a4cfa57856c75cdcc9e106958ea043
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601675"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Biblioteki uwierzytelniania v2.0 usługi Azure Active Directory

[Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) obsługuje standardowe protokoły OAuth 2.0 i OpenID Connect 1.0. Biblioteka Microsoft Authentication Library (MSAL) jest przeznaczona do pracy z punktem końcowym v2.0 usługi Azure AD. Istnieje również możliwość użycia bibliotek typu open source, które obsługują protokołu OAuth 2.0 i OpenID Connect 1.0.

Zaleca się, że używasz bibliotek napisanych przez protokół domeny eksperci, postępuj zgodnie z metodologii cykl projektowania zabezpieczeń (SDL), takie jak [co następuje Microsoft][Microsoft-SDL]. Jeśli zdecydujesz się dostępne wsparcie dla protokołów, postępuj zgodnie z metodologii w jak SDL i płatności firmy Microsoft należy zamknąć uwagę zagadnienia dotyczące zabezpieczeń w specyfikacji standardów dla każdego protokołu.

> [!NOTE]
> Szukasz biblioteki w wersji 1.0 usługi Azure AD (ADAL)? Wyewidencjonuj [przewodnik biblioteki ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Rodzaje bibliotek

Punkt końcowy v2.0 usługi Azure AD działa z dwoma typami biblioteki:

* **Biblioteki klienckie**. Serwery i klientów natywnych użyj biblioteki klienckie uzyskiwanie tokenów dostępu do wywoływania zasobu, np. Microsoft Graph.
* **Biblioteki oprogramowania pośredniczącego serwera**. Aplikacje sieci Web możliwe używanie bibliotek oprogramowania pośredniczącego serwera podczas logowania użytkownika. Interfejsy API sieci Web możliwe używanie bibliotek oprogramowania pośredniczącego serwera do sprawdzania poprawności tokenów, które są wysyłane przez klientów natywnych lub przez inne serwery.

## <a name="library-support"></a>Obsługa bibliotek

Ponieważ można wybrać wszystkie biblioteki zgodnych ze standardami, korzystając z punktu końcowego v2.0, warto wiedzieć, gdzie szukać pomocy technicznej. Problemy i sugestie funkcji w kodzie biblioteki skontaktuj się z właścicielem biblioteki. Problemy i sugestie funkcji w implementacji protokołu po stronie usługi skontaktuj się z pomocą firmy Microsoft. [Plik wniosek dotyczący funkcji](https://feedback.azure.com/forums/169401-azure-active-directory) dodatkowych funkcji, które chcesz zobaczyć w protokole. [Utwórz żądanie obsługi](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request) Jeśli znajdziesz problem gdzie punktu końcowego v2.0 usługi Azure AD nie jest zgodna z protokołu OAuth 2.0 lub OpenID Connect 1.0.

Biblioteki są dostępne w dwóch kategoriach pomocy technicznej:

* **Obsługiwane przez firmę Microsoft**. Firma Microsoft oferuje poprawki dla tych bibliotek i wykonaniu SDL należytą starannością na tych bibliotek.
* **Zgodne**. Microsoft przetestowała tych bibliotek w podstawowych scenariuszy i potwierdza, że pracują z punktem końcowym v2.0. Firma Microsoft udostępnia poprawki dla tych bibliotek i nie zrobił przeglądu tych bibliotek. Problemy i sugestie funkcji powinny być kierowane do biblioteki typu open-source projektu.

Aby uzyskać listę bibliotek, które działają z punktem końcowym v2.0 zobacz następne sekcje w tym artykule.

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienckie obsługiwane przez firmę Microsoft

> [!IMPORTANT]
> Biblioteki MSAL (wersja zapoznawcza) są odpowiednie do użycia w środowisku produkcyjnym. Firma Microsoft udostępnia ten sam poziom obsługi produkcji dla tych bibliotek jako bieżących bibliotek produkcyjnych (ADAL). W trakcie okresu zapoznawczego należy oczekiwać zmiany interfejsu API biblioteki MSAL, format wewnętrznej pamięci podręcznej i innych mechanizmów tych bibliotek, bez powiadomienia, który trzeba będzie być wraz z poprawki lub ulepszenia funkcji. Może to mieć wpływ na aplikację. Na przykład zmianę formatu pamięci podręcznej może wymagać użytkowników, aby zalogować się ponownie. Zmiany interfejsu API może wymagać aktualizacji kodu. Po udostępnieniu wersji ogólna dostępność (GA) należy zaktualizować wszystkie aplikacje przy użyciu wersji zapoznawczej, biblioteki, w ciągu sześciu miesięcy lub ich może ona przestać działać.

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| Klient modelu .NET, Windows Store, platformy uniwersalnej systemu Windows, platformy Xamarin dla systemów iOS i Android | Biblioteka MSAL .NET (wersja zapoznawcza) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikacja klasyczna](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (wersja zapoznawcza) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplikacja jednostronicowa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | Biblioteka MSAL (wersja zapoznawcza) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikacja systemu iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | Biblioteka MSAL (wersja zapoznawcza) | [Centralne repozytorium](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikacja dla systemu android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Biblioteki oprogramowania pośredniczącego serwera obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Do pobrania | Kod źródłowy | Sample | Informacje ogólne
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Pośredniczącym OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[Aplikacja MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Oprogramowanie pośredniczące elementu nośnego OAuth OWIN, dla usługi Azure AD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | Rozszerzenie JWT Handler dla platformy .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Oprogramowania pośredniczącego OpenID Connect w ASP.NET |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Zabezpieczenia programu ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplikacja MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Oprogramowanie pośredniczące elementu nośnego OAuth platformy ASP.NET |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Zabezpieczenia programu ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Rozszerzenie JWT Handler dla platformy .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Usługa Azure AD usługi Passport. |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplikacja sieci Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>Biblioteki klienckie zgodne

| Platforma | Nazwa biblioteki | Przetestowanej wersji | Kod źródłowy | Sample |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Przykład aplikacji natywnej](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Przykład aplikacji natywnej](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribejava Sekretarz Java](https://github.com/scribejava/scribejava) | [Wersja 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [PHP Ligi klienta protokołu oauth2](https://github.com/thephpleague/oauth2-client) | [W wersji 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [Klient protokołu oauth2](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Powiązana zawartość
Aby uzyskać więcej informacji na temat punktu końcowego v2.0 usługi Azure AD, zobacz [usługi Azure AD app model v2.0 Przegląd][AAD-App-Model-V2-Overview].

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
