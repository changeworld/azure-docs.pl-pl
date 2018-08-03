---
title: Protokoły uwierzytelniania w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Omówienie protokołów uwierzytelniania obsługiwane przez usługi Azure Active Directory (AD)
documentationcenter: dev-center-name
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 81626cac41b1733f49e7fe1254c6b2bf8f5281cb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442286"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protokoły uwierzytelniania w usłudze Azure Active Directory
Azure Active Directory (Azure AD) obsługuje niektóre z najczęściej używanych protokołów uwierzytelniania i autoryzacji. Tematy w tej sekcji opisano obsługiwane protokoły i ich wdrażania w usłudze Azure AD. Tematy zawarte recenzję obsługiwane typy oświadczeń, wprowadzenie do korzystania z metadanych Federacji, szczegółowe OAuth 2.0. i dokumentację referencyjną protokołu SAML 2.0 i sekcją dotycząca rozwiązywania problemów.

## <a name="authentication-protocols-articles-and-reference"></a>Protokoły uwierzytelniania, artykuły i dokumentacja
* [Ważne informacje dotyczące podpisywania Przerzucanie klucza w usłudze Azure AD](active-directory-signing-key-rollover.md) — Dowiedz się więcej o usłudze Azure AD podpisywania kadencji przerzucania klucza, zmiany wprowadzone na automatyczne aktualizowanie klucza i dyskusji instrukcje aktualizowania najbardziej typowych scenariuszach aplikacji.
* [Obsługiwane tokeny i oświadczenia](active-directory-token-and-claims.md) — Dowiedz się więcej o oświadczenia w tokeny wystawianych przez urząd usługi Azure AD.
* [Metadane Federacji](active-directory-federation-metadata.md) — Dowiedz się, jak znajdować i interpretować dokumentów metadanych, które generuje w usłudze Azure AD.
* [OAuth 2.0 w usłudze Azure AD](active-directory-protocols-oauth-code.md) — Dowiedz się więcej o implementacji protokołu OAuth 2.0 w usłudze Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) — Dowiedz się, jak używać protokołu OAuth 2.0, protokół uwierzytelniania.
* [Wywołania usług przy użyciu poświadczeń klienta](active-directory-protocols-oauth-service-to-service.md) — Dowiedz się, jak używać przepływie przyznawania poświadczeń klienta OAuth 2.0 dla wywołań usług.
* [Wywołania usług za pomocą usługi Flow "w imieniu"](active-directory-protocols-oauth-on-behalf-of.md) — Dowiedz się, jak korzystać z przepływu OAuth 2.0 "w imieniu" dla wywołań usług.
* [Referencyjne protokołu SAML](active-directory-saml-protocol-reference.md) — Dowiedz się więcej o profilach usługi Azure AD logowania jednokrotnego i jednym SAML wylogowania.

## <a name="see-also"></a>Zobacz też
[Przewodnik dewelopera usługi Azure Active Directory](azure-ad-developers-guide.md)

[Przykłady kodu usługi Active Directory](active-directory-code-samples.md)
