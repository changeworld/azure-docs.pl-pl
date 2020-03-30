---
title: Zmienianie modelu licencji maszyny Wirtualnej programu SQL Server na platformie Azure
description: Dowiedz się, jak przełączyć licencjonowanie maszyny wirtualnej programu SQL Server na platformie Azure z płatności zgodnie z rzeczywistym użyciem na własną licencję przy użyciu korzyści hybrydowej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201830"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Zmienianie modelu licencji maszyny wirtualnej programu SQL Server na platformie Azure
W tym artykule opisano sposób zmiany modelu licencji dla maszyny wirtualnej programu SQL Server (VM) na platformie Azure przy użyciu nowego dostawcy zasobów maszyny wirtualnej SQL, **Microsoft.SqlVirtualMachine**.

Istnieją trzy modele licencji dla maszyny Wirtualnej, która jest gospodarzem programu SQL Server: płatność zgodnie z rzeczywistymi instrukcjami, korzyści hybrydowe platformy Azure i odzyskiwanie po awarii (DR). Model licencji maszyny Wirtualnej programu SQL Server można zmodyfikować przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

- Model **płatności zgodnie z rzeczywistymi przypadł na** sekundę oznacza, że koszt uruchomienia maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server.
- [Usługa Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) umożliwia korzystanie z własnej licencji programu SQL Server z maszyną wirtualną z uruchomionym programem SQL Server. 
- Typ licencji **odzyskiwania po awarii** jest używany dla [bezpłatnej repliki odzyskiwania po awarii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) na platformie Azure. 

Usługa Azure Hybrid Benefit umożliwia korzystanie z licencji programu SQL Server z pakietem Software Assurance ("Licencja kwalifikowana") na maszynach wirtualnych platformy Azure. Dzięki korzyści hybrydowej platformy Azure klienci nie są obciążani opłatami za korzystanie z licencji programu SQL Server na maszynie Wirtualnej. Ale nadal muszą płacić za koszt podstawowej chmury obliczeniowej (czyli stawki podstawowej), magazynu i kopii zapasowych. Muszą również zapłacić za we/wy związane z korzystaniem z usług (w stosownych przypadkach).

Zgodnie z warunkami produktu firmy Microsoft: "Klienci muszą wskazać, że używają usługi Azure SQL Database (wystąpienie zarządzane, pula elastyczna i pojedyncza baza danych), usługi Azure Data Factory, usługi integracji programu SQL Server lub maszyny wirtualne programu SQL Server na platformie Azure Korzyści hybrydowe dla programu SQL Server podczas konfigurowania obciążeń na platformie Azure."

Aby wskazać użycie korzyści hybrydowych platformy Azure dla programu SQL Server na maszynie wirtualnej platformy Azure i być zgodnym, masz trzy opcje:

- Aprowizuj maszynę wirtualną przy użyciu obrazu programu SQL Server z usługi Azure Marketplace przy użyciu obrazu programu SQL Server przy użyciu własnej licencji. Ta opcja jest dostępna tylko dla klientów, którzy mają umowę Enterprise Agreement.
- Aprowizuj maszynę wirtualną przy użyciu obrazu programu SQL Server zgodnie z rzeczywistym użyciem z witryny Azure Marketplace i aktywuj korzyści hybrydowe platformy Azure.
- Samodzielne instalowanie programu SQL Server na maszynie Wirtualnej platformy Azure, ręczne [rejestrowanie się u dostawcy zasobów maszyn wirtualnych SQL](virtual-machines-windows-sql-register-with-resource-provider.md)i aktywowanie korzyści hybrydowych platformy Azure.

Typ licencji programu SQL Server jest ustawiany podczas aprowizowania maszyny Wirtualnej. Możesz go zmienić w dowolnym momencie. Przełączanie między modelami licencji nie powoduje przestojów, nie powoduje ponownego uruchomienia maszyny Wirtualnej ani usługi SQL Server, nie zwiększa żadnych dodatkowych kosztów i wchodzi w życie natychmiast. W rzeczywistości aktywowanie korzyści hybrydowej platformy Azure *zmniejsza* koszty.

## <a name="prerequisites"></a>Wymagania wstępne

Zmiana modelu licencjonowania maszyny Wirtualnej programu SQL Server ma następujące wymagania: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Maszyna [wirtualna programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zarejestrowana u dostawcy zasobów maszyny [Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Pakiet Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) jest wymogiem korzystania z [korzyści hybrydowej platformy Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/) 


## <a name="vms-already-registered-with-the-resource-provider"></a>Maszyny wirtualne już zarejestrowane u dostawcy zasobów 

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Model licencji można zmodyfikować bezpośrednio z portalu: 

1. Otwórz [witrynę Azure portal](https://portal.azure.com) i otwórz [zasób maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) dla maszyny wirtualnej programu SQL Server. 
1. Wybierz **pozycję Konfiguruj** w obszarze **Ustawienia**. 
1. Wybierz opcję **Korzyści hybrydowe platformy Azure** i zaznacz to pole wyboru, aby potwierdzić, że masz licencję programu SQL Server z pakietem Software Assurance. 
1. Wybierz **pozycję Zastosuj** u dołu strony **Konfigurowanie.** 

![Korzyści hybrydowe platformy Azure w portalu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Za pomocą interfejsu wiersza polecenia platformy Azure można zmienić model licencji.  


**Korzyści hybrydowe platformy Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Zapłać zgodnie z oczekiwaniami**: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Odzyskiwanie awaryjne (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Za pomocą programu PowerShell można zmienić model licencji.

**Korzyść użycia hybrydowego platformy Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Płatność zgodnie z rzeczywistym użyciem**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Odzyskiwanie** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Maszyny wirtualne niezarejestrowane u dostawcy zasobów

Jeśli aprowizowana maszyna wirtualna programu SQL Server została udostępniona na podstawie obrazów usługi Azure Marketplace zgodnie z rzeczywistym posuwem, typ licencji programu SQL Server będzie płatny zgodnie z rzeczywistymi oczekiwaniami. Jeśli aprowizowana maszyna wirtualna programu SQL Server przy użyciu obrazu bring-your-own-license z portalu Azure Marketplace, typem licencji będzie AHUB. Wszystkie maszyny wirtualne programu SQL Server aprowizowane domyślnie (płatność zgodnie z rzeczywistym użyciem) lub obrazy portalu Azure Marketplace z własną licencją zostaną automatycznie zarejestrowane u dostawcy zasobów maszyny Wirtualnej SQL, aby mogli zmienić [typ licencji.](#vms-already-registered-with-the-resource-provider)

Możesz samodzielnie zainstalować program SQL Server tylko na maszynie Wirtualnej Platformy Azure za pośrednictwem usługi Azure Hybrid Benefit. Należy [zarejestrować te maszyny wirtualne u dostawcy zasobów maszyny Wirtualnej SQL,](virtual-machines-windows-sql-register-with-resource-provider.md) ustawiając licencję programu SQL Server jako korzyść hybrydową platformy Azure, aby wskazać użycie korzyści hybrydowej platformy Azure zgodnie z warunkami produktu firmy Microsoft.

Typ licencji maszyny Wirtualnej programu SQL Server można zmienić jako płatną lub hybrydową korzyść platformy Azure tylko wtedy, gdy maszyna wirtualna programu SQL Server jest zarejestrowana u dostawcy zasobów maszyny Wirtualnej SQL.

## <a name="remarks"></a>Uwagi

- Klienci dostawcy rozwiązań w chmurze platformy Azure (CSP) mogą korzystać z korzyści hybrydowej platformy Azure, najpierw wdrażając maszynę wirtualną zgodnie z rzeczywistym użyciem, a następnie konwertując ją na własną licencję, jeśli mają aktywny pakiet Software Assurance.
- Jeśli upuścisz zasób maszyny Wirtualnej programu SQL Server, powrócisz do ustawienia licencji zakodowanej obrazu. 
- Możliwość zmiany modelu licencji jest funkcją dostawcy zasobów maszyny Wirtualnej SQL. Wdrażanie obrazu portalu Azure Marketplace za pośrednictwem witryny Azure portal automatycznie rejestruje maszynę wirtualną programu SQL Server u dostawcy zasobów. Ale klienci, którzy samodzielnie instalują program SQL Server, będą musieli ręcznie [zarejestrować swoją maszynę wirtualną programu SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Dodanie maszyny Wirtualnej programu SQL Server do zestawu dostępności wymaga ponownego utworzenia maszyny Wirtualnej. W związku z tym wszystkie maszyny wirtualne dodane do zestawu dostępności powrócą do domyślnego typu licencji płatności zgodnie z rzeczywistymi przystępu. Korzyści hybrydowe platformy Azure będą musiały być włączone ponownie. 


## <a name="limitations"></a>Ograniczenia

Zmiana modelu licencji to:
   - Dostępne tylko dla klientów z [pakietem Software Assurance.](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)
   - Obsługiwane tylko dla wersji standard i Enterprise programu SQL Server. Zmiany licencji dla programów Express, Web i Developer nie są obsługiwane. 
   - Obsługiwane tylko dla maszyn wirtualnych wdrożonych za pośrednictwem modelu usługi Azure Resource Manager. Maszyny wirtualne wdrożone za pośrednictwem modelu klasycznego nie są obsługiwane. 
   - Dostępne tylko dla chmur publicznych lub platformy Azure dla instytucji rządowych. 
   - Obsługiwane tylko na maszynach wirtualnych, które mają jeden interfejs sieciowy (NIC). 


## <a name="known-errors"></a>Znane błędy

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Nie znaleziono zasobu "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>"\<w obszarze grupa zasobów '> grupy zasobów'.

Ten błąd występuje podczas próby zmiany modelu licencji na maszynie wirtualnej programu SQL Server, która nie została zarejestrowana u dostawcy zasobów maszyny Wirtualnej SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Musisz zarejestrować subskrypcję u dostawcy zasobów, a następnie [zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów.](virtual-machines-windows-sql-register-with-resource-provider.md) 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Maszyna wirtualna\<' vmname\>' ma więcej niż jedną kartę sieciową skojarzoną

Ten błąd występuje na maszynach wirtualnych, które mają więcej niż jedną kartę sieciową. Usuń jedną z kart sieciowych przed zmianą modelu licencjonowania. Chociaż można dodać kartę sieciową z powrotem do maszyny Wirtualnej po zmianie modelu licencji, operacje w witrynie Azure portal, takie jak automatyczna kopia zapasowa i poprawki nie będą już obsługiwane. 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


