---
title: Kopii zapasowej na żądanie w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Użyj funkcji kopii zapasowej i przywracania funkcji w usłudze Service Fabric, aby utworzyć kopię zapasową danych aplikacji na podstawie potrzeb.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: bed3402de83984cae9134fe44058980ec18861b3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413941"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Kopii zapasowej na żądanie w usłudze Azure Service Fabric

Można utworzyć kopię zapasową danych Reliable Stateful services i Reliable Actors awarii lub dane utraty scenariuszy.

Usługa Azure Service Fabric zawiera funkcje dla [okresowe tworzenie kopii zapasowych danych](service-fabric-backuprestoreservice-quickstart-azurecluster.md) i kopii zapasowej danych na podstawie potrzeb. Kopii zapasowej na żądanie jest przydatne, ponieważ chroni je przed _utraty danych_/_uszkodzenie danych_ z powodu planowanych zmian w usłudze podstawowej lub jego środowiska.

Funkcje tworzenia kopii zapasowej na żądanie są przydatne do przechwytywania stanu usługi, zanim ręcznie wyzwolisz usługi lub operacji środowiska usługi. Na przykład, jeśli wprowadzisz zmiany w pliki binarne usługi podczas uaktualniania lub zmiany na starszą wersję usługi. W takim przypadku kopii zapasowej na żądanie może pomóc chronić dane przed uszkodzeniem przez błędy kodu aplikacji.
## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj moduł Microsoft.ServiceFabric.Powershell.Http [w wersja zapoznawcza] dla konfiguracji połączeń.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Upewnij się, że klaster jest podłączony za pomocą `Connect-SFCluster` polecenia przed dokonaniem wszelkie żądania konfiguracji, za pomocą modułu Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Wyzwolenie tworzenia kopii zapasowej na żądanie

Kopii zapasowej na żądanie wymaga szczegóły magazynu służącego do przekazywania plików kopii zapasowej. Lokalizacja kopii zapasowej na żądanie, określ, okresowe zasad tworzenia kopii zapasowej lub żądania utworzenia kopii zapasowej na żądanie.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Kopii zapasowej na żądanie w magazynie określonym przez okresowe zasad tworzenia kopii zapasowej

Można skonfigurować okresowe zasad tworzenia kopii zapasowej na potrzeby partycji usługi Reliable Stateful lub Reliable Actor dodatkowych kopii zapasowej na żądanie do magazynu.

Następujący przypadek jest kontynuacja scenariusza w [włączenie okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). W przypadku włączenia zasad tworzenia kopii zapasowej do używania partycji i tworzenia kopii zapasowej z częstotliwością zestawu w usłudze Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu Powershell

Użyj [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) interfejsu API, aby skonfigurować wyzwalanie dla kopii zapasowej na żądanie dla partycji o identyfikatorze `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Użyj [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) interfejsu API, aby włączyć śledzenie dla [postępu tworzenia kopii zapasowej na żądanie](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Kopii zapasowej na żądanie do określonego magazynu

Możesz zażądać kopii zapasowej na żądanie dla partycji usługi Reliable Stateful lub Reliable Actor. Podaj informacje magazynu w ramach żądania utworzenia kopii zapasowej na żądanie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu Powershell

Użyj [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) interfejsu API, aby skonfigurować wyzwalanie dla kopii zapasowej na żądanie dla partycji o identyfikatorze `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Podaj następujące informacje usługi Azure Storage:

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

Możesz użyć [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) interfejsu API, aby skonfigurować śledzenia [postępu tworzenia kopii zapasowej na żądanie](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

## <a name="tracking-on-demand-backup-progress"></a>Śledzenie postępu tworzenia kopii zapasowej na żądanie

Partycji usługi Reliable Stateful lub Reliable Actor akceptuje tylko jedno żądanie kopii zapasowej na żądanie w danym momencie. Inne żądanie może akceptowane tylko w przypadku, po zakończeniu bieżącego żądania utworzenia kopii zapasowej na żądanie.

Różne partycje można uruchomić żądania tworzenia kopii zapasowej na żądanie, w tym samym czasie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Żądanie kopii zapasowej na żądanie nie może być w jednym z następujących stanów:

- **Zaakceptowane**: Kopia zapasowa została uruchomiona na partycji i jest w toku.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Powodzenie**, **błąd**, lub **limitu czasu**: Żądana kopii zapasowej na żądanie można wykonać w dowolnej z następujących stanów:
  - **Powodzenie**: A _Powodzenie_ kopii zapasowej stan wskazuje, że stan partycji jest kopia zapasowa wykonana pomyślnie. Odpowiedź zawiera _BackupEpoch_ i _BackupLSN_ dla partycji oraz godzina w formacie UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Błąd**: A _błąd_ kopii zapasowej stan wskazuje, że wystąpił błąd podczas wykonywania kopii zapasowej stan partycji. Przyczyną błędu jest wyrażona w odpowiedzi.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Limit czasu**: A _limitu czasu_ kopii zapasowej stan wskazuje, że kopia zapasowa stanu partycji nie można utworzyć w określonym czasie. Domyślna wartość limitu czasu to 10 minut. Zainicjowanie nowego żądania utworzenia kopii zapasowej na żądanie z większą [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) w tym scenariuszu.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Kolejne kroki

- [Zrozumienie okresowe konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
