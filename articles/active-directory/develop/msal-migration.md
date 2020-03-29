---
title: Migrowanie do biblioteki uwierzytelniania firmy Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania Firmy Microsoft (MSAL) a biblioteką uwierzytelniania usługi Azure AD (ADAL) oraz o tym, jak przeprowadzić migrację do usługi MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164934"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrowanie aplikacji do biblioteki uwierzytelniania firmy Microsoft (MSAL)

Zarówno Biblioteka uwierzytelniania firmy Microsoft (MSAL), jak i biblioteka uwierzytelniania usługi Azure AD (ADAL) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów współpracowała z platformą Azure AD for developers (w wersji 1.0) w celu uwierzytelnienia tożsamości usługi Azure AD (kont służbowych i szkolnych), żądając tokenów przy użyciu biblioteki uwierzytelniania usługi Azure AD (ADAL). Korzystanie z usługi MSAL:

- Można uwierzytelnić szerszy zestaw tożsamości firmy Microsoft (tożsamości usługi Azure AD i kont Microsoft oraz kont społecznościowych i lokalnych za pośrednictwem usługi Azure AD B2C), ponieważ używa punktu końcowego platformy tożsamości firmy Microsoft.
- Użytkownicy otrzymają najlepsze środowisko rejestracji jednokrotnej.
- Aplikacja może włączyć przyrostową zgodę, a obsługa dostępu warunkowego jest łatwiejsza.
- Korzystasz z innowacji.

**MSAL jest teraz zalecaną biblioteką śuchalną do użycia z platformą tożsamości firmy Microsoft.** Żadne nowe funkcje nie zostaną zaimplementowane w adal. Wysiłki koncentrują się na poprawie MSAL.

W poniższych artykułach opisano różnice między bibliotekami MSAL i ADAL i pomagają w migracji do usługi MSAL:
- [Migrowanie do platformy MSAL.NET](msal-net-migration.md)
- [Migrowanie do biblioteki MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrowanie do biblioteki MSAL.Android](migrate-android-adal-msal.md)
- [Migracja do usługi MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Migrowanie do biblioteki MSAL dla języka Python](migrate-python-adal-msal.md)
- [Migrowanie do biblioteki MSAL dla języka Java](migrate-adal-msal-java.md)
- [Migrowanie aplikacji platformy Xamarin do biblioteki MSAL.NET przy użyciu brokerów](msal-net-migration-ios-broker.md)
