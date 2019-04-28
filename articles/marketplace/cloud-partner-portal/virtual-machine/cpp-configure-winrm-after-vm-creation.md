---
title: Konfigurowanie usługi WinRM po utworzeniu maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Opisano sposób konfigurowania Windows Remote Management (WinRM), po utworzeniu maszyny wirtualnej hostowanej na platformie Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 6350d5bde737c46cf14a9aef75a7ec57260a6afa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744604"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurowanie usługi WinRM po utworzeniu maszyny wirtualnej

W tym artykule opisano sposób konfigurowania istniejącej hostowanymi na platformie Azure maszyny wirtualnej (VM), aby włączyć usługę WinRM over HTTPS.  Ta konfiguracja ma zastosowanie tylko do maszyn wirtualnych z systemem Windows i wymaga następujący dwuetapowy proces:

1. Włącz ruch na porcie dla usługi WinRM za pośrednictwem protokołu HTTPS.  Skonfiguruj to ustawienie dla maszyny Wirtualnej w witrynie Azure portal.
2. Skonfiguruj maszynę Wirtualną, aby włączyć usługę WinRM, uruchamiając podane skrypty programu PowerShell.


## <a name="enabling-port-traffic"></a>Włączanie ruch na porcie

Usługa WinRM za pośrednictwem protokołu HTTPS korzysta z portu 5896, która nie jest włączona domyślnie na wstępnie skonfigurowanych Windows maszyn wirtualnych oferowanych w portalu Azure Marketplace. Aby włączyć ten protokół, użyj następujące czynności można dodać nowej reguły do sieciowej grupy zabezpieczeń (NSG), za pomocą [witryny Azure portal](https://portal.azure.com).  Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Przejdź do bloku **maszyny wirtualne >**  <*nazwa_maszyny_wirtualnej*>  **> Ustawienia/sieci**.
2.  Kliknij nazwę sieciową grupę zabezpieczeń (w tym przykładzie **testvm11002**) aby wyświetlić jego właściwości:

    ![Właściwości grupy zabezpieczeń sieci](./media/nsg-properties.png)
 
3. W obszarze **ustawienia**, wybierz opcję **reguły zabezpieczeń dla ruchu przychodzącego** do wyświetlania tego bloku.
4. Kliknij przycisk **+ Dodaj** Aby utworzyć nową regułę o nazwie `WinRM_HTTPS` protokołu TCP dla portu 5986.

    ![Dodawanie reguły zabezpieczeń ruchu przychodzącego](./media/nsg-new-rule.png)

5. Kliknij przycisk **OK** po zakończeniu podawania wartości.  Lista reguł zabezpieczeń ruchu przychodzącego może zawierać następujące nowe wpisy.

    ![Lista reguł zabezpieczeń ruchu przychodzącego](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Skonfiguruj maszynę Wirtualną, aby włączyć usługę WinRM 

Wykonaj następujące kroki, aby włączyć i skonfigurować funkcję Windows Remote Management na maszynie Wirtualnej Windows.   

1. Nawiąż połączenie pulpitu zdalnego do maszyny Wirtualnej hostowanej na platformie Azure.  Aby uzyskać więcej informacji, zobacz [jak połączyć i zaloguj się do platformy Azure maszynę wirtualną z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Pozostałe kroki zostanie uruchomiony na maszynie Wirtualnej.
2. Pobierz następujące pliki i zapisać je do folderu na maszynie Wirtualnej:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Otwórz **konsolę programu PowerShell** z podwyższonym poziomem uprawnień (**Uruchom jako Administrator**). 
4. Uruchom następujące polecenie, podając wymaganego parametru: w pełni kwalifikowana nazwa domeny (FQDN) dla maszyny Wirtualnej: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Skrypt programu PowerShell konfiguracji 1](./media/powershell-file1.png)

    Ten skrypt zależy od innych dwa pliki znajdujące się w tym samym folderze.


## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu usługi WinRM można przystąpić do [wdrażanie maszyny Wirtualnej z jej składowych wirtualnych dysków twardych](./cpp-deploy-vm-vhd.md).
