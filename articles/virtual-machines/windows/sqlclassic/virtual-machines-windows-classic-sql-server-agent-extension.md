---
title: Automatyzacja zadań zarządzania na maszynach wirtualnych SQL (klasyczny) | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób zarządzania rozszerzeniem agenta programu SQL Server, które automatyzuje określone zadania administracyjne programu SQL Server. Należą do nich automatyczne tworzenie kopii zapasowych, automatyczne łatanie i integracja usługi Azure Key Vault. W tym temacie użyto klasycznego trybu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b76ade40db1e85abc0fb42af2e6f4ab88cb092c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982283"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatyzacja zadań zarządzania na maszynach wirtualnych platformy Azure za pomocą rozszerzenia agenta programu SQL Server (classic)
> [!div class="op_single_selector"]
> * [Menedżer zasobów](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../classic/sql-server-agent-extension.md)
> 
>
 
Rozszerzenie programu SQL Server IaaS Agent (SQLIaaSAgent) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten temat zawiera omówienie usług obsługiwanych przez rozszerzenie, a także instrukcje dotyczące instalacji, stanu i usuwania.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję Menedżera zasobów tego artykułu, zobacz [Rozszerzenie programu SQL Server Agent extension for SQL Server VMs Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta programu SQL Server IaaS obsługuje następujące zadania administracyjne:

| Funkcja administrowania | Opis |
| --- | --- |
| **Automatyczna usługa Backup SQL** |Automatyzuje planowanie kopii zapasowych dla wszystkich baz danych dla domyślnego wystąpienia programu SQL Server na maszynie Wirtualnej. Aby uzyskać więcej informacji, zobacz [Automatyczna kopia zapasowa dla programu SQL Server w usłudze Azure Virtual Machines (Classic)](../classic/sql-automated-backup.md). |
| **Automatyczne stosowanie poprawek SQL** |Konfiguruje okno konserwacji, w którym mogą mieć miejsce ważne aktualizacje systemu Windows na maszynie wirtualnej, dzięki czemu można uniknąć aktualizacji w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [Automatyczne instalowanie poprawek dla programu SQL Server w usłudze Azure Virtual Machines (Classic)](../classic/sql-automated-patching.md). |
| **Integracja z usługą Azure Key Vault** |Umożliwia automatyczne instalowanie i konfigurowanie usługi Azure Key Vault na maszynie wirtualnej programu SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych platformy Azure (Classic).](../classic/ps-sql-keyvault.md) |

## <a name="prerequisites"></a>Wymagania wstępne
Wymagania dotyczące używania rozszerzenia agenta IaaS programu SQL Server na maszynie wirtualnej:

### <a name="operating-system"></a>System operacyjny:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Wersje programu SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Pobierz i skonfiguruj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview).

Uruchom program Windows PowerShell i połącz go z subskrypcją platformy Azure za pomocą polecenia **Add-AzureAccount.**

    Add-AzureAccount

Jeśli masz wiele subskrypcji, użyj **Select-AzureSubscription,** aby wybrać subskrypcję, która zawiera docelową klasyczną maszynę wirtualną.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

W tym momencie można uzyskać listę klasycznych maszyn wirtualnych i ich nazwy skojarzonych usług z **Get-AzureVM** polecenia.

    Get-AzureVM

## <a name="installation"></a>Instalacja
W przypadku klasycznych maszyn wirtualnych należy użyć programu PowerShell, aby zainstalować rozszerzenie agenta programu SQL Server IaaS i skonfigurować skojarzone z nim usługi. Aby zainstalować rozszerzenie, użyj polecenia cmdlet Programu PowerShell **Set-AzureVMSqlServerExtension.** Na przykład następujące polecenie instaluje rozszerzenie na maszynie Wirtualnej systemu Windows Server (klasyczne) i nazywa je "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

W przypadku aktualizacji do najnowszej wersji rozszerzenia agenta SQL IaaS należy ponownie uruchomić maszynę wirtualną po zaktualizowaniu rozszerzenia.

> [!NOTE]
> Klasyczne maszyny wirtualne nie mają opcji instalowania i konfigurowania rozszerzenia agenta SQL IaaS za pośrednictwem portalu.

> [!NOTE]
> Rozszerzenie programu SQL Server IaaS agent jest obsługiwane tylko na [obrazach galerii maszyn wirtualnych programu SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (płatność zgodnie z rzeczywistym lub przyjmij własną licencję). Nie jest obsługiwany, jeśli ręcznie zainstalować program SQL Server na maszynie wirtualnej systemu windows tylko dla systemu operacyjnego lub wdrożyć dostosowane SQL Server VM VHD. W takich przypadkach może być możliwe zainstalowanie rozszerzenia i zarządzanie nim ręcznie przy użyciu programu PowerShell, ale zdecydowanie zaleca się zainstalowanie obrazu galerii maszyn wirtualnych programu SQL Server, a następnie jego dostosowanie.

## <a name="status"></a>Stan
Jednym ze sposobów sprawdzenia, czy rozszerzenie jest zainstalowane jest wyświetlanie stanu agenta w witrynie Azure Portal. Wybierz maszynę wirtualną wymienioną w bloku maszyny wirtualnej, a następnie kliknij **rozszerzenia**. Na liście powinno zostać wyświetlone rozszerzenie **SQLIaaSAgent.**

![Rozszerzenie agenta IaaS programu SQL Server w witrynie Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Można również użyć polecenia cmdlet **Get-AzureVMSqlServerExtension** Azure Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Usuwania
W witrynie Azure Portal można odinstalować rozszerzenie, klikając wielokropek na **rozszerzeń** bloku właściwości maszyny wirtualnej. Następnie kliknij przycisk **Odinstaluj**.

![Odinstalowywanie rozszerzenia agenta IaaS programu SQL Server w witrynie Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Można również użyć polecenia cmdlet **programu Remove-AzureVMSqlServerExtension** Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
Rozpocznij korzystanie z jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz tematy, do których odwołuje się w sekcji [Obsługiwane usługi](#supported-services) w tym artykule.

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

