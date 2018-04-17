---
title: Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Zarządzanie Azure Analysis Services przy użyciu programu PowerShell.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: reference
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c7315835bca446c4cae592f4bdd58a733b203655
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell

W tym artykule opisano poleceń cmdlet programu PowerShell używane w celu wykonania zadania zarządzania bazy danych i serwera usług Azure Analysis Services. 

Zadania zarządzania serwerem, takie jak tworzenie lub usuwanie serwera, wstrzymywania lub wznawiania operacji serwera lub zmiana poziomu usług (warstwy), użyj polecenia cmdlet usługi Azure Resource Manager (AzureRM). Innych zadań związanych z zarządzaniem baz danych, takich jak dodawanie lub usuwanie członków roli przetwarzania lub podziału na partycje za pomocą poleceń cmdlet zawartych w tym samym module SqlServer jako SQL Server Analysis Services.

## <a name="permissions"></a>Uprawnienia
Większość zadań programu PowerShell wymaga uprawnień administratora na serwerze usług Analysis Services, którym zarządzasz. Zaplanowane zadania programu PowerShell są operacje instalacji nienadzorowanej. Konta, na którym działa planista musi mieć uprawnienia administratora na serwerze usług Analysis Services. 

Dla operacji serwera za pomocą poleceń cmdlet AzureRm, konta lub konta, na którym działa harmonogramu musi również należeć do roli właściciela zasobu w [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). 

## <a name="server-operations"></a>Operacji serwera 
Polecenia cmdlet systemu Azure Analysis Services są uwzględnione w [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) składnika modułu. Aby zainstalować AzureRM moduły poleceń cmdlet, zobacz [poleceń cmdlet usługi Azure Resource Manager](/powershell/azure/overview) w galerii programu PowerShell.

|Polecenie cmdlet|Opis| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|Dodaje uwierzytelnionego konto do użycia dla żądań polecenia cmdlet serwera usług Azure Analysis Services.| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Pobiera Szczegóły wystąpienia serwera.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Tworzy wystąpienie serwera.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Usuwa wystąpienie serwera.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Uruchamia ponownie wystąpienie serwera usług Analysis Services w środowisku aktualnie zalogowany. określony w poleceniu Add-AzureAnalysisServicesAccount.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Wznawia wystąpienia serwera.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Wstrzymuje wystąpienia serwera.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modyfikuje wystąpienia serwera.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Testy istnienia wystąpienia serwera.| 

## <a name="database-operations"></a>Operacje bazy danych

Azure operacje bazy danych usług Analysis Services używać tego samego [SqlServer](https://www.powershellgallery.com/packages/SqlServer) modułu jako SQL Server Analysis Services. Jednak nie wszystkie polecenia cmdlet są obsługiwane dla usług Azure Analysis Services. 

Moduł SqlServer udostępnia polecenia cmdlet do bazy danych specyficznych dla zadań zarządzania, a także cmdlet Invoke-ASCmd ogólnego przeznaczenia akceptuje zapytań tabelarycznych modelu skryptów języka (TMSL) lub skryptu. Następujące polecenia cmdlet w SqlServer module są obsługiwane dla usług Azure Analysis Services.

  
|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Dodaj RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Dodaj członka do roli bazy danych.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Wykonywanie kopii zapasowej bazy danych usług Analysis Services.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Usuwanie członka z roli bazy danych.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Wykonanie skryptu TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Przetwarzanie bazy danych.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Przetwarzanie partycji.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Przetwarza tabelę.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Scalania partycji.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Przywróć bazę danych usług Analysis Services.| 
  

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł PowerShell programu SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobierz narzędzia SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Moduł SqlServer w galerii programu PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabelaryczne modelu programowania dla 1200 poziom zgodności lub nowszym](https://msdn.microsoft.com/library/mt712541.aspx)
