---
title: Zarządzanie bazami danych za pomocą usługi Azure Automation
description: Dowiedz się, jak usługa Azure Automation może służyć do zarządzania bazami danych SQL platformy Azure na dużą skalę.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822802"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Zarządzanie bazami danych SQL platformy Azure przy użyciu usługi Azure Automation

W tym przewodniku przedstawiono usługę Azure Automation oraz sposób, w jaki można ją wykorzystać do uproszczenia zarządzania bazami danych SQL platformy Azure.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?

[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure ułatwiająca zarządzanie chmurą za pomocą automatyzacji procesów. Korzystanie z usługi Azure Automation, długotrwałe, ręczne, podatne na błędy i często powtarzane zadania można zautomatyzować, aby zwiększyć niezawodność, wydajność i czas na wartość dla twojej organizacji.

Usługa Azure Automation zapewnia aparat wykonywania przepływu pracy o wysokiej niezawodności i wysokiej dostępności, a także skaluje się do twoich potrzeb w miarę rozwoju organizacji. W usłudze Azure Automation procesy można uruchamiać ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania są wykonywane dokładnie wtedy, gdy są potrzebne.

Zmniejsz obciążenie operacyjne i uwolnij pracowników DZIAŁU IT / DevOps, aby skupić się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez usługę Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>W jaki sposób usługa Azure Automation może pomóc w zarządzaniu bazami danych SQL platformy Azure?

Usługa Azure SQL Database można zarządzać w usłudze Azure Automation przy użyciu [poleceń cmdlet programu Azure SQL Database PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) dostępnych w [narzędziach programu Azure PowerShell.](/powershell/azure/overview) Usługa Azure Automation ma te polecenia cmdlet programu Azure SQL Database PowerShell dostępne po wyjęciu z pudełka, dzięki czemu można wykonywać wszystkie zadania zarządzania bazy danych SQL w ramach usługi. Te polecenia cmdlet w usłudze Azure Automation można również sparować z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w usługach platformy Azure i w systemach innych firm.

Usługa Azure Automation ma również możliwość komunikowania się bezpośrednio z serwerami SQL, wydając polecenia SQL przy użyciu programu PowerShell.

[Galeria roboczej usługi Azure Automation](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) zawiera wiele kreśleń wiązek produktów i wiązek owych produktów, aby rozpocząć automatyzację zarządzania bazami danych SQL platformy Azure, innymi usługami platformy Azure i systemami innych firm. W skład książki eks-owe galerii należą:

- [Uruchamianie zapytań SQL względem bazy danych programu SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Pionowo skaluj (w górę lub w dół) usługę Azure SQL Database zgodnie z harmonogramem](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Obcinanie tabeli SQL, jeśli jej baza danych zbliża się do jej maksymalnego rozmiaru](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Tabele indeksów w bazie danych SQL platformy Azure, jeśli są bardzo pofragmentowane](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawy usługi Azure Automation i jak można go używać do zarządzania bazami danych SQL platformy Azure, skorzystaj z tych łączy, aby dowiedzieć się więcej o usłudze Azure Automation.

- [Omówienie usługi Azure Automation](../automation/automation-intro.md)
- [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
- [Automatyzacja platformy Azure: Twój agent SQL w chmurze](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 