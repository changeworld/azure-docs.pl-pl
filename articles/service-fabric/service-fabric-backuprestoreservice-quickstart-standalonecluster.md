---
title: Okresowe kopii zapasowej i przywracania w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Używać usługi Service Fabric okresowych kopii zapasowych i przywracania funkcji umożliwiających okresowe tworzenia kopii zapasowych danych aplikacji.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: f992aed6eba775052483b1657d04dead18b2b2ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059177"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Okresowe kopii zapasowej i przywracania w usłudze Azure Service Fabric
> [!div class="op_single_selector"]
> * [Klastry na platformie Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Klastry autonomiczne](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric to platforma systemów rozproszonych, która ułatwia tworzenie i zarządzanie nimi niezawodne, rozproszony mikrousług na podstawie aplikacji w chmurze. Umożliwia to uruchamianie mikrousługi stanowe i bezstanowe. Usługi stanowe może zachować modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzi lub ukończoną transakcję. Jeśli usługa stanowa przestanie działać przez dłuższy czas lub traci informacji z powodu awarii, może być konieczne można przywrócić do niektórych ostatniej kopii zapasowej stanu, aby móc kontynuować świadczenie usług, po jego wróci do sprawności.

Usługa Service Fabric replikuje stanu w wielu węzłach, aby upewnij się, że usługa o wysokiej dostępności. Nawet w przypadku awarii jednego węzła w klastrze, usługa jest nadal dostępna. W niektórych przypadkach jednak nadal pożądane jest dane usługi wiarygodne szersze awariami.
 
Na przykład usługa warto kopię zapasową danych w celu zapewnienia ochrony przed w następujących scenariuszach:
- Trwałą utratę cały klaster usługi Service Fabric.
- Trwałą utratę większości replik partycji usługi
- Błędy administracyjne, według której stan przypadkowo pobiera usunięty lub uszkodzony. Administrator z uprawnieniami wystarczające Usuwa błędnie usługi.
- Błędy w usłudze, które powodują uszkodzenie danych. Na przykład to może się zdarzyć, gdy uaktualnianie kodu usługi rozpoczyna się zapisywanie uszkodzonych danych w niezawodnej kolekcji. W takim przypadku zarówno kod, jak i danych może być konieczne można przywrócić do poprzedniego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodną mieć przetwarzanie danych dla analizę biznesową, która miejsce osobno od usługi, która generuje dane w trybie offline.

Usługa Service Fabric udostępnia wbudowane interfejsu API do punktu w czasie [kopia zapasowa i przywracanie](service-fabric-reliable-services-backup-restore.md). Deweloperzy aplikacji może używać tych interfejsów API, aby utworzyć kopię zapasową stanu usługi okresowo. Ponadto aby administratorzy usługi wyzwalanie tworzenia kopii zapasowej z poza usługi w określonym czasie, takie jak przed uaktualnieniem aplikacji, deweloperzy muszą ujawnić kopii zapasowej (i przywracanie) jako interfejsu API z usługi. Obsługa kopii zapasowych jest dodatkowy koszt powyżej tego. Na przykład można wykonać pięciu przyrostowych kopii zapasowych co pół godziny następuje pełnej kopii zapasowej. Po pełnej kopii zapasowej można usunąć poprzednich przyrostowych kopii zapasowych. Takie podejście wymaga dodatkowego kodu, co prowadzi do dodatkowych kosztów, podczas tworzenia aplikacji.

Tworzenie kopii zapasowych danych aplikacji w regularnych odstępach czasu jest podstawowe potrzebę zarządzania aplikację rozproszoną i ochrona przed utratą danych lub długotrwały utratą dostępności usługi. Usługa Service Fabric udostępnia opcjonalne tworzenia kopii zapasowych i przywracania usługi, która pozwala na konfigurowanie okresowych kopii zapasowej niezawodne usługi stanowe (w tym usług aktora) bez konieczności pisania dodatkowego kodu. Również ułatwia tworzenie, przywracanie, poprzednio wykonane kopie zapasowe. 

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
* Klaster usługi Service Fabric za pomocą Service Fabric w wersji 6.4 lub nowszej. Zapoznaj się z tym [artykułu](service-fabric-cluster-creation-for-windows-server.md) kroki pobrać wymagany pakiet.
* Certyfikat X.509 do szyfrowania wymagane do połączenia z magazynem kluczy tajnych do przechowywania kopii zapasowych. Zapoznaj się [artykułu](service-fabric-windows-cluster-x509-security.md) wiedzieć, jak można uzyskać lub utworzyć certyfikat z podpisem własnym X.509.

* Aplikacja usługi Service Fabric Reliable Stateful utworzone przy użyciu zestawu SDK usługi Service Fabric w wersji 3.0 lub nowszej. Dla aplikacji przeznaczonych na.Net Core 2.0, aplikacji powinny zostać skompilowane przy użyciu zestawu SDK usługi Service Fabric w wersji 3.1 lub nowszej.
* Zainstaluj moduł Microsoft.ServiceFabric.Powershell.Http [w wersja zapoznawcza] dla konfiguracji połączeń.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Upewnij się, że klaster jest podłączony za pomocą `Connect-SFCluster` polecenia przed dokonaniem wszelkie żądania konfiguracji, za pomocą modułu Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Włączanie usługi Kopia zapasowa i przywracanie
Najpierw należy włączyć _kopia zapasowa i przywracanie usługi_ w klastrze. Pobierz szablon dla klastra, który chcesz wdrożyć. Możesz użyć [przykładowe szablony](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Włącz _kopia zapasowa i przywracanie usługi_ następujące czynności:

1. Sprawdź, czy `apiversion` ustawiono `10-2017` w konfiguracji klastra plików, a jeśli nie, zaktualizuj go jak pokazano w poniższym fragmencie kodu:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Teraz Włącz _kopia zapasowa i przywracanie usługi_ przez dodanie poniższego `addonFeatures` sekcji `properties` sekcji, jak pokazano w poniższym fragmencie kodu: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Skonfiguruj certyfikat X.509 w celu szyfrowania poświadczeń. Jest to ważne, aby upewnić się, że poświadczenia podane, jeśli istnieją, aby łączenie się z magazynem są szyfrowane przed wprowadzeniem trwałych. Skonfigurować certyfikat szyfrowania, dodając następujące `BackupRestoreService` sekcji `fabricSettings` sekcji, jak pokazano w poniższym fragmencie kodu: 

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

4. Gdy zostały zaktualizowane do pliku konfiguracji klastra przy użyciu zmian poprzednim, stosować je i pozwolić zakończyć wdrażania/uaktualniania. Po zakończeniu _kopia zapasowa i przywracanie usługi_ zacznie działać w klastrze. Identyfikator Uri usługi jest `fabric:/System/BackupRestoreService` i usługa może znajdować się w sekcji usługi systemowe w Eksploratorze usługi Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Opcja włączania okresowych kopii zapasowych usługi Reliable Stateful i Reliable Actors
Przejdźmy przez czynności, aby włączyć okresowe kopie zapasowe usługi Reliable Stateful i Reliable Actors. Te czynności zakładają
- Czy klaster został skonfigurowany za pomocą _kopia zapasowa i przywracanie usługi_.
- Usługi Reliable Stateful jest wdrażana w klastrze. Na potrzeby tego przewodnika Szybki Start jest identyfikator Uri aplikacji `fabric:/SampleApp` i identyfikator Uri usługi Reliable Stateful należących do tej aplikacji jest `fabric:/SampleApp/MyStatefulService`. Ta usługa jest wdrażany z jedną partycją i identyfikator partycji jest `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

Pierwszym krokiem jest tworzenie zasad kopii zapasowych, opisujący harmonogram tworzenia kopii zapasowych, docelowy magazyn danych kopii zapasowej, nazwę zasad, maksymalna przyrostowe kopie zapasowe mają być dozwolone przed wyzwoleniem pełnej kopii zapasowej oraz zasady przechowywania dla magazynu kopii zapasowych. 

W magazynie kopii zapasowej tworzenia udziału plików i zapewniają dostęp do odczytu i zapisu do tego udziału plików dla wszystkich maszyn węzła sieci szkieletowej usługi. W tym przykładzie przyjęto założenie, udział o nazwie `BackupStore` znajduje się na `StorageServer`.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu Powershell

Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego interfejsu API REST do tworzenia nowych zasad.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
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
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Włącz okresowe wykonywanie kopii zapasowej
Po zdefiniowaniu zasad, aby spełnić wymagania dotyczące ochrony danych aplikacji, zasady tworzenia kopii zapasowej należy skojarzone z aplikacją. W zależności od wymagań zasad tworzenia kopii zapasowej może być skojarzony z aplikacją, usługi lub partycji.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu Powershell
Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego interfejsu API REST, aby skojarzyć zasady tworzenia kopii zapasowych o nazwie `BackupPolicy1` utworzoną w kroku przy użyciu aplikacji `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Sprawdź, czy działają okresowe kopie zapasowe

Po włączeniu kopii zapasowej dla aplikacji, wszystkie partycje należących do Reliable Stateful services i Reliable Actors w ramach aplikacji rozpocznie się pobieranie kopii zapasowej okresowo zgodnie z skojarzonych zasad kopii zapasowych.

![Zdarzenie kondycji kopii zapasowej partycji][0]

### <a name="list-backups"></a>Wykaz kopii zapasowych

Kopie zapasowe skojarzone z wszystkie partycje należących do Reliable Stateful services i Reliable Actors aplikacji mogą być wyliczane przy użyciu _GetBackups_ interfejsu API. W zależności od wymagań kopie zapasowe mogą być wyliczane dla aplikacji, usług lub partycji.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Za pomocą Microsoft.ServiceFabric.Powershell.Http modułu programu PowerShell

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Wywołania REST przy użyciu programu Powershell

Wykonaj następujący skrypt programu PowerShell do wywołania interfejsu API protokołu HTTP, można wyliczyć kopie zapasowe utworzone dla wszystkich partycji wewnątrz `SampleApp` aplikacji.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Przykładowe dane wyjściowe dla powyższych Uruchom:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="limitation-caveats"></a>Ograniczenie / zastrzeżenia
- Polecenia cmdlet programu PowerShell usługi Service Fabric są w wersji zapoznawczej.
- Klastry usługi Service Fabric nie są obsługiwane w systemie Linux.

## <a name="next-steps"></a>Kolejne kroki
- [Opis okresowe konfiguracji kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Dokumentacja interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

