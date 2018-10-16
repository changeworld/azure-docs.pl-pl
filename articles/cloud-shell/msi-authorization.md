---
title: Użyj tożsamości zarządzanych zasobów platformy Azure w usłudze Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Uwierzytelnianie kodu za pomocą tożsamości usługi Zarządzanej w usłudze Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: 09f54efaf3ff89711c34b7960a271438f38cf224
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345088"
---
# <a name="use-msi-in-azure-cloud-shell"></a>W usłudze Azure Cloud Shell przy użyciu pliku MSI

Usługa Azure Cloud Shell obsługuje autoryzacji przy użyciu tożsamości usługi zarządzanej (MSI). Korzystać, aby pobrać tokenów dostępu w celu bezpiecznego komunikowania się z usługami platformy Azure.

## <a name="about-managed-service-identity-msi"></a>O tożsamości usługi zarządzanej (MSI)
Typowe wyzwania, podczas tworzenia aplikacji w chmurze jest sposób bezpiecznego zarządzania poświadczeniami, które powinny znajdować się w kodzie do uwierzytelniania w usługach w chmurze. W usłudze Cloud Shell może być konieczne pobierania z usługi Key Vault dla poświadczeń, które skrypt może wymagać uwierzytelniania.

Tożsamość usługi zarządzanej (MSI) ułatwia rozwiązywanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

## <a name="acquire-access-token-in-cloud-shell"></a>Uzyskiwanie tokenu dostępu w usłudze Cloud Shell

Wykonaj następujące polecenia, aby ustawić token dostępu tożsamości usługi Zarządzanej jako zmienną środowiskową `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Obsługa wygaśnięcia tokenu

Lokalne podsystemu MSI buforuje tokenów. W związku z tym można wywołać ją tak często, jak Ci się podoba i powoduje wywołanie w locie do usługi Azure AD, tylko wtedy, gdy:
- Trafienia pamięci podręcznej występuje ze względu na brak tokenu w pamięci podręcznej
- token utracił ważność

Jeśli token jest buforowania w kodzie, należy być przygotowanym do obsługi scenariuszy, gdzie zasobu wskazuje, że token utracił ważność.

Aby obsługiwać błędy tokenu, odwiedź stronę [MSI strony o curling tokenów dostępu tożsamości usługi Zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pliku MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Uzyskiwanie tokenów dostępu z poziomu maszyn wirtualnych z pliku MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
