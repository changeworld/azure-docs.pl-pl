---
title: Automatyczne stosowanie poprawek dla maszyn wirtualnych programu SQL Server (klasyczny) | Dokumentacja firmy Microsoft
description: Wyjaśnia funkcji Automatyczne stosowanie poprawek dla maszyn wirtualnych SQL Server działających na platformie Azure przy użyciu trybu klasycznego wdrożenia.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa912e3eb76d72e7a79c83d7e51d493310bd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362139"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatyczne stosowanie poprawek programu SQL Server na maszynach wirtualnych platformy Azure (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-patching.md)
> 
> 

Automatyczne stosowanie poprawek ustanawia oknem konserwacji na maszynie wirtualnej platformy Azure działa program SQL Server. Automatyczne aktualizacje można instalować tylko w tym oknie konserwacji. Dla programu SQL Server zapewnia to, że aktualizacje systemu i wszystkie skojarzone ponownych uruchomień wystąpić na najlepszy czas dla bazy danych. 

> [!IMPORTANT]
> Instalowane są tylko aktualizacje systemu Windows oznaczone jako **Ważne**. Pozostałe aktualizacje programu SQL Server, na przykład aktualizacje zbiorcze, należy instalować ręcznie. 

Automatyczne stosowanie poprawek zależy od [rozszerzenia agenta IaaS w programie SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję tego artykułu w zakresie usługi Resource Manager, zobacz [automatyczne stosowanie poprawek dla programu SQL Server w usłudze Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć, automatyczne stosowanie poprawek, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja programu SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Zainstaluj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview).

**Rozszerzenie programu SQL Server IaaS**:

* [Zainstaluj rozszerzenie IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego stosowania poprawek. Dla klasycznych maszyn wirtualnych należy skonfigurować te ustawienia za pomocą programu PowerShell.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącz/Wyłącz (wyłączony) |Włącza lub wyłącza automatyczne stosowanie poprawek dla maszyny wirtualnej platformy Azure. |
| **Harmonogram konserwacji** |Codziennie, poniedziałek, Wtorek, Środa, czwartek, Friday, Saturday lub Sunday |Harmonogram pobierania i instalowania aktualizacji Windows, SQL Server i Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Godzina rozpoczęcia lokalne, które można zaktualizować maszyny wirtualnej. |
| **Czas trwania okna obsługi** |30-180 |Liczba minut na ukończenie pobierania i instalacji aktualizacji. |
| **Kategoria poprawki** |Ważne |Kategoria aktualizacji do pobrania i zainstalowania. |

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
W poniższym przykładzie programu PowerShell służy do konfigurowania automatyczne stosowanie poprawek istniejącej maszyny wirtualnej programu SQL Server. **New AzureVMSqlServerAutoPatchingConfig** polecenie konfiguruje nowe okno konserwacji dla aktualizacji automatycznych.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Na podstawie tego przykładu, w poniższej tabeli opisano praktycznego wpływu na docelowej maszynie Wirtualnej platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **dayOfWeek** |Każdy czwartek zainstalowanych poprawek. |
| **MaintenanceWindowStartingHour** |Rozpocznij aktualizacje o 11:00. |
| **MaintenanceWindowDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. Na podstawie czasu uruchomienia, muszą wykonać przez 1:00 pm. |
| **PatchCategory** |Tylko to możliwe ustawienie tego parametru jest "Ważne". |

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta IaaS programu SQL Server.

Aby wyłączyć automatyczne stosowanie poprawek, uruchom takie same skrypt bez parametru - Włącz, aby AzureVMSqlServerAutoPatchingConfig nowy. Podobnie jak w przypadku instalacji, może potrwać kilka minut, aby wyłączyć automatyczne stosowanie poprawek.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o innych zadaniach automatyzacji dostępności, zobacz [rozszerzenie agenta IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

