---
title: Udostępnianie galerii obrazów dla dzierżaw na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udostępnianie obrazów maszyn wirtualnych dzierżawy platformy Azure przy użyciu współdzielonego, galerie obrazów.
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 56c0e906203a010a1356beae4b6e5d66e34cfc2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159843"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Udostępnianie galerii obrazów maszyn wirtualnych dzierżawy platformy Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-powershell"></a>Utwórz Maszynę wirtualną przy użyciu programu PowerShell


Zaloguj się do obu dzierżaw przy użyciu Identyfikatora aplikacji, identyfikator wpisu tajnego i dzierżawy. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Tworzenie maszyny Wirtualnej w grupie zasobów, które ma uprawnienie dotyczące rejestracji aplikacji. Informacje przedstawione w tym przykładzie należy zastąpić własnymi.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>Kolejne kroki

Można również utworzyć obraz udostępnione zasoby galerii pakietu przy użyciu [witryny Azure portal](shared-images-portal.md).