---
title: Użyj MSI w powłoce chmura Azure | Dokumenty Microsoft
description: Uwierzytelniania kodu z pliku MSI powłoki chmura Azure
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
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="use-msi-in-azure-cloud-shell"></a>Użyj MSI w powłoce chmura Azure

Powłoka chmura azure obsługuje autoryzacji z tożsamości usługi zarządzane (MSI). Wykorzystać, aby pobrać tokenów dostępu do bezpiecznej komunikacji z usług Azure.

## <a name="about-managed-service-identity-msi"></a>O tożsamości zarządzanych usług (MSI)
Wspólnym wyzwaniem podczas budowanie aplikacji w chmurze jest jak bezpiecznie zarządzać poświadczenia, które powinny znajdować się w kodzie do uwierzytelniania w chmurze. W powłoce chmury może być konieczne do pobierania z magazynu kluczy dla poświadczeń, które skrypt może wymagać uwierzytelnienia.

Tożsamość usługi zarządzanej (MSI) powoduje, że rozwiązania tego problemu prostszy, zapewniając usług Azure automatycznie zarządzane tożsamości w usłudze Azure Active Directory (Azure AD). Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, w tym usługi Key Vault bez żadnych poświadczeń w kodzie.

## <a name="acquire-access-token-in-cloud-shell"></a>Uzyskać tokenu dostępu w chmurze powłoki

Wykonanie poniższych poleceń, aby ustawić token dostępu MSI jako zmiennej środowiskowej `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Obsługa wygaśnięcia tokenu

Podsystemu lokalnego MSI buforuje tokenów. W związku z tym można wywołać ją tyle razy, ile Ci się podoba i powoduje wywołanie w locie do usługi Azure AD tylko wtedy, gdy:
- Chybienia pamięci podręcznej występuje ze względu na nie tokenu w pamięci podręcznej
- token utracił ważność

Kod w pamięci podręcznej tokenu, powinny być przygotowane do obsługi scenariuszy, w którym zasobu wskazuje, czy token utracił ważność.

Do obsługi błędów token, odwiedź witrynę [strona MSI o zwijania MSI tokeny dostępu](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Pobieranie tokeny dostępu z maszyn wirtualnych MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
