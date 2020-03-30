---
title: Zarejestruj się u dostawcy zasobów maszyny Wirtualnej SQL
description: Zarejestruj maszynę wirtualną programu Azure SQL Server u dostawcy zasobów maszyny wirtualnej SQL, aby włączyć funkcje maszyn wirtualnych programu SQL Server wdrożonych poza usługą Azure Marketplace, a także zgodność i lepszą możliwość zarządzania.
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 01e683e31905281d25fdcf976bc58397c052a6c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243187"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Rejestrowanie maszyny wirtualnej programu SQL Server na platformie Azure u dostawcy zasobów maszyny wirtualnej SQL

W tym artykule opisano sposób rejestrowania maszyny wirtualnej programu SQL Server (VM) na platformie Azure u dostawcy zasobów maszyny wirtualnej SQL. Rejestrowanie się u dostawcy zasobów tworzy _zasób_ **maszyny wirtualnej SQL** w ramach subskrypcji, który jest oddzielnym zasobem od zasobu maszyny wirtualnej. Wyrejestrowanie maszyny Wirtualnej programu SQL Server od dostawcy zasobów spowoduje usunięcie _zasobu_ **maszyny wirtualnej SQL,** ale nie spowoduje upuszczenia rzeczywistej maszyny wirtualnej. 

Wdrażanie obrazu usługi SQL Server VM Azure Marketplace za pośrednictwem portalu Azure portal automatycznie rejestruje maszynę wirtualną programu SQL Server u dostawcy zasobów. Jeśli jednak zdecydujesz się samodzielnie zainstalować program SQL Server na maszynie wirtualnej platformy Azure lub aprowizować maszynę wirtualną platformy Azure z niestandardowej maszyny wirtualnej, należy zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów:

- **Korzyści z funkcji:** Rejestracja maszyny Wirtualnej programu SQL Server u dostawcy zasobów odblokowuje [automatyczne łatanie,](virtual-machines-windows-sql-automated-patching.md) [automatyczne tworzenie kopii zapasowych,](virtual-machines-windows-sql-automated-backup-v2.md)a także funkcje monitorowania i zarządzania. Odblokowuje również elastyczność [licencjonowania](virtual-machines-windows-sql-ahb.md) i [edycji.](virtual-machines-windows-sql-change-edition.md) Wcześniej te funkcje były dostępne tylko dla obrazów maszyn wirtualnych programu SQL Server wdrożonych w witrynie Azure Marketplace. 

- **Zgodność:** Rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL oferuje uproszczoną metodę spełnienia wymogu powiadamiania firmy Microsoft, że korzyść hybrydowa platformy Azure została włączona zgodnie z postanowieniami dotyczącymi produktu. Ten proces neguje konieczność zarządzania formularzami rejestracji licencjonowania dla każdego zasobu.  

- **Bezpłatne zarządzanie:** Rejestracja u dostawcy zasobów maszyny Wirtualnej SQL we wszystkich trzech trybach zarządzania jest całkowicie bezpłatna. Nie ma żadnych dodatkowych kosztów związanych z dostawcą zasobów lub ze zmieniającymi się trybami zarządzania. 

- **Uproszczone zarządzanie licencjami:** rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL upraszcza zarządzanie licencjami programu SQL Server i umożliwia szybkie identyfikowanie maszyn wirtualnych programu SQL Server z włączoną korzyścią hybrydową platformy Azure przy użyciu [portalu Azure portal](virtual-machines-windows-sql-manage-portal.md), interfejsu wiersza polecenia az lub programu PowerShell: 

   # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Aby korzystać z dostawcy zasobów maszyny Wirtualnej SQL, należy najpierw [zarejestrować subskrypcję u dostawcy zasobów,](#register-subscription-with-rp)co daje dostawcy zasobów możliwość tworzenia zasobów w ramach tej określonej subskrypcji.

