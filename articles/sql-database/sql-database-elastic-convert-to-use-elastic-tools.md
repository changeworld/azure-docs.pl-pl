---
title: Migrowanie istniejących baz danych do skalowania w poziomie | Dokumentacja firmy Microsoft
description: Konwertuj podzielonej baz danych za pomocą narzędzi elastycznej bazy danych przez utworzenie niezależnego fragmentu menedżera map
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 41652717417f47858698b80eac83a4022dcfe13b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrowanie istniejących baz danych do skalowania w poziomie
Łatwe zarządzanie istniejących skalowalnych w poziomie podzielonej baz danych za pomocą narzędzi bazy danych usługi Azure SQL Database (takich jak [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md)). Najpierw przekonwertuj istniejącego zestawu baz danych do użycia [menedżera map niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Przegląd
Aby przeprowadzić migrację z istniejącej bazy danych podzielonej: 

1. Przygotowanie [bazy danych Menedżera mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).
2. Utwórz mapę niezależnego fragmentu.
3. Przygotuj poszczególnych fragmentów.  
4. Dodać mapowania do mapy niezależnego fragmentu.

Te techniki można implementować przy użyciu [biblioteki klienta .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), lub skryptów programu PowerShell, zobacz [Azuresql DB - skrypty narzędzi elastycznej bazy danych](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Przykłady w tym miejscu użyć skryptów środowiska PowerShell.

Aby uzyskać więcej informacji o ShardMapManager, zobacz [zarządzania mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md). Omówienie narzędzi elastycznej bazy danych, zobacz [Przegląd funkcji elastycznej bazy danych](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Przygotowanie bazy danych Menedżera niezależnego fragmentu mapy
Menedżer mapy niezależnego fragmentu jest specjalne bazy danych, która zawiera dane do zarządzania bazami danych skalowalnych w poziomie. Użyj istniejącej bazy danych lub Utwórz nową bazę danych. Bazy danych, działają jako Menedżer mapy niezależnego fragmentu nie powinien być tej samej bazy danych jako niezależnego fragmentu. Skrypt programu PowerShell nie tworzy bazy danych. 

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1: tworzenie niezależnych menedżera map
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Aby pobrać niezależnego fragmentu menedżera map
Po utworzeniu można pobrać manager mapy niezależnego fragmentu z tego polecenia cmdlet. Ten krok jest niezbędny, za każdym razem, gdy musisz użyć obiektu ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Krok 2: Tworzenie mapy niezależnego fragmentu
Wybierz typ mapy niezależnego fragmentu do utworzenia. Wybór zależy od architektury bazy danych: 

1. Pojedynczej dzierżawy na bazę danych (terminów, zobacz [słownik](sql-database-elastic-scale-glossary.md).) 
2. Wiele dzierżaw dla jednej bazy danych (dwa typy):
   1. Mapowanie list
   2. Mapowanie zakresu

Model pojedynczego dzierżawcy można utworzyć **mapowania listy** mapy niezależnego fragmentu. Model pojedynczej dzierżawy przypisuje jednej bazy danych dla każdego dzierżawcy. Jest to efektywne modelu dla deweloperów SaaS, ponieważ takie rozwiązanie upraszcza zarządzanie.

![Mapowanie list][1]

Modelu wielodostępnym przypisuje kilka dzierżaw do pojedynczej bazy danych (i grup dzierżawcy mogą rozpowszechniają wielu baz danych). Jeśli potrzebujesz każdego dzierżawcy, aby korzystać z niewielkie zbiory danych, należy użyć tego modelu. W tym modelu Przypisz zakres dzierżawcy do bazy danych przy użyciu **mapowanie zakresu**. 

![Mapowanie zakresu][2]

Lub możesz wdrożyć model bazy danych wielu dzierżawców przy użyciu *mapowanie list* można przypisać wielu dzierżawców do pojedynczej bazy danych. Na przykład DB1 jest używany do przechowywania informacji na temat dzierżawy ID 1 i 5 i bazy danych DB2 przechowuje dane dla dzierżawy 7 i dzierżawcy 10. 

![Wiele dzierżaw dla jednej bazy danych][3] 

**W oparciu o wybór, wybierz jedną z następujących opcji:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opcja 1: Tworzenie mapy niezależnego fragmentu mapowania listy
Tworzenie mapy niezależnego fragmentu przy użyciu obiektu ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opcja 2: Tworzenie mapy niezależnego fragmentu mapowania zakresu
Aby korzystać z tego wzorca mapowania, wartości Identyfikatora dzierżawy musi być ciągłe zakresy i można mieć przerwa w zakresach przez pominięcie zakres podczas tworzenia bazy danych.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Opcja 3: Lista mapowania na pojedynczej bazy danych
Konfigurowanie ten wzorzec wymaga również tworzenia mapy listy jak pokazano w kroku 2, opcja 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3: Przygotowanie poszczególnych odłamków
Dodaj każdy identyfikator niezależnego fragmentu (baza danych) z menedżerem mapy niezależnego fragmentu. Do przechowywania informacji o mapowaniu to przygotowuje pojedynczych baz danych. Tej metody należy wykonać na każdym niezależnego fragmentu.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Krok 4: Dodawanie mapowania
Dodanie mapowań zależy od rodzaju mapy niezależnego fragmentu, który został utworzony. Jeśli utworzono mapy listy, możesz dodać mapowania listy. Jeśli utworzono map zakres, należy dodać mapowania zakresu.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opcja 1: mapowanie danych dla mapowania listy
Mapowania danych przez dodanie mapowania listy dla każdego dzierżawcy.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opcja 2: mapowanie danych dla mapowania zakresu
Dodaj mapowanie zakresu dla wszystkich dzierżawy identyfikator zakresu - skojarzenia bazy danych:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Krok 4 — opcja 3: mapowanie danych dla wielu dzierżawców w pojedynczej bazy danych
Dla każdego dzierżawcy Uruchom Add-ListMapping (opcja 1). 

## <a name="checking-the-mappings"></a>Sprawdzanie mapowania
Informacje o istniejących odłamków i mapowania skojarzonych z nimi mogą być przeszukiwane przy użyciu następujących poleceń:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Podsumowanie
Po zakończeniu instalacji można rozpocząć korzystanie z biblioteki klienta elastycznej bazy danych. Można również użyć [routingu zależne od danych](sql-database-elastic-scale-data-dependent-routing.md) i [zapytania wielu niezależnych](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Kolejne kroki
Pobierz skrypty programu PowerShell z [bazy danych elastyczne bazy danych SQL Azure narzędzi skryptów](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Narzędzia są również w witrynie GitHub: [Azure/elastyczna db-tools](https://github.com/Azure/elastic-db-tools).

Użyj narzędzia do scalania podziału do przenoszenia danych z modelu wielodostępnym lub do pojedynczej dzierżawy modelu. Zobacz [narzędzia do scalania podziału](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Zasoby dodatkowe
Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

## <a name="questions-and-feature-requests"></a>Pytania i żądania funkcji
Odpowiedzi na pytania, użyj [forum bazy danych SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) i funkcja żądań, dodaj je do [forum opinii bazy danych SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

