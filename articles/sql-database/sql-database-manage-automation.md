---
title: Zarządzanie bazami danych SQL Azure przy użyciu automatyzacji Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu usługi Automatyzacja Azure umożliwia zarządzanie bazami danych Azure SQL na dużą skalę.
services: sql-database, automation
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: f0f071c2ad5e79168d89361c89a005c247599655
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Zarządzanie bazami danych SQL Azure przy użyciu usługi Automatyzacja Azure
W tym przewodniku przedstawiono usługi Automatyzacja Azure oraz jak on używany w celu uproszczenia zarządzania baz danych Azure SQL.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Automatyzacja Azure](https://azure.microsoft.com/services/automation/) jest usługą platformy Azure, dla uproszczenia zarządzania chmurą za pomocą automatyzacji procesu. Przy użyciu usługi Automatyzacja Azure, można zautomatyzować długotrwałe, ręcznych, podatnych i często powtarzanych zadań, aby zwiększyć niezawodność, wydajność i wartość czasu dla Twojej organizacji.

Automatyzacja Azure umożliwia aparatowi wykonywania przepływów pracy wysoce niezawodne i wysokiej dostępności, która może obsłużyć do własnych potrzeb miarę rozwoju organizacji. W automatyzacji Azure procesów może być rozpoczęte ręcznie, przez systemy 3rd firmy lub w zaplanowanych odstępach czasu tak, aby zadania stanie dokładnie w razie potrzeby.

Obniżyć koszty operacyjne i zwolnić IT / personel DevOps skupić się na pracy, który dodaje biznesowych wartość przez przeniesienie zadań zarządzania chmury do automatycznego uruchamiania przez usługi Automatyzacja Azure.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak usługi Automatyzacja Azure ułatwia zarządzanie bazami danych Azure SQL?
Baza danych SQL Azure można zarządzać w automatyzacji Azure za pomocą [poleceń cmdlet programu PowerShell bazy danych SQL Azure](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/) dostępnych w [narzędzia programu Azure PowerShell](/powershell/azure/overview). Automatyzacja Azure ma tych poleceń cmdlet programu PowerShell bazy danych SQL Azure fabrycznej, dzięki czemu można wykonywać wszystkie zadania zarządzania bazy danych SQL w ramach usługi. Można również skojarzyć te polecenia cmdlet usługi Automatyzacja Azure, za pomocą poleceń cmdlet dla innych usług Azure, aby zautomatyzować złożone zadania 3 systemów firm i usług Azure.

Automatyzacja Azure ma również możliwość komunikacji z serwerami SQL bezpośrednio, wysyłając poleceń SQL przy użyciu programu PowerShell.

[Galerię elementów runbook usługi Automatyzacja Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) zawiera szereg produktu zespołu i społeczność elementy runbook, aby rozpocząć automatyzacji zarządzania bazami danych SQL Azure, innych usług platformy Azure i 3 systemów firm. Galeria elementów runbook obejmują:

* [Uruchamianie zapytań SQL na bazie danych programu SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Skalowanie w pionie (w górę lub w dół) bazy danych SQL Azure zgodnie z harmonogramem](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Obciąć tabeli SQL, jeśli zbliża się do maksymalnego rozmiaru bazy danych](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Indeks tabel w bazie danych SQL Azure, jeśli są one bardzo pofragmentowane](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi Automatyzacja Azure i jak może służyć do zarządzania baz danych Azure SQL, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat automatyzacji Azure.

* [Omówienie usługi Automatyzacja Azure](../automation/automation-intro.md)
* [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa uczenia usługi Automatyzacja Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Automatyzacja Azure: Agenta programu SQL w chmurze](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

