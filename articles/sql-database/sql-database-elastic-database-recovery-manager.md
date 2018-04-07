---
title: Za pomocą Menedżera odzyskiwania do rozwiązywania problemów z mapy niezależnego fragmentu | Dokumentacja firmy Microsoft
description: Klasa RecoveryManager służy do rozwiązywania problemów przy użyciu map niezależnego fragmentu
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 350ea0d4b744467849916f2d958cc49fd72d3e4b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Używanie klasy RecoveryManager do rozwiązywanie problemów z mapą fragmentów
[RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) klasa umożliwia aplikacji ADO.Net łatwiej wykryć i naprawić wszystkie niespójności między mapy globalne niezależnego fragmentu (GSM), a następnie mapować lokalnego niezależnego fragmentu (LSM) tak, w środowisku bazy danych podzielonej. 

GSM i LSM śledzić mapowanie każdej bazy danych w środowisku podzielonej. Czasami występuje podział między GSM i LSM. W takim przypadku należy użyć klasy RecoveryManager wykrywania i naprawiania przerwy.

Klasa RecoveryManager jest częścią [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md). 

![Identyfikator niezależnego fragmentu mapy][1]

Aby uzyskać definicje terminów, zobacz [słownik narzędzi elastycznej bazy danych](sql-database-elastic-scale-glossary.md). Aby zrozumieć, jak **ShardMapManager** służy do zarządzania danymi w podzielonym rozwiązania, zobacz [zarządzania mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Dlaczego warto używać Menedżera odzyskiwania?
W środowisku podzielonej bazy danych ma jednej dzierżawy na bazę danych i wielu baz danych na serwerze. Może istnieć wiele serwerów w środowisku. Każda baza danych jest mapowany na mapie niezależnego fragmentu, wywołania mogą być kierowane właściwym serwerem i bazą danych. Bazy danych są śledzone według **klucza dzielenia na fragmenty**, i ma przypisany każdego niezależnych **zakres wartości klucza**. Na przykład klucz dzielenia na fragmenty może reprezentować nazwy klientów z "D" do "F." Mapowanie wszystkich odłamków (alias bazy danych) i ich zakresy mapowania są zawarte w **mapy globalne niezależnego fragmentu (GSM)**. Każda baza danych zawiera także mapy zakresów zawartych w niezależnych, znany jako **mapy lokalnego niezależnego fragmentu (LSM) tak,**. Gdy aplikacja łączy się niezależnego fragmentu, mapowanie jest buforowany z aplikacją do szybkiego pobierania. LSM służy do sprawdzania poprawności danych z pamięci podręcznej. 

GSM i LSM będzie zsynchronizowany z następujących powodów:

1. Usuwanie niezależnych, której zakres jest sądzi, że nie będzie już w użyciu lub zmiana nazwy niezależnego fragmentu. Powoduje usunięcie niezależnych **oddzielona mapowanie niezależnych**. Podobnie zmieniono nazwę bazy danych może spowodować oddzielone niezależnego fragmentu mapowania. W zależności od celem zmiany niezależnych może być konieczne do usunięcia lub lokalizacji niezależnego fragmentu musi zostać zaktualizowany. Aby odzyskać usuniętej bazy danych, zobacz [przywrócić usuniętą bazę](sql-database-recovery-using-backups.md).
2. Występuje zdarzenie geograficznie pracy w trybie failover. Aby kontynuować, jedna aktualizacja nazwy serwera i nazwa bazy danych Menedżera mapy niezależnego fragmentu w aplikacji i następnie zaktualizuj szczegóły mapowania niezależnych dla wszystkich odłamków na mapie niezależnego fragmentu. W przypadku geograficznie trybu failover, takie logiki odzyskiwania powinno zostać zautomatyzowane w ramach przepływu pracy awaryjnej. Automatyzowanie akcje odzyskiwania umożliwia frictionless możliwości zarządzania włączone geograficznie baz danych i pozwala uniknąć człowieka działań ręcznych. Informacje na temat opcji, aby odzyskać bazę danych w przypadku awarii centrum danych, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md) i [odzyskiwania po awarii](sql-database-disaster-recovery.md).
3. Identyfikator niezależnego fragmentu lub ShardMapManager bazy danych są odzyskiwane do wcześniej punktu w czasie. Aby uzyskać informacje dotyczące punktu w czasie odzyskiwania przy użyciu kopii zapasowych, zobacz [odzyskiwania za pomocą kopii zapasowej](sql-database-recovery-using-backups.md).

Aby uzyskać więcej informacji na temat narzędzi elastycznej bazy danych Azure SQL bazy danych — replikacja geograficzna i przywracania zobacz następujące tematy: 

