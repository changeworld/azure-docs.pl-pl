---
title: Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Usługa Azure Analysis Services management przy użyciu programu PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a958c33e173c881a3ad09a49fe9f71ddb0c9df56
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508947"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell

W tym artykule opisano poleceń cmdlet programu PowerShell, używane do wykonywania serwer usług Azure Analysis Services i zadań zarządzania bazą danych. 

Zadania zarządzania zasobami serwera, takich jak tworzenie lub usuwanie serwera, zawieszanie lub wznawianie operacji serwerowych lub zmiana poziomu usług (warstwa) użyj poleceń cmdlet usług Azure Analysis Services. Inne zadania do zarządzania bazami danych, takich jak dodawanie lub usuwanie członków ról, przetwarzanie lub partycjonowanie użyj poleceń cmdlet zawartych w tym samym modułem programu SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Uprawnienia

Większość zadań programu PowerShell wymaga, że masz uprawnienia administratora na serwerze usług Analysis Services, którymi zarządzasz. Zaplanowane zadania w programie PowerShell to operacje instalacji nienadzorowanej. Konto lub jednostki, obsługujących harmonogram usługi musi mieć uprawnienia administratora na serwerze usług Analysis Services. 

Dla operacji serwera przy użyciu poleceń cmdlet programu Azure PowerShell, Twoje konto lub konta uruchamiania usługi scheduler należy także być członkiem roli właściciela dla zasobów w [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operacje zasobów i serwera 

Zainstaluj moduł - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentacja — [Az.AnalysisServices odwołania](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operacje bazy danych

Ten sam moduł SqlServer operacji bazy danych usług Analysis Services na platformie Azure służy program SQL Server Analysis Services. Jednak nie wszystkie polecenia cmdlet są obsługiwane w przypadku usług Azure Analysis Services. 

W module SqlServer udostępnia polecenia cmdlet do bazy danych specyficznych dla zadań zarządzania, a także ogólnego przeznaczenia polecenia cmdlet Invoke-ASCmd, które akceptuje zapytanie skryptów języka TMSL (Tabular Model) lub skrypt. Następujące polecenia cmdlet w SqlServer module są obsługiwane w przypadku usług Azure Analysis Services.

Zainstaluj moduł - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentacja — [odwołanie SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Obsługiwane polecenia cmdlet

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

* [Program SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Pobierz moduł PowerShell programu SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobieranie programu SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Module SqlServer w galerii programu PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabelaryczny Model programowania dla zgodności poziomu 1200 i wyższym](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
