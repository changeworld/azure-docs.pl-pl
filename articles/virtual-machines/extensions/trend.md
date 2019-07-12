---
title: Instalowanie oprogramowania Trend Micro Deep Security na maszynie Wirtualnej | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zainstalować i skonfigurować Trend Micro zabezpieczeń na maszynie Wirtualnej utworzone za pomocą klasycznego modelu wdrażania na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: roiyz
ms.openlocfilehash: 0e70bc692357e9db9fa02a2f210320507b1b5824
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705900"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Jak zainstalować i skonfigurować rozwiązanie Trend Micro Deep Security as a Service na maszynie wirtualnej systemu Windows
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
W tym artykule pokazano, jak zainstalować i skonfigurować Trend Micro Deep Security as a Service, w nowej lub istniejącej maszyny wirtualnej (VM) systemem Windows Server. Deep Security jako usługi obejmuje ochronę chroniące przed złośliwym kodem, zapory, system zapobiegania włamaniom i monitorowania integralności.

Klient jest instalowany jako rozszerzenie zabezpieczeń, za pośrednictwem agenta maszyny Wirtualnej. Na nowej maszyny wirtualnej należy zainstalować Deep Security Agent, ponieważ Agent maszyny Wirtualnej jest tworzony automatycznie w portalu Azure.

Istniejącej maszyny Wirtualnej utworzone za pomocą witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell może nie mieć agenta maszyny Wirtualnej. Dla istniejącej maszyny wirtualnej, która nie ma agenta maszyny Wirtualnej musisz pobrać i zainstalować ją najpierw. Ten artykuł dotyczy zarówno sytuacji.

W przypadku bieżącej subskrypcji z Trend Micro rozwiązania w środowisku lokalnym, można użyć go w celu ochrony maszyn wirtualnych platformy Azure. Jeśli nie jesteś klientem jeszcze, możesz zarejestrować się w przypadku subskrypcji wersji próbnej. Aby uzyskać więcej informacji o tym rozwiązaniu, zobacz wpis w blogu Trend Micro [zabezpieczeń platformy Microsoft Azure VM Agent rozszerzenia dla głębokiego](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Zainstaluj Deep Security Agent na utworzonej nowej maszynie Wirtualnej

[Witryny Azure portal](https://portal.azure.com) umożliwia zainstalowanie rozszerzenia zabezpieczeń Trend Micro, korzystając z obrazu z **Marketplace** do utworzenia maszyny wirtualnej. Jeśli tworzysz pojedynczej maszyny wirtualnej przy użyciu portalu to prosty sposób można dodawać ochronę z Trend Micro.

Za pomocą wpisu z **Marketplace** Otwiera kreatora, który pomoże Ci skonfigurować maszynę wirtualną. Możesz użyć **ustawienia** bloku, trzeci panelu kreatora, można zainstalować rozszerzenia zabezpieczeń Trend Micro.  Aby uzyskać ogólne instrukcje, zobacz [tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure portal](../windows/classic/tutorial.md).

Po przejściu do **ustawienia** bloku kreatora, wykonaj następujące czynności:

1. Kliknij przycisk **rozszerzenia**, następnie kliknij przycisk **Dodaj rozszerzenie** w kolejnym okienku.

   ![Rozpocznij dodawanie rozszerzenia][1]

2. Wybierz **Deep Security Agent** w **nowy zasób** okienka. W okienku Deep Security Agent kliknij **Utwórz**.

   ![Identyfikowanie Deep Security Agent][2]

3. Wprowadź **identyfikator dzierżawy** i **hasło aktywacyjne dzierżawy** dla rozszerzenia. Opcjonalnie można wprowadzić **identyfikator zasad zabezpieczeń**. Następnie kliknij przycisk **OK** do dodania klienta.

   ![Podaj szczegóły rozszerzenia][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Zainstaluj Deep Security Agent istniejącej maszyny wirtualnej
Aby zainstalować agenta na istniejącej maszyny Wirtualnej, potrzebne są następujące elementy:

* Moduł Azure PowerShell, wersja 0.8.2 lub nowszej, zainstalowanych na komputerze lokalnym. Możesz sprawdzić wersję programu Azure PowerShell, który został zainstalowany przy użyciu **Get-Module azure | format-table wersji** polecenia. Aby uzyskać instrukcje i łącza do najnowszej wersji, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview). Zaloguj się do swojej subskrypcji platformy Azure za pomocą `Add-AzureAccount`.
* Agent maszyny Wirtualnej, zainstalowane na docelowej maszynie wirtualnej.

Po pierwsze sprawdzenie, czy Agent maszyny Wirtualnej jest już zainstalowany. Wypełnij pola nazwy usługi w chmurze i Nazwa maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia programu Azure PowerShell z poziomu administratora. Zastąp wszystkie elementy w ramach oferty, w tym < a > znaków.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Jeśli nie znasz usługi w chmurze i nazwy maszyny wirtualnej, uruchom **Get-AzureVM** do wyświetlenia informacji dla wszystkich maszyn wirtualnych w Twojej bieżącej subskrypcji.

Jeśli **zapisu hosta** polecenie **True**, jest zainstalowany Agent maszyny Wirtualnej. Jeśli zostanie zwrócona **False**, zobacz instrukcje i linkiem umożliwiającym pobranie wpis w blogu platformy Azure [VM Agent i rozszerzenia — część 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Jeśli jest zainstalowany Agent maszyny Wirtualnej, uruchom następujące polecenia.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Kolejne kroki
Trwa kilka minut, zanim agenta aby rozpocząć uruchamianie po jej zainstalowaniu. Po tym musisz aktywować Deep Security na maszynie wirtualnej, aby można było zarządzać przez głębokiego Menedżera zabezpieczeń. Zobacz następujące artykuły, aby uzyskać dodatkowe informacje:

* Trend w artykule o tym rozwiązaniu [Instant-On Cloud Security dla systemu Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* A [przykładowy skrypt programu Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) można skonfigurować maszyny wirtualnej
* [Instrukcje](https://go.microsoft.com/fwlink/?LinkId=404099) dla przykładu

## <a name="additional-resources"></a>Dodatkowe zasoby
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]

[Rozszerzenia maszyn wirtualnych platformy Azure i funkcji]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]:../windows/classic/connect-logon.md
[Rozszerzenia maszyn wirtualnych platformy Azure i funkcji]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
