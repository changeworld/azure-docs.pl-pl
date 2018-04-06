---
title: Monitorowanie magazynu w pamięci XTP | Dokumentacja firmy Microsoft
description: Szacowana i monitorowanie magazynu w pamięci XTP korzystać, pojemności; Napraw błąd pojemności 41823
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/04/2018
ms.author: jodebrui
ms.openlocfilehash: 0802a3b51847236efb64e628ed259dc7776bac4e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitor OLTP w pamięci magazynu
Korzystając z [OLTP w pamięci](sql-database-in-memory.md), dane w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabel znajdują się w magazynie OLTP w pamięci. Każda warstwa usług Premium i biznesowe krytyczne ma maksymalny rozmiar magazynu OLTP w pamięci, które opisano w [limity zasobów na podstawie jednostek dtu w warstwie](sql-database-dtu-resource-limits.md) i [limity zasobów na podstawie vCore](sql-database-vcore-resource-limits.md). Po przekroczeniu tego limitu insert i zaktualizować operacji mogą kończyć się niepowodzeniem z błędem 41823 autonomiczny baz danych i błąd 41840 dla pul elastycznych. W tym momencie możesz należy usunąć dane w celu odzyskania pamięci, lub Uaktualnij warstwę wydajności bazy danych.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Określić, czy danych mieści się w zakończenia magazynu OLTP w pamięci
Określ caps magazynu warstwy z innej usługi. Zobacz [limity zasobów na podstawie jednostek dtu w warstwie](sql-database-dtu-resource-limits.md) i [limity zasobów na podstawie vCore](sql-database-vcore-resource-limits.md).

Trwa szacowanie wymagań pamięci dla działania tabel zoptymalizowanych pod kątem pamięci jest taki sam sposób dla programu SQL Server, ponieważ w bazie danych SQL Azure. Potrwać kilka minut, aby przejrzeć ten artykuł na [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabeli i zmiennej wiersze tabeli, a także indeksy są wliczane do użytkownika maksymalny rozmiar danych. Instrukcja ALTER TABLE musi ponadto wystarczająco dużo miejsca, aby utworzyć nową wersję całą tabelę i jego indeksy.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Możesz monitorować użycie magazynu w pamięci w procentach zakończenia magazynu dla warstwę wydajności w [portalu Azure](https://portal.azure.com/): 

1. W bloku bazy danych odszukaj pole wykorzystania zasobów, a następnie kliknij przycisk Edytuj.
2. Wybierz metrykę, `In-Memory OLTP Storage percentage`.
3. Aby dodać alert, kliknij pole wykorzystania zasobów, aby otworzyć blok metryki, a następnie kliknij pozycję Dodaj alert.

Lub użyj następującego zapytania, aby wyświetlić wykorzystanie magazynu w pamięci:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Popraw sytuacjach magazynu OLTP poza w-pamięci — błędy 41823 i 41840
Naciśnięcie zakończenia magazynu OLTP w pamięci w wynikach bazy danych w WSTAWIANIA, aktualizacji, ALTER i Utwórz operacji niepowodzeniem z powodu komunikat o błędzie 41823 (w przypadku autonomicznej bazy danych) lub błąd 41840 (dla puli elastycznej). Zarówno błędy spowodować przerwanie aktywnej transakcji.

Komunikaty o błędach 41823 i 41840 wskazują, że tabele zoptymalizowane pod kątem pamięci i zmiennych tabel w bazie danych lub puli osiągnął maksymalny rozmiar magazynu OLTP w pamięci.

Aby rozwiązać ten problem, albo:

* Usuwanie danych z tabel zoptymalizowanych pod kątem pamięci potencjalnie Odciążanie danych do tabel tradycyjnych, opartej na dysku. lub,
* Uaktualnienie warstwy usług, aby za mało przechowywania w pamięci dla danych, które należy przechowywać w tabelach zoptymalizowanych pod kątem pamięci.

> [!NOTE] 
> W rzadkich przypadkach błędy 41823 i 41840 może być przejściowy, co oznacza brak wystarczającej ilości dostępnego magazynu OLTP w pamięci i operacji zakończy się pomyślnie. Dlatego zaleca się zarówno monitorowania ogólnej dostępności magazynu OLTP w pamięci, aby ponowić próbę, gdy najpierw napotkania błędu 41823 lub 41840. Aby uzyskać więcej informacji na temat logika ponowień, zobacz [wykrywania konfliktów i ponów próbę wykonania logiki z OLTP w pamięci](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Kolejne kroki
Do monitorowania wskazówki, zobacz [monitorowania bazy danych SQL Azure przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md).
