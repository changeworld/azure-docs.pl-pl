---
title: Konfigurowanie logowania jednokrotnego aplikacji
description: Jak skonfigurować logowanie jednokrotne dla aplikacji niestandardowej, którą tworzysz i rejestrujesz w usłudze Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 630be1676d73410f1b0d376b163a8599299ddbe2
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883155"
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
