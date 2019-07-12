---
title: Jak zmienić modelu licencjonowania dla maszyny Wirtualnej z programu SQL Server na platformie Azure
description: Dowiedz się, jak przełączyć licencjonowania dla maszyny wirtualnej SQL na platformie Azure na podstawie "płatność za rzeczywiste użycie" do "bring-your-own-license" za pomocą korzyści użycia hybrydowego platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
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
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786758"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure
W tym artykule opisano, jak zmienić modelu licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure za pomocą nowego dostawcę zasobów maszyny Wirtualnej SQL — **Microsoft.SqlVirtualMachine**.

Istnieją dwa modele licencjonowania maszyny wirtualnej (VM), hostowany program SQL Server — płatność za rzeczywiste użycie i korzyści użycia hybrydowego platformy Azure (AHB). A teraz przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell można modyfikować modelu licencjonowania maszyny wirtualnej programu SQL Server. 

**Płatność za rzeczywiste użycie** model (PAYG) oznacza, że koszt na sekundę maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server.
[Korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) pozwala na używanie własnej licencji programu SQL Server za pomocą maszyny Wirtualnej z programu SQL Server. 

Korzyść użycia hybrydowego platformy Azure firmy Microsoft dla programu SQL Server umożliwia korzystanie z licencji programu SQL Server z pakietem Software Assurance ("kwalifikowany licencji") w usłudze Azure Virtual Machines. Dzięki korzyści użycia hybrydowego platformy Azure dla programu SQL Server klienci nie zostanie obciążona za użycie licencji programu SQL Server na maszynie Wirtualnej, ale są nadal musisz zapłacić za koszty podstawowe zasoby obliczeniowe chmury (oznacza to, że stawka podstawowa), magazynu i kopii zapasowych, jak również we/wy skojarzonych z ich u SE usług (o ile dotyczy).

Zgodnie z postanowieniami produktu firmy Microsoft "klientów musi wskazywać, czy używają usługi Azure SQL Database (wystąpienie zarządzane, puli elastycznej i pojedynczej bazy danych), usługa Azure Data Factory, SQL Server Integration Services lub maszyn wirtualnych programu SQL Server w ramach hybrydowego platformy Azure Korzyści z programu SQL Server podczas konfigurowania obciążeń na platformie Azure."

Aby wskazać, użyj korzyści użycia hybrydowego platformy Azure dla programu SQL Server na maszynie Wirtualnej platformy Azure i zgodności, dostępne są trzy opcje:

1. Aprowizuj maszynę wirtualną przy użyciu obrazu programu SQL Server w ramach opcji BYOL z portalu Azure marketplace, jest dostępna tylko dla klientów z umową Enterprise Agreement.
1. Aprowizowanie maszyny wirtualnej przy użyciu obrazu PAYG programu SQL Server z witryny Azure marketplace i Aktywuj AHB.
1. Samodzielnie ręcznie zainstalować program SQL Server na maszynie Wirtualnej platformy Azure [zarejestrować ich SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) i Aktywuj AHB.

Typ licencji programu SQL Server jest ustawiona, gdy maszyna wirtualna jest aprowizowana i można je zmienić w dowolnym momencie później. Przełączanie między modelami licencji spowoduje naliczenie **bez przerwy w działaniu**, ponownego uruchamiania maszyny Wirtualnej, dodaje **bez dodatkowych kosztów** (w rzeczywistości aktywowanie AHB *zmniejsza* koszt) i jest **obowiązywać natychmiast**. 

## <a name="prerequisites"></a>Wymagania wstępne

