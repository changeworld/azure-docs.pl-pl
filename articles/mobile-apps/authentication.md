---
title: Dodawanie uwierzytelniania do aplikacji mobilnych za pomocą usług Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług takich jak App Center, które ułatwiają Konfigurowanie uwierzytelniania użytkowników i umożliwiają uwierzytelnianie aplikacji mobilnych przy użyciu kont społecznościowych, Azure Active Directory i uwierzytelniania niestandardowego.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795759"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Dodawanie uwierzytelniania i zarządzanie tożsamościami użytkowników w aplikacjach mobilnych

Udostępnienie użytkownikowi i ich zachowania w aplikacji pozwala deweloperom lepiej zaangażować użytkowników, tworząc dostosowane do nich środowiska. Niezależnie od tego, czy jesteś deweloperem aplikacji tworzącym aplikację do współpracy dla użytkowników w organizacji, czy tworzysz następną platformę sieci społecznościowej, będziesz potrzebować metody uwierzytelniania użytkowników i zarządzania tożsamościami użytkowników. Korzystanie z usługi zarządzania tożsamościami to jedna z najważniejszych funkcji usługi zaplecza mobilnego.

Użyj następujących usług, aby włączyć uwierzytelnianie użytkowników w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
[App Center auth](/appcenter/auth/) to oparta na chmurze usługa zarządzania tożsamościami, która umożliwia deweloperom uwierzytelnianie użytkowników i zarządzanie tożsamościami użytkowników. App Center auth integruje się także z innymi częściami App Center, dzięki czemu deweloperzy mogą korzystać z tożsamości użytkownika w celu [wyświetlania danych użytkownika](/appcenter/data/index) w innych usługach, a nawet [wysyłać powiadomienia wypychane do użytkowników zamiast poszczególnych urządzeń](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Najważniejsze funkcje**
- **Obsługiwane przez Azure Active Directory B2C (Azure AD B2C)** 
    - Klasy korporacyjnej.
    - Wysoka dostępność.
    - Usługa zabezpieczona i globalna.
- Korzystaj **z własnej tożsamości** i używaj innych popularnych dostawców zarządzania tożsamościami i dostępem, takich jak rozwiązanie Auth0 i Firebase.
- **Obsługa usługi AAD** 
    - Połącz istniejących dzierżawców usługi AAD. 
    - Włącz uwierzytelnianie w domenie firmowej.
    - Zarządzanie dostępem do poufnych danych.
- **Proste środowisko użytkownika** i środowisko Magical SDK dzięki zapakowaniu biblioteki MSAL z zestawem SDK App Center.
- **Obsługa platform** — iOS, Android, Xamarin, reaguje na natywne.

**Wołują**
- [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Wprowadzenie do uwierzytelniania App Center](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) to usługa zarządzania tożsamościami między firmami, która umożliwia deweloperom uwierzytelnianie klientów. Ta usługa białych etykiet pozwala deweloperom na dostosowywanie i kontrolowanie sposobu, w jaki użytkownicy bezpiecznie współdziałają z aplikacjami sieci Web, komputerami przenośnymi, mobilnymi i jednostronicowymi. Za pomocą usługi Azure AD B2C użytkownicy mogą tworzyć konta, logować się, resetować hasła i edytować profile. W usłudze Azure AD B2C zaimplementowano formę protokołów OpenID Connect i OAuth 2.0. 

**Najważniejsze funkcje**
- Bezpieczne uwierzytelnianie klientów przy użyciu preferowanego dostawcy tożsamości.
- **Zarządzanie tożsamościami i dostępem klientów**.
- Obsługiwane są nazwy logowania społecznościowego, takie jak Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo.
- Połącz się z kontami użytkowników przy użyciu **standardowych protokołów** , takich jak OpenID Connect Connect lub SAML, aby umożliwić zarządzanie tożsamościami na różnych platformach.
- Zapewnianie firmowych rejestracji i logowania.
- Łatwa integracja z bazami danych programu CRM, narzędziami do analizy marketingowej i systemami weryfikacji kont.
- Przechwytuj dane logowania, preferencji i konwersji dla klientów.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Dokumentacja Azure AD B2C](/azure/active-directory-b2c/)
- [Przewodniki Szybki start](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Przykłady](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Usługa Active Directory systemu Azure
[Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) to usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft, która pomaga pracownikom w logowaniu i dostępie.
- Zasoby zewnętrzne, takie jak usługa Microsoft Office 365, witryna Azure Portal i tysiące innych aplikacji SaaS.
- Zasoby wewnętrzne, takie jak aplikacje w sieci firmowej i intranecie, a także aplikacje w chmurze opracowane przez organizację.

**Najważniejsze funkcje**
- **Bezproblemowe i wysoce bezpieczny dostęp** dzięki połączeniu użytkowników z wymaganymi aplikacjami.
- **Kompleksowa ochrona tożsamości** oraz lepsze zabezpieczenia tożsamości i dostępu oparte na użytkowniku, lokalizacji, urządzeniu, danych i kontekście aplikacji.
- **Tysiące wstępnie zintegrowanych aplikacji** zarówno komercyjnych, jak i niestandardowych aplikacji, takich jak Office 365, Salesforce.com i Box.
- **Zarządzanie dostępem w dużej skali**.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Co to jest usługa Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rozpoczynanie pracy z usługą Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Przewodniki Szybki start](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)