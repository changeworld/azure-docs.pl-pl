---
title: Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia agenta SQL Server IaaS | Microsoft Docs
description: W tym artykule opisano sposób zarządzania rozszerzeniem SQL Server agenta IaaS, które automatyzuje określone SQL Server zadania administracyjne. Obejmują one automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integrację Azure Key Vault.
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
ms.openlocfilehash: 59b5138950e0fb94ea0051fa9cfe9aa75cd7d770
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877806"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozszerzenie agenta IaaS w programie SQL Server (SqlIaasExtension) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten artykuł zawiera omówienie usług obsługiwanych przez rozszerzenie. Ten artykuł zawiera również instrukcje dotyczące instalacji, stanu i usuwania rozszerzenia.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Aby wyświetlić klasyczną wersję tego artykułu, zobacz [SQL Server rozszerzenia agenta IaaS dla SQL Server maszyn wirtualnych (klasyczny)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Istnieją trzy tryby zarządzania dla rozszerzenia SQL Server IaaS: 

- Tryb **pełny** zapewnia wszystkie funkcje, ale wymaga ponownego uruchomienia uprawnień SQL Server i administratora systemu. Jest to opcja instalowana domyślnie. Służy do zarządzania maszyną wirtualną SQL Server przy użyciu jednego wystąpienia. 

- **Lekkie** nie wymaga ponownego uruchomienia SQL Server, ale obsługuje tylko Zmienianie typu licencji i wydania SQL Server. Użyj tej opcji dla SQL Server maszyn wirtualnych z wieloma wystąpieniami lub udziału w wystąpieniu klastra trybu failover (FCI). 

