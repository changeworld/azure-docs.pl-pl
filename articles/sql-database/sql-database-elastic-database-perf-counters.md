---
title: Liczniki wydajności dla menedżera map fragmentów
description: ShardMapManager klas i danych zależnych routingu liczniki wydajności
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 9c134ee96f7749529ab665df041cfc51c979acde
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647327"
---
# <a name="performance-counters-for-shard-map-manager"></a>Liczniki wydajności dla menedżera map fragmentów
Można przechwycić wydajność [menedżera map niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md), zwłaszcza w przypadku korzystania [danych zależnych routingu](sql-database-elastic-scale-data-dependent-routing.md). Liczniki są tworzone za pomocą metod klasy Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Liczniki są używane do śledzenia wydajności [danych zależnych routingu](sql-database-elastic-scale-data-dependent-routing.md) operacji. Te liczniki są dostępne w Monitorze wydajności, w kategorii "Zarządzanie bazą danych niezależnego fragmentu: elastycznej".

**Aby uzyskać najnowszą wersję:** przejdź do [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zobacz też [uaktualnić aplikację do korzystania z najnowszych biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Aby utworzyć kategorii wydajności i liczniki, użytkownik musi być częścią lokalnej **Administratorzy** grupy na komputerze hostującym aplikacji.  
* Aby utworzyć wystąpienie licznika wydajności i liczniki aktualizacji, użytkownik musi być członkiem albo **Administratorzy** lub **Użytkownicy monitora wydajności** grupy. 

## <a name="create-performance-category-and-counters"></a>Tworzenie kategorii wydajności i liczniki
Aby utworzyć liczników, należy wywołać metodę CreatePeformanceCategoryAndCounters [ShardMapManagmentFactory klasy](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Tylko administrator może wykonać metody: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Można również użyć [to](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skrypt programu PowerShell, można wykonać metody. Ta metoda tworzy następujące liczniki wydajności:  

* **Buforowane mapowania**: liczba buforowanych mapy niezależnego fragmentu mapowania.
* **Operacje DDR na sekundę**: szybkości danych zależnych operacji routingu mapy niezależnego fragmentu. Ten licznik jest aktualizowany po wywołaniu [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) wynikiem połączenia z programem niezależnych docelowego. 
* **Mapowanie trafienia pamięci podręcznej wyszukiwania na sekundę**: szybkość przeprowadzania operacji wyszukiwania pomyślne pamięci podręcznej dla mapowania na mapie niezależnego fragmentu. 
* **Chybienia pamięci podręcznej wyszukiwania na sekundę mapowania**: liczba operacji wyszukiwania w pamięci podręcznej nie powiodło się dla mapowania na mapie niezależnego fragmentu.
* **Mapowania dodane lub zaktualizowane w pamięci podręcznej na sekundę**: szybkości mapowania, które są dodawane lub zaktualizowane w pamięci podręcznej mapy niezależnego fragmentu. 
* **Mapowania usuwane z pamięci podręcznej na sekundę**: szybkość z jaką mapowania są usuwane z pamięci podręcznej mapy niezależnego fragmentu. 

Liczniki wydajności są tworzone dla każdej mapy niezależnych pamięci podręcznej na proces.  

## <a name="notes"></a>Uwagi
Następujące zdarzenia wyzwolić tworzenie liczników wydajności:  

* Inicjowanie [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) z [wczesny ładowania](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), jeśli ShardMapManager zawiera wszystkie mapy niezależnego fragmentu. Obejmują one [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) i [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) metody.
* Pomyślne wyszukiwania mapy niezależnego fragmentu (przy użyciu [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) lub [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Pomyślnym utworzeniu przy użyciu CreateShardMap() mapy niezależnego fragmentu.

Liczniki wydajności zostaną zaktualizowane przez wszystkie operacje pamięci podręcznej wykonanych na mapie niezależnego fragmentu i mapowania. Pomyślne usunięcie mapy niezależnego fragmentu usunięcie wystąpienia liczników wydajności przy użyciu DeleteShardMap reults ().  

## <a name="best-practices"></a>Najlepsze praktyki
* Tworzenie kategorii wydajności i liczniki powinny być wykonywane tylko raz, przed utworzeniem obiektu ShardMapManager. Co wykonanie polecenia CreatePerformanceCategoryAndCounters() czyści poprzednie liczniki (utraty danych przekazywanych przez wszystkie wystąpienia) i tworzy nowe.  
* Wystąpienia licznika wydajności są tworzone na proces. Awarii aplikacji ani usuwania mapy niezależnego fragmentu z pamięci podręcznej spowoduje usunięcie wystąpienia liczników wydajności.  

### <a name="see-also"></a>Zobacz także
[Elastic Database features overview (Omówienie funkcji Elastic Database)](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

