---
title: Tworzenie liczników wydajności w celu śledzenia wydajności Menedżera mapy fragmentu
description: Liczniki wydajności routingu zależnego od danych ShardMapManager i klas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568668"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Tworzenie liczników wydajności w celu śledzenia wydajności Menedżera mapy fragmentu

Liczniki wydajności służą do śledzenia wydajności operacji [routingu zależnych od danych](sql-database-elastic-scale-data-dependent-routing.md) . Te liczniki są dostępne w Monitorze wydajności w obszarze "Elastic Database: Kategoria fragmentu Management.

Wydajność [Menedżera mapy fragmentu](sql-database-elastic-scale-shard-map-management.md)można przechwycić, szczególnie w przypadku korzystania z [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md). Liczniki są tworzone przy użyciu metod klasy Microsoft. Azure. SQLDatabase. ElasticScale. Client.  


**Najnowsza wersja:** Przejdź do [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zobacz też [Uaktualnianie aplikacji, aby użyć najnowszej biblioteki klienta Elastic Database](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby utworzyć kategorię wydajności i liczniki, użytkownik musi być częścią lokalnej grupy **administratorów** na komputerze hostującym aplikację.  
* Aby utworzyć wystąpienie licznika wydajności i zaktualizować liczniki, użytkownik musi być członkiem grupy **administratorzy** lub **Użytkownicy monitora wydajności** .

## <a name="create-performance-category-and-counters"></a>Tworzenie kategorii i liczników wydajności

Aby utworzyć liczniki, wywołaj metodę CreatePerformanceCategoryAndCounters [klasy ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Tylko administrator może wykonać metodę:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Za pomocą [tego](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skryptu programu PowerShell można także wykonać metodę.
Metoda tworzy następujące liczniki wydajności:  

* **Mapowania buforowane**: Liczba mapowań przechowywanych w pamięci podręcznej dla mapy fragmentu.
* **Operacje DDR/s**: Częstotliwość operacji routingu zależnych od danych dla mapy fragmentu. Ten licznik jest aktualizowany, gdy wywołanie [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) powoduje pomyślne połączenie z miejscem docelowym fragmentu.
* **Trafienia pamięci podręcznej wyszukiwania mapowania/s**: Szybkość pomyślnych operacji wyszukiwania w pamięci podręcznej dla mapowań w mapie fragmentu.
* **Chybienia w pamięci podręcznej wyszukiwania mapowania/s**: Szybkość nieudanych operacji wyszukiwania w pamięci podręcznej dla mapowań w mapie fragmentu.
* **Dodano lub Zaktualizowano mapowania w pamięci podręcznej/s**: Szybkość, z jaką mapowania są dodawane lub aktualizowane w pamięci podręcznej dla mapy fragmentu.
* **Mapowania usunięte z pamięci podręcznej/s**: Szybkość, z jaką mapowania są usuwane z pamięci podręcznej dla mapy fragmentu.

Liczniki wydajności są tworzone dla każdej buforowanej mapy fragmentu na proces.  

## <a name="notes"></a>Uwagi

Następujące zdarzenia wyzwalają tworzenie liczników wydajności:  

* Inicjalizacja [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) z [ładowaniem eager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), jeśli ShardMapManager zawiera wszystkie mapy fragmentu. Obejmują one metody [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) i [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Pomyślne wyszukiwanie mapy fragmentu (przy użyciu [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) lub [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Pomyślne utworzenie mapy fragmentu za pomocą CreateShardMap ().

Liczniki wydajności zostaną zaktualizowane przez wszystkie operacje pamięci podręcznej wykonywane na mapie i mapowania fragmentu. Pomyślne usunięcie mapy fragmentu za pomocą DeleteShardMap () powoduje usunięcie wystąpienia liczników wydajności.  

## <a name="best-practices"></a>Najlepsze praktyki

* Tworzenie kategorii wydajności i liczników należy wykonać tylko raz przed utworzeniem obiektu ShardMapManager. Każde wykonanie polecenia CreatePerformanceCategoryAndCounters () czyści poprzednie liczniki (tracące dane zgłaszane przez wszystkie wystąpienia) i tworzy nowe.  
* Wystąpienia liczników wydajności są tworzone dla poszczególnych procesów. Wszystkie awarie aplikacji lub usunięcie mapy fragmentu z pamięci podręcznej spowodują usunięcie wystąpień liczników wydajności.  

### <a name="see-also"></a>Zobacz także

[Elastic Database features overview (Omówienie funkcji Elastic Database)](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
