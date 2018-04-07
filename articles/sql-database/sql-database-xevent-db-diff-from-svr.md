---
title: Rozszerzony zdarzeń w bazie danych SQL | Dokumentacja firmy Microsoft
description: Opisuje zdarzeń rozszerzonych (systemów Xevent) w bazie danych SQL Azure oraz jak sesji zdarzeń różnić się nieznacznie od sesji zdarzeń w programie Microsoft SQL Server.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 4a1a8a332628e79972e7c03dbc2ac839f244a002
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="extended-events-in-sql-database"></a>Rozszerzone zdarzenia w bazie danych SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

W tym temacie wyjaśniono, jak implementacja zdarzeń rozszerzonych w bazie danych SQL Azure jest nieco inne w porównaniu do zdarzeń rozszerzonych w programie Microsoft SQL Server.

- Bazy danych SQL V12 uzyskane funkcji rozszerzone zdarzenia w ciągu sekundy połowy kalendarza 2015.
- SQL Server miał zdarzeń rozszerzonych od 2008.
- Zestaw funkcji rozszerzonej zdarzeń w bazie danych SQL jest podzbiorem niezawodne funkcje na serwerze SQL.

*Systemu XEvents* jest nieformalne pseudonimu, czasami używanego dla "zdarzeń rozszerzonych" blogów i innych nieformalne lokalizacji.

Dodatkowe informacje o rozszerzonych zdarzeń dla usługi Azure SQL Database i programu Microsoft SQL Server są dostępne pod adresem:

- [Szybki Start: Rozszerzone zdarzenia w programie SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Rozszerzone zdarzenia](http://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Wymagania wstępne

W tym temacie przyjęto założenie, że masz już pewne informacje dotyczące:

- [Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozszerzone zdarzenia](http://msdn.microsoft.com/library/bb630282.aspx) w programie Microsoft SQL Server.

- Zbiorczego naszej dokumentacji dotyczących zdarzeń rozszerzonych dotyczą zarówno programu SQL Server i bazy danych SQL.

Przed ujawnieniem do następujących elementów jest przydatne w przypadku wybrania pliku zdarzeń jako [docelowej](#AzureXEventsTargets):

- [Usługi magazynu Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Przy użyciu programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera szczegółowe informacje na temat środowiska PowerShell i usługi magazynu Azure.

## <a name="code-samples"></a>Przykłady kodu

Powiązanych tematach przedstawiono dwa przykłady kodu:


- [Pierścienia kodu docelowego buforu dla zdarzeń rozszerzonych w bazie danych SQL](sql-database-xevent-code-ring-buffer.md)
    - Krótki prostego skryptu języka Transact-SQL.
    - Możemy wyróżnić w temacie przykładowy kod, że po zakończeniu z elementem docelowym pierścień buforu, należy zwolnić zasoby jego wykonując upuść alter `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instrukcji. Później można dodać inne wystąpienie buforu pierścień przez `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Kod docelowego pliku zdarzenia rozszerzonego zdarzeń w bazie danych SQL](sql-database-xevent-code-event-file.md)
    - Faza 1 jest program PowerShell można utworzyć kontenera magazynu Azure.
    - Faza 2 jest języka Transact-SQL, który używa kontenera magazynu Azure.

## <a name="transact-sql-differences"></a>Różnice w języku Transact-SQL


- Podczas wykonywania [tworzenie zdarzeń sesji](http://msdn.microsoft.com/library/bb677289.aspx) polecenia na serwerze SQL, należy użyć **ON SERVER** klauzuli. W bazie danych SQL korzystasz, ale **bazy danych na** klauzuli zamiast tego.


- **Bazy danych na** dotyczą również klauzuli [ALTER zdarzenia sesji](http://msdn.microsoft.com/library/bb630368.aspx) i [PORZUCIĆ zdarzenia sesji](http://msdn.microsoft.com/library/bb630257.aspx) poleceń języka Transact-SQL.


- Najlepszym rozwiązaniem jest uwzględnienie opcja sesji zdarzeń **STARTUP_STATE = ON** w Twojej **tworzenie zdarzeń sesji** lub **ALTER zdarzenia sesji** instrukcje.
    - **= ON** wartość obsługuje automatyczne ponowne uruchomienie po ponownej konfiguracji logicznej bazy danych z powodu pracy awaryjnej.

## <a name="new-catalog-views"></a>Nowych widoków katalogów

Funkcja zdarzeń rozszerzonych jest obsługiwany przez kilka [widoków wykazu](http://msdn.microsoft.com/library/ms174365.aspx). Widoków wykazu informujące o *metadanych lub definicji* sesji zdarzeń utworzonych przez użytkownika w bieżącej bazie danych. Widoki nie zwracają informacje na temat wystąpień zdarzeń aktywnych sesji.

| Nazwa<br/>Przeglądanie katalogu | Opis |
|:--- |:--- |
| **sys.database_event_session_actions** |Zwraca wiersz dla każdej akcji każdego zdarzenia sesji zdarzeń. |
| **sys.database_event_session_events** |Zwraca wiersz dla każdego zdarzenia w sesji zdarzeń. |
| **sys.database_event_session_fields** |Zwraca wiersz dla każdego dostosować w kolumnie jawnie ustawiona na zdarzenia i obiektów docelowych. |
| **sys.database_event_session_targets** |Zwraca wiersz dla każdego obiektu docelowego zdarzeń dla sesji zdarzeń. |
| **sys.database_event_sessions** |Zwraca wiersz dla każdej sesji zdarzeń w bazie danych SQL Database. |

Program Microsoft SQL Server, podobne widoków wykazu mają nazwy, które obejmują *.server\_*  zamiast *.database\_*. Wzorzec nazwy przypomina **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Nowe dynamicznych widoków zarządzania [(widoków DMV)](http://msdn.microsoft.com/library/ms188754.aspx)

Baza danych SQL Azure ma [dynamicznych widoków zarządzania (widoków DMV)](http://msdn.microsoft.com/library/bb677293.aspx) obsługujących zdarzeń rozszerzonych. Widoków DMV informujące o *active* sesji zdarzeń.

| Nazwa DMV | Opis |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Zwraca informacje o akcjach sesji zdarzeń. |
| **sys.dm_xe_database_session_events** |Zwraca informacje dotyczące sesji zdarzeń. |
| **sys.dm_xe_database_session_object_columns** |Zawiera wartości konfiguracji dla obiektów, które są powiązane z sesją. |
| **sys.dm_xe_database_session_targets** |Zwraca informacje na temat celów sesji. |
| **sys.dm_xe_database_sessions** |Zwraca wiersz dla każdej sesji zdarzeń, które są ograniczone do bieżącej bazy danych. |

Program Microsoft SQL Server, podobne widoków wykazu są nazywane bez  *\_bazy danych* część nazwy, takie jak:

- **sys.dm_xe_sessions**, a nie nazwy<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Wspólne widoków DMV
Dla zdarzeń rozszerzonych istnieją dodatkowe widoków DMV, które są wspólne dla usługi Azure SQL Database i programu Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Znajdowanie dostępnych rozszerzone zdarzenia, akcje i cele

Możesz uruchomić proste SQL **wybierz** Aby uzyskać listę dostępnych zdarzeń, akcje i obiekt docelowy.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Elementów docelowych dla sesji zdarzeń z bazy danych SQL

Poniżej przedstawiono obiektów docelowych, które można przechwycić wyniki z sesji zdarzeń w bazie danych SQL:

- [Pierścień bufor docelowy](http://msdn.microsoft.com/library/ff878182.aspx) -krótko przechowuje dane zdarzenia w pamięci.
- [Docelowy licznik zdarzeń](http://msdn.microsoft.com/library/ff878025.aspx) -liczby wszystkich zdarzeń występujących podczas sesji zdarzeń rozszerzonych.
- [Docelowy plik zdarzeń](http://msdn.microsoft.com/library/ff878115.aspx) -zapisuje pełny buforów do kontenera magazynu Azure.

[Funkcji Śledzenie zdarzeń systemu Windows ()](http://msdn.microsoft.com/library/ms751538.aspx) interfejsu API nie jest dostępna dla zdarzeń rozszerzonych w bazie danych SQL.

## <a name="restrictions"></a>Ograniczenia

Istnieje kilka różnic związanych z zabezpieczeniami befitting środowiska chmury bazy danych SQL:

- Podstawą zdarzeń rozszerzonych jest model izolacji jednego dzierżawcy. Sesja zdarzeń w jednej bazie danych nie można uzyskać dostępu zdarzenia lub dane z innej bazy danych.
- Nie mogą wystawiać **tworzenie zdarzeń sesji** instrukcji w kontekście **wzorca** bazy danych.

## <a name="permission-model"></a>Model uprawnień

Musi mieć **kontroli** uprawnienia w bazie danych do wystawiania **tworzenie zdarzeń sesji** instrukcji. Właściciel bazy danych (dbo) ma **kontroli** uprawnienia.

### <a name="storage-container-authorizations"></a>Autoryzacje kontenera magazynu

Token sygnatury dostępu Współdzielonego Generowanie z kontenera magazynu Azure należy określić **rwl** uprawnienia. **Rwl** wartość zawiera następujące uprawnienia:

- Odczyt
- Zapisywanie
- List

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Istnieją scenariusze, w którym intensywne użycie zdarzeń rozszerzonych może spowodować zgromadzenie active więcej pamięci niż jest w dobrej kondycji dla całego systemu. W związku z tym system bazy danych SQL Azure dynamicznie ustawia i dostosowuje limitów ilości aktywnej pamięci, który można zgromadzonych przez usługę sesji zdarzeń. Wiele czynników przejdź do dynamicznego obliczeń.

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że maksymalna pamięci była wymuszana, są niektóre działania naprawcze, które można wykonać:

- Uruchom mniejszą liczbę sesji jednoczesnych zdarzeń.
- Za pomocą programu **Utwórz** i **ALTER** instrukcje dla sesji zdarzeń, Zmniejsz ilość pamięci na **MAX\_pamięci** klauzuli.

### <a name="network-latency"></a>Opóźnienie sieci

**Plik zdarzeń** docelowy może wystąpić opóźnienie sieci lub błędy podczas trwałych danych do obiektów blob magazynu Azure. Inne zdarzenia w bazie danych SQL mogą zostać opóźnione, gdy będą oni czekać do komunikacji sieciowej zakończyć. To opóźnienie może zmniejszyć obciążenie.

- Aby zmniejszyć to zagrożenie wydajności, należy unikać ustawienie **EVENT_RETENTION_MODE** opcji w celu **NO_EVENT_LOSS** w definicji sesji zdarzeń.

## <a name="related-links"></a>Powiązane linki

- [Używanie programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Polecenia cmdlet usługi Azure Storage](http://msdn.microsoft.com/library/dn806401.aspx)
- [Przy użyciu programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera szczegółowe informacje na temat środowiska PowerShell i usługi magazynu Azure.
- [Jak używać magazynu obiektów Blob w .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [Tworzenie sesji zdarzeń (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)
- [Wpisy blogu Kehayias Jonathanowi o rozszerzonych zdarzenia w programie Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure *aktualizacje usługi* strony sieci Web, zawężony przez parametr do bazy danych SQL Azure:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Inne tematy przykładowy kod dla zdarzeń rozszerzonych są dostępne w poniższych łączy. Regularnie należy jednak sprawdzić wszelkie przykład, aby zobaczyć, czy próbki jest przeznaczony dla programu Microsoft SQL Server i bazy danych SQL Azure. Następnie można zdecydować, czy drobne zmiany są potrzebne do uruchomienia przykładu.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
