---
title: Jak zresetować interfejs sieciowy dla maszyny Wirtualnej systemu Windows platformy Azure| Dokumenty firmy Microsoft
description: Pokazuje, jak zresetować interfejs sieciowy dla maszyny Wirtualnej systemu Windows platformy Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: a8bd12d98b76d5848753987c4f7bcb76d4e2266d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250077"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Jak zresetować interfejs sieciowy dla maszyny wirtualnej z systemem Windows na platformie Azure 

W tym artykule pokazano, jak zresetować interfejs sieciowy dla maszyny Wirtualnej systemu Windows platformy Azure, aby rozwiązać problemy, gdy nie można połączyć się z maszyną wirtualną systemu Microsoft Azure Windows (VM) po:

* Wyłączasz domyślny interfejs sieciowy (NIC). 
* Ręcznie ustaw statyczny adres IP dla karty sieciowej. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Resetowanie interfejsu sieciowego

### <a name="for-vms-deployed-in-resource-group-model"></a>Dla maszyn wirtualnych wdrożonych w modelu grupy zasobów

1.  Przejdź do [witryny Azure portal](https://ms.portal.azure.com).
2.  Wybierz maszynę wirtualną, którego dotyczy problem.
3.  Wybierz **pozycję Sieć,** a następnie wybierz interfejs sieciowy maszyny Wirtualnej.

    ![Lokalizacja interfejsu sieciowego](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Wybierz **konfiguracje IP**.
5.  Wybierz adres IP. 
6.  Jeśli **przypisanie prywatnego adresu IP** nie jest **statyczne,** zmień go na **Statyczny**.
7.  Zmień **adres IP** na inny adres IP, który jest dostępny w podsieci.
8. Maszyna wirtualna uruchomi się ponownie, aby zainicjować nową kartę sieciową w systemie.
9.  Spróbuj RDP do komputera. Jeśli się powiedzie, możesz zmienić prywatny adres IP z powrotem na oryginalny, jeśli chcesz. W przeciwnym razie możesz go zachować. 

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz [zainstalowaną najnowszą usługę Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
2. Otwórz podwyższoną sesję programu Azure PowerShell (uruchom jako administrator). Uruchom następujące polecenia:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Spróbuj RDP do komputera.  Jeśli się powiedzie, możesz zmienić prywatny adres IP z powrotem na oryginalny, jeśli chcesz. W przeciwnym razie możesz go zachować.

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Aby zresetować interfejs sieciowy, wykonaj następujące czynności:

#### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

1.  Przejdź do [witryny Azure portal]( https://ms.portal.azure.com).
2.  Wybierz **maszyny wirtualne (klasyczne).**
3.  Wybierz maszynę wirtualną, którego dotyczy problem.
4.  Wybierz **adresy IP**.
5.  Jeśli **przypisanie prywatnego adresu IP** nie jest **statyczne,** zmień go na **Statyczny**.
6.  Zmień **adres IP** na inny adres IP, który jest dostępny w podsieci.
7.  Wybierz **pozycję Zapisz**.
8.  Maszyna wirtualna uruchomi się ponownie, aby zainicjować nową kartę sieciową w systemie.
9.  Spróbuj RDP do komputera. Jeśli to się powiedzie, można przywrócić prywatny adres IP z powrotem do oryginału.  

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz [zainstalowaną najnowszą usługę Azure PowerShell.](https://docs.microsoft.com/powershell/azure/overview)
2. Otwórz podwyższoną sesję programu Azure PowerShell (uruchom jako administrator). Uruchom następujące polecenia:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Spróbuj RDP do komputera. Jeśli się powiedzie, możesz zmienić prywatny adres IP z powrotem na oryginalny, jeśli chcesz. W przeciwnym razie możesz go zachować. 

## <a name="delete-the-unavailable-nics"></a>Usuwanie niedostępnych kart sieciowych
Po zdalnym pulpicie na komputerze należy usunąć stare karty sieciowe, aby uniknąć potencjalnego problemu:

1.  Otwórz Menedżera urządzeń.
2.  Wybierz **pozycję Wyświetl** > **wyświetlanie ukrytych urządzeń**.
3.  Wybierz **pozycję Karty sieciowe**. 
4.  Sprawdź, czy karty są nazywane "kartą sieciową Microsoft Hyper-V".
5.  Może zostać wyświetlona niedostępna karta, która jest wyszarzona. Kliknij prawym przyciskiem myszy kartę, a następnie wybierz polecenie Odinstaluj.

    ![obraz karty sieciowej](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Odinstaluj tylko niedostępne karty o nazwie "Karta sieciowa Microsoft Hyper-V". Odinstalowanie któregokolwiek z innych ukrytych kart może spowodować dodatkowe problemy.
    >
    >

6.  Teraz wszystkie niedostępne karty powinny zostać wyczyszczone z systemu.
