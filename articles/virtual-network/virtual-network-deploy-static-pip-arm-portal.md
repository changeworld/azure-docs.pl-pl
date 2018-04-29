---
title: Utwórz maszynę Wirtualną z statycznego publicznego adresu IP - portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną za pomocą statycznego publicznego adresu IP przy użyciu portalu Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50ae4d6e8c275db16f811a2a1a063eda441f150b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Utwórz maszynę Wirtualną za pomocą statycznego publicznego adresu IP przy użyciu portalu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [Program PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasyczny)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu modelu wdrażania Menedżera zasobów, które firma Microsoft zaleca w przypadku większości nowych wdrożeń zamiast klasycznym modelu wdrażania.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Utwórz maszynę Wirtualną z statycznego publicznego adresu IP

Aby utworzyć Maszynę wirtualną za pomocą statycznego publicznego adresu IP w portalu Azure, wykonaj następujące kroki:

1. W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. W lewym górnym rogu portalu kliknij **Utwórz zasób**>>**obliczeniowe**>**systemu Windows Server 2012 R2 Datacenter**.
3. W **wybierz model wdrożenia** wybierz **Resource Manager** i kliknij przycisk **Utwórz**.
4. W **podstawy** okienku, wprowadź następujący informacji o maszyny Wirtualnej, a następnie kliknij **OK**.
   
    ![Portal Azure — podstawy](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. W **wybierz rozmiar** okienku, kliknij przycisk **A1 standardowe** następujące, a następnie kliknij przycisk **wybierz**.
   
    ![Portal Azure — wybierz rozmiar](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. W **ustawienia** okienku, kliknij przycisk **publicznego adresu IP**, a następnie w **tworzenie publicznego adresu IP** okienku w obszarze **przypisania**, kliknij  **Statyczne** w następujący sposób. A następnie kliknij przycisk **OK**.
   
    ![Portal Azure — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. W **ustawienia** okienku, kliknij przycisk **OK**.
8. Przegląd **Podsumowanie** okienko następujące, a następnie kliknij przycisk **OK**.
   
    ![Portal Azure — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Zobaczysz nowy Kafelek na pulpicie nawigacyjnym.
   
    ![Portal Azure — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Po utworzeniu maszyny Wirtualnej **ustawienia** okienku są wyświetlane w następujący sposób:
    
    ![Portal Azure — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawianie adresów IP w ramach systemu operacyjnego

Ręcznie nigdy nie należy przypisywać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej. Zaleca się, że nie zostanie statycznie przypisany prywatny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny Wirtualnej, o ile to konieczne, takie jak kiedy [przypisywanie wielu adresów IP do maszyny Wirtualnej systemu Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawić prywatnego adresu IP w ramach systemu operacyjnego, upewnij się, czy jest ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatnego adresu IP](virtual-network-network-interface-addresses.md#private) ustawienia.

## <a name="next-steps"></a>Kolejne kroki

Przepływ ruchu sieciowego do i z maszyny Wirtualnej utworzone w tym artykule. Można zdefiniować reguły zabezpieczeń ruchu przychodzącego i wychodzącego w ramach grupy zabezpieczeń sieci, które ograniczyć ruch, który może przepływać do i z interfejsu sieciowego i podsieci. Aby dowiedzieć się więcej na temat grup zabezpieczeń sieci, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md).