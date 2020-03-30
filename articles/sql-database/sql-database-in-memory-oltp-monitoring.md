---
title: Monitorowanie pamięci masowej W PAMIĘCI XTP
description: Oszacuj i monitoruj użycie pamięci masowej W pamięci XTP, pojemność; rozwiązywanie problemu pojemności 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 22ff83b1ccd009624082e45073123a45006df70f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209426"
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorowanie pamięci masowej OLTP w pamięci

W przypadku korzystania z [protokołu OLTP w pamięci](sql-database-in-memory.md)dane w tabelach zoptymalizowanych pod kątem pamięci i zmienne tabeli znajdują się w pamięci magazynu OLTP. Każda warstwa usługi Premium i Business Critical ma maksymalny rozmiar magazynu OLTP w pamięci. Zobacz [limity zasobów oparte na jednostce DTU — pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md), [limity zasobów oparte na jednostce DTU — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md),[limity zasobów oparte na ramach wersji vCore — pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów oparte na parcie wirtualne — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

Po przekroczeniu tego limitu operacje wstawiania i aktualizacji mogą rozpocząć się niepowodzeniem z błędem 41823 dla pojedynczych baz danych i błędem 41840 dla pul elastycznych. W tym momencie należy usunąć dane, aby odzyskać pamięć lub uaktualnić warstwę usług lub rozmiar obliczeń bazy danych.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Określanie, czy dane mieszczą się w zaślepieniu magazynu OLTP w pamięci wbudowanej

Określ limity magazynowania różnych warstw usług. Zobacz [limity zasobów oparte na jednostce DTU — pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md), [limity zasobów oparte na jednostce DTU — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md),[limity zasobów oparte na ramach wersji vCore — pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów oparte na parcie wirtualne — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

Szacowanie wymagań dotyczących pamięci dla tabeli zoptymalizowanej pod kątem pamięci działa w taki sam sposób dla programu SQL Server, jak w usłudze Azure SQL Database. Poświęć kilka minut, aby przejrzeć ten artykuł na [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Wiersze zmiennych tabeli i tabeli, a także indeksy, są wliczane do maksymalnego rozmiaru danych użytkownika. Ponadto alter table potrzebuje wystarczającej ilości miejsca, aby utworzyć nową wersję całej tabeli i jej indeksów.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Można monitorować użycie magazynu w pamięci jako procent limitu miejsca do magazynowania dla rozmiaru obliczeniowego w [witrynie Azure portal:](https://portal.azure.com/) 

1. W bloku Baza danych zlokalizuj pole Wykorzystanie zasobów i kliknij pozycję Edytuj.
2. Wybierz metrykę `In-Memory OLTP Storage percentage`.
3. Aby dodać alert, kliknij pole Wykorzystanie zasobów, aby otworzyć metryk bloku, a następnie kliknij dodaj alert.

Możesz też użyć następującej kwerendy, aby wyświetlić wykorzystanie magazynu w pamięci:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Poprawianie sytuacji przechowywania oltp poza pamięcią podręczną — błędy 41823 i 41840

Naciśnięcie limitu magazynu OLTP w pamięci w bazie danych powoduje, że operacje INSERT, UPDATE, ALTER i CREATE nie powiódł się z komunikatem o błędzie 41823 (dla pojedynczych baz danych) lub błędem 41840 (dla puli elastycznych). Oba błędy powodują przerwanie aktywnej transakcji.

Komunikaty o błędach 41823 i 41840 wskazują, że zoptymalizowane pod kątem pamięci tabele i zmienne tabeli w bazie danych lub puli osiągnęły maksymalny rozmiar magazynu OLTP w pamięci.

Aby rozwiązać ten błąd,:

* Usuwanie danych z tabel zoptymalizowanych pod kątem pamięci, potencjalnie odciążając dane do tradycyjnych tabel opartych na dysku; Lub
* Uaktualnij warstwę usługi do warstwy wystarczającej ilości pamięci masowej w pamięci danych potrzebnych do przechowywania w tabelach zoptymalizowanych pod kątem pamięci.

> [!NOTE] 
> W rzadkich przypadkach błędy 41823 i 41840 mogą być przejściowe, co oznacza, że jest wystarczająco dużo dostępnego magazynu OLTP w pamięci i ponowienie próby wykonania operacji zakończy się pomyślnie. Dlatego zaleca się zarówno monitorowanie ogólnej dostępnej pamięci OLTP w pamięci, jak i ponawianie próby przy pierwszym napotkaniu błędu 41823 lub 41840. Aby uzyskać więcej informacji na temat logiki ponawiania prób, zobacz [Wykrywanie konfliktów i Ponawianie prób logiki za pomocą protokołu OLTP w pamięci](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać wskazówki dotyczące monitorowania, zobacz [Monitorowanie bazy danych SQL azure przy użyciu widoków zarządzania dynamicznego](sql-database-monitoring-with-dmvs.md).
