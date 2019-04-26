---
title: Informacje o platformie tożsamości firmy Microsoft | Azure
description: Informacje na temat platformy tożsamości firmy Microsoft, stanowiącej unowocześnienie platformy deweloperów i usługi tożsamości Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b3eee08c036862e6ce9f0c590a596f7b1d3fb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301031"
---
# <a name="about-microsoft-identity-platform"></a>Informacje o platformie tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft to unowocześnienie platformy deweloperów i usługi tożsamości Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania programu Microsoft Graph, innych interfejsów API firmy Microsoft lub interfejsów API opracowanych przez deweloperów. Jest to kompleksowa platforma składająca się z usługi uwierzytelniania, bibliotek typu open source, funkcji rejestrowania i konfigurowania aplikacji (za pośrednictwem portalu dla deweloperów oraz interfejsu API aplikacji), pełnej dokumentacji dla deweloperów, przykładów kodu oraz innej zawartości dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

Do tej pory większość deweloperów używała platformy usługi Azure AD w wersji 1.0 w celu uwierzytelniania tożsamości usługi Azure AD (kont służbowych) przez żądanie tokenów z punktu końcowego usługi Azure AD w wersji 1.0, korzystając z biblioteki Azure Active Directory Authentication Library (ADAL), z witryny Azure Portal do rejestracji i konfiguracji aplikacji oraz z interfejsu API programu Graph dla usługi Azure AD do programowej konfiguracji aplikacji. Platforma Azure AD w wersji 1.0 to dojrzała platforma, która będzie nadal działała w przypadku aplikacji dla przedsiębiorstw.

Aby rozszerzyć i rozwinąć możliwości platformy tożsamości firmy Microsoft, umożliwiono uwierzytelnianie szerszej gamy tożsamości firmy Microsoft (takich jak tożsamości usługi Azure AD, konta Microsoft — na przykład konta outlook.com i hotmail.com — oraz konta społecznościowe i lokalne uwierzytelniane za pośrednictwem usługi Azure AD B2C) za pomocą punktu końcowego usługi Azure AD w wersji 2.0. W takim przypadku będziesz używać biblioteki Microsoft Authentication Library (MSAL) lub dowolnej biblioteki typu open source dla protokołu OAuth 2.0 lub OpenID Connect, witryny Azure Portal do rejestracji i konfiguracji aplikacji oraz interfejsu API programu Microsoft Graph do programowej konfiguracji aplikacji. Zaktualizowana platforma tożsamości firmy Microsoft (a w szczególności biblioteki MSAL i najnowsze środowisko rejestracji aplikacji w witrynie Azure Portal) została znacząco unowocześniona w ciągu ostatniego roku. W celu sfinalizowania tej wersji zachęcamy deweloperów do tworzenia i testowania aplikacji z użyciem najnowszej wersji platformy tożsamości firmy Microsoft.

W przypadku aplikacji korzystających z najnowszych wersji bibliotek ADAL i MSAL możliwe jest logowanie jednokrotne między aplikacjami. W przypadku aplikacji zaktualizowanych z biblioteki ADAL do MSAL będzie utrzymywany stan logowania użytkownika. Deweloperzy mogą samodzielnie decydować o aktualizacji aplikacji do biblioteki MSAL, ponieważ aplikacje utworzone z użyciem biblioteki ADAL będą nadal obsługiwane.

## <a name="microsoft-identity-platform-experience"></a>Środowisko platformy tożsamości firmy Microsoft

Na poniższym diagramie przedstawiono ogólnie środowisko obsługi tożsamości firmy Microsoft, obejmujące środowisko rejestracji aplikacji, zestawy SDK, punkty końcowe i obsługiwane tożsamości.

![Platforma tożsamości firmy Microsoft obecnie](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

Platforma tożsamości firmy Microsoft obejmuje dwa punkty końcowe (w wersji 1.0 i 2.0) oraz dwa zestawy bibliotek klienta do obsługi tych punktów końcowych. Podczas tworzenia nowej aplikacji należy wziąć pod uwagę zalety i aktualny stan tych punktów końcowych oraz bibliotek uwierzytelniania. Rozważ również następujące elementy:

* Obsługiwane platformy

    * [Biblioteka ADAL](active-directory-authentication-libraries.md) obsługuje platformy .NET, JavaScript, iOS, Android, Java i Python
    * [Wersja zapoznawcza biblioteki MSAL](reference-v2-libraries.md) obsługuje platformy .NET, JavaScript, iOS i Android
    * Oba punkty końcowe obsługują oprogramowanie pośredniczące serwera .NET i Node.js do ochrony interfejsów API i funkcji logowania. 

* Większość innowacji, takich jak dynamiczne i przyrostowe wyrażanie zgody, dotyczy punktu końcowego w wersji 2.0 i biblioteki MSAL, ale punkt końcowy w wersji 1.0 i biblioteka ADAL są nadal obsługiwane.

    W witrynie Azure Portal konieczna była wcześniej statyczna identyfikacja wszystkich zakresów wymaganych przez aplikację. Punkt końcowy w wersji 2.0 oraz powiązane z nim portale umożliwiają zarówno znane wcześniej statyczne definiowanie zakresów, jak i dynamiczne żądanie zakresów odpowiednio do uprawnień wymaganych przez aplikację. W wariancie dynamicznym dostępna jest też dodatkowa funkcja opcjonalna, czyli przyrostowe wyrażanie zgody. Przyrostowe wyrażanie zgody umożliwia żądanie podzestawu zakresów podczas pierwszego uwierzytelnienia użytkownika, a następnie żądanie dodatkowych zakresów stosownie do potrzeb. 
    
    Na przykład podczas korzystania z aplikacji aparatu na urządzeniu przenośnym użytkownik zostanie poproszony o umożliwienie aplikacji dostępu do aparatu — po wyrażeniu zgody aplikacja będzie mogła uzyskać dostęp do aparatu i zrobić zdjęcie.  Gdy aplikacja będzie gotowa do zapisania nowego zdjęcia, może poprosić o zgodę uprawnienia do odczytu/zapisu zdjęć. 

* Możliwe zmiany powodujące niezgodność

    Biblioteka MSAL jest odpowiednia do użytku w środowisku produkcyjnym. Zapewniamy takie samo wsparcie tej biblioteki na poziomie produkcyjnym, jak w przypadku bieżących bibliotek produkcyjnych. W czasie obowiązywania wersji zapoznawczej możliwe jest wprowadzenie zmian w interfejsie API, wewnętrznym formacie pamięci podręcznej i innych mechanizmach tej biblioteki, które będą wprowadzane wraz z poprawkami błędów i ulepszeniami funkcji. Może to mieć wpływ na aplikację. Na przykład zmiana formatu pamięci podręcznej może wymagać od użytkowników ponownego zalogowania. Zmiany w interfejsie API mogą wymagać aktualizacji kodu. Po opublikowaniu wersji ogólnodostępnej będzie wymagana aktualizacja do wersji ogólnodostępnej w ciągu sześciu miesięcy, ponieważ aplikacje utworzone przy użyciu wersji zapoznawczej biblioteki mogą przestać działać.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat wersji 1.0 i 2.0.

* [Informacje o wersji 1.0](v1-overview.md)
* [Informacje o wersji 2.0](v2-overview.md)
