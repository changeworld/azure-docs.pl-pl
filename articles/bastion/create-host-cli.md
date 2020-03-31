---
title: Tworzenie hosta bastionu przy użyciu interfejsu wiersza polecenia platformy Azure | Azure Bastion
description: W tym artykule dowiesz się, jak utworzyć i usunąć hosta bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337573"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Tworzenie hosta bastionu platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak utworzyć hosta bastionu platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Po aprowizyjce usługi Azure Bastion w sieci wirtualnej bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Wdrożenie usługi Azure Bastion jest na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną.

Opcjonalnie można utworzyć hosta Bastion platformy Azure przy użyciu [witryny Azure portal](bastion-create-host-portal.md)lub za pomocą [programu Azure PowerShell.](bastion-create-host-powershell.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja ułatwia tworzenie nowego zasobu bastionu platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

1. Utwórz sieć wirtualną i podsieć Bastion platformy Azure. Należy utworzyć podsieć Bastion platformy Azure przy użyciu wartości nazwy **AzureBastionSubnet**. Ta wartość umożliwia platformie Azure wiedzieć, które podsieci wdrożyć zasoby bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci co najmniej /27 lub większej podsieci (/27, /26 itd.). Utwórz **usługę AzureBastionSubnet** bez żadnych tabel tras lub delegacji. Jeśli używasz sieciowych grup zabezpieczeń w **usłudze AzureBastionSubnet,** zapoznaj się z artykułem [Praca z sieciami roboczymi.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Utwórz publiczny adres IP dla bastionu platformy Azure. Publiczny adres IP jest publicznym adresem IP zasobu Bastion, na którym będzie dostępny protokół RDP/SSH (za port 443). Publiczny adres IP musi znajdować się w tym samym regionie co zasób Bastion, który tworzysz.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Utwórz nowy zasób Bastion platformy Azure w sieci AzureBastionSubnet sieci wirtualnej. Tworzenie i wdrażanie zasobu Bastion zajmuje około 5 minut.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Następne kroki

* Przeczytaj często zadawane pytania dotyczące [bastionu, aby](bastion-faq.md) uzyskać dodatkowe informacje.

* Aby używać grup zabezpieczeń sieciowych w podsieci Bastion platformy Azure, zobacz [Praca z sieciami roboczymi](bastion-nsg.md).
