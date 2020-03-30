---
title: Menedżer odzyskiwania w celu rozwiązania problemów z mapą niezależnego fragmentu
description: Użyj klasy RecoveryManager, aby rozwiązać problemy z mapami niezależnego fragmentu
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823850"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Używanie klasy RecoveryManager do rozwiązywanie problemów z mapą fragmentów

[Klasa RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) zapewnia ADO.NET aplikacjom możliwość łatwego wykrywania i korygowania wszelkich niespójności między mapą globalnego niezależnego fragmentu (GSM) a lokalną mapą niezależnego fragmentu (LSM) w środowisku podzielonej bazy danych.

GSM i LSM śledzić mapowanie każdej bazy danych w środowisku podzielonym na fragmenty. Od czasu do czasu występuje przerwa między GSM i LSM. W takim przypadku należy użyć RecoveryManager klasy do wykrywania i naprawy przerwy.

Klasa RecoveryManager jest częścią [biblioteki klienta elastycznej bazy danych.](sql-database-elastic-database-client-library.md)

![Mapa odłamków][1]

Definicje terminów można znaleźć w [glosariuszu narzędzi elastycznej bazy danych](sql-database-elastic-scale-glossary.md). Aby zrozumieć, jak **ShardMapManager** jest używany do zarządzania danymi w rozwiązaniu podzielonym na fragmenty, zobacz [Zarządzanie mapami niezależnego fragmentu.](sql-database-elastic-scale-shard-map-management.md)

## <a name="why-use-the-recovery-manager"></a>Dlaczego warto skorzystać z menedżera odzyskiwania

W środowisku podzielonej bazy danych jest jeden dzierżawcy na bazę danych i wiele baz danych na serwer. W środowisku może być również wiele serwerów. Każda baza danych jest mapowana na mapie niezależnego fragmentu, dzięki czemu wywołania mogą być kierowane do właściwego serwera i bazy danych. Bazy danych są śledzone zgodnie z **kluczem dzielenia na fragmenty,** a każdemu niezależnemu fragmentowi jest przypisywany **zakres wartości klucza.** Na przykład klucz dzielenia na fragmenty może reprezentować nazwy klientów od "D" do "F". Mapowanie wszystkich fragmentów (aka baz danych) i ich zakresów mapowania są zawarte w **globalnej mapie niezależnego fragmentu (GSM).** Każda baza danych zawiera również mapę zakresów zawartych na niezależnej mapie, która jest znana jako **mapa lokalnego niezależnego fragmentu (LSM).** Gdy aplikacja łączy się z fragmentem, mapowanie jest buforowane z aplikacją do szybkiego pobierania. LSM służy do sprawdzania poprawności danych w pamięci podręcznej.

GSM i LSM mogą być zsynchronizowane z następujących powodów:

1. Usunięcie niezależnego fragmentu, którego zakres uważa się, że nie jest już używany lub zmiana nazwy niezależnego fragmentu. Usunięcie fragmentu **powoduje, że odwzorowanie oddzielonych fragmentów jest oddzielone.** Podobnie zmieniona baza danych może spowodować mapowanie oddzielonych fragmentów. W zależności od intencji zmiany fragment może wymagać usunięcia lub lokalizacja niezależnego fragmentu musi zostać zaktualizowana. Aby odzyskać usuniętą bazę danych, zobacz [Przywracanie usuniętej bazy danych](sql-database-recovery-using-backups.md).
2. Występuje zdarzenie trybu failover geograficznego. Aby kontynuować, należy zaktualizować nazwę serwera i nazwę bazy danych menedżera mapy niezależnego fragmentu w aplikacji, a następnie zaktualizować szczegóły mapowania niezależnego fragmentu dla wszystkich fragmentów na mapie niezależnego fragmentu. If there is a geo-failover, such recovery logic should be automated within the failover workflow. Automatyzacja akcji odzyskiwania umożliwia bezproblemowe zarządzanie bazami danych z obsługą geograficzną i pozwala uniknąć ręcznych działań człowieka. Aby dowiedzieć się więcej o opcjach odzyskiwania bazy danych w przypadku awarii centrum danych, zobacz [Ciągłość działania](sql-database-business-continuity.md) i [odzyskiwanie po awarii](sql-database-disaster-recovery.md).
3. Fragment lub baza danych ShardMapManager jest przywracana do wcześniejszego czasu w punkcie. Aby dowiedzieć się więcej o odzyskiwaniu czasu przy użyciu kopii zapasowych, zobacz [Odzyskiwanie przy użyciu kopii zapasowych](sql-database-recovery-using-backups.md).

Aby uzyskać więcej informacji na temat narzędzi elastycznej bazy danych azure SQL Database, replikacji geograficznej i przywracania, zobacz następujące:

