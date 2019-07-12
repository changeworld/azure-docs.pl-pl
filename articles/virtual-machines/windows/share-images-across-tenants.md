---
title: Udostępnianie galerii obrazów dla dzierżaw na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udostępnianie obrazów maszyn wirtualnych dzierżawy platformy Azure przy użyciu współdzielonego, galerie obrazów.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: c26abe948fa415c780d543c615c34af2091cfbc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709157"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Udostępnianie galerii obrazów maszyn wirtualnych dzierżawy platformy Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Nie można użyć portalu, aby wdrożyć Maszynę wirtualną z obrazu w innej dzierżawie usługi azure. Aby utworzyć Maszynę wirtualną z obrazu udostępniane między dzierżawami, należy użyć [wiersza polecenia platformy Azure](../linux/share-images-across-tenants.md) lub programu Powershell.

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