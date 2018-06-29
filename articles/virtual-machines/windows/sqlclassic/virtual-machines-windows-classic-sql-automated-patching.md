---
title: Automatyczne stosowanie poprawek dla programu SQL Server VMs (klasyczne) | Dokumentacja firmy Microsoft
description: Zawiera opis funkcji Automatyczne stosowanie poprawek dla programu SQL Server maszyn wirtualnych działających na platformie Azure przy użyciu trybu klasycznego wdrożenia.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 4bb13ec9d835959273801c2f53c8d736491080a6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061339"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatyczne stosowanie poprawek dla programu SQL Server na maszynach wirtualnych platformy Azure (klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-patching.md)
> 
> 

Automatyczne Patching ustanawia okna obsługi dla maszyny wirtualnej platformy Azure działa program SQL Server. Automatyczne aktualizacje można zainstalować tylko w tym oknie obsługi. Dla programu SQL Server zapewnia, że aktualizacje systemu i ponowne uruchomienia, skojarzone są wykonywane w najlepiej czasie dla bazy danych. 

> [!IMPORTANT]
> Tylko aktualizacje systemu Windows oznaczone **ważne** są zainstalowane. Inne aktualizacje programu SQL Server, takich jak aktualizacje zbiorcze, należy zainstalować ręcznie. 

Automatyczne Patching jest zależna od [rozszerzenia agenta programu SQL Server IaaS](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję usługi Resource Manager w tym artykule, zobacz [automatyczne stosowanie poprawek dla programu SQL Server w Menedżerze zasobów maszyn wirtualnych Azure](../sql/virtual-machines-windows-sql-automated-patching.md).

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

**Program Azure PowerShell**:

* [Zainstaluj najnowsze poleceń programu PowerShell Azure](/powershell/azure/overview).

**Rozszerzenia programu SQL Server IaaS**:

* [Zainstaluj rozszerzenie IaaS serwera SQL](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować dla automatyczne stosowanie poprawek. Klasycznych maszyn wirtualnych należy skonfigurować te ustawienia za pomocą programu PowerShell.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącza/wyłącza (wyłączone) |Włącza lub wyłącza automatyczne stosowanie poprawek dla maszyny wirtualnej platformy Azure. |
| **Harmonogram konserwacji** |Codziennie, poniedziałek, Wtorek, środę, czwartek, piątek, sobota, niedziela |Harmonogram pobieranie i instalowanie aktualizacji systemu Windows, programu SQL Server i Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Godzina rozpoczęcia lokalnego można zaktualizować maszyny wirtualnej. |
| **Czas trwania okna obsługi** |30-180 |Liczba minut uprawnienia do pobierania i instalacji aktualizacji. |
| **Poprawka kategorii** |Ważne |Kategoria aktualizacje do pobrania i zainstalowania. |

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
W poniższym przykładzie programu PowerShell służy do konfigurowania automatyczne stosowanie poprawek na istniejącej maszyny Wirtualnej programu SQL Server. **AzureVMSqlServerAutoPatchingConfig nowy** polecenie konfiguruje nowe okno obsługi, aktualizacje automatyczne.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Na podstawie tego przykładu, w poniższej tabeli opisano praktyczne wpływa na docelowej maszynie Wirtualnej platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Każdy czwartek zainstalowanych poprawek. |
| **MaintenanceWindowStartingHour** |Aktualizacje BEGIN o 11:00. |
| **MaintenanceWindowDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. Na podstawie czasu rozpoczęcia, użytkownicy muszą wykonać przez 1:00 pm. |
| **PatchCategory** |Ustawienie tylko dla tego parametru jest "Ważne". |

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta programu SQL Server IaaS.

Aby wyłączyć automatyczne stosowanie poprawek, uruchom takie same skrypt bez parametru - Enable, aby AzureVMSqlServerAutoPatchingConfig nowy. Podobnie jak w przypadku instalacji, może upłynąć kilka minut, aby wyłączyć automatyczne stosowanie poprawek.

## <a name="next-steps"></a>Kolejne kroki
Informacje o innych zadaniach automatyzacji dostępny, zobacz [rozszerzenia agenta programu SQL Server IaaS](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

