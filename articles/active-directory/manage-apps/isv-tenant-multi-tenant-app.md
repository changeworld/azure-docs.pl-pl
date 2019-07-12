---
title: Tworzenie dzierżawy usługi Azure dla aplikacji wielodostępnych
description: Wskazówki dla niezależnych dostawców oprogramowania na integrację z usługą Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659582"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Tworzenie dzierżawy usługi Azure dla aplikacji wielodostępnych  

Aby zapewnić dostęp do Twojej aplikacji wielodostępnej należy utworzyć dzierżawę usługi Azure Active Directory, aby zarejestrować aplikację i włączyć Federację tożsamości klienta. Zobacz [Wybieranie protokołu federation odpowiednie dla twojej aplikacji wielodostępnej](isv-choose-multi-tenant-federation.md). Tej dzierżawy będzie można testować aplikację i Federacji w środowisku, w którym to podobne do środowiska usługi Azure AD klientów.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Koszty hostingu aplikacji z wieloma dzierżawami

Usługa Azure Active Directory jest dostępna w trzech jednostek SKU: bezpłatna, podstawowa i Premium. [Zobacz szczegółowe porównanie funkcji](https://azure.microsoft.com/pricing/details/active-directory/).

Możesz bezpłatnie tworzyć subskrypcji platformy Azure i usługi Azure active directory i korzystanie z podstawowych funkcji.

## <a name="create-your-tenant"></a>Tworzenie dzierżawy

1. Tworzenie dzierżawy. Zobacz [Konfigurowanie środowiska deweloperskiego](../develop/quickstart-create-new-tenant.md).

2. Włącz i przetestować dostęp pojedynczego logowania jednokrotnego do aplikacji

   a. **W przypadku aplikacji OIDC lub Oath**, [Zarejestruj swoją aplikację](../develop/quickstart-register-app.md) jako aplikacji z wieloma dzierżawami. Wybierz konta w dowolnej organizacji katalogu i opcję osobiste konta Microsoft w typach obsługiwane konta

   b. **Dla aplikacji i WS-Fed — opartej na SAML**, możesz [opartej na SAML skonfigurować logowanie jednokrotne](configure-single-sign-on-non-gallery-applications.md) aplikacji w usłudze Azure AD za pomocą ogólnego szablonu języka SAML.

Możesz również [przekonwertować jednej dzierżawy aplikacjom wielodostępnym](../develop/howto-convert-app-to-be-multi-tenant.md) w razie potrzeby.

## <a name="next-steps"></a>Następne kroki

[Integracja logowania jednokrotnego w aplikacji](isv-sso-content.md)
