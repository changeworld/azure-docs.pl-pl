---
title: Udostępnianie obrazów galerii wśród dzierżawców platformy Azure
description: Dowiedz się, jak udostępniać obrazy maszyn wirtualnych w dzierżawach platformy Azure przy użyciu galerii obrazów udostępnionych.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276339"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Udostępnianie obrazów maszyn wirtualnych galerii w dzierżawach platformy Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Create a scale set using Azure CLI (Tworzenie zestawu skalowania przy użyciu interfejsu wiersza polecenia platformy Azure)

Zaloguj się do jednostki usługi dla dzierżawy 1 przy użyciu identyfikatora aplikacji, klucza aplikacji i identyfikatora dzierżawy 1. Można użyć, `az account show --query "tenantId"` aby uzyskać identyfikatory dzierżawy w razie potrzeby.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Zaloguj się jednostki usługi dla dzierżawy 2 przy użyciu appID, klucz aplikacji i identyfikator dzierżawy 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Utwórz zestaw skalowania. Zastąp informacje w przykładzie własnymi.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Następne kroki

Jeśli napotkasz jakiekolwiek problemy, możesz [rozwiązać problem z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).
