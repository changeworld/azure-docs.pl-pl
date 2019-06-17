---
title: Korzystanie z Recovery Manager w celu rozwiązywania problemów z mapą fragmentów | Dokumentacja firmy Microsoft
description: Korzystanie z klasy RecoveryManager do Rozwiązywanie problemów z mapami fragmentów
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
ms.date: 01/03/2019
ms.openlocfilehash: 1bab1ed9e2a24b0a84f4327d47a910934319b397
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475907"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Używanie klasy RecoveryManager do rozwiązywanie problemów z mapą fragmentów

[RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) klasa umożliwia aplikacji ADO.NET łatwiej wykryć i naprawić wszystkie niespójności między mapowania fragmentów globalne (GSM) i lokalnego podzielonej na fragmenty mapy (LSM) tak, w środowisku bazy danych podzielonej na fragmenty.

GSM i LSM śledzić mapowania poszczególnych baz danych w środowisku podzielonej na fragmenty. Czasami występuje przerwa między GSM i LSM. W takim przypadku użyj klasy RecoveryManager aby wykryć i naprawić przerwy.

Klasy RecoveryManager jest częścią [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md).

![Mapy fragmentów][1]

Aby uzyskać definicje terminów, zobacz [słownik narzędzi elastycznych baz danych](sql-database-elastic-scale-glossary.md). Aby zrozumieć sposób, w jaki **ShardMapManager** służy do zarządzania danymi w rozwiązaniu podzielonej na fragmenty, zobacz [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Dlaczego warto korzystać z Menedżera odzyskiwania

W środowisku bazy danych podzielonej na fragmenty ma jedną dzierżawę na bazę danych i wieloma bazami danych na serwer. Można również istnieć wiele serwerów w środowisku. Każda baza danych jest mapowany w ramach mapowania fragmentów, dlatego wywołania, można je skierować na właściwym serwerem i bazy danych. Bazy danych są śledzone na podstawie położenia **klucz fragmentowania**, i każdy fragment ma przypisany **zakres wartości klucza**. Na przykład klucz fragmentowania może reprezentują nazwy klienta od "D" do "F." Mapowania wszystkich fragmentów (czyli baz danych) i ich zakresów mapowania są zawarte w **mapowania fragmentów globalne (GSM)** . Każda baza danych zawiera także mapę zakresów znajdujących się w fragmentu, który jest znany jako **mapowanie fragmentów w postaci lokalnej (LSM) tak,** . Gdy aplikacja nawiązuje połączenie z fragmentu, mapowanie jest buforowana przy użyciu aplikacji do szybkiego pobierania. LSM służy do sprawdzania poprawności danych w pamięci podręcznej.

GSM i LSM może stać zsynchronizowany z następujących powodów:

1. Usunięcie fragmentów, w której zakres jest uważane za czynnik nie będzie już w użyciu lub zmiana nazwy fragmentu. Powoduje usunięcie fragmentu **oddzielone mapowania fragmentów**. Podobnie zmieniono nazwę bazy danych może spowodować mapowania fragmentów oddzielony. W zależności od intencji zmiany fragmentu może być konieczne usunięcie lub lokalizacji fragmentu zawierającego musi zostać zaktualizowany. Aby odzyskać usuniętą bazę danych, zobacz [przywrócić usuniętą bazę](sql-database-recovery-using-backups.md).
2. Występuje, zdarzenia pracy awaryjnej geo. Aby kontynuować, jeden zaktualizować nazwę serwera i nazwa bazy danych Menedżera mapowań fragmentów w aplikacji, a następnie zaktualizować szczegóły mapowania fragmentów dla wszystkich fragmentów w postaci mapy fragmentów. W przypadku geograficznie trybu failover, takie logika odzyskiwania powinno zostać zautomatyzowane w przepływie pracy trybu failover. Automatyzowanie akcji odzyskiwania umożliwia bezproblemową możliwości zarządzania dla baz danych z komputerów z obsługą geograficznej i pozwala uniknąć ręczne działania wykonywane przez ludzi. Aby dowiedzieć się więcej na temat opcji, aby odzyskać bazę danych, w przypadku awarii centrum danych, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md) i [odzyskiwania po awarii](sql-database-disaster-recovery.md).
3. Fragment lub ShardMapManager baza danych zostanie przywrócona do wcześniej punktu w czasie. Aby dowiedzieć się więcej na temat punktu w czasie odzyskiwania za pomocą kopii zapasowych, zobacz [odzyskiwania za pomocą kopii zapasowych](sql-database-recovery-using-backups.md).

Aby uzyskać więcej informacji na temat narzędzi elastycznej bazy danych Azure SQL Database, replikacja geograficzna i przywracania zobacz następujące tematy:

