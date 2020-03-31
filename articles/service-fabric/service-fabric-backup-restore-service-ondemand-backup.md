---
title: Kopia zapasowa na żądanie w sieci szkieletowej usług Azure
description: Użyj funkcji tworzenia kopii zapasowych i przywracania w sieci szkieletowej usług, aby wykonać kopię zapasową danych aplikacji na podstawie potrzeb.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458425"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Kopia zapasowa na żądanie w sieci szkieletowej usług Azure

Można wyw kopii zapasowej danych niezawodnych usług stanowych i wiarygodnych podmiotów w celu rozwiązania scenariuszy awarii lub utraty danych.

Usługa Azure Service Fabric ma funkcje [okresowej kopii zapasowej danych](service-fabric-backuprestoreservice-quickstart-azurecluster.md) i tworzenia kopii zapasowych danych na podstawie potrzeb. Kopia zapasowa na żądanie jest przydatna, ponieważ chroni przed_uszkodzeniem danych_ _utraty_/danych z powodu planowanych zmian w podstawowej usłudze lub jej środowisku.

Funkcje tworzenia kopii zapasowych na żądanie są przydatne do przechwytywania stanu usług przed ręcznym wyzwoleniem operacji usługi lub środowiska usługi. Na przykład jeśli wprowadzać zmiany w plikach binarnych usługi podczas uaktualniania lub obniżania poziomu usługi. W takim przypadku kopia zapasowa na żądanie może pomóc w ochronie danych przed uszkodzeniem przez błędy kodu aplikacji.
## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj moduł Microsoft.ServiceFabric.Powershell.http [W wersji zapoznawczej] do nawiązywanych wywołań konfiguracyjnych.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Upewnij się, że program `Connect-SFCluster` Cluster jest połączony za pomocą polecenia przed wykonaniem dowolnego żądania konfiguracji przy użyciu modułu Microsoft.ServiceFabric.Powershell.http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

Kopia zapasowa na żądanie wymaga szczegółów magazynu do przekazywania plików kopii zapasowej. Lokalizację kopii zapasowej na żądanie można określić w zasadach okresowych kopii zapasowych lub w żądaniu kopii zapasowej na żądanie.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Kopia zapasowa na żądanie do magazynu określona przez zasady okresowego tworzenia kopii zapasowych

Zasady okresowej kopii zapasowej można skonfigurować tak, aby używały partycji niezawodnej usługi stanowej lub niezawodnego aktora do dodatkowej kopii zapasowej na żądanie w magazynie.

Poniższy przypadek jest kontynuacją scenariusza w [włączanie okresowej kopii zapasowej dla niezawodnej usługi stanowej i wiarygodnych aktorów.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) W takim przypadku można włączyć zasady tworzenia kopii zapasowych do korzystania z partycji i kopii zapasowej występuje z ustawioną częstotliwością w usłudze Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

Użyj interfejsu API [BackupPartition,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) aby skonfigurować wyzwalanie kopii `974bd92a-b395-4631-8a7f-53bd4ae9cf22`zapasowej na żądanie dla identyfikatora partycji .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Użyj interfejsu API [GetBackupProgress,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) aby włączyć śledzenie [postępu tworzenia kopii zapasowych na żądanie](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Kopia zapasowa na żądanie do określonego magazynu

Można zażądać kopii zapasowej na żądanie dla partycji niezawodnej usługi stanowej lub niezawodnego aktora. Podaj informacje o magazynie w ramach żądania kopii zapasowej na żądanie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

Użyj interfejsu API [BackupPartition,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) aby skonfigurować wyzwalanie kopii `974bd92a-b395-4631-8a7f-53bd4ae9cf22`zapasowej na żądanie dla identyfikatora partycji . Dołącz następujące informacje usługi Azure Storage:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Za pomocą interfejsu [API GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) można skonfigurować śledzenie [postępu tworzenia kopii zapasowych na żądanie.](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)

### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług
Upewnij się, że tryb zaawansowany został włączony w ustawieniach Eksploratora sieci szkieletowej usług.
1. Wybierz żądane partycje i kliknij akcje. 
2. Wybierz pozycję Trigger Partition Backup i wypełnij informacje dotyczące platformy Azure:

    ![Kopia zapasowa partycji wyzwalacza][0]

    lub FileShare:

    ![Trigger Partition Backup FileShare][1]

## <a name="tracking-on-demand-backup-progress"></a>Śledzenie postępu tworzenia kopii zapasowych na żądanie

Partycja niezawodnej usługi stanowej lub niezawodnego aktora akceptuje tylko jedno żądanie kopii zapasowej na żądanie w czasie. Inne żądanie można zaakceptować dopiero po zakończeniu bieżącego żądania kopii zapasowej na żądanie.

Różne partycje mogą wyzwalać żądania kopii zapasowej na żądanie w tym samym czasie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Żądania kopii zapasowej na żądanie mogą być w następujących stanach:

- **Zaakceptowane:** Kopia zapasowa została uruchomiona na partycji i jest w toku.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Powodzenie,** **niepowodzenie**lub **limit czasu:** Żądana kopia zapasowa na żądanie może zostać ukończona w dowolnym z następujących stanów:
  - **Powodzenie:** Stan kopii zapasowej _powoda wskazuje,_ że stan partycji został pomyślnie utworzony kopię zapasową. Odpowiedź zapewnia _BackupEpoch_ i _BackupLSN_ dla partycji wraz z czasem w czasie UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Błąd:** Stan kopii zapasowej _awarii_ wskazuje, że wystąpił błąd podczas wykonywania kopii zapasowej stanu partycji. Przyczyna awarii jest podana w odpowiedzi.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Limit czasu:** Stan kopii zapasowej _limitu czasu_ wskazuje, że nie można utworzyć kopii zapasowej stanu partycji w określonym czasie. Domyślna wartość limitu czasu wynosi 10 minut. Inicjuj nowe żądanie kopii zapasowej na żądanie z większą [krotną wypłatą kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) w tym scenariuszu.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Następne kroki

- [Opis okresowej konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odwołanie do interfejsu API REST w yrestore backuprestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png