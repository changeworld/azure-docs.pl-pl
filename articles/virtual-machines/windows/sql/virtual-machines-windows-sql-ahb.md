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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c2f302d7b87426115df716dfba638eee0756f79
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480740"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure
W tym artykule opisano, jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure za pomocą nowego dostawcę zasobów maszyny Wirtualnej SQL — **Microsoft.SqlVirtualMachine**. Istnieją dwa modele dla maszyny wirtualnej (VM) hostowany program SQL Server — zgodnie z rzeczywistym użyciem, licencjonowanie i model dostarczania własnej licencji (BYOL). A teraz przy użyciu programu PowerShell lub wiersza polecenia platformy Azure, możesz zmodyfikować model licencjonowania, który korzysta z maszyny Wirtualnej programu SQL Server. 

**Płatność za rzeczywiste użycie** model (PAYG) oznacza, że koszt na sekundę maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server.

**Bring-your-own-license** modelu (BYOL) jest również nazywany [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), i umożliwia własnej licencji programu SQL Server za pomocą maszyny Wirtualnej z programu SQL Server. Aby uzyskać więcej informacji na temat cen, zobacz [przewodnik cen maszyn wirtualnych programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Przełączanie między dwoma modelami licencji spowoduje naliczenie **bez przerwy w działaniu**, ponownego uruchamiania maszyny Wirtualnej, dodaje **bez dodatkowych kosztów** (w rzeczywistości aktywowanie AHB *zmniejsza* koszt) i jest **obowiązywać natychmiast**. 

  >[!NOTE]
  > - Możliwość konwertowania modelu licencjonowania jest obecnie dostępna tylko w przypadku rozpoczęcia od obrazu maszyny wirtualnej z programem SQL Server w wersji z płatnością zgodnie z rzeczywistym użyciem. W przypadku rozpoczęcia od obrazu typu „bring-your-own-license” z portalu nie można przekonwertować obrazu na wersję z płatnością zgodnie z rzeczywistym użyciem.
  > - Klienci programu CSP mogą wykorzystywać korzyści AHB najpierw wdrażanie maszyny Wirtualnej zgodnie z rzeczywistym użyciem, a następnie konwertując go bring-your-own-license. 
  > - Obecnie ta możliwość jest włączona tylko dla urządzeń w chmurze publicznej.


## <a name="prerequisites"></a>Wymagania wstępne
Użycie dostawcy zasobów maszyny Wirtualnej SQL wymaga rozszerzenie SQL IaaS. Jako takie aby kontynuować korzystanie z dostawcy zasobów maszyny Wirtualnej SQL, potrzebne są następujące elementy:
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- [Pakiet Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A *płatność za rzeczywiste użycie* [maszyny Wirtualnej programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) z [rozszerzenie SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) zainstalowane. 


## <a name="register-sql-resource-provider-with-your-subscription"></a>Rejestrowanie dostawcy zasobów bazy danych SQL w ramach subskrypcji 

Możliwości przełączania się między Modele licencjonowania jest funkcją, dostarczone przez nowego dostawcę zasobów maszyny Wirtualnej SQL (Microsoft.SqlVirtualMachine). Maszyny wirtualne SQL Server wdrożony po grudnia 2018 r są automatycznie rejestrowane za pomocą nowego dostawcy zasobów. Jednak trzeba zarejestrować ręcznie za pomocą dostawcy zasobów było możliwe przełączyć się ich modelu licencjonowania istniejących maszyn wirtualnych, które zostały wdrożone przed tą datą. 

  > [!NOTE] 
  > Jeśli usuniesz zasób maszynę Wirtualną programu SQL Server, będzie wrócisz do ustawienia zakodowany licencji obrazu. 

Aby zarejestrować się do maszyny Wirtualnej z programu SQL Server za pomocą dostawcy zasobów bazy danych SQL, należy zarejestrować dostawcę zasobów, do Twojej subskrypcji. Można to zrobić przy użyciu wiersza polecenia platformy Azure, programu PowerShell lub za pomocą witryny Azure portal. 

### <a name="with-the-azure-portal"></a>Z witryną Azure Portal
Poniższe kroki zarejestruje dostawcy zasobów bazy danych SQL w ramach subskrypcji platformy Azure przy użyciu witryny Azure portal. 

1. Otwórz witrynę Azure portal i przejdź do **wszystkich usług**. 
1. Przejdź do **subskrypcje** i wybierz subskrypcję zainteresowania.  
1. W **subskrypcje** bloku, przejdź do **dostawców zasobów**. 
1. Typ `sql` filtru, aby wyświetlić dostawców zasobów związanych z SQL. 
1. Wybierz opcję *zarejestrować*, *ponownie zarejestrować*, lub *Wyrejestruj* dla **Microsoft.SqlVirtualMachine** dostawcy, w zależności od usługi żądaną akcję. 

   ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure
Poniższy fragment kodu zarejestruje dostawcy zasobów bazy danych SQL w ramach subskrypcji platformy Azure. 

```cli
# Register the new SQL resource provider for your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>Z programem PowerShell
Poniższy fragment kodu zarejestruje dostawcy zasobów bazy danych SQL w ramach subskrypcji platformy Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>Zarejestruj maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów bazy danych SQL
Po zarejestrowaniu dostawcy zasobów bazy danych SQL w ramach subskrypcji można zarejestrować maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów. Aby zrobić, za pomocą wiersza polecenia platformy Azure i programu PowerShell. 

### <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

Zarejestruj maszynę Wirtualną SQL Server przy użyciu wiersza polecenia platformy Azure za pomocą poniższej wstawki kodu: 

```cli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>Z programem PowerShell

Zarejestruj maszynę Wirtualną SQL Server z poniższego fragmentu kodu przy użyciu programu PowerShell: 
```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="change-licensing-model"></a>Zmień model licencjonowania
Maszyna wirtualna serwera SQL został zarejestrowany za pomocą dostawcy zasobów, można zmienić modelu licencjonowania przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 


  >[!NOTE]
  >  Możliwość konwertowania modelu licencjonowania jest obecnie dostępna tylko w przypadku rozpoczęcia od obrazu maszyny wirtualnej z programem SQL Server w wersji z płatnością zgodnie z rzeczywistym użyciem. W przypadku rozpoczęcia od obrazu typu „bring-your-own-license” z portalu nie można przekonwertować obrazu na wersję z płatnością zgodnie z rzeczywistym użyciem. 

### <a name="with-the-azure-portal"></a>Z witryną Azure Portal
Możesz zmodyfikować model licencjonowania bezpośrednio z portalu. 

1. Przejdź do maszyny Wirtualnej programu SQL Server w ramach [witryny Azure portal](https://portal.azure.com). 
1. Wybierz **konfiguracji programu SQL Server** w **ustawienia** okienka. 
1. Wybierz **Edytuj** w **licencja programu SQL Server** okienku, zmodyfikuj licencję. 

![AHB w portalu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Ta opcja nie jest dostępna dla obrazów bring-your-own-license. 

### <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure
Aby zmienić model licencjonowania, można użyć wiersza polecenia platformy Azure.  

Poniższy fragment kodu zmienia licencji zgodnie z rzeczywistym użyciem modelu BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure):
```azurecli
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Poniższy fragment kodu zmienia modelu BYOL na płatność za rzeczywiste użycie: 
```azurecli
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>Z programem PowerShell 
Można użyć programu PowerShell, aby zmienić model licencjonowania. 

Poniższy fragment kodu zmienia licencji zgodnie z rzeczywistym użyciem modelu BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure): 
```powershell
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Poniższy fragment kodu zmienia modelu BYOL na płatność za rzeczywiste użycie:
```powershell
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 


## <a name="view-current-licensing"></a>Bieżąca licencja widoku 

Poniższy fragment kodu służy do wyświetlania bieżącego modelu licencjonowania maszyny wirtualnej programu SQL Server. 

```powershell
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
```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Użyj poniższego kodu, aby sprawdzić posiadaną wersję programu Azure PowerShell:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Nie można odnaleźć zasobu "Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < grupa zasobów >" w grupie zasobów "< grupa zasobów >". Nie można odnaleźć właściwości "sqlServerLicenseType" dla tego obiektu. Sprawdź, czy właściwość istnieje i czy można ustawić.
Ten błąd występuje, gdy maszynę Wirtualną programu SQL Server nie został zarejestrowany za pomocą dostawcy zasobów bazy danych SQL. Należy zarejestrować dostawcę zasobów za pomocą usługi [subskrypcji](#register-sql-resource-provider-with-your-subscription), a następnie zarejestrować maszyny Wirtualnej programu SQL Server przy użyciu języka SQL [dostawcy zasobów](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na maszynie Wirtualnej Windows wskazówki dotyczące cen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


