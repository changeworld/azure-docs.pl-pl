---
title: Protokoły uwierzytelniania platformy tożsamości firmy Microsoft
description: Omówienie protokołów uwierzytelniania obsługiwanych przez platformę tożsamości firmy Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884685"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protokoły uwierzytelniania platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft obsługuje kilka najczęściej używanych protokołów uwierzytelniania i autoryzacji. Tematy w tej sekcji opisano obsługiwane protokoły i ich implementacji na platformie tożsamości firmy Microsoft. Tematy obejmowały przegląd obsługiwanych typów oświadczeń, wprowadzenie do korzystania z metadanych federacji, szczegółowe OAuth 2.0. i dokumentację referencyjną protokołu SAML 2.0 oraz sekcję rozwiązywania problemów.

## <a name="authentication-protocols-articles-and-reference"></a>Protokoły uwierzytelniania artykuły i odwołania

* [Ważne informacje dotyczące podpisywania klucza rollover na platformie tożsamości firmy Microsoft](active-directory-signing-key-rollover.md) — dowiedz się więcej o rytmie rolowania kluczy podpisywania platformy tożsamości firmy Microsoft, zmianach, które można wprowadzić w celu automatycznej aktualizacji klucza, oraz omówieniu sposobu aktualizowania najbardziej typowych scenariuszy aplikacji.
* [Obsługiwane typy tokenów i oświadczeń](id-tokens.md) — dowiedz się więcej o roszczeniach w tokenach, które problemują z platformą tożsamości firmy Microsoft.
* [OAuth 2.0 na platformie tożsamości firmy Microsoft](v2-oauth2-auth-code-flow.md) — dowiedz się więcej o implementacji OAuth 2.0 na platformie tożsamości firmy Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) — dowiedz się, jak używać protokołu autoryzacji OAuth 2.0 do uwierzytelniania.
* [Service to Service Calls with Client Credentials](v2-oauth2-client-creds-grant-flow.md) — dowiedz się, jak używać poświadczeń klienta OAuth 2.0 przepływ dotacji dla usługi do wywołań usługi.
* [Service to Service połączeń z w imieniu przepływu](v2-oauth2-on-behalf-of-flow.md) — dowiedz się, jak używać OAuth 2.0 w imieniu przepływu do obsługi wywołań.
* [Odwołanie do protokołu SAML](active-directory-saml-protocol-reference.md) — dowiedz się więcej o profilach saml logowania jednokrotnego i wylogowania jednokrotnego platformy tożsamości firmy Microsoft.

## <a name="see-also"></a>Zobacz też

* [Omówienie platformy tożsamości firmy Microsoft](v2-overview.md)
* [Przykłady kodu usługi Active Directory](sample-v2-code.md)
