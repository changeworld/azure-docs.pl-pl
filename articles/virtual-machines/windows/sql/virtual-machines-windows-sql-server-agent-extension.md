---
title: Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure za pomocą rozszerzenia agenta IaaS programu SQL Server | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania rozszerzenie agenta programu SQL Server, który automatyzuje określonych zadań administracyjnych programu SQL Server. Obejmują one automatyczne kopie zapasowe, automatyczne stosowanie poprawek i integracji magazynu kluczy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798054"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure za pomocą rozszerzenia agenta IaaS programu SQL Server
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozszerzenie agenta IaaS w programie SQL Server (SqlIaasExtension) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten artykuł zawiera omówienie i usługi obsługiwane przez rozszerzenie, a także instrukcje dotyczące instalacji, stanu i usuwania.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Aby wyświetlić klasycznej wersji w tym artykule, zobacz [rozszerzenie agenta programu SQL Server dla programu SQL Server VMs Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Istnieją trzy tryby rozszerzenie SQL IaaS możliwości zarządzania SQL: **Pełne**, **Lightweight**, i **NoAgent**. 

- **Pełna** tryb zapewnia wszystkie funkcje, ale wymaga ponownego uruchomienia programu SQL Server i SA uprawnień. Jest to opcja, która jest domyślnie instalowany i powinna być używana do zarządzania maszynę Wirtualną programu SQL Server przy użyciu jednego wystąpienia. 

- **Uproszczone** nie jest wymagane ponowne uruchomienie programu SQL Server, ale obsługuje tylko Zmienianie typu licencji i wersji programu SQL Server. Ta opcja powinna być używane dla maszyn wirtualnych programu SQL Server z wieloma wystąpieniami lub uczestniczących w wystąpieniu klastra trybu failover (FCI). 

- **NoAgent** jest dedykowany do programu SQL Server 2008 i SQL Server 2008 R2 zainstalowanym systemem Windows Server 2008. Aby uzyskać informacje o użyciu `NoAgent` tryb dla obrazu systemu Windows Server 2008, zobacz [rejestracji systemu Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

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
* Windows Server 2019 

**Wersje programu SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Pobierz i skonfiguruj najnowszych poleceń programu Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Zmienić tryb zarządzania

Bieżący tryb agenta SQL IaaS można wyświetlić przy użyciu programu PowerShell: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Dla maszyn wirtualnych serwera SQL, które mają *NoAgent* lub *uproszczone* po zainstalowaniu rozszerzenia IaaS można uaktualnić tryb *pełne* przy użyciu witryny Azure portal. Nie jest możliwe w przypadku obniżania — w tym celu należy całkowicie odinstalować rozszerzenie SQL IaaS i zainstaluj go ponownie. 

Tryb uaktualniania agenta do *pełne*, wykonaj następujące czynności: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Przejdź do swojej [maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) zasobów. 
1. Wybierz maszyny wirtualnej programu SQL Server, a następnie wybierz pozycję **Przegląd**. 
1. Dla maszyn wirtualnych SQL z *NoAgent* lub *uproszczone* tryby IaaS, wybierz w komunikacie o **tylko licencji typu i wersji aktualizacji są dostępne za pomocą rozszerzenie SQL IaaS**.

    ![Uruchom zmiana trybu z portalu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Zgodę na **ponownie uruchomić usługę programu SQL Server** zaznaczyć pole wyboru, a następnie wybierz **Potwierdź** uaktualnić swoje tryb IaaS na wartość "full". 

    ![Włączanie pełnego zarządzania dla rozszerzenia IaaS](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Instalacja
Rozszerzenie SQL IaaS jest zainstalowany podczas rejestracji, maszyna wirtualna serwera SQL z [dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Jednakże, jeśli to konieczne, agenta SQL IaaS można również zainstalować ręcznie przy użyciu *pełne* lub *uproszczone* instalacji w trybie. 

*Pełne* rozszerzenie agenta IaaS programu SQL Server jest instalowany automatycznie podczas przeprowadzania aprowizacji obrazy galerii maszyn wirtualnych programu SQL Server przy użyciu witryny Azure portal. 

### <a name="full-mode-installation"></a>Instalacja w trybie pełnego
*Pełne* rozszerzenie SQL IaaS zapewnia pełne możliwości zarządzania dla jednego wystąpienia na maszynie Wirtualnej programu SQL Server. W przypadku wystąpienia domyślnego, następnie rozszerzenie będzie działać z domyślnego wystąpienia, a nie będzie obsługiwał, Zarządzanie innymi wystąpieniami. Jeśli istnieje, nie wystąpienia domyślnego, ale tylko jedno nazwane wystąpienie, będzie on zarządzać nazwanego wystąpienia. Jeśli istnieje żadne wystąpienie domyślne, a wiele wystąpień nazwanych, rozszerzenie zakończy się niepowodzeniem do zainstalowania. 

Instalowanie *pełne* tryb SQL IaaS spowoduje ponowne uruchomienie usługi programu SQL Server. Aby uniknąć ponownego uruchamiania usługi programu SQL Server, zainstaluj *uproszczone* tryb zamiast ograniczone możliwości zarządzania. 

Instalacja agenta SQL IaaS z *pełne* tryb przy użyciu programu PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parametr | Dopuszczalne wartości                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, lub `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Jeśli rozszerzenie nie jest już zainstalowany, instalowanie **pełne** rozszerzenia powoduje ponowne uruchomienie usługi programu SQL Server. Użyj **uproszczone** trybu, aby uniknąć ponownego uruchomienia usługi SQL Server. 
> - Aktualizowanie rozszerzenie SQL IaaS nie uruchamia ponownie usługę programu SQL Server. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Zainstaluj na maszynie Wirtualnej przy użyciu jednego nazwanego wystąpienia programu SQL Server
Rozszerzenie SQL IaaS będzie działać z nazwanego wystąpienia na serwerze SQL Server, jeśli wystąpienie domyślne zostaje odinstalowane i ponownym zainstalowaniu rozszerzenia IaaS.

Aby użyć nazwanego wystąpienia programu SQL Server, wykonaj następujące czynności:
   1. Wdróż maszynę Wirtualną programu SQL Server z witryny marketplace. 
   1. Odinstaluj rozszerzenie IaaS z poziomu [witryny Azure portal](https://portal.azure.com).
   1. Odinstalowanie programu SQL Server w maszynę Wirtualną programu SQL Server.
   1. Instalowanie programu SQL Server przy użyciu nazwanego wystąpienia, w ramach maszyny Wirtualnej programu SQL Server. 
   1. Zainstaluj rozszerzenie IaaS z w witrynie Azure portal.  


### <a name="install-in-lightweight-mode"></a>Instalowanie w trybie uproszczonym
Usługi programu SQL Server nie uruchamia ponownie w trybie uproszczonym, ale oferuje ona ograniczone funkcje. 

Instalacja agenta SQL IaaS z *uproszczone* tryb przy użyciu programu PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parametr | Dopuszczalne wartości                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, lub `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Pobierz stan rozszerzenie programu SQL IaaS
Jest jednym ze sposobów, aby sprawdzić, czy rozszerzenie jest zainstalowane. Aby wyświetlić stan agenta w witrynie Azure portal. Wybierz **wszystkie ustawienia** w oknie maszyny wirtualnej, a następnie kliknąć **rozszerzenia**. Powinien zostać wyświetlony **SqlIaasExtension** rozszerzenie wymienione.

![Rozszerzenie agenta IaaS programu SQL Server w witrynie Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Można również użyć **Get AzVMSqlServerExtension** polecenia cmdlet programu Azure PowerShell.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Poprzednie polecenie potwierdza agent jest zainstalowany i zawiera informacje o stanie ogólne. Możesz również uzyskać informacje o stanie określonej o automatyczne kopie zapasowe i stosowanie poprawek za pomocą następujących poleceń.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Usuwanie
W witrynie Azure portal, można odinstalować rozszerzenie, klikając przycisk wielokropka **rozszerzenia** okna właściwości maszyny wirtualnej. Następnie kliknij pozycję **Usuń**.

![Odinstaluj rozszerzenie agenta IaaS SQL Server w witrynie Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Można również użyć **AzVMSqlServerExtension Usuń** polecenia cmdlet programu PowerShell.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Następne kroki
Rozpocząć korzystanie z jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz artykuły, do którego odwołuje się [obsługiwanych usługach](#supported-services) dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

