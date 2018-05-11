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
ms.openlocfilehash: afd5460ef3e286cd3d8b550e7d7e54b3e09f2990
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problemy przy logowaniu do aplikacji lokalnych przy użyciu serwera proxy aplikacji usługi Azure AD

Jeśli występują problemy przy logowaniu do aplikacji lokalnych, możesz wypróbować, wykonując poniższe czynności w celu rozwiązania problemu.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>I załadować mojej aplikacji, ale coś na stronie wygląda przerwany

Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.

  * [Mogę uzyskać dostęp do swojej aplikacji, ale strona aplikacji nie jest wyświetlana poprawnie](https://docs.microsoft.com/azure/active-directory/application-proxy-page-appearance-broken-problem/)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale jej załadowanie trwa zbyt długo](https://docs.microsoft.com/azure/active-directory/application-proxy-page-load-speed-problem/)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale linki na stronie aplikacji nie działają](https://docs.microsoft.com/azure/active-directory/application-proxy-page-links-broken-problem/)

## <a name="im-having-a-connectivity-problem-my-application"></a>Mam problem z połączeniem Moja aplikacja
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jakie porty otworzyć dla swojej aplikacji](https://docs.microsoft.com/azure/active-directory/application-proxy-connectivity-ports-how-to/)
  * [Wystąpił problem spowodowany brakiem działającego łącznika w grupie łączników dla mojej aplikacji](https://docs.microsoft.com/azure/active-directory/application-proxy-connectivity-no-working-connector/)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Mam problem podczas konfiguracji serwera Proxy aplikacji usługi AD platformy Azure w portalu administracyjnym
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problem z konfiguracją aplikacji serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/application-proxy-config-how-to/)
  * [Nie wiem, jak skonfigurować logowanie jednokrotne do aplikacji serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/application-proxy-config-sso-how-to/)
  * [Wystąpił problem podczas tworzenia aplikacji w portalu administracyjnym](https://docs.microsoft.com/azure/active-directory/application-proxy-config-problem/)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Wystąpił problem podczas konfigurowania uwierzytelniania zaplecza do mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jak skonfigurować ograniczone delegowanie protokołu Kerberos](https://docs.microsoft.com/azure/active-directory/application-proxy-back-end-kerberos-constrained-delegation-how-to/)
  * [Nie wiem, jak skonfigurować współdziałanie swojej aplikacji z usługą PingAccess](https://docs.microsoft.com/azure/active-directory/application-proxy-back-end-ping-access-how-to/)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Wystąpił problem podczas logowania do mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Występuje błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](https://docs.microsoft.com/azure/active-directory/application-proxy-sign-in-bad-gateway-timeout-error/)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Mam problem z łącznikiem agenta Proxy aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problemy z instalacją łącznika agenta serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/application-proxy-connector-installation-problem/)

## <a name="next-steps"></a>Kolejne kroki
[Jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](manage-apps/application-proxy.md)
