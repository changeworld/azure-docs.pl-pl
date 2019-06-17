---
title: Automatyzowanie zadań zarządzania na maszynach wirtualnych programu SQL (klasyczny) | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób zarządzania rozszerzenie agenta programu SQL Server, który automatyzuje określonych zadań administracyjnych programu SQL Server. Obejmują one automatyczne kopie zapasowe, automatyczne stosowanie poprawek i integracji magazynu kluczy Azure. Ten temat korzysta z trybu klasycznego wdrożenia.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2b719185aabd39cd70b9cb890a9599aa06ca4ff4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334849"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure za pomocą rozszerzenia agenta programu SQL Server (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../classic/sql-server-agent-extension.md)
> 
>
 
Rozszerzenie agenta IaaS programu SQL Server (SQLIaaSAgent) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten temat zawiera omówienie usług obsługiwanych przez rozszerzenie, a także instrukcje dotyczące instalacji, stanu i usuwania.

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję tego artykułu w zakresie usługi Resource Manager, zobacz [rozszerzenie agenta programu SQL Server dla programu SQL Server maszyn wirtualnych Menedżera zasobów](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta IaaS programu SQL Server obsługuje następujące zadania:

| Funkcja administrowania | Opis |
| --- | --- |
| **Automatyczna usługa Backup SQL** |Automatyzuje proces planowania kopii zapasowych dla wszystkich baz danych dla domyślnego wystąpienia programu SQL Server na maszynie wirtualnej. Aby uzyskać więcej informacji, zobacz [automatyczne tworzenie kopii zapasowych dla programu SQL Server na maszynach wirtualnych platformy Azure (wersja klasyczna)](../classic/sql-automated-backup.md). |
| **Automatyczne stosowanie poprawek SQL** |Konfiguruje okno obsługi, podczas którego ważne aktualizacje Windows do maszyny Wirtualnej może mieć miejsce, dzięki czemu można uniknąć aktualizacje w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [automatyczne stosowanie poprawek dla programu SQL Server na maszynach wirtualnych platformy Azure (wersja klasyczna)](../classic/sql-automated-patching.md). |
| **Integracja z usługą Azure Key Vault** |Pozwala na automatyczne instalowanie i konfigurowanie usługi Azure Key Vault na maszynie Wirtualnej programu SQL Server. Aby uzyskać więcej informacji, zobacz [konfigurowania integracji magazynu kluczy Azure dla programu SQL Server na maszynach wirtualnych Azure (wersja klasyczna)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Wymagania wstępne
Wymagania dotyczące korzystania z rozszerzenia agenta IaaS programu SQL Server na maszynie Wirtualnej:

### <a name="operating-system"></a>System operacyjny:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Wersje programu SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Pobierz i skonfiguruj najnowszych poleceń programu Azure PowerShell](/powershell/azure/overview).

Uruchom program Windows PowerShell i połącz go z subskrypcji platformy Azure za pomocą **Add-AzureAccount** polecenia.

    Add-AzureAccount

Jeśli masz wiele subskrypcji, użyj **Select-AzureSubscription** Wybierz subskrypcję, która zawiera urządzenie docelowe klasycznej maszyny Wirtualnej.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

W tym momencie można uzyskać listy klasycznych maszyn wirtualnych i ich nazw skojarzona usługa z **Get-AzureVM** polecenia.

    Get-AzureVM

## <a name="installation"></a>Instalacja
Dla klasycznych maszyn wirtualnych należy użyć programu PowerShell, aby zainstalować rozszerzenie agenta IaaS programu SQL Server i skonfigurować skojarzonych z nimi usług. Użyj **AzureVMSqlServerExtension zestaw** polecenia cmdlet programu PowerShell, aby zainstalować rozszerzenie. Na przykład poniższe polecenie instaluje rozszerzenie na maszynie Wirtualnej serwera systemu Windows (wersja klasyczna) i nadaje mu "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Po aktualizacji do najnowszej wersji rozszerzenia agenta IaaS programu SQL, należy ponownie uruchomić maszynę wirtualną po zaktualizowaniu rozszerzenia.

> [!NOTE]
> Klasycznych maszyn wirtualnych nie ma możliwości, aby zainstalować i skonfigurować rozszerzenia agenta SQL IaaS, za pośrednictwem portalu.

> [!NOTE]
> Rozszerzenie agenta IaaS programu SQL Server jest obsługiwana tylko na [obrazy z galerii maszyn wirtualnych programu SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (płatność za rzeczywiste użycie lub bring-your-own-license). Nie jest obsługiwana, jeśli ręcznie zainstalować program SQL Server na maszynie wirtualnej tylko do systemu operacyjnego Windows Server lub wdrożyć dostosowane VHD maszyny Wirtualnej programu SQL Server. W takich przypadkach może być możliwe do zainstalowania i zarządzać rozszerzenie ręcznie przy użyciu programu PowerShell, ale zdecydowanie zalecane jest zamiast tego zainstalować obrazu z galerii maszyn wirtualnych programu SQL Server, a następnie ją Dostosuj.

## <a name="status"></a>Stan
Jest jednym ze sposobów, aby sprawdzić, czy rozszerzenie jest zainstalowane. Aby wyświetlić stan agenta w witrynie Azure Portal. Wybierz maszynę wirtualną na liście w bloku maszyny wirtualnej, a następnie kliknij polecenie **rozszerzenia**. Powinien zostać wyświetlony **SQLIaaSAgent** rozszerzenie wymienione.

![Rozszerzenie agenta IaaS programu SQL Server w witrynie Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Można również użyć **Get AzureVMSqlServerExtension** polecenia cmdlet programu Azure Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Usuwanie
W witrynie Azure Portal, można odinstalować rozszerzenie, klikając przycisk wielokropka **rozszerzenia** blok właściwości maszyny wirtualnej. Następnie kliknij przycisk **Odinstaluj**.

![Odinstaluj rozszerzenie agenta IaaS programu SQL Server w witrynie Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Można również użyć **AzureVMSqlServerExtension Usuń** polecenia cmdlet programu Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
Rozpocząć korzystanie z jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz tematy, do którego odwołuje się [obsługiwanych usługach](#supported-services) dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

