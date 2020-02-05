---
title: Przykłady kodu usługi Azure Active Directory B2C | Microsoft Docs
description: Przykłady kodu mobilnych, klasycznych, internetowych i jednostronicowych aplikacji usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 69e6b7beecb95c0e89b60640353155fc7802a881
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982946"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Przykłady kodu usługi Azure Active Directory B2C

Poniższe tabele zawierają linki do przykładów dla aplikacji systemu iOS i Android oraz platform .NET i Node.js.

## <a name="mobile-and-desktop-apps"></a>Aplikacje mobilne i klasyczne

| Przykład | Opis |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Przykład dla systemu iOS w języku Swift, który uwierzytelnia użytkowników usługi Azure AD B2C i wywołuje interfejs API przy użyciu protokołu OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | Prosta aplikacja systemu Android przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania użytkowników za pośrednictwem usługi Azure Active Directory B2C oraz do uzyskiwania dostępu do internetowego interfejsu API z tokenami wynikowymi. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Przykład przedstawiający sposób użycia biblioteki innej firmy do kompilowania aplikacji systemu iOS w języku Objective-C, która uwierzytelnia użytkowników tożsamości firmy Microsoft w naszej usłudze tożsamości Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Przykład przedstawiający sposób użycia biblioteki innej firmy do kompilowania aplikacji systemu Android, która uwierzytelnia użytkowników tożsamości firmy Microsoft w naszej usłudze tożsamości Azure AD B2C i wywołuje internetowy interfejs API za pomocą tokenów dostępu standardu OAuth 2.0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Przykład przedstawiający sposób użycia aplikacji klasycznej systemu Windows na platformie .NET (WPF) do logowania użytkownika za pomocą usługi Azure AD B2C, uzyskiwania tokenu dostępu przy użyciu platformy MSAL.NET i wywoływania interfejsu API. | 
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Prosta aplikacja Xamarin Forms przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania użytkowników za pośrednictwem usługi Azure Active Directory B2C oraz do uzyskiwania dostępu do internetowego interfejsu API z tokenami wynikowymi. |

## <a name="web-apps-and-apis"></a>Aplikacje internetowe i interfejsy API

| Przykład | Opis |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Połączony przykład dotyczący internetowej aplikacji platformy .NET, która wywołuje internetowy interfejs API platformy .NET, a obydwa rozwiązania są zabezpieczane przy użyciu usługi Azure AD B2C. |
| [dotnetcore-webapp](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | Internetowa aplikacja platformy ASP.NET Core, która może zalogować użytkownika za pomocą usługi Azure AD B2C, uzyskać token dostępu przy użyciu platformy MSAL.NET i wywołać interfejs API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Aplikacja Node.js, która zapewnia szybki i łatwy sposób konfigurowania aplikacji internetowej w usłudze Express przy użyciu protokołu OpenID Connect. |
| [javascript-nodejs-webapp](https://github.com/AzureADQuickStarts/active-directory-b2c-javascript-nodejs-webapp) | Serwer środowiska node.js, który zapewnia szybki i łatwy sposób konfigurowania usługi interfejsu API REST przy użyciu protokołu OAuth2. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Niewielki internetowy interfejs API środowiska node.js interfejsu API dla usługi Azure AD B2C, który pokazuje, jak chronić internetowy interfejs API i akceptować tokeny dostępu B2C przy użyciu biblioteki passport.js. |
| [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | DoB2C się, jak zintegrować platformę tożsamości firmy Microsoft z aplikacją sieci Web w języku Python.  |

## <a name="single-page-apps"></a>Aplikacje jednostronicowe

| Przykład | Opis |
|--------| ----------- |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Aplikacja jednostronicowa (SPA) wywołująca internetowy interfejs API. Uwierzytelnianie jest przeprowadzane przy użyciu usługi Azure AD B2C w środowisku MSAL.js. | 
| [javascript-hellojs-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp) | Jednostronicowa aplikacja implementowana przy użyciu zaplecza internetowego interfejsu API platformy ASP.NET, która umożliwia logowanie użytkowników i tworzenie nowych kont przy użyciu usługi Azure AD B2C oraz wywołuje internetowy interfejs API przy użyciu tokenów dostępu protokołu OAuth 2.0. |
