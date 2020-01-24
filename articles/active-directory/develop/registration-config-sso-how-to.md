---
title: Konfigurowanie logowania jednokrotnego aplikacji | Microsoft Docs
description: Jak skonfigurować Logowanie jednokrotne dla aplikacji niestandardowej, którą tworzysz i rejestrujesz w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: bb77f376e22428e9259ff3efc84cf6f1cb3491fe
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702645"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak skonfigurować Logowanie jednokrotne dla aplikacji

Włączenie federacyjnego logowania jednokrotnego w aplikacji jest automatycznie włączane, gdy federowanie za pomocą usługi Azure AD dla OpenID Connect Connect, SAML 2,0 lub WS-karmione. Jeśli użytkownicy końcowi będą mogli się zalogować pomimo posiadania już istniejącej sesji z usługą Azure AD, prawdopodobnie aplikacja może być błędnie skonfigurowana.

* Jeśli używasz biblioteki ADAL/MSAL, upewnij się, że masz **PromptBehavior** ustawioną na wartość **Autostart** , a nie **zawsze**.

* W przypadku kompilowania aplikacji mobilnej może być konieczne dodanie dodatkowych konfiguracji w celu włączenia obsługi rejestracji Jednokrotnej dla brokera lub nieobsługiwanego przez brokera.

W przypadku systemu Android zobacz [Włączanie logowania jednokrotnego aplikacji w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

W przypadku systemu iOS zobacz [Włączanie logowania jednokrotnego aplikacji w systemie iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Następne kroki

[Logowanie jednokrotne usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrowanie aplikacji z usługą AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Wyrażanie zgody i uprawnień dla aplikacji AzureAD v 2.0 z zbieżnością](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
