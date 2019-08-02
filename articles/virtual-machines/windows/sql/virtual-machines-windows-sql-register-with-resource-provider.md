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
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305865"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Zarejestruj SQL Server maszynę wirtualną na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL

W tym artykule opisano sposób rejestrowania maszyny wirtualnej platformy Azure SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL. 

Wdrażanie obrazu z witryny Marketplace SQL Server VM za pomocą Azure Portal automatycznie rejestruje maszynę wirtualną SQL Server przy użyciu dostawcy zasobów. Jeśli jednak zdecydujesz się na samodzielne zainstalowanie SQL Server na maszynie wirtualnej platformy Azure zamiast wybierania obrazu z portalu Azure Marketplace, należy zarejestrować SQL Serverą maszynę wirtualną za pomocą dostawcy zasobów dzisiaj dla:

-  **Zgodność — zgodnie** z postanowieniami produktu firmy Microsoft klienci korzystający z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) muszą wskazać firmie Microsoft w przypadku korzystania z korzyść użycia hybrydowego platformy Azure i do tego celu, muszą zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL. 

-  **Zalety funkcji** — Rejestracja SQL SERVEREJ maszyny wirtualnej przy użyciu dostawcy zasobów odblokowuje [Automatyczne stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md), [Automatyczne tworzenie kopii zapasowej](virtual-machines-windows-sql-automated-backup-v2.md), monitorowanie i możliwości zarządzania, a także elastyczność i [wydanie](virtual-machines-windows-sql-change-edition.md) [licencji](virtual-machines-windows-sql-ahb.md) . Wcześniej były one dostępne tylko w przypadku SQL Server obrazów maszyn wirtualnych z poziomu portalu Azure Marketplace.

Samodzielna instalacja SQL Server na maszynie wirtualnej platformy Azure lub inicjowanie obsługi maszyny wirtualnej platformy Azure z niestandardowego dysku VHD z systemem SQL Server jest zgodne przez Korzyść użycia hybrydowego platformy Azure dla SQL Server ze stanem wskazywanym przez klientów na korzystanie z firmy Microsoft przez rejestrację przy użyciu zasobu maszyny wirtualnej SQL dostawcy. 

Aby korzystać z dostawcy zasobów maszyny wirtualnej SQL, należy również zarejestrować dostawcę zasobów maszyny wirtualnej SQL w ramach subskrypcji. Można to osiągnąć przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure i programu PowerShell. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu dostawcy zasobów, potrzebne są następujące elementy: 

- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- [Maszyna wirtualna SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i program [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Zarejestruj przy użyciu dostawcy zasobów maszyny wirtualnej SQL
Jeśli [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) jest już zainstalowane na maszynie wirtualnej, rejestrowanie przy użyciu dostawcy zasobów maszyny wirtualnej SQL to po prostu utworzenie zasobu metadanych typu Microsoft. SqlVirtualMachine/SqlVirtualMachines. Poniżej znajduje się fragment kodu do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL, jeśli rozszerzenie SQL IaaS jest już zainstalowane na maszynie wirtualnej. Przed zarejestrowaniem dostawcy zasobów maszyny wirtualnej SQL jako "PAYG" lub "— AHUB" musisz podać odpowiedni typ licencji SQL Server. 

Zarejestruj SQL Server maszynę wirtualną przy użyciu programu PowerShell, używając następującego fragmentu kodu:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Jeśli rozszerzenie SQL IaaS nie jest zainstalowane na maszynie wirtualnej, możesz zarejestrować się przy użyciu dostawcy zasobów maszyny wirtualnej SQL, określając tryb uproszczonego zarządzania SQL. W trybie uproszczonego zarządzania SQL dostawca zasobów maszyny wirtualnej SQL zainstaluje rozszerzenie SQL IaaS w [trybie uproszczonym](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) i zweryfikuje metadane wystąpienia SQL Server. nie spowoduje to ponownego uruchomienia usługi SQL Server. Przed zarejestrowaniem dostawcy zasobów maszyny wirtualnej SQL jako "PAYG" lub "— AHUB" musisz podać odpowiedni typ licencji SQL Server. 

Zarejestruj SQL Server maszynę wirtualną w trybie uproszczonego zarządzania SQL przy użyciu programu PowerShell, używając następującego fragmentu kodu:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

Zarejestrowanie się w [trybie uproszczonym](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) przy użyciu dostawcy zasobów maszyny wirtualnej SQL zapewni zgodność i umożliwi Elastyczne licencjonowanie, a także w miejscu SQL Server aktualizacji wersji. Wystąpienia klastra trybu failover i wdrożenia z obsługą wiele wystąpień mogą być rejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL tylko w trybie uproszczonym. Można postępować zgodnie z instrukcjami znajdującymi się w Azure Portal, aby przeprowadzić uaktualnienie do [trybu w trybie pełnym](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) i włączyć zestaw funkcji kompleksowego zarządzania z użyciem SQL Server ponownego uruchamiania w dowolnym momencie. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Rejestrowanie SQL Server 2008/R2 na maszynach wirtualnych z systemem Windows Server 2008

SQL Server 2008 i 2008 R2 zainstalowane w systemie Windows Server 2008 można zarejestrować w trybie noagent przy użyciu zasobu maszyny [](virtual-machines-windows-sql-server-agent-extension.md) wirtualnej SQL. Ta opcja zapewnia zgodność i umożliwia monitorowanie SQL Server maszyny wirtualnej w Azure Portal z ograniczoną funkcjonalnością.

Poniższa tabela zawiera szczegółowe informacje na temat akceptowalnych wartości parametrów dostarczonych podczas rejestracji:

| Parametr | Akceptowalne wartości                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`lub`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` lub `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Aby zarejestrować SQL Server 2008 lub 2008 R2 w wystąpieniu systemu Windows Server 2008, użyj następującego fragmentu kodu programu PowerShell:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Sprawdź stan rejestracji
Możesz sprawdzić, czy SQL Server został już zarejestrowany u dostawcy zasobów maszyny wirtualnej SQL przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

### <a name="azure-portal"></a>Azure Portal 
Aby sprawdzić stan rejestracji przy użyciu Azure Portal, wykonaj następujące czynności.

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do swojej [maszyny wirtualnej SQL](virtual-machines-windows-sql-manage-portal.md).
1. Z listy wybierz maszynę wirtualną z SQL Server. Jeśli Twoja maszyna wirtualna w SQL Server nie jest wymieniona w tym miejscu, prawdopodobnie SQL Server maszyna wirtualna nie została zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL. 
1. Wyświetl wartość w obszarze `Status`. Jeśli `Status = Succeeded`usługa SQL Server VM została pomyślnie zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL. 

    ![Weryfikowanie stanu przy użyciu rejestracji jednostki UZALEŻNIONej SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>AZ CLI

Sprawdź bieżący stan rejestracji maszyny wirtualnej SQL Server za pomocą następującego polecenia AZ CLI. `ProvisioningState`będzie wyświetlana `Succeeded` , jeśli rejestracja zakończyła się pomyślnie. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Sprawdź bieżące SQL Server stanu rejestracji maszyny wirtualnej za pomocą następującego polecenia cmdlet programu PowerShell. `ProvisioningState`będzie wyświetlana `Succeeded` , jeśli rejestracja zakończyła się pomyślnie. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Błąd oznacza, że maszyna wirtualna SQL Server nie została zarejestrowana u dostawcy zasobów. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Zarejestruj dostawcę zasobów maszyny wirtualnej SQL z subskrypcją 

Aby zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL, należy zarejestrować dostawcę zasobów w ramach subskrypcji. Można to zrobić za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="azure-portal"></a>Azure Portal

Poniższe kroki spowodują zarejestrowanie dostawcy zasobów maszyny wirtualnej SQL w ramach subskrypcji platformy Azure przy użyciu Azure Portal. 

1. Otwórz Azure Portal i przejdź do **wszystkich usług**. 
1. Przejdź do obszaru **subskrypcje** i wybierz swoją subskrypcję.  
1. Na stronie **subskrypcje** przejdź do **dostawcy zasobów**. 
1. Wpisz `sql` wartość w polu Filtr, aby wyświetlić dostawców zasobów związanych z programem SQL. 
1. Wybierz opcję *zarejestruj*, *zarejestruj*lub wyrejestruj ** dla dostawcy **Microsoft. SqlVirtualMachine** w zależności od żądanej akcji. 

   ![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>AZ CLI
Poniższy fragment kodu spowoduje zarejestrowanie dostawcy zasobów maszyny wirtualnej SQL w ramach subskrypcji platformy Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

Poniższy fragment kodu programu PowerShell spowoduje zarejestrowanie dostawcy zasobów maszyny wirtualnej SQL w ramach subskrypcji platformy Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Uwagi

 - Dostawca zasobów maszyny wirtualnej SQL obsługuje tylko SQL Server maszyny wirtualne wdrożone przy użyciu "Menedżer zasobów". SQL Server maszyny wirtualne wdrożone przy użyciu "modelu klasycznego" nie są obsługiwane. 
 - Dostawca zasobów maszyny wirtualnej SQL obsługuje tylko SQL Server maszyny wirtualne wdrożone w chmurze publicznej. Wdrożenia w chmurze prywatnej lub instytucji rządowych nie są obsługiwane. 
 
## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**Czy należy zarejestrować moją SQL Serverą maszynę wirtualną z poziomu obrazu SQL w witrynie Azure Marketplace?**

Nie. Firma Microsoft rejestruje maszyny wirtualne obsługiwane przez obrazy SQL w portalu Azure Marketplace. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL jest wymagana tylko wtedy, gdy maszyna wirtualna nie została zainicjowana z obrazów SQL w portalu Azure Marketplace, a SQL Server była samoinstalowany.

**Czy dostawca zasobów maszyny wirtualnej SQL jest dostępny dla wszystkich klientów?** 

Tak. Klienci powinni zarejestrować swoją SQL Serverą maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli nie korzystali z obrazu SQL Server z portalu Azure Marketplace i samodzielnego instalowania SQL Server lub do przenoszenia niestandardowego wirtualnego dysku twardego. Maszyny wirtualne należące do wszystkich typów subskrypcji (Direct, EA i CSP) mogą być rejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL.

**Czy należy zarejestrować się przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli na mojej maszynie wirtualnej SQL Server jest już zainstalowane rozszerzenie SQL IaaS?**

Jeśli maszyna wirtualna SQL Server jest samodzielna, nie jest obsługiwana przez obrazy SQL w portalu Azure Marketplace, należy zarejestrować się przy użyciu dostawcy zasobów maszyny wirtualnej SQL, nawet jeśli zostało zainstalowane rozszerzenie SQL IaaS. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL tworzy nowy zasób typu Microsoft. SqlVirtualMachines. Zainstalowanie rozszerzenia SQL IaaS nie powoduje utworzenia tego zasobu.

**Co to jest domyślny tryb zarządzania SQL podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL?**

Domyślny tryb zarządzania SQL podczas rejestrowania z usługą RP maszyny wirtualnej SQL jest _pełny_. Jeśli właściwość SQL Management nie jest ustawiona podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL, tryb zostanie ustawiony jako pełny zarządzanie. Posiadanie rozszerzenia SQL IaaS zainstalowane na maszynie wirtualnej jest wymaganiem wstępnym do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL w trybie pełnego zarządzania.

**Jakie są wymagania wstępne, które należy zarejestrować u dostawcy zasobów maszyny wirtualnej SQL?**

Nie ma wymagań wstępnych do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL w trybie uproszczonym lub bez agenta. Wymagania wstępne dotyczące rejestrowania się w ramach dostawcy zasobów maszyny wirtualnej SQL w trybie pełnym mają zainstalowane rozszerzenie SQL IaaS na maszynie wirtualnej.

**Czy można zarejestrować się przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli na maszynie wirtualnej nie zainstalowano rozszerzenia SQL IaaS?**

Tak, możesz zarejestrować się w trybie uproszczonego zarządzania przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli na maszynie wirtualnej nie zainstalowano rozszerzenia SQL IaaS. W trybie uproszczonym dostawca programu SQL VM zasobów będzie używać aplikacji konsolowej do weryfikowania wersji i wydania wystąpienia programu SQL. Aplikacja konsolowa zostanie zamknięta po sprawdzeniu, czy na maszynie wirtualnej jest uruchomione co najmniej jedno wystąpienie serwera SQL. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym nie spowoduje ponownego uruchomienia SQL Server i nie spowoduje utworzenia agenta na maszynie wirtualnej.

**Czy zarejestrowano agenta w ramach dostawcy zasobów maszyny wirtualnej SQL?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL spowoduje utworzenie nowego zasobu metadanych i nie spowoduje instalacji agenta na maszynie wirtualnej. Rozszerzenie SQL IaaS jest wymagane tylko w celu zapewnienia pełnej możliwości zarządzania, więc uaktualnienie trybu zarządzania z uproszczonej do pełnego spowoduje zainstalowanie rozszerzenia SQL IaaS i ponowne uruchomienie SQL Server.

**Czy zarejestrowano dostawcę zasobów maszyny wirtualnej SQL SQL Server na mojej maszynie wirtualnej?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL spowoduje utworzenie nowego zasobu metadanych i nie spowoduje ponownego uruchomienia SQL Server na maszynie wirtualnej. Ponowne uruchomienie SQL Server jest konieczne tylko w celu zainstalowania rozszerzenia SQL IaaS. do zapewnienia pełnej możliwości zarządzania wymaga rozszerzenia SQL IaaS. Uaktualnianie trybu zarządzania z poziomu uproszczonego do pełnego spowoduje zainstalowanie rozszerzenia SQL IaaS i ponowne uruchomienie SQL Server.

**Jaka jest różnica między trybami zarządzania uproszczonego i bez agenta podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL?** 

Tryb zarządzania bez agenta jest dostępny tylko dla SQL Server 2008/R2 w systemie Windows Server 2008; jest to jedyny dostępny tryb zarządzania dla tych wersji. W przypadku wszystkich innych wersji SQL Server dostępne są dwa tryby zarządzania. Tryb No-Agent wymaga, aby właściwości SQL Server wersji i wydania zostały ustawione przez klienta. Tryb uproszczony wysyła zapytanie do maszyny wirtualnej w celu znalezienia wersji i wydania wystąpienia programu SQL.

**Czy można zarejestrować się przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie lekkim lub bez agenta za pomocą interfejsu wiersza polecenia platformy Azure?**

Nie. Właściwość trybu zarządzania SQL jest dostępna tylko podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL przy użyciu Azure PowerShell. Interfejs wiersza polecenia platformy Azure nie obsługuje ustawiania właściwości możliwości zarządzania SQL i zawsze rejestruje się w domyślnym trybie zarządzania przy użyciu dostawcy zasobów maszyny wirtualnej SQL.

**Czy można zarejestrować dostawcę zasobów maszyny wirtualnej SQL bez określenia typu licencji SQL?**

Nie. Typ licencji SQL nie jest właściwością opcjonalną podczas rejestrowania z usługą RP maszyny wirtualnej SQL. Musisz ustawić typ licencji SQL jako PAYG lub — AHUB podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL we wszystkich trybach zarządzania (bez agenta, Lightweight i Full) za pomocą polecenia AZ CLI i PowerShell.

**Czy można uaktualnić rozszerzenie SQL IaaS z trybu bez agenta do trybu pełnego?**

Nie. Uaktualnianie trybu zarządzania SQL do wersji pełnej lub lekkiej nie jest dostępne w trybie bez agenta. Jest to ograniczenie techniczne systemu Windows Server 2008.

**Czy można uaktualnić rozszerzenie SQL IaaS z trybu uproszczonego do trybu pełnego?**

Tak. Uaktualnianie trybu zarządzania SQL z uproszczonej do Full jest obsługiwane za pośrednictwem programu PowerShell lub Azure Portal; i wymaga ponownego uruchomienia SQL Server.

**Czy można obniżyć poziom rozszerzenia SQL IaaS z trybu pełnoekranowego do trybu zarządzania bez agenta lub uproszczonego?**

Nie. Starsza konfiguracja trybu zarządzania rozszerzeniami IaaS SQL nie jest obsługiwana. Trybu zarządzania SQL nie można zmienić na starszą wersję z trybu pełnego na lekki lub bez agenta; i nie można go zmienić z trybu lekkiego na tryb bez agenta. Aby zmienić tryb zarządzania z pełnego zarządzania; Usuń rozszerzenie SQL IaaS; Porzuć zasób Micorsoft. SqlVirtualMachine i ponownie zarejestruj maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL.

**Czy można zarejestrować się przy użyciu dostawcy zasobów maszyny wirtualnej SQL z Azure Portal?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL nie jest dostępna w Azure Portal. Rejestrowanie przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie pełnego zarządzania jest obsługiwane za pomocą interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym lub bez agentów zarządzania jest obsługiwana tylko przez interfejsy API Azure PowerShell.

**Czy można zarejestrować maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL przed zainstalowaniem SQL Server?**

Nie. Maszyna wirtualna powinna mieć co najmniej jedno wystąpienie programu SQL, aby można było pomyślnie zarejestrować się w dostawcy zasobów maszyny wirtualnej SQL. Jeśli na maszynie wirtualnej nie ma wystąpienia SQL, nowy zasób pomocą. SqlVirtualMachine będzie w stanie niepowodzenia.

**Czy można zarejestrować maszynę wirtualną z zasobem maszyny wirtualnej SQL, jeśli istnieje wiele wystąpień SQL?**

Tak. Dostawca zasobów maszyny wirtualnej SQL będzie rejestrował tylko jedno wystąpienie programu SQL Server. Dostawca zasobów maszyny wirtualnej SQL zarejestruje domyślne wystąpienie programu SQL w przypadku wielu wystąpień. Jeśli nie ma wystąpienia domyślnego, obsługiwane jest tylko rejestrowanie w trybie uproszczonym. Aby można było uaktualnić tryb uproszczony do pełnego zarządzania, musi istnieć domyślne wystąpienie programu SQL Server lub maszyna wirtualna powinna mieć tylko jedno nazwane wystąpienie SQL.

**Czy można zarejestrować SQL Server wystąpienie klastra trybu failover z dostawcą zasobów maszyny wirtualnej SQL?**

Tak. Wystąpienia SQL FCI na maszynie wirtualnej platformy Azure mogą być zarejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym. Nie można jednak uaktualnić wystąpień programu SQL FCI do pełnego trybu zarządzania.

**Czy można zarejestrować maszynę wirtualną przy użyciu jednostki UZALEŻNIONej SQL maszyny wirtualnej, jeśli jest skonfigurowana zawsze włączona Grupa dostępności?**

Tak. Nie ma żadnych ograniczeń dotyczących rejestrowania wystąpienia SQL Server na maszynie wirtualnej platformy Azure z dostawcą zasobów maszyny wirtualnej SQL, jeśli uczestniczy w konfiguracji grupy dostępności zawsze włączone.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na maszynie wirtualnej z systemem Windows — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server wskazówki dotyczące cennika maszyn wirtualnych z systemem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server w informacjach o wersji maszyny wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
