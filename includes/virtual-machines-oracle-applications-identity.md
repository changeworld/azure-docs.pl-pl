---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361559"
---
### <a name="identity-tier"></a>Warstwa tożsamości 

Partnerstwo Microsoft-Oracle umożliwia skonfigurowanie ujednoliconej tożsamości na platformie Azure, OCI i aplikacji Oracle. W przypadku pakietu aplikacji JD Edwards EnterpriseOne lub PeopleSoft do skonfigurowania logowania jednokrotnego między usługą Azure AD a Oracle IDCS potrzebne jest wystąpienie serwera ORACLE HTTP Server (OHS).

OHS działa jako odwrotny serwer proxy do warstwy aplikacji, co oznacza, że wszystkie żądania do aplikacji końcowych przejść przez nią. Oracle Access Manager WebGate to wtyczka serwera www OHS, która przechwytuje każde żądanie przechodzące do aplikacji końcowej. Jeśli dostępny zasób jest chroniony (wymaga uwierzytelnionej sesji), WebGate inicjuje przepływ uwierzytelniania OIDC za pomocą usługi Identity Cloud Service za pośrednictwem przeglądarki użytkownika. Aby uzyskać więcej informacji na temat przepływów obsługiwanych przez platformę WebGate OpenID Connect, zobacz [dokumentację programu Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

Dzięki tej konfiguracji użytkownik już zalogowany do usługi Azure AD może przejść do aplikacji JD Edwards EnterpriseOne lub PeopleSoft bez ponownego logowania za pośrednictwem usługi Oracle Identity Cloud Service. Klienci wdrażając to rozwiązanie uzyskać korzyści z logowania jednokrotnego, w tym pojedynczy zestaw poświadczeń, ulepszone środowisko logowania, ulepszone zabezpieczenia i niższe koszty pomocy technicznej.

Aby dowiedzieć się więcej na temat konfigurowania logowania jednokrotnego dla JD Edwards EnterpriseOne lub PeopleSoft z usługą Azure AD, zobacz powiązany [oficjalny dokument Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).