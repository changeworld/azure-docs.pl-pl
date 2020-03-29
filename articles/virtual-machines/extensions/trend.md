---
title: Instalowanie programu Trend Micro Deep Security na maszynie Wirtualnej
description: W tym artykule opisano sposób instalowania i konfigurowania zabezpieczeń firmy Trend Micro na maszynie Wirtualnej utworzonej przy za pomocą klasycznego modelu wdrażania na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: cffd2eab3a616b4d16d847d0f2e1a26655f40459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919927"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Jak zainstalować i skonfigurować trend Trend Micro Deep Security jako usługa na maszynie Wirtualnej systemu Windows

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
W tym artykule przedstawiono sposób instalowania i konfigurowania programu Trend Micro Deep Security jako usługi na nowej lub istniejącej maszynie wirtualnej z systemem Windows Server. Deep Security as a Service obejmuje ochronę przed złośliwym oprogramowaniem, zaporę, system zapobiegania włamaniom i monitorowanie integralności.

Klient jest instalowany jako rozszerzenie zabezpieczeń za pośrednictwem agenta maszyny Wirtualnej. Na nowej maszynie wirtualnej należy zainstalować agenta zabezpieczeń deep, ponieważ agent maszyny wirtualnej jest tworzony automatycznie przez portal Azure.

Istniejąca maszyna wirtualna utworzona przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell może nie mieć agenta maszyny Wirtualnej. Dla istniejącej maszyny wirtualnej, która nie ma agenta maszyny Wirtualnej, należy najpierw pobrać i zainstalować. Ten artykuł obejmuje obie sytuacje.

Jeśli masz bieżącą subskrypcję firmy Trend Micro dla rozwiązania lokalnego, możesz jej użyć, aby chronić maszyny wirtualne platformy Azure. Jeśli nie jesteś jeszcze klientem, możesz zarejestrować się w subskrypcji próbnej. Aby uzyskać więcej informacji na temat tego rozwiązania, zobacz wpis w blogu [firmy Microsoft Azure VM Agent Extension For Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalowanie agenta zabezpieczeń głębokiego na nowej maszynie wirtualnej

[Portal Azure](https://portal.azure.com) umożliwia zainstalowanie rozszerzenia zabezpieczeń firmy Trend Micro podczas tworzenia maszyny wirtualnej za pomocą obrazu z **portalu Marketplace.** Jeśli tworzysz jedną maszynę wirtualną, korzystanie z portalu jest łatwym sposobem na dodanie ochrony przed programem Trend Micro.

Za pomocą wpisu z **portalu Marketplace** otwiera kreatora, który pomaga skonfigurować maszynę wirtualną. Do zainstalowania rozszerzenia zabezpieczeń Trend Micro użyj bloku **Ustawienia,** trzeciego panelu kreatora.  Aby uzyskać ogólne instrukcje, zobacz [Tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure portal](../windows/classic/tutorial.md).

Po przejściu do bloku **Ustawienia** kreatora wykonaj następujące czynności:

1. Kliknij **pozycję Rozszerzenia**, a następnie kliknij pozycję Dodaj **rozszerzenie** w następnym okienku.

   ![Rozpoczynanie dodawania rozszerzenia][1]

2. Wybierz **pozycję Głęboki agent zabezpieczeń** w okienku Nowy **zasób.** W okienku Agent zabezpieczeń głębokich kliknij pozycję **Utwórz**.

   ![Identyfikowanie agenta zabezpieczeń głębokiego][2]

3. Wprowadź **identyfikator dzierżawy** i **hasło aktywacji dzierżawy** dla rozszerzenia. Opcjonalnie można wprowadzić **identyfikator zasad zabezpieczeń**. Następnie kliknij przycisk **OK,** aby dodać klienta.

   ![Podaj szczegóły rozszerzenia][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalowanie agenta zabezpieczeń głębokiego na istniejącej maszynie wirtualnej
Aby zainstalować agenta na istniejącej maszynie wirtualnej, potrzebne są następujące elementy:

* Moduł programu Azure PowerShell w wersji 0.8.2 lub nowszej, zainstalowany na komputerze lokalnym. Można sprawdzić wersję programu Azure PowerShell, który został zainstalowany przy użyciu **get-module azure | format-tabela wersji** polecenia. Aby uzyskać instrukcje i łącze do najnowszej wersji, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview). Zaloguj się do subskrypcji `Add-AzureAccount`platformy Azure przy użyciu programu .
* Agent maszyny Wirtualnej zainstalowany na docelowej maszynie wirtualnej.

Najpierw sprawdź, czy agent maszyny Wirtualnej jest już zainstalowany. Wprowadź nazwę usługi w chmurze i nazwę maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia programu Azure PowerShell na poziomie administratora. Zastąp wszystko w cudzysłowie, w tym znaki < i >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Jeśli nie znasz nazwy usługi w chmurze i maszyny wirtualnej, uruchom **program Get-AzureVM,** aby wyświetlić te informacje dla wszystkich maszyn wirtualnych w bieżącej subskrypcji.

Jeśli polecenie **hosta zapisu** zwraca **wartość True,** jest zainstalowany agent maszyny Wirtualnej. Jeśli zwraca **False**, zobacz instrukcje i link do pobrania w blogu azure [Agent maszyny Wirtualnej i rozszerzeń - Część 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Jeśli agent maszyny Wirtualnej jest zainstalowany, uruchom te polecenia.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
Trwa kilka minut dla agenta, aby rozpocząć pracę po zainstalowaniu. Następnie należy aktywować deep security na maszynie wirtualnej, dzięki czemu może być zarządzany przez Deep Security Manager. Dodatkowe instrukcje można znaleźć w następujących artykułach:

* Artykuł trendu na temat tego rozwiązania, [Instant-On Cloud Security for Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* [Przykładowy skrypt programu Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) do konfigurowania maszyny wirtualnej
* [Instrukcje](https://go.microsoft.com/fwlink/?LinkId=404099) dotyczące próbki

## <a name="additional-resources"></a>Zasoby dodatkowe
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]

[Rozszerzenia i funkcje maszyn wirtualnych platformy Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]:../windows/classic/connect-logon.md
[Rozszerzenia i funkcje maszyn wirtualnych platformy Azure]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
