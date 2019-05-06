---
title: Dowiedz się więcej o Microsoft Authentication Library (MSAL) | Azure
description: Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji można uzyskać tokenów w celu wywołania interfejsów API sieci Web zabezpieczonych. Te interfejsy API sieci Web może być programu Microsoft Graph, inne APIS firmy Microsoft, interfejsów API sieci Web innych firm lub własnego interfejsu API sieci Web. Biblioteka MSAL obsługuje wielu architektury aplikacji i platform.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2198a5efe276ce872487cde7b2ddb5cb00c58c50
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080898"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Omówienie programu Microsoft Authentication Library (MSAL)
Microsoft Authentication Library (MSAL) umożliwia deweloperom uzyskanie [tokenów](developer-glossary.md#security-token) z Microsoft punktu końcowego platformy tożsamości w celu uzyskania dostępu do zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web może być programu Microsoft Graph, inne APIS firmy Microsoft, interfejsów API sieci Web innych firm lub własnego interfejsu API sieci Web. Biblioteka MSAL jest dostępna dla platformy .NET, JavaScript, Android i iOS, które obsługują wielu architektur różnych aplikacji i.

Biblioteka MSAL zapewnia wiele sposobów, aby uzyskiwać tokeny przy użyciu spójnego interfejsu API dla wielu platform. Przy użyciu biblioteki MSAL zapewnia następujące korzyści:

* Nie trzeba bezpośrednio za pomocą biblioteki uwierzytelniania OAuth lub kodu na podstawie protokołu w aplikacji.
* Uzyskuje tokenów w imieniu użytkownika lub w imieniu aplikacji (jeśli ma zastosowanie do platformy).
* Obsługuje pamięć podręczną tokenu i odświeża tokenów dla Ciebie, znajdujących się blisko wygaśnięcia. Nie ma potrzeby obsługi wygaśnięcia tokenu na własną rękę.
* Pomaga określić, które odbiorcami aplikacji do logowania (Twojej organizacji, kilka organizacje, pracy oraz służbowych i osobistych kont Microsoft społecznościowych tożsamości za pomocą usługi Azure AD B2C, użytkownicy w suwerennych i chmur krajowych).
* Ułatwia konfigurowanie aplikacji z plików konfiguracji.
* Ułatwia rozwiązywanie problemów z aplikacją, zapewniając wyjątki umożliwiające działanie, rejestrowanie i dane telemetryczne.

## <a name="application-types-and-scenarios"></a>Typy aplikacji i scenariuszy
Przy użyciu biblioteki MSAL, token można kupić z różnych typów aplikacji: aplikacji sieci web, interfejsów API sieci web, aplikacji jednostronicowej (JavaScript), w przypadku aplikacji mobilnych i natywnych i demonów i aplikacji po stronie serwera. 

Biblioteka MSAL może służyć w wielu scenariuszach aplikacji, w tym następujące:

* [Aplikacji jednostronicowej (JavaScript)](scenario-spa-overview.md) 
* [Aplikacja sieci Web logowania użytkowników](scenario-web-app-sign-user-overview.md)
* [Aplikacja sieci Web logowania użytkownika i wywołuje internetowy interfejs API w imieniu użytkownika](scenario-web-app-call-api-overview.md)
* [Zabezpieczanie interfejsu API sieci web tylko uwierzytelnieni użytkownicy mogą uzyskać do niego dostęp](scenario-protected-web-api-overview.md)
* [Inny podrzędny interfejs API sieci web w imieniu zalogowanego użytkownika podczas wywoływania interfejsu API sieci Web](scenario-web-api-call-api-overview.md)
* [Aplikacja komputerowa, wywołuje internetowy interfejs API w imieniu zalogowanego użytkownika](scenario-desktop-overview.md)
* [Wywoływanie interfejsu API sieci Web w imieniu użytkownika, który jest zalogowany interaktywnie aplikacji mobilnej](scenario-mobile-overview.md).
* [Demon usługi/Pulpit aplikacji wywołanie interfejsu API sieci web w imieniu sam](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Języki i platformy

| Biblioteka | Obsługiwane platformy i struktury|
| --- | --- | 
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/>[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET framework, .NET Core, platformy Xamarin dla systemu Android, Xamarin iOS, Universal Windows Platform|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/>[MSAL.js (wersja zapoznawcza)](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript frameworks such as AngularJS, Ember.js, or Durandal.js|
| ![Biblioteka MSAL dla systemu Android](media/sample-v2-code/logo_Android.png) <br/>[Biblioteka MSAL dla systemu Android (wersja zapoznawcza)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| ![Biblioteka MSAL dla systemu iOS](media/sample-v2-code/logo_iOS.png) <br/>[BIBLIOTEKA MSAL. Objective-C (wersja zapoznawcza)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS|

## <a name="differences-between-adal-and-msal"></a>Różnice między biblioteki ADAL i biblioteki MSAL
Active Directory Authentication Library (ADAL) integruje się z usługą Azure AD dla punktu końcowego Deweloperzy (w wersji 1.0), gdzie MSAL integruje się z punktem końcowym programu Microsoft identity platformy (w wersji 2.0). Punkt końcowy w wersji 1.0 obsługuje kont służbowych, ale nie osobistych kont. Punkt końcowy v2.0 stanowi ujednolicenie kont osobistych i kont służbowych w postaci jednego systemu uwierzytelniania. Ponadto za pomocą biblioteki MSAL można również uzyskać uwierzytelnienia dla usługi Azure AD B2C.

Aby uzyskać bardziej szczegółowe informacje, przeczytaj temat [ADAL.NET migrowany platformy MSAL.NET](msal-net-migration.md) i [ADAL.js migrowany MSAL.js](msal-compare-msal-js-and-adal-js.md).

            