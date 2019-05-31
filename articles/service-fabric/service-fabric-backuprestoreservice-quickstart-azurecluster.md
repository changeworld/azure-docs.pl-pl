---
title: Okresowe kopii zapasowej i przywracania w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Używać usługi Service Fabric okresowych kopii zapasowych i przywracania funkcji umożliwiających okresowe tworzenia kopii zapasowych danych aplikacji.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: e81cc1b3d80afd39a74c3046b1f8020e0a524ae4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237378"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Okresowe kopii zapasowej i przywracania w usłudze Azure Service Fabric 
> [!div class="op_single_selector"]
> * [Klastry na platformie Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Klastry autonomiczne](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric to platforma systemów rozproszonych, która ułatwia tworzenie i zarządzanie nimi niezawodne, rozproszony mikrousług na podstawie aplikacji w chmurze. Umożliwia to uruchamianie mikrousługi stanowe i bezstanowe. Usługi stanowe może zachować modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzi lub ukończoną transakcję. Jeśli usługa stanowa przestanie działać przez dłuższy czas lub traci informacji z powodu awarii, może być konieczne można przywrócić do niektórych ostatniej kopii zapasowej stanu, aby móc kontynuować świadczenie usług, po jego wróci do sprawności.

Usługa Service Fabric replikuje stanu w wielu węzłach, aby upewnij się, że usługa o wysokiej dostępności. Nawet w przypadku awarii jednego węzła w klastrze, usługa jest nadal dostępna. W niektórych przypadkach jednak nadal pożądane jest dane usługi wiarygodne szersze awariami.
 
Na przykład usługa warto kopię zapasową danych w celu zapewnienia ochrony przed w następujących scenariuszach:
- W przypadku trwałą utratę cały klaster usługi Service Fabric.
- Trwałą utratę większości replik partycji usługi
- Błędy administracyjne, według której stan przypadkowo pobiera usunięty lub uszkodzony. Administrator z uprawnieniami wystarczające Usuwa błędnie usługi.
- Błędy w usłudze, które powodują uszkodzenie danych. Na przykład to może się zdarzyć, gdy uaktualnianie kodu usługi rozpoczyna się zapisywanie uszkodzonych danych w niezawodnej kolekcji. W takim przypadku zarówno kod, jak i danych może być konieczne można przywrócić do poprzedniego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodną mieć przetwarzanie danych dla analizę biznesową, która miejsce osobno od usługi, która generuje dane w trybie offline.

Usługa Service Fabric udostępnia wbudowane interfejsu API do punktu w czasie [kopia zapasowa i przywracanie](service-fabric-reliable-services-backup-restore.md). Deweloperzy aplikacji może używać tych interfejsów API, aby utworzyć kopię zapasową stanu usługi okresowo. Ponadto aby administratorzy usługi wyzwalanie tworzenia kopii zapasowej z poza usługi w określonym czasie, takie jak przed uaktualnieniem aplikacji, deweloperzy muszą ujawnić kopii zapasowej (i przywracanie) jako interfejsu API z usługi. Obsługa kopii zapasowych jest dodatkowy koszt powyżej tego. Na przykład można wykonać pięciu przyrostowych kopii zapasowych co pół godziny następuje pełnej kopii zapasowej. Po pełnej kopii zapasowej można usunąć poprzednich przyrostowych kopii zapasowych. Takie podejście wymaga dodatkowego kodu, co prowadzi do dodatkowych kosztów, podczas tworzenia aplikacji.

Usługa kopii zapasowej i przywracania w usłudze Service Fabric umożliwia łatwe i automatycznych kopii zapasowych informacji przechowywanych w usług stanowych. Tworzenie kopii zapasowych danych aplikacji w regularnych odstępach czasu jest podstawą dla ochrona przed niedostępność strat i usługi danych. Usługa Service Fabric udostępnia opcjonalne tworzenia kopii zapasowych i przywracania usługi, która pozwala na konfigurowanie okresowych kopii zapasowej niezawodne usługi stanowe (w tym usług aktora) bez konieczności pisania dodatkowego kodu. Również ułatwia tworzenie, przywracanie, poprzednio wykonane kopie zapasowe. 


Usługa Service Fabric udostępnia zestaw interfejsów API, aby osiągnąć następujące funkcje związane z okresowe tworzenie kopii zapasowej i przywracanie funkcji:

- Zaplanować okresowe kopii zapasowej Reliable Stateful services i Reliable Actors z obsługą przekazywania lokalizacji magazynu kopii zapasowej (zewnętrznych). Obsługiwane lokalizacje
    - Azure Storage
    - Udziału plików (lokalny)
- Wyliczanie kopii zapasowych
- Wyzwalanie tworzenia kopii zapasowej ad-hoc partycji
- Przywróć partycji przy użyciu poprzedniej kopii zapasowej
- Czasowo zawieszają kopii zapasowych
- Zarządzanie przechowywania kopii zapasowych (nadchodzących)

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster usługi Service Fabric za pomocą Service Fabric w wersji 6.2 i nowszych. Należy skonfigurować klaster w systemie Windows Server. Zapoznaj się z tym [artykułu](service-fabric-cluster-creation-via-arm.md) kroki umożliwiające utworzenie usługi Service Fabric klastra za pomocą szablonu zasobów platformy Azure.
* Certyfikat X.509 do szyfrowania wymagane do połączenia z magazynem kluczy tajnych do przechowywania kopii zapasowych. Zapoznaj się [artykułu](service-fabric-cluster-creation-via-arm.md) wiedzieć, jak pobrać lub utworzyć certyfikat X.509.
* Aplikacja usługi Service Fabric Reliable Stateful utworzone przy użyciu zestawu SDK usługi Service Fabric w wersji 3.0 lub nowszej. Dla aplikacji przeznaczonych dla platformy .NET Core 2.0, aplikacja powinna utworzona przy użyciu zestawu SDK usługi Service Fabric w wersji 3.1 lub nowszej.
* Utwórz konto usługi Azure Storage do przechowywania kopii zapasowych aplikacji.
* Zainstaluj moduł Microsoft.ServiceFabric.Powershell.Http [w wersja zapoznawcza] dla konfiguracji połączeń.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Upewnij się, że klaster jest podłączony za pomocą `Connect-SFCluster` polecenia przed dokonaniem wszelkie żądania konfiguracji, za pomocą modułu Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Włączanie usługi Kopia zapasowa i przywracanie

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Włącz `Include backup restore service` pole wyboru w obszarze `+ Show optional settings` w `Cluster Configuration` kartę.

![Włącz usługę przywracania kopii zapasowej za pomocą portalu][1]


### <a name="using-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Resource Manager platformy Azure
Najpierw należy włączyć _kopia zapasowa i przywracanie usługi_ w klastrze. Pobierz szablon dla klastra, który chcesz wdrożyć. Można użyć [przykładowe szablony](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) lub Utwórz szablon usługi Resource Manager. Włącz _kopia zapasowa i przywracanie usługi_ następujące czynności:

1. Sprawdź, czy `apiversion` ustawiono **`2018-02-01`** dla `Microsoft.ServiceFabric/clusters` zasobów, a jeśli nie, zaktualizuj go jak pokazano w poniższym fragmencie kodu:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz Włącz _kopia zapasowa i przywracanie usługi_ przez dodanie poniższego `addonFeatures` sekcji `properties` sekcji, jak pokazano w poniższym fragmencie kodu: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Skonfiguruj certyfikat X.509 w celu szyfrowania poświadczeń. Jest to ważne upewnić się, że poświadczenia podane do połączenia z magazynem są szyfrowane przed wprowadzeniem trwałych. Skonfigurować certyfikat szyfrowania, dodając następujące `BackupRestoreService` sekcji `fabricSettings` sekcji, jak pokazano w poniższym fragmencie kodu: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Gdy zostały zaktualizowane do szablonu klastra przy użyciu zmian poprzednim, stosować je i pozwolić zakończyć wdrażania/uaktualniania. Po zakończeniu _kopia zapasowa i przywracanie usługi_ zacznie działać w klastrze. Identyfikator Uri usługi jest `fabric:/System/BackupRestoreService` i usługa może znajdować się w sekcji usługi systemowe w Eksploratorze usługi Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Opcja włączania okresowych kopii zapasowych usługi Reliable Stateful i Reliable Actors
Przejdźmy przez czynności, aby włączyć okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors. Te czynności zakładają
- Klaster znajduje się instalację za pomocą zabezpieczeń X.509 z _kopia zapasowa i przywracanie usługi_.
- Usługi Reliable Stateful jest wdrażana w klastrze. Na potrzeby tego przewodnika Szybki Start jest identyfikator Uri aplikacji `fabric:/SampleApp` i identyfikator Uri usługi Reliable Stateful należących do tej aplikacji jest `fabric:/SampleApp/MyStatefulService`. Ta usługa jest wdrażany z jedną partycją i identyfikator partycji jest `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Certyfikat klienta z rolą administratora jest zainstalowany w _Moje_ (_osobistych_) przechowywania nazwę _CurrentUser_ certyfikatu lokalizacji magazynu na komputer, z którego poniżej skrypty zostanie wywołany. W tym przykładzie użyto `1b7ebe2174649c45474a4819dafae956712c31d3` jako odcisk palca certyfikatu. Aby uzyskać więcej informacji na temat certyfikatów klienta, zobacz [kontroli dostępu opartej na rolach dla klientów usługi Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

Pierwszym krokiem jest tworzenie zasad kopii zapasowych, opisujący harmonogram tworzenia kopii zapasowych, docelowy magazyn danych kopii zapasowej, nazwę zasad, maksymalna przyrostowe kopie zapasowe mają być dozwolone przed wyzwoleniem pełnej kopii zapasowej oraz zasady przechowywania dla magazynu kopii zapasowych. 

W magazynie kopii zapasowej Użyj usługi Azure Storage konta utworzonego powyżej. Kontener `backup-container` jest skonfigurowany do przechowywania kopii zapasowych. Utworzenie kontenera o tej nazwie, jeśli go jeszcze nie istnieje, podczas tworzenia kopii zapasowej przekazywania. Wypełnij `ConnectionString` z prawidłowe parametry połączenia dla konta usługi Azure Storage, zastępując `account-name` nazwą konta magazynu, i `account-key` kluczem konta magazynu.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

Wykonaj następujące polecenia cmdlet programu PowerShell do tworzenia nowych zasad tworzenia kopii zapasowej. Zastąp `account-name` nazwą konta magazynu, a `account-key` kluczem konta magazynu.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego interfejsu API REST do tworzenia nowych zasad. Zastąp `account-name` nazwą konta magazynu, a `account-key` kluczem konta magazynu.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

### <a name="enable-periodic-backup"></a>Włącz okresowe wykonywanie kopii zapasowej
Po zdefiniowaniu zasad tworzenia kopii zapasowej, aby spełnić wymagania dotyczące ochrony danych aplikacji, zasady tworzenia kopii zapasowej należy skojarzone z aplikacją. W zależności od wymagań zasad tworzenia kopii zapasowej może być skojarzony z aplikacją, usługi lub partycji.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego interfejsu API REST, aby skojarzyć zasady tworzenia kopii zapasowych o nazwie `BackupPolicy1` utworzoną w kroku przy użyciu aplikacji `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Sprawdź, czy działają okresowe kopie zapasowe

Po włączeniu kopii zapasowej na poziomie aplikacji, wszystkie partycje należących do Reliable Stateful services i Reliable Actors w ramach aplikacji rozpocznie się pobieranie kopii zapasowej okresowo zgodnie z skojarzonych zasad kopii zapasowych. 

![Zdarzenie kondycji kopii zapasowej partycji][0]

### <a name="list-backups"></a>Wykaz kopii zapasowych

Kopie zapasowe skojarzone z wszystkie partycje należących do Reliable Stateful services i Reliable Actors aplikacji mogą być wyliczane przy użyciu _GetBackups_ interfejsu API. Kopie zapasowe mogą być wyliczane dla aplikacji, usługi lub partycji.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell do wywołania interfejsu API protokołu HTTP, można wyliczyć kopie zapasowe utworzone dla wszystkich partycji wewnątrz `SampleApp` aplikacji.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="limitation-caveats"></a>Ograniczenie / zastrzeżenia
- Polecenia cmdlet programu PowerShell usługi Service Fabric są w wersji zapoznawczej.
- Klastry usługi Service Fabric nie są obsługiwane w systemie Linux.

## <a name="next-steps"></a>Kolejne kroki
- [Opis okresowe konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png

