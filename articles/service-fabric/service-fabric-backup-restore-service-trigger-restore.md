---
title: Przywracanie kopii zapasowej w sieci szkieletowej usług Azure
description: Użyj funkcji okresowej kopii zapasowej i przywracania w sieci szkieletowej usług do przywracania danych z kopii zapasowej danych aplikacji.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1737102ee652cc2263bd0a908c1336bc93a6757b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377909"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Przywracanie kopii zapasowej w sieci szkieletowej usług Azure

W usłudze Azure Service Fabric niezawodne usługi stanowe i niezawodne podmioty mogą zachować zmienny, autorytatywny stan po zakończeniu transakcji żądania i odpowiedzi. Usługa stanowa może zejść na długi czas lub utracić informacje z powodu awarii. Jeśli tak się stanie, usługa musi zostać przywrócona z najnowszej możliwej do zaakceptowania kopii zapasowej, aby mogła nadal działać.

Na przykład można skonfigurować usługę do utworzenia kopii zapasowej swoich danych w celu ochrony przed następującymi scenariuszami:

- **Przypadek odzyskiwania po awarii:** trwała utrata całego klastra sieci szkieletowej usług.
- **Przypadek utraty danych:** Trwała utrata większości replik partycji usługi.
- **Przypadek utraty danych:** Przypadkowe usunięcie lub uszkodzenie usługi. Na przykład administrator błędnie usuwa usługę.
- **Przypadek uszkodzenia danych:** Błędy w usłudze powodują uszkodzenie danych. Na przykład uszkodzenie danych może się zdarzyć, gdy uaktualnienie kodu usługi zapisuje wadliwe dane do niezawodnej kolekcji. W takim przypadku może być konieczności przywrócenia kodu i danych do wcześniejszego stanu.

## <a name="prerequisites"></a>Wymagania wstępne

- Aby wyzwolić przywracanie, _usługa analizy błędów (FAS)_ musi być włączona dla klastra.
- _Usługa przywracania kopii zapasowej (BRS)_ utworzyła kopię zapasową.
- Przywracanie można wyzwolić tylko na partycji.
- Zainstaluj moduł Microsoft.ServiceFabric.Powershell.http [W wersji zapoznawczej] do nawiązywanych wywołań konfiguracyjnych.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Upewnij się, że program `Connect-SFCluster` Cluster jest połączony za pomocą polecenia przed wykonaniem dowolnego żądania konfiguracji przy użyciu modułu Microsoft.ServiceFabric.Powershell.http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Wyzwolone przywracanie

Przywracanie można wyzwolić dla dowolnego z następujących scenariuszy:

- Przywracanie danych w _celu odzyskiwania po awarii_.
- Przywracanie danych w celu _uszkodzenia/utraty danych_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Przywracanie danych w przypadku odzyskiwania po awarii

Jeśli cały klaster sieci szkieletowej usług zostanie utracony, można odzyskać dane dla partycji niezawodnej usługi stanowej i wiarygodnych aktorów. Żądaną kopię zapasową można wybrać z listy podczas korzystania z [interfejsu GetBackupAPI ze szczegółami magazynu kopii zapasowych](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Wyliczenie kopii zapasowej może być dla aplikacji, usługi lub partycji.

W poniższym przykładzie załóżmy, że utracony klaster jest tym samym klastrem, do którym się odwoływane jest w [włączaniu okresowej kopii zapasowej dla niezawodnej usługi stanowej i wiarygodnych aktorów.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) W takim `SampleApp` przypadku jest wdrażany z włączonymi zasadami tworzenia kopii zapasowych, a kopie zapasowe są skonfigurowane do usługi Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

Wykonaj skrypt programu PowerShell, aby użyć interfejsu API REST, aby zwrócić `SampleApp` listę kopii zapasowych utworzonych dla wszystkich partycji wewnątrz aplikacji. Interfejs API wymaga informacji o magazynie kopii zapasowych do listy dostępnych kopii zapasowych.

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

Przykładowe dane wyjściowe dla powyższego uruchomienia:

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

Aby wyzwolić przywracanie, wybierz jedną z kopii zapasowych. Na przykład bieżąca kopia zapasowa do odzyskiwania po awarii może być następująca kopia zapasowa:

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

Dla interfejsu API przywracania należy podać szczegóły _BackupId_ i _BackupLocation._

Należy również wybrać partycję docelową w klastrze alternatywnym, jak opisano w [schemacie partycji](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Alternatywna kopia zapasowa klastra jest przywracana do partycji określonej w schemacie partycji z oryginalnego utraconego klastra.

Jeśli identyfikator partycji w `1c42c47f-439e-4e09-98b9-88b8f60800c6`klastrze alternatywnym jest , można `974bd92a-b395-4631-8a7f-53bd4ae9cf22` zamapować go na oryginalny identyfikator partycji klastra, porównując wysoki i niski klucz dla _partycjonowania dystansowego (UniformInt64Partition)_.

W przypadku _partycjonowania nazwanego_wartość nazwy jest porównywana w celu zidentyfikowania partycji docelowej w klastrze alternatywnym.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

Żądanie przywrócenia względem partycji klastra kopii zapasowej przy użyciu następującego [interfejsu API przywracania:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Można śledzić postęp przywracania z TrackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług
Można wyzwolić przywracanie z Eksploratora sieci szkieletowej usług. Upewnij się, że tryb zaawansowany został włączony w ustawieniach Eksploratora sieci szkieletowej usług.
1. Wybierz żądane partycje i kliknij akcje. 
2. Wybierz polecenie Przywracanie partycji wyzwalacza i wypełnij informacje dotyczące platformy Azure:

    ![Przywracanie partycji wyzwalacza][2]

    lub FileShare:

    ![Wyzwalacz akcji plików przywracania partycji][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Przywracanie danych _w_/celu_utraty danych_

W przypadku _utraty danych_ lub _uszkodzenia danych,_ kopie zapasowe partycji dla niezawodnej usługi stanowej i partycji Reliable Actors można przywrócić do dowolnej z wybranych kopii zapasowych.

Poniższy przykład jest kontynuacją [włączania okresowej kopii zapasowej dla niezawodnej usługi stanowej i wiarygodnych aktorów.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) W tym przykładzie zasady tworzenia kopii zapasowych jest włączona dla partycji, a usługa tworzy kopie zapasowe z żądaną częstotliwością w usłudze Azure Storage.

Wybierz kopię zapasową z danych wyjściowych [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). W tym scenariuszu kopia zapasowa jest generowana z tego samego klastra, co poprzednio.

Aby wyzwolić przywracanie, wybierz kopię zapasową z listy. W przypadku uszkodzenia bieżących _danych utraty_/_danych_wybierz następującą kopię zapasową:

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

Dla interfejsu API przywracania podaj szczegóły _BackupId_ i _BackupLocation._ Klaster ma włączoną kopię zapasową, dzięki czemu _usługa przywracania kopii zapasowych sieci szkieletowej sieci szkieletowej (SERVICE)_ identyfikuje prawidłową lokalizację magazynu na podstawie skojarzonych zasad tworzenia kopii zapasowych.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Postęp przywracania można śledzić za pomocą TrackRestoreProgress.

## <a name="track-restore-progress"></a>Śledzenie postępu przywracania

Partycja niezawodnej usługi stanowej lub niezawodnego aktora akceptuje tylko jedno żądanie przywracania w czasie. Partycja akceptuje tylko inne żądanie po zakończeniu bieżącego żądania przywracania. Wiele żądań przywracania można wyzwolić na różnych partycjach w tym samym czasie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Żądanie przywracania postępuje w następującej kolejności:

1. **Zaakceptowane:** Stan przywracania _zaakceptowane_ wskazuje, że żądana partycja została wyzwolona z poprawnymi parametrami żądania.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress:** Stan przywracania _InProgress_ wskazuje, że przywracanie występuje w partycji z kopią zapasową wymienioną w żądaniu. Partycja zgłasza stan _dataloss._
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Powodzenie,** **Niepowodzenie**lub **Limit czasu:** Żądane przywracanie można ukończyć w dowolnym z następujących stanów. Każdy stan ma następujące znaczenie i szczegóły odpowiedzi:
    - **Powodzenie:** Stan przywracania _powoda_ wskazuje na stan odzyskanej partycji. Partycja raportuje _stany RestoredEpoch_ i _RestoredLSN_ wraz z czasem w czasie utc.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Błąd:** Stan przywracania _awarii_ wskazuje na niepowodzenie żądania przywracania. Zgłaszana jest przyczyna błędu.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Limit czasu:** Stan przywracania _limitu czasu_ wskazuje, że żądanie ma limit czasu. Utwórz nowe żądanie przywracania z większą [funkcją RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Domyślny limit czasu wynosi 10 minut. Upewnij się, że partycja nie jest w stanie utraty danych przed żądaniem przywrócić ponownie.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatyczne przywracanie

Można skonfigurować niezawodną usługę stanową i partycje Reliable Actors w klastrze sieci szkieletowej usług dla _automatycznego przywracania_. W zasadach `AutoRestore` tworzenia kopii zapasowych ustawiona na _true_. Włączenie _automatycznego przywracania_ automatycznie przywraca dane z najnowszej kopii zapasowej partycji po zgłoszeniu utraty danych. Aby uzyskać więcej informacji, zobacz:

- [Automatyczne przywracanie włączania w zasadach tworzenia kopii zapasowych](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Odwołanie do interfejsu API restorepartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Odwołanie do interfejsu API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Następne kroki
- [Opis konfiguracji okresowej kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odwołanie do interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png