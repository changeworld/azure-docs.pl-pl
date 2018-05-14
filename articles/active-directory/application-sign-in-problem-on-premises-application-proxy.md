---
title: Problemy przy logowaniu do aplikacji lokalnych przy użyciu serwera proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Rozwiązywanie typowych problemów, które muszą ponieść, gdy nie można zalogować się do aplikacji lokalnych zintegrowane z usługą Azure AD przy użyciu serwera Proxy aplikacji usługi AD platformy Azure
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 5aba26d14ba0ee8e8ee58f62deb8a82ad147a8aa
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problemy przy logowaniu do aplikacji lokalnych przy użyciu serwera proxy aplikacji usługi Azure AD

Jeśli występują problemy przy logowaniu do aplikacji lokalnych, możesz wypróbować, wykonując poniższe czynności w celu rozwiązania problemu.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>I załadować mojej aplikacji, ale coś na stronie wygląda przerwany

Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.

  * [Mogę uzyskać dostęp do swojej aplikacji, ale strona aplikacji nie jest wyświetlana poprawnie](application-proxy-page-appearance-broken-problem.md)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale jej załadowanie trwa zbyt długo](application-proxy-page-load-speed-problem.md)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale linki na stronie aplikacji nie działają](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Mam problem z połączeniem Moja aplikacja
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jakie porty otworzyć dla swojej aplikacji](application-proxy-connectivity-ports-how-to.md)
  * [Wystąpił problem spowodowany brakiem działającego łącznika w grupie łączników dla mojej aplikacji](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Mam problem podczas konfiguracji serwera Proxy aplikacji usługi AD platformy Azure w portalu administracyjnym
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problem z konfiguracją aplikacji serwera proxy aplikacji](application-proxy-config-how-to.md)
  * [Nie wiem, jak skonfigurować logowanie jednokrotne do aplikacji serwera proxy aplikacji](application-proxy-config-sso-how-to.md)
  * [Wystąpił problem podczas tworzenia aplikacji w portalu administracyjnym](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Wystąpił problem podczas konfigurowania uwierzytelniania zaplecza do mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jak skonfigurować ograniczone delegowanie protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Nie wiem, jak skonfigurować współdziałanie swojej aplikacji z usługą PingAccess](application-proxy-back-end-ping-access-how-to.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Wystąpił problem podczas logowania do mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Występuje błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Mam problem z łącznikiem agenta Proxy aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problemy z instalacją łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Kolejne kroki
[Jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](manage-apps/application-proxy.md)