* [Omówienie: Business ciągłość działalności biznesowej i bazy danych odzyskiwania po awarii z usługą SQL Database w chmurze](sql-database-business-continuity.md)
* [Rozpocznij pracę z narzędziami elastycznej bazy danych](sql-database-elastic-scale-get-started.md)  
* [Zarządzanie ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Pobieranie RecoveryManager z ShardMapManager

Pierwszym krokiem jest utworzenie wystąpienia RecoveryManager. [Metoda GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) zwraca Menedżer odzyskiwania dla bieżącego [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) wystąpienia. Aby rozwiązać niespójności w ramach mapowania fragmentów, możesz pobrać RecoveryManager mapy określonego fragmentu.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

W tym przykładzie RecoveryManager jest inicjowany z ShardMapManager. ShardMapManager zawierający ShardMap jest również już zainicjowany.

Ponieważ ten kod aplikacji obsługuje mapy fragmentów, poświadczenia używane w metodzie fabryki (w poprzednim przykładzie smmConnectionString) powinna być poświadczenia, które ma uprawnienia odczytu i zapisu w bazie danych usługi GSM przywoływane przez połączenie ciąg. Te poświadczenia zazwyczaj różnią się od poświadczenia użyte do otwarcia połączenia w przypadku routingu zależnego od danych. Aby uzyskać więcej informacji, zobacz [przy użyciu poświadczeń klienta elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Usuwanie fragmentu ShardMap po usunięciu fragmentu

[Metoda DetachShard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) odłącza danego fragmentu z mapy fragmentów i usuwa mapowania skojarzone z fragmentem.  

* Parametr lokalizacja jest lokalizacja fragmentu, szczegółowo nazwy serwera i nazwę bazy danych, fragmentu odłączany.
* Parametr shardMapName jest nazwą mapy fragmentów. Jest to tylko wymagane, gdy wiele mapowań fragmentów są zarządzane przez ten sam Menedżera mapowań fragmentów. Opcjonalny.

> [!IMPORTANT]
> Tej techniki należy używać tylko jeśli masz pewność, że zakres zaktualizowano mapowanie jest pusty. Powyższych metod nie sprawdzają dane dla zakresu, przeniesione, więc zaleca się obejmują sprawdzenia w kodzie.

W tym przykładzie usuwa fragmentów z mapy fragmentów.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Mapowania fragmentów odzwierciedla lokalizacji fragmentu zawierającego w usłudze GSM przed usunięciem fragmentu. Usunięto fragmentu, przyjmuje się założenie było to zamierzone i zakresu kluczy fragmentowania nie jest już używana. Jeśli nie można wykonać przywracania do punktu w czasie. Aby odzyskać fragmentu z wcześniejszych punktu w czasie. (W takim przypadku przejrzyj następującą sekcję, aby wykryć niespójności fragmentu). Aby odzyskać, patrz [punktu w czasie odzyskiwania](sql-database-recovery-using-backups.md).

Ponieważ zakłada się, że usunięcie bazy danych było to zamierzone, akcja końcowego oczyszczania administracyjnych jest można usunąć wpisu do fragmentów w Menedżera mapowań fragmentów. Zapobiega to aplikacja z przypadkowo zapisywania informacji o na zakres, który nie jest oczekiwane.

## <a name="to-detect-mapping-differences"></a>Aby znaleźć różnice mapowania

[Metoda DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) wybiera i zwraca jedną z mapy fragmentów (lokalne lub globalne) jako źródło prawdziwych danych i uzgadnia mapowania na obu mapowań fragmentów w postaci (GSM i LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Lokalizacji* Określa nazwę serwera i nazwę bazy danych.
* *ShardMapName* parametr jest nazwą mapy fragmentów. Jest to tylko wymagane, jeśli wiele mapowań fragmentów są zarządzane przez ten sam Menedżera mapowań fragmentów. Opcjonalny.

## <a name="to-resolve-mapping-differences"></a>Aby rozwiązać różnice mapowania

[Metoda ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) wybiera jedno z mapowań fragmentów w postaci (lokalne lub globalne) jako źródło prawdziwych danych i uzgadnia mapowania na obu mapowań fragmentów w postaci (GSM i LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* parametru wylicza różnice w mapowania między GSM i LSM dla określonego fragmentu.
* [Wyliczenie MappingDifferenceResolution](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) służy do wskazywania metody postępowania po otrzymaniu różnica między mapowań fragmentów.
* **MappingDifferenceResolution.KeepShardMapping** jest zalecane, gdy LSM zawiera mapowanie dokładne i w związku z tym powinny być używane mapowanie we fragmencie. Zazwyczaj jest to wymagane w przypadku przejścia w tryb failover: fragmentu znajduje się teraz na nowym serwerze. Ponieważ najpierw należy usunąć fragment z GSM (przy użyciu metody RecoveryManager.DetachShard), mapowanie już nie istnieje w usłudze GSM. W związku z tym LSM należy ponownie ustanowić mapowania fragmentów.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Dołącz do ShardMap fragmentu, po przywróceniu fragmentu

[Metoda AttachShard](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) dołącza danego fragmentu do mapy fragmentów. Następnie wykrycia niespójności mapy fragmentów i aktualizuje mapowania, aby dopasować fragmentu punkcie przywracania fragmentu. Zakłada się, że bazy danych również jest zmieniana na odzwierciedlają oryginalna nazwa bazy danych (przed przywrócono fragmentu), ponieważ Przywracanie do punktu w czasie wartością domyślną jest dołączany wraz z sygnaturą czasową nową bazę danych.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* *Lokalizacji* parametr jest nazwa serwera i nazwa bazy danych, fragmentu dołączany.
* *ShardMapName* parametr jest nazwą mapy fragmentów. Jest to tylko wymagane, gdy wiele mapowań fragmentów są zarządzane przez ten sam Menedżera mapowań fragmentów. Opcjonalny.

Ten przykład dodaje fragmentu do mapy fragmentów, który niedawno został przywrócony z wcześniejszego punktu w stanu. Ponieważ fragmentów (czyli mapowanie fragmentów w LSM) została przywrócona, jest potencjalnie niespójne z wpisem fragmentów w usłudze GSM. Poza ten przykładowy kod fragmentu zostało przywrócone i zmieniono jego nazwę oryginalną nazwę bazy danych. Ponieważ został przywrócony, zakłada się, jest mapowanie w LSM zaufanych mapowania.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualizowanie lokalizacji fragmentów po geo-przejściu w tryb failover (przywracanie) fragmentami

W przypadku geograficznie trybu failover pomocniczej bazy danych jest udostępniane zapisu i staje się nowej podstawowej bazy danych. Nazwa serwera bazy danych oraz potencjalnie (w zależności od konfiguracji), może różnić się od oryginalnej podstawowej. W związku z tym wpisy mapowania fragmentów w usłudze GSM i LSM muszą zostać usunięte. Podobnie jeśli baza danych zostanie przywrócona do innej nazwy lub lokalizacji lub do wcześniejszego punktu w czasie, może to spowodować niespójności w mapowań fragmentów. Menedżera mapowań fragmentów obsługuje dystrybucję otwarte połączenia z poprawną bazą danych. Dystrybucja opiera się na danych mapy fragmentów i wartość klucza fragmentowania, który jest elementem docelowym żądania aplikacji. Po przełączeniu geograficznie te informacje muszą zostać zaktualizowane nazwę dokładne serwera, nazwę bazy danych i mapowanie fragmentów odzyskanej bazy danych.

## <a name="best-practices"></a>Najlepsze praktyki

Geograficznego przejścia w trybie failover i odzyskiwania są operacje, które zwykle są zarządzane przez administratora chmury aplikacji celowo przy użyciu jednej z funkcji ciągłości działania baz danych Azure SQL. Planowanie ciągłości biznesowej wymaga procesów, procedury i środki w celu zapewnienia, że operacje biznesowe można nadal bez przeszkód korzystać z programu. Metody dostępne jako część klasy RecoveryManager używanego w ramach tego przepływu pracy do upewnij się, że GSM i LSM są zawsze na bieżąco na podstawie działań odzyskiwania. Istnieje pięć podstawowych kroków, aby prawidłowo zapewnienie, że GSM i LSM odzwierciedlają dokładne informacje po wystąpieniu zdarzenia pracy awaryjnej. Kod aplikacji, wykonaj następujące kroki, można zintegrować istniejącymi narzędziami i przepływ pracy.

1. Pobieranie RecoveryManager z ShardMapManager.
2. Odłącz poprzedniego fragmentu z mapy fragmentów.
3. Dołączanie nowych fragmentów do mapy fragmentów, w tym nową lokalizację fragmentu.
4. Wykrycia niespójności w mapowanie między GSM i LSM.
5. Rozwiązywanie różnic między GSM i LSM, ufające LSM.

W tym przykładzie wykonuje następujące czynności:

1. Usuwa fragmentów z mapy fragmentów, które odzwierciedlają lokalizacji fragmentów przed przełączeniem do trybu failover.
2. Dołącza fragmentów mapowania fragmentów w czasie wykonywania odbicia nowe lokalizacje fragmentów (parametr "Configuration.SecondaryServer" jest nową nazwę serwera, ale taką samą nazwę bazy danych).
3. Pobiera tokeny odzyskiwania został określony poprzez wykrycie mapowania różnice między GSM i LSM dla każdego fragmentu.
4. Rozpoznaje niespójności, ufające mapowanie z LSM każdego fragmentu.

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
