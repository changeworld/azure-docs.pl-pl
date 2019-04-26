---
title: Jak zresetować interfejsu sieciowego maszyny wirtualnej platformy Azure Windows | Dokumentacja firmy Microsoft
description: Pokazuje, jak można zresetować interfejsu sieciowego dla maszyny Wirtualnej Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 3a8e005f8678deef9fc4aebd2d620619fe6074bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307310"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Jak zresetować interfejsu sieciowego dla maszyny Wirtualnej Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

W tym artykule przedstawiono sposób resetowania interfejsu sieciowego dla maszyny Wirtualnej Windows Azure rozwiązać problemy, jeśli nie możesz się połączyć na Microsoft Azure Windows maszyn wirtualnych (VM) po:

* Można wyłączyć domyślnego interfejsu sieciowego (NIC). 
* Możesz ręcznie ustawić statyczny adres IP dla karty sieciowej. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Zresetuj interfejs sieciowy

### <a name="for-vms-deployed-in-resource-group-model"></a>Dla maszyn wirtualnych wdrożonych w modelu grupy zasobów

1.  Przejdź do witryny [Azure Portal](https://ms.portal.azure.com).
2.  Wybierz maszynę wirtualną, do których to dotyczy.
3.  Wybierz **sieć** i następnie wybierz sieć interfejsu maszyny wirtualnej.

    ![Lokalizacja interfejsu sieciowego](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Wybierz **konfiguracje adresów IP**.
5.  Wybierz adres IP. 
6.  Jeśli **przypisanie prywatnego adresu IP** nie **statyczne**, zmień ją na **statyczne**.
7.  Zmiana **adresu IP** na inny adres IP, który jest dostępny w tej podsieci.
8. Maszyny wirtualnej zostanie uruchomiony ponownie w celu zainicjowania nowych kart Sieciowych w systemie.
9.  Spróbuj użyć protokołu RDP na komputerze. Jeśli to się powiedzie, można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można pozostawić ją. 

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz [najnowszą wersję programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) zainstalowane
2. Otwórz sesję programu Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia:

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
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Spróbuj użyć protokołu RDP na komputerze.  Jeśli to się powiedzie, można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można pozostawić ją.

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

Aby zresetować interfejsu sieciowego, wykonaj następujące kroki:

#### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

1.  Przejdź do witryny [Azure Portal]( https://ms.portal.azure.com).
2.  Wybierz **maszyny wirtualne (klasyczne)**.
3.  Wybierz maszynę wirtualną, do których to dotyczy.
4.  Wybierz **adresów IP**.
5.  Jeśli **przypisanie prywatnego adresu IP** nie **statyczne**, zmień ją na **statyczne**.
6.  Zmiana **adresu IP** na inny adres IP, który jest dostępny w tej podsieci.
7.  Wybierz pozycję **Zapisz**.
8.  Maszyny wirtualnej zostanie uruchomiony ponownie w celu zainicjowania nowych kart Sieciowych w systemie.
9.  Spróbuj użyć protokołu RDP na komputerze. Jeśli to się powiedzie, można przywrócić prywatny adres IP wrócić do oryginalnego.  

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz [najnowszą wersję programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) zainstalowane.
2. Otwórz sesję programu Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia:

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
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Spróbuj użyć protokołu RDP na komputerze. Jeśli to się powiedzie, można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można pozostawić ją. 

## <a name="delete-the-unavailable-nics"></a>Usuń niedostępne karty sieciowe
Po możesz pulpitu zdalnego z maszyną, należy usunąć stare karty sieciowe, aby uniknąć potencjalnego problemu:

1.  Otwórz Menedżera urządzeń.
2.  Wybierz **widoku** > **Pokaż ukryte urządzenia**.
3.  Wybierz **karty sieciowe**. 
4.  Wyszukaj karty o nazwie "Karta sieciowa Microsoft Hyper-V".
5.  Możesz zobaczyć niedostępne karty przedstawione jako wyszarzone. Kliknij prawym przyciskiem myszy kartę, a następnie wybierz pozycję Odinstaluj.

    ![Obraz karty Sieciowej](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Tylko odinstalować niedostępne karty, o nazwie "Karta sieciowa Microsoft Hyper-V". Po odinstalowaniu wszystkich ukrytych kart może spowodować dodatkowe problemy.
    >
    >

6.  Teraz wszystkie niedostępne karty powinny zostać wyczyszczone z systemu.
