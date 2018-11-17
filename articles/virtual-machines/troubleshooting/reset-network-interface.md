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
ms.openlocfilehash: 61001d4926dcce68872a368afb5b28f2d3a8e2c0
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819004"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Jak zresetować interfejsu sieciowego dla maszyny Wirtualnej Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Nie można połączyć do firmy Microsoft Windows Virtual Machine (maszyna wirtualna Azure), po wyłączeniu domyślnego interfejsu sieciowego (NIC) lub ręcznie ustawia statyczny adres IP dla karty sieciowej. W tym artykule pokazano, jak można zresetować interfejsu sieciowego dla maszyny Wirtualnej Windows Azure, który zostanie rozwiązany problem połączenia zdalnego.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Zresetuj interfejs sieciowy

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

Aby zresetować interfejsu sieciowego, wykonaj następujące kroki:

#### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

1.  Przejdź do witryny [Azure Portal]( https://ms.portal.azure.com).
2.  Wybierz **maszyny wirtualne (klasyczne)**.
3.  Wybierz maszynę wirtualną, do których to dotyczy.
4.  Wybierz **adresów IP**.
5.  Jeśli **przypisanie prywatnego adresu IP** nie **statyczne**, zmień ją na **statyczne**.
6.  Zmiana **adresu IP** na inny adres IP, który jest dostępny w tej podsieci.
7.  Wybierz przycisk Zapisz.
8.  Maszyny wirtualnej zostanie uruchomiony ponownie w celu zainicjowania nowych kart Sieciowych w systemie.
9.  Spróbuj użyć protokołu RDP na komputerze. Jeśli to się powiedzie, można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można pozostawić ją. 

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz [najnowszą wersję programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) zainstalowane.
2. Otwórz sesję programu Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Suscription ID>"
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

### <a name="for-vms-deployed-in-resource-group-model"></a>Dla maszyn wirtualnych wdrożonych w modelu grupy zasobów

1.  Przejdź do witryny [Azure Portal]( https://ms.portal.azure.com).
2.  Wybierz maszynę wirtualną, do których to dotyczy.
3.  Wybierz **interfejsy sieciowe**.
4.  Wybierz interfejs sieciowy skojarzony z tym komputerze
5.  Wybierz **konfiguracje adresów IP**.
6.  Wybierz adres IP. 
7.  Jeśli **przypisanie prywatnego adresu IP** nie **statyczne**, zmień ją na **statyczne**.
8.  Zmiana **adresu IP** na inny adres IP, który jest dostępny w tej podsieci.
9. Maszyny wirtualnej zostanie uruchomiony ponownie w celu zainicjowania nowych kart Sieciowych w systemie.
10. Spróbuj użyć protokołu RDP na komputerze. Jeśli to się powiedzie, można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można pozostawić ją. 

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz [najnowszą wersję programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) zainstalowane
2. Otwórz sesję programu Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Suscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureRMAccount
    Select-AzureRMSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzureRMVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzureRMVM
    ```
3. Spróbuj użyć protokołu RDP na komputerze.  Jeśli to się powiedzie, można zmienić adresu prywatnego adresu IP wrócić do oryginalnego, jeśli chcesz. W przeciwnym razie można pozostawić ją. 

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
