---
title: Udostępnianie obrazów galerii między dzierżawcami na platformie Azure
description: Dowiedz się, jak udostępniać obrazy maszyn wirtualnych w dzierżawach platformy Azure przy użyciu udostępnionych galerii obrazów.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276339"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Udostępnianie obrazów maszyn wirtualnych z galerii w ramach dzierżawców platformy Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Tworzenie zestawu skalowania przy użyciu interfejsu wiersza polecenia platformy Azure

Zaloguj się do jednostki usługi dla dzierżawy 1 przy użyciu identyfikatora appID, klucz aplikacji i identyfikator dzierżawy 1. W razie potrzeby można użyć `az account show --query "tenantId"` w celu uzyskania identyfikatorów dzierżawy.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Zaloguj się do jednostki usługi dla dzierżawy 2 przy użyciu identyfikatora appID, klucz aplikacji i identyfikator dzierżawy 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Utwórz zestaw skalowania. Zastąp informacje w podanym przykładzie własnym.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Następne kroki

Jeśli napotkasz jakiekolwiek problemy, możesz [rozwiązać problemy z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).
