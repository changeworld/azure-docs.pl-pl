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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591214"
---
# <a name="azure-active-directory-for-developers"></a>Usługa Azure Active Directory dla deweloperów

Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami w chmurze, która umożliwia deweloperom tworzenie aplikacji zapewniających bezpieczne logowanie użytkowników mających konta służbowe Microsoft. Usługa Azure AD obsługuje zarówno deweloperów tworzących aplikacje biznesowe dla pojedynczej dzierżawy, jak i deweloperów chcących tworzyć aplikacje wielodostępne. Oprócz podstawowego logowania usługa Azure AD umożliwia również aplikacjom wywoływanie interfejsów API firmy Microsoft, takich jak [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview), a także niestandardowych interfejsów API opartych na platformie Azure AD. W tej dokumentacji pokazano, jak dodać obsługę usługi Azure AD do własnej aplikacji, używając standardowych w branży protokołów, takich jak OAuth 2.0 i OpenID Connect.

> [!NOTE]
> Większość zawartości na tej stronie koncentruje się na punkcie końcowym usługi Azure AD w wersji 1.0, który obsługuje tylko konta służbowe Microsoft. Jeśli chcesz zarejestrować konta użytkownika lub osobiste konta Microsoft, zobacz dodatkowe informacje na temat [punktu końcowego usługi Azure AD w wersji 2.0](active-directory-appmodel-v2-overview.md). Punkt końcowy usługi Azure AD w wersji 2.0 oferuje ujednolicone środowisko deweloperów dla aplikacji, które chcesz zarejestrować na kontach użytkowników z kontami usługi Azure AD (służbowymi) i osobistymi kontami Microsoft.

| | |
| --- | --- |
|[Informacje podstawowe o uwierzytelnianiu](authentication-scenarios.md) | Wprowadzenie do uwierzytelniania za pomocą usługi Azure AD. |
|[Typy aplikacji](authentication-scenarios.md#application-types-and-scenarios) | Przegląd scenariuszy uwierzytelniania obsługiwanych przez usługę Azure AD. |      
| | |

## <a name="get-started"></a>Rozpoczęcie pracy
Przedstawione poniżej instalatory z przewodnikiem prowadzą użytkownika krok po kroku przez proces tworzenia aplikacji na preferowanej platformie przy użyciu zestawu SDK biblioteki usługi Azure AD Authentication Library (ADAL). Jeśli szukasz informacji na temat korzystania z biblioteki Microsoft Authentication Library (MSAL), zobacz dokumentację dotyczącą [punktu końcowego usługi Azure AD w wersji 2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplikacje mobilne i klasyczne](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />Aplikacje mobilne i klasyczne</center> | [Omówienie](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Aplikacje internetowe](./media/azure-ad-developers-guide/Web_app.png)<br />Aplikacje internetowe</center> | [Omówienie](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![Aplikacje jednostronicowe](./media/azure-ad-developers-guide/SPA.png)<br />Aplikacje jednostronicowe</center> | [Omówienie](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![Interfejsy API sieci Web](./media/azure-ad-developers-guide/Web_API.png)<br />Interfejsy API sieci Web</center> | [Omówienie](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.js](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![Usługa-usługa](./media/azure-ad-developers-guide/Service_App.png)<br />Usługa-usługa</center> | [Omówienie](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Przewodniki z instrukcjami
Te przewodniki prowadzą użytkownika przez niektóre z najczęściej wykonywanych zadań w usłudze Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Rejestracja aplikacji](quickstart-v1-integrate-apps-with-azure-ad.md)           | Jak zarejestrować aplikację w usłudze Azure AD. |
|[Aplikacje wielodostępne](howto-convert-app-to-be-multi-tenant.md)    | Jak zalogować dowolne konto służbowe Microsoft. |
|[Protokoły OAuth i OpenID Connect](v1-protocols-openid-connect-code.md)| Jak logować użytkowników i wywoływać interfejsy API sieci Web za pomocą protokołów uwierzytelniania firmy Microsoft. |
|  |  |

## <a name="reference-topics"></a>Tematy informacyjne
Następujące artykuły zawierają szczegółowe informacje o interfejsach API, komunikatach protokołów i terminach używanych w usłudze Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Biblioteki uwierzytelniania (ADAL)](active-directory-authentication-libraries.md)   | Omówienie bibliotek i zestawów SDK dostarczanych przez usługę Azure AD. |
| [Przykłady kodu](sample-v1-code.md)                                  | Lista wszystkich przykładów kodu usługi Azure AD. |
| [Słownik](developer-glossary.md)                                      | Terminologia i definicje słów używanych w tej dokumentacji. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
