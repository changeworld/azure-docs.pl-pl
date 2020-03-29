---
title: Zarządzanie usługami Analizy Platformy Azure za pomocą programu PowerShell | Dokumenty firmy Microsoft
description: W tym artykule opisano polecenia cmdlet programu Azure Analysis Services programu PowerShell dla typowych zadań administracyjnych, takich jak tworzenie serwerów, zawieszanie operacji lub zmiana poziomu usług.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572690"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Zarządzanie usługami Azure Analysis Services przy użyciu programu PowerShell

W tym artykule opisano polecenia cmdlet programu PowerShell używane do wykonywania zadań zarządzania serwerem i bazą danych usługi Azure Analysis Services. 

Zadania zarządzania zasobami serwera, takie jak tworzenie lub usuwanie serwera, zawieszanie lub wznawianie operacji serwera lub zmienianie poziomu usług (warstwy) używają poleceń cmdlet usług Azure Analysis Services. Inne zadania do zarządzania bazami danych, takie jak dodawanie lub usuwanie elementów członkowskich roli, przetwarzanie lub partycjonowanie używają poleceń cmdlet zawartych w tym samym module Programu SqlServer jako usługi SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Uprawnienia

Większość zadań programu PowerShell wymaga uprawnień administratora na serwerze usług Analysis Services, na który zarządzasz. Zaplanowane zadania programu PowerShell są operacjami nienadzorowanymi. Konto lub podmiot zabezpieczeń usługi z uruchomionym harmonogramem musi mieć uprawnienia administratora na serwerze usług Analysis Services. 

W przypadku operacji serwera przy użyciu poleceń cmdlet programu Azure PowerShell konto lub harmonogram z uruchomionym kontem musi również należeć do roli Właściciela zasobu w [kontroli dostępu opartej na rolach platformy Azure (RBAC).](../role-based-access-control/overview.md) 

## <a name="resource-and-server-operations"></a>Operacje zasobów i serwerów 

Moduł instalacyjny - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentacja - [Odwołanie do Az.AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operacje bazy danych

Operacje bazy danych usługi Azure Analysis Services używają tego samego modułu Programu SqlServer, co usługi SQL Server Analysis Services. Jednak nie wszystkie polecenia cmdlet są obsługiwane dla usług Azure Analysis Services. 

Moduł SqlServer udostępnia polecenia cmdlet zarządzania bazą danych specyficzne dla zadania, a także ogólne przeznaczenie Invoke-ASCmd poleceń cmdlet, który akceptuje tabular model skryptów języka (TMSL) kwerendy lub skryptu. Następujące polecenia cmdlet w module SqlServer są obsługiwane dla usług Azure Analysis Services.

Moduł instalacji - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentacja — [odwołanie do programu SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Obsługiwane polecenia cmdlet

|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Numer dodania roli](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Dodawanie członka do roli bazy danych.| 
|[Kopia zapasowa-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Tworzenie kopii zapasowych bazy danych usług Analysis Services.|  
|[Usuń-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Usuwanie członka z roli bazy danych.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Wykonanie skryptu TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Przetwórz bazę danych.|  
|[Wywoływanie-przetwarzanie](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Przetwórz partycję.| 
|[Przywołuj—ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Przetwórz tabelę.|  
|[Scalanie partycji](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Scalanie partycji.|  
|[Przywróć-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Przywracanie bazy danych usług Analysis Services.| 
  

## <a name="related-information"></a>Informacje pokrewne

* [Program PowerShell dla oprogramowania SQL Server](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Pobieranie modułu programu SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobieranie SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Moduł Programu SqlServer w galerii programu PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programowanie modelu tabelaryczne dla poziomu zgodności 1200 lub wyższego](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
