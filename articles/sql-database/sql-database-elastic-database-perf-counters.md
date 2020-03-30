---
title: Liczniki wydajności do śledzenia menedżera map niezależnego fragmentu
description: Liczniki wydajności klasy ShardMapManager i liczniki wydajności routingu zależne od danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823901"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Tworzenie liczników wydajności w celu śledzenia wydajności menedżera map niezależnego fragmentu

Liczniki wydajności są używane do śledzenia wydajności operacji [routingu zależnego od danych.](sql-database-elastic-scale-data-dependent-routing.md) Te liczniki są dostępne w Monitorze wydajności w kategorii "Elastyczna baza danych: zarządzanie fragmentami".

Można przechwycić wydajność [menedżera map niezależnego fragmentu,](sql-database-elastic-scale-shard-map-management.md)szczególnie w przypadku [korzystania z routingu zależnego od danych.](sql-database-elastic-scale-data-dependent-routing.md) Liczniki są tworzone przy metodach klasy Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Najnowsza wersja:** Przejdź do [witryny Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zobacz też [Uaktualnianie aplikacji do korzystania z najnowszej biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby utworzyć kategorię wydajności i liczniki, użytkownik musi **należeć** do lokalnej grupy Administratorzy na komputerze obsługującym aplikację.  
* Aby utworzyć wystąpienie licznika wydajności i zaktualizować liczniki, użytkownik musi być członkiem grupy Administratorzy lub **Użytkownicy Monitora** **wydajności.**

## <a name="create-performance-category-and-counters"></a>Tworzenie kategorii wydajności i liczników

Aby utworzyć liczniki, wywołaj CreatePerformanceCategoryAndCounters metody [ShardMapManagementFactory klasy](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Tylko administrator może wykonać metodę:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Można również użyć [tego](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skryptu programu PowerShell do wykonania metody.
Metoda tworzy następujące liczniki wydajności:  

* **Mapowania w pamięci podręcznej:** Liczba mapowań buforowanych dla mapy niezależnego fragmentu.
* **Operacje DDR/s**: Szybkość operacji routingu zależnych od danych dla mapy niezależnego fragmentu. Ten licznik jest aktualizowany, gdy wywołanie [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) powoduje pomyślne połączenie z niezależnego fragmentu docelowego.
* **Mapowanie trafień pamięci podręcznej wyszukiwania/s:** Szybkość pomyślnych operacji wyszukiwania pamięci podręcznej dla mapowań na mapie niezależnego fragmentu.
* **Pomija pamięć podręczną wyszukiwania mapowania/s:** Szybkość nieudanych operacji wyszukiwania pamięci podręcznej dla mapowań na mapie niezależnego fragmentu.
* **Mapowania dodane lub zaktualizowane w pamięci podręcznej/s:** Szybkość, z jaką mapowania są dodawane lub aktualizowane w pamięci podręcznej dla mapy niezależnego fragmentu.
* **Mapowania usunięte z pamięci podręcznej/s:** Szybkość, z jaką mapowania są usuwane z pamięci podręcznej dla mapy niezależnego fragmentu.

Liczniki wydajności są tworzone dla każdej buforowanej mapy niezależnego fragmentu na proces.  

## <a name="notes"></a>Uwagi

Następujące zdarzenia wyzwalają tworzenie liczników wydajności:  

* Inicjowanie [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) z [zapałem ładowania,](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)jeśli ShardMapManager zawiera żadnych map niezależnego fragmentu. Należą do nich [Metody GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) i [TryGetSqlShardMapManager.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager)
* Pomyślne wyszukiwanie mapy niezależnego fragmentu (przy użyciu [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) lub [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Pomyślne utworzenie mapy niezależnego fragmentu przy użyciu createshardmap().

Liczniki wydajności zostaną zaktualizowane przez wszystkie operacje pamięci podręcznej wykonywane na mapie niezależnego fragmentu i mapowania. Pomyślne usunięcie mapy niezależnego fragmentu przy użyciu deleteShardMap() powoduje usunięcie wystąpienia liczników wydajności.  

## <a name="best-practices"></a>Najlepsze rozwiązania

* Tworzenie kategorii wydajności i liczniki powinny być wykonywane tylko raz przed utworzeniem obiektu ShardMapManager. Każde wykonanie polecenia CreatePerformanceCategoryAndCounters() czyści poprzednie liczniki (utratę danych zgłoszonych przez wszystkie wystąpienia) i tworzy nowe.  
* Wystąpienia licznika wydajności są tworzone dla procesu. Każda awaria aplikacji lub usunięcie mapy niezależnego fragmentu z pamięci podręcznej spowoduje usunięcie wystąpień liczników wydajności.  

### <a name="see-also"></a>Zobacz też

[Elastic Database features overview (Omówienie funkcji Elastic Database)](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
