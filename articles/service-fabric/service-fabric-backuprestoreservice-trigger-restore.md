---
title: Przywracanie kopii zapasowej w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Używać usługi Service Fabric okresowych kopii zapasowych i przywracania funkcji w celu przywrócenia danych z kopii zapasowych danych aplikacji.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d0b385e5856e85ad0d7d9baeaddbac0fb4230e3b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730282"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Przywracanie kopii zapasowej w usłudze Azure Service Fabric


Niezawodne usługi stanowe i Reliable Actors w usłudze Service Fabric można zachować modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzi lub ukończoną transakcję. Jeśli usługa stanowa przestanie działać przez dłuższy czas lub traci informacji z powodu awarii, może być konieczne można przywrócić do najnowszych dopuszczalne wykonywania kopii zapasowej jego stan do kontynuowania świadczenie usług, po jego wróci.

Na przykład usługa warto tworzyć kopie zapasowe swoich danych w celu ochrony z następujących scenariuszy:

- W przypadku trwałą utratę cały klaster usługi Service Fabric. **(W przypadku odzyskiwania po awarii — odzyskiwanie po awarii)**
- Trwałą utratę większości replik partycji usługi. **(W przypadku utraty danych)**
- Błędy administracyjne, według której stan przypadkowo pobiera usunięty lub uszkodzony. Administrator z uprawnieniami wystarczające Usuwa błędnie usługi. **(W przypadku utraty danych)**
- Błędy w usłudze, które powodują uszkodzenie danych. Na przykład uszkodzenie danych może się zdarzyć, gdy uaktualniania kodu usługi, który rozpoczyna się zapisywanie uszkodzonych danych w niezawodnej kolekcji. W takim przypadku zarówno kod, jak i danych może być konieczne można przywrócić do poprzedniego stanu. **(W przypadku uszkodzenia danych)**


## <a name="prerequisites"></a>Wymagania wstępne
* Aby wyzwolić, Przywróć _błędów usługi analizy_ powinno być włączone dla klastra
* Kopia zapasowa była przywracania powinna podjęta przez _kopii zapasowej przywracania usługi (BRS)_
* Przywracanie mogą być wywoływane tylko w partycji.

## <a name="triggering-restore"></a>Wyzwalanie przywracania obiektu

