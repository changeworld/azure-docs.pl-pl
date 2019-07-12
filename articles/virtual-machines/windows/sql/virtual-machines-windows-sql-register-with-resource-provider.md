---
title: Zarejestruj maszynę wirtualną programu SQL Server na platformie Azure za pomocą dostawcy zasobów maszyny Wirtualnej SQL | Dokumentacja firmy Microsoft
description: Zarejestruj maszynę Wirtualną usługi SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL, aby zwiększyć możliwości zarządzania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7d7d04299c2d6c7a498701df9fb39c8990484a79
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807252"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Zarejestruj maszynę wirtualną programu SQL Server na platformie Azure za pomocą dostawcy zasobów maszyny Wirtualnej SQL

W tym artykule opisano, jak można zarejestrować maszyny wirtualnej (VM) serwera SQL Azure za pomocą dostawcy zasobów maszyny Wirtualnej SQL. 

Wdrażanie obrazu portalu marketplace maszyn wirtualnych programu SQL Server w witrynie Azure portal automatycznie rejestruje maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów. Jednak jeśli zdecydujesz się samodzielnie zainstalować program SQL Server na maszynie wirtualnej platformy Azure zamiast obrazu z witryny Azure Marketplace, następnie należy zarejestrować maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów obecnie dla:

-  **Zgodność** — zgodnie z postanowieniami firmy Microsoft, klienci korzystający z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) musi wskazywać do firmy Microsoft, korzystając z korzyści użycia hybrydowego platformy Azure —, aby to zrobić, muszą zarejestrować za pomocą dostawcy zasobów maszyny Wirtualnej SQL. 

-  **Funkcja korzyści** — rejestrowanie maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów odblokowuje [automatyczne stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md), [automatyczne kopie zapasowe](virtual-machines-windows-sql-automated-backup-v2.md), możliwości monitorowania i zarządzania, a także [licencjonowania](virtual-machines-windows-sql-ahb.md) i [wersji](virtual-machines-windows-sql-change-edition.md) elastyczność. Wcześniej były one tylko dostępne do obrazów maszyn wirtualnych programu SQL Server w witrynie Azure Marketplace.

Samodzielnie Instalowanie programu SQL Server na Maszynie wirtualnej platformy Azure lub inicjowania obsługi administracyjnej maszyny Wirtualnej platformy Azure z niestandardowego dysku VHD z programem SQL Server jest zgodne za pośrednictwem korzyści użycia hybrydowego platformy Azure dla programu SQL Server z warunkiem, że klienci wskazywać używające do firmy Microsoft przez zarejestrowanie kodowań z zasobu maszyny Wirtualnej SQL Dostawca. 

Aby korzystać z dostawcy zasobów maszyny Wirtualnej SQL, należy również zarejestrować dostawcę zasobów maszyny Wirtualnej SQL, z Twoją subskrypcją. Można to zrobić za pomocą witryny Azure portal, interfejsu wiersza polecenia platformy Azure i programu PowerShell. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować się do maszyny Wirtualnej z programu SQL Server za pomocą dostawcy zasobów, potrzebne następujące elementy: 

- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- A [programu SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli) i [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Rejestrowanie dostawcy zasobów maszyny Wirtualnej SQL
Jeśli [SQL IaaS rozszerzenia](virtual-machines-windows-sql-server-agent-extension.md) jest już zainstalowana na maszynie Wirtualnej, a następnie rejestrowanie dostawcy zasobów maszyny Wirtualnej SQL jest po prostu utworzenie typu Microsoft.SqlVirtualMachine/SqlVirtualMachines zasób metadanych. Poniżej przedstawiono fragment kodu, aby zarejestrować za pomocą dostawcy zasobów maszyny Wirtualnej SQL, jeśli rozszerzenie SQL IaaS jest już zainstalowany na maszynie Wirtualnej. Należy podać typ licencji programu SQL Server żądanego podczas rejestrowania za pomocą dostawcy zasobów maszyny Wirtualnej SQL jako "PAYG" lub "AHUB". 

Zarejestruj maszynę Wirtualną SQL Server z poniższego fragmentu kodu przy użyciu programu PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Jeśli rozszerzenie SQL IaaS nie jest zainstalowany na maszynie Wirtualnej, następnie można zarejestrować dostawcy zasobów maszyny Wirtualnej SQL, określając trybie uproszczonym zarządzania SQL. W trybie uproszczonego zarządzania SQL, automatycznie dostawcy zasobów maszyny Wirtualnej SQL zainstalować rozszerzenie programu SQL IaaS w [trybie uproszczonym](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) i sprawdź programu SQL Server wystąpienie metadanych; spowoduje nie uruchamia ponownie usługę SQL Server. Należy podać typ licencji programu SQL Server żądanego podczas rejestrowania za pomocą dostawcy zasobów maszyny Wirtualnej SQL jako "PAYG" lub "AHUB". 

Zarejestruj maszynę Wirtualną programu SQL Server w trybie uproszczonego SQL zarządzania przy użyciu programu PowerShell za pomocą poniższej wstawki kodu:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

Rejestrowanie dostawcy zasobów maszyny Wirtualnej SQL w [trybie uproszczonym](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) będzie zapewnianiu zgodności i włączyć elastyczne Licencjonowanie także w miejscu programu SQL Server edition — aktualizacje. Wystąpienia klastra trybu failover i wdrożenia obejmujące wiele wystąpień można zarejestrować dostawcy zasobów maszyny Wirtualnej SQL tylko w trybie uproszczonym. Możesz wykonać kierunki, w witrynie Azure portal, aby uaktualnić do [trybu pełnego](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) i Włącz zestaw funkcji kompleksowe funkcje zarządzania o ponowne uruchomienie programu SQL Server w dowolnym momencie. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Rejestrowanie programu SQL Server 2008/R2 na maszynach wirtualnych z systemem Windows Server 2008

SQL Server 2008 i 2008 R2 z zainstalowanym systemem Windows Server 2008 można zarejestrować za pomocą maszyny Wirtualnej SQL dostawcy zasobów w [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) trybu. Ta opcja zapewnia zgodność i zezwala na maszynę Wirtualną programu SQL Server do monitorowania w witrynie Azure portal z ograniczoną funkcjonalnością.

W poniższej tabeli przedstawiono dopuszczalne wartości dla parametrów dostarczonych podczas rejestracji:

| Parametr | Dopuszczalne wartości                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, lub `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` lub `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Aby zarejestrować usługi programu SQL Server 2008 lub 2008 R2 w wystąpieniu systemu Windows Server 2008, należy użyć poniższej wstawki kodu programu Powershell:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Sprawdzanie stanu rejestracji
Aby sprawdzić, jeśli program SQL Server została już zarejestrowana za pomocą dostawcy zasobów maszyny Wirtualnej SQL przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

### <a name="azure-portal"></a>Azure Portal 
Aby sprawdzić stan rejestracji za pomocą witryny Azure portal, wykonaj następujące czynności.

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do swojej [maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md).
1. Wybierz maszynę Wirtualną usługi SQL Server z listy. Jeśli maszyna wirtualna serwera SQL nie ma na liście, prawdopodobnie nie został zarejestrowany do maszyny Wirtualnej z programu SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL. 
1. Wyświetl wartości w obszarze `Status`. Jeśli `Status = Succeeded`, a następnie maszynę Wirtualną programu SQL Server został zarejestrowany za pomocą dostawcy zasobów maszyny Wirtualnej SQL pomyślnie. 

    ![Sprawdź stan Rejestracja dostawcy zasobów SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Interfejs wiersza polecenia az

Sprawdź, czy bieżący stan rejestracji maszyny Wirtualnej programu SQL Server za pomocą następującego polecenia interfejsu wiersza polecenia AZ `ProvisioningState` pokaże `Succeeded` Jeśli rejestracji zakończyło się pomyślnie. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Sprawdź bieżący stan rejestracji maszyny Wirtualnej programu SQL Server za pomocą następującego polecenia cmdlet programu PowerShell. `ProvisioningState` pokaże `Succeeded` Jeśli rejestracji zakończyło się pomyślnie. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Błąd wskazuje, że maszynę Wirtualną programu SQL Server nie został zarejestrowany za pomocą dostawcy zasobów. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Procedura Rejestruj dostawcę zasobów maszyny Wirtualnej SQL z subskrypcją 

Aby zarejestrować się do maszyny Wirtualnej z programu SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL, należy zarejestrować dostawcę zasobów, do Twojej subskrypcji. Aby to zrobić za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="azure-portal"></a>Azure Portal

Poniższe kroki zostaną Zarejestruj dostawcę zasobów maszyny Wirtualnej SQL z subskrypcją platformy Azure przy użyciu witryny Azure portal. 

1. Otwórz witrynę Azure portal i przejdź do **wszystkich usług**. 
1. Przejdź do **subskrypcje** i wybierz subskrypcję zainteresowania.  
1. Na **subskrypcje** stronie, przejść do **dostawców zasobów**. 
1. Typ `sql` filtru, aby wyświetlić dostawców zasobów związanych z SQL. 
1. Wybierz opcję *zarejestrować*, *ponownie zarejestrować*, lub *Wyrejestruj* dla **Microsoft.SqlVirtualMachine** dostawcy, w zależności od usługi żądaną akcję. 

   ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Interfejs wiersza polecenia az
Poniższy fragment kodu będzie Zarejestruj dostawcę zasobów maszyny Wirtualnej SQL z subskrypcją platformy Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

Poniższy fragment kodu programu PowerShell będzie Zarejestruj dostawcę zasobów maszyny Wirtualnej SQL z subskrypcją platformy Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Uwagi

 - Dostawca zasobów maszyny Wirtualnej SQL obsługuje tylko maszyny wirtualne SQL Server wdrożony przy użyciu Menedżera zasobów. Maszyny wirtualne SQL Server wdrożony przy użyciu modelu klasycznego nie są obsługiwane. 
 - Dostawca zasobów maszyny Wirtualnej SQL obsługuje tylko maszyny wirtualne SQL Server wdrożony w chmurze publicznej. Wdrożenia do prywatnej lub chmury dla instytucji rządowych nie są obsługiwane. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na maszynie Wirtualnej Windows wskazówki dotyczące cen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


