---
title: Kopii zapasowej na żądanie w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Używać usługi Service Fabric kopii zapasowej i przywracania funkcji do tworzenia kopii zapasowych danych aplikacji na podstawie potrzeb.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 2e83dcb4ce42a0c08ce482e9ffbbf1ac18634fd5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730311"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Kopii zapasowej na żądanie w usłudze Azure Service Fabric

Dane Reliable Stateful services i Reliable Actors można można utworzyć kopie zapasowe adres scenariuszy utraty danych lub po awarii.

Usługa Service Fabric jest wyposażona w funkcje [okresowe tworzenie kopii zapasowych danych](service-fabric-backuprestoreservice-quickstart-azurecluster.md) i tworzenie kopii zapasowych danych na podstawie potrzeb. Kopii zapasowej na żądanie jest przydatne, ponieważ chroni je przed _utraty danych_/_uszkodzenie danych_ spowodowany zaplanowanych zmian w usłudze podstawowej lub jego środowiska.

Funkcje tworzenia kopii zapasowej na żądanie jest przydatne podczas przechwytywania stanu usługi przed dowolną operacją wyzwalanych ręcznie, związane z usługi lub środowiska usługi. Np. zmiana pliki binarne usługi, która jest, uaktualnianie lub zmiany na starszą wersję usługi; jak będzie miał danych chroniony przed uszkodzeniem danych przez usterki w kodzie aplikacji.

## <a name="triggering-on-demand-backup"></a>Wyzwolenie tworzenia kopii zapasowej na żądanie

Tworzenie kopii zapasowej na żądanie wymaga szczegóły magazynu przekazywania plików kopii zapasowej. Tworzenie kopii zapasowej na żądanie będzie miało miejsce w pamięci masowej, określone w okresowych zasad tworzenia kopii zapasowej lub w określonym magazynie żądania utworzenia kopii zapasowej na żądanie.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Kopii zapasowej na żądanie w magazynie określonym przez okresowe zasad tworzenia kopii zapasowej

Partycji usługi Reliable Stateful lub Reliable Actors można żądać dla dodatkowych na potrzeby poszczególnych kopii zapasowej magazynu określone w okresowych zasad tworzenia kopii zapasowej. 

Następujący przypadek jest to kontynuacja przykładu, zgodnie z opisem w [włączenie okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), której partycja ma włączone zasady tworzenia kopii zapasowych i trwa na żądaną częstotliwość tworzenia kopii zapasowej w usłudze Azure Storage.

Kopii zapasowej na żądanie dla partycji o identyfikatorze `974bd92a-b395-4631-8a7f-53bd4ae9cf22` mogą być wywoływane [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) interfejsu API. 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[Postępu tworzenia kopii zapasowej na żądanie](service-fabric-backuprestoreservice-ondemand-backup.md#tracking-on-demand-backup-progress) może być śledzone przez [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) interfejsu API.

### <a name="on-demand-backup-to-specified-storage"></a>Kopii zapasowej na żądanie do określonego magazynu

Tworzenie kopii zapasowej na żądanie można żądać dla partycji usługi Reliable Stateful lub Reliable Actor wraz z informacjami magazynu. Należy podać informacje magazynu w ramach żądania utworzenia kopii zapasowej na żądanie.

Kopii zapasowej na żądanie dla partycji o identyfikatorze `974bd92a-b395-4631-8a7f-53bd4ae9cf22` mogą być wywoływane [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) interfejsu API za pomocą usługi Azure storage informacje, jak pokazano poniżej.

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

[Postępu tworzenia kopii zapasowej na żądanie](service-fabric-backuprestoreservice-ondemand-backup.md#tracking-on-demand-backup-progress) może być śledzone przez [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) interfejsu API.


## <a name="tracking-on-demand-backup-progress"></a>Śledzenie postępu tworzenia kopii zapasowej na żądanie

Partycji usługi Reliable Stateful lub Reliable Actor akceptuje tylko jedno żądanie kopii zapasowej na żądanie w danym momencie. Inne żądanie może akceptowane tylko wtedy, gdy bieżące żądania utworzenia kopii zapasowej na żądanie zostało zakończone. 

Wiele żądań tworzenia kopii zapasowej na żądanie mogą być wywoływane na różne partycje w tym samym czasie.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Żądania utworzenia kopii zapasowej na żądanie postępu może być jedną z następujących stanów

* **Zaakceptowane** — kopia zapasowa została zainicjowana na partycji i jest w toku.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Powodzenie / niepowodzenie / Timeout** — żądaną kopii zapasowej na żądanie można wykonać w dowolnej z następujących stanów. Każdy stan zawiera następujące informacje dotyczące znaczenia i odpowiedzi.

    * **Powodzenie** -stanu kopii zapasowej jako _Powodzenie_ wskazuje, że stan partycji jest kopia zapasowa wykonana pomyślnie. Odpowiedź będzie zapewniać __BackupEpoch__ i __BackupLSN__ dla partycji oraz godzina w formacie UTC.
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Błąd** -stanu kopii zapasowej jako _błąd_ wskazuje wystąpił błąd podczas wykonywania kopii zapasowej stan partycji. W odpowiedzi należy podawać przyczynę niepowodzenia.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Limit czasu** -stanu kopii zapasowej jako _limitu czasu_ wskazuje, że kopia zapasowa stanu partycji nie można utworzyć w podanym okresie; domyślnej wartości limitu czasu wynosi 10 minut. Zainicjowanie nowego żądania utworzenia kopii zapasowej z większą [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) w żądania utworzenia kopii zapasowej na żądanie jest zalecane, w tym scenariuszu.

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
- [Opis okresowe konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

