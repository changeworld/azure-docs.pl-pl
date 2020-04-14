---
title: Konfigurowanie usługi WinRM po utworzeniu maszyny wirtualnej platformy Azure | Azure Marketplace
description: W tym artykule wyjaśniono, jak skonfigurować usługę Windows Remote Management (WinRM) po utworzeniu maszyny wirtualnej hostowanego na platformie Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: b80325594eedb87293c31de3236bb4690eb89e05
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273022"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurowanie usługi WinRM po utworzeniu maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty maszyny wirtualnej platformy Azure,](https://aka.ms/CreateAzureVMoffer) aby zarządzać zmigrowanymi ofertami.

W tym artykule wyjaśniono, jak skonfigurować istniejącą maszynę wirtualną hostowane na platformie Azure (VM), aby włączyć usługę WinRM za pośrednictwem protokołu HTTPS.  Ta konfiguracja dotyczy tylko maszyn wirtualnych z systemem Windows i wymaga następującego dwuetapowego procesu:

1. Włącz ruch portowy dla protokołu WinRM za pośrednictwem protokołu HTTPS.  To ustawienie zostanie skonfigurowane dla maszyny Wirtualnej w witrynie Azure portal.
2. Skonfiguruj maszynę wirtualną, aby włączyć usługę WinRM, uruchamiając dostarczone skrypty programu PowerShell.


## <a name="enabling-port-traffic"></a>Włączanie ruchu portowego

Protokół WinRM over HTTPS używa portu 5986, który nie jest domyślnie włączony na wstępnie skonfigurowanych maszynach wirtualnych systemu Windows oferowanych w portalu Azure Marketplace. Aby włączyć ten protokół, należy wykonać następujące czynności, aby dodać nową regułę do sieciowej grupy zabezpieczeń (NSG) za pomocą [witryny Azure portal](https://portal.azure.com).  Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).

1. Przejdź do bloku **Maszyny wirtualne >**   < *nazwa* >   wirtualna>** Ustawienia/Sieć**.
2. Kliknij nazwę sieciowej sieciowej sieciowej (w tym przykładzie **testvm11002**), aby wyświetlić jej właściwości:

    ![Właściwości sieciowej grupy zabezpieczeń](./media/nsg-properties.png)
 
3. W obszarze **Ustawienia**wybierz pozycję **Reguły zabezpieczeń ruchu przychodzącego,** aby wyświetlić ten blok.
4. Kliknij **przycisk +Dodaj,** aby `WinRM_HTTPS` utworzyć nową regułę wywołaną dla portu TCP 5986.

    ![Dodawanie reguły zabezpieczeń sieci przychodzącej](./media/nsg-new-rule.png)

5. Po zakończeniu dostarczania wartości kliknij **przycisk OK.**  Lista przychodzących reguł zabezpieczeń powinna zawierać następujące nowe wpisy.

    ![Lista reguł zabezpieczeń sieci przychodzącej](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurowanie maszyny wirtualnej w celu włączenia usługi WinRM 

Aby włączyć i skonfigurować funkcję Zdalnego zarządzania systemem Windows na maszynie Wirtualnej systemu Windows, należy wykonać następujące czynności.   

1. Ustanawianie połączenia pulpitu zdalnego z maszyną wirtualną hostowanym na platformie Azure.  Aby uzyskać więcej informacji, zobacz [Jak połączyć się i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Pozostałe kroki zostaną uruchomione na maszynie wirtualnej.
2. Pobierz następujące pliki i zapisz je w folderze na maszynie Wirtualnej:
    - [Konfigurowanie plikuWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [Makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Otwórz **konsolę programu PowerShell** z podwyższonymi uprawnieniami **(Uruchom jako administrator).** 
4. Uruchom następujące polecenie, podając wymagany parametr: w pełni kwalifikowaną nazwę domeny (FQDN) dla maszyny Wirtualnej: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Skrypt konfiguracji programu PowerShell 1](./media/powershell-file1.png)

    Ten skrypt zależy od pozostałych dwóch plików w tym samym folderze.


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi WinRM można przystąpić do [wdrażania maszyny wirtualnej z jej elementów VHD.](./cpp-deploy-vm-vhd.md)
