---
title: Dodawanie uwierzytelniania do aplikacji mobilnych za pomocą visual studio App Center i usług platformy Azure
description: Dowiedz się więcej o usługach, takich jak Visual Studio App Center, które pomagają skonfigurować uwierzytelnianie użytkowników i umożliwić aplikacjom mobilnym uwierzytelnianie za pomocą kont społecznościowych, usługi Azure Active Directory i uwierzytelniania niestandardowego.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241051"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Dodawanie uwierzytelniania i zarządzanie tożsamościami użytkowników w aplikacjach mobilnych

Posiadanie widoku użytkownika i jego zachowania w aplikacji umożliwia deweloperom lepsze angażowanie użytkowników, tworząc dla nich dostosowane środowiska. Niezależnie od tego, czy jesteś deweloperem aplikacji, który tworzy aplikację do współpracy dla użytkowników w organizacji, czy tworzysz następną platformę sieci społecznościowych, potrzebujesz sposobu uwierzytelniania użytkowników i zarządzania tożsamościami użytkowników. Usługa zarządzania tożsamościami jest jedną z najważniejszych funkcji mobilnej usługi zaplecza.

Użyj następujących usług, aby włączyć uwierzytelnianie użytkowników w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth](/appcenter/auth/) to usługa zarządzania tożsamościami oparta na chmurze, której deweloperzy mogą używać do uwierzytelniania użytkowników i zarządzania tożsamościami użytkowników. App Center Auth integruje się również z innymi częściami programu Visual Studio App Center. Deweloperzy mogą używać tożsamości użytkownika do [wyświetlania danych użytkownika](/appcenter/data/index) w innych usługach, a nawet [wysyłania powiadomień wypychanych do użytkowników zamiast do poszczególnych urządzeń.](/appcenter/push/push-to-user#setting-user-identity) 

**Najważniejsze funkcje**
- Obsługiwane przez usługę Azure Active Directory B2C (Azure AD B2C). 
    - Klasa klasy korporacyjnej.
    - Wysoka dostępna.
    - Bezpieczna i globalna obsługa.
- Przynieś własną tożsamość i możliwość korzystania z innych popularnych dostawców zarządzania tożsamościami i dostępem, takich jak Auth0 i Firebase.
- Obsługa usługi Azure Active Directory.
    - Połącz istniejących dzierżaw usługi Azure AD. 
    - Włącz uwierzytelnianie w domenie firmowej.
    - Zarządzanie dostępem do poufnych danych.
- Proste środowisko użytkownika i magiczne środowisko SDK przez zawijanie biblioteki uwierzytelniania firmy Microsoft za pomocą sdk visual studio App Center.
- Obsługa platformy dla systemów iOS, Android, Xamarin i React Native.

**Odwołania**
- [Zarejestruj się w programie Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Wprowadzenie do aplikacji App Center Auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Usługa Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) to usługa zarządzania tożsamościami między firmami (B2C), której deweloperzy mogą używać do uwierzytelniania swoich klientów. Ta usługa white-label pozwala deweloperom dostosować i kontrolować sposób bezpiecznej interakcji użytkowników z aplikacjami internetowymi, komputerowymi, mobilnymi lub jednostronicowymi. Za pomocą usługi Azure AD B2C użytkownicy mogą tworzyć konta, logować się, resetować hasła i edytować profile. W usłudze Azure AD B2C zaimplementowano formę protokołów OpenID Connect i OAuth 2.0. 

**Najważniejsze funkcje**
- Bezpieczne uwierzytelnianie klientów za pomocą preferowanego dostawcy tożsamości.
- Zarządzaj tożsamością i dostępem klientów.
- Uzyskaj wsparcie logowania do mediów społecznościowych, takich jak Facebook, GitHub, Google, LinkedIn, Twitter, WeChat i Weibo.
- Połącz się z kontami użytkowników przy użyciu standardowych protokołów branżowych, takich jak OpenID Connect lub SAML, aby umożliwić zarządzanie tożsamościami na różnych platformach.
- Zapewnij markową rejestrację i środowisko logowania.
- Łatwa integracja z bazami danych CRM, narzędziami do analizy marketingowej i systemami weryfikacji kont.
- Przechwytywanie danych logowania, preferencji i konwersji dla klientów.

**Odwołania**
- [Portal Azure](https://portal.azure.com/)
- [Dokumentacja usługi Azure AD B2C](/azure/active-directory-b2c/)
- [Szybki start](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Próbki](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
[Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to chmurowa usługa zarządzania tożsamościami i dostępami firmy Microsoft, która pomaga pracownikom zalogować się i uzyskać dostęp do:
- Zasoby zewnętrzne, takie jak Microsoft Office 365, portal Azure i tysiące innych aplikacji jako usługi (SaaS).
- Zasoby wewnętrzne, takie jak aplikacje w sieci firmowej i intranecie, a także aplikacje w chmurze opracowane przez organizację.

**Najważniejsze funkcje**
- Bezproblemowy, wysoce bezpieczny dostęp dzięki połączeniu użytkowników z potrzebnymi im aplikacjami.
- Kompleksowa ochrona tożsamości i zwiększone zabezpieczenia tożsamości i dostępu na podstawie użytkownika, lokalizacji, urządzenia, danych i kontekstu aplikacji.
- Tysiące wstępnie zintegrowanych aplikacji dla aplikacji komercyjnych i niestandardowych, takich jak Office 365, Salesforce.com i Box.
- Możliwość zarządzania dostępem na dużą skalę.

**Odwołania**
- [Portal Azure](https://portal.azure.com/)
- [Co to jest Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rozpoczynanie pracy z usługą Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Szybki start](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)