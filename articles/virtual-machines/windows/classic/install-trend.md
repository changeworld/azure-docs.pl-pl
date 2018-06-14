---
title: Zainstaluj Trend Micro głębokie zabezpieczeń na maszynie Wirtualnej | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób instalowania i konfigurowania zabezpieczeń Trend Micro na maszynie Wirtualnej utworzone za pomocą klasycznego modelu wdrożenia na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: iainfou
ms.openlocfilehash: 155f4d8c7aa3e0e8f64944435e14112a7970420d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914525"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Jak zainstalować i skonfigurować rozwiązanie Trend Micro Deep Security as a Service na maszynie wirtualnej systemu Windows
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym artykule przedstawiono sposób instalowania i konfigurowania zabezpieczeń głębokie Trend Micro jako usługa na nowej lub istniejącej maszyny wirtualnej (VM) system operacyjny Windows Server. Głębokie zabezpieczeń jako usługa obejmuje ochrony przed złośliwym oprogramowaniem, zapory systemu zapobiegania nieautoryzowanego dostępu i kontroli integralności.

Klient jest instalowany jako rozszerzenia zabezpieczeń, za pomocą agenta maszyny Wirtualnej. Na nowej maszyny wirtualnej należy zainstalować głębokie agenta zabezpieczeń, jak Agent maszyny Wirtualnej jest tworzona automatycznie w portalu Azure.

Istniejącej maszyny Wirtualnej utworzone za pomocą portalu Azure, Azure CLI lub środowiska PowerShell może nie mieć agenta maszyny Wirtualnej. Dla istniejącej maszyny wirtualnej, która nie ma agenta maszyny Wirtualnej musisz pobrać i zainstalować ją najpierw. W tym artykule opisano zarówno sytuacji.

Jeśli masz bieżącej subskrypcji z Trend Micro dla rozwiązania lokalnego, można użyć go w celu ochrony maszyn wirtualnych platformy Azure. Jeśli nie jesteś klientem jeszcze, można założyć dla subskrypcji wersji próbnej. Aby uzyskać więcej informacji o tym rozwiązaniu, zobacz blogu Trend Micro [zabezpieczeń firmy Microsoft Azure VM Agent rozszerzenia dla bezpośrednich](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Zainstaluj agenta głębokie zabezpieczeń na nowej maszyny Wirtualnej

[Portalu Azure](http://portal.azure.com) umożliwia instalowanie rozszerzenia zabezpieczeń Trend Micro, korzystając z obrazu z **Marketplace** można utworzyć maszyny wirtualnej. Jeśli tworzysz jednej maszyny wirtualnej za pomocą portalu jest to prosty sposób dodawania ochrony z Trend Micro.

Za pomocą wpisu z **Marketplace** Otwiera kreatora, który pomaga skonfigurować maszynę wirtualną. Możesz użyć **ustawienia** bloku, trzeci panelu kreatora, aby zainstalować rozszerzenie zabezpieczeń Trend Micro.  Aby uzyskać ogólne instrukcje, zobacz [Utwórz maszynę wirtualną z systemem Windows w portalu Azure](tutorial.md).

Po przejściu do **ustawienia** bloku kreatora, wykonaj następujące czynności:

1. Kliknij przycisk **rozszerzenia**, następnie kliknij przycisk **dodanie rozszerzenia** w okienku dalej.

   ![Rozpocznij dodawanie rozszerzenia][1]

2. Wybierz **głębokie Agent zabezpieczeń** w **nowy zasób** okienka. W okienku głębokie Agent zabezpieczeń kliknij **Utwórz**.

   ![Identyfikowanie agenta głębokie zabezpieczeń][2]

3. Wprowadź **identyfikator dzierżawy** i **hasło aktywacji dzierżawy** rozszerzenia. Opcjonalnie możesz wprowadzić **identyfikator zasad zabezpieczeń**. Następnie kliknij przycisk **OK** do dodania klienta.

   ![Podaj szczegóły rozszerzenia][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Zainstaluj agenta głębokie zabezpieczeń na istniejącej maszyny Wirtualnej
Aby zainstalować agenta na istniejącej maszyny Wirtualnej, potrzebne są następujące elementy:

* Moduł Azure PowerShell, wersja 0.8.2 lub nowszej, zainstalowanych na komputerze lokalnym. Można sprawdzić wersji programu Azure PowerShell, który został zainstalowany przy użyciu **azure Get-Module | wersja formatu tabeli** polecenia. Aby uzyskać instrukcje i łącza do najnowszej wersji, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Zaloguj się przy użyciu subskrypcji platformy Azure `Add-AzureAccount`.
* Zainstalowana na docelowej maszynie wirtualnej agenta maszyny Wirtualnej.

Po pierwsze sprawdzenie, czy Agent maszyny Wirtualnej jest już zainstalowany. Wypełnij pola Nazwa usługi w chmurze i Nazwa maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia programu Azure PowerShell poziomie administratora. Zastąp wszystko w obrębie oferty, w tym < i > znaków.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Jeśli nie znasz usługi w chmurze i nazwy maszyny wirtualnej, należy uruchomić **Get-AzureVM** do wyświetlenia informacji dla wszystkich maszyn wirtualnych w Twojej bieżącej subskrypcji.

Jeśli **hosta zapisu** polecenie zwraca **True**, Agent maszyny Wirtualnej jest zainstalowany. Jeśli zmienna zwraca **False**, zobacz instrukcje i łącze do pobrania w Azure blogu [agenta maszyny Wirtualnej i rozszerzenia — część 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Jeśli jest zainstalowany Agent maszyny Wirtualnej, uruchom następujące polecenia.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Kolejne kroki
Trwa kilka minut, aż do uruchomienia po zainstalowaniu agenta. Po tym musisz aktywować głębokie zabezpieczeń na maszynie wirtualnej, więc można nim zarządzać za głębokie Menedżera zabezpieczeń. Zobacz następujące artykuły, aby uzyskać dodatkowe instrukcje:

* Trend w artykule o tym rozwiązaniu [Instant-On Cloud Security platformy Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* A [przykładowy skrypt programu Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) można skonfigurować maszyny wirtualnej
* [Instrukcje](http://go.microsoft.com/fwlink/?LinkId=404099) przykładowej

## <a name="additional-resources"></a>Zasoby dodatkowe
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]

[Rozszerzenia maszyny Wirtualnej platformy Azure i funkcje]

<!-- Image references -->
[1]: ./media/install-trend/new_vm_Blade3.png
[2]: ./media/install-trend/find_SecurityAgent.png
[3]: ./media/install-trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]:connect-logon.md
[Rozszerzenia maszyny Wirtualnej platformy Azure i funkcje]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