Aby uzyskać więcej informacji na temat korzyści płynących z używania dostawcy zasobów maszyny Wirtualnej SQL, obejrzyj ten klip wideo [channel9:](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Korzystaj z dostawcy zasobów maszyn wirtualnych SQL podczas samodzielnej instalacji programu SQL Server na platformie Azure — Microsoft Channel 9 Video"></iframe>


## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów, musisz: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Maszyna [wirtualna](virtual-machines-windows-portal-sql-server-provision.md) programu SQL Server modelu zasobów platformy Azure wdrożona w publicznej chmurze lub w chmurze platformy Azure dla instytucji rządowych. 
- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub programu [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Tryby zarządzania

Jeśli [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) nie zostało jeszcze zainstalowane, zarejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL automatycznie instaluje rozszerzenie IaaS programu SQL Server w jednym z trzech trybów zarządzania, określonych podczas procesu rejestracji. Niepodkreślenie trybu zarządzania spowoduje zainstalowanie rozszerzenia IaaS SQL w trybie pełnego zarządzania.  

Jeśli rozszerzenie SQL IaaS zostało już zainstalowane ręcznie, jest ono już w trybie pełnego zarządzania, a zarejestrowanie się u dostawcy zasobów w trybie pełnym nie spowoduje ponownego uruchomienia usługi PROGRAMU SQL Server.

Trzy tryby zarządzania to:

- **Tryb uproszczony** nie wymaga ponownego uruchomienia programu SQL Server, ale obsługuje tylko zmianę typu licencji i wersji programu SQL Server. Użyj tej opcji dla maszyn wirtualnych programu SQL Server z wieloma wystąpieniami lub uczestniczących w wystąpieniu klastra trybu failover (FCI). Nie ma wpływu na pamięć lub procesor cpu podczas korzystania z trybu lekkiego i nie ma żadnych kosztów skojarzonych. Zaleca się, aby najpierw zarejestrować maszynę wirtualną programu SQL Server w trybie lekkim, a następnie uaktualnić do trybu pełnego podczas okna zaplanowanej konserwacji.  

- **Tryb pełny** zapewnia wszystkie funkcje, ale wymaga ponownego uruchomienia programu SQL Server i uprawnień administratora systemu. Jest to opcja instalowana domyślnie podczas ręcznej instalacji rozszerzenia IaaS SQL. Użyj go do zarządzania maszyną wirtualną programu SQL Server za pomocą jednego wystąpienia. Tryb pełny instaluje dwie usługi systemu Windows, które mają minimalny wpływ na pamięć i procesor — można je monitorować za pomocą Menedżera zadań. Korzystanie z trybu pełnego zarządzania nie wiąże się z żadnymi kosztami. 

- **Tryb NoAgent** jest dedykowany programom SQL Server 2008 i SQL Server 2008 R2 zainstalowanym w systemie Windows Server 2008. Nie ma wpływu na pamięć lub procesor podczas korzystania z trybu NoAgent. Korzystanie z trybu zarządzania NoAgent nie wiąże się z żadnymi kosztami. 

Bieżący tryb agenta IaaS programu SQL Server można wyświetlić przy użyciu programu PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Zarejestruj subskrypcję za pomocą rp

Aby zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny Wirtualnej SQL, należy najpierw zarejestrować subskrypcję u dostawcy zasobów. Daje to dostawcy zasobów maszyny Wirtualnej SQL możliwość tworzenia zasobów w ramach subskrypcji.  Można to zrobić przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

### <a name="azure-portal"></a>Portal Azure

1. Otwórz witrynę Azure portal i przejdź do **witryny Wszystkie usługi**. 
1. Przejdź do **subskrypcji** i wybierz subskrypcję odsetek.  
1. Na stronie **Subskrypcje** przejdź do **sekcji Dostawcy zasobów**. 
1. Wprowadź **sql** w filtrze, aby przywołać dostawców zasobów związanych z SQL. 
1. Wybierz **pozycję Zarejestruj ,** Ponownie zarejestruj **lub** **Wyrejestruj** dla dostawcy **Microsoft.SqlVirtualMachine,** w zależności od żądanej akcji. 

![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Wiersz polecenia

Zarejestruj dostawcę zasobów maszyny Wirtualnej SQL w ramach subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia Az lub programu PowerShell. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Rejestrowanie maszyny wirtualnej SQL w urzędzie RP 

### <a name="lightweight-management-mode"></a>Lekki tryb zarządzania

Jeśli [rozszerzenie agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) nie zostało zainstalowane na maszynie wirtualnej, zalecenie jest zarejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL w trybie uproszczonym. Spowoduje to zainstalowanie rozszerzenia IaaS SQL w [trybie uproszczonym](#management-modes) i uniemożliwi ponowne uruchomienie usługi SQL Server. Następnie można uaktualnić do trybu pełnego w dowolnym momencie, ale w ten sposób uruchomi się ponownie usługi PROGRAMU SQL Server, więc zaleca się czekać do zaplanowanego okna konserwacji. 

Podaj typ licencji programu SQL Server jako`PAYG`płatność zgodnie z rzeczywistym użyciem`AHUB`( ) w celu zapłaty`DR`za użycie, korzyść hybrydową platformy Azure ( ) w celu użycia własnej licencji lub odzyskiwanie po awarii ( ) w celu aktywowania [bezpłatnej licencji repliki odzyskiwania po awarii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure).

Wystąpienia klastra trybu failover i wdrożeń wielu wystąpień można zarejestrować tylko u dostawcy zasobów maszyny Wirtualnej SQL w trybie uproszczonym. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Zarejestruj maszynę wirtualną programu SQL Server w trybie uproszczonym w aplikacji Az CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[Powershell](#tab/powershell)

Zarejestruj maszynę wirtualną programu SQL Server w trybie lekkim w programie PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Pełny tryb zarządzania


Jeśli rozszerzenie SQL IaaS zostało już zainstalowane ręcznie na maszynie wirtualnej, można zarejestrować maszynę wirtualną programu SQL Server w trybie pełnym bez ponownego uruchamiania usługi PROGRAMU SQL Server. **Jeśli jednak rozszerzenie SQL IaaS nie zostało zainstalowane, rejestracja w trybie pełnym spowoduje zainstalowanie rozszerzenia IaaS SQL w trybie pełnym i ponowne uruchomienie usługi PROGRAMU SQL Server. Należy postępować ostrożnie.**


Aby zarejestrować maszynę wirtualną programu SQL Server bezpośrednio w trybie pełnym (i ewentualnie ponownie uruchomić usługę PROGRAMU SQL Server), należy użyć następującego polecenia programu PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Tryb zarządzania NoAgent 

Sql Server 2008 i 2008 R2 zainstalowane w systemie Windows Server 2008 _(nie R2)_ mogą być zarejestrowane u dostawcy zasobów sql vm w [trybie NoAgent](#management-modes). Ta opcja zapewnia zgodność i umożliwia sql server maszyny Wirtualnej do monitorowania w witrynie Azure portal z ograniczoną funkcjonalnością.

Określ `AHUB`albo `PAYG`, `DR` , lub jako **sqlLicenseType**, i albo `SQL2008-WS2008` lub `SQL2008R2-WS2008` jako **sqlImageOffer**. 

Aby zarejestrować wystąpienie programu SQL Server 2008 lub 2008 R2 w wystąpieniu systemu Windows Server 2008, użyj następującego fragmentu kodu interfejsu wiersza polecenia az lub programu PowerShell: 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Zarejestruj maszynę wirtualną programu SQL Server 2008 w trybie NoAgent w aplikacji Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```
 
 
Zarejestruj maszynę wirtualną programu SQL Server 2008 R2 w trybie NoAgent w aplikacji Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008R2
 ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Zarejestruj maszynę wirtualną programu SQL Server 2008 w trybie NoAgent w programie PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Zarejestruj maszynę wirtualną programu SQL Server 2008 R2 w trybie NoAgent w programie PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Uaktualnienie do trybu pełnego zarządzania 

Maszyny wirtualne programu SQL Server, które mają zainstalowane *lekkie* rozszerzenie IaaS, mogą uaktualnić tryb do _pełnego_ za pomocą portalu Azure, interfejsu wiersza polecenia Az lub programu PowerShell. Maszyny wirtualne programu SQL Server w trybie _NoAgent_ można uaktualnić do _pełnej_ po uaktualnieniu systemu operacyjnego do systemu Windows 2008 R2 lub powyżej. Nie jest możliwe do obniżenia — w tym celu należy [wyrejestrować](#unregister-vm-from-rp) maszynę wirtualną programu SQL Server z dostawcy zasobów maszyny Wirtualnej SQL. Spowoduje to usunięcie _zasobu_ **maszyny wirtualnej SQL,** ale nie spowoduje usunięcia rzeczywistej maszyny wirtualnej. 

Bieżący tryb agenta IaaS programu SQL Server można wyświetlić przy użyciu programu PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Aby uaktualnić tryb agenta do pełnego: 


### <a name="azure-portal"></a>Portal Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do zasobu [maszyn wirtualnych SQL.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 
1. Wybierz maszynę wirtualną programu SQL Server i wybierz pozycję **Przegląd**. 
1. W przypadku maszyn wirtualnych programu SQL Server z trybem NoAgent lub lightweight IaaS wybierz **opcję Tylko typ licencji, a aktualizacje wersji są dostępne z komunikatem rozszerzenia IaaS SQL.**

   ![Wybór zmiany trybu z portalu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Zaznacz pole wyboru **Zgadzam się na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej,** a następnie wybierz pozycję **Potwierdź,** aby uaktualnić tryb IaaS do pełnego. 

    ![Pole wyboru, aby wyrazić zgodę na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Wiersz polecenia

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Uruchom następujący fragment kodu interfejsu wiersza polecenia Az:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Uruchom następujący fragment kodu programu PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Weryfikowanie stanu rejestracji
Można sprawdzić, czy maszyna wirtualna programu SQL Server została już zarejestrowana u dostawcy zasobów maszyny Wirtualnej SQL przy użyciu portalu Azure, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

### <a name="azure-portal"></a>Portal Azure 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
1. Przejdź do [maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Wybierz maszynę wirtualną programu SQL Server z listy. Jeśli maszyna wirtualna programu SQL Server nie jest wymieniona w tym miejscu, prawdopodobnie nie została zarejestrowana u dostawcy zasobów maszyny Wirtualnej SQL. 
1. Wyświetl wartość w obszarze **Stan**. Jeśli **stan** **zakończy się pomyślnie,** maszyna wirtualna programu SQL Server została pomyślnie zarejestrowana u dostawcy zasobów maszyny Wirtualnej SQL. 

![Weryfikowanie stanu przy rejestracji SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Wiersz polecenia

Sprawdź bieżący stan rejestracji maszyny Wirtualnej programu SQL Server przy użyciu interfejsu wiersza polecenia Az lub programu PowerShell. `ProvisioningState`pokaże, `Succeeded` czy rejestracja się powiodła. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Błąd wskazuje, że maszyna wirtualna programu SQL Server nie została zarejestrowana u dostawcy zasobów. 


## <a name="unregister-vm-from-rp"></a>Wyrejestruj maszynę wirtualną z RP

Aby wyrejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny wirtualnej SQL, usuń *zasób* maszyny wirtualnej SQL przy użyciu portalu Azure lub interfejsu wiersza polecenia platformy Azure. Usunięcie *zasobu* maszyny wirtualnej SQL nie powoduje usunięcia maszyny wirtualnej programu SQL Server. Należy jednak zachować ostrożność i postępować zgodnie z instrukcjami, ponieważ możliwe jest przypadkowe usunięcie maszyny wirtualnej podczas próby usunięcia *zasobu*. 

Wyrejestrowanie maszyny Wirtualnej SQL z dostawcą zasobów maszyny Wirtualnej SQL jest konieczne, aby obniżyć tryb zarządzania z pełnego. 

### <a name="azure-portal"></a>Portal Azure

Aby wyrejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów przy użyciu witryny Azure portal, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do zasobu maszyny Wirtualnej programu SQL Server. 
  
   ![Zasób maszyn wirtualnych SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Wybierz pozycję **Usuń**. 

   ![Usuwanie dostawcy zasobów maszyny Wirtualnej SQL](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Wpisz nazwę maszyny wirtualnej SQL i **wyczyść pole wyboru obok maszyny wirtualnej**.

   ![Usuwanie dostawcy zasobów maszyny Wirtualnej SQL](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Niepowodzenie wyczyszczenia pola wyboru obok nazwy maszyny wirtualnej spowoduje całkowite *usunięcie* maszyny wirtualnej. Wyczyść to pole wyboru, aby wyrejestrować maszynę wirtualną programu SQL Server od dostawcy zasobów, ale *nie usunąć rzeczywistej maszyny wirtualnej*. 

1. Wybierz **polecenie Usuń,** aby potwierdzić usunięcie *zasobu*maszyny wirtualnej SQL, a nie maszyny wirtualnej programu SQL Server. 

### <a name="command-line"></a>Wiersz polecenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby wyrejestrować maszynę wirtualną programu SQL Server od dostawcy zasobów za pomocą interfejsu wiersza polecenia azure cli, użyj polecenia [az sql vm delete.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) Spowoduje to usunięcie *zasobu* maszyny wirtualnej programu SQL Server, ale nie spowoduje usunięcia maszyny wirtualnej. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby wyrejestrować maszynę wirtualną programu SQL Server od dostawcy zasobów za pomocą interfejsu wiersza polecenia Platformy Azure, użyj polecenia [New-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) Spowoduje to usunięcie *zasobu* maszyny wirtualnej programu SQL Server, ale nie spowoduje usunięcia maszyny wirtualnej. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Ograniczenia

Dostawca zasobów maszyny Wirtualnej SQL obsługuje tylko:
- Maszyny wirtualne programu SQL Server wdrożone za pośrednictwem usługi Azure Resource Manager. Maszyny wirtualne programu SQL Server wdrożone za pośrednictwem modelu klasycznego nie są obsługiwane. 
- Maszyny wirtualne programu SQL Server wdrożone w chmurze publicznej lub platformy Azure dla instytucji rządowych. Wdrożenia w innych chmurach prywatnych lub rządowych nie są obsługiwane. 


## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**Czy należy zarejestrować maszynę wirtualną programu SQL Server aprowizowana z obrazu programu SQL Server w portalu Azure Marketplace?**

Nie. Firma Microsoft automatycznie rejestruje maszyny wirtualne aprowied z obrazów programu SQL Server w portalu Azure Marketplace. Rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL jest wymagane tylko wtedy, gdy maszyna wirtualna *nie* została zainicjowana z obrazów programu SQL Server w portalu Azure Marketplace, a program SQL Server został zainstalowany samodzielnie.

**Czy dostawca zasobów maszyny wirtualnej SQL jest dostępny dla wszystkich klientów?** 

Tak. Klienci powinni zarejestrować swoje maszyny wirtualne programu SQL Server u dostawcy zasobów maszyny Wirtualnej SQL, jeśli nie używali obrazu programu SQL Server z witryny Azure Marketplace i zamiast tego samodzielnie instalowali program SQL Server lub jeśli przynieśli niestandardowy dysk WIRTUALNY. Maszyny wirtualne należące do wszystkich typów subskrypcji (Direct, Enterprise Agreement i Cloud Solution Provider) mogą rejestrować się u dostawcy zasobów maszyn wirtualnych SQL.

**Czy należy zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL, jeśli moja maszyna wirtualna programu SQL Server ma już zainstalowane rozszerzenie IaaS programu SQL Server?**

Jeśli maszyna wirtualna programu SQL Server jest zainstalowana samodzielnie i nie jest aprowizowana z obrazów programu SQL Server w portalu Azure Marketplace, należy zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL, nawet jeśli zainstalowano rozszerzenie IaaS programu SQL Server. Rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL tworzy nowy zasób typu Microsoft.SqlVirtualMachines. Zainstalowanie rozszerzenia IaaS programu SQL Server nie powoduje utworzenia tego zasobu.

**Co to jest domyślny tryb zarządzania podczas rejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL?**

Domyślny tryb zarządzania podczas rejestracji u dostawcy zasobów maszyny Wirtualnej SQL jest *pełny*. Jeśli właściwość zarządzania programu SQL Server nie jest ustawiona podczas rejestracji u dostawcy zasobów maszyny Wirtualnej SQL, tryb zostanie ustawiony jako pełne zarządzanie, a usługa SQL Server zostanie ponownie uruchomiona. Zaleca się, aby najpierw zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL w trybie uproszczonym, a następnie uaktualnić do pełnej podczas okna konserwacji. 

**Jakie są wymagania wstępne do zarejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL?**

Nie ma żadnych wymagań wstępnych do rejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL w trybie lekkim lub bez agenta. Warunkiem wstępnym rejestracji u dostawcy zasobów maszyny Wirtualnej SQL w trybie pełnym jest zainstalowanie rozszerzenia IaaS programu SQL Server na maszynie Wirtualnej, ponieważ w przeciwnym razie usługa SQL Server zostanie ponownie uruchomiona. 

**Czy mogę zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL, jeśli nie mam zainstalowanego rozszerzenia IaaS programu SQL Server na maszynie Wirtualnej?**

Tak, można zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL w trybie zarządzania w sposób lekki, jeśli nie masz rozszerzenia IaaS programu SQL Server zainstalowanego na maszynie wirtualnej. W trybie uproszczonym dostawca zasobów maszyny Wirtualnej SQL użyje aplikacji konsoli do weryfikacji wersji i wersji wystąpienia programu SQL Server. 

Domyślny tryb zarządzania SQL podczas rejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL jest _pełny_. Jeśli właściwość SQL Management nie jest ustawiona podczas rejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL, tryb zostanie ustawiony jako pełne zarządzanie. Zaleca się, aby najpierw zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL w trybie uproszczonym, a następnie uaktualnić do pełnej podczas okna konserwacji. 

**Czy rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL zainstaluje agenta na mojej maszynie wirtualnej?**

Nie. Rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL spowoduje utworzenie tylko nowego zasobu metadanych. Nie zainstaluje agenta na maszynie wirtualnej.

Rozszerzenie IaaS programu SQL Server jest potrzebne tylko do włączenia pełnej możliwości zarządzania. Uaktualnienie trybu zarządzania z lekkiego do pełnego zainstaluje rozszerzenie IaaS programu SQL Server i uruchomi ponownie program SQL Server.

**Czy rejestrowanie się u dostawcy zasobów maszyny Wirtualnej programu SQL Server spowoduje ponowne uruchomienie programu SQL Server na mojej maszynie wirtualnej?**

To zależy od trybu określonego podczas rejestracji. Jeśli określono tryb lightweight lub NoAgent, usługa SQL Server nie zostanie ponownie uruchomiona. Jednak określenie trybu zarządzania jako pełnego lub pozostawienie pustego trybu zarządzania spowoduje zainstalowanie rozszerzenia IaaS SQL w trybie pełnego zarządzania, co spowoduje ponowne uruchomienie usługi SQL Server. 

**Jaka jest różnica między trybami zarządzania lekkimi i bezgentowymi podczas rejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL?** 

Tryb zarządzania bez agenta jest dostępny tylko dla programów SQL Server 2008 i SQL Server 2008 R2 w systemie Windows Server 2008. Jest to jedyny dostępny tryb zarządzania dla tych wersji. Dla wszystkich innych wersji programu SQL Server dwa dostępne tryby zarządzania są lekkie i pełne. 

Tryb bez agenta wymaga, aby klient ustawiał właściwości wersji i wersji programu SQL Server. Tryb lekkiego zapytania maszyny Wirtualnej, aby znaleźć wersję i wydanie wystąpienia programu SQL Server.

**Czy mogę zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL bez określania typu licencji programu SQL Server?**

Nie. Typ licencji programu SQL Server nie jest właściwością opcjonalną podczas rejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL. Musisz ustawić typ licencji programu SQL Server jako pay-as-you-go lub korzyści hybrydowe platformy Azure podczas rejestrowania się u dostawcy zasobów maszyny Wirtualnej SQL we wszystkich trybach zarządzania (bez agenta, lekkie i pełne).

**Czy można uaktualnić rozszerzenie IaaS programu SQL Server z trybu bezgentowego do trybu pełnego?**

Nie. Uaktualnienie trybu zarządzania do pełnego lub lekkiego nie jest dostępne w trybie bez agenta. Jest to techniczne ograniczenie systemu Windows Server 2008. Najpierw należy uaktualnić system operacyjny do systemu Windows Server 2008 R2 lub większego, a następnie można uaktualnić do trybu pełnego zarządzania. 

**Czy można uaktualnić rozszerzenie IaaS programu SQL Server z trybu lekkiego do trybu pełnego?**

Tak. Uaktualnianie trybu zarządzania ze odciążonych do pełnych jest obsługiwane za pośrednictwem programu PowerShell lub witryny Azure portal. Wymaga ponownego uruchomienia usługi SQL Server.

**Czy mogę obniżyć rozszerzenie IaaS programu SQL Server z trybu pełnego na tryb zarządzania bez agenta lub lekkiego?**

Nie. Obniżenie poziomu trybu zarządzania rozszerzeniem programu SQL Server IaaS nie jest obsługiwane. Trybu zarządzania nie można obniżyć z trybu pełnego do trybu lekkiego lub bez agenta i nie można go obniżyć z trybu lekkiego do trybu bez agenta. 

Aby zmienić tryb zarządzania z pełnego zarządzania, [wyrejestrować](#unregister-vm-from-rp) maszynę wirtualną programu SQL Server od dostawcy zasobów programu SQL Server, upuszczając *zasób* programu SQL Server i ponownie zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów maszyny wirtualnej SQL w innym trybie zarządzania.

**Czy mogę zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL z witryny Azure portal?**

Nie. Rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL nie jest dostępne w witrynie Azure portal. Rejestrowanie się u dostawcy zasobów maszyny Wirtualnej SQL jest obsługiwane tylko w usłudze Azure CLI lub PowerShell. 

**Czy mogę zarejestrować maszynę wirtualną u dostawcy zasobów maszyny Wirtualnej SQL przed zainstalowaniem programu SQL Server?**

Nie. Maszyna wirtualna powinna mieć co najmniej jedno wystąpienie programu SQL Server (Aparat baz danych), aby pomyślnie zarejestrować się u dostawcy zasobów maszyny Wirtualnej SQL. Jeśli nie ma wystąpienia programu SQL Server na maszynie Wirtualnej, nowy zasób Microsoft.SqlVirtualMachine będzie w stanie awarii.

**Czy mogę zarejestrować maszynę wirtualną u dostawcy zasobów maszyny Wirtualnej SQL, jeśli istnieje wiele wystąpień programu SQL Server?**

Tak. Dostawca zasobów maszyny Wirtualnej SQL zarejestruje tylko jedno wystąpienie programu SQL Server (Aparat baz danych). Dostawca zasobów maszyny Wirtualnej SQL zarejestruje domyślne wystąpienie programu SQL Server w przypadku wielu wystąpień. Jeśli nie ma wystąpienia domyślnego, obsługiwane jest tylko rejestrowanie w trybie odciążenia. Aby uaktualnić z trybu lekkiego do pełnego zarządzania, domyślne wystąpienie programu SQL Server powinno istnieć lub maszyna wirtualna powinna mieć tylko jedno wystąpienie o nazwie SQL Server.

**Czy można zarejestrować wystąpienie klastra trybu failover programu SQL Server u dostawcy zasobów maszyny Wirtualnej SQL?**

Tak. Wystąpienia klastra trybu failover programu SQL Server na maszynie Wirtualnej platformy Azure mogą być rejestrowane u dostawcy zasobów maszyny Wirtualnej SQL w trybie uproszczonym. Jednak wystąpień klastra trybu failover programu SQL Server nie można uaktualnić do trybu pełnego zarządzania.

**Czy mogę zarejestrować maszynę wirtualną u dostawcy zasobów maszyny Wirtualnej SQL, jeśli skonfigurowano grupę dostępności zawsze włączone?**

Tak. Nie ma żadnych ograniczeń do rejestrowania wystąpienia programu SQL Server na maszynie Wirtualnej platformy Azure z dostawcą zasobów maszyny Wirtualnej SQL, jeśli uczestniczysz w konfiguracji grupy zawsze włączone dostępności.

**Jaki jest koszt rejestracji u dostawcy zasobów maszyny Wirtualnej SQL lub uaktualnienia do trybu pełnego zarządzania?**
Brak. Nie ma żadnych opłat związanych z rejestrowaniem się u dostawcy zasobów maszyny Wirtualnej SQL lub przy użyciu dowolnego z trzech trybów zarządzania. Zarządzanie maszyną wirtualną programu SQL Server za pomocą dostawcy zasobów jest całkowicie bezpłatne. 

**Jaki jest wpływ na wydajność korzystania z różnych trybów zarządzania?**
Korzystanie z trybów *NoAgent* i *lekkich* trybów zarządzania nie ma wpływu. Istnieje minimalny wpływ podczas korzystania z trybu *pełnego* zarządzania z dwóch usług, które są zainstalowane w os. Można je monitorować za pomocą Menedżera zadań i być widoczne we wbudowanej konsoli usług systemu Windows. 

Dwie nazwy usług to:
- `SqlIaaSExtensionQuery`(Nazwa wyświetlana - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension`(Nazwa wyświetlana - `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
