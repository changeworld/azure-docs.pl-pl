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
ms.openlocfilehash: 667a696e96234aca33981946a5b063ab5bfb080b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075876"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure
W tym artykule opisano, jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure za pomocą nowego dostawcę zasobów maszyny Wirtualnej SQL — **Microsoft.SqlVirtualMachine**. Istnieją dwa modele dla maszyny wirtualnej (VM) hostowany program SQL Server — zgodnie z rzeczywistym użyciem, licencjonowanie i model dostarczania własnej licencji (BYOL). A teraz, przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell można zmodyfikować model licencjonowania, który korzysta z maszyny Wirtualnej programu SQL Server. 

**Płatność za rzeczywiste użycie** model (PAYG) oznacza, że koszt na sekundę maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server.

**Bring-your-own-license** modelu (BYOL) jest również nazywany [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), i umożliwia własnej licencji programu SQL Server za pomocą maszyny Wirtualnej z programu SQL Server. Aby uzyskać więcej informacji na temat cen, zobacz [przewodnik cen maszyn wirtualnych programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Przełączanie między dwoma modelami licencji spowoduje naliczenie **bez przerwy w działaniu**, ponownego uruchamiania maszyny Wirtualnej, dodaje **bez dodatkowych kosztów** (w rzeczywistości aktywowanie AHB *zmniejsza* koszt) i jest **obowiązywać natychmiast**. 

## <a name="remarks"></a>Uwagi


 - Klienci usługi Azure Cloud Solution Partner (CSP) mogą korzystać z korzyści użycia hybrydowego platformy Azure, najpierw wdrażanie maszyny Wirtualnej zgodnie z rzeczywistym użyciem, a następnie konwertując go bring-your-own-license. 
 - Podczas rejestrowania niestandardowego obrazu maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów, należy określić typ licencji, jak = "AHUB". Pozostawienie licencji typu jako puste lub określenie "PAYG" spowoduje, że rejestracja nie powiedzie się. 
 - Jeśli usuniesz zasób maszynę Wirtualną programu SQL Server, będzie wrócisz do ustawienia ustaloną licencji obrazu. 
 - Dodanie maszyny Wirtualnej programu SQL Server do zestawu dostępności, wymaga ponownego tworzenia maszyny Wirtualnej. Jako takie, wszystkie maszyny wirtualne dodane do dostępności zestaw będzie wróć do domyślnego typu licencji zgodnie z rzeczywistym użyciem i AHB trzeba będzie ponownie włączyć. 
 - Możliwość zmiany modelu licencjonowania jest funkcją dostawcy zasobów maszyny Wirtualnej SQL. Wdrażanie obrazu z witryny marketplace w witrynie Azure portal automatycznie rejestruje maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów. Jednak klienci, którzy własnym zainstalowany program SQL Server należy ręcznie [zarejestrować ich SQL Server VM](#register-sql-server-vm-with-the-sql-vm-resource-provider). 
 

 
## <a name="limitations"></a>Ograniczenia

 - Możliwość konwertowania modelu licencjonowania jest obecnie dostępna tylko w przypadku rozpoczęcia od obrazu maszyny wirtualnej z programem SQL Server w wersji z płatnością zgodnie z rzeczywistym użyciem. W przypadku rozpoczęcia od obrazu typu „bring-your-own-license” z portalu nie można przekonwertować obrazu na wersję z płatnością zgodnie z rzeczywistym użyciem.
 - Zmiana modelu licencjonowania jest obsługiwana tylko dla maszyn wirtualnych wdrożonych przy użyciu modelu usługi Resource Manager. Maszyny wirtualne wdrożone przy użyciu modelu klasycznego, nie są obsługiwane. 
 - Zmiana modelu licencjonowania jest włączony tylko w przypadku instalacji w chmurze publicznej.
 - Zmiana modelu licencjonowania jest obsługiwana tylko na maszyny wirtualne z jedną kartą Sieciową (interfejs sieciowy). Na maszynach wirtualnych, które mają więcej niż jedną kartę Sieciową, należy najpierw usunąć jedną z kart sieciowych (przy użyciu witryny Azure portal) przed rozpoczęciem procedury. W przeciwnym razie wystąpi błąd podobny do następującego: `The virtual machine '\<vmname\>' has more than one NIC associated.` Mimo że można dodać kartę Sieciową do maszyny Wirtualnej, po zmianie trybu licencjonowania, operacje wykonywane przez blok konfiguracji programu SQL, takich jak automatyczne stosowanie poprawek i kopia zapasowa nie jest już uznawane za obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Użycie dostawcy zasobów maszyny Wirtualnej SQL wymaga rozszerzenie SQL IaaS. Jako takie aby kontynuować korzystanie z dostawcy zasobów maszyny Wirtualnej SQL, potrzebne są następujące elementy:
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- [Pakiet Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A *płatność za rzeczywiste użycie* [maszyny Wirtualnej programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) z [rozszerzenie SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) zainstalowane. 

## <a name="with-the-azure-portal"></a>Z witryną Azure Portal

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Możesz zmodyfikować model licencjonowania bezpośrednio z portalu. 

1. Otwórz [witryny Azure portal](https://portal.azure.com) i uruchom [zasobów maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) dla maszyny Wirtualnej programu SQL Server. 
1. Wybierz **skonfigurować** w obszarze **ustawienia**. 
1. Wybierz **korzyść użycia hybrydowego platformy Azure** opcji i upewnij się, że masz licencję programu SQL Server z pakietem Software Assurance. 
1. Wybierz **Zastosuj** w dolnej części **Konfiguruj** strony. 

![AHB w portalu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Ta opcja nie jest dostępna dla obrazów bring-your-own-license. 

## <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

Aby zmienić model licencjonowania, można użyć wiersza polecenia platformy Azure.  

Poniższy fragment kodu zmienia licencji zgodnie z rzeczywistym użyciem modelu BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Poniższy fragment kodu zmienia swój model bring-your-own-license na płatność za rzeczywiste użycie: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

## <a name="with-powershell"></a>Z programem PowerShell
Można użyć programu PowerShell, aby zmienić model licencjonowania.

Poniższy fragment kodu zmienia licencji zgodnie z rzeczywistym użyciem modelu BYOL (lub za pomocą korzyści użycia hybrydowego platformy Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
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

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Zarejestruj maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL
W niektórych sytuacjach może być konieczne ręczne zarejestrowanie maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL. Aby to zrobić, może również należy ręcznie zarejestrować dostawcy zasobów w ramach subskrypcji. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Procedura Rejestruj dostawcę zasobów maszyny Wirtualnej SQL z subskrypcją 

Aby zarejestrować się do maszyny Wirtualnej z programu SQL Server za pomocą dostawcy zasobów bazy danych SQL, należy zarejestrować dostawcę zasobów, do Twojej subskrypcji. Aby to zrobić za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. 

#### <a name="with-the-azure-portal"></a>Z witryną Azure Portal
Poniższe kroki, będą rejestrować dostawcy zasobów bazy danych SQL do subskrypcji platformy Azure przy użyciu witryny Azure portal. 

1. Otwórz witrynę Azure portal i przejdź do **wszystkich usług**. 
1. Przejdź do **subskrypcje** i wybierz subskrypcję zainteresowania.  
1. W **subskrypcje** bloku, przejdź do **dostawców zasobów**. 
1. Typ `sql` filtru, aby wyświetlić dostawców zasobów związanych z SQL. 
1. Wybierz opcję *zarejestrować*, *ponownie zarejestrować*, lub *Wyrejestruj* dla **Microsoft.SqlVirtualMachine** dostawcy, w zależności od usługi żądaną akcję. 

   ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure
Poniższy fragment kodu będzie Zarejestruj dostawcę zasobów maszyny Wirtualnej SQL z subskrypcją platformy Azure. 

```azurecli-interactive
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>Z programem PowerShell

Poniższy fragment kodu spowoduje zarejestrowanie dostawcy zasobów bazy danych SQL do subskrypcji platformy Azure.

```powershell-interactive
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Zarejestruj maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów bazy danych SQL
Po zarejestrowaniu dostawcy zasobów maszyny Wirtualnej SQL do Twojej subskrypcji można zarejestrować maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów przy użyciu wiersza polecenia platformy Azure. 

#### <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

Zarejestruj maszynę Wirtualną SQL Server przy użyciu wiersza polecenia platformy Azure przy użyciu poniższych fragmentu kodu: 

```azurecli-interactive
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation> --license-type <AHUB or PAYG>
```

#### <a name="with-powershell"></a>Z programem PowerShell
Zarejestruj maszynę Wirtualną SQL Server z poniższego fragmentu kodu przy użyciu programu PowerShell:

```powershell-interactive
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>Znane błędy

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Rozszerzenie IaaS SQL nie jest zainstalowany na maszynie wirtualnej
Rozszerzenie SQL IaaS jest to konieczne wymaganie wstępne rejestrowanie maszyny Wirtualnej programu SQL Server za pomocą dostawcy zasobów maszyny Wirtualnej SQL. Jeśli spróbujesz zarejestrować maszyny Wirtualnej programu SQL Server przed zainstalowaniem rozszerzenie SQL IaaS, wystąpi następujący błąd:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Aby rozwiązać ten problem, należy zainstalować rozszerzenie SQL IaaS przed podjęciem próby wykonania można zarejestrować maszyny Wirtualnej programu SQL Server. 

  > [!NOTE]
  > Instalowanie SQL IaaS rozszerzenia spowoduje ponowne uruchomienie usługi programu SQL Server i należy to robić tylko w oknie obsługi. Aby uzyskać więcej informacji, zobacz [instalacji SQL IaaS rozszerzenia](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Zasób "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<grupa zasobów >" w grupie zasobów "\<grupa zasobów >" nie został znaleziony. Nie można odnaleźć właściwości "sqlServerLicenseType" dla tego obiektu. Sprawdź, czy właściwość istnieje i czy można ustawić.
Ten błąd występuje podczas próby zmiany modelu licencjonowania na SQL Server VM, który nie został zarejestrowany za pomocą dostawcy zasobów bazy danych SQL. Należy zarejestrować dostawcę zasobów, aby Twoje [subskrypcji](#register-sql-vm-resource-provider-with-subscription), a następnie zarejestrować maszyny Wirtualnej programu SQL Server przy użyciu języka SQL [dostawcy zasobów](#register-sql-server-vm-with-sql-resource-provider). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nie można sprawdzić poprawności argumentu parametru "Sku"
Błąd może się pojawić podczas próby zmiany modelu licencjonowania maszyny Wirtualnej programu SQL Server przy użyciu programu Azure PowerShell > 4.0: Set-AzResource: Nie można sprawdzić poprawności argumentu parametru "Sku". Argument ma wartość null lub jest pusty. Podaj argument, który nie jest zerowa lub pusta, a następnie spróbuj ponownie wykonać polecenie.
Aby rozwiązać ten problem, Usuń komentarz następujące wiersze w opisanych powyżej fragmentu kodu programu PowerShell podczas przełączania model licencjonowania:

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Użyj poniższego kodu, aby sprawdzić posiadaną wersję programu Azure PowerShell:

```powershell-interactive
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na maszynie Wirtualnej Windows wskazówki dotyczące cen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