Przywracanie może być dla każdego z poniższych scenariuszy 
* Przywracanie danych, w przypadku powstania _odzyskiwania po awarii_ (DR)
* Przywracanie danych, w przypadku powstania _uszkodzenie danych / utraty danych_



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Przywracanie danych, w przypadku powstania _odzyskiwania po awarii_ (DR)
W przypadku całego klastra usługi Service Fabric utratę danych dla partycji usługa stanowa niezawodne i Reliable Actors można przywrócić do alternatywnego klastra. Żądany kopii zapasowych wybiera się z szeregu [GetBackupAPI przy użyciu magazynu kopii zapasowych szczegółów](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Wyliczenie kopii zapasowej może być dla aplikacji, usług lub partycji.

Załóżmy, że utracone klastra został klastra opisane w [włączenie okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), który miał `SampleApp` wdrożona, gdzie partycji został mających włączone zasady tworzenia kopii zapasowych i kopie zapasowe były wykonywane w usłudze Azure Storage. 


Wykonaj następujący skrypt programu PowerShell do wywołania interfejsu API REST, można wyliczyć kopie zapasowe utworzone dla wszystkich partycji wewnątrz `SampleApp` w utracone klastra usługi Service Fabric. Wyliczenie interfejsu API wymaga informacji magazynu, gdzie są przechowywane kopie zapasowe aplikacji, wyliczania dostępnych kopii zapasowych. 

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Przykładowe dane wyjściowe dla powyższych Uruchom:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```



Służącą do wyzwalania przywracania, należy wybrać odpowiednią kopię zapasową. Pozwól żądaną kopii zapasowej dla bieżącego odzyskiwania po awarii (DR) być następującej kopii zapasowej

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

Dla interfejsu API przywracania, należy podać __BackupId__ i __BackupLocation__ szczegółowe informacje. Partycja w klastrze alternatywne musi wybrane zgodnie [schemat partycji](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Odpowiada za użytkownika wybranej partycji docelowej w celu przywrócenia kopii zapasowej z alternatywnych klastra zgodnie z schemat partycji w oryginalnym klastrze utracone.

Przyjęto, że identyfikator partycji w klastrze alternatywny zostało `1c42c47f-439e-4e09-98b9-88b8f60800c6`, która mapuje do oryginalnego Identyfikatora partycji klastra `974bd92a-b395-4631-8a7f-53bd4ae9cf22` porównując klucz wysoka i niska wartość klucza dla _Ranged partycjonowania (UniformInt64Partition)_.

Dla _o nazwie partycjonowanie_, nazwa wartość jest porównywana do identyfikowania partycji docelowej w klastrze alternatywne.

Przywracanie jest wymagany dla partycji kopii zapasowej klastra przez następujące [przywrócić interfejsu API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
Postęp przywracania może być [TrackRestoreProgress](service-fabric-backuprestoreservice-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Przywracanie danych, w przypadku powstania _uszkodzenie danych / utraty danych_

W przypadku _utraty danych_ lub _uszkodzenie danych_ danych dla partycji usługi Reliable Stateful i Reliable Actors można przywrócić do dowolnej wybranej kopii zapasowych. Następujący przypadek jest to kontynuacja przykładu, zgodnie z opisem w [włączenie okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), której partycja ma włączone zasady tworzenia kopii zapasowych i trwa na żądaną częstotliwość tworzenia kopii zapasowej w usłudze Azure Storage. 

Wybrano odpowiednią kopii zapasowej z danych wyjściowych [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). W tym scenariuszu kopii zapasowej jest generowany na podstawie tego samego klastra w przeszłości.
Służącą do wyzwalania przywracania, należy wybrać odpowiednią kopię zapasową z listy. Pozwól żądaną kopii zapasowej dla bieżącego _utraty danych_ / _uszkodzenie danych_ można następujące kopii zapasowej

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

Dla interfejsu API przywracania, należy podać __BackupId__ i __BackupLocation__ szczegółowe informacje. Ponieważ klaster kopii zapasowej włączono usługi Service Fabric _kopii zapasowej przywracania usługi (BRS)_ Określa lokalizację magazynu poprawną ze skojarzonych zasad kopii zapasowych.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Postęp przywracania może być [TrackRestoreProgress](service-fabric-backuprestoreservice-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>Śledzenia postępu przywracania

Partycji usługi Reliable Stateful lub Reliable Actor akceptuje tylko jedno żądanie przywracania w danym momencie. Inne żądanie może akceptowane tylko wtedy, gdy bieżące żądanie przywracania została ukończona. Wiele żądań przywracania mogą być wywoływane na różne partycje w tym samym czasie.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

Przywracanie żądanie będzie realizowane w następującej kolejności

1. __Zaakceptowano__ -Przywróć stan jako _zaakceptowano_ wskazuje, że żądanie zostało wyzwolone z parametrami właściwe żądanie.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __W toku__ -Przywróć stan jako _InProgress_ wskazuje, że partycja zostaną poddane przywracania z kopiami zapasowymi, o których wspomniano w żądaniu. Partycja będzie zgłaszać _dataloss_ stanu.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Powodzenie__/ __błąd__/ __limitu czasu__ -żądany przywracania można wykonać w dowolnej z następujących stanów. Każdy stan zawiera następujące informacje dotyczące znaczenia i odpowiedzi.
       
    * __Powodzenie__ -Przywróć stan jako _Powodzenie_ wskazuje stan partycji została ponownie nawiązana. Odpowiedź zawiera RestoreEpoch i RestordLSN dla partycji oraz godzina w formacie UTC. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Błąd__ -Przywróć stan jako _błąd_ wskazuje niepowodzenie żądania przywracania. W żądaniu należy podawać przyczynę niepowodzenia.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Limit czasu__ -Przywróć stan jako _limitu czasu_ wskazuje żądanie ma limit czasu. Nowe żądanie przywracania z większą [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) zaleca się; wartość domyślna limitu czasu wynosi 10 minut. Zaleca się, aby upewnić się, czy partycja jest poza stan utraty danych, przed zażądaniem Przywracanie ponownie.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Automatycznego przywracania

Partycje usługi Reliable Stateful i Reliable Actors w klastrze usługi Service Fabric można skonfigurować dla _auto przywracania_. Podczas tworzenia zasad tworzenia kopii zapasowej, zasady mogą mieć `AutoRestore` równa _true_.  Włączanie _auto przywracania_ dla partycji, przywraca dane z najnowszej kopii zapasowej, jeśli jest zgłaszana utraty danych.
 
 [Automatyczne włączanie przywracania w zasadach kopii zapasowych](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[Dokumentacja interfejsu API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[dokumentacja interfejsu API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Kolejne kroki
- [Opis okresowe konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
