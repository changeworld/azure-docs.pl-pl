---
title: Automatyzacji zadań zarządzania na maszynach wirtualnych SQL (klasyczne) | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób zarządzania rozszerzenie agenta programu SQL Server, który automatyzuje określonych zadań administracyjnych programu SQL Server. Obejmują one automatyczna usługa Backup, automatyczne stosowanie poprawek i integracji magazynu kluczy Azure. W tym temacie używa trybu klasycznego wdrożenia.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ff9a8b91b0359c57fae5b1a01b5d895ab9a1685
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29851762"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatyzacji zadań zarządzania na maszynach wirtualnych Azure z rozszerzeniem agenta serwera SQL (klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../classic/sql-server-agent-extension.md)
> 
>
 
Rozszerzenie agenta SQL Server IaaS (SQLIaaSAgent) działa na maszynach wirtualnych Azure w celu zautomatyzowania zadań administracyjnych. Ten temat zawiera omówienie usług obsługiwanych przez rozszerzenie, jak również instrukcje dotyczące instalacji, stanu i usuwania.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję usługi Resource Manager w tym artykule, zobacz [rozszerzenie agenta serwera SQL dla programu SQL Server maszyn wirtualnych Menedżera zasobów](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta programu SQL Server IaaS obsługuje następujące zadania:

| Funkcja administracji | Opis |
| --- | --- |
| **Automatyczna usługa Backup SQL** |Automatyzuje Planowanie kopii zapasowych dla wszystkich baz danych dla domyślnego wystąpienia programu SQL Server w maszynie Wirtualnej. Aby uzyskać więcej informacji, zobacz [automatycznego tworzenia kopii zapasowej dla programu SQL Server w maszynach wirtualnych platformy Azure (klasyczne)](../classic/sql-automated-backup.md). |
| **Automatyczne stosowanie poprawek SQL** |Konfiguruje okno obsługi, w którym ważne aktualizacje systemu Windows do maszyny Wirtualnej może mieć miejsce, aby uniknąć aktualizacje w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [automatyczne stosowanie poprawek dla programu SQL Server w maszynach wirtualnych platformy Azure (klasyczne)](../classic/sql-automated-patching.md). |
| **Integracja z usługą Azure Key Vault** |Umożliwia automatyczne instalowanie i konfigurowanie usługi Azure Key Vault na maszyną Wirtualną programu SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji magazynu kluczy Azure dla programu SQL Server na maszynach wirtualnych Azure (klasyczne)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Wymagania wstępne
Wymagania dotyczące używania rozszerzenie agenta IaaS serwera SQL na maszynie Wirtualnej:

### <a name="operating-system"></a>System operacyjny:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Wersje programu SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Pobierz i skonfiguruj najnowszych poleceń programu PowerShell Azure](/powershell/azure/overview).

Uruchom program Windows PowerShell i połącz go z subskrypcją platformy Azure z **Add-AzureAccount** polecenia.

    Add-AzureAccount

Jeśli masz wiele subskrypcji, użyj **AzureSubscription wybierz** Wybierz subskrypcję, która zawiera urządzenie docelowe klasyczne maszyny Wirtualnej.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

W tym momencie można uzyskać listy klasycznych maszyn wirtualnych i ich nazwy usługi skojarzone z **Get-AzureVM** polecenia.

    Get-AzureVM

## <a name="installation"></a>Instalacja
Dla klasycznych maszyn wirtualnych należy użyć programu PowerShell, aby zainstalować rozszerzenie agenta programu SQL Server IaaS i skonfigurować skojarzonych z nimi usług. Użyj **AzureVMSqlServerExtension zestaw** polecenia cmdlet programu PowerShell, aby zainstalować to rozszerzenie. Na przykład następujące polecenie instaluje rozszerzenie na maszynie Wirtualnej serwera systemu Windows (klasyczne) i nazwy jej "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Po aktualizacji do najnowszej wersji rozszerzenia agenta IaaS SQL, należy ponownie uruchomić maszynę wirtualną po zaktualizowaniu rozszerzenia.

> [!NOTE]
> Klasycznych maszyn wirtualnych nie ma możliwości, aby zainstalować i skonfigurować rozszerzenia SQL IaaS agenta za pośrednictwem portalu.
> 
> 

## <a name="status"></a>Stan
Jest jednym ze sposobów Sprawdź, czy rozszerzenie jest zainstalowane, aby wyświetlić stan agenta w portalu Azure. Wybierz maszynę wirtualną na liście w bloku maszyny wirtualnej, a następnie kliknij polecenie **rozszerzenia**. Powinny pojawić się **SQLIaaSAgent** rozszerzenie na liście.

![Rozszerzenie IaaS agenta serwera SQL w portalu Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Można również użyć **Get-AzureVMSqlServerExtension** polecenia cmdlet programu Azure Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Usunięcie
W portalu Azure, można odinstalować rozszerzenia, klikając wielokropek **rozszerzenia** bloku właściwości maszyny wirtualnej. Następnie kliknij przycisk **Odinstaluj**.

![Odinstaluj rozszerzenie programu SQL Server Agent środowiska IaaS w portalu Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Można również użyć **AzureVMSqlServerExtension Usuń** polecenia cmdlet programu Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
Rozpocznij przy użyciu jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz tematy, do którego odwołuje się [obsługiwane usługi](#supported-services) sekcji tego artykułu.

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

