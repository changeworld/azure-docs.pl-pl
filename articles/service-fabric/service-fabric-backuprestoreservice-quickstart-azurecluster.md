---
title: Okresowe wykonywanie kopii zapasowej i przywracanie w usłudze Azure Service Fabric
description: Użyj funkcji okresowej kopii zapasowej i przywracania sieci szkieletowej usług, aby włączyć okresową kopię zapasową danych aplikacji.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: f56fcb7d1dde700d954c3b55bcf8cd7759893521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259008"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Okresowe tworzenie kopii zapasowych i przywracanie w klastrze sieci szkieletowej usług Azure
> [!div class="op_single_selector"]
> * [Klastry na platformie Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Klastry autonomiczne](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Usługa Fabric to platforma systemów rozproszonych, która ułatwia tworzenie i zarządzanie niezawodnymi, rozproszonymi aplikacjami w chmurze opartymi na mikrousługach. Umożliwia uruchamianie zarówno bezstanowych, jak i stanowych mikroserw. Usługi stanowe mogą utrzymywać modyfikowalną, autorytatywną stan poza żądaniem i odpowiedzią lub pełną transakcją. Jeśli usługa stanowa ulegnie awarii przez długi czas lub utraci informacje z powodu awarii, może być konieczne przywrócenie do niektórych ostatnich kopii zapasowych jej stanu, aby kontynuować świadczenie usług po powrocie do góry.

Usługa Sieci szkieletowej replikuje stan w wielu węzłach, aby upewnić się, że usługa jest wysoce dostępna. Nawet jeśli jeden węzeł w klastrze ulegnie awarii, usługa będzie nadal dostępna. W niektórych przypadkach jednak nadal jest pożądane, aby dane usługi były wiarygodne przed szerszymi awariami.
 
Na przykład usługa może chcieć zrobić z powrotem do swoich danych w celu ochrony przed następującymi scenariuszami:
- W przypadku trwałej utraty całego klastra sieci szkieletowej usług.
- Trwała utrata większości replik partycji usługi
- Błędy administracyjne, przez które stan przypadkowo zostanie usunięty lub uszkodzony. Na przykład administrator z wystarczającym uprawnieniem błędnie usuwa usługę.
- Błędy w usłudze, które powodują uszkodzenie danych. Na przykład może się to zdarzyć, gdy uaktualnienie kodu usługi rozpoczyna zapisywanie wadliwych danych do niezawodnej kolekcji. W takim przypadku kod i dane mogą być przywrócone do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodne, aby mieć w trybie offline przetwarzania danych dla analizy biznesowej, która odbywa się oddzielnie od usługi, która generuje dane.

Usługa Service Fabric udostępnia wbudowany interfejs API do wykonywania [kopii zapasowych i przywracania](service-fabric-reliable-services-backup-restore.md)w czasie. Deweloperzy aplikacji mogą używać tych interfejsów API do okresowego tworzenia kopii zapasowych stanu usługi. Ponadto jeśli administratorzy usługi chcą wyzwolić kopię zapasową spoza usługi w określonym czasie, na przykład przed uaktualnieniem aplikacji, deweloperzy muszą udostępnić kopię zapasową (i przywrócić) jako interfejs API z usługi. Utrzymanie kopii zapasowych jest dodatkowym kosztem powyżej tego. Na przykład można wykonać pięć przyrostowych kopii zapasowych co pół godziny, a następnie pełną kopię zapasową. Po wykonaniu pełnej kopii zapasowej można usunąć wcześniejsze przyrostowe kopie zapasowe. Takie podejście wymaga dodatkowego kodu prowadzącego do dodatkowych kosztów podczas tworzenia aplikacji.

Usługa tworzenia kopii zapasowych i przywracania w sieci szkieletowej usług umożliwia łatwe i automatyczne tworzenie kopii zapasowych informacji przechowywanych w usługach stanowych. Okresowe tworzenie kopii zapasowych danych aplikacji ma zasadnicze znaczenie dla ochrony przed utratą danych i niedostępnością usług. Usługa Service Fabric zapewnia opcjonalną usługę tworzenia kopii zapasowych i przywracania, która umożliwia konfigurowanie okresowej kopii zapasowej stanowych usług niezawodnych (w tym usług aktora) bez konieczności pisania dodatkowego kodu. Ułatwia również przywracanie wcześniej wykonanych kopii zapasowych. 


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
* Klaster sieci szkieletowej usług z siecią szkieletową w wersji 6.4 lub wyższej. Zapoznaj się z tym [artykułem,](service-fabric-cluster-creation-via-arm.md) aby dokłeda wykonać kroki tworzenia klastra sieci szkieletowej usług przy użyciu szablonu zasobów platformy Azure.
* Certyfikat X.509 do szyfrowania wpisów tajnych potrzebnych do połączenia się z magazynem w celu przechowywania kopii zapasowych. Zapoznaj się z [artykułem,](service-fabric-cluster-creation-via-arm.md) aby dowiedzieć się, jak uzyskać lub utworzyć certyfikat X.509.
* Usługa Sieci szkieletowej Niezawodna aplikacja stanowa zbudowana przy użyciu SDK sieci szkieletowej usług w wersji 3.0 lub wyższej. W przypadku aplikacji przeznaczonych dla platformy .NET Core 2.0 aplikacja powinna być skompilowana przy użyciu SDK sieci szkieletowej usług w wersji 3.1 lub wyższej.
* Utwórz konto usługi Azure Storage do przechowywania kopii zapasowych aplikacji.
* Zainstaluj moduł Microsoft.ServiceFabric.Powershell.http [W wersji zapoznawczej] do nawiązywanych wywołań konfiguracyjnych.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Upewnij się, że program `Connect-SFCluster` Cluster jest połączony za pomocą polecenia przed wykonaniem dowolnego żądania konfiguracji przy użyciu modułu Microsoft.ServiceFabric.Powershell.http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Włączanie usługi tworzenia kopii zapasowych i przywracania

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Włącz `Include backup restore service` pole `+ Show optional settings` wyboru `Cluster Configuration` na karcie.

![Włącz usługę przywracania kopii zapasowej za pomocą portalu][1]


### <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Najpierw należy włączyć _usługę tworzenia kopii zapasowych i przywracania_ w klastrze. Pobierz szablon klastra, który chcesz wdrożyć. Przykładowe [szablony](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) można użyć lub utworzyć szablon Menedżera zasobów. Włącz _usługę tworzenia kopii zapasowych i przywracania,_ wykonując następujące czynności:

1. Sprawdź, `apiversion` czy jest **`2018-02-01`** ustawiona na `Microsoft.ServiceFabric/clusters` zasób, a jeśli nie, zaktualizuj go, jak pokazano w poniższym urywek:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz włącz _usługę tworzenia kopii zapasowych i przywracania,_ dodając następującą `addonFeatures` sekcję w sekcji, `properties` jak pokazano w poniższym urywek: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurowanie certyfikatu X.509 do szyfrowania poświadczeń. Jest to ważne, aby upewnić się, że poświadczenia dostarczone do łączenia się z magazynem są szyfrowane przed utrwaleniem. Skonfiguruj certyfikat `BackupRestoreService` szyfrowania, dodając następującą sekcję w sekcji, `fabricSettings` jak pokazano w poniższym urywek: 

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

4. Po zaktualizowaniu szablonu klastra o poprzednie zmiany zastosuj je i pozwól na zakończenie wdrażania/uaktualniania. Po zakończeniu _usługa tworzenia kopii zapasowych i przywracania_ zostanie uruchomiona w klastrze. Uri tej usługi `fabric:/System/BackupRestoreService` jest i usługa może znajdować się w sekcji usługi systemu w Eksploratorze sieci szkieletowej usług. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Włączanie okresowej kopii zapasowej dla niezawodnej usługi stanowej i niezawodnych aktorów
Przejdźmy przez kroki, aby włączyć okresowe tworzenie kopii zapasowych dla niezawodnej usługi stanowej i niezawodnych aktorów. Kroki te zakładają, że
- Że klaster jest skonfigurowany przy użyciu zabezpieczeń X.509 z _usługą tworzenia kopii zapasowych i przywracania_.
- Niezawodna usługa stanowa jest wdrażana w klastrze. Na potrzeby tego przewodnika szybki start, `fabric:/SampleApp` Uri aplikacji jest i Uri dla `fabric:/SampleApp/MyStatefulService`niezawodnej usługi stanowej należących do tej aplikacji jest . Ta usługa jest wdrażana z pojedynczą partycją, a identyfikator partycji jest `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Certyfikat klienta z rolą administratora jest zainstalowany w nazwie magazynu _Mój_ _(Osobisty)_ lokalizacji magazynu certyfikatów _CurrentUser_ na komputerze, z którego będą wywoływane poniższe skrypty. W tym `1b7ebe2174649c45474a4819dafae956712c31d3` przykładzie użyto jako odcisk palca tego certyfikatu. Aby uzyskać więcej informacji na temat certyfikatów klientów, zobacz [Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Tworzenie zasad tworzenia kopii zapasowych

Pierwszym krokiem jest utworzenie zasad tworzenia kopii zapasowych opisujących harmonogram tworzenia kopii zapasowych, docelowy magazyn danych kopii zapasowych, nazwę zasad, maksymalne przyrostowe kopie zapasowe, które mają być dozwolone przed wyzwoleniem pełnej kopii zapasowej i przechowywania zasad przechowywania kopii zapasowych. 

W przypadku magazynu kopii zapasowych użyj konta usługi Azure Storage utworzonego powyżej. Kontener `backup-container` jest skonfigurowany do przechowywania kopii zapasowych. Kontener o tej nazwie jest tworzony, jeśli jeszcze nie istnieje, podczas przekazywania kopii zapasowej. Wypełnij `ConnectionString` prawidłowym ciągiem połączenia dla konta `account-name` usługi Azure Storage, `account-key` zastępując nazwą konta magazynu i kluczem konta magazynu.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program PowerShell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

Wykonaj następujące polecenia cmdlet programu PowerShell w celu utworzenia nowych zasad tworzenia kopii zapasowych. Zastąp `account-name` nazwą konta `account-key` magazynu i kluczem konta magazynu.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego interfejsu API REST w celu utworzenia nowych zasad. Zastąp `account-name` nazwą konta `account-key` magazynu i kluczem konta magazynu.

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

#### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług

1. W Eksploratorze sieci szkieletowej usług przejdź do karty Kopie zapasowe i wybierz pozycję Akcje > tworzenie zasad tworzenia kopii zapasowych.

    ![Tworzenie zasad tworzenia kopii zapasowych][6]

2. Wypełnij informacje. W przypadku klastrów platformy Azure Należy wybrać usługę AzureBlobStore.

    ![Tworzenie zasad tworzenia kopii zapasowych usługi Azure Blob Storage][7]

### <a name="enable-periodic-backup"></a>Włączanie okresowego tworzenia kopii zapasowych
Po zdefiniowaniu zasad tworzenia kopii zapasowych w celu spełnienia wymagań dotyczących ochrony danych aplikacji zasady tworzenia kopii zapasowych powinny być skojarzone z aplikacją. W zależności od wymagań zasady tworzenia kopii zapasowych mogą być skojarzone z aplikacją, usługą lub partycją.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program PowerShell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego `BackupPolicy1` interfejsu API REST `SampleApp`w celu skojarzenia zasad tworzenia kopii zapasowych z nazwą utworzoną w powyższym kroku z aplikacją .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług

1. Wybierz aplikację i przejdź do akcji. Kliknij pozycję Włącz/Aktualizuj kopię zapasową aplikacji.

    ![Włącz tworzenie kopii zapasowych aplikacji][3]

2. Na koniec wybierz żądaną zasadę i kliknij pozycję Włącz kopię zapasową.

    ![Wybierz pozycję Zasady][4]


### <a name="verify-that-periodic-backups-are-working"></a>Sprawdź, czy okresowe kopie zapasowe działają

Po włączeniu kopii zapasowej na poziomie aplikacji wszystkie partycje należące do niezawodnych usług stanowych i wiarygodnych podmiotów w ramach aplikacji rozpocznie się wykonywanie kopii zapasowej okresowo zgodnie z zasadami skojarzonego tworzenia kopii zapasowych. 

![Zdarzenie kondycji kopii zapasowej partycji][0]

### <a name="list-backups"></a>Lista kopii zapasowych

Kopie zapasowe skojarzone ze wszystkimi partycjami należącymi do niezawodnych usług stanowych i wiarygodnych aktorów aplikacji można wyliczyć za pomocą interfejsu API _GetBackups._ Kopie zapasowe mogą być wyliczone dla aplikacji, usługi lub partycji.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Program PowerShell przy użyciu modułu Microsoft.ServiceFabric.Powershell.http

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Połączenie odpoczynku przy użyciu programu PowerShell

Wykonaj następujący skrypt programu PowerShell, aby wywołać interfejs API HTTP w celu `SampleApp` wyliczenia kopii zapasowych utworzonych dla wszystkich partycji wewnątrz aplikacji.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Korzystanie z Eksploratora sieci szkieletowej usług

Aby wyświetlić kopie zapasowe w Eksploratorze sieci szkieletowej usług, przejdź do partycji i wybierz kartę Kopie zapasowe.

![Wyliczaj kopie zapasowe][5]

## <a name="limitation-caveats"></a>Ograniczenia/zastrzeżenia
- Polecenia cmdlet sieciowej usługi PowerShell są w trybie podglądu.
- Brak obsługi klastrów sieci szkieletowej usług w systemie Linux.

## <a name="next-steps"></a>Następne kroki
- [Opis konfiguracji okresowej kopii zapasowej](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odwołanie do interfejsu API REST przywracania kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png