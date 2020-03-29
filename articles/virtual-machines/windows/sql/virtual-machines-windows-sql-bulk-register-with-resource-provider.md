---
title: Zbiorcza rejestracja maszyn wirtualnych SQL na platformie Azure u dostawcy zasobów maszyny wirtualnej SQL | Dokumenty firmy Microsoft
description: Zbiorcze rejestrowanie maszyn wirtualnych programu SQL Server u dostawcy zasobów maszyny wirtualnej SQL w celu zwiększenia możliwości zarządzania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353883"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Zbiorczy rejestr maszyn wirtualnych SQL na platformie Azure u dostawcy zasobów maszyny wirtualnej SQL

W tym artykule opisano sposób zbiorczego rejestrowania maszyny wirtualnej programu SQL Server `Register-SqlVMs` (VM) na platformie Azure u dostawcy zasobów maszyny wirtualnej SQL przy użyciu polecenia cmdlet programu PowerShell.

Polecenie `Register-SqlVMs` cmdlet może służyć do rejestrowania wszystkich maszyn wirtualnych na danej liście subskrypcji, grup zasobów lub listy określonych maszyn wirtualnych. Polecenie cmdlet zarejestruje maszyny wirtualne w trybie _zarządzania w lekkim_ zarządzania, a następnie wygeneruje zarówno raport, jak [i plik dziennika.](#output-description) 

Proces rejestracji nie niesie ze sobą żadnego ryzyka, nie ma przestojów i nie uruchomi ponownie programu SQL Server ani maszyny wirtualnej. 

Aby uzyskać więcej informacji na temat dostawcy zasobów, zobacz [dostawca zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować maszynę wirtualną programu SQL Server u dostawcy zasobów, należy wykonać następujące czynności: 

- [Subskrypcja platformy Azure,](https://azure.microsoft.com/free/) która została [zarejestrowana u dostawcy zasobów](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) i zawiera niezarejestrowane maszyny wirtualne programu SQL Server. 
- Poświadczenia klienta używane do rejestrowania maszyn wirtualnych istnieją w dowolnej z następujących ról RBAC: **Współautor maszyny wirtualnej,** **Współautor**lub **Właściciel**. 
- Najnowsza wersja [programu Az PowerShell](/powershell/azure/new-azureps-module-az). 
- Najnowsza wersja [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Wprowadzenie

Przed kontynuowaniem należy najpierw utworzyć lokalną kopię skryptu, zaimportować go jako moduł programu PowerShell i połączyć się z platformą Azure. 

### <a name="create-script"></a>Tworzenie skryptu

Aby utworzyć skrypt, skopiuj [pełny skrypt](#full-script) z końca `RegisterSqlVMs.psm1`tego artykułu i zapisz go lokalnie jako . 

### <a name="import-script"></a>Importowanie skryptu

Po utworzeniu skryptu można go zaimportować jako moduł w terminalu programu Powershell. 

Otwórz administracyjny terminal programu PowerShell i `RegisterSqlVMs.psm1` przejdź do miejsca, w którym został zapisany plik. Następnie uruchom następujące polecenie cmdlet programu PowerShell, aby zaimportować skrypt jako moduł: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Użyj następującego polecenia cmdlet programu PowerShell, aby połączyć się z platformą Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Wszystkie maszyny wirtualne na liście subskrypcji 

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie maszyny wirtualne programu SQL Server na liście subskrypcji:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Przykładowe dane wyjściowe: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Wszystkie maszyny wirtualne w jednej subskrypcji

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie maszyny wirtualne programu SQL Server w jednej subskrypcji: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Wszystkie maszyny wirtualne w wielu grupach zasobów

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie maszyny wirtualne programu SQL Server w wielu grupach zasobów w ramach jednej subskrypcji:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Wszystkie maszyny wirtualne w grupie zasobów

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie maszyny wirtualne programu SQL Server w jednej grupie zasobów: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>Określone maszyny wirtualne w pojedynczej grupie zasobów

Użyj następującego polecenia cmdlet, aby zarejestrować określone maszyny wirtualne programu SQL Server w jednej grupie zasobów:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>Określona maszyna wirtualna

Użyj następującego polecenia cmdlet, aby zarejestrować określoną maszynę wirtualną programu SQL Server: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Opis wyjścia

Zarówno plik raportu, jak i `Register-SqlVMs` pliku dziennika jest generowany za każdym razem, gdy jest używane polecenie cmdlet. 

### <a name="report"></a>Raport

Raport jest generowany `.txt` jako `RegisterSqlVMScriptReport<Timestamp>.txt` plik o nazwie, w którym sygnatura czasowa jest godziną rozpoczęcia polecenia cmdlet. Raport zawiera następujące szczegóły:

| **Wartość wyjściowa** | **Opis** |
| :--------------  | :-------------- | 
| Liczba subskrypcji rejestracja nie powiodła się, ponieważ nie masz dostępu lub poświadczenia są niepoprawne | Zapewnia to liczbę i listę subskrypcji, które miały problemy z dostarczonym uwierzytelnianiem. Szczegółowy błąd można znaleźć w dzienniku, wyszukując identyfikator subskrypcji. | 
| Liczba subskrypcji, których nie można wypróbować, ponieważ nie są one zarejestrowane w RP | Ta sekcja zawiera liczbę i listę subskrypcji, które nie zostały zarejestrowane w dostawcy zasobów maszyny Wirtualnej SQL. |
| Znaleziono całkowitą liczbę znalezionych maszyn wirtualnych | Liczba maszyn wirtualnych, które zostały znalezione w zakresie parametrów przekazanych do polecenia cmdlet. | 
| Maszyny wirtualne już zarejestrowane | Liczba maszyn wirtualnych, które zostały pominięte, ponieważ zostały już zarejestrowane u dostawcy zasobów. |
| Liczba maszyn wirtualnych zarejestrowanych pomyślnie | Liczba maszyn wirtualnych, które zostały pomyślnie zarejestrowane po uruchomieniu polecenia cmdlet. Wyświetla listę zarejestrowanych maszyn `SubscriptionID, Resource Group, Virtual Machine`wirtualnych w formacie . | 
| Liczba maszyn wirtualnych nie została zarejestrowana z powodu błędu | Liczba maszyn wirtualnych, których rejestracja nie powiodła się z powodu błędu. Szczegóły błędu można znaleźć w pliku dziennika. | 
| Liczba maszyn wirtualnych pominiętych jako maszyna wirtualna lub agent podmuchu na maszynie wirtualnej nie jest uruchomiona | Liczba i lista maszyn wirtualnych, które nie mogą być zarejestrowane jako maszyna wirtualna lub agent gościa na maszynie wirtualnej nie były uruchomione. Można je ponowić po uruchomieniu maszyny wirtualnej lub agenta gościa. Szczegóły można znaleźć w pliku dziennika. |
| Liczba pominiętych maszyn wirtualnych, ponieważ nie są one uruchomione w programie SQL Server w systemie Windows | Liczba maszyn wirtualnych, które zostały pominięte, ponieważ nie są one uruchomione SQL Server lub nie są maszyną wirtualną systemu Windows. Maszyny wirtualne są wymienione `SubscriptionID, Resource Group, Virtual Machine`w formacie . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

Błędy są rejestrowane w pliku `VMsNotRegisteredDueToError<Timestamp>.log` dziennika o nazwie, gdzie sygnatura czasowa jest czas, kiedy skrypt rozpoczął. Jeśli błąd występuje na poziomie subskrypcji, dziennik zawiera oddzielony przecinkami Identyfikator subskrypcji i komunikat o błędzie. Jeśli błąd dotyczy rejestracji maszyny wirtualnej, dziennik zawiera identyfikator subskrypcji, nazwę grupy zasobów, nazwę maszyny wirtualnej, kod błędu i komunikat oddzielony przecinkami. 

## <a name="remarks"></a>Uwagi

Podczas rejestrowania maszyn wirtualnych programu SQL Server u dostawcy zasobów przy użyciu dostarczonego skryptu należy wziąć pod uwagę następujące kwestie:

- Rejestracja u dostawcy zasobów wymaga agenta gościa uruchomionego na maszynie wirtualnej programu SQL Server. Obrazy systemu Windows Server 2008 nie mają agenta gościa, więc te maszyny wirtualne zawiodą i muszą być zarejestrowane ręcznie przy użyciu [trybu zarządzania NoAgent](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).
- Istnieje logika ponawiania wbudowana, aby przezwyciężyć błędy przezroczyste. Jeśli maszyna wirtualna została pomyślnie zarejestrowana, jest to szybka operacja. Jeśli jednak rejestracja nie powiedzie się, każda maszyna wirtualna zostanie ponowiona.  W związku z tym należy dać dużo czasu, aby zakończyć proces rejestracji — chociaż rzeczywiste wymaganie czasu zależy od typu i liczby błędów. 

## <a name="full-script"></a>Pełny skrypt

Aby uzyskać pełny skrypt w usłudze GitHub, zobacz [zbiorcze rejestrowanie maszyn wirtualnych SQL w programie Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Skopiuj pełny `RegisterSqLVMs.psm1`skrypt i zapisz go jako .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
