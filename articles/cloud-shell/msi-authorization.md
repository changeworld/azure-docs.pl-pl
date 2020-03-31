---
title: Używanie tożsamości zarządzanych dla zasobów w usłudze Azure Cloud Shell
description: Uwierzytelnij kod za pomocą usługi MSI w usłudze Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72328716"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Używanie tożsamości zarządzanych dla zasobów platformy Azure w usłudze Azure Cloud Shell

Usługa Azure Cloud Shell obsługuje autoryzację z zarządzanymi tożsamościami dla zasobów platformy Azure. Skorzystaj z tej usługi, aby pobrać tokeny dostępu, aby bezpiecznie komunikować się z usługami platformy Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Informacje o tożsamościach zarządzanych dla zasobów platformy Azure
Typowym wyzwaniem podczas tworzenia aplikacji w chmurze jest sposób bezpiecznego zarządzania poświadczeniami, które muszą znajdować się w kodzie do uwierzytelniania w usługach w chmurze. W usłudze Cloud Shell może być konieczne uwierzytelnienie pobierania z usługi Key Vault dla poświadczeń, które mogą być potrzebne do skryptu.

Tożsamości zarządzane dla zasobów platformy Azure upraszcza rozwiązanie tego problemu, nadając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

## <a name="acquire-access-token-in-cloud-shell"></a>Uzyskaj token dostępu w usłudze Cloud Shell

Wykonaj następujące polecenia, aby ustawić token dostępu MSI `access_token`jako zmienną środowiskową.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Obsługa wygaśnięcia tokenu

Lokalny podsystem MSI buforuje tokeny. W związku z tym można wywołać go tak często, jak chcesz i na-the-wire wywołania wyników usługi Azure AD tylko wtedy, gdy:
- miss pamięci podręcznej występuje z powodu braku tokenu w pamięci podręcznej
- token wygasł

Jeśli buforujesz token w kodzie, należy przygotować do obsługi scenariuszy, w których zasób wskazuje, że token wygasł.

Aby obsłużyć błędy tokenów, odwiedź [stronę MSI dotyczącą zwijania tokenów dostępu MSI.](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Pobieranie tokenów dostępu od maszyn wirtualnych MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
