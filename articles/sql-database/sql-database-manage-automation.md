---
title: Zarządzanie bazami danych Azure SQL przy użyciu usługi Azure Automation | Dokumentacja firmy Microsoft
description: Naucz się, jak używać usługi Azure Automation do zarządzania bazami danych Azure SQL na dużą skalę.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: e488e742fc49102f7c58d132a66bca2347ad575c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702099"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Zarządzanie bazami danych Azure SQL przy użyciu usługi Azure Automation

Ten przewodnik Wprowadzenie do usługi Azure Automation i jak może służyć do uproszczenia zarządzania bazy danych Azure SQL.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?

[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) jest usługą platformy Azure dla uproszczenie zarządzania chmurą poprzez automatyzację procesów. Za pomocą usługi Azure Automation, długotrwałych, ręczne, podatne na błędy i często powtarzanych zadań można zautomatyzować zwiększyć niezawodność, wydajność i czasy wykonywania operacji dla Twojej organizacji.

Usługa Azure Automation zapewnia aparatu wykonywania przepływów pracy o wysokiej niezawodności i wysokiej dostępności i który jest skalowana w celu spełnienia konkretnych potrzeb w miarę rozwoju organizacji. W usłudze Azure Automation procesów może być rozpoczęła się ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu tak, aby zadania się tak zdarzyć, dokładnie tak, gdy jest to potrzebne.

Niższe koszty operacyjne i Zwolnij część IT / pracownicy działu DevOps, aby skupić się na pracy, który dodaje firm wartość przez przeniesienie zadań zarządzania systemem chmury uruchamiana automatycznie przez usługę Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak usługi Azure Automation ułatwiają zarządzanie bazami danych Azure SQL?

Usługa Azure SQL Database można zarządzać w usłudze Azure Automation za pomocą [poleceń cmdlet programu PowerShell usługi Azure SQL Database](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) dostępnych w [narzędzia programu Azure PowerShell](/powershell/azure/overview). Usługa Azure Automation obejmuje tych poleceń cmdlet programu PowerShell usługi Azure SQL Database gotowych, tak, aby wykonywać wszystkie zadania zarządzania bazą danych SQL w ramach usługi. Można również skojarzyć tych poleceń cmdlet w usłudze Azure Automation za pomocą poleceń cmdlet dla innych usług platformy Azure w celu zautomatyzowania złożonych zadań, w usługach platformy Azure i systemach innych firm.

Usługa Azure Automation ma również możliwość komunikacji z serwerami SQL bezpośrednio przez wydanie polecenia SQL przy użyciu programu PowerShell.

[Galerii elementów runbook usługi Azure Automation](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) zawiera szereg produktu zespół i społeczności elementy runbook, aby rozpocząć automatyzację zarządzania bazami danych Azure SQL, innych usług platformy Azure i systemach innych firm. Galeria elementów runbook obejmują:

- [Uruchamiać zapytania SQL względem bazy danych programu SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Skalowanie w pionie (górę lub w dół) usługi Azure SQL Database zgodnie z harmonogramem](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Obcinanie tabeli SQL, jeśli jego bazy danych zbliża się do maksymalnego rozmiaru](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indeks tabel w bazie danych SQL Azure, jeśli są one bardzo pofragmentowane](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już podstawy usługi Azure Automation i jak może służyć do zarządzania bazami danych Azure SQL, skorzystaj z poniższych linków, aby dowiedzieć się więcej o usłudze Azure Automation.

- [Omówienie usługi Azure Automation](../automation/automation-intro.md)
- [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
- [Usługa Azure Automation: Agenta programu SQL w chmurze](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 