---
title: Rozszerzone zdarzenia w usłudze SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule opisano rozszerzonej events (XEvents) w usłudze Azure SQL Database, a także jak sesji zdarzeń różni się nieco od sesji zdarzeń w programie Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 7f742b094575b78f453fb735b23cc5319a27fa7e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65206653"
---
# <a name="extended-events-in-sql-database"></a>Rozszerzone zdarzenia w bazie danych SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

W tym temacie wyjaśniono, jak implementacja rozszerzone zdarzenia w usłudze Azure SQL Database jest nieco inne w porównaniu do rozszerzonych zdarzeń w programie Microsoft SQL Server.

- Bazy danych SQL Database V12 zdobyte funkcji zdarzeń rozszerzonych w drugiej połowie kalendarza 2015.
- Program SQL Server miał rozszerzonych zdarzeń od 2008.
- Zestaw funkcji zdarzeń rozszerzonych w bazie danych SQL jest niezawodną podzbiorem funkcji w programie SQL Server.

*XEvents* nieformalne pseudonim, którego używa się czasami "zdarzeń rozszerzonych" znajduje się w naszych blogach i innych lokalizacji nieformalne.

Dodatkowe informacje na temat zdarzenia rozszerzone dla usługi Azure SQL Database i programu Microsoft SQL Server jest dostępne pod adresem:

