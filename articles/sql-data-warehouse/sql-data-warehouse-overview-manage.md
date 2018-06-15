---
title: Zarządzanie bazami danych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Omówienie zarządzania bazami danych magazynu danych SQL. Obejmuje narzędzia do zarządzania, jednostek dwu i wydajność skalowania w poziomie, rozwiązywanie problemów z wydajność zapytań, ustanawiania zasad zabezpieczeń dobrej i przywracanie bazy danych z uszkodzenie danych lub regionalnej awarii.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: ''
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: acf521bdc15dfab4c7e43081159bc1385768838e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198491"
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Zarządzanie bazami danych w magazynie danych SQL Azure
Usługa SQL Data Warehouse automatyzuje wiele aspektów Zarządzanie bazami danych. Na przykład do skalowania wydajności tylko należy dostosować i opłacać odpowiedni poziom zasoby obliczeniowe, a następnie zezwolić wykonywać wszystkie zadania, skalowanie w poziomie oraz skalowanie wstecz usługi SQL Data Warehouse.

Bez wątpienia można monitorować obciążenie ustalenie potrzeb wydajności, a także rozwiązywanie problemów z kwerendami długotrwałe. Należy również wykonać kilka zadań zabezpieczeń do zarządzania uprawnieniami dla użytkowników i nazwy logowania.

To omówienie dotyczy aspektami zarządzania magazynu danych SQL.

* Narzędzia do zarządzania
* Skalowanie możliwości obliczeniowych
* Wstrzymywanie i wznawianie
* Najlepsze rozwiązania w zakresie wydajności
* Monitorowanie zapytania
* Bezpieczeństwo
* Tworzenie kopii zapasowej i przywracanie

## <a name="management-tools"></a>Narzędzia do zarządzania
Można użyć różnych narzędzi do zarządzania bazami danych w magazynie danych programu SQL. W przypadku zarządzania bazami danych, użytkownik opracuje preferencji narzędzia dla każdego typu zadania, które należy wykonać.

### <a name="azure-portal"></a>Azure Portal
[Portalu Azure] [ Azure portal] jest oparte na sieci web portalu, gdzie możesz tworzenie, aktualizowanie i usuwanie baz danych i monitorowania zasobów bazy danych. To narzędzie jest doskonałym właśnie rozpoczniesz pracę z platformą Azure, zarządzanie niewielkiej liczby danych bazy danych magazynu, czy trzeba szybko czymś.