* [Omówienie: Chmury firm odzyskiwania po awarii ciągłości i bazy danych z bazy danych SQL](sql-database-business-continuity.md) 
* [Wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md)  
* [Zarządzanie ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Pobieranie RecoveryManager z ShardMapManager
Pierwszym krokiem jest utworzenie wystąpienia RecoveryManager. [Metody GetRecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) zwraca Menedżera odzyskiwania dla bieżącego [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) wystąpienia. W celu rozwiązania niespójności w planie niezależnego fragmentu, należy pobrać RecoveryManager mapy określonego niezależnego fragmentu. 

   ```
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 
   ```

W tym przykładzie RecoveryManager jest inicjowana z ShardMapManager. ShardMapManager zawierający ShardMap jest również już zainicjowany. 

Ponieważ ten kod aplikacji manipuluje mapy niezależnego fragmentu, poświadczenia używane w metodzie fabryki (w powyższym przykładzie smmConnectionString) powinna być poświadczenia, które mają uprawnienia odczytu i zapisu w bazie danych GSM odwołuje się do ciągu połączenia. Te poświadczenia zwykle różnią się od poświadczenia używane do otwierania połączenia zależne od danych routingu. Aby uzyskać więcej informacji, zobacz [klienta elastycznej bazy danych przy użyciu poświadczeń](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Usuwanie niezależnych od ShardMap po usunięciu niezależnego fragmentu
[DetachShard metoda](https://msdn.microsoft.com/library/azure/dn842083.aspx) odłącza danego niezależnego fragmentu z mapy niezależnego fragmentu i usuwa skojarzone z niezależnego fragmentu mapowania.  

* Parametr lokalizacja jest lokalizacja niezależnego fragmentu, w szczególności nazwę serwera i nazwę bazy danych, niezależnych odłączany. 
* Parametr shardMapName jest nazwa mapy niezależnego fragmentu. Jest to tylko wymagana, gdy wielu map niezależnego fragmentu są zarządzane przez tego samego menedżera mapy niezależnego fragmentu. Opcjonalny. 


> [!IMPORTANT]
> Użyj tej techniki tylko jeśli masz pewność, że zakres, zaktualizowany mapowania jest pusty. Powyżej metod nie sprawdzenie, czy dane dla zakresu przenoszony, dlatego warto uwzględnić kontroli w kodzie.
>

W tym przykładzie usuwa mapy niezależnych fragmentów. 

   ```
   rm.DetachShard(s.Location, customerMap);
   ``` 

Mapa niezależnego fragmentu odzwierciedla lokalizacji niezależnego fragmentu w GSM przed usunięciem elementu niezależnego fragmentu. Ponieważ niezależnych został usunięty, zakłada się to było zamierzone i zakresem kluczy dzielenia na fragmenty nie jest już używana. Jeśli nie możesz wykonać przywracanie do punktu w czasie. Aby odzyskać niezależnych od wcześniejszej punktu w czasie. (W takim przypadku przejrzyj następującą sekcję do wykrycia niespójności niezależnego fragmentu). Aby odzyskać, zobacz [punktu w czasie odzyskiwania](sql-database-recovery-using-backups.md).

Ponieważ zakłada się, że usunięcie bazy danych było zamierzone, akcja końcowego oczyszczania administracyjnych jest usunięcie wpisu do niezależnego fragmentu w Menedżerze mapy niezależnego fragmentu. Zapobiega to aplikacja przypadkowo zapisywania informacji do zakresu, który nie jest oczekiwany.

## <a name="to-detect-mapping-differences"></a>Aby znaleźć różnice mapowania
[Metody DetectMappingDifferences](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) wybiera i zwraca jedną z mapy niezależnego fragmentu (lokalnego lub globalnego) jako źródło prawdy i uzgadnia mapowanie na obu mapach niezależnego fragmentu (GSM i LSM).

   ```
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Lokalizacji* Określa nazwę serwera i nazwę bazy danych. 
* *ShardMapName* parametru jest nazwa mapy niezależnego fragmentu. Jest to tylko wymagane, jeśli wiele map niezależnego fragmentu są zarządzane przez tego samego menedżera mapy niezależnego fragmentu. Opcjonalny. 

## <a name="to-resolve-mapping-differences"></a>Aby rozwiązać różnice mapowania
[Metody ResolveMappingDifferences](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) wybiera jeden map niezależnego fragmentu (lokalnego lub globalnego) jako źródło prawdy i uzgadnia mapowanie na obu mapach niezależnego fragmentu (GSM i LSM).

   ```
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* parametru wylicza różnice w mapowania między GSM i LSM dla określonych niezależnego fragmentu. 
* [Wyliczenie MappingDifferenceResolution](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) służy do wskazywania metody rozpoznawania różnica między niezależnego fragmentu mapowania. 
* **MappingDifferenceResolution.KeepShardMapping** zalecane jest gdy LSM zawiera mapowania dokładne i w związku z tym mapowanie w niezależnych powinien być używany. Jest to typowe w przypadku, jeśli istnieje trybu failover: niezależnych znajduje się teraz na nowym serwerze. Ponieważ najpierw należy usunąć niezależnych GSM (przy użyciu metody RecoveryManager.DetachShard), mapowanie już nie istnieje na GSM. W związku z tym LSM należy użyć do przywrócenia niezależnego fragmentu mapowania.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Dołącz identyfikator niezależnego fragmentu do ShardMap po przywróceniu niezależnego fragmentu
[Metody AttachShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) dołącza podany identyfikator niezależnego fragmentu do mapy niezależnego fragmentu. Następnie wykrycia niespójności mapy niezależnego fragmentu i aktualizuje mapowania do dopasowania niezależnego fragmentu w punkcie przywracania niezależnego fragmentu. Przyjęto, że bazy danych jest również nowa nazwa odzwierciedla oryginalna nazwa bazy danych (przed przywróceniem niezależnego fragmentu), od punktu w czasie przywracania domyślnie dołączony sygnatura czasowa nową bazę danych. 

   ```
   rm.AttachShard(location, shardMapName)
   ``` 

* *Lokalizacji* parametru jest nazwa serwera i nazwa bazy danych, niezależnych dołączony. 
* *ShardMapName* parametru jest nazwa mapy niezależnego fragmentu. Jest to tylko wymagana, gdy wielu map niezależnego fragmentu są zarządzane przez tego samego menedżera mapy niezależnego fragmentu. Opcjonalny. 

W tym przykładzie dodaje niezależnych do mapy niezależnego fragmentu, który został ostatnio przywrócony z wcześniejszego stanu punktu. Ponieważ niezależnego fragmentu (to znaczy mapowanie niezależnych w LSM) została przywrócona, jest potencjalnie niezgodnych z wpisu niezależnego fragmentu w GSM. Poza ten przykładowy kod niezależnych została przywrócona oraz zmieniono jego nazwę oryginalną nazwę bazy danych. Ponieważ został przywrócony, zakłada się, że mapowanie w LSM jest zaufany mapowania. 

   ```
   rm.AttachShard(s.Location, customerMap); 
   var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Trwa aktualizowanie lokalizacji niezależnego fragmentu po przełączeniu geograficznie (przywracanie) odłamków
W przypadku geograficznie trybu failover, dodatkowej bazy danych jest udostępniane zapisu i staje się nowych podstawowej bazy danych. Nazwa serwera i potencjalnie bazy danych (w zależności od konfiguracji), może być inny niż oryginalny podstawowy. W związku z tym wpisy mapowanie niezależnych GSM i LSM muszą zostać usunięte. Podobnie jeśli przywróceniu bazy danych do innej nazwy lub lokalizacji lub do wcześniejszego punktu w czasie, może to powodować niespójności w społeczności maps niezależnego fragmentu. Menedżer mapy niezależnego fragmentu obsługuje dystrybucji otwarte połączenia z poprawną bazą danych. Dystrybucji jest oparty na danych w planie niezależnego fragmentu i wartość klucza dzielenia na fragmenty, który jest miejscem docelowym żądania aplikacji. Po przełączeniu geograficznie te informacje muszą zostać zaktualizowane nazwę dokładne serwera, nazwa bazy danych i mapowanie niezależnych odzyskanej bazy danych. 

## <a name="best-practices"></a>Najlepsze praktyki
Geograficznie pracy w trybie failover i odzyskiwania są operacje zazwyczaj zarządza administrator chmury aplikacji celowo przy użyciu jednej z funkcjach ciągłości biznesowej baz danych SQL Azure. Planowanie ciągłości biznesowej wymaga procesów, procedury i środki w celu zapewnienia, że operacje biznesowe można nadal bez przeszkód. Dostępne metody jako część klasy RecoveryManager należy w ramach tego przepływu pracy upewnij się, że GSM i LSM są aktualizowane na podstawie działań odzyskiwania. Brak pięć podstawowych kroków, aby poprawnie zapewnienie, że GSM i LSM odzwierciedlają dokładnych informacji po wystąpieniu zdarzenia pracy awaryjnej. Kod aplikacji, aby wykonać te kroki można zintegrować istniejących narzędzi i przepływ pracy. 

1. Pobrać RecoveryManager z ShardMapManager. 
2. Odłączyć starego niezależnego fragmentu z mapy niezależnego fragmentu.
3. Dołącz nowy identyfikator niezależnego fragmentu do mapy niezależnego fragmentu, w tym do nowej lokalizacji niezależnego fragmentu.
4. Wykrył niespójności w mapowanie między GSM i LSM. 
5. Rozwiąż problemy dotyczące różnic między GSM i LSM, ufające LSM. 

W tym przykładzie wykonuje następujące czynności:

1. Usuwa odłamków z mapy niezależnego fragmentu odzwierciedlenia lokalizacji niezależnego fragmentu przed zdarzeniem trybu failover.
2. Dołącza odłamków do mapy niezależnego fragmentu odzwierciedlające nowe lokalizacje niezależnego fragmentu (parametr "Configuration.SecondaryServer" jest nową nazwę serwera, ale tej samej nazwie bazy danych).
3. Pobiera tokeny odzyskiwania został określony poprzez wykrycie mapowania różnice między GSM i LSM dla każdego niezależnego fragmentu. 
4. Rozpoznaje niespójności przez ufające mapowanie LSM z każdym niezależnego fragmentu. 
   
   ```
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

