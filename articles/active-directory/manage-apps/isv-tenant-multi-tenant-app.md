---
title: Tworzenie dzierżawy platformy Azure dla aplikacji z wieloma dzierżawcami
description: Wskazówki dotyczące integracji z usługą Azure Active Directory dla niezależnych dostawców oprogramowania
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812612"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Tworzenie dzierżawy platformy Azure dla aplikacji z wieloma dzierżawcami  

Aby zapewnić dostęp do aplikacji z wieloma dzierżawcami, należy utworzyć dzierżawę usługi Azure Active Directory, aby zarejestrować aplikację i włączyć federację tożsamości klienta. Zobacz [Wybieranie odpowiedniego protokołu federacyjnego dla aplikacji wielodostępnej](isv-choose-multi-tenant-federation.md). Ta dzierżawa pozwoli ci przetestować aplikację i federację w środowisku podobnym do środowisk usługi Azure AD klientów.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Koszty hostingu aplikacji wielodostępnej

Usługa Azure Active Directory jest dostępna w wielu wersjach. [Zobacz szczegółowe porównanie funkcji](https://azure.microsoft.com/pricing/details/active-directory/).

Możesz bezpłatnie utworzyć subskrypcję platformy Azure i usługę Azure active directory oraz korzystać z podstawowych funkcji.

## <a name="create-your-tenant"></a>Tworzenie dzierżawy

1. Utwórz dzierżawę. Zobacz [Konfigurowanie środowiska deweloperskiego](../develop/quickstart-create-new-tenant.md).

2. Włączanie i testowanie dostępu do aplikacji z logami jednokrotnymi,

   a. **W przypadku aplikacji OIDC lub Oath** [zarejestruj aplikację](../develop/quickstart-register-app.md) jako aplikację wielodostępną. Wybierz opcję Konta w dowolnym katalogu organizacji i osobistych kontach Microsoft w typach obsługiwanych kont

   b. **W przypadku aplikacji opartych na saml i WS-Fed** [można skonfigurować aplikacje jednokrotnego logowania oparte na saml](configure-single-sign-on-non-gallery-applications.md) przy użyciu ogólnego szablonu SAML w usłudze Azure AD.

W razie potrzeby można również [przekonwertować aplikację z jedną dzierżawą na wielodostępną.](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>Następne kroki

[Integracja aplikacji SSO w aplikacji](isv-sso-content.md)
