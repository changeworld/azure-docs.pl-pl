---
title: Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Usługa Azure Analysis Services management przy użyciu programu PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9e7683883963db2cf1911405225fcdbf289de2bb
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187542"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell

W tym artykule opisano poleceń cmdlet programu PowerShell, używane do wykonywania serwer usług Azure Analysis Services i zadań zarządzania bazą danych. 

Zadania zarządzania serwerem, takich jak tworzenie lub usuwanie serwera, zawieszanie lub wznawianie operacji serwerowych lub zmiana poziomu usług (warstwa) za pomocą poleceń cmdlet usługi Azure Resource Manager (zasób) i poleceń cmdlet usług Analysis Services (serwer). Inne zadania do zarządzania bazami danych, takich jak dodawanie lub usuwanie członków ról, przetwarzanie lub partycjonowanie użyj poleceń cmdlet zawartych w tym samym modułem programu SQL Server Analysis Services.

## <a name="permissions"></a>Uprawnienia

Większość zadań programu PowerShell wymaga, że masz uprawnienia administratora na serwerze usług Analysis Services, którymi zarządzasz. Zaplanowane zadania w programie PowerShell to operacje instalacji nienadzorowanej. Konto lub jednostki, obsługujących harmonogram usługi musi mieć uprawnienia administratora na serwerze usług Analysis Services. 

W przypadku operacji serwera przy użyciu poleceń cmdlet usługi AzureRm swojego konta lub konta uruchamiania usługi scheduler należy także być członkiem roli właściciela dla zasobów w [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operacje zarządzania zasobów 

Moduł — [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Polecenie cmdlet|Opis| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Pobiera Szczegóły wystąpienia serwera.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Tworzy wystąpienie serwera.|   
|[Nowe AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Tworzy nowy konfiguracji zapory usług Analysis Services.|   
|[Nowe AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Tworzy nową regułę zapory usług Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Usuwa wystąpienie serwera.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Wznawia wystąpienie serwera.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Wstrzymuje wystąpienie serwera.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modyfikuje wystąpienie serwera.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Sprawdza obecność wystąpienia serwera.| 

## <a name="server-management-operations"></a>Operacje zarządzania serwerem

Moduł — [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Polecenie cmdlet|Opis| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Dodaje uwierzytelnione konto do użycia dla żądań polecenia cmdlet serwera usług Azure Analysis Services.| 
|[AzureAnalysisServicesInstance eksportu](/powershell/module/azurerm.analysisservices/export-azureanalysisservicesinstancelog)|Eksportuje dziennika z wystąpienia serwera usług Analysis Services w aktualnie zalogowanego w środowisku określony w poleceniu Add-AzureAnalysisServicesAccount|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Powoduje ponowne uruchomienie wystąpienia serwera usług Analysis Services w środowisku aktualnie zalogowany. określony w poleceniu Add-AzureAnalysisServicesAccount.|  
|[AzureAnalysisServicesInstance synchronizacji](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Synchronizuje określonej bazy danych w podanym wystąpieniu serwera usług Analysis Services do wszystkich wystąpień skalowalnego w poziomie zapytania w aktualnie zalogowanego w środowisku określony w poleceniu Add-AzureAnalysisServicesAccount|  

## <a name="database-operations"></a>Operacje bazy danych

Operacje bazy danych w usłudze Azure Analysis Services używać tego samego [SqlServer module](https://www.powershellgallery.com/packages/SqlServer) jak SQL Server Analysis Services. Jednak nie wszystkie polecenia cmdlet są obsługiwane w przypadku usług Azure Analysis Services. Aby dowiedzieć się więcej, zobacz [program SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

W module SqlServer udostępnia polecenia cmdlet do bazy danych specyficznych dla zadań zarządzania, a także ogólnego przeznaczenia polecenia cmdlet Invoke-ASCmd, które akceptuje zapytanie skryptów języka TMSL (Tabular Model) lub skrypt. Następujące polecenia cmdlet w SqlServer module są obsługiwane w przypadku usług Azure Analysis Services.

  
|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Dodaj RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Dodaj członka do roli bazy danych.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Tworzenie kopii zapasowej bazy danych usług Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Usuń członka z roli bazy danych.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Wykonywanie skryptów TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Przetwarzanie bazy danych.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Przetwarzanie partycji.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Przetwarza tabelę.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Scalania partycji.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Przywracanie bazy danych usług Analysis Services.| 
  

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł PowerShell programu SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobieranie programu SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Module SqlServer w galerii programu PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabelaryczny Model programowania dla zgodności poziomu 1200 i wyższym](https://msdn.microsoft.com/library/mt712541.aspx)
