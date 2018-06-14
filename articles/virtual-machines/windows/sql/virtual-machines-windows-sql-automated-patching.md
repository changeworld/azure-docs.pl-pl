---
title: Automatyczne stosowanie poprawek dla programu SQL Server VMs (Resource Manager) | Dokumentacja firmy Microsoft
description: Zawiera opis funkcji Automatyczne stosowanie poprawek dla programu SQL Server maszyn wirtualnych działających na platformie Azure za pomocą Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 398e682db6c42bd7f4864113ddf10a6a75e2b65b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29850620"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatyczne stosowanie poprawek dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatyczne Patching ustanawia okna obsługi dla maszyny wirtualnej platformy Azure działa program SQL Server. Automatyczne aktualizacje można zainstalować tylko w tym oknie obsługi. Dla programu SQL Server to rescriction gwarantuje, że aktualizacje systemu i ponowne uruchomienia, skojarzone są wykonywane w najlepiej czasie dla bazy danych. 

> [!IMPORTANT]
> Tylko aktualizacje systemu Windows oznaczone **ważne** są zainstalowane. Inne aktualizacje programu SQL Server, takich jak aktualizacje zbiorcze, należy zainstalować ręcznie. 

Automatyczne Patching jest zależna od [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć automatyczne stosowanie poprawek, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja programu SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Zainstaluj najnowsze poleceń programu PowerShell Azure](/powershell/azure/overview) Jeśli zamierzasz skonfigurować automatyczne stosowanie poprawek przy użyciu programu PowerShell.

> [!NOTE]
> Automatyczne Patching zależy od rozszerzenie agenta programu SQL Server IaaS. Bieżący obrazów Galeria maszyny wirtualnej SQL dodać to rozszerzenie domyślnie. Aby uzyskać więcej informacji, zobacz [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować dla automatyczne stosowanie poprawek. Kroki konfiguracji rzeczywisty zależy od przy użyciu portalu Azure lub poleceń programu PowerShell systemu Windows Azure.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącza/wyłącza (wyłączone) |Włącza lub wyłącza automatyczne stosowanie poprawek dla maszyny wirtualnej platformy Azure. |
| **Harmonogram konserwacji** |Codziennie, poniedziałek, Wtorek, środę, czwartek, piątek, sobota, niedziela |Harmonogram pobieranie i instalowanie aktualizacji systemu Windows, programu SQL Server i Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Godzina rozpoczęcia lokalnego można zaktualizować maszyny wirtualnej. |
| **Czas trwania okna obsługi** |30-180 |Liczba minut uprawnienia do pobierania i instalacji aktualizacji. |
| **Patch Category** |Ważne | Kategoria aktualizacji systemu Windows, aby pobrać i zainstalować.|

## <a name="configuration-in-the-portal"></a>Konfiguracja w portalu
Aby skonfigurować automatyczne stosowanie poprawek, podczas udostępniania lub dla istniejących maszyn wirtualnych, można użyć portalu Azure.

### <a name="new-vms"></a>Nowe maszyny wirtualne
Użyj portalu Azure, aby skonfigurować automatyczne stosowanie poprawek, podczas tworzenia nowej maszyny wirtualnej serwera SQL w modelu wdrażania usługi Resource Manager.

W **ustawienia programu SQL Server** bloku, wybierz opcję **automatyczne stosowanie poprawek**. Poniżej przedstawiono zrzut ekranu na portalu Azure **automatyczne stosowanie poprawek SQL** bloku.

![SQL automatyczne stosowanie poprawek w portalu Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Dla kontekstu, zobacz temat pełną na [obsługi maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne
W przypadku istniejących maszyn wirtualnych programu SQL Server należy wybrać maszyny wirtualnej programu SQL Server. Następnie wybierz **konfigurację programu SQL Server** sekcji **ustawienia** bloku.

![SQL automatyczne stosowanie poprawek dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

W **konfigurację programu SQL Server** bloku, kliknij przycisk **Edytuj** przycisku na automatyczne stosowanie poprawek sekcji.

![Skonfiguruj automatyczne stosowanie poprawek SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Gdy skończysz, kliknij przycisk **OK** przycisk w dolnej części **konfigurację programu SQL Server** bloku, aby zapisać zmiany.

Jeśli włączasz automatyczne stosowanie poprawek po raz pierwszy, Azure konfiguruje agenta programu SQL Server IaaS w tle. W tym czasie portalu Azure może nie informować, że skonfigurowano automatyczne stosowanie poprawek. Poczekaj kilka minut dla agenta, który ma zostać zainstalowany, skonfigurowany. Po utworzeniu tego portalu Azure odzwierciedla nowych ustawień.

> [!NOTE]
> Można również skonfigurować automatyczne stosowanie poprawek za pomocą szablonu. Aby uzyskać więcej informacji, zobacz [szablon Szybki Start Azure automatyczne stosowanie poprawek](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
Po aprowizacji maszyny Wirtualnej SQL, skonfiguruj automatyczne stosowanie poprawek za pomocą programu PowerShell.

W poniższym przykładzie programu PowerShell służy do konfigurowania automatyczne stosowanie poprawek na istniejącej maszyny Wirtualnej programu SQL Server. **AzureRM.Compute\New AzureRmVMSqlServerAutoPatchingConfig** polecenie konfiguruje nowe okno obsługi, aktualizacje automatyczne.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Jeśli rozszerzenie nie jest już zainstalowany, instalowania rozszerzenia uruchamia ponownie usługę SQL Server.

Na podstawie tego przykładu, w poniższej tabeli opisano praktyczne wpływa na docelowej maszynie Wirtualnej platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Każdy czwartek zainstalowanych poprawek. |
| **MaintenanceWindowStartingHour** |Aktualizacje BEGIN o 11:00. |
| **MaintenanceWindowsDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. Na podstawie czasu rozpoczęcia, użytkownicy muszą wykonać przez 1:00 pm. |
| **PatchCategory** |Jedyną możliwą ustawienie tego parametru to **ważne**. Spowoduje to zainstalowanie usługi Windows update oznaczone jako ważne; nie instaluje wszystkie aktualizacje programu SQL Server, które nie znajdują się w tej kategorii. |

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta programu SQL Server IaaS.

Aby wyłączyć automatyczne stosowanie poprawek, uruchom ten sam skrypt bez **-Włącz** parametr **AzureRM.Compute\New AzureRmVMSqlServerAutoPatchingConfig**. Brak **-Włącz** parametru sygnały polecenie, aby wyłączyć funkcję.

## <a name="next-steps"></a>Kolejne kroki
Informacje o innych zadaniach automatyzacji dostępny, zobacz [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

