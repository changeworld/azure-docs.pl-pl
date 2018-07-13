---
title: Tworzenie maszyny Wirtualnej za pomocą statyczny publiczny adres IP — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 524293f9a1ded73ee7cb6ba4f53208a9f9c54ffa
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670988"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Tworzenie maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu witryny Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [Program PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasyczny)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager zalecanego przez firmę Microsoft w przypadku większości nowych wdrożeń zamiast klasycznego modelu wdrażania.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Tworzenie maszyny Wirtualnej ze statycznym publicznym adresem IP

Aby utworzyć Maszynę wirtualną z statyczny publiczny adres IP w witrynie Azure portal, wykonaj następujące czynności:

1. W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. W lewym górnym rogu portalu, kliknij polecenie **Utwórz zasób**>>**obliczenia**>**systemu Windows Server 2012 R2 Datacenter**.
3. W **wybierz model wdrożenia** , wybierz na liście **usługi Resource Manager** i kliknij przycisk **Utwórz**.
4. W **podstawy** , wprowadź informacje o maszynie Wirtualnej w następujący sposób, a potem kliknij **OK**.
   
    ![Witryna Azure portal — podstawy](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. W **wybierz rozmiar** okienku kliknij **standardowa A1** poniżej, a następnie kliknij przycisk **wybierz**.
   
    ![Witryna Azure portal — Wybieranie rozmiaru](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. W **ustawienia** okienku kliknij **publiczny adres IP**, a następnie w obszarze **tworzenie publicznego adresu IP** okienku w obszarze **przypisania**, kliknij  **Statyczne** w następujący sposób. A następnie kliknij przycisk **OK**.
   
    ![Witryna Azure portal — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. W **ustawienia** okienku kliknij **OK**.
8. Przegląd **Podsumowanie** okienku poniżej, a następnie kliknij przycisk **OK**.
   
    ![Witryna Azure portal — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Zwróć uwagę, nowy Kafelek na pulpicie nawigacyjnym.
   
    ![Witryna Azure portal — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Po utworzeniu maszyny Wirtualnej **ustawienia** okienku są wyświetlane w następujący sposób:
    
    ![Witryna Azure portal — tworzenie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej. Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne, takie jak czas [przypisywanie wielu adresów IP do maszyny Wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jej jako ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatny adres IP](virtual-network-network-interface-addresses.md#private) ustawienia.

## <a name="next-steps"></a>Kolejne kroki

Dowolny ruch sieciowy może przepływać do i z maszyn wirtualnych utworzonych w tym artykule. Można zdefiniować reguły zabezpieczeń ruchu przychodzącego i wychodzącego w ramach grupy zabezpieczeń sieci, które ograniczają ruch może przepływać do i z interfejsu sieciowego i/lub podsieci. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md).