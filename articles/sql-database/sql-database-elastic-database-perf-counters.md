---
title: Liczniki wydajności dla menedżera map fragmentów
description: ShardMapManager klas i danych zależnych routingu liczników wydajności
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
ms.date: 03/31/2018
ms.openlocfilehash: f98c09a7e51fa729ef4a940e5f3c03de55d8dfd2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875284"
---
# <a name="performance-counters-for-shard-map-manager"></a>Liczniki wydajności dla menedżera map fragmentów
Możesz przechwycić wydajności [Menedżera mapowań fragmentów](sql-database-elastic-scale-shard-map-management.md), zwłaszcza w przypadku korzystania [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md). Liczniki są tworzone za pomocą metod klasy Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Liczniki są używane do śledzenia wydajności [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md) operacji. Te liczniki są dostępne w Monitorze wydajności, w obszarze kategoria ""elastycznej bazy danych: fragmentu Zarządzanie.

**Aby uzyskać najnowszą wersję:** przejdź do [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zobacz też [uaktualnić aplikację do korzystania z najnowszych Biblioteka kliencka elastic database](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Aby utworzyć kategorii wydajności i liczników, użytkownik musi być częścią lokalnej **Administratorzy** grupy na komputerze hostującym aplikację.  
* Aby utworzyć wystąpienie licznika wydajności i zaktualizować liczników, użytkownik musi być członkiem **Administratorzy** lub **Użytkownicy monitora wydajności** grupy. 

## <a name="create-performance-category-and-counters"></a>Tworzenie kategorii wydajności i liczników
Aby utworzyć liczników, wywołaj metodę CreatePeformanceCategoryAndCounters [klasy ShardMapManagementFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Tylko administrator może wykonać metodę: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Można również użyć [to](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skrypt programu PowerShell, aby wykonać metodę. Ta metoda tworzy następujące liczniki wydajności:  

* **Buforowane mapowania**: liczba mapowań mapowania fragmentów w pamięci podręcznej.
* **Rekord DDR OP./s**: liczba zależne routingu operacje na danych dla mapowania fragmentów. Ten licznik jest aktualizowany po wywołaniu [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) skutkuje udane połączenie do fragmentu docelowego. 
* **Mapowanie trafień w pamięci podręcznej wyszukiwania na sekundę**: szybkość przeprowadzania operacji wyszukiwania pomyślne pamięci podręcznej do mapowania w ramach mapowania fragmentów. 
* **Mapowanie chybień w pamięci podręcznej wyszukiwania na sekundę**: liczba operacji wyszukiwania w pamięci podręcznej nie powiodło się dla mapowania w ramach mapowania fragmentów.
* **Mapowania dodane lub zaktualizowane w pamięci podręcznej na sekundę**: stawka mapowania, które zostaną dodane lub zaktualizowane w pamięci podręcznej dla mapowania fragmentów. 
* **Mapowania usuwane z pamięci podręcznej na sekundę**: szybkość jaką mapowania są usuwane z pamięci podręcznej dla mapowania fragmentów. 

Liczniki wydajności są tworzone dla każdego fragmentu pamięci podręcznej jest mapowany na proces.  

## <a name="notes"></a>Uwagi
Następujące zdarzenia wyzwolić tworzenie liczników wydajności:  

* Inicjowanie [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) z [wczesne ładowanie](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), jeśli ShardMapManager zawiera żadnych mapowań fragmentów. Obejmują one [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) i [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) metody.
* Pomyślne wyszukiwania mapowania fragmentów w postaci (przy użyciu [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) lub [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Pomyślne utworzenie mapy fragmentów za pomocą CreateShardMap().

Liczniki wydajności zostaną zaktualizowane, wszystkie operacje pamięci podręcznej wykonać mapowania fragmentów i mapowania. Pomyślne usunięcie mapy fragmentów za pomocą reults () DeleteShardMap usunięcie wystąpienia liczników wydajności.  

## <a name="best-practices"></a>Najlepsze praktyki
* Tworzenie kategorii wydajności i liczniki powinny być wykonywane tylko raz przed tworzeniem ShardMapManager obiektu. Każdego wykonania polecenia CreatePerformanceCategoryAndCounters() czyści poprzednie liczniki (utraty danych przekazywanych przez wszystkie wystąpienia) i tworzy nowe.  
* Wystąpienia licznika wydajności są tworzone na proces. Wszelkie awarię aplikacji lub usunięcie mapowania fragmentów w postaci z pamięci podręcznej spowoduje usunięcie wystąpienia liczników wydajności.  

### <a name="see-also"></a>Zobacz także
[Elastic Database features overview (Omówienie funkcji Elastic Database)](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

