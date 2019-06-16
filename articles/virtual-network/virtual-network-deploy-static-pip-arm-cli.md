---
title: Tworzenie maszyny Wirtualnej za pomocą statyczny publiczny adres IP — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 4b5b0453d4f9935eed9acd5059c0aa9741b25524
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730474"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu wiersza polecenia platformy Azure

Można utworzyć maszyny wirtualnej ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy nie zmienia się. Dowiedz się więcej o [statyczne publiczne adresy IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić publiczny adres IP przypisane do istniejącej maszyny wirtualnej z dynamicznego na statyczne lub pracować w prywatnych adresów IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP zostały [za symboliczną cenę](https://azure.microsoft.com/pricing/details/ip-addresses)i ma [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) do liczby publicznych adresów IP używanych na subskrypcję.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Z komputera lokalnego lub za pomocą usługi Azure Cloud Shell, można wykonać następujące czynności. Aby korzystać z komputera lokalnego, upewnij się, że [wiersza polecenia platformy Azure zainstalowany](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby użyć usługi Azure Cloud Shell, wybierz **wypróbuj** w prawym górnym rogu pola dowolnego polecenia, która jest zgodna. Cloud Shell zaloguje się do platformy Azure.

1. Jeśli używasz powłoki Cloud, przejdź do kroku 2. Otwórz polecenia sesji i logowania do platformy Azure przy `az login`.
2. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów w regionie wschodnie stany USA Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). `--public-ip-address-allocation=static` Opcja przypisuje statyczny publiczny adres IP do maszyny wirtualnej. Poniższy przykład tworzy maszynę wirtualną Ubuntu przy użyciu statycznych i podstawowa jednostka SKU publicznego adresu IP o nazwie *myPublicIpAddress*:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Jeśli publiczny adres IP musi być standardowej jednostki SKU, Dodaj `--public-ip-sku Standard` do poprzedniego polecenia. Dowiedz się więcej o [publicznego adresu IP jednostki SKU](virtual-network-ip-addresses-overview-arm.md#sku). Jeśli maszyna wirtualna zostanie dodany do puli zaplecza publicznej usługi Azure Load Balancer, jednostki SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Wyświetl przypisany publiczny adres IP i upewnij się, że został on utworzony jako statyczne, podstawowy adres jednostki SKU z [az sieci public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure przypisany publiczny adres IP z adresów używanych w danym regionie utworzono maszynę wirtualną w. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Nie należy modyfikować ustawienia adresu IP w ramach systemu operacyjnego maszyny wirtualnej. System operacyjny jest niezależnych od platformy Azure publicznych adresów IP. Chociaż można było dodać prywatnej ustawienia adresu IP do systemu operacyjnego, zaleca się nie ten sposób, chyba że to konieczne, a nie dopiero po odczytu [Dodaj prywatny adres IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [publiczne adresy IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure
- Dowiedz się więcej o wszystkich [ustawienia publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o [prywatnych adresów IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywanie [statyczny prywatny adres IP](virtual-network-network-interface-addresses.md#add-ip-addresses) na maszynie wirtualnej platformy Azure
- Dowiedz się więcej na temat tworzenia [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyn wirtualnych