Użycie dostawcy zasobów maszyny Wirtualnej SQL wymaga rozszerzenie SQL IaaS. Jako takie aby kontynuować korzystanie z dostawcy zasobów maszyny Wirtualnej SQL, potrzebne są następujące elementy:
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- [Pakiet Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A [maszyny Wirtualnej programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zarejestrowane w usłudze [dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md) zainstalowane. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Zmiany licencji dla maszyn wirtualnych już zarejestrowanego dostawcy zasobów 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Możesz zmodyfikować model licencjonowania bezpośrednio z portalu. 

1. Otwórz [witryny Azure portal](https://portal.azure.com) i uruchom [zasobów maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) dla maszyny Wirtualnej programu SQL Server. 
1. Wybierz **skonfigurować** w obszarze **ustawienia**. 
1. Wybierz **korzyść użycia hybrydowego platformy Azure** opcję i zaznacz pole wyboru, aby upewnić się, że masz licencję programu SQL Server z pakietem Software Assurance. 
1. Wybierz **Zastosuj** w dolnej części **Konfiguruj** strony. 

![AHB w portalu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[INTERFEJS WIERSZA POLECENIA AZ](#tab/bash)

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

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
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
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Zmień licencję dla maszyn wirtualnych nie jest zarejestrowana u dostawcy zasobów

Jeśli zainicjowano obsługę administracyjną maszyny Wirtualnej z programu SQL Server z obrazów w portalu Azure Marketplace PAYG typu licencji SQL będzie PAYG. Jeśli zainicjowano obsługę administracyjną maszyny Wirtualnej programu SQL Server w witrynie Azure Marketplace przy użyciu obrazów BYOL typu licencji będzie AHUB. Wszystkie maszyny wirtualne programu SQL Server, udostępnioną za pomocą domyślnego (PAYG) lub obrazów portalu Azure Marketplace w ramach opcji BYOL zostanie automatycznie zarejestrowany dostawcy zasobów maszyny Wirtualnej SQL, dzięki czemu będzie ona mogła zmienić [typ licencji](#change-license-for-vms-already-registered-with-resource-provider)

Tylko kwalifikujesz się do samodzielnie zainstalować program SQL Server na maszynie Wirtualnej platformy Azure za pomocą korzyści użycia hybrydowego platformy Azure i należy [zarejestrować te maszyny wirtualne za pomocą dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , ustawiając licencję programu SQL Server jako AHB, aby wskazać użycia AHB zgodnie z opisem w Warunki produktu firmy Microsoft.

Typ licencji maszyny Wirtualnej programu SQL Server jako PAYG lub AHB można zmienić tylko, jeśli maszyna wirtualna SQL został zarejestrowany za pomocą dostawcy zasobów maszyny Wirtualnej SQL; i wszystkie maszyny wirtualne SQL powinien być zarejestrowany z punktu odzyskiwania maszyny Wirtualnej SQL pod kątem zgodności licencji.

## <a name="remarks"></a>Uwagi

 - Klienci usługi Azure Cloud Solution Partner (CSP) mogą korzystać z korzyści użycia hybrydowego platformy Azure, najpierw wdrażanie maszyny Wirtualnej zgodnie z rzeczywistym użyciem, a następnie konwertując go bring-your-own-license jeśli mają aktywne skojarzenia zabezpieczeń.
 - Jeśli usuniesz zasób maszynę Wirtualną programu SQL Server, będzie wrócisz do ustawienia ustaloną licencji obrazu. 
  - Możliwość zmiany modelu licencjonowania jest funkcją dostawcy zasobów maszyny Wirtualnej SQL. Wdrażanie obrazu z witryny marketplace w witrynie Azure portal automatycznie rejestruje maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów. Jednak klienci, którzy własnym zainstalowany program SQL Server należy ręcznie [zarejestrować ich SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Dodanie maszyny Wirtualnej programu SQL Server do zestawu dostępności, wymaga ponownego tworzenia maszyny Wirtualnej. Jako takie, wszystkie maszyny wirtualne dodane do dostępności zestaw będzie wróć do domyślnego typu licencji zgodnie z rzeczywistym użyciem i AHB trzeba będzie ponownie włączyć. 


## <a name="limitations"></a>Ograniczenia

 - Zmiana modelu licencjonowania jest dostępna tylko dla klientów z pakietem software assurance.
 - Zmiana modelu licencjonowania jest obsługiwana tylko dla wersji standard i enterprise programu SQL Server. Zmiany licencji Express, Web i deweloper nie są obsługiwane. 
 - Zmiana modelu licencjonowania jest obsługiwana tylko dla maszyn wirtualnych wdrożonych przy użyciu modelu usługi Resource Manager. Maszyny wirtualne wdrożone przy użyciu modelu klasycznego, nie są obsługiwane. 
 - Zmiana modelu licencjonowania jest włączony tylko w przypadku instalacji w chmurze publicznej.
 - Zmiana modelu licencjonowania jest obsługiwana tylko na maszyny wirtualne z jedną kartą Sieciową (interfejs sieciowy). Na maszynach wirtualnych, które mają więcej niż jedną kartę Sieciową, należy najpierw usunąć jedną z kart sieciowych (przy użyciu witryny Azure portal) przed rozpoczęciem procedury. W przeciwnym razie wystąpi błąd podobny do następującego: `The virtual machine '\<vmname\>' has more than one NIC associated.` Mimo że można dodać kartę Sieciową do maszyny Wirtualnej, po zmianie trybu licencjonowania, operacje wykonywane za pomocą strony konfiguracji programu SQL w witrynie Azure portal, np. funkcję automatycznego instalowania poprawek i kopia zapasowa nie jest już uznawane za obsługiwane.


## <a name="known-errors"></a>Znane błędy

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Zasób "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<grupa zasobów >" w grupie zasobów "\<grupa zasobów >" nie został znaleziony. Nie można odnaleźć właściwości "sqlServerLicenseType" dla tego obiektu. Sprawdź, czy właściwość istnieje i czy można ustawić.
Ten błąd występuje podczas próby zmiany modelu licencjonowania na SQL Server VM, który nie został zarejestrowany za pomocą dostawcy zasobów maszyny Wirtualnej SQL. Należy zarejestrować dostawcę zasobów, aby Twoje [subskrypcji](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), a następnie zarejestrować maszyny Wirtualnej programu SQL Server przy użyciu języka SQL [dostawcy zasobów](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nie można sprawdzić poprawności argumentu parametru "Sku"
Błąd może się pojawić podczas próby zmiany modelu licencjonowania maszyny Wirtualnej programu SQL Server przy użyciu programu Azure PowerShell > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Aby rozwiązać ten problem, Usuń komentarz następujące wiersze w opisanych powyżej fragmentu kodu programu PowerShell podczas przełączania model licencjonowania:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Użyj poniższego kodu, aby sprawdzić posiadaną wersję programu Azure PowerShell:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na maszynie Wirtualnej Windows wskazówki dotyczące cen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


