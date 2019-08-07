---
title: Jak zmienić model licencjonowania dla maszyny wirtualnej SQL Server na platformie Azure
description: Dowiedz się, jak przełączać Licencjonowanie dla maszyny wirtualnej SQL na platformie Azure z "płatność zgodnie z rzeczywistym użyciem" do pozycji "Przenieś własną licencję" przy użyciu Korzyść użycia hybrydowego platformy Azure.
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
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816720"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak zmienić model licencjonowania dla SQL Server maszyny wirtualnej na platformie Azure
W tym artykule opisano sposób zmiany modelu licencjonowania dla SQL Server maszyny wirtualnej na platformie Azure przy użyciu nowego dostawcy zasobów maszyny wirtualnej SQL — **Microsoft. SqlVirtualMachine**.

Istnieją dwa modele licencjonowania dla maszyny wirtualnej obsługującej SQL Server — płatność zgodnie z rzeczywistym użyciem i Korzyść użycia hybrydowego platformy Azure (AHB). Teraz za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell można modyfikować model licencjonowania SQL Server maszyny wirtualnej. 

Model **płatność zgodnie z rzeczywistym** użyciem (PAYG) oznacza, że koszt korzystania z maszyny wirtualnej platformy Azure na sekundę obejmuje również koszt licencji na SQL Server.
[Korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) umożliwia korzystanie z własnej licencji SQL Server z maszyną wirtualną z systemem SQL Server. 

Microsoft Azure korzyść użycia hybrydowego dla SQL Server umożliwia korzystanie z licencji SQL Server z programem Software Assurance ("kwalifikowana Licencja") na platformie Azure Virtual Machines. W przypadku Korzyść użycia hybrydowego platformy Azure SQL Server klienci nie będą obciążani opłatami za użycie licencji SQL Server na maszynie wirtualnej, ale muszą płacić za koszty związane z obliczeniami w chmurze (czyli stawką bazową), magazynem i zapleczem, a także we/wy skojarzone ze swoimi literami u SE usług (zgodnie z oczekiwaniami).

Zgodnie z postanowieniami produktu firmy Microsoft "klienci muszą wskazać, że używają Azure SQL Database (wystąpienia zarządzane, Pula elastyczna i pojedyncza baza danych), Azure Data Factory, SQL Server Integration Services lub SQL Server Virtual Machines w ramach hybrydowej platformy Azure Korzyść dla SQL Server podczas konfigurowania obciążeń na platformie Azure ".

Aby wskazać użycie Korzyść użycia hybrydowego platformy Azure dla SQL Server na maszynie wirtualnej platformy Azure i być zgodne, dostępne są trzy opcje:

1. Zainicjuj obsługę administracyjną maszyny wirtualnej przy użyciu obrazu SQL Server BYOL z portalu Azure Marketplace, który jest dostępny tylko dla klientów z Umowa Enterprise.
1. Zainicjuj obsługę maszyny wirtualnej przy użyciu obrazu SQL Server PAYG z witryny Azure Marketplace i aktywuj AHB.
1. Samodzielne instalowanie SQL Server na maszynie wirtualnej platformy Azure, ręczne [rejestrowanie SQL Server maszyn wirtualnych](virtual-machines-windows-sql-register-with-resource-provider.md) i aktywowanie AHB.

Typ licencji SQL Server jest ustawiany, gdy maszyna wirtualna jest obsługiwana i można ją zmienić w dowolnym momencie później. Przełączenie między modelamilicencji nie powoduje przestoju, nie powoduje ponownego uruchomienia maszyny wirtualnej, program nie będzie miał **żadnych dodatkowych kosztów** (w rzeczywistości aktywowanie AHB *zmniejsza* koszty) i obowiązuje od **razu**. 

## <a name="prerequisites"></a>Wymagania wstępne