Aby zacząć korzystać z portalu Azure, zobacz [Utwórz magazyn danych SQL (Azure portal)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools w programie Visual Studio
[SQL Server Data Tools] [ SQL Server Data Tools] (SSDT) w programie Visual Studio pozwala łączyć, zarządzanie i tworzenie baz danych. Jeśli jesteś deweloperem zapoznać się z programu Visual Studio lub innych środowisk zintegrowanego rozwoju (IDE), spróbuj użyć narzędzia SSDT w programie Visual Studio.

Program SSDT obejmuje Eksplorator obiektów SQL Server, co pozwala na wizualizowanie, połączenia i wykonywanie skryptów SQL Data Warehouse w przypadku baz danych. Aby szybko nawiązać SQL Data Warehouse, wystarczy kliknąć **Otwórz w programie Visual Studio** przycisku w pasku po bazy danych przeglądania szczegółowych informacji w portalu Azure poleceń.  

Aby rozpocząć pracę z narzędziami SSDT w programie Visual Studio, zobacz [zapytania magazyn danych SQL Azure z programem Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Narzędzia wiersza polecenia
Narzędzia wiersza polecenia idealnie nadają się do automatyzowania obciążeń.  Program PowerShell oraz narzędzia sqlcmd są dwa skuteczne sposoby automatyzacji procesów.  Firma Microsoft zaleca tych narzędzi do zarządzania dużą liczbę serwerów logicznych i wdrażanie zmiany zasobów w środowisku produkcyjnym jako zadania niezbędne mogą być uwzględnione w skryptach i następnie automatycznego.

### <a name="dynamic-management-views"></a>Dynamicznych widoków zarządzania
Widoków DMV są chleb masła i zarządzania magazynu danych SQL. Prawie wszystkie informacje, które udostępnia w portalu używa widoków DMV. Aby wyświetlić listę widoków DMV magazynu danych SQL, zobacz [widoków systemowych SQL Data Warehouse][SQL Data Warehouse system views].

Aby rozpocząć, zobacz [Connect i zapytania przy użyciu narzędzia sqlcmd][Connect and query with sqlcmd], i [Utwórz bazę danych (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych
W usłudze SQL Data Warehouse można szybko skalować wydajności lub ponownie według zwiększenie lub zmniejszenie zasoby obliczeniowe procesora CPU, pamięci i przepustowość we/wy. Do skalowania wydajności, należy wykonać jest Dostosuj liczbę jednostek magazynu danych (dwu) przydzielanych przez Magazyn danych SQL z bazą danych. Usługa SQL Data Warehouse szybkie sprawia, że zmiany i obsługuje wszystkie podstawowej zmiany sprzętu lub oprogramowania.

Aby dowiedzieć się więcej na temat skalowania jednostek dwu, zobacz [skalowanie wydajności].

## <a name="pause-and-resume"></a>Wstrzymywanie i wznawianie
W celu ograniczenia kosztów, możesz wstrzymywać i wznawiać obliczeń zasobów na żądanie. Na przykład jeśli nie będzie używać bazy danych w nocy i w weekendy, można wstrzymywać go w tych godzinach i wznawiać go w ciągu dnia. Użytkownik nie zostanie obciążona dla jednostek dwu, podczas bazy danych zostało wstrzymane.

Aby uzyskać więcej informacji, zobacz [wstrzymać obliczeń][Pause compute], i [wznowić operacje obliczeniowe][Resume compute].

## <a name="performance-best-practices"></a>Najlepsze rozwiązania w zakresie wydajności
Wprowadzenie do korzystania z nowej technologii, odnajdywanie porady i wskazówki, które działają najlepiej prawo od początku można zapisać można bardzo długo.  Najlepsze rozwiązania w całym wiele nasze tematy będą dostępne.

Aby wyświetlić wiele podsumowanie najważniejszych zagadnień podczas opracowywania obciążenie, zobacz [najlepsze rozwiązania magazynu danych SQL][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Monitorowanie zapytania
Czasami kwerendy działa zbyt długo, ale nie masz pewności z których jeden jest dziedziczonej z istotnymi elementami. Magazyn danych SQL ma dynamicznych widoków zarządzania (widoków DMV) używane do ustalenia, która kwerenda trwa zbyt długo.

Aby znaleźć kwerend, zobacz [monitorowania obciążenia przy użyciu widoków DMV][Monitor your workload using DMVs].

## <a name="security"></a>Bezpieczeństwo
Aby zachować bezpieczny system, muszą być o alercie i chronią przed nieautoryzowanym dostępem dowolnego typu. Upewnij się, że reguły zapory są stosowane tylko autoryzowane adresy IP mogą się łączyć musi systemu zabezpieczeń. Musi on odpowiedniego uwierzytelnienia poświadczeń użytkownika. Po użytkownik połączył się z bazą danych, użytkownik powinien mieć tylko uprawnienia do wykonywania minimalnej liczbie czynności. Aby zabezpieczyć dane, należy używać szyfrowania. Istotne jest również inspekcji i śledzenia, więc można odtwarzanie zdarzenia, jeśli istnieje wszelkich podejrzanych działań.

Informacje na temat zarządzania zabezpieczeniami, przejdź do [Omówienie zabezpieczeń][Security overview].

## <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie
Niezawodne kopie zapasowe danych jest integralną część żadnych produkcyjną bazę danych. Usługa SQL Data Warehouse chroni dane przez automatyczne tworzenie kopii zapasowej active baz danych w regularnych odstępach czasu. Te kopie zapasowe umożliwiają odzyskanie scenariuszy, w którym uszkodzone dane lub przypadkowo usunięty, danych lub bazy danych.  Dla harmonogramu tworzenia kopii zapasowej danych zasad przechowywania i przywracanie bazy danych, zobacz [Przywracanie z migawki][Restore from snapshot].

## <a name="next-steps"></a>Kolejne kroki
Przy użyciu bazy danych dobrym projektowy zasad ułatwi zarządzanie bazami danych w usłudze SQL Data Warehouse. Aby dowiedzieć się więcej, przejdź do [omówienie tworzenia][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: pause-and-resume-compute-portal.md#pause-compute
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: pause-and-resume-compute-portal.md#resume-compute
[skalowanie wydajności]: quickstart-scale-compute-portal.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
