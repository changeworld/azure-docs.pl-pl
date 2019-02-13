---
title: Jak zmienić modelu licencjonowania dla maszyny Wirtualnej z programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przełączyć licencjonowania dla maszyny wirtualnej programu SQL na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8526716b299d26d8d70c9c5e5cdace34e188d019
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111071"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure
W tym artykule opisano, jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure za pomocą nowego dostawcę zasobów maszyny Wirtualnej SQL — **Microsoft.SqlVirtualMachine**. Istnieją dwa modele dla maszyny wirtualnej (VM) hostowany program SQL Server — płatność za użycie, licencjonowania i model dostarczania własnej licencji (BYOL). A teraz przy użyciu programu PowerShell lub wiersza polecenia platformy Azure, możesz zmodyfikować model licencjonowania, który korzysta z maszyny Wirtualnej programu SQL Server. 

**Płatność za użycie** model (PAYG) oznacza, że koszt na sekundę maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server.

**Bring-your-own-license** modelu (BYOL) jest również nazywany [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), i umożliwia własnej licencji programu SQL Server za pomocą maszyny Wirtualnej z programu SQL Server. Aby uzyskać więcej informacji na temat cen, zobacz [przewodnik cen maszyn wirtualnych programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Przełączanie między dwoma modelami licencji spowoduje naliczenie **bez przerwy w działaniu**, ponownego uruchamiania maszyny Wirtualnej, dodaje **bez dodatkowych kosztów** (w rzeczywistości aktywowanie AHB *zmniejsza* koszt) i jest **obowiązywać natychmiast**. 

  >[!NOTE]
  > - Możliwość konwertowania modelu licencjonowania jest obecnie dostępna tylko podczas uruchamiania przy użyciu obrazu maszyny Wirtualnej programu SQL Server zgodnie z rzeczywistym użyciem. W przypadku uruchomienia przy użyciu obrazu bring-your-own-license, z poziomu portalu, nie można przekonwertować tego obrazu na płatność za rzeczywiste użycie. 
  > - Klienci programu CSP mogą wykorzystywać korzyści AHB najpierw wdrażanie maszyny Wirtualnej zgodnie z rzeczywistym użyciem, a następnie konwertując go bring-your-own-license. 


## <a name="prerequisites"></a>Wymagania wstępne
Użycie dostawcy zasobów maszyny Wirtualnej SQL wymaga rozszerzenie SQL IaaS. Jako takie aby kontynuować korzystanie z dostawcy zasobów maszyny Wirtualnej SQL, potrzebne są następujące elementy:
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- A [maszyny Wirtualnej programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) z [rozszerzenie SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) zainstalowane. 


## <a name="register-existing-sql-server-vm-with-sql-resource-provider"></a>Zarejestruj istniejącej maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów bazy danych SQL
Możliwości przełączania się między Modele licencjonowania jest funkcją, dostarczone przez nowego dostawcę zasobów maszyny Wirtualnej SQL (Microsoft.SqlVirtualMachine). Maszyny wirtualne SQL Server wdrożony po grudnia 2018 r są automatycznie rejestrowane za pomocą nowego dostawcy zasobów. Jednak trzeba zarejestrować ręcznie za pomocą dostawcy zasobów było możliwe przełączyć się ich modelu licencjonowania istniejących maszyn wirtualnych, które zostały wdrożone przed tą datą. 

  > [!NOTE] 
  > Jeśli usuniesz zasób maszynę Wirtualną SQL, będą wrócisz do ustawienia zakodowany licencji obrazu. 

### <a name="register-sql-resource-provider-with-your-subscription"></a>Rejestrowanie dostawcy zasobów bazy danych SQL w ramach subskrypcji 

Aby zarejestrować się do maszyny Wirtualnej z programu SQL Server za pomocą dostawcy zasobów bazy danych SQL, należy zarejestrować dostawcę zasobów, do Twojej subskrypcji. Można to zrobić za pomocą programu PowerShell lub za pomocą witryny Azure portal. 

#### <a name="using-powershell"></a>Korzystanie z programu PowerShell
Poniższy fragment kodu zarejestruje dostawcy zasobów bazy danych SQL w ramach subskrypcji platformy Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

#### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Poniższe kroki zarejestruje dostawcy zasobów bazy danych SQL w ramach subskrypcji platformy Azure przy użyciu witryny Azure portal. 

1. Otwórz witrynę Azure portal i przejdź do **wszystkich usług**. 
1. Przejdź do **subskrypcje** i wybierz subskrypcję zainteresowania.  
1. W **subskrypcje** bloku, przejdź do **dostawców zasobów**. 
1. Typ `sql` filtru, aby wyświetlić dostawców zasobów związanych z SQL. 
1. Wybierz opcję *zarejestrować*, *ponownie zarejestrować*, lub *Wyrejestruj* dla **Microsoft.SqlVirtualMachine** dostawcy, w zależności od usługi żądaną akcję. 

  ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Zarejestruj maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów bazy danych SQL
Po zarejestrowaniu dostawcy zasobów bazy danych SQL w ramach subskrypcji można użyć programu PowerShell, można zarejestrować maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów bazy danych SQL. 


```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
Można użyć programu PowerShell, aby zmienić model licencjonowania.  Pamiętaj, że maszyna wirtualna serwera SQL został już zarejestrowany przy użyciu nowego dostawcę zasobów programu SQL przed przełączeniem modelu licencjonowania. 

Poniższy fragment kodu zmienia swój model płatności za użycie licencji na BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure): 
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 

Poniższy fragment kodu zmienia modelu BYOL na płatność za użycie:
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

  >[!NOTE]
  > Aby przełączyć się między licencji, należy używać nowego dostawcę zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz uruchomić te polecenia przed zarejestrowaniem maszyny Wirtualnej programu SQL Server przy użyciu nowego dostawcę, może wystąpić ten błąd: `Get-AzResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Jeśli zostanie wyświetlony ten błąd, zarejestruj maszyny Wirtualnej programu SQL Server za pomocą nowego dostawcy zasobów. 

 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby zmienić model licencjonowania, można użyć wiersza polecenia platformy Azure.  Pamiętaj, że maszyna wirtualna serwera SQL został już zarejestrowany przy użyciu nowego dostawcę zasobów programu SQL przed przełączeniem modelu licencjonowania. 

Poniższy fragment kodu zmienia swój model płatności za użycie licencji na BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Poniższy fragment kodu zmienia modelu BYOL na płatność za użycie: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Aby przełączyć się między licencji, należy używać nowego dostawcę zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz uruchomić te polecenia przed zarejestrowaniem maszyny Wirtualnej programu SQL Server przy użyciu nowego dostawcę, może wystąpić ten błąd: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Jeśli zostanie wyświetlony ten błąd [zarejestrować maszyny Wirtualnej programu SQL Server przy użyciu nowego dostawcę zasobów](#register-existing-sql-server-vm-with-sql-resource-provider). 

## <a name="view-current-licensing"></a>Bieżąca licencja widoku 

Poniższy fragment kodu służy do wyświetlania bieżącego modelu licencjonowania maszyny wirtualnej programu SQL Server. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Znane błędy

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Rozszerzenie IaaS SQL nie jest zainstalowany na maszynie wirtualnej
Rozszerzenie SQL IaaS jest to konieczne wymaganie wstępne rejestrowanie maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz zarejestrować maszyny Wirtualnej programu SQL Server przed zainstalowaniem rozszerzenie SQL IaaS, wystąpi następujący błąd:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Aby rozwiązać ten problem, należy zainstalować rozszerzenie SQL IaaS przed podjęciem próby wykonania można zarejestrować maszyny Wirtualnej programu SQL Server. 

  > [!NOTE]
  > Instalowanie SQL IaaS rozszerzenia spowoduje ponowne uruchomienie usługi programu SQL Server i należy to robić tylko w oknie obsługi. Aby uzyskać więcej informacji, zobacz [instalacji SQL IaaS rozszerzenia](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nie można sprawdzić poprawności argumentu parametru "Sku"
Błąd może się pojawić podczas próby zmiany modelu licencjonowania maszyny Wirtualnej programu SQL Server przy użyciu programu Azure PowerShell > 4.0:

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Aby rozwiązać ten problem, Usuń komentarz następujące wiersze w opisanych powyżej fragmentu kodu programu PowerShell podczas przełączania model licencjonowania: 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Użyj poniższego kodu, aby sprawdzić posiadaną wersję programu Azure PowerShell:

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na maszynie Wirtualnej Windows wskazówki dotyczące cen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


