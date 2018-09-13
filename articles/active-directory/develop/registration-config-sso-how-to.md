---
title: Konfigurowanie nowej aplikacji z wieloma dzierżawami | Dokumentacja firmy Microsoft
description: Jak skonfigurować logowanie jednokrotne dla aplikacji niestandardowych, tworzenia i rejestrowania w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 2aa3efcdec57a3e236d2b9739636656b35d16cd6
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724153"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Konfigurowanie nowej aplikacji wielodostępnych

Włączanie federacyjnego logowania jednokrotnego (SSO) w aplikacji jest włączana automatycznie podczas federowania za pośrednictwem usługi Azure AD, OpenID Connect, SAML 2.0 i WS-Fed. Jeśli użytkownicy końcowi się pomimo mających już istniejącej sesji z usługą Azure AD, prawdopodobnie aplikacji może być nieprawidłowo skonfigurowana.

* Upewnij się, jeśli używasz biblioteki ADAL/MSAL **PromptBehavior** równa **automatycznie** zamiast **zawsze**.

* Jeśli tworzysz aplikację mobilną, może być konieczne dodatkowe konfiguracje, aby włączyć logowanie Jednokrotne obsługiwanych przez brokera lub innych obsługiwanych przez brokera.

Dla systemów Android, zobacz [włączania wielu aplikacji usługa rejestracji Jednokrotnej w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Dla systemów iOS, zobacz [włączania wielu aplikacji usługa rejestracji Jednokrotnej w systemie iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Kolejne kroki

[Logowania jednokrotnego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Włączanie wielu aplikacji usługa rejestracji Jednokrotnej w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Włączanie wielu aplikacji usługa rejestracji Jednokrotnej w systemie iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrowanie aplikacji do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Wyrażania zgody i udzielania do nich uprawnień dla usługi Azure AD v2.0 zbieżne aplikacje](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Usługi Azure AD w witrynie StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
