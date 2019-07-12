---
title: Protokoły uwierzytelniania w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Omówienie protokołów uwierzytelniania obsługiwane przez usługi Azure Active Directory (AD)
documentationcenter: dev-center-name
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964c5882432ae0637039e32ca961008e8223b6b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612581"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protokoły uwierzytelniania w usłudze Azure Active Directory
Azure Active Directory (Azure AD) obsługuje niektóre z najczęściej używanych protokołów uwierzytelniania i autoryzacji. Tematy w tej sekcji opisano obsługiwane protokoły i ich wdrażania w usłudze Azure AD. Tematy zawarte recenzję obsługiwane typy oświadczeń, wprowadzenie do korzystania z metadanych Federacji, szczegółowe OAuth 2.0. i dokumentację referencyjną protokołu SAML 2.0 i sekcją dotycząca rozwiązywania problemów.

## <a name="authentication-protocols-articles-and-reference"></a>Protokoły uwierzytelniania, artykuły i dokumentacja
* [Ważne informacje dotyczące podpisywania Przerzucanie klucza w usłudze Azure AD](active-directory-signing-key-rollover.md) — Dowiedz się więcej o usłudze Azure AD podpisywania kadencji przerzucania klucza, zmiany wprowadzone na automatyczne aktualizowanie klucza i dyskusji instrukcje aktualizowania najbardziej typowych scenariuszach aplikacji.
* [Obsługiwane tokeny i oświadczenia](v1-id-and-access-tokens.md) — Dowiedz się więcej o oświadczenia w tokeny wystawianych przez urząd usługi Azure AD.
* [Metadane Federacji](azure-ad-federation-metadata.md) — Dowiedz się, jak znajdować i interpretować dokumentów metadanych, które generuje w usłudze Azure AD.
* [OAuth 2.0 w usłudze Azure AD](v1-protocols-oauth-code.md) — Dowiedz się więcej o implementacji protokołu OAuth 2.0 w usłudze Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) — Dowiedz się, jak używać protokołu OAuth 2.0, protokół uwierzytelniania.
* [Wywołania usług przy użyciu poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md) — Dowiedz się, jak używać przepływie przyznawania poświadczeń klienta OAuth 2.0 dla wywołań usług.
* [Wywołania usług za pomocą usługi Flow "w imieniu"](v1-oauth2-on-behalf-of-flow.md) — Dowiedz się, jak korzystać z przepływu OAuth 2.0 "w imieniu" dla wywołań usług.
* [Referencyjne protokołu SAML](active-directory-saml-protocol-reference.md) — Dowiedz się więcej o profilach usługi Azure AD logowania jednokrotnego i jednym SAML wylogowania.

## <a name="see-also"></a>Zobacz też
[Przewodnik dewelopera usługi Azure Active Directory](v1-overview.md)

[Przykłady kodu usługi Active Directory](sample-v1-code.md)
