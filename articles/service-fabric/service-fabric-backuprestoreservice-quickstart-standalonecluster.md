---
title: Okresowe tworzenie kopii zapasowych/przywracanie w autonomicznej sieci szkieletowej usług Azure
description: Użyj funkcji okresowej kopii zapasowej i przywracania sieci szkieletowej usług, aby włączyć okresową kopię zapasową danych aplikacji.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 938cbbde9f53c52350ef64715f6c61c4aa961057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526247"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Okresowe tworzenie kopii zapasowych i przywracanie w autonomicznej sieci szkieletowej usług
> [!div class="op_single_selector"]
> * [Klastry na platformie Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Klastry autonomiczne](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Usługa Fabric to platforma systemów rozproszonych, która ułatwia tworzenie i zarządzanie niezawodnymi, rozproszonymi aplikacjami w chmurze opartymi na mikrousługach. Umożliwia uruchamianie zarówno bezstanowych, jak i stanowych mikroserw. Usługi stanowe mogą utrzymywać modyfikowalną, autorytatywną stan poza żądaniem i odpowiedzią lub pełną transakcją. Jeśli usługa stanowa ulegnie awarii przez długi czas lub utraci informacje z powodu awarii, może być konieczne przywrócenie do niektórych ostatnich kopii zapasowych jej stanu, aby kontynuować świadczenie usług po powrocie do góry.

Usługa Sieci szkieletowej replikuje stan w wielu węzłach, aby upewnić się, że usługa jest wysoce dostępna. Nawet jeśli jeden węzeł w klastrze ulegnie awarii, usługa będzie nadal dostępna. W niektórych przypadkach jednak nadal jest pożądane, aby dane usługi były wiarygodne przed szerszymi awariami.
 
Na przykład usługa może chcieć zrobić z powrotem do swoich danych w celu ochrony przed następującymi scenariuszami:
- Trwała utrata całego klastra sieci szkieletowej usług.
- Trwała utrata większości replik partycji usługi
- Błędy administracyjne, przez które stan przypadkowo zostanie usunięty lub uszkodzony. Na przykład administrator z wystarczającym uprawnieniem błędnie usuwa usługę.
- Błędy w usłudze, które powodują uszkodzenie danych. Na przykład może się to zdarzyć, gdy uaktualnienie kodu usługi rozpoczyna zapisywanie wadliwych danych do niezawodnej kolekcji. W takim przypadku kod i dane mogą być przywrócone do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodne, aby mieć w trybie offline przetwarzania danych dla analizy biznesowej, która odbywa się oddzielnie od usługi, która generuje dane.

Usługa Service Fabric udostępnia wbudowany interfejs API do wykonywania [kopii zapasowych i przywracania](service-fabric-reliable-services-backup-restore.md)w czasie. Deweloperzy aplikacji mogą używać tych interfejsów API do okresowego tworzenia kopii zapasowych stanu usługi. Ponadto jeśli administratorzy usługi chcą wyzwolić kopię zapasową spoza usługi w określonym czasie, na przykład przed uaktualnieniem aplikacji, deweloperzy muszą udostępnić kopię zapasową (i przywrócić) jako interfejs API z usługi. Utrzymanie kopii zapasowych jest dodatkowym kosztem powyżej tego. Na przykład można wykonać pięć przyrostowych kopii zapasowych co pół godziny, a następnie pełną kopię zapasową. Po wykonaniu pełnej kopii zapasowej można usunąć wcześniejsze przyrostowe kopie zapasowe. Takie podejście wymaga dodatkowego kodu prowadzącego do dodatkowych kosztów podczas tworzenia aplikacji.

Tworzenie kopii zapasowych danych aplikacji okresowo jest podstawową potrzebą zarządzania aplikacją rozproszoną i ochrony przed utratą danych lub długotrwałą utratą dostępności usług. Usługa Service Fabric zapewnia opcjonalną usługę tworzenia kopii zapasowych i przywracania, która umożliwia konfigurowanie okresowej kopii zapasowej stanowych usług niezawodnych (w tym usług aktora) bez konieczności pisania dodatkowego kodu. Ułatwia również przywracanie wcześniej wykonanych kopii zapasowych. 

Sieci szkieletowej usług zawiera zestaw interfejsów API, aby osiągnąć następujące funkcje związane z funkcją okresowej kopii zapasowej i przywracania:

- Zaplanuj okresowe tworzenie kopii zapasowych niezawodnych usług stanowych i wiarygodnych aktorów z obsługą przekazywania kopii zapasowej do (zewnętrznych) lokalizacji magazynu. Obsługiwane lokalizacje magazynowania
    - Azure Storage
    - Udział plików (lokalny)
- Wyliczaj kopie zapasowe
- Wyzwalanie kopii zapasowej doraźnej partycji
- Przywracanie partycji przy użyciu poprzedniej kopii zapasowej
- Tymczasowe zawieszanie kopii zapasowych
- Zarządzanie przechowywaniem kopii zapasowych (nadchodzące)

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster sieci szkieletowej usług z siecią szkieletową w wersji 6.4 lub wyższej. Zapoznaj się z tym [artykułem,](service-fabric-cluster-creation-for-windows-server.md) aby uzyskać kroki, aby pobrać wymagany pakiet.
* Certyfikat X.509 do szyfrowania wpisów tajnych potrzebnych do połączenia się z magazynem w celu przechowywania kopii zapasowych. Zapoznaj się z [artykułem,](service-fabric-windows-cluster-x509-security.md) aby dowiedzieć się, jak uzyskać lub utworzyć certyfikat X.509 z podpisem własnym.

* Usługa Sieci szkieletowej Niezawodna aplikacja stanowa zbudowana przy użyciu SDK sieci szkieletowej usług w wersji 3.0 lub wyższej. W przypadku aplikacji przeznaczonych dla platformy .Net Core 2.0 aplikacja powinna być zbudowana przy użyciu SDK sieci szkieletowej usług w wersji 3.1 lub wyższej.
* Zainstaluj moduł Microsoft.ServiceFabric.Powershell.http [W wersji zapoznawczej] do nawiązywanych wywołań konfiguracyjnych.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Upewnij się, że program `Connect-SFCluster` Cluster jest połączony za pomocą polecenia przed wykonaniem dowolnego żądania konfiguracji przy użyciu modułu Microsoft.ServiceFabric.Powershell.http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Włączanie usługi tworzenia kopii zapasowych i przywracania
Najpierw należy włączyć _usługę tworzenia kopii zapasowych i przywracania_ w klastrze. Pobierz szablon klastra, który chcesz wdrożyć. Można użyć [przykładowych szablonów](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Włącz _usługę tworzenia kopii zapasowych i przywracania,_ wykonując następujące czynności:

1. Sprawdź, `apiversion` czy jest `10-2017` ustawiona w pliku konfiguracji klastra, a jeśli nie, zaktualizuj go w następującym urywek:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Teraz włącz _usługę tworzenia kopii zapasowych i przywracania,_ dodając następującą `addonFeatures` sekcję w sekcji, `properties` jak pokazano w poniższym urywek: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurowanie certyfikatu X.509 do szyfrowania poświadczeń. Jest to ważne, aby upewnić się, że poświadczenia dostarczone, jeśli istnieją, aby połączyć się z magazynem są szyfrowane przed utrwaleniem. Skonfiguruj certyfikat `BackupRestoreService` szyfrowania, dodając następującą sekcję w sekcji, `fabricSettings` jak pokazano w poniższym urywek: 

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

4. Po zaktualizowaniu pliku konfiguracji klastra o poprzednie zmiany zastosuj je i pozwól na zakończenie wdrażania/uaktualniania. Po zakończeniu _usługa tworzenia kopii zapasowych i przywracania_ zostanie uruchomiona w klastrze. Uri tej usługi `fabric:/System/BackupRestoreService` jest i usługa może znajdować się w sekcji usługi systemu w Eksploratorze sieci szkieletowej usług. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Włączanie okresowej kopii zapasowej dla niezawodnej usługi stanowej i niezawodnych aktorów
Przejdźmy przez kroki, aby włączyć okresowe tworzenie kopii zapasowych dla niezawodnej usługi stanowej i niezawodnych aktorów. Kroki te zakładają, że
- Aby klaster był skonfigurowany z _usługą tworzenia kopii zapasowych i przywracania_.
- Niezawodna usługa stanowa jest wdrażana w klastrze. Na potrzeby tego przewodnika szybki start, `fabric:/SampleApp` Uri aplikacji jest i Uri dla `fabric:/SampleApp/MyStatefulService`niezawodnej usługi stanowej należących do tej aplikacji jest . Ta usługa jest wdrażana z pojedynczą partycją, a identyfikator partycji jest `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Tworzenie zasad tworzenia kopii zapasowych

Pierwszym krokiem jest utworzenie zasad tworzenia kopii zapasowych opisujących harmonogram tworzenia kopii zapasowych, docelowy magazyn danych kopii zapasowych, nazwę zasad, maksymalne przyrostowe kopie zapasowe, które mają być dozwolone przed wyzwoleniem pełnej kopii zapasowej i przechowywania zasad przechowywania kopii zapasowych. 

W przypadku przechowywania kopii zapasowych utwórz udział plików i nadaj ReadWrite dostęp do tego udziału plików dla wszystkich maszyn węzła sieci szkieletowej usługi. W tym przykładzie założono, że udział z nazwą `BackupStore` jest obecny na `StorageServer`.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego interfejsu API REST w celu utworzenia nowych zasad.

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

#### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług

1. W Eksploratorze sieci szkieletowej usług przejdź do karty Kopie zapasowe i wybierz pozycję Akcje > tworzenie zasad tworzenia kopii zapasowych.

    ![Tworzenie zasad tworzenia kopii zapasowych][6]

2. Wypełnij informacje. W przypadku klastrów autonomicznych należy wybrać opcję FileShare.

    ![Tworzenie narzędzia FileShare zasad kopii zapasowej][7]

### <a name="enable-periodic-backup"></a>Włączanie okresowego tworzenia kopii zapasowych
Po zdefiniowaniu zasad w celu spełnienia wymagań dotyczących ochrony danych aplikacji zasady tworzenia kopii zapasowych powinny być skojarzone z aplikacją. W zależności od wymagań zasady tworzenia kopii zapasowych mogą być skojarzone z aplikacją, usługą lub partycją.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell
Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego `BackupPolicy1` interfejsu API REST `SampleApp`w celu skojarzenia zasad tworzenia kopii zapasowych z nazwą utworzoną w powyższym kroku z aplikacją .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług

1. Wybierz aplikację i przejdź do akcji. Kliknij pozycję Włącz/Aktualizuj kopię zapasową aplikacji.

    ![Włącz tworzenie kopii zapasowych aplikacji][3] 

2. Na koniec wybierz żądaną zasadę i kliknij pozycję Włącz kopię zapasową.

    ![Wybierz pozycję Zasady][4]

### <a name="verify-that-periodic-backups-are-working"></a>Sprawdź, czy okresowe kopie zapasowe działają

Po włączeniu tworzenia kopii zapasowej dla aplikacji wszystkie partycje należące do niezawodnych usług stanowych i wiarygodnych podmiotów w ramach aplikacji rozpocznie się wykonywanie kopii zapasowej okresowo zgodnie z zasadami skojarzonego tworzenia kopii zapasowych.

![Zdarzenie kondycji kopii zapasowej partycji][0]

### <a name="list-backups"></a>Lista kopii zapasowych

Kopie zapasowe skojarzone ze wszystkimi partycjami należącymi do niezawodnych usług stanowych i wiarygodnych aktorów aplikacji można wyliczyć za pomocą interfejsu API _GetBackups._ W zależności od wymagań kopie zapasowe mogą być wyliczone dla aplikacji, usługi lub partycji.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program Powershell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku za pomocą programu Powershell

Wykonaj następujący skrypt programu PowerShell, aby wywołać interfejs API HTTP w celu `SampleApp` wyliczenia kopii zapasowych utworzonych dla wszystkich partycji wewnątrz aplikacji.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Przykładowe dane wyjściowe dla powyższego uruchomienia:

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

#### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług

Aby wyświetlić kopie zapasowe w Eksploratorze sieci szkieletowej usług, przejdź do partycji i wybierz kartę Kopie zapasowe.

![Wyliczaj kopie zapasowe][5]

## <a name="limitation-caveats"></a>Ograniczenia/zastrzeżenia
- Polecenia cmdlet sieciowej usługi PowerShell są w trybie podglądu.
- Brak obsługi klastrów sieci szkieletowej usług w systemie Linux.

## <a name="next-steps"></a>Następne kroki
- [Opis konfiguracji okresowej kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odwołanie do interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png