---
title: Azure Active Directory protokołów uwierzytelniania | Microsoft Docs
description: Omówienie protokołów uwierzytelniania obsługiwanych przez Azure Active Directory (AD)
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: b370736a5e4994651499716e3a923cf59465ca96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845420"
---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory protokoły uwierzytelniania

Azure Active Directory (Azure AD) obsługuje kilka najczęściej używanych protokołów uwierzytelniania i autoryzacji. W tematach w tej sekcji opisano obsługiwane protokoły i ich implementacje w usłudze Azure AD. W tematach uwzględniono przegląd obsługiwanych typów zgłoszeń, wprowadzenie do korzystania z metadanych Federacji, szczegółowe informacje dotyczące protokołu OAuth 2,0. Dokumentacja dotycząca protokołu SAML 2,0 i sekcja rozwiązywania problemów.

## <a name="authentication-protocols-articles-and-reference"></a>Artykuły i Dokumentacja protokołów uwierzytelniania

* [Ważne informacje o przerzucaniu klucza podpisywania w usłudze Azure AD](active-directory-signing-key-rollover.md) — informacje o kluczu podpisywania usługi Azure AD erze, zmiany można wprowadzić, aby automatycznie zaktualizować klucz oraz jak zaktualizować najpopularniejsze scenariusze aplikacji.
* [Obsługiwane typy tokenów i oświadczeń](v1-id-and-access-tokens.md) — informacje o oświadczeniach w tokenach, które są problemy z usługą Azure AD.
* [Metadane federacji](azure-ad-federation-metadata.md) — informacje dotyczące znajdowania i interpretowania dokumentów metadanych generowanych przez usługę Azure AD.
* [Uwierzytelnianie oauth 2,0 w usłudze Azure AD](v1-protocols-oauth-code.md) — informacje o implementacji protokołu OAuth 2,0 w usłudze Azure AD.
* [OpenID Connect Connect 1,0](v1-protocols-openid-connect-code.md) — Dowiedz się, jak za pomocą uwierzytelniania OAuth 2,0, protokołu autoryzacji.
* [Wywołania usługi do obsługi przy użyciu poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md) — informacje o sposobie przydzielenia przez usługę do wywołań usług przy użyciu poświadczeń klienta OAuth 2,0.
* [Wywołania usługi do obsługi w imieniu przepływu](v1-oauth2-on-behalf-of-flow.md) — informacje na temat używania protokołu OAuth 2,0 w imieniu usługi Flow do wywołań usługi do obsługi.
* [Odwołanie do protokołu SAML](active-directory-saml-protocol-reference.md) — informacje o profilach SAML logowania jednokrotnego i logowania jednokrotnego w usłudze Azure AD.

## <a name="see-also"></a>Zobacz także

* [Przewodnik dewelopera Azure Active Directory](v1-overview.md)
* [Przykłady kodu Active Directory](sample-v1-code.md)
