---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361559"
---
### <a name="identity-tier"></a>Warstwa tożsamości 

Partnerstwo firmy Microsoft — Oracle umożliwia skonfigurowanie ujednoliconej tożsamości na platformie Azure, OCI i aplikacji Oracle. W przypadku pakietu aplikacji JD Edwards EnterpriseOne lub PeopleSoft do konfigurowania logowania jednokrotnego między usługą Azure AD i Oracle OHS jest wymagany wystąpienie serwera Oracle HTTP (IDCS).

OHS działa jako zwrotny serwer proxy do warstwy aplikacji, co oznacza, że wszystkie żądania do aplikacji końcowych przechodzą przez nią. Webbramą programu Oracle Access Manager jest wtyczka serwera sieci Web OHS, która przechwytuje każde żądanie przechodzenia do aplikacji końcowej. Jeśli dostęp do zasobu jest chroniony (wymaga uwierzytelnionej sesji), usługa webbram inicjuje przepływ uwierzytelniania OIDC przy użyciu usługi Identity w chmurze za pomocą przeglądarki użytkownika. Więcej informacji o przepływach obsługiwanych przez program webgate OpenID Connect Connect można znaleźć w [dokumentacji programu Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

W przypadku tej konfiguracji użytkownik zalogowany do usługi Azure AD może przejść do aplikacji JD Edwards EnterpriseOne lub PeopleSoft bez ponownego zalogowania się za pomocą usługi tożsamości Oracle w chmurze. Klienci, którzy wdrażają to rozwiązanie, uzyskują zalety logowania jednokrotnego, w tym jeden zestaw poświadczeń, ulepszone środowisko logowania, ulepszone zabezpieczenia i krótszy koszt pomocy technicznej.

Aby dowiedzieć się więcej o konfigurowaniu rejestracji Jednokrotnej dla JD Edwards EnterpriseOne lub PeopleSoft za pomocą usługi Azure AD, zapoznaj się z dokumentem związanym z oprogramowaniem [Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).