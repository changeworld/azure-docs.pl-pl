---
title: Jak zmienić modelu licencjonowania dla maszyny Wirtualnej SQL na platformie Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: c516bf9b48164f2ef8dc7fea6fb834bdae00a0d1
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332153"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure
W tym artykule opisano, jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure za pomocą nowego dostawcy zasobów bazy danych SQL — **Microsoft.SqlVirtualMachine**. Istnieją dwa modele dla maszyny wirtualnej (VM) hostowany program SQL Server — płatność za użycie, licencjonowania i model dostarczania własnej licencji (BYOL). A teraz przy użyciu programu PowerShell lub wiersza polecenia platformy Azure, możesz zmodyfikować model licencjonowania, który korzysta z maszyny Wirtualnej SQL. 

**Płatność za użycie** model oznacza, że koszt na sekundę maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server.

**Bring-your-own-license** modelu jest także znana jako [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), i umożliwia własnej licencji programu SQL Server za pomocą maszyny Wirtualnej z programu SQL Server. Aby uzyskać więcej informacji na temat cen, zobacz [przewodnik cen maszyn wirtualnych SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Przełączanie między dwoma modelami licencji spowoduje naliczenie **bez przerwy w działaniu**, ponownego uruchamiania maszyny Wirtualnej, dodaje **bez dodatkowych kosztów** (w rzeczywistości aktywowanie AHB zmniejsza koszt) i jest **obowiązywać natychmiast**. 

## <a name="prerequisites"></a>Wymagania wstępne
Użycie dostawcy zasobów maszyny Wirtualnej SQL wymaga rozszerzenie SQL IaaS. Jako takie aby kontynuować korzystanie z dostawcy zasobów maszyny Wirtualnej SQL, potrzebne są następujące elementy:
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- A [maszyny Wirtualnej programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) z [rozszerzenie SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) zainstalowane. 

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Zarejestruj istniejącej maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów
Możliwości przełączania się między Modele licencjonowania jest funkcją, dostarczone przez nowego dostawcę zasobów maszyny Wirtualnej SQL (Microsoft.SqlVirtualMachine). W tej chwili aby można było przełączyć model licencjonowania, najpierw należy zarejestrować nowego dostawcę do Twojej subskrypcji, a następnie zarejestrować istniejącej maszyny Wirtualnej z nowego dostawcę zasobów maszyny Wirtualnej SQL. Aby korzystać z dostawcy zasobów maszyny Wirtualnej SQL, należy również zainstalować rozszerzenie SQL IaaS. Ten sposób będzie można zarejestrować maszynę Wirtualną, która została wdrożona za pomocą wirtualnego dysku twardego. Aby uzyskać więcej informacji, zobacz [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

  >[!IMPORTANT]
  > Jeśli usuniesz zasób maszynę Wirtualną SQL, będą wrócisz do ustawienia zakodowany licencji obrazu. 
  

### <a name="powershell"></a>PowerShell


Poniższy fragment kodu będzie nawiązania połączenia z usługi Azure i Sprawdź identyfikator subskrypcji, której używasz. 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

Poniższy fragment kodu najpierw rejestruje nowego dostawcy zasobu języka SQL dla Twojej subskrypcji, a następnie rejestruje istniejącej maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portal
Można również zarejestrować nowego dostawcę zasobów maszyny Wirtualnej SQL przy użyciu portalu. Można więc wykonaj następujące kroki:
1. Otwórz witrynę Azure portal i przejdź do **wszystkich usług**. 
1. Przejdź do **subskrypcje** i wybierz subskrypcję zainteresowania.  
1. W **subskrypcje** bloku, przejdź do **dostawcy zasobów**. 
1. Typ `sql` filtru, aby wyświetlić dostawców zasobów związanych z SQL. 
1. Wybierz opcję *zarejestrować*, *ponownie zarejestrować*, lub *Wyrejestruj* dla **Microsoft.SqlVirtualMachine** dostawcy, w zależności od usługi żądaną akcję. 

  ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
Można użyć programu PowerShell, aby zmienić model licencjonowania.  Pamiętaj, że maszyna wirtualna SQL został już zarejestrowany przy użyciu nowego dostawcę zasobów programu SQL przed przełączeniem modelu licencjonowania. 

Poniższy fragment kodu zmienia swój model płatności za użycie licencji na BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure): 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

Poniższy fragment kodu zmienia modelu BYOL na płatność za użycie:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Aby przełączyć się między licencji, należy używać nowego dostawcę zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz uruchomić te polecenia przed zarejestrowaniem maszyny Wirtualnej SQL przy użyciu nowego dostawcę, może wystąpić ten błąd: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Jeśli zostanie wyświetlony ten błąd [zarejestrować maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby zmienić model licencjonowania, można użyć wiersza polecenia platformy Azure.  Pamiętaj, że maszyna wirtualna SQL został już zarejestrowany przy użyciu nowego dostawcę zasobów programu SQL przed przełączeniem modelu licencjonowania. 

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
  >Aby przełączyć się między licencji, należy używać nowego dostawcę zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz uruchomić te polecenia przed zarejestrowaniem maszyny Wirtualnej SQL przy użyciu nowego dostawcę, może wystąpić ten błąd: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Jeśli zostanie wyświetlony ten błąd [zarejestrować maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Bieżąca licencja widoku 

Poniższy fragment kodu służy do wyświetlania bieżącego modelu licencjonowania maszyny wirtualnej SQL. 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
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

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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


