---
title: Migrowanie istniejących baz danych do skalowania w poziomie | Dokumentacja firmy Microsoft
description: Konwertuj podzielonej na fragmenty bazy danych za pomocą narzędzi elastycznych baz danych, tworząc menedżera map fragmentów
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 49686e407b2d733c04bad31706c6c4f315bf28bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075207"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrowanie istniejących baz danych do skalowania w poziomie
Łatwe zarządzanie istniejących skalowanych w poziomie podzielonych na fragmenty baz danych przy użyciu narzędzia graficzne bazy danych Azure SQL Database (takie jak [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)). Najpierw przekonwertuj istniejącego zestawu baz danych do użycia [Menedżera mapowań fragmentów](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Omówienie
Aby przeprowadzić migrację istniejącej bazy danych podzielonej na fragmenty: 

1. Przygotowanie [bazy danych Menedżera mapowań fragmentów](sql-database-elastic-scale-shard-map-management.md).
2. Tworzenie mapy fragmentów.
3. Przygotuj poszczególne fragmenty.  
4. Dodaj mapowania do mapy fragmentów.

Techniki te można zaimplementować przy użyciu [biblioteki klienta .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), lub skryptów programu PowerShell adrese [bazy danych SQL Azure — skrypty narzędzi elastycznej bazy danych](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Przykłady w tym miejscu za pomocą skryptów programu PowerShell.

Aby uzyskać więcej informacji na temat ShardMapManager zobacz [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md). Aby uzyskać omówienie narzędzi elastycznej bazy danych, zobacz [omówienie funkcji Elastic Database](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Przygotowywanie bazy danych Menedżera mapowań fragmentów
Menedżera mapowań fragmentów to specjalne bazy danych, która zawiera dane do zarządzania bazami danych skalowanych w poziomie. Użyj istniejącej bazy danych lub Utwórz nową bazę danych. Działający jako menedżera mapowań fragmentów bazy danych nie może być tej samej bazy danych jako fragmentów. Skrypt programu PowerShell nie tworzy bazy danych. 

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1: Tworzenie fragmentu menedżera map
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Aby pobrać menedżera map fragmentów
Po utworzeniu możesz pobrać za pomocą tego polecenia cmdlet Menedżera mapowań fragmentów. Ten krok jest niezbędny, za każdym razem, gdy konieczne jest użycie obiektu ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Krok 2: Tworzenie mapy fragmentów
Wybierz typ mapowania fragmentów do utworzenia. Wybór zależy od architektury bazy danych: 

1. Pojedynczej dzierżawy na bazę danych (warunków, zobacz [słownik](sql-database-elastic-scale-glossary.md).) 
2. Wiele dzierżaw na bazę danych (dwóch typów):
   1. Mapowanie list
   2. Mapowanie zakresu

W przypadku modelu jednej dzierżawy, utworzyć **mapowanie list** mapowania fragmentów. Modelu jednej dzierżawy przypisze jedną bazę danych na dzierżawę. Jest to skuteczne modelu dla deweloperów SaaS, ponieważ upraszcza zarządzanie.

![Mapowanie list][1]

Modelu wielodostępnym przypisuje kilka dzierżaw poszczególnych baz danych (i grup dzierżawców można rozpowszechniać w wielu bazach danych). Jeśli oczekujesz, że każdego dzierżawcy, aby korzystać z danych w małych, należy użyć tego modelu. W tym modelu należy przypisać określonego zakresu dzierżaw, bazę danych za pomocą **mapowania zakresu**. 

![Mapowanie zakresu][2]

Lub możesz zaimplementować modelu wielodostępną bazą danych przy użyciu *mapowanie list* można przypisać wielu dzierżaw do poszczególnych baz danych. Na przykład DB1 służy do przechowywania informacji o dzierżawie ID 1 i 5, a bazy danych DB2 przechowuje dane dla dzierżawy 7 i dzierżawy 10. 

![Wielu dzierżaw w pojedynczej bazy danych][3] 

**Na podstawie wybranego, wybierz jedną z następujących opcji:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opcja 1: Tworzenie mapy fragmentów mapowania listy
Tworzenie mapy fragmentów za pomocą obiektu ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opcja 2: Tworzenie mapy fragmentów mapowania zakresu
Korzystanie z tego wzorca mapowania, wartości Identyfikatora dzierżawy musi być ciągłe zakresów i zaakceptować mają przerwa w zakresach, pomijanie zakresu, podczas tworzenia bazy danych.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opcja 3: Lista mapowań na poszczególnych baz danych
Konfigurowanie ten wzorzec wymaga również tworzenie mapowania w postaci listy jak pokazano w kroku 2, opcja 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3: Przygotowanie poszczególne fragmenty.
Dodaj każdego fragmentu (baza danych) do Menedżera mapowań fragmentów. Wykonanie tych kroków przygotowuje poszczególnych baz danych do przechowywania informacji o mapowaniu. Tej metody należy wykonać dla każdego fragmentu.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Krok 4: Dodaj mapowanie
Dodanie mapowań zależy od rodzaju mapy fragmentów, który został utworzony. Jeśli utworzono mapowania w postaci listy, możesz dodać listę mapowań. Jeśli utworzono mapę zakresu, możesz dodać mapowania zakresu.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opcja 1: mapowanie danych dla mapowania list
Mapowania danych, dodając mapowanie list dla każdej dzierżawy.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opcja 2: mapowanie danych dla mapowania zakresu
Dodaj mapowania zakresu dla wszystkich dzierżawy identyfikator zakresu - skojarzenia bazy danych:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Krok 4 — opcja 3: mapowania danych dla wielu dzierżaw w poszczególnych baz danych
Dla każdej dzierżawy Uruchom ListMapping Dodaj (opcja 1). 

## <a name="checking-the-mappings"></a>Sprawdzanie mapowania
Informacje o istniejących fragmentów i mapowania skojarzone z nimi można wykonywać zapytania za pomocą następującego polecenia:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Podsumowanie
Po zakończeniu instalacji możesz rozpocząć używać biblioteki klienckiej Elastic Database. Można również użyć [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md) i [zapytania z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Kolejne kroki
Pobierz skrypty programu PowerShell z [usługi Azure SQL DB Elastic Database narzędzi skryptów](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Narzędzia są również w witrynie GitHub: [Narzędzia platformy Azure/elastyczne db](https://github.com/Azure/elastic-db-tools).

Narzędzie dzielenia i scalania do przenoszenia danych do lub z modelu wielu dzierżaw do modelu jednej dzierżawy. Zobacz [narzędzie do scalania podziału](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Dodatkowe zasoby
Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

## <a name="questions-and-feature-requests"></a>Pytania i sugestie funkcji
Masz pytania, należy użyć [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) i żądania funkcji, dodaj je do [forum z opiniami bazy danych SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

