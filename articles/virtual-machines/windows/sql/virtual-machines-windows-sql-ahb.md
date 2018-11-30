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
ms.openlocfilehash: 3bcbfc876ed27d16180ca03801f2054ad804e148
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500340"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-virtual-machine-in-azure"></a>Jak zmienić modelu licencjonowania maszyny wirtualnej SQL na platformie Azure
W tym artykule opisano, jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure za pomocą nowego dostawcy zasobów bazy danych SQL — **Microsoft.SqlVirtualMachine**. Istnieją dwa modele dla maszyny wirtualnej (VM) hostowany program SQL Server — płatność za użycie, licencjonowania i model dostarczania własnej licencji (BYOL). A teraz przy użyciu programu Powershell lub wiersza polecenia platformy Azure, możesz zmodyfikować model licencjonowania, który korzysta z maszyny Wirtualnej SQL. 

**Płatność za użycie** model oznacza, że koszt na sekundę maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server.

**Bring-your-own-license** modelu jest także znana jako [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), i umożliwia własnej licencji programu SQL Server za pomocą maszyny Wirtualnej z programu SQL Server. Aby uzyskać więcej informacji na temat cen, zobacz [przewodnik cen maszyn wirtualnych SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Przełączanie między dwoma modelami licencji spowoduje naliczenie **bez przerwy w działaniu**, ponownego uruchamiania maszyny Wirtualnej, dodaje **bez dodatkowych kosztów** (w rzeczywistości aktywowanie AHB zmniejsza koszt) i jest **obowiązywać natychmiast**. 


## <a name="register-legacy-sql-vm-with-new-resource-provider"></a>Rejestrowanie starszej wersji maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów
Możliwości przełączania się między Modele licencjonowania jest funkcją, dostarczone przez nowego dostawcę zasobów maszyny Wirtualnej SQL (Microsoft.SqlVirtualMachine). W tej chwili aby można było przełączyć model licencjonowania, najpierw należy zarejestrować nowego dostawcę do Twojej subskrypcji, a następnie zarejestrować starsze maszyny Wirtualnej z nowego dostawcę zasobów maszyny Wirtualnej SQL. 

  >[!IMPORTANT]
  > Jeśli usuniesz zasób maszynę Wirtualną SQL, będą wrócisz do ustawienia zakodowany licencji obrazu. 

### <a name="powershell"></a>PowerShell

Poniższy kod najpierw rejestruje dostawcę zasobów programu SQL z Twoją subskrypcją i następnie rejestruje starsze maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów. 

```powershell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>

# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your legacy SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portal
Można również zarejestrować nowego dostawcę zasobów maszyny Wirtualnej SQL przy użyciu portalu. Można więc wykonaj następujące kroki:
1. Otwórz witrynę Azure portal i przejdź do **wszystkich usług**. 
1. Przejdź do **subskrypcje** i wybierz subskrypcję zainteresowania.  

  ![Wybierz swoją subskrypcję](media/virtual-machines-windows-sql-ahb/open-subscriptions.png)

1. W **subskrypcje** bloku, przejdź do **dostawcy zasobów**. 
1. Typ `sql` filtru, aby wyświetlić dostawców zasobów związanych z SQL. 
1. Wybierz opcję *zarejestrować*, *ponownie zarejestrować*, lub *Wyrejestruj* dla **Microsoft.SqlVirtualMachine** dostawcy, w zależności od usługi żądaną akcję. 

  ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
Można użyć programu Powershell, aby zmienić model licencjonowania.  Pamiętaj, że maszyna wirtualna SQL został już zarejestrowany przy użyciu nowego dostawcę zasobów programu SQL przed przełączeniem modelu licencjonowania. 

Ten fragment kodu zmienia swój model płatności za użycie licencji na BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure): 
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

Ten fragment kodu zmienia modelu BYOL na płatność za użycie:
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Aby przełączyć się między licencji, należy używać nowego dostawcę zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz uruchomić te polecenia przed zarejestrowaniem maszyny Wirtualnej SQL przy użyciu nowego dostawcę, można napotkać ten błąd: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Jeśli zostanie wyświetlony ten błąd [zarejestrować maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów](#register-legacy-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby zmienić model licencjonowania, można użyć wiersza polecenia platformy Azure.  Pamiętaj, że maszyna wirtualna SQL został już zarejestrowany przy użyciu nowego dostawcę zasobów programu SQL przed przełączeniem modelu licencjonowania. 

Ten fragment kodu zmienia swój model płatności za użycie licencji na BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Ten fragment kodu zmienia modelu BYOL na płatność za użycie: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Aby przełączyć się między licencji, należy używać nowego dostawcę zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz uruchomić te polecenia przed zarejestrowaniem maszyny Wirtualnej SQL przy użyciu nowego dostawcę, można napotkać ten błąd: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Jeśli zostanie wyświetlony ten błąd [zarejestrować maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów](#register-legacy-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Bieżąca licencja widoku 

Poniższy fragment kodu służy do wyświetlania bieżącego modelu licencjonowania maszyny wirtualnej SQL. 

```powershell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [Program SQL Server na wskazówki cen maszyn wirtualnych systemu windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


