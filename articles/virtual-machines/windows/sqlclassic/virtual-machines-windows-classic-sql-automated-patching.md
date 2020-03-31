---
title: Automatyczne łatanie maszyn wirtualnych programu SQL Server (klasyczny) | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcję automatycznego wprowadzania poprawek dla maszyn wirtualnych programu SQL Server uruchomionych na platformie Azure przy użyciu klasycznego trybu wdrażania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978080"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatyczne poprawianie programu SQL Server w maszynach wirtualnych platformy Azure (klasyczny)
> [!div class="op_single_selector"]
> * [Menedżer zasobów](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-patching.md)
> 
> 

Automatyczne stosowanie poprawek ustanawia okno konserwacji dla maszyny wirtualnej platformy Azure z programem SQL Server. Automatyczne aktualizacje można instalować tylko w tym oknie konserwacji. W przypadku programu SQL Server gwarantuje to, że aktualizacje systemu i wszelkie skojarzone ponowne uruchomienia systemu występują w najlepszym możliwym czasie dla bazy danych. 

> [!IMPORTANT]
> Instalowane są tylko aktualizacje systemu Windows oznaczone jako **Ważne**. Pozostałe aktualizacje programu SQL Server, na przykład aktualizacje zbiorcze, należy instalować ręcznie. 

Automatyczne stosowanie poprawek zależy od [rozszerzenia agenta IaaS w programie SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję Menedżera zasobów tego artykułu, zobacz [Automatyczne łatanie dla programu SQL Server w Menedżerze zasobów maszyn wirtualnych platformy Azure](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć automatycznego instalowania poprawek, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja programu SQL Server:**

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Program Azure PowerShell:**

* [Zainstaluj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview).

**Rozszerzenie IaaS programu SQL Server:**

* [Zainstaluj rozszerzenie IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować dla automatycznego instalowania poprawek. W przypadku klasycznych maszyn wirtualnych do skonfigurowania tych ustawień należy użyć programu PowerShell.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza automatyczne łatanie dla maszyny wirtualnej platformy Azure. |
| **Harmonogram obsługi** |Codziennie, poniedziałek, wtorek, środa, czwartek, piątek, sobota, niedziela |Harmonogram pobierania i instalowania aktualizacji systemu Windows, SQL Server i firmy Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Lokalny czas rozpoczęcia, aby zaktualizować maszynę wirtualną. |
| **Czas trwania okna konserwacji** |30-180 |Liczba minut dozwolonych do ukończenia pobierania i instalowania aktualizacji. |
| **Kategoria poprawek** |Ważne |Kategoria aktualizacji do pobrania i zainstalowania. |

## <a name="configuration-with-powershell"></a>Konfiguracja z programem PowerShell
W poniższym przykładzie program PowerShell służy do konfigurowania automatycznego poprawiania na istniejącej maszynie wirtualnej programu SQL Server. Polecenie **New-AzureVMSqlServerAutoPatchingConfig** konfiguruje nowe okno konserwacji dla aktualizacji automatycznych.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Na podstawie tego przykładu w poniższej tabeli opisano praktyczny wpływ na docelową maszynę wirtualną platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **Dayofweek** |Poprawki zainstalowane w każdy czwartek. |
| **KonserwacjaWindowStartingHour** |Rozpocznij aktualizacje o godzinie 11:00. |
| **KonserwacjaWindowDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. W zależności od godziny rozpoczęcia muszą one zostać ukończone do godziny 13:00. |
| **Kategoria poprawek** |Jedynym możliwym ustawieniem dla tego parametru jest "Ważne". |

Zainstalowanie i skonfigurowanie agenta IaaS programu SQL Server może potrwać kilka minut.

Aby wyłączyć automatyczne łatanie, uruchom ten sam skrypt bez parametru -Enable do new-AzureVMSqlServerAutoPatchingConfig. Podobnie jak w przypadku instalacji, wyłączenie automatycznego instalowania poprawek może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [Rozszerzenie agenta programu SQL Server IaaS](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