Użycie dostawcy zasobów maszyny wirtualnej SQL wymaga rozszerzenia SQL IaaS. W związku z tym, aby kontynuować korzystanie z dostawcy zasobów maszyny wirtualnej SQL, potrzebne są następujące elementy:
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- [SQL Server maszynę wirtualną](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zarejestrowaną z zainstalowanym [dostawcą zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md) . 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Zmień licencję dla maszyn wirtualnych już zarejestrowanych przy użyciu dostawcy zasobów 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Model licencjonowania można modyfikować bezpośrednio w portalu. 

1. Otwórz [Azure Portal](https://portal.azure.com) i uruchom zasób usługi [SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) dla maszyny wirtualnej SQL Server. 
1. W obszarze **Ustawienia**wybierz pozycję **Konfiguruj** . 
1. Wybierz opcję **korzyść użycia hybrydowego platformy Azure** i zaznacz pole wyboru, aby potwierdzić, że masz licencję na SQL Server z programem Software Assurance. 
1. Wybierz pozycję **Zastosuj** w dolnej części strony **Konfigurowanie** . 

![AHB w portalu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Aby zmienić model licencjonowania, możesz użyć interfejsu wiersza polecenia platformy Azure.  

Poniższy fragment kodu zmienia model licencji z płatność zgodnie z rzeczywistym użyciem na BYOL (lub przy użyciu Korzyść użycia hybrydowego platformy Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Poniższy fragment kodu umożliwia przełączenie modelu "Przenieś własną licencję" na model płatność zgodnie z rzeczywistym użyciem: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
Aby zmienić model licencjonowania, można użyć programu PowerShell.

Poniższy fragment kodu zmienia model licencji z płatność zgodnie z rzeczywistym użyciem na BYOL (lub przy użyciu Korzyść użycia hybrydowego platformy Azure):

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

Poniższy fragment kodu umożliwia przełączenie modelu BYOL na model płatność zgodnie z rzeczywistym użyciem:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Zmień licencję dla maszyn wirtualnych, które nie są zarejestrowane przy użyciu dostawcy zasobów

Jeśli zainicjowano SQL Server maszynę wirtualną na podstawie obrazów z witryny Azure Marketplace, wówczas typem licencji SQL będzie PAYG. Jeśli zainicjowano SQL Server maszynę wirtualną przy użyciu obrazu BYOL z witryny Azure Marketplace, typ licencji to — AHUB. Wszystkie maszyny wirtualne SQL Server inicjowane z domyślnych (PAYG) lub BYOL obrazy w portalu Azure Marketplace zostaną automatycznie zarejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL, dzięki czemu mogą zmienić [Typ licencji](#change-license-for-vms-already-registered-with-resource-provider)

Możesz zakwalifikować się tylko do samoinstalowania SQL Server na maszynie wirtualnej platformy Azure za pośrednictwem Korzyść użycia hybrydowego platformy Azure i należy [zarejestrować te maszyny wirtualne przy użyciu dostawcy zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , ustawiając SQL Server licencję jako AHB, aby wskazać użycie AHB zgodnie z postanowieniami produktu firmy Microsoft.

Typ licencji maszyny wirtualnej SQL Server można zmienić tylko jako PAYG lub AHB, jeśli maszyna wirtualna SQL jest zarejestrowana u dostawcy zasobów maszyny wirtualnej SQL; wszystkie maszyny wirtualne SQL powinny być zarejestrowane przy użyciu jednostki UZALEŻNIONej SQL w celu zapewnienia zgodności licencji.

## <a name="remarks"></a>Uwagi

 - Klienci korzystający z usługi Azure Cloud Solution partner (CSP) mogą korzystać z Korzyść użycia hybrydowego platformy Azure, najpierw wdrażając maszynę wirtualną z opcją płatność zgodnie z rzeczywistym użyciem, a następnie konwertując ją na własną licencję, jeśli mają aktywne skojarzenia zabezpieczeń.
 - W przypadku porzucenia SQL Server zasobów maszyny wirtualnej powrócisz do ustawienia ustalonej licencji dla obrazu. 
  - Możliwość zmiany modelu licencjonowania to funkcja dostawcy zasobów maszyny wirtualnej SQL. Wdrożenie obrazu z portalu Marketplace za pomocą Azure Portal powoduje automatyczne zarejestrowanie SQL Serverj maszyny wirtualnej przy użyciu dostawcy zasobów. Jednak klienci, którzy samodzielnie instalują SQL Server, będą musieli ręcznie [zarejestrować swoją SQL Serverą maszynę wirtualną](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Dodanie maszyny wirtualnej SQL Server do zestawu dostępności wymaga ponownego utworzenia maszyny wirtualnej. W związku z tym wszystkie maszyny wirtualne dodane do zestawu dostępności pozostaną z powrotem do domyślnego typu licencji płatność zgodnie z rzeczywistym użyciem, a AHB należy ponownie włączyć. 


## <a name="limitations"></a>Ograniczenia

 - Zmiana modelu licencjonowania jest dostępna tylko dla klientów z programem Software Assurance.
 - Zmiana modelu licencjonowania jest obsługiwana tylko w przypadku wersji Standard i Enterprise SQL Server. Zmiany licencji dla ekspresowych, sieci Web i dewelopera nie są obsługiwane. 
 - Zmiana modelu licencjonowania jest obsługiwana tylko w przypadku maszyn wirtualnych wdrożonych przy użyciu modelu Menedżer zasobów. Maszyny wirtualne wdrożone przy użyciu modelu klasycznego nie są obsługiwane. Możesz migrować maszynę wirtualną z modelu klasycznego do usługi Resource Manager (ARM) i zarejestrować się przy użyciu dostawcy zasobów maszyny wirtualnej SQL. Po zarejestrowaniu maszyny wirtualnej przy użyciu dostawcy zasobów maszyny wirtualnej SQL zmiany modelu licencjonowania będą dostępne na maszynie wirtualnej. 
 - Zmiana modelu licencjonowania jest włączona tylko dla instalacji chmury publicznej.
 - Zmiana modelu licencjonowania jest obsługiwana tylko na maszynach wirtualnych z jedną kartą sieciową (interfejs sieciowy). Na maszynach wirtualnych, które mają więcej niż jedną kartę sieciową, należy najpierw usunąć jedną z kart sieciowych (przy użyciu Azure Portal) przed podjęciem próby wykonania procedury. W przeciwnym razie zostanie uruchomiony błąd podobny do następującego: `The virtual machine '\<vmname\>' has more than one NIC associated.`Mimo że może być możliwe dodanie karty sieciowej z powrotem do maszyny wirtualnej po zmianie trybu licencjonowania, operacje wykonywane przez stronę konfiguracji SQL w Azure Portal, na przykład automatyczne stosowanie poprawek i kopii zapasowej, nie będą już uznawane za obsługiwane.


## <a name="known-errors"></a>Znane błędy

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Nie znaleziono zasobu "Microsoft. SqlVirtualMachine/SqlVirtualMachines\</Resource-Group >" w grupie zasobów\<"Resource-Group >". Nie można odnaleźć właściwości "sqlServerLicenseType" w tym obiekcie. Sprawdź, czy właściwość istnieje i czy można ją ustawić.
Ten błąd występuje podczas próby zmiany modelu licencjonowania na SQL Server maszynie wirtualnej, która nie została zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL. Należy zarejestrować dostawcę zasobów w [ramach subskrypcji](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), a następnie zarejestrować SQL SERVERą maszynę wirtualną przy użyciu [dostawcy zasobów](virtual-machines-windows-sql-register-with-resource-provider.md)SQL. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nie można zweryfikować argumentu dla parametru "SKU"
Ten błąd może wystąpić podczas próby zmiany modelu licencjonowania SQL Server VM podczas korzystania Azure PowerShell > 4,0:`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Aby rozwiązać ten problem, usuń znaczniki komentarza z tych wierszy w wcześniej wymienionym fragmencie kodu programu PowerShell podczas przełączania modelu licencjonowania:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Użyj następującego kodu, aby zweryfikować wersję Azure PowerShell:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na maszynie wirtualnej z systemem Windows — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server wskazówki dotyczące cennika maszyn wirtualnych z systemem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server w informacjach o wersji maszyny wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


