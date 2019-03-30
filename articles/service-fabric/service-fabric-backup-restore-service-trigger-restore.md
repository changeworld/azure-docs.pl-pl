---
title: Przywracanie kopii zapasowej w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Użyj okresowych kopii zapasowych i przywracanie funkcji w usłudze Service Fabric do przywrócenia danych z kopii zapasowych danych aplikacji.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: a82004fdd6bbb4eda0842670f210f846f9446384
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667178"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Przywracanie kopii zapasowej w usłudze Azure Service Fabric

W usłudze Azure Service Fabric Reliable Stateful services i Reliable Actors można zachować modyfikowalny, autorytatywny stan po zakończeniu transakcji żądań i odpowiedzi. Usługa stanowa może przestaną działać przez dłuższy czas lub utratę informacji z powodu awarii. Jeśli tak się stanie, usługa musi zostać przywrócone z najnowszych dopuszczalne kopii zapasowej tak, aby kontynuować pracę.

Na przykład można skonfigurować usługi do tworzenia kopii zapasowych swoje dane, aby zapewnić ochronę przed w następujących scenariuszach:

- **W przypadku odzyskiwania po awarii**: Trwałą utratę cały klaster usługi Service Fabric.
- **Przypadek utraty danych**: Trwałą utratę większości replik partycji usługi.
- **Przypadek utraty danych**: Przypadkowym uszkodzeniem lub usunięciem usługi. Na przykład administrator usunie błędnie usługi.
- **Przypadek uszkodzenie danych**: Błędy w usłudze spowodować uszkodzenie danych. Na przykład uszkodzenia danych może się zdarzyć, gdy uaktualniania kodu usługi zapisuje dane wadliwe Reliable Collection. W takim przypadku trzeba przywrócić kodu i danych do wcześniejszego stanu.

## <a name="prerequisites"></a>Wymagania wstępne

- Wyzwalanie przywracania _błędów usługi analizy_ musi być włączona dla klastra.
- _Kopii zapasowej przywracania usługi (BRS)_ utworzona kopia zapasowa.
- Przywracanie mogą być wywoływane tylko w partycji.

## <a name="triggered-restore"></a>Wyzwolono Przywracanie

Przywracanie mogą być wywoływane dla każdego z następujących scenariuszy:

- Przywracanie danych _odzyskiwania po awarii_.
- Przywracanie danych _uszkodzenie/utracie danych_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Przywracanie danych w przypadku odzyskiwania po awarii

W przypadku utraty całego klastra usługi Service Fabric można odzyskać danych dla partycji usługa stanowa niezawodne i Reliable Actors. Żądany kopii zapasowych można wybrać z listy gdy używasz [GetBackupAPI przy użyciu magazynu kopii zapasowych szczegółów](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Wyliczenie kopii zapasowej może być dla aplikacji, usług lub partycji.

W poniższym przykładzie przyjęto założenie, że utracone klaster jest w tym samym klastrze, który jest określany w [włączenie okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). W tym przypadku `SampleApp` jest wdrażany za pomocą zasad tworzenia kopii zapasowej są włączone, i kopie zapasowe są skonfigurowane do usługi Azure Storage.

Uruchom skrypt programu PowerShell, aby powrócić do listy kopie zapasowe utworzone dla wszystkich partycji wewnątrz za pomocą interfejsu API REST `SampleApp` aplikacji. Interfejs API wymaga informacji magazynu kopii zapasowych, aby wyświetlić listę dostępnych kopii zapasowych.

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

Aby wyzwolić przywracania, wybierz jedną z kopii zapasowych. Na przykład bieżącej kopii zapasowej na potrzeby odzyskiwania po awarii może być następującej kopii zapasowej:

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

Dla interfejsu API przywracania, należy podać _BackupId_ i _BackupLocation_ szczegółowe informacje.

Należy również wybrać partycję docelową alternatywne klastra zgodnie z opisem w [schemat partycji](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Kopia zapasowa alternatywne klastra zostanie przywrócony do partycji określona w schemacie partycji z oryginalnego klastra utracone.

Jeśli identyfikator partycji w klastrze alternatywne `1c42c47f-439e-4e09-98b9-88b8f60800c6`, można go mapować do oryginalnego Identyfikatora partycji klastra `974bd92a-b395-4631-8a7f-53bd4ae9cf22` porównując klucz wysoka i niska wartość klucza dla _Ranged partycjonowania (UniformInt64Partition)_.

Dla _o nazwie partycjonowanie_, nazwa wartość jest porównywana do identyfikowania partycji docelowej w klastrze alternatywne.

Żądania przywracania względem partycji kopii zapasowej klastra przy użyciu następujących [API przywracania](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Możesz śledzić postęp przywracania z TrackRestoreProgress.

### <a name="data-restore-for-data-corruptiondata-loss"></a>Przywracanie danych _uszkodzenie danych_/_utraty danych_

Aby uzyskać _utraty danych_ lub _uszkodzenie danych_, partycji kopii zapasowych usługi Reliable Stateful i partycji w elementach Reliable Actors można przywrócić do dowolnej wybranej kopii zapasowych.

Poniższy przykład jest kontynuacją [włączenie okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). W tym przykładzie zasad tworzenia kopii zapasowej jest włączona dla partycji, a usługa jest tworzenia kopii zapasowych na żądaną częstotliwość w usłudze Azure Storage.

Wybierz kopię zapasową z danych wyjściowych [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). W tym scenariuszu kopii zapasowej jest generowana z tym samym klastrze, przed.

Aby wyzwolić przywracania, wybierz kopię zapasową z listy. Dla bieżącego _utraty danych_/_uszkodzenie danych_, wybierz kopię zapasową następujących:

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

W przypadku interfejsu API przywracania, podaj _BackupId_ i _BackupLocation_ szczegółowe informacje. Klaster ma włączoną kopią zapasową więc usługi Service Fabric _kopii zapasowej przywracania usługi (BRS)_ Określa lokalizację magazynu poprawną ze skojarzonych zasad kopii zapasowych.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Możesz śledzić postępy przywracania przy użyciu TrackRestoreProgress.

## <a name="track-restore-progress"></a>Śledzenie postępu przywracania

Partycji usługi Reliable Stateful lub Reliable Actor akceptuje tylko jedno żądanie przywracania w danym momencie. Partycja akceptuje tylko kolejnego żądania po zakończeniu bieżącego żądania przywracania. Wiele żądań przywracania mogą być wywoływane na różne partycje w tym samym czasie.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Żądanie przywracania postępów w następującej kolejności:

1. **Zaakceptowane**: _Zaakceptowano_ Przywróć stan wskazuje, że żądanej partycji zostało wyzwolone z parametrami właściwe żądanie.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: _InProgress_ Przywróć stan wskazuje, że proces przywracania odbywa się w partycji z kopiami zapasowymi, o których wspomniano w żądaniu. Raporty partycji _dataloss_ stanu.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Powodzenie**, **błąd**, lub **limitu czasu**: Żądana przywracania można wykonać w dowolnej z następujących stanów. Każdy stan zawiera następujące informacje dotyczące znaczenia i odpowiedzi:
    - **Success**: A _Powodzenie_ Przywróć stan wskazuje na stan odzyskała partycji. Raporty partycji _RestoredEpoch_ i _RestoredLSN_ stany, wraz z czasu w formacie UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Błąd**: A _błąd_ Przywróć stan wskazuje niepowodzenie żądania przywracania. Przyczyną błędu jest zgłaszany.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Limit czasu**: A _limitu czasu_ Przywróć stan wskazuje, że żądanie ma limit czasu. Utwórz nowe żądanie przywracania z większą [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Domyślny limit czasu wynosi 10 minut. Upewnij się, że partycja nie jest w stanie utraty danych przed zażądaniem przywracania ponownie.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatycznego przywracania

Możesz skonfigurować usługę Reliable Stateful i klastrze partycji elementów Reliable Actors usługi Service Fabric _auto przywracania_. W ramach zasad tworzenia kopii zapasowej ustaw `AutoRestore` do _true_. Włączanie _auto przywracania_ automatycznie przywraca dane z najnowszej kopii zapasowej partycji, gdy zostaje zgłoszone utraty danych. Aby uzyskać więcej informacji, zobacz:

- [Automatyczne włączanie przywracania w zasadach kopii zapasowych](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Dokumentacja interfejsu API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Dokumentacja interfejsu API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Kolejne kroki
- [Opis okresowe konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
