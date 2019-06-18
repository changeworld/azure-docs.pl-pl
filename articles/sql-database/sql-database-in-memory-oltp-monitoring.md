---
title: Monitorowanie magazynu w pamięci XTP | Dokumentacja firmy Microsoft
description: Szacowanie i monitorowanie magazynu w pamięci XTP korzystać, pojemności Napraw błąd pojemności 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7542e9fa04eb838baca37dbe13f7cdacdfaf041b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035747"
---
# <a name="monitor-in-memory-oltp-storage"></a>Pojemność magazynu OLTP w pamięci monitora

Korzystając z [OLTP w pamięci](sql-database-in-memory.md), znajdują się szczegółowe dane w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabel w pojemność magazynu OLTP w pamięci. Każda warstwa usługi Premium i krytyczne dla działania firmy ma maksymalny rozmiar magazynu OLTP w pamięci. Zobacz [limity zasobów oparty na jednostkach DTU — Pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md), [limity zasobów oparty na jednostkach DTU - pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md),[limity zasobów opartych na rdzeniach wirtualnych - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów opartych na rdzeniach wirtualnych - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).

Po przekroczeniu tego limitu, wstawiania i aktualizowania operacji może uruchomić kończy się niepowodzeniem z błędem 41823 dla pojedynczych baz danych i błąd 41840 dla pul elastycznych. W tym momencie należy albo usunąć danych w celu odzyskania pamięci, lub Uaktualnij warstwę usług lub obliczenia rozmiaru bazy danych.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Określić, czy data mieści się w limit pojemności magazynu OLTP w pamięci

Określ limity magazynu z różnymi warstwami usług. Zobacz [limity zasobów oparty na jednostkach DTU — Pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md), [limity zasobów oparty na jednostkach DTU - pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md),[limity zasobów opartych na rdzeniach wirtualnych - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów opartych na rdzeniach wirtualnych - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).

Szacowanie wymagania dotyczące pamięci dla tabel zoptymalizowanych pod kątem pamięci działa taki sam sposób dla programu SQL Server, ponieważ jest w usłudze Azure SQL Database. Dopiero po kilku minutach, aby zapoznać się z tego artykułu na [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabela i zmiennych wiersze tabeli, a także indeksów, wliczają się do użytkownika maksymalny rozmiar danych. Ponadto instrukcja ALTER TABLE wymaga wystarczającej ilości miejsca, aby utworzyć nową wersję cała tabela i jej indeksów.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Możesz monitorować użycie magazynu w pamięci w procentach limit magazynu dla rozmiaru usługi obliczeniowe w [witryny Azure portal](https://portal.azure.com/): 

1. W bloku bazy danych odszukaj pole wykorzystanie zasobów, a następnie kliknij przycisk Edytuj.
2. Wybierz metrykę `In-Memory OLTP Storage percentage`.
3. Aby dodać alert, kliknij pole wykorzystanie zasobów, aby otworzyć blok metryk, a następnie kliknij pozycję Dodaj alert.

Lub użyj następującego zapytania, aby pokazać wykorzystanie magazynu w pamięci:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Popraw sytuacjach magazynu OLTP-w-pamięci — błędy 41823 i 41840

Osiągnięcia limit pojemności magazynu OLTP w pamięci w wynikach bazy danych w WSTAWIANIA, aktualizacji, zmienić operacje i tworzenia niepowodzeniem z powodu komunikat o błędzie 41823 (dla pojedynczych baz danych) lub błąd 41840 (dla pul elastycznych). Zarówno błędy powodują przerwanie aktywnej transakcji.

Komunikaty o błędach 41823 i 41840 wskazują, że tabele zoptymalizowane pod kątem pamięci i zmiennych tabel w bazie danych lub puli Osiągnięto maksymalny rozmiar magazynu OLTP w pamięci.

Aby rozwiązać ten problem, albo:

* Usuwanie danych z tabel zoptymalizowanych pod kątem pamięci, potencjalnie przeniesieniu danych do tabel tradycyjnych, opartej na dyskach. Ewentualnie
* Uaktualnienie warstwy usługi, aby za pomocą wystarczająco dużego magazynu w pamięci dla danych, które musisz zachować w tabelach zoptymalizowanych pod kątem pamięci.

> [!NOTE] 
> W rzadkich przypadkach błędy 41823 i 41840 może być błędem przejściowym, co oznacza ma za mało dostępnego magazynu OLTP w pamięci i ponownym wykonaniem operacji zakończy się pomyślnie. Dlatego zaleca się do obu monitor ogólnie dostępnego magazynu OLTP w pamięci i spróbuj ponownie po pierwszym napotkaniu błędu 41823 lub 41840. Aby uzyskać więcej informacji na temat logikę ponawiania próby zobacz [wykrywania konfliktów i logika ponowień, za pomocą OLTP w pamięci](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Kolejne kroki
Do monitorowania wskazówki, zobacz [monitorowania usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md).