- **Noagent** jest przeznaczony dla SQL Server 2008 i SQL Server 2008 R2 zainstalowanych w systemie Windows Server 2008. Aby uzyskać informacje dotyczące korzystania z tego trybu dla obrazu systemu Windows Server 2008, zobacz [rejestracja w systemie Windows server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta SQL Server IaaS obsługuje następujące zadania administracyjne:

| Funkcja administrowania | Opis |
| --- | --- |
| **SQL Server automatyczne kopie zapasowe** |Automatyzuje Planowanie kopii zapasowych dla wszystkich baz danych dla wystąpienia domyślnego lub [poprawnie zainstalowane](virtual-machines-windows-sql-server-iaas-faq.md#administration) nazwane wystąpienie SQL Server na maszynie wirtualnej. Aby uzyskać więcej informacji, zobacz [zautomatyzowane tworzenie kopii zapasowych SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server zautomatyzowane stosowanie poprawek** |Konfiguruje okno obsługi, w którym mogą być wykonywane ważne aktualizacje systemu Windows dla maszyny wirtualnej, dzięki czemu można uniknąć aktualizacji w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](virtual-machines-windows-sql-automated-patching.md). |
| **Integracja Azure Key Vault** |Umożliwia automatyczne instalowanie i Konfigurowanie Azure Key Vault na maszynie wirtualnej SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji Azure Key Vault dla SQL Server na platformie Azure Virtual Machines (Menedżer zasobów)](virtual-machines-windows-ps-sql-keyvault.md). |

Po zainstalowaniu i uruchomieniu rozszerzenia SQL Server IaaS Agent udostępnia funkcje administracyjne:

* W panelu SQL Server maszyny wirtualnej w Azure Portal i za pomocą Azure PowerShell dla obrazów SQL Server w witrynie Azure Marketplace.
* Za pomocą Azure PowerShell ręcznej instalacji rozszerzenia. 

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania dotyczące używania SQL Server rozszerzenia agenta IaaS na maszynie wirtualnej:

**System operacyjny**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Wersja SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Pobieranie i Konfigurowanie najnowszych poleceń Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Zmień tryby zarządzania

Bieżący tryb SQL Server agenta IaaS można wyświetlić za pomocą programu PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server maszyny wirtualne, na których zainstalowano uproszczone rozszerzenie IaaS, mogą uaktualnić tryb do _pełnej_ wersji przy użyciu Azure Portal. SQL Server maszyny wirtualne w trybie _bez agenta_ można uaktualnić do wersji _pełnej_ po uaktualnieniu systemu operacyjnego do wersji Windows 2008 R2 lub nowszej. Nie jest możliwe przeprowadzenie obniżenia — w tym celu należy całkowicie odinstalować rozszerzenie SQL IaaS i zainstalować je ponownie. 

Aby uaktualnić tryb agenta do pełnej: 


# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do zasobu [maszyny wirtualnej SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Wybierz swoją SQL Server maszynę wirtualną, a następnie wybierz pozycję **Przegląd**. 
1. W przypadku SQL Server maszyn wirtualnych z trybem noagent lub Lightweight IaaS wybierz opcję **jedyne typy licencji i aktualizacje wersji są dostępne w komunikacie rozszerzenia SQL IaaS** .

   ![Wybory dotyczące zmiany trybu z portalu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Zaznacz pole wyboru **Zgadzam się na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej** , a następnie wybierz pozycję **Potwierdź** , aby uaktualnić tryb IaaS do pełnego. 

    ![Pole wyboru, aby wyrazić zgodę na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Uruchom następujący fragment kodu polecenia AZ CLI:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Uruchom następujący fragment kodu programu PowerShell:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Instalacja
SQL Server rozszerzenie IaaS jest instalowane po zarejestrowaniu maszyny wirtualnej SQL Server z [dostawcą zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md). W razie potrzeby można zainstalować agenta SQL Server IaaS ręcznie przy użyciu trybu pełnego lub uproszczonego. 

SQL Server rozszerzenie agenta IaaS w trybie pełnym jest instalowane automatycznie podczas aprowizacji jednego z SQL Server obrazów portalu Azure Marketplace z maszynami wirtualnymi przy użyciu Azure Portal. 

### <a name="install-in-full-mode"></a>Zainstaluj w trybie pełnym
Tryb pełny dla rozszerzenia SQL Server IaaS umożliwia pełne zarządzanie jednym wystąpieniem na SQL Server maszynie wirtualnej. Jeśli istnieje wystąpienie domyślne, rozszerzenie będzie współdziałać z wystąpieniem domyślnym i nie będzie obsługiwać zarządzania innymi wystąpieniami. Jeśli nie ma wystąpienia domyślnego, ale tylko jednego nazwane wystąpienie, będzie zarządzać nazwanym wystąpieniem. Jeśli nie ma wystąpienia domyślnego i istnieje wiele wystąpień nazwanych, rozszerzenie nie zostanie zainstalowane. 

Zainstaluj SQL Server agenta IaaS z trybem pełnym przy użyciu programu PowerShell:

  ```powershell-interactive
     #Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with 'Full' SQL Server IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parametr | Akceptowalne wartości                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` lub `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Jeśli rozszerzenie nie jest jeszcze zainstalowane, instalacja pełnego rozszerzenia powoduje ponowne uruchomienie usługi SQL Server. Aby uniknąć ponownego uruchomienia usługi SQL Server, należy zainstalować tryb uproszczony z ograniczoną możliwością zarządzania.
> 
> Aktualizacja rozszerzenia IaaS SQL Server nie powoduje ponownego uruchomienia usługi SQL Server. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalowanie na maszynie wirtualnej z jednym wystąpieniem o nazwie SQL Server
SQL Server rozszerzenie IaaS będzie współdziałać z nazwanym wystąpieniem na SQL Server, jeśli wystąpienie domyślne zostanie odinstalowane i zostanie zainstalowane ponownie rozszerzenie IaaS.

Aby użyć nazwanego wystąpienia SQL Server:
   1. Wdróż maszynę wirtualną SQL Server z poziomu portalu Azure Marketplace. 
   1. Odinstaluj rozszerzenie IaaS z [Azure Portal](https://portal.azure.com).
   1. Odinstaluj SQL Server całkowicie w SQL Server maszynie wirtualnej.
   1. Zainstaluj SQL Server z nazwanym wystąpieniem w ramach maszyny wirtualnej SQL Server. 
   1. Zainstaluj rozszerzenie IaaS z Azure Portal.  


### <a name="install-in-lightweight-mode"></a>Zainstaluj w trybie uproszczonym
Tryb uproszczony nie spowoduje ponownego uruchomienia usługi SQL Server, ale oferuje ograniczoną funkcjonalność. 

Zainstaluj SQL Server agenta IaaS z trybem uproszczonym przy użyciu programu PowerShell:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parametr | Akceptowalne wartości                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` lub `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Pobierz stan rozszerzenia IaaS SQL Server
Jednym ze sposobów sprawdzenia, czy rozszerzenie jest zainstalowane, jest wyświetlenie stanu agenta w Azure Portal. Wybierz pozycję **wszystkie ustawienia** w oknie maszyna wirtualna, a następnie wybierz pozycję **rozszerzenia**. Powinna zostać wyświetlona lista rozszerzeń **SqlIaasExtension** .

![Stan rozszerzenia agenta SQL Server IaaS w Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Można również użyć polecenia cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Poprzednie polecenie potwierdza, że Agent jest zainstalowany i zawiera ogólne informacje o stanie. Informacje o stanie automatycznej kopii zapasowej i poprawek można uzyskać, korzystając z następujących poleceń:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Usunięcie
W Azure Portal można odinstalować rozszerzenie, wybierając wielokropek w oknie **rozszerzenia** we właściwościach maszyny wirtualnej. Następnie wybierz pozycję **Usuń**.

![Odinstalowywanie rozszerzenia SQL Server Agent IaaS w Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Można również użyć polecenia cmdlet **Remove-AzVMSqlServerExtension** programu PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Następne kroki
Zacznij korzystać z jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz artykuły, do których odwołuje się sekcja [obsługiwane usługi](#supported-services) w tym artykule.

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na platformie Azure Virtual Machines, zobacz temat [co to jest SQL Server na platformie azure Virtual Machines?](virtual-machines-windows-sql-server-iaas-overview.md).

