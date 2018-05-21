---
title: Usługa Azure Active Directory dla deweloperów | Microsoft Docs
description: Ten artykuł zawiera omówienie logowania się na konta służbowe Microsoft przy użyciu usługi Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f0f577e2ff18645cfa93763f2345ef4230907e09
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-for-developers"></a>Usługa Azure Active Directory dla deweloperów

Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami w chmurze, która umożliwia deweloperom tworzenie aplikacji zapewniających bezpieczne logowanie użytkowników mających konta służbowe Microsoft. Usługa Azure AD obsługuje zarówno deweloperów tworzących aplikacje biznesowe dla pojedynczej dzierżawy, jak i deweloperów chcących tworzyć aplikacje wielodostępne. Oprócz podstawowego logowania usługa Azure AD umożliwia również aplikacjom wywoływanie interfejsów API firmy Microsoft, takich jak [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview), a także niestandardowych interfejsów API opartych na platformie Azure AD. W tej dokumentacji pokazano, jak dodać obsługę usługi Azure AD do własnej aplikacji, używając standardowych w branży protokołów, takich jak OAuth 2.0 i OpenID Connect.

> [!NOTE]
> Większość zawartości na tej stronie koncentruje się na punkcie końcowym usługi Azure AD w wersji 1.0, który obsługuje tylko konta służbowe Microsoft. Jeśli chcesz zarejestrować konta użytkownika lub osobiste konta Microsoft, zobacz dodatkowe informacje na temat [punktu końcowego usługi Azure AD w wersji 2.0](active-directory-appmodel-v2-overview.md). Punkt końcowy usługi Azure AD w wersji 2.0 oferuje ujednolicone środowisko deweloperów dla aplikacji, które chcesz zarejestrować na kontach użytkowników z kontami usługi Azure AD (służbowymi) i osobistymi kontami Microsoft.

| | |
| --- | --- |
|[Informacje podstawowe o uwierzytelnianiu](active-directory-authentication-scenarios.md) | Wprowadzenie do uwierzytelniania za pomocą usługi Azure AD. |
|[Typy aplikacji](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Przegląd scenariuszy uwierzytelniania obsługiwanych przez usługę Azure AD. |      
| | |

## <a name="get-started"></a>Rozpoczęcie pracy
Przedstawione poniżej instalatory z przewodnikiem prowadzą użytkownika krok po kroku przez proces tworzenia aplikacji na preferowanej platformie przy użyciu zestawu SDK biblioteki usługi Azure AD Authentication Library (ADAL). Jeśli szukasz informacji na temat korzystania z biblioteki Microsoft Authentication Library (MSAL), zobacz dokumentację dotyczącą [punktu końcowego usługi Azure AD w wersji 2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplikacje mobilne i klasyczne](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Aplikacje mobilne i klasyczne</center> | [Omówienie](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Aplikacje internetowe](./media/active-directory-developers-guide/Web_app.png)<br />Aplikacje internetowe</center> | [Omówienie](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) | |
| <center>![Aplikacje jednostronicowe](./media/active-directory-developers-guide/SPA.png)<br />Aplikacje jednostronicowe</center> | [Omówienie](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Interfejsy API sieci Web](./media/active-directory-developers-guide/Web_API.png)<br />Interfejsy API sieci Web</center> | [Omówienie](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Usługa-usługa](./media/active-directory-developers-guide/Service_App.png)<br />Usługa-usługa</center> | [Omówienie](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Przewodniki z instrukcjami
Te przewodniki prowadzą użytkownika przez niektóre z najczęściej wykonywanych zadań w usłudze Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Rejestracja aplikacji](active-directory-integrating-applications.md)           | Jak zarejestrować aplikację w usłudze Azure AD. |
|[Aplikacje wielodostępne](active-directory-devhowto-multi-tenant-overview.md)    | Jak zalogować dowolne konto służbowe Microsoft. |
|[Protokoły OAuth i OpenID Connect](active-directory-protocols-openid-connect-code.md)| Jak logować użytkowników i wywoływać interfejsy API sieci Web za pomocą protokołów uwierzytelniania firmy Microsoft. |
|[Dodatkowe przewodniki](active-directory-developers-guide-index.md#guides)        |  Lista dostępnych przewodników dotyczących usługi Azure AD. |
|  |  |

## <a name="reference-topics"></a>Tematy informacyjne
Następujące artykuły zawierają szczegółowe informacje o interfejsach API, komunikatach protokołów i terminach używanych w usłudze Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Biblioteki uwierzytelniania (ADAL)](active-directory-authentication-libraries.md)   | Omówienie bibliotek i zestawów SDK dostarczanych przez usługę Azure AD. |
| [Przykłady kodu](active-directory-code-samples.md)                                  | Lista wszystkich przykładów kodu usługi Azure AD. |
| [Słownik](active-directory-dev-glossary.md)                                      | Terminologia i definicje słów używanych w tej dokumentacji. |
| [Dodatkowe tematy informacyjne](active-directory-developers-guide-index.md#reference)| Lista dostępnych tematów informacyjnych usługi Azure AD. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
