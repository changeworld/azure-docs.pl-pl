---
title: Automatyczne łatanie maszyn wirtualnych programu SQL Server (Menedżer zasobów) | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcję automatycznego wprowadzania poprawek dla maszyn wirtualnych programu SQL Server uruchomionych na platformie Azure przy użyciu Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 292f3e8819f6f9f4b2989423814e02dfcfb4bfdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127669"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatyczne stosowanie poprawek dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Menedżer zasobów](virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatyczne stosowanie poprawek ustanawia okno konserwacji dla maszyny wirtualnej platformy Azure z programem SQL Server. Automatyczne aktualizacje można instalować tylko w tym oknie konserwacji. W przypadku programu SQL Server to ograniczenie gwarantuje, że aktualizacje systemu i wszystkie związane z nimi ponowne uruchomienia będą występować w czasie najlepszym dla bazy danych. 

> [!IMPORTANT]
> Instalowane są tylko aktualizacje systemu Windows i programu SQL Server oznaczone jako **ważne** lub **krytyczne.** Inne aktualizacje programu SQL Server, takie jak dodatki Service Pack i aktualizacje zbiorcze, które nie są oznaczone jako **ważne** lub **krytyczne,** muszą być instalowane ręcznie. 

Automatyczne stosowanie poprawek zależy od [rozszerzenia agenta IaaS w programie SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć automatycznego instalowania poprawek, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja programu SQL Server:**

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Program Azure PowerShell:**

* [Zainstaluj najnowsze polecenia programu Azure PowerShell,](/powershell/azure/overview) jeśli planujesz skonfigurować automatyczne łatanie za pomocą programu PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatyczne łatanie opiera się na rozszerzeniu agenta IaaS programu SQL Server. Bieżące obrazy galerii maszyn wirtualnych SQL domyślnie dodają to rozszerzenie. Aby uzyskać więcej informacji, zobacz [ROZSZERZENIE AGENTA PROGRAMU SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować dla automatycznego instalowania poprawek. Kroki konfiguracji rzeczywistej różnią się w zależności od tego, czy używasz poleceń azure portal lub programu Azure Windows PowerShell.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza automatyczne łatanie dla maszyny wirtualnej platformy Azure. |
| **Harmonogram obsługi** |Codziennie, poniedziałek, wtorek, środa, czwartek, piątek, sobota, niedziela |Harmonogram pobierania i instalowania aktualizacji systemu Windows, SQL Server i firmy Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Lokalny czas rozpoczęcia, aby zaktualizować maszynę wirtualną. |
| **Czas trwania okna konserwacji** |30-180 |Liczba minut dozwolonych do ukończenia pobierania i instalowania aktualizacji. |
| **Kategoria poprawek** |Ważne | Kategoria aktualizacji systemu Windows do pobrania i zainstalowania.|

## <a name="configuration-in-the-portal"></a>Konfiguracja w portalu
Za pomocą witryny Azure Portal można skonfigurować automatyczne poprawianie podczas inicjowania obsługi administracyjnej lub dla istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
Użyj portalu Azure, aby skonfigurować automatyczne poprawianie podczas tworzenia nowej maszyny wirtualnej programu SQL Server w modelu wdrażania Menedżera zasobów.

Na karcie **Ustawienia programu SQL Server** wybierz pozycję Zmień **konfigurację** w obszarze **Automatyczne łatanie**. Poniższy zrzut ekranu portalu platformy Azure przedstawia blok **automatycznego instalowania poprawek SQL.**

![Automatyczne łatanie SQL w witrynie Azure portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

W kontekście zobacz pełny temat [inicjowania obsługi administracyjnej maszyny wirtualnej programu SQL Server](virtual-machines-windows-portal-sql-server-provision.md)na platformie Azure .

### <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych programu SQL Server otwórz [zasób maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) i wybierz **pozycję Poprawki** w obszarze **Ustawienia**. 

![Automatyczne łatanie SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Po zakończeniu kliknij przycisk **OK** na dole bloku **konfiguracji programu SQL Server,** aby zapisać zmiany.

Jeśli po raz pierwszy włączysz automatyczne łatanie, platforma Azure konfiguruje agenta IaaS programu SQL Server w tle. W tym czasie witryny Azure portal może nie pokazać, że automatyczne poprawki jest skonfigurowany. Poczekaj kilka minut na zainstalowanie agenta, skonfigurowane. Następnie portal Azure odzwierciedla nowe ustawienia.

## <a name="configuration-with-powershell"></a>Konfiguracja z programem PowerShell
Po zainicjowaniu obsługi administracyjnej maszyny Wirtualnej SQL użyj programu PowerShell do skonfigurowania automatycznego poprawiania.

W poniższym przykładzie program PowerShell służy do konfigurowania automatycznego poprawiania na istniejącej maszynie wirtualnej programu SQL Server. Polecenie **New-AzVMSqlServerAutoPatchingConfig** konfiguruje nowe okno konserwacji dla automatycznych aktualizacji.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Jeśli rozszerzenie nie jest jeszcze zainstalowane, zainstalowanie rozszerzenia uruchamia ponownie usługę PROGRAMU SQL Server.

Na podstawie tego przykładu w poniższej tabeli opisano praktyczny wpływ na docelową maszynę wirtualną platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **Dayofweek** |Poprawki zainstalowane w każdy czwartek. |
| **KonserwacjaWindowStartingHour** |Rozpocznij aktualizacje o godzinie 11:00. |
| **KonserwacjaWindowsDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. W zależności od godziny rozpoczęcia muszą one zostać ukończone do godziny 13:00. |
| **Kategoria poprawek** |Jedynym możliwym ustawieniem dla tego parametru jest **Ważne**. Spowoduje to zainstalowanie aktualizacji systemu Windows oznaczonej jako Ważne; nie instaluje żadnych aktualizacji programu SQL Server, które nie są uwzględnione w tej kategorii. |

Zainstalowanie i skonfigurowanie agenta IaaS programu SQL Server może potrwać kilka minut.

Aby wyłączyć automatyczne łatanie, uruchom ten sam skrypt bez parametru **-Enable** do **new-AzVMSqlServerAutoPatchingConfig**. Brak parametru **-Enable** sygnalizuje polecenie wyłączenia funkcji.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [Rozszerzenie agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md)

