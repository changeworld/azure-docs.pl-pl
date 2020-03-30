---
title: 'Znane problemy: Migracja z oracle do bazy danych Platformy Azure dla postgreSQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej o znanych problemach i ograniczeniach migracji z migracji online z Oracle do usługi Azure Database dla serwera PostgreSQL-Single przy użyciu usługi migracji bazy danych Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235245"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Znane problemy/ograniczenia migracji z migracjami online z Oracle do usługi Azure DB dla serwera PostgreSQL-Single

Znane problemy i ograniczenia związane z migracjami online z Oracle do usługi Azure Database dla serwera PostgreSQL-Single są opisane w poniższych sekcjach.

## <a name="oracle-versions-supported-as-a-source-database"></a>Wersje Oracle obsługiwane jako źródłowych baz danych

Usługa migracji bazy danych platformy Azure obsługuje łączenie się z:

- Oracle w wersji 10g, 11g i 12c.
- Oracle Enterprise, Standard, Express i Personal Edition.

Usługa migracji bazy danych platformy Azure nie obsługuje łączenia się z bazami danych kontenerów z wieloma dzierżawami (CDB).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Wersje PostgreSQL obsługiwane jako docelowa baza danych

Usługa migracji bazy danych platformy Azure obsługuje migracje do bazy danych usługi Azure database dla serwera PostgreSQL-Single w wersji 9.5, 9.6, 10 i 11. Zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL bieżące](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) informacje na temat obsługi wersji w usłudze Azure Database dla serwera PostgreSQL-Single.

## <a name="datatype-limitations"></a>Ograniczenia typu danych

Następujące typy danych **nie** zostaną poddane migracji:

- Bfile
- Rowid
- REF
- Urowid
- ANYDATA
- SDO_GEOMETRY
- Tabele zagnieżdżone
- Typy danych zdefiniowane przez użytkownika
- Uwagi
- Kolumny wirtualne
- Widoki materializowane na podstawie kolumny ROWID

Ponadto puste kolumny BLOB/CLOB są mapowane na wartość NULL w obiekcie docelowym.

## <a name="lob-limitations"></a>Ograniczenia LOB

- Gdy tryb LOB o ograniczonym rozmiarze jest włączony, puste lejki w źródle Oracle są replikowane jako wartości NULL.
- Długie nazwy obiektów (ponad 30 bajtów) nie są obsługiwane.
- Dane w kolumnie LONG i LONG RAW nie mogą przekraczać 64k. Wszelkie dane powyżej 64k zostaną obcięty.
- Tylko w programie Oracle 12 wszelkie zmiany w kolumnach lob nie są obsługiwane (migrowane).
- Aktualizowanie do kolumn XMLTYPE i LOB nie są obsługiwane (migrowane).

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

- Klienci muszą korzystać z SYSDBA, aby połączyć się z Oracle.
- Zmiany danych wynikające z operacji partycji/podpodziału (ADD, DROP, EXCHANGE i TRUNCATE) nie zostaną zmigrowane i mogą spowodować następujące błędy:
  - W przypadku operacji ADD aktualizacje i usunięcia dodanych danych mogą zwracać ostrzeżenie "0 wierszy, których dotyczy problem".
  - W przypadku operacji DROP i TRUNCATE nowe wstawienia mogą powodować błędy "duplikatów".
  - W przypadku operacji PROGRAMU EXCHANGE może wystąpić ostrzeżenie o "0 wierszy, których dotyczy problem", jak i błędy "duplikaty".
- Tabel, których nazwy zawierają apostrofy nie mogą być replikowane.