- [Szybki Start: Rozszerzone zdarzenia w programie SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Zdarzenia rozszerzone](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Wymagania wstępne

W tym temacie założono, że masz już pewną wiedzę na temat:

- [Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozszerzone zdarzenia](https://msdn.microsoft.com/library/bb630282.aspx) w programie Microsoft SQL Server.

- Duża część naszej dokumentacji dotyczące zdarzeń rozszerzonych ma zastosowanie do programu SQL Server i bazy danych SQL.

Wcześniejsze narażenia na następujące elementy jest przydatne w przypadku, gdy wybór plik zdarzeń jako [docelowej](#AzureXEventsTargets):

- [Usługa Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Przy użyciu programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera szczegółowe informacje na temat programu PowerShell i usługi Azure Storage.

## <a name="code-samples"></a>Przykłady kodu

Tematy pokrewne zawierają dwa przykłady kodu:


- [Kod docelowy buforu dla rozszerzonych zdarzeń w bazie danych SQL cyklicznego](sql-database-xevent-code-ring-buffer.md)
    - Krótkie prosty skrypt języka Transact-SQL.
    - Możemy wyróżnić w temacie próbki kodu, że po zakończeniu z obiektem docelowym bufor cykliczny, należy zwolnić zasoby jej przez wykonanie polecenia alter i upuszczanie `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instrukcji. Później możesz dodać inne wystąpienie bufor cykliczny przez `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Kod docelowy pliku zdarzenia dla rozszerzonych zdarzeń w bazie danych SQL](sql-database-xevent-code-event-file.md)
    - Faza 1 jest program PowerShell w celu utworzenia kontenera usługi Azure Storage.
    - Faza 2 jest języka Transact-SQL, który korzysta z kontenera usługi Azure Storage.

## <a name="transact-sql-differences"></a>Różnice w języku Transact-SQL


- Podczas wykonywania [tworzenie zdarzeń sesji](https://msdn.microsoft.com/library/bb677289.aspx) polecenia w programie SQL Server, możesz użyć **ON SERVER** klauzuli. Jednak w bazie danych SQL, możesz użyć **bazy danych na** klauzuli zamiast tego.


- **Bazy danych na** klauzuli ma również zastosowanie do [ALTER zdarzeń sesji](https://msdn.microsoft.com/library/bb630368.aspx) i [PORZUCIĆ zdarzeń sesji](https://msdn.microsoft.com/library/bb630257.aspx) polecenia języka Transact-SQL.


- Najlepszym rozwiązaniem jest uwzględnienie opcja sesji zdarzeń z **STARTUP_STATE = ON** w swojej **tworzenie zdarzeń sesji** lub **ALTER zdarzeń sesji** instrukcji.
    - **= ON** wartość obsługuje automatyczne ponowne uruchomienie po ponownej konfiguracji przez logicznej bazy danych z powodu przejścia w tryb failover.

## <a name="new-catalog-views"></a>Nowych widoków katalogów

Z funkcji zdarzeń rozszerzonych jest obsługiwany przez kilka [widoków wykazu](https://msdn.microsoft.com/library/ms174365.aspx). Widoki wykazu informujące o *metadanych lub definicji* sesji zdarzeń utworzonych przez użytkownika w bieżącej bazie danych. Widoki nie zwracają informacje na temat wystąpień zdarzeń aktywnych sesji.

| Nazwa<br/>Widok katalogu | Opis |
|:--- |:--- |
| **sys.database_event_session_actions** |Zwraca wiersz dla każdej akcji każdego zdarzenia sesji zdarzeń. |
| **sys.database_event_session_events** |Zwraca wiersz dla każdego zdarzenia w sesji zdarzeń. |
| **sys.database_event_session_fields** |Zwraca wiersz dla każdej kolumny ustalaj jawnie ustawione na zdarzeniach i cele. |
| **sys.database_event_session_targets** |Zwraca wiersz dla każdego obiektu docelowego zdarzeń dla sesji zdarzeń. |
| **sys.database_event_sessions** |Zwraca wiersz dla każdej sesji zdarzeń w bazie danych SQL Database. |

W programie Microsoft SQL Server, podobne widoki wykazu mają nazwy, które zawierają *.server\_*  zamiast *.database\_* . Wzorzec nazwy przypomina **sys.server_event_%** .

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Nowe dynamiczne widoki zarządzania [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Usługa Azure SQL Database ma [dynamicznych widoków zarządzania (DMV)](https://msdn.microsoft.com/library/bb677293.aspx) obsługujące zdarzeń rozszerzonych. Dynamiczne widoki zarządzania informujące o *active* sesji zdarzeń.

| Nazwa DMV | Opis |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Zwraca informacje o akcjach sesji zdarzeń. |
| **sys.dm_xe_database_session_events** |Zwraca informacje dotyczące sesji zdarzeń. |
| **sys.dm_xe_database_session_object_columns** |Pokazuje wartości konfiguracji dla obiektów, które są powiązane z sesją. |
| **sys.dm_xe_database_session_targets** |Zwraca informacje na temat celów sesji. |
| **sys.dm_xe_database_sessions** |Zwraca wiersz dla każdej sesji zdarzeń, które są ograniczone do bieżącej bazy danych. |

W programie Microsoft SQL Server, podobne widokach katalogów są nazywane bez  *\_bazy danych* część nazwy, takie jak:

- **sys.dm_xe_sessions**, zamiast nazwy<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Wspólny dla obu dynamicznych widoków zarządzania
Rozszerzone zdarzenia są dodatkowe dynamicznych widoków zarządzania, które są wspólne dla usługi Azure SQL Database i programu Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Znajdowanie dostępnych rozszerzone zdarzenia, akcje i elementy docelowe

Możesz uruchomić proste SQL **wybierz** Aby uzyskać listę dostępnych zdarzenia, akcje i obiekt docelowy.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Elementy docelowe swoje sesje zdarzeń bazy danych SQL

Poniżej przedstawiono obiekty docelowe, których można przechwycić wyniki z swoje sesje zdarzeń bazy danych SQL:

- [Celu buforu cyklicznego](https://msdn.microsoft.com/library/ff878182.aspx) -krótko przechowywane są dane zdarzeń w pamięci.
- [Element docelowy zdarzenia licznika](https://msdn.microsoft.com/library/ff878025.aspx) -zlicza wszystkie zdarzenia występujące w trakcie sesji zdarzeń rozszerzonych.
- [Element docelowy pliku zdarzenia](https://msdn.microsoft.com/library/ff878115.aspx) — zapisuje pełny buforów w kontenerze usługi Azure Storage.

[Śledzenie zdarzeń dla Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) interfejsu API nie jest dostępna dla rozszerzonych zdarzeń w bazie danych SQL.

## <a name="restrictions"></a>Ograniczenia

Istnieje kilka różnic związanych z zabezpieczeniami, befitting w środowisku chmury, bazy danych SQL Database:

- Rozszerzone zdarzenia są oparte na modelu jednej dzierżawy izolacji. Sesji zdarzeń w jednej bazie danych nie może uzyskać dostęp do danych lub zdarzenia z innej bazy danych.
- Nie mogą wystawiać **tworzenie zdarzeń sesji** instrukcji w kontekście **wzorca** bazy danych.

## <a name="permission-model"></a>Model uprawnień

Konieczne jest posiadanie **kontroli** uprawnień w bazie danych do wysyłania **tworzenie zdarzeń sesji** instrukcji. Właściciel bazy danych (dbo) ma **kontroli** uprawnień.

### <a name="storage-container-authorizations"></a>Autoryzacje kontenera magazynu

Token sygnatury dostępu Współdzielonego, możesz wygenerować dla kontenera usługi Azure Storage, należy określić **rwl** uprawnienia. **Rwl** wartość zawiera następujące uprawnienia:

- Odczyt
- Zapisywanie
- List

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Istnieją scenariusze, w którym intensywne użycie zdarzeń rozszerzonych może wzrosnąć active więcej pamięci niż jest w dobrej kondycji dla całego systemu. W związku z tym system Azure SQL Database dynamicznie ustawia i dostosowuje limitów ilości aktywnej pamięci, który może być zgromadzonych przez sesji zdarzeń. Wiele czynników konstrukcyjnym dynamicznych obliczeń.

Jeśli zostanie wyświetlony komunikat o błędzie stwierdzający, że zostało wymuszone maksymalnej pamięci, są niektóre akcje naprawcze, które można wykonać:

- Uruchom mniejszą liczbę sesji jednoczesnych zdarzeń.
- Za pomocą usługi **Utwórz** i **ALTER** instrukcji dla sesji zdarzeń zmniejszyć ilość pamięci, należy określić w **MAX\_pamięci** klauzuli.

### <a name="network-latency"></a>Opóźnienie sieci

**Plik zdarzeń** docelowy może wystąpić opóźnienie sieci lub błędów podczas utrwalanie danych obiektów blob usługi Azure Storage. Może się wydłużyć innych zdarzeń w bazie danych SQL, gdy będą oni czekać do komunikacji sieciowej zakończyć. To opóźnienie może zmniejszyć obciążenie.

- Aby zmniejszyć to zagrożenie wydajności, należy unikać ustawienia **EVENT_RETENTION_MODE** opcję **NO_EVENT_LOSS** własnych definicji sesji zdarzeń.

## <a name="related-links"></a>Powiązane linki

- [Używanie programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Polecenia cmdlet usługi Azure Storage](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Przy użyciu programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera szczegółowe informacje na temat programu PowerShell i usługi Azure Storage.
- [Jak używać magazynu obiektów Blob w języku .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Wpisy na blogu Jonathana Kehayias o rozszerzonych zdarzeniach w programie Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure *aktualizacji usługi* strony sieci Web, zawężony za pomocą parametru do usługi Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Inne tematy przykładowy kod dla rozszerzonych zdarzeń są dostępne poniższe linki. Jednak musi regularnie sprawdzać dowolnego przykładu, aby zobaczyć, czy plik jest przeznaczony dla programu Microsoft SQL Server i usługi Azure SQL Database. Następnie można zdecydować, czy drobne zmiany są potrzebne do uruchomienia przykładu.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
