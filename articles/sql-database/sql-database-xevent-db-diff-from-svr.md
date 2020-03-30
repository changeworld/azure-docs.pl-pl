---
title: Zdarzenia rozszerzone
description: W tym artykule opisano zdarzenia rozszerzone (XEvents) w usłudze Azure SQL Database i jak sesje zdarzeń różnią się nieznacznie od sesji zdarzeń w programie Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213959"
---
# <a name="extended-events-in-sql-database"></a>Zdarzenia rozszerzone w bazie danych SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

W tym temacie wyjaśniono, jak implementacja zdarzeń rozszerzonych w usłudze Azure SQL Database jest nieco inna w porównaniu do zdarzeń rozszerzonych w programie Microsoft SQL Server.

- Baza danych SQL V12 uzyskała funkcję rozszerzonych zdarzeń w drugiej połowie kalendarza 2015.
- SQL Server miał rozszerzone zdarzenia od 2008 roku.
- Zestaw funkcji zdarzeń rozszerzonych w bazie danych SQL jest niezawodnym podzbiorem funkcji programu SQL Server.

*XEvents* to nieformalny pseudonim, który jest czasami używany do "rozszerzonych wydarzeń" w blogach i innych nieformalnych miejscach.

Dodatkowe informacje dotyczące zdarzeń rozszerzonych dla usługi Azure SQL Database i Microsoft SQL Server są dostępne pod adresem:

- [Szybki start: rozszerzone zdarzenia w programie SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Zdarzenia rozszerzone](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Wymagania wstępne

W tym temacie założono, że masz już jakąś wiedzę na temat:

- [Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozszerzone zdarzenia](https://msdn.microsoft.com/library/bb630282.aspx) w programie Microsoft SQL Server.

- Większość naszej dokumentacji dotyczącej zdarzeń rozszerzonych dotyczy zarówno programu SQL Server, jak i bazy danych SQL.

Uprzednia ekspozycja na następujące elementy jest przydatna przy wyborze pliku zdarzenia jako [obiektu docelowego:](#AzureXEventsTargets)

- [Usługa Usługi Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Korzystanie z usługi Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera kompleksowe informacje na temat programu PowerShell i usługi Azure Storage.

## <a name="code-samples"></a>Przykłady kodu

Tematy pokrewne zawierają dwie przykłady kodu:


- [Kod docelowy buforu pierścieniowego dla zdarzeń rozszerzonych w bazie danych SQL](sql-database-xevent-code-ring-buffer.md)
    - Krótki prosty skrypt Transact-SQL.
    - Podkreślamy w przykładowym temacie kodu, że po zakończeniu pracy z docelowym buforem pierścieniowym `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` należy zwolnić jego zasoby, wykonując instrukcję alter-drop. Później można dodać kolejne wystąpienie `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`buforu pierścieniowego przez .


- [Kod docelowy pliku zdarzeń dla zdarzeń rozszerzonych w bazie danych SQL](sql-database-xevent-code-event-file.md)
    - Faza 1 to program PowerShell do utworzenia kontenera usługi Azure Storage.
    - Faza 2 to Transact-SQL, który używa kontenera usługi Azure Storage.

## <a name="transact-sql-differences"></a>Różnice między transact-SQL


- Podczas wykonywania polecenia [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) na programie SQL Server należy użyć klauzuli ON **SERVER.** Ale w bazie danych SQL zamiast tego używasz klauzuli **ON DATABASE.**


- Klauzula **ON DATABASE** ma również zastosowanie do poleceń [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) i DROP EVENT [SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL.


- Najlepszym rozwiązaniem jest uwzględnienie opcji sesji zdarzenia **STARTUP_STATE = ON** w instrukcjach CREATE EVENT **SESSION** lub ALTER **EVENT SESSION.**
    - = **On** wartość obsługuje automatyczne ponowne uruchomienie po ponownej konfiguracji logicznej bazy danych z powodu pracy awaryjnej.

## <a name="new-catalog-views"></a>Nowe widoki katalogu

Funkcja zdarzeń rozszerzonych jest obsługiwana przez kilka [widoków katalogu](https://msdn.microsoft.com/library/ms174365.aspx). Widoki wykazu informują o *metadanych lub definicjach* sesji zdarzeń utworzonych przez użytkownika w bieżącej bazie danych. Widoki nie zwracają informacji o wystąpieniach aktywnych sesji zdarzeń.

| Nazwa<br/>widok katalogu | Opis |
|:--- |:--- |
| **sys.database_event_session_actions** |Zwraca wiersz dla każdej akcji dla każdego zdarzenia sesji zdarzenia. |
| **sys.database_event_session_events** |Zwraca wiersz dla każdego zdarzenia w sesji zdarzenia. |
| **sys.database_event_session_fields** |Zwraca wiersz dla każdej kolumny można dostosować, który został jawnie ustawiony na zdarzenia i obiekty docelowe. |
| **sys.database_event_session_targets** |Zwraca wiersz dla każdego obiektu docelowego zdarzenia dla sesji wydarzenia. |
| **sys.database_event_sessions** |Zwraca wiersz dla każdej sesji zdarzeń w bazie danych SQL Database. |

W programie Microsoft SQL Server podobne widoki wykazu mają nazwy, które zawierają *serwer zamiast\_ * *.database\_*. Wzór nazwy jest jak **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nowe dynamiczne widoki zarządzania [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Usługa Azure SQL Database ma [dynamiczne widoki zarządzania (DMV),](https://msdn.microsoft.com/library/bb677293.aspx) które obsługują zdarzenia rozszerzone. DmVs informują o *aktywnych* sesjach wydarzeń.

| Nazwa DMV | Opis |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Zwraca informacje o akcjach sesji zdarzeń. |
| **sys.dm_xe_database_session_events** |Zwraca informacje o zdarzeniach sesji. |
| **sys.dm_xe_database_session_object_columns** |Pokazuje wartości konfiguracji dla obiektów, które są powiązane z sesją. |
| **sys.dm_xe_database_session_targets** |Zwraca informacje o celach sesji. |
| **sys.dm_xe_database_sessions** |Zwraca wiersz dla każdej sesji zdarzenia, która jest objęta zakresem bieżącej bazy danych. |

W programie Microsoft SQL Server podobne * \_* widoki wykazu są nazywane bez części bazy danych nazwy, na przykład:

- **sys.dm_xe_sessions**, zamiast nazwy<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs wspólne dla obu
W przypadku zdarzeń rozszerzonych istnieją dodatkowe moduły DMV, które są wspólne zarówno dla usługi Azure SQL Database, jak i programu Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **Sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Znajdowanie dostępnych rozszerzonych zdarzeń, akcji i obiektów docelowych

Można uruchomić prosty SQL **SELECT,** aby uzyskać listę dostępnych zdarzeń, akcji i docelowych.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Obiekty docelowe dla sesji zdarzeń bazy danych SQL

Oto obiekty docelowe, które mogą przechwytywać wyniki z sesji zdarzeń w bazie danych SQL:

- [Docelowy bufor pierścieniowy](https://msdn.microsoft.com/library/ff878182.aspx) — krótko przechowuje dane zdarzeń w pamięci.
- [Miejsce docelowe licznika zdarzeń](https://msdn.microsoft.com/library/ff878025.aspx) — zlicza wszystkie zdarzenia, które występują podczas rozszerzonej sesji zdarzeń.
- [Miejsce docelowe pliku zdarzeń](https://msdn.microsoft.com/library/ff878115.aspx) — zapisuje pełne bufory w kontenerze usługi Azure Storage.

Interfejs API [śledzenia zdarzeń dla systemu Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) nie jest dostępny dla zdarzeń rozszerzonych w bazie danych SQL.

## <a name="restrictions"></a>Ograniczenia

Istnieje kilka różnic związanych z zabezpieczeniami, które przywodzą na myśl środowisko chmury bazy danych SQL Database:

- Rozszerzone zdarzenia są oparte na modelu izolacji pojedynczej dzierżawy. Sesja zdarzeń w jednej bazie danych nie może uzyskać dostępu do danych lub zdarzeń z innej bazy danych.
- Nie można wydać instrukcji **CREATE EVENT SESSION** w kontekście **głównej** bazy danych.

## <a name="permission-model"></a>Model uprawnień

Aby wydać instrukcję CREATE EVENT **SESSION,** musisz mieć uprawnienie **Control** w bazie danych. Właściciel bazy danych (dbo) ma uprawnienie **Control.**

### <a name="storage-container-authorizations"></a>Autoryzacje kontenerów magazynu

Token sygnatury dostępu Współdzielonego, który generujesz dla kontenera usługi Azure Storage, musi określać **rwl** dla uprawnień. Wartość **rwl** zapewnia następujące uprawnienia:

- Odczyt
- Zapisywanie
- List

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Istnieją scenariusze, w których intensywne korzystanie z rozszerzonych zdarzeń może gromadzić więcej aktywnej pamięci niż jest w dobrej kondycji dla całego systemu. W związku z tym system azure SQL Database dynamicznie ustawia i dostosowuje limity ilości aktywnej pamięci, które mogą być gromadzone przez sesję zdarzenia. Wiele czynników przechodzi do obliczeń dynamicznych.

Jeśli zostanie wyświetlony komunikat o błędzie informujący, że maksymalna pamięć została wymuszona, niektóre działania naprawcze, które można podjąć, to:

- Uruchom mniej równoczesnych sesji zdarzeń.
- Za pomocą instrukcji **CREATE** i **ALTER** dla sesji zdarzeń zmniejsz ilość pamięci określoną w klauzuli **\_MAX MEMORY.**

### <a name="network-latency"></a>Opóźnienie sieci

Obiekt **docelowy pliku zdarzeń** może wystąpić opóźnienie sieci lub błędy podczas utrwalania danych do obiektów blob usługi Azure Storage. Inne zdarzenia w bazie danych SQL mogą być opóźnione podczas oczekiwania na zakończenie komunikacji sieciowej. To opóźnienie może spowolnić obciążenie pracą.

- Aby zmniejszyć to ryzyko wydajności, należy unikać ustawiania **opcji EVENT_RETENTION_MODE,** aby **NO_EVENT_LOSS** w definicjach sesji zdarzeń.

## <a name="related-links"></a>Powiązane linki

- [Korzystanie z programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Polecenia cmdlet usługi Azure Storage](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Korzystanie z usługi Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera kompleksowe informacje na temat programu PowerShell i usługi Azure Storage.
- [Jak korzystać z magazynu obiektów Blob z platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [TWORZENIE SESJI ZDARZEŃ (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias blogu o rozszerzonych zdarzeń w programie Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Strona sieci Web *Aktualizacje usługi* Azure, zawężona według parametru do bazy danych SQL usługi Azure:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Inne przykładowe tematy kodu dla zdarzeń rozszerzonych są dostępne pod następującymi łączami. Jednak należy rutynowo sprawdzić dowolny przykład, aby zobaczyć, czy przykładowe obiekty docelowe Microsoft SQL Server w porównaniu do bazy danych SQL Azure. Następnie można zdecydować, czy do uruchomienia próbki są potrzebne niewielkie zmiany.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