* [Omówienie: Ciągłość działania chmury i odzyskiwanie po awarii bazy danych za pomocą bazy danych SQL](sql-database-business-continuity.md)
* [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md)  
* [Zarządzanie mapami ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Pobieranie recoverymanager z ShardMapManager

Pierwszym krokiem jest utworzenie recoverymanager wystąpienia. [GetRecoveryManager Metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) zwraca menedżera odzyskiwania dla bieżącego wystąpienia [ShardMapManager.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Aby rozwiązać wszelkie niespójności na mapie niezależnego fragmentu, należy najpierw pobrać RecoveryManager dla konkretnej mapy niezależnego fragmentu.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

W tym przykładzie RecoveryManager jest inicjowany z ShardMapManager. ShardMapManager zawierający ShardMap jest również już zainicjowane.

Ponieważ ten kod aplikacji manipuluje samą mapą niezależnego fragmentu, poświadczenia używane w metodzie fabrycznej (w poprzednim przykładzie smmConnectionString) powinny być poświadczeniami, które mają uprawnienia do odczytu i zapisu w bazie danych GSM, do których odwołuje się połączenie Ciąg. Te poświadczenia zazwyczaj różnią się od poświadczeń używanych do otwierania połączeń dla routingu zależnego od danych. Aby uzyskać więcej informacji, zobacz [Używanie poświadczeń w kliencie elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Usuwanie fragmentu z mapy niezależnego fragmentu po usunięciu fragmentu

[Metoda Odłącza](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) odłącza dany fragment od mapy niezależnego fragmentu i usuwa mapowania skojarzone z fragmentem.  

* Parametr lokalizacji jest lokalizacją niezależnego fragmentu, w szczególności nazwą serwera i nazwą bazy danych, odłączanego fragmentu.
* Parametr shardMapName jest nazwą mapy niezależnego fragmentu. Jest to wymagane tylko wtedy, gdy wiele map niezależnego fragmentu są zarządzane przez tego samego menedżera mapy niezależnego fragmentu. Element opcjonalny.

> [!IMPORTANT]
> Tej techniki należy używać tylko wtedy, gdy masz pewność, że zakres zaktualizowanego mapowania jest pusty. Powyższe metody nie sprawdzają danych dla zakresu, który jest przenoszony, dlatego najlepiej jest uwzględnić kontrole w kodzie.

W tym przykładzie usuwa fragmenty z mapy niezależnego fragmentu.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Mapa niezależnego fragmentu odzwierciedla lokalizację niezależnego fragmentu w GSM przed usunięciem fragmentu. Ponieważ fragment został usunięty, zakłada się, że było to zamierzone, a zakres klucza dzielenia na fragmenty nie jest już używany. Jeśli nie, można wykonać przywracanie czasu punktu w. , aby odzyskać fragment z wcześniejszego punktu w czasie. (W takim przypadku przejrzyj następującą sekcję, aby wykryć niespójności fragmentów). Aby odzyskać, zobacz [Odzyskiwanie w punkcie czasu](sql-database-recovery-using-backups.md).

Ponieważ zakłada się, że usunięcie bazy danych było zamierzone, ostateczną akcję oczyszczania administracyjnego jest usunięcie wpisu do fragmentu w menedżerze mapy niezależnego fragmentu. Zapobiega to aplikacji przypadkowo zapisywania informacji do zakresu, który nie jest oczekiwany.

## <a name="to-detect-mapping-differences"></a>Aby wykryć różnice mapowania

[DetectMappingDifferences Metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) wybiera i zwraca jedną z map niezależnego fragmentu (lokalne lub globalne) jako źródło prawdy i uzgadnia mapowania na obu mapach niezależnego fragmentu (GSM i LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Lokalizacja* określa nazwę serwera i nazwę bazy danych.
* Parametr *shardMapName* jest nazwą mapy niezależnego fragmentu. Jest to wymagane tylko wtedy, gdy wiele map niezależnego fragmentu są zarządzane przez tego samego menedżera mapy niezależnego fragmentu. Element opcjonalny.

## <a name="to-resolve-mapping-differences"></a>Aby rozwiązać różnice mapowania

[ResolveMappingDifferences Metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) wybiera jedną z map niezależnego fragmentu (lokalne lub globalne) jako źródło prawdy i uzgadnia mapowania na obu mapach niezależnego fragmentu (GSM i LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* Parametr wylicza różnice w mapowaniach między GSM i LSM dla określonego fragmentu.
* [MappingDifferenceResoluation wyliczenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) służy do wskazania metody rozwiązywania różnicy między mapowania niezależnego fragmentu.
* **MappingDifferenceResolution.KeepShardMapping** zaleca się, gdy LSM zawiera dokładne mapowanie i dlatego mapowanie w niezależnego fragmentu powinny być używane. Zazwyczaj dzieje się tak, jeśli istnieje przewija się w błąd: fragment znajduje się teraz na nowym serwerze. Ponieważ fragment musi najpierw zostać usunięty z GSM (przy użyciu RecoveryManager.DetachShard metody), mapowanie nie istnieje już na GSM. W związku z tym LSM musi służyć do ponownego ustanowienia mapowania niezależnego fragmentu.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Dołączanie fragmentu do mapy niezależnego odłamka po przywróceniu fragmentu

[AttachShard Metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) dołącza danego niezależnego fragmentu do mapy niezależnego fragmentu. Następnie wykrywa niespójności mapy niezależnego fragmentu i aktualizuje mapowania, aby dopasować fragment w punkcie przywracania niezależnego fragmentu. Zakłada się, że baza danych jest również zmieniana w celu odzwierciedlenia oryginalnej nazwy bazy danych (przed przywróceniem fragmentu), ponieważ przywracanie czasu punktu domyślnie do nowej bazy danych dołączonej do sygnatury czasowej.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* Parametr *lokalizacji* jest nazwą serwera i nazwą bazy danych dołączonego fragmentu.
* Parametr *shardMapName* jest nazwą mapy niezależnego fragmentu. Jest to wymagane tylko wtedy, gdy wiele map niezależnego fragmentu są zarządzane przez tego samego menedżera mapy niezależnego fragmentu. Element opcjonalny.

W tym przykładzie dodaje fragment do mapy niezależnego fragmentu, który został niedawno przywrócony z wcześniejszego czasu w punkcie. Ponieważ fragment (a mianowicie mapowanie dla niezależnego fragmentu w LSM) został przywrócony, jest potencjalnie niezgodne z wpisem niezależnego fragmentu w GSM. Poza tym przykładowym kodem fragment został przywrócony i zmieniono jego nazwę na oryginalną nazwę bazy danych. Ponieważ został przywrócony, zakłada się, że mapowanie w LSM jest zaufanym mapowaniem.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualizowanie lokalizacji niezależnego fragmentu po przeżycie awaryjnym (przywracaniu geograficznym) fragmentów

Jeśli istnieje geo-failover, pomocnicza baza danych jest zapisywany dostępne i staje się nową podstawową bazą danych. Nazwa serwera i potencjalnie bazy danych (w zależności od konfiguracji) może się różnić od oryginalnego podstawowego. W związku z tym wpisy mapowania dla niezależnego fragmentu w GSM i LSM muszą być ustalone. Podobnie jeśli baza danych zostanie przywrócona do innej nazwy lub lokalizacji lub do wcześniejszego punktu w czasie, może to spowodować niespójności w mapach niezależnego fragmentu. Menedżer map niezależnego fragmentu obsługuje dystrybucję otwartych połączeń do poprawnej bazy danych. Dystrybucja jest oparta na danych na mapie niezależnego fragmentu i wartości klucza dzielenia na fragmenty, który jest celem żądania aplikacji. Po geo-failover te informacje muszą być aktualizowane o dokładną nazwę serwera, nazwę bazy danych i mapowanie niezależnego fragmentu odzyskanej bazy danych.

## <a name="best-practices"></a>Najlepsze rozwiązania

Geo-failover i odzyskiwanie są operacje zazwyczaj zarządzane przez administratora chmury aplikacji celowo przy użyciu jednej z baz danych SQL platformy Azure funkcji ciągłości biznesowej. Planowanie ciągłości działania wymaga procesów, procedur i środków, aby zapewnić, że operacje biznesowe mogą być kontynuowane bez przerwy. Metody dostępne jako część RecoveryManager klasy powinny być używane w ramach tego przepływu pracy, aby upewnić się, że GSM i LSM są aktualizowane na podstawie podjętej akcji odzyskiwania. Istnieje pięć podstawowych kroków, aby prawidłowo upewnić się, że GSM i LSM odzwierciedlają dokładne informacje po zdarzeniu pracy awaryjnej. Kod aplikacji do wykonania tych kroków można zintegrować z istniejącymi narzędziami i przepływem pracy.

1. Pobierz RecoveryManager z ShardMapManager.
2. Odłącz stary fragment od mapy niezależnego fragmentu.
3. Dołącz nowy fragment do mapy niezależnego fragmentu, łącznie z nową lokalizacją niezależnego fragmentu.
4. Wykrywanie niespójności w mapowaniu między GSM i LSM.
5. Rozwiązuj różnice między GSM i LSM, ufając LSM.

W tym przykładzie wykonuje się następujące kroki:

1. Usuwa fragmenty z mapy niezależnego fragmentu, które odzwierciedlają lokalizacje niezależnego fragmentu przed zdarzeniem trybu failover.
2. Dołącza fragmenty do mapy niezależnego fragmentu odzwierciedlając nowe lokalizacje niezależnego fragmentu (parametr "Configuration.SecondaryServer" to nowa nazwa serwera, ale ta sama nazwa bazy danych).
3. Pobiera tokeny odzyskiwania, wykrywając różnice mapowania między GSM i LSM dla każdego niezależnego fragmentu.
4. Rozwiązuje niespójności, ufając mapowanie z LSM każdego niezależnego fragmentu.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
