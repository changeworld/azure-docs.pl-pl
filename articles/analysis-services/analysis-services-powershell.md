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
ms.openlocfilehash: 1f9c30f1c914f6c8d42967e014d967ba0d5b85cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66142294"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell

W tym artykule opisano poleceń cmdlet programu PowerShell, używane do wykonywania serwer usług Azure Analysis Services i zadań zarządzania bazą danych. 

Zadania zarządzania serwerem, takich jak tworzenie lub usuwanie serwera, zawieszanie lub wznawianie operacji serwerowych lub zmiana poziomu usług (warstwa) za pomocą poleceń cmdlet usługi Azure Resource Manager (zasób) i poleceń cmdlet usług Analysis Services (serwer). Inne zadania do zarządzania bazami danych, takich jak dodawanie lub usuwanie członków ról, przetwarzanie lub partycjonowanie użyj poleceń cmdlet zawartych w tym samym modułem programu SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Uprawnienia

Większość zadań programu PowerShell wymaga, że masz uprawnienia administratora na serwerze usług Analysis Services, którymi zarządzasz. Zaplanowane zadania w programie PowerShell to operacje instalacji nienadzorowanej. Konto lub jednostki, obsługujących harmonogram usługi musi mieć uprawnienia administratora na serwerze usług Analysis Services. 

Dla operacji serwera przy użyciu poleceń cmdlet programu Azure PowerShell, Twoje konto lub konta uruchamiania usługi scheduler należy także być członkiem roli właściciela dla zasobów w [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operacje zarządzania zasobów 

Moduł — [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Polecenie cmdlet|Opis| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Pobiera Szczegóły wystąpienia serwera.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Tworzy wystąpienie serwera.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Tworzy nowy konfiguracji zapory usług Analysis Services.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Tworzy nową regułę zapory usług Analysis Services.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Usuwa wystąpienie serwera.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Wznawia wystąpienie serwera.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Wstrzymuje wystąpienie serwera.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Modyfikuje wystąpienie serwera.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Sprawdza obecność wystąpienia serwera.| 

## <a name="server-management-operations"></a>Operacje zarządzania serwerem

Moduł — [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Polecenie cmdlet|Opis| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|Dodaje uwierzytelnione konto do użycia dla żądań polecenia cmdlet serwera usług Azure Analysis Services.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|Eksportuje dziennika z wystąpienia serwera usług Analysis Services w aktualnie zalogowanego w środowisku określony w poleceniu Add-AzAnalysisServicesAccount|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Powoduje ponowne uruchomienie wystąpienia serwera usług Analysis Services w środowisku aktualnie zalogowany. określony w poleceniu Add-AzAnalysisServicesAccount.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Synchronizuje określonej bazy danych w podanym wystąpieniu serwera usług Analysis Services do wszystkich wystąpień skalowalnego w poziomie zapytania w aktualnie zalogowanego w środowisku określony w poleceniu Add-AzAnalysisServicesAccount|  

## <a name="database-operations"></a>Operacje bazy danych

Operacje bazy danych w usłudze Azure Analysis Services używać tego samego [SqlServer module](https://www.powershellgallery.com/packages/SqlServer) jak SQL Server Analysis Services. Jednak nie wszystkie polecenia cmdlet są obsługiwane w przypadku usług Azure Analysis Services. Aby dowiedzieć się więcej, zobacz [program SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

W module SqlServer udostępnia polecenia cmdlet do bazy danych specyficznych dla zadań zarządzania, a także ogólnego przeznaczenia polecenia cmdlet Invoke-ASCmd, które akceptuje zapytanie skryptów języka TMSL (Tabular Model) lub skrypt. Następujące polecenia cmdlet w SqlServer module są obsługiwane w przypadku usług Azure Analysis Services.

  
|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Dodaj członka do roli bazy danych.| 
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
* [Tabelaryczny Model programowania dla zgodności poziomu 1200 i wyższym](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
