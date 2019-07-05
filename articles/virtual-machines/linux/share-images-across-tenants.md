---
title: Udostępnianie galerii obrazów dla dzierżaw na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udostępnianie obrazów maszyn wirtualnych dzierżawy platformy Azure przy użyciu współdzielonego, galerie obrazów.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 88596a4bda3c9ebae99403e95b3539f068cad665
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465478"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Udostępnianie galerii obrazów maszyn wirtualnych dzierżawy platformy Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Nie można użyć portalu, aby wdrożyć Maszynę wirtualną z obrazu w innej dzierżawie usługi azure. Aby utworzyć Maszynę wirtualną z obrazu udostępniane między dzierżawami, należy użyć wiersza polecenia platformy Azure lub [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Utwórz Maszynę wirtualną przy użyciu wiersza polecenia platformy Azure

Zaloguj się z jednostką usługi dla dzierżawy za pomocą identyfikator aplikacji, klucz aplikacji i identyfikator dzierżawy 1 1. Możesz użyć `az account show --query "tenantId"` można pobrać identyfikatorów dzierżawy, jeśli to konieczne.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Zaloguj się w jednostki usługi dla dzierżawy za pomocą identyfikator aplikacji, klucz aplikacji i identyfikator dzierżawy 2 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Tworzenie maszyny Wirtualnej. Informacje przedstawione w tym przykładzie Zastąp własnymi.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli napotkasz problemy, możesz to zrobić [Rozwiązywanie problemów z galerii obrazów udostępnionych](troubleshooting-shared-images.md).