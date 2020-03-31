---
title: Omówienie usługi Azure Active Directory dla deweloperów (wersja 1.0)
description: Ten artykuł zawiera omówienie logowania na kontach służbowych i szkolnych firmy Microsoft przy użyciu punktu końcowego i platformy usługi Azure Active Directory w wersji 1.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ade350c91ebd2f3a68b52011e598f739a14c220f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154495"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Omówienie usługi Azure Active Directory dla deweloperów (wersja 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami w chmurze, która umożliwia deweloperom tworzenie aplikacji zapewniających bezpieczne logowanie użytkowników mających konta służbowe Microsoft. Usługa Azure AD obsługuje zarówno deweloperów tworzących aplikacje biznesowe dla pojedynczej dzierżawy, jak i deweloperów chcących tworzyć aplikacje wielodostępne. Oprócz podstawowego logowania usługa Azure AD umożliwia również aplikacjom wywoływanie interfejsów API firmy Microsoft, takich jak [Microsoft Graph](https://docs.microsoft.com/graph/overview), a także niestandardowych interfejsów API opartych na platformie Azure AD. W tej dokumentacji pokazano, jak dodać obsługę usługi Azure AD do własnej aplikacji, używając standardowych w branży protokołów, takich jak OAuth 2.0 i OpenID Connect.

> [!NOTE]
> Większość zawartości na tej stronie koncentruje się na punkcie końcowym i platformie w wersji 1.0, która obsługuje tylko konta służbowe firmy Microsoft lub szkoły. Jeśli chcesz zalogować się do kont Microsoft dla konsumentów lub osobistych, zobacz informacje o [punkcie końcowym i platformie w wersji 2.0](../develop/v2-overview.md). Punkt końcowy w wersji 2.0 oferuje ujednolicone środowisko deweloperskie dla aplikacji, które chcą zalogować się we wszystkich tożsamościach firmy Microsoft.

| | |
| --- | --- |
|[Podstawowe informacje o uwierzytelnianiu](v1-authentication-scenarios.md) | Wprowadzenie do uwierzytelniania za pomocą usługi Azure AD. |
|[Typy aplikacji](app-types.md) | Przegląd scenariuszy uwierzytelniania obsługiwanych przez usługę Azure AD. |
| | |

## <a name="get-started"></a>Wprowadzenie

Szybki start w wersji 1.0 i samouczki umożliwiają tworzenie aplikacji na preferowanej platformie przy użyciu zestawu SDK biblioteki uwierzytelniania usługi Azure AD (ADAL). Aby rozpocząć, zobacz **przewodniki Szybki start** w wersji **1.0 i samouczki w wersji 1.0** [na platformie tożsamości firmy Microsoft (usługa Azure Active Directory dla deweloperów).](index.yml)

## <a name="how-to-guides"></a>Przewodniki z instrukcjami

Szczegółowe informacje i wskazówki dotyczące najczęstszych zadań w usłudze Azure AD można znaleźć w przewodnikach w wersji **1.0.**

## <a name="reference-topics"></a>Tematy informacyjne

Następujące artykuły zawierają szczegółowe informacje o interfejsach API, komunikatach protokołów i terminach używanych w usłudze Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Biblioteki uwierzytelniania (ADAL)](active-directory-authentication-libraries.md)   | Omówienie bibliotek i zestawów SDK dostarczanych przez usługę Azure AD. |
| [Przykłady kodu](sample-v1-code.md)                                  | Lista wszystkich przykładów kodu usługi Azure AD. |
| [Słownik](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | Terminologia i definicje słów używanych w tej dokumentacji. |
|  |  |

## <a name="videos"></a>Filmy wideo

Zobacz [wideo dotyczące platformy deweloperów usługi Azure Active Directory,](videos.md) aby uzyskać pomoc w migracji na nową platformę tożsamości firmy Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
