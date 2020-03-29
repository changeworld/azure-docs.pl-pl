---
title: Konfigurowanie logowania jednokrotnego aplikacji | Dokumenty firmy Microsoft
description: Jak skonfigurować logowanie jednokrotne dla aplikacji niestandardowej, którą tworzysz i rejestrujesz w usłudze Azure AD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702645"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak skonfigurować logowanie jednokrotne dla aplikacji

Włączenie federacyjnego logowania jednokrotnego (SSO) w aplikacji jest automatycznie włączane podczas federowania za pośrednictwem usługi Azure AD dla OpenID Connect, SAML 2.0 lub WS-Fed. Jeśli użytkownicy końcowi muszą się zalogować, mimo że już istniejącą sesję w usłudze Azure AD, prawdopodobnie aplikacja może zostać błędnie skonfigurowana.

* Jeśli używasz ADAL/MSAL, upewnij się, że **promptbehavior** jest ustawiony na **Auto,** a nie **Zawsze**.

* Jeśli budujesz aplikację mobilną, może być konieczne dodatkowe konfiguracje, aby włączyć brokered lub non-brokered SSO.

W systemie Android zobacz [Włączanie funkcji SSO aplikacji krzyżowej w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

W przypadku systemu iOS zobacz Włączanie funkcji [SSO aplikacji krzyżowej w iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Następne kroki

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Włączanie funkcji SSO aplikacji krzyżowej w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Włączanie funkcji SSO aplikacji krzyżowej w iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrowanie aplikacji z usługą AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Zgoda i uprawnienia dla aplikacji konwergentnych usługi AzureAD w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Przepływ stosu azuread](https://stackoverflow.com/questions/tagged/azure-active-directory)
