---
title: Migrowanie istniejących baz danych w celu skalowania w poziomie | Dokumenty firmy Microsoft
description: Konwertowanie podzielonych baz danych w celu użycia narzędzi elastycznej bazy danych przez utworzenie menedżera map fragmentów
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c776f4ac09626f0abd1eb754cde391a1c5447627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421225"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrowanie istniejących baz danych w celu skalowania w poziomie

Łatwe zarządzanie istniejącymi podzielonymi na poziomie bazami danych podzielonymi na fragmenty za pomocą narzędzi bazy danych usługi Azure SQL Database (takich jak [biblioteka klienta elastycznej bazy danych).](sql-database-elastic-database-client-library.md) Najpierw przekonwertuj istniejący zestaw baz danych, aby użyć [menedżera map niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).

## <a name="overview"></a>Omówienie

Aby przeprowadzić migrację istniejącej podzielonej bazy danych:

1. Przygotuj [bazę danych menedżera map niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).
2. Utwórz mapę niezależnego fragmentu.
3. Przygotuj poszczególne odłamki.  
4. Dodaj mapowania do mapy niezależnego fragmentu.

Techniki te można zaimplementować przy użyciu [biblioteki klienta programu .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)lub skryptów programu PowerShell znalezionych w [programie Azure SQL DB — skrypty narzędzi elastycznej bazy danych.](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) Przykłady w tym miejscu użyć skryptów programu PowerShell.

Aby uzyskać więcej informacji na temat narzędzia ShardMapManager, zobacz [Zarządzanie mapami niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md). Aby zapoznać się z omówieniem narzędzi elastycznej bazy danych, zobacz [omówienie funkcji elastycznej bazy danych](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Przygotowywanie bazy danych menedżera map niezależnego fragmentu

Menedżer mapy niezależnego fragmentu to specjalna baza danych zawierająca dane do zarządzania skalowanymi w poziomie baz danych. Można użyć istniejącej bazy danych lub utworzyć nową bazę danych. Baza danych działająca jako menedżer mapy niezależnego fragmentu nie powinna być tą samą bazą danych co niezależnego fragmentu. Skrypt programu PowerShell nie tworzy bazy danych dla Ciebie.

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1: tworzenie menedżera map niezależnego fragmentu

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Aby pobrać menedżera mapy niezależnego fragmentu

Po utworzeniu można pobrać menedżera mapy niezależnego fragmentu za pomocą tego polecenia cmdlet. Ten krok jest potrzebny za każdym razem, gdy trzeba użyć Obiektu ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Krok 2: tworzenie mapy odłamków

Wybierz typ mapy niezależnego fragmentu do utworzenia. Wybór zależy od architektury bazy danych:

1. Pojedyncza dzierżawa na bazę danych (aby uzyskać warunki, zobacz [słowniczek](sql-database-elastic-scale-glossary.md).)
2. Wielu dzierżawców na bazę danych (dwa typy):
   1. Mapowanie listy
   2. Mapowanie zasięgu

W przypadku modelu z jedną dzierżawą utwórz mapę niezależnego fragmentu **mapowania listy.** Model z jedną dzierżawą przypisuje jedną bazę danych na dzierżawę. Jest to skuteczny model dla deweloperów SaaS, ponieważ upraszcza zarządzanie.

![Mapowanie listy][1]

Model wielodostępny przypisuje kilku dzierżawców do pojedynczej bazy danych (i można dystrybuować grupy dzierżawców w wielu bazach danych). Użyj tego modelu, gdy oczekujesz, że każda dzierżawa ma małe potrzeby danych. W tym modelu należy przypisać zakres dzierżawców do bazy danych przy użyciu **mapowania zakresu**.

![Mapowanie zasięgu][2]

Lub można zaimplementować model bazy danych z wieloma dzierżawami przy użyciu *mapowania listy,* aby przypisać wielu dzierżawców do pojedynczej bazy danych. Na przykład DB1 służy do przechowywania informacji o identyfikatorze dzierżawy 1 i 5, a DB2 przechowuje dane dla dzierżawy 7 i dzierżawy 10.

![Wielu dzierżawców w jednej db][3]

**Na podstawie wyboru wybierz jedną z następujących opcji:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opcja 1: tworzenie mapy niezależnego fragmentu dla mapowania listy

Utwórz mapę niezależnego fragmentu przy użyciu obiektu ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opcja 2: tworzenie mapy niezależnego fragmentu dla mapowania zakresu

Aby korzystać z tego wzorca mapowania, wartości identyfikatora dzierżawy muszą być zakresami ciągłymi i jest dopuszczalne, aby mieć przerwę w zakresach, pomijając zakres podczas tworzenia baz danych.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opcja 3: Lista mapowań w pojedynczej bazie danych

Skonfigurowanie tego wzorca wymaga również utworzenia mapy listy, jak pokazano w kroku 2, opcja 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3: Przygotowanie pojedynczych odłamków

Dodaj każdy fragment (bazy danych) do menedżera mapy niezależnego fragmentu. Spowoduje to przygotowanie poszczególnych baz danych do przechowywania informacji mapowania. Wykonaj tę metodę na każdym niezależniu.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Krok 4: Dodawanie mapowań

Dodawanie mapowań zależy od rodzaju mapy niezależnego fragmentu, który został utworzony. Jeśli utworzono mapę listy, należy dodać mapowania list. Jeśli utworzono mapę zakresu, należy dodać mapowania zakresów.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opcja 1: mapowanie danych dla mapowania listy

Mapowanie danych przez dodanie mapowania listy dla każdej dzierżawy.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opcja 2: mapowanie danych dla mapowania zakresu

Dodaj mapowania zakresu dla wszystkich zakresów identyfikatorów dzierżawców — skojarzenia bazy danych:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Krok 4 opcja 3: mapowanie danych dla wielu dzierżawców w indywidualnej bazie danych

Dla każdej dzierżawy uruchom Add-ListMapping (opcja 1).

## <a name="checking-the-mappings"></a>Sprawdzanie mapowań

Informacje o istniejących fragmentach i mapowaniach z nimi skojarzonych można wyszukiwać za pomocą następujących poleceń:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Podsumowanie

Po zakończeniu instalacji można rozpocząć korzystanie z biblioteki klienta elastycznej bazy danych. Można również użyć [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md) i [zapytania wieloprzędowego](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Następne kroki

Pobierz skrypty programu PowerShell ze [skryptów narzędzi azure SQL DB-Elastic Database](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Narzędzia są również na GitHub: [Azure/ elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Narzędzie scalanie podziału służy do przenoszenia danych do lub z modelu wielodostępnego do modelu pojedynczej dzierżawy. Zobacz [Narzędzie scalania podziału](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

## <a name="questions-and-feature-requests"></a>Pytania i prośby o funkcje

W przypadku pytań należy użyć [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) i żądań funkcji, dodaj je do forum opinii bazy danych [SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
