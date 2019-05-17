---
title: Automatyzowanie zadań zarządzania na maszynach wirtualnych programu SQL (Resource Manager) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania rozszerzenie agenta programu SQL Server, który automatyzuje określonych zadań administracyjnych programu SQL Server. Obejmują one automatyczne kopie zapasowe, automatyczne stosowanie poprawek i integracji magazynu kluczy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2943a1501e1d81ff4884c21b5aa3861e16523bbb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827737"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure za pomocą rozszerzenia agenta programu SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozszerzenie agenta IaaS w programie SQL Server (SqlIaasExtension) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten artykuł zawiera omówienie usług obsługiwanych przez rozszerzenie, a także instrukcje dotyczące instalacji, stanu i usuwania.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Aby wyświetlić klasycznej wersji w tym artykule, zobacz [rozszerzenie agenta programu SQL Server dla programu SQL Server VMs Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta IaaS programu SQL Server obsługuje następujące zadania:

| Funkcja administrowania | Opis |
| --- | --- |
| **Automatyczna usługa Backup SQL** |Automatyzuje Planowanie tworzenia kopii zapasowych dla wszystkich baz danych dla dowolnego wystąpienia domyślnego lub [prawidłowo zainstalowane](virtual-machines-windows-sql-server-iaas-faq.md#administration) nazwane wystąpienie programu SQL Server na maszynie Wirtualnej. Aby uzyskać więcej informacji, zobacz [automatyczne tworzenie kopii zapasowych dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatyczne stosowanie poprawek SQL** |Konfiguruje okno obsługi, podczas którego ważne aktualizacje Windows do maszyny Wirtualnej może mieć miejsce, dzięki czemu można uniknąć aktualizacje w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [automatyczne stosowanie poprawek dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integracja z usługą Azure Key Vault** |Pozwala na automatyczne instalowanie i konfigurowanie usługi Azure Key Vault na maszynie Wirtualnej programu SQL Server. Aby uzyskać więcej informacji, zobacz [konfigurowania integracji magazynu kluczy Azure dla programu SQL Server na maszynach wirtualnych Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Gdy zainstalowana i uruchomiona, rozszerzenie agenta IaaS programu SQL Server udostępnia te funkcje administracyjne na panelu programu SQL Server maszyny wirtualnej w witrynie Azure portal i za pomocą programu Azure PowerShell dla obrazów programu SQL Server z portalu marketplace oraz za pośrednictwem platformy Azure Program PowerShell w przypadku ręcznej instalacji rozszerzenia. 

## <a name="prerequisites"></a>Wymagania wstępne
Wymagania dotyczące korzystania z rozszerzenia agenta IaaS programu SQL Server na maszynie Wirtualnej:

**System operacyjny**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersje programu SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Pobierz i skonfiguruj najnowszych poleceń programu Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> W tej chwili [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md) nie jest obsługiwana dla infrastruktury klasyfikacji plików programu SQL Server na platformie Azure. Firma Microsoft zaleca, odinstaluj rozszerzenie z maszyny wirtualne, które uczestniczą w infrastruktury klasyfikacji plików. Funkcje obsługiwane przez rozszerzenie nie są dostępne dla maszyn wirtualnych SQL, po odinstalowaniu agenta.

## <a name="installation"></a>Instalacja
Rozszerzenie agenta IaaS programu SQL Server jest automatycznie instalowany podczas przeprowadzania aprowizacji obrazów programu SQL Server w galerii maszyn wirtualnych. Rozszerzenie SQL IaaS oferuje możliwości zarządzania dla jednego wystąpienia na maszynie Wirtualnej programu SQL Server. W przypadku wystąpienia domyślnego, następnie rozszerzenie będzie działać z domyślnego wystąpienia, a nie będzie obsługiwał, Zarządzanie innymi wystąpieniami. Jeśli istnieje, nie wystąpienia domyślnego, ale tylko jedno nazwane wystąpienie, będzie on zarządzać nazwanego wystąpienia. Jeśli istnieje żadne wystąpienie domyślne, a wiele wystąpień nazwanych, rozszerzenie zakończy się niepowodzeniem do zainstalowania. 



Jeśli musisz ręcznie zainstaluj rozszerzenie na jednym z tych maszyn wirtualnych serwera SQL, należy użyć następującego polecenia programu PowerShell:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Jeśli rozszerzenie nie jest już zainstalowany, instalowania rozszerzenia powoduje ponowne uruchomienie usługi programu SQL Server. Jednak aktualizowanie rozszerzenie SQL IaaS nie ponowne uruchomienie usługi programu SQL Server. 

> [!NOTE]
> Choć jest możliwe zainstalować rozszerzenie agenta IaaS programu SQL Server do niestandardowych obrazów programu SQL Server, funkcji jest obecnie ograniczona do [zmianę typu licencji](virtual-machines-windows-sql-ahb.md). Inne funkcje udostępniane przez rozszerzenie SQL IaaS będą działać tylko na [obrazy z galerii maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (płatność za rzeczywiste użycie lub bring-your-own-license).

### <a name="use-a-single-named-instance"></a>Używanie pojedynczej nazwane wystąpienie
Rozszerzenie SQL IaaS będą działać przy użyciu nazwanego wystąpienia na obrazu programu SQL Server domyślnego wystąpienia zostanie poprawnie odinstalowany, a rozszerzenie IaaS jest ponowna instalacja.

Aby użyć nazwanego wystąpienia programu SQL Server, wykonaj następujące czynności:
   1. Wdróż maszynę Wirtualną programu SQL Server z witryny marketplace. 
   1. Odinstaluj rozszerzenie IaaS z poziomu [witryny Azure portal](https://portal.azure.com).
   1. Odinstalowanie programu SQL Server w maszynę Wirtualną programu SQL Server.
   1. Instalowanie programu SQL Server przy użyciu nazwanego wystąpienia, w ramach maszyny Wirtualnej programu SQL Server. 
   1. Zainstaluj rozszerzenie IaaS z w witrynie Azure portal.  

## <a name="status"></a>Stan
Jest jednym ze sposobów, aby sprawdzić, czy rozszerzenie jest zainstalowane. Aby wyświetlić stan agenta w witrynie Azure portal. Wybierz **wszystkie ustawienia** w oknie maszyny wirtualnej, a następnie kliknąć **rozszerzenia**. Powinien zostać wyświetlony **SqlIaasExtension** rozszerzenie wymienione.

![Rozszerzenie agenta IaaS programu SQL Server w witrynie Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Można również użyć **Get AzVMSqlServerExtension** polecenia cmdlet programu Azure PowerShell.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Poprzednie polecenie potwierdza agent jest zainstalowany i zawiera informacje o stanie ogólne. Możesz również uzyskać informacje o stanie określonej o automatyczne kopie zapasowe i stosowanie poprawek za pomocą następujących poleceń.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Usunięcie
W witrynie Azure portal, można odinstalować rozszerzenie, klikając przycisk wielokropka **rozszerzenia** okna właściwości maszyny wirtualnej. Następnie kliknij pozycję **Usuń**.

![Odinstaluj rozszerzenie agenta IaaS SQL Server w witrynie Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Można również użyć **AzVMSqlServerExtension Usuń** polecenia cmdlet programu PowerShell.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Kolejne kroki
Rozpocząć korzystanie z jednej z usług obsługiwanych przez rozszerzenie. Więcej informacji na ten temat można znaleźć w artykułach, do którego odwołuje się [obsługiwanych usługach](#supported-services) dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

