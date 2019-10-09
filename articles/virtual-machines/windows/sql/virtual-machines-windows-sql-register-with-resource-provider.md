---
title: Zarejestruj SQL Server maszynę wirtualną na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL | Microsoft Docs
description: Zarejestruj maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL, aby zwiększyć możliwości zarządzania.
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
ms.openlocfilehash: b0a7221107f05ff2239bd77cc18e7ffedc18efc1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023597"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Rejestrowanie SQL Server maszyny wirtualnej na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL

W tym artykule opisano sposób rejestrowania maszyny wirtualnej SQL Server na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL. 

Wdrożenie SQL Server maszyny wirtualnej w portalu Azure Marketplace za pomocą Azure Portal automatycznie rejestruje maszynę wirtualną SQL Server przy użyciu dostawcy zasobów. Jeśli zdecydujesz się na samodzielne zainstalowanie SQL Server na maszynie wirtualnej platformy Azure zamiast wybierania obrazu z witryny Azure Marketplace lub w przypadku udostępnienia maszyny wirtualnej platformy Azure z niestandardowego dysku VHD z SQL Server, należy zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów dla :

- **Uproszczenie zarządzania licencjami**: zgodnie z postanowieniami produktu firmy Microsoft klienci muszą poinformować firmę Microsoft, gdy używają [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL upraszcza zarządzanie licencjami SQL Server i umożliwia szybkie identyfikowanie SQL Server maszyn wirtualnych przy użyciu Korzyść użycia hybrydowego platformy Azure w [portalu](virtual-machines-windows-sql-manage-portal.md) lub polecenia AZ CLI: 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **Zalety funkcji**: Rejestracja SQL SERVEREJ maszyny wirtualnej przy użyciu dostawcy zasobów odblokowuje [Automatyczne stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md), [Automatyczne tworzenie kopii zapasowych](virtual-machines-windows-sql-automated-backup-v2.md)oraz możliwości monitorowania i zarządzania. Umożliwia również odblokowanie [licencjonowania](virtual-machines-windows-sql-ahb.md) i [wersji](virtual-machines-windows-sql-change-edition.md) . Wcześniej te funkcje były dostępne tylko w przypadku SQL Server obrazów maszyn wirtualnych z witryny Azure Marketplace.

- **Bezpłatne zarządzanie**: Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL i wszystkie tryby zarządzania są całkowicie bezpłatne. Dostawca zasobów nie wiąże się z dodatkowymi kosztami ani z zmianami trybów zarządzania. 

Aby korzystać z dostawcy zasobów maszyny wirtualnej SQL, należy również zarejestrować dostawcę zasobów maszyny wirtualnej SQL w ramach subskrypcji. Można to zrobić za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

  > [!NOTE]
  > Nie istnieją żadne dodatkowe wymagania licencyjne związane z rejestracją u dostawcy zasobów. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL oferuje uproszczoną metodę w celu spełnienia wymagań powiadamiania firmy Microsoft o tym, że Korzyść użycia hybrydowego platformy Azure został włączony w miejscu zarządzania formularzami rejestracji licencjonowania dla każdego zasobu. 

Aby uzyskać więcej informacji na temat korzyści z używania dostawcy zasobów maszyny wirtualnej SQL, zobacz następujące wideo [channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) : 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Korzystanie z dostawcy zasobów maszyny wirtualnej SQL podczas samoinstalowania SQL Server na platformie Azure — wideo w witrynie Microsoft Channel 9"></iframe>


## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu dostawcy zasobów, potrzebne są następujące elementy: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- [Maszyna wirtualna SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu PowerShell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Zarejestruj przy użyciu dostawcy zasobów maszyny wirtualnej SQL
Jeśli na maszynie wirtualnej nie zainstalowano [rozszerzenia agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) , możesz zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL, określając tryb uproszczonego zarządzania SQL. 

Gdy wartość Lightweight jest określona podczas procesu rejestracji, dostawca zasobów maszyny wirtualnej SQL zainstaluje w [trybie uproszczonym](#change-management-modes) rozszerzenie SQL IaaS i zweryfikuje metadane wystąpienia SQL Server. nie spowoduje to ponownego uruchomienia usługi SQL Server. Przed zarejestrowaniem dostawcy zasobów maszyny wirtualnej SQL jako "PAYG" lub "— AHUB" musisz podać odpowiedni typ licencji SQL Server.

Zarejestrowanie się w trybie uproszczonym przy użyciu dostawcy zasobów maszyny wirtualnej SQL zapewni zgodność i umożliwi Elastyczne licencjonowanie, a także w miejscu SQL Server aktualizacji wersji. Wystąpienia klastra trybu failover i wdrożenia z obsługą wiele wystąpień mogą być rejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL tylko w trybie uproszczonym. W dowolnym momencie można [przeprowadzić uaktualnienie](#change-management-modes) do trybu pełnego zarządzania, ale spowoduje to ponowne uruchomienie usługi SQL Server. 


# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
Użyj poniższego fragmentu kodu, aby zarejestrować się w dostawcy zasobów maszyny wirtualnej SQL, SQL Server Jeśli rozszerzenie IaaS jest już zainstalowane na maszynie wirtualnej. Musisz podać typ licencji SQL Server, która ma być zarejestrowana u dostawcy zasobów maszyny wirtualnej SQL: płatność zgodnie z rzeczywistym użyciem (`PAYG`) lub Korzyść użycia hybrydowego platformy Azure (`AHUB`). 

Zarejestruj maszynę wirtualną SQL Server przy użyciu następującego fragmentu kodu programu PowerShell:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

W przypadku wersji płatnych (Enterprise lub standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

W przypadku wersji bezpłatnych (Developer, Web i Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Jeśli rozszerzenie SQL IaaS zostało zainstalowane na maszynie wirtualnej ręcznie, możesz zarejestrować się w trybie pełnym przy użyciu dostawcy zasobów maszyny wirtualnej SQL, tworząc zasób metadanych typu Microsoft. SqlVirtualMachine/SqlVirtualMachines. Poniżej znajduje się fragment kodu do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL, jeśli rozszerzenie SQL IaaS jest już zainstalowane na maszynie wirtualnej. Musisz podać typ licencji SQL Server pożądany jako "PAYG" lub "— AHUB". Aby zarejestrować się w trybie pełnego zarządzania, użyj następującego polecenia programu PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Rejestrowanie SQL Server 2008 lub 2008 R2 na maszynach wirtualnych z systemem Windows Server 2008

SQL Server 2008 i 2008 R2 zainstalowane w systemie Windows Server 2008 mogą być zarejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL w [trybie No-Agent](#change-management-modes). Ta opcja zapewnia zgodność i umożliwia monitorowanie SQL Server maszyny wirtualnej w Azure Portal z ograniczoną funkcjonalnością.

Poniższa tabela zawiera szczegółowe informacje na temat akceptowalnych wartości parametrów dostarczonych podczas rejestracji:

| Parametr | Akceptowalne wartości                                 |
| :------------------| :--------------------------------------- |
| **sqllicensetype** | `AHUB` lub `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` lub `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Aby zarejestrować wystąpienie SQL Server 2008 lub 2008 R2 w wystąpieniu systemu Windows Server 2008, użyj następującego fragmentu kodu środowiska PowerShell lub polecenia AZ CLI:  

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Sprawdź stan rejestracji
Możesz sprawdzić, czy maszyna wirtualna SQL Server została już zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 
1. Przejdź do [SQL Server maszyn wirtualnych](virtual-machines-windows-sql-manage-portal.md).
1. Z listy wybierz maszynę wirtualną z SQL Server. Jeśli maszyna wirtualna w SQL Server nie jest wymieniona w tym miejscu, prawdopodobnie nie została zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL. 
1. Wyświetl wartość w obszarze **stan**. Jeśli **stan** ma wartość **powodzenie**, maszyna wirtualna SQL Server została pomyślnie zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL. 

![Weryfikowanie stanu przy użyciu rejestracji jednostki UZALEŻNIONej SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Wiersz polecenia

Sprawdź bieżące SQL Server stanu rejestracji maszyny wirtualnej za pomocą polecenia AZ CLI lub PowerShell. Jeśli rejestracja zakończyła się pomyślnie, `ProvisioningState` pokaże `Succeeded`. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Błąd oznacza, że maszyna wirtualna SQL Server nie została zarejestrowana u dostawcy zasobów. 

## <a name="change-management-modes"></a>Zmień tryby zarządzania

Istnieją trzy bezpłatne tryby zarządzania dla rozszerzenia SQL Server IaaS: 

- Tryb **pełny** zapewnia wszystkie funkcje, ale wymaga ponownego uruchomienia uprawnień SQL Server i administratora systemu. Jest to opcja instalowana domyślnie. Służy do zarządzania maszyną wirtualną SQL Server przy użyciu jednego wystąpienia. Tryb pełny instaluje dwie usługi systemu Windows, które mają minimalny wpływ na pamięć i procesor CPU — można je monitorować za pomocą Menedżera zadań. Korzystanie z trybu pełnego zarządzania nie wiąże się z żadnymi kosztami. 

- **Lekkie** nie wymaga ponownego uruchomienia SQL Server, ale obsługuje tylko Zmienianie typu licencji i wydania SQL Server. Użyj tej opcji dla SQL Server maszyn wirtualnych z wieloma wystąpieniami lub udziału w wystąpieniu klastra trybu failover (FCI). Użycie trybu uproszczonego nie ma wpływu na pamięć ani procesor CPU. Korzystanie z uproszczonego trybu zarządzania nie wiąże się z żadnymi kosztami. 

- **Noagent** jest przeznaczony dla SQL Server 2008 i SQL Server 2008 R2 zainstalowanych w systemie Windows Server 2008. Użycie trybu noagent nie ma wpływu na pamięć ani procesor CPU. Nie jest dostępny żaden koszt związany z korzystaniem z trybu zarządzania bez agenta. 

Bieżący tryb SQL Server agenta IaaS można wyświetlić za pomocą programu PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server maszyny wirtualne, na których zainstalowano *uproszczone* rozszerzenie IaaS, mogą uaktualnić tryb do _pełnej_ wersji przy użyciu Azure Portal. SQL Server maszyny wirtualne w trybie _bez agenta_ można uaktualnić do wersji _pełnej_ po uaktualnieniu systemu operacyjnego do wersji Windows 2008 R2 lub nowszej. Nie jest możliwe przeprowadzenie obniżenia — w tym celu należy całkowicie odinstalować rozszerzenie SQL IaaS i zainstalować je ponownie. 

Aby uaktualnić tryb agenta do pełnej: 


### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Przejdź do zasobu [maszyny wirtualnej SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Wybierz swoją SQL Server maszynę wirtualną, a następnie wybierz pozycję **Przegląd**. 
1. W przypadku SQL Server maszyn wirtualnych z trybem noagent lub Lightweight IaaS wybierz opcję **jedyne typy licencji i aktualizacje wersji są dostępne w komunikacie rozszerzenia SQL IaaS** .

   ![Wybory dotyczące zmiany trybu z portalu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Zaznacz pole wyboru **Zgadzam się na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej** , a następnie wybierz pozycję **Potwierdź** , aby uaktualnić tryb IaaS do pełnego. 

    ![Pole wyboru, aby wyrazić zgodę na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Wiersz polecenia

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Uruchom następujący fragment kodu polecenia AZ CLI:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

Uruchom następujący fragment kodu programu PowerShell:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```
---

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Rejestrowanie dostawcy zasobów maszyny wirtualnej SQL z subskrypcją 

Aby zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL, należy zarejestrować dostawcę zasobów w ramach subskrypcji. Można to zrobić za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Otwórz Azure Portal i przejdź do pozycji **wszystkie usługi**. 
1. Przejdź do pozycji **subskrypcje** i wybierz swoją subskrypcję.  
1. Na stronie **subskrypcje** przejdź do pozycji **dostawcy zasobów**. 
1. Wprowadź **SQL** w filtrze, aby wyświetlić dostawców zasobów związanych z programem SQL. 
1. Wybierz pozycję **zarejestruj**, **zarejestruj ponownie**lub **Wyrejestruj** dostawcę **Microsoft. SqlVirtualMachine** , w zależności od żądanej akcji. 

![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Wiersz polecenia

Zarejestruj dostawcę zasobów maszyny wirtualnej SQL w ramach subskrypcji platformy Azure przy użyciu polecenia AZ CLI lub PowerShell. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Poniższy fragment kodu spowoduje zarejestrowanie dostawcy zasobów maszyny wirtualnej SQL w ramach subskrypcji platformy Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Uwagi

- Dostawca zasobów maszyny wirtualnej SQL obsługuje tylko SQL Server maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager. SQL Server maszyny wirtualne wdrożone za pośrednictwem modelu klasycznego nie są obsługiwane. 
- Dostawca zasobów maszyny wirtualnej SQL obsługuje tylko SQL Server maszyny wirtualne wdrożone w chmurze publicznej. Wdrożenia w chmurze prywatnej lub publicznej nie są obsługiwane. 
 

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**Czy należy zarejestrować moją SQL Serverą maszynę wirtualną z poziomu obrazu SQL Server w witrynie Azure Marketplace?**

Nie. Firma Microsoft automatycznie rejestruje maszyny wirtualne obsługiwane przez obrazy SQL Server w portalu Azure Marketplace. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL jest wymagana tylko wtedy, gdy maszyna wirtualna *nie* została zainicjowana z poziomu obrazów SQL Server w portalu Azure Marketplace i SQL Server została zainstalowana samoobsługowo.

**Czy dostawca zasobów maszyny wirtualnej SQL jest dostępny dla wszystkich klientów?** 

Tak. Klienci powinni rejestrować SQL Server maszyny wirtualne przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli nie korzystają z obrazu SQL Server z witryny Azure Marketplace, a zamiast tego są instalowane samodzielnie SQL Server lub do własnego wirtualnego dysku twardego. Maszyny wirtualne należące do wszystkich typów subskrypcji (Direct, Umowa Enterprise i dostawca rozwiązań w chmurze) mogą być rejestrowane przez dostawcę zasobów maszyny wirtualnej SQL.

**Czy należy zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL, jeśli SQL Server na maszynie wirtualnej SQL Server jest już zainstalowane rozszerzenie IaaS?**

Jeśli maszyna wirtualna SQL Server jest samodzielna i nie została zainicjowana z poziomu obrazów SQL Server w portalu Azure Marketplace, należy zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL, nawet jeśli zainstalowano rozszerzenie SQL Server IaaS. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL tworzy nowy zasób typu Microsoft. SqlVirtualMachines. Zainstalowanie rozszerzenia IaaS SQL Server nie powoduje utworzenia tego zasobu.

**Jaki jest domyślny tryb zarządzania podczas rejestrowania się w dostawcy zasobów maszyny wirtualnej SQL?**

Domyślny tryb zarządzania podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL jest *pełny*. Jeśli właściwość Zarządzanie SQL Server nie jest ustawiona podczas rejestrowania u dostawcy zasobów maszyny wirtualnej SQL, tryb zostanie ustawiony jako pełny zarządzania. Posiadanie SQL Servergo rozszerzenia IaaS na maszynie wirtualnej jest wymaganiem wstępnym do rejestracji u dostawcy zasobów maszyny wirtualnej SQL w trybie pełnego zarządzania.

**Jakie wymagania wstępne należy zarejestrować u dostawcy zasobów maszyny wirtualnej SQL?**

Nie ma wymagań wstępnych do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL w trybie uproszczonym lub bez agenta. Wymagania wstępne dotyczące rejestrowania się w ramach dostawcy zasobów maszyny wirtualnej SQL w trybie pełnym SQL Server mają zainstalowane rozszerzenie IaaS na maszynie wirtualnej.

**Czy można zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL, jeśli na maszynie wirtualnej nie zainstalowano rozszerzenia SQL Server IaaS?**

Tak, możesz zarejestrować się w trybie uproszczonego zarządzania przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli na maszynie wirtualnej nie zainstalowano rozszerzenia IaaS SQL Server. W trybie uproszczonym dostawca zasobów maszyny wirtualnej SQL będzie używać aplikacji konsolowej do weryfikowania wersji i wydania wystąpienia SQL Server. 

Domyślny tryb zarządzania SQL podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL jest _pełny_. Jeśli właściwość SQL Management nie jest ustawiona podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL, tryb zostanie ustawiony jako pełny zarządzanie. Posiadanie rozszerzenia SQL IaaS zainstalowane na maszynie wirtualnej jest wymaganiem wstępnym do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL w trybie pełnego zarządzania.

**Czy w ramach dostawcy zasobów maszyny wirtualnej SQL zostanie zainstalowany agent na mojej maszynie wirtualnej?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL spowoduje utworzenie nowego zasobu metadanych. Nie zainstaluje agenta na maszynie wirtualnej.

SQL Server rozszerzenia IaaS jest wymagany tylko w celu umożliwienia pełnego zarządzania. Uaktualnianie trybu zarządzania z wersji uproszczonej do pełnej spowoduje zainstalowanie rozszerzenia SQL Server IaaS i ponowne uruchomienie SQL Server.

**Czy zarejestrowano SQL Server ponownego uruchomienia dostawcy zasobów maszyny wirtualnej SQL Server na mojej maszynie wirtualnej?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL spowoduje utworzenie nowego zasobu metadanych. Nie można ponownie uruchomić SQL Server na maszynie wirtualnej. 

Ponowne uruchomienie SQL Server jest konieczne tylko w celu zainstalowania rozszerzenia IaaS SQL Server. Do zapewnienia pełnej możliwości zarządzania jest wymagana SQL Server rozszerzenie IaaS. Uaktualnianie trybu zarządzania z wersji uproszczonej do pełnej spowoduje zainstalowanie rozszerzenia SQL Server IaaS i ponowne uruchomienie SQL Server.

**Jaka jest różnica między trybami zarządzania uproszczonego i bez agenta podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL?** 

Tryb zarządzania bez agenta jest dostępny tylko dla SQL Server 2008 i SQL Server 2008 R2 w systemie Windows Server 2008. Jest to jedyny dostępny tryb zarządzania dla tych wersji. W przypadku wszystkich innych wersji SQL Server dwa dostępne tryby zarządzania są lekkie i pełne. 

Tryb No-Agent wymaga, aby właściwości SQL Server wersji i wydania zostały ustawione przez klienta. Tryb uproszczony wysyła zapytanie do maszyny wirtualnej w celu znalezienia wersji i wydania wystąpienia SQL Server.

**Czy można zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym lub bez agenta za pomocą interfejsu wiersza polecenia platformy Azure?**

Nie. Właściwość tryb zarządzania jest dostępna tylko w przypadku rejestracji z dostawcą zasobów maszyny wirtualnej SQL przy użyciu Azure PowerShell. Interfejs wiersza polecenia platformy Azure nie obsługuje ustawiania właściwości SQL Server możliwości zarządzania. Zawsze rejestruje się przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie domyślnym, który jest w pełni zarządzany.

**Czy można zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL bez określenia typu licencji SQL Server?**

Nie. Typ licencji SQL Server nie jest właściwością opcjonalną podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL. Musisz ustawić typ licencji SQL Server jako płatność zgodnie z rzeczywistym użyciem lub Korzyść użycia hybrydowego platformy Azure podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL we wszystkich trybach zarządzania (bez agenta, Lightweight i Full) przy użyciu interfejsu wiersza polecenia platformy Azure i programu PowerShell.

**Czy można uaktualnić rozszerzenie SQL Server IaaS z trybu bez agenta do trybu pełnego?**

Nie. Uaktualnianie trybu zarządzania do wersji pełnej lub lekkiej nie jest dostępne w trybie bez agenta. Jest to ograniczenie techniczne systemu Windows Server 2008.

**Czy można uaktualnić rozszerzenie SQL Server IaaS z trybu uproszczonego do trybu pełnego?**

Tak. Uaktualnianie trybu zarządzania z lekkich do pełnych jest obsługiwane za pośrednictwem programu PowerShell lub Azure Portal. Wymaga ponownego uruchomienia SQL Server.

**Czy mogę obniżyć SQL Server rozszerzenie IaaS z trybu pełnego do poziomu bez agenta lub Lightweight Management Mode?**

Nie. Obniżenie poziomu trybu zarządzania rozszerzeniami SQL Server IaaS nie jest obsługiwane. Trybu zarządzania nie można zmienić z trybu pełnego na lekki lub bez agenta i nie można go zmienić z trybu uproszczonego na tryb bez agenta. 

Aby zmienić tryb zarządzania z pełnego zarządzania, Usuń SQL Server rozszerzenie IaaS. Następnie Porzuć zasób Microsoft. SqlVirtualMachine i ponownie zarejestruj maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL.

**Czy można zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL z Azure Portal?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL nie jest dostępna w Azure Portal. Rejestrowanie przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie pełnego zarządzania jest obsługiwane za pomocą interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonego lub bez agenta zarządzania jest obsługiwana tylko przez interfejsy API Azure PowerShell.

**Czy można zarejestrować maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL przed zainstalowaniem SQL Server?**

Nie. Maszyna wirtualna powinna mieć co najmniej jedno wystąpienie SQL Server, aby można było zarejestrować się w dostawcy zasobów maszyny wirtualnej SQL. Jeśli na maszynie wirtualnej nie ma SQL Server wystąpienia, nowy zasób Microsoft. SqlVirtualMachine będzie w stanie niepowodzenia.

**Czy można zarejestrować maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli istnieje wiele wystąpień SQL Server?**

Tak. Dostawca zasobów maszyny wirtualnej SQL będzie rejestrował tylko jedno wystąpienie SQL Server. Dostawca zasobów maszyny wirtualnej SQL zarejestruje domyślne wystąpienie SQL Server w przypadku wielu wystąpień. Jeśli nie ma wystąpienia domyślnego, obsługiwane jest tylko rejestrowanie w trybie uproszczonym. Aby można było uaktualnić tryb uproszczony do pełnego zarządzania, należy podać domyślne wystąpienie SQL Server lub maszyna wirtualna powinna mieć tylko jedno nazwane wystąpienie SQL Server.

**Czy można zarejestrować SQL Server wystąpienie klastra trybu failover z dostawcą zasobów maszyny wirtualnej SQL?**

Tak. SQL Server wystąpienia klastra trybu failover na maszynie wirtualnej platformy Azure można zarejestrować w trybie uproszczonym przy użyciu dostawcy zasobów maszyny wirtualnej SQL. Niemniej jednak SQL Server wystąpienia klastra trybu failover nie można uaktualnić do trybu pełnego zarządzania.

**Czy można zarejestrować moją maszynę wirtualną u dostawcy zasobów maszyny wirtualnej SQL, jeśli jest skonfigurowana zawsze włączona Grupa dostępności?**

Tak. Nie ma żadnych ograniczeń dotyczących rejestrowania wystąpienia SQL Server na maszynie wirtualnej platformy Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli bierzesz udział w konfiguracji grupy dostępności zawsze włączone.

**Jaki jest koszt rejestrowania w ramach dostawcy zasobów maszyny wirtualnej SQL, czy z uaktualnieniem do trybu pełnego zarządzania?**
Brak. Nie jest naliczana opłata związana z rejestracją u dostawcy zasobów maszyny wirtualnej SQL lub z użyciem jednego z trzech trybów zarządzania. Zarządzanie maszyną wirtualną SQL Server przy użyciu dostawcy zasobów jest całkowicie bezpłatne. 

**Jaki jest wpływ na wydajność korzystania z różnych trybów zarządzania?**
Nie ma to wpływu na użycie trybu *noagent* i *uproszczonego* zarządzania. W przypadku korzystania z trybu *pełnego* zarządzania z dwóch usług, które są zainstalowane w systemie operacyjnym, ma minimalny wpływ. Można je monitorować za pomocą Menedżera zadań. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
