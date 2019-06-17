---
title: Problemy z logowaniem do aplikacji lokalnych przy użyciu serwera proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Rozwiązywanie typowych problemów sterowaną nie można zalogować się do aplikacji lokalnych zintegrowana z usługą Azure AD przy użyciu serwera Proxy aplikacji usługi AD systemu Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0adc2effb27ffcd870ec543ee7b353a092808797
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825259"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problemy z logowaniem do aplikacji lokalnych przy użyciu serwera proxy aplikacji usługi Azure AD

Jeśli występują problemy z logowaniem do aplikacji w środowisku lokalnym, możesz spróbować kroki opisane poniżej w celu rozwiązania problemu.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Mogę załadować moją aplikację, lecz niektóre elementy strony wyglądają na uszkodzone

Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.

  * [Mogę uzyskać dostęp do swojej aplikacji, ale strona aplikacji nie jest wyświetlana poprawnie](application-proxy-page-appearance-broken-problem.md)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale jej załadowanie trwa zbyt długo](application-proxy-page-load-speed-problem.md)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale linki na stronie aplikacji nie działają](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Mam problem z połączeniem w mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jakie porty otworzyć dla swojej aplikacji](application-proxy-connectivity-ports-how-to.md)
  * [Wystąpił problem spowodowany brakiem działającego łącznika w grupie łączników dla mojej aplikacji](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Mam problem ze skonfigurowaniem serwera proxy aplikacji usługi Azure AD w portalu administracyjnym
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problem z konfiguracją aplikacji serwera proxy aplikacji](application-proxy-config-how-to.md)
  * [Nie wiem, jak skonfigurować logowanie jednokrotne do aplikacji serwera proxy aplikacji](application-proxy-config-sso-how-to.md)
  * [Wystąpił problem podczas tworzenia aplikacji w portalu administracyjnym](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Mam problem ze skonfigurowaniem uwierzytelniania zaplecza dla mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jak skonfigurować ograniczone delegowanie protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Nie wiem, jak skonfigurować współdziałanie swojej aplikacji z usługą PingAccess](application-proxy-back-end-ping-access-how-to.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Mam problem podczas logowania do mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Występuje błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Mam problem z łącznikiem agenta serwera proxy aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problemy z instalacją łącznika agenta serwera Proxy aplikacji](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Kolejne kroki
[Jak zapewnić bezpieczny, zdalny dostęp do aplikacji lokalnych](application-proxy.md)
