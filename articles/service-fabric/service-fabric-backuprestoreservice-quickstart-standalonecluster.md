---
title: Okresowe kopii zapasowej i przywracania w sieci szkieletowej usług Azure (wersja zapoznawcza) | Dokumenty Microsoft
description: Użyj usługi sieć szkieletowa okresowe kopii zapasowej i przywracania funkcji ochrony przed utratą danych aplikacji.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: b8d81e713bce8cdbb2c8000c740b16553620fe7f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Okresowe kopii zapasowej i przywracania w sieci szkieletowej usług Azure (wersja zapoznawcza)
> [!div class="op_single_selector"]
> * [Klastry w systemie Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Klastry autonomicznego](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Sieć szkieletowa usług to platforma systemów rozproszonych, który ułatwia tworzenie aplikacji i zarządzanie nimi mikrousług niezawodnych, rozproszonych, na podstawie chmury. Umożliwia uruchamianie zarówno bezstanowe i stanowe micro usług. Usługi stanowej można zachować stanu modyfikowalna, autorytatywnych poza żądania i odpowiedzi lub ukończyć transakcji. Jeśli usługi stanowej przestanie działać przez dłuższy czas lub utraci informacji z powodu awarii, może być konieczne można przywrócić do niektórych ostatniej kopii zapasowej stanu, aby udostępniać usługi po jego wróci do sprawności.

Sieć szkieletowa usług replikuje stanu na wielu węzłach, aby upewnić się, że usługa jest wysokiej dostępności. Nawet w przypadku awarii jednego węzła w klastrze, usługa jest nadal dostępny. W niektórych przypadkach, jednak nadal pożądane jest wiarygodne przed awariami szerszych danych usługi.
 
Na przykład usługa może być konieczne jego dane kopii zapasowej w celu zapewnienia ochrony przed w następujących scenariuszach:
- W przypadku trwałą utratę całego klastra sieci szkieletowej usług.
- Trwałą utratę większości replik partycji usługi
- Błędy administracyjne zgodnie z którymi stan przypadkowo pobiera usunięte lub uszkodzony. Administrator z uprawnieniami wystarczające Usuwa błędnie usługi.
- Usterki w usłudze, które spowodować uszkodzenie danych. Na przykład może to być spowodowane uaktualnienie kodu usługi uruchamia zapisywania danych błędny niezawodnej kolekcji. W takim przypadku zarówno kod i dane mogą mieć przywrócenie do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodną mieć w trybie offline przetwarzania danych do analizy biznesowej, wykonywanej oddzielnie z usługą, która generuje dane.

Usługa Service Fabric realizuje wbudowanych API do punktu w czasie [i przywracania kopii zapasowych](service-fabric-reliable-services-backup-restore.md). Deweloperzy aplikacji mogą okresowo wykonaj kopię zapasową stanu usługi, korzystając z poniższych interfejsów API. Ponadto jeśli chcesz, aby administratorzy usługi do wyzwolenia kopii zapasowej z poza usługę w określonym czasie, takie jak przed rozpoczęciem uaktualniania aplikacji, deweloperzy muszą ujawnić kopii zapasowej (i przywrócić) jako interfejs API z usługi. Obsługa kopii zapasowych jest dodatkowych kosztów powyżej tego. Na przykład można korzystać z 5 przyrostowych kopii zapasowych co pół godziny, następuje pełnej kopii zapasowej. Po utworzeniu pełnej kopii zapasowej można usunąć poprzednich przyrostowe kopie zapasowe. Ta metoda wymaga dodatkowego kodu, co może prowadzić do dodatkowych kosztów podczas tworzenia aplikacji.

Tworzenie kopii zapasowych danych aplikacji w regularnych odstępach czasu jest podstawowe potrzebę zarządzania aplikacji rozproszonej oraz ochrona przed utratą danych lub długimi utratą dostępności usługi. Usługa Service Fabric realizuje opcjonalne tworzenia kopii zapasowej i przywracania usługi, która pozwala na skonfigurowanie okresowe kopii zapasowej stanowe niezawodnych usług (takich jak usługi aktora) bez konieczności pisania kodu dodatkowe. Ułatwia także przywrócenie poprzednio wykonane kopie zapasowe. 

> [!NOTE]
> Okresowe funkcji kopii zapasowej i przywracania jest obecnie w **Podgląd** i nie są obsługiwane w przypadku obciążeń produkcyjnych. 
>

Sieć szkieletowa usług zawiera zestaw interfejsów API do osiągnięcia następujące funkcje związane z okresowe tworzenie kopii zapasowej i przywracania funkcji:

- Planowanie okresowe kopii zapasowej niezawodnej Stateful services i Reliable Actors z obsługą przekazać lokalizacje przechowywania kopii zapasowej do (zewnętrzne). Lokalizacje przechowywania
    - Azure Storage
    - Udziału plików (lokalnego)
- Wyliczanie kopii zapasowych
- Wyzwól kopię zapasową ad hoc partycji
- Przywróć partycji korzystającej z poprzedniej kopii zapasowej
- Tymczasowo wstrzymać kopii zapasowych
- Zarządzanie przechowywania kopii zapasowych (nadchodzących)

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster sieci szkieletowej usług z sieci szkieletowej wersję 6.2 i nowszych. Klastra powinna być instalacji w systemie Windows Server. Zobacz [artykułu](service-fabric-cluster-creation-for-windows-server.md) dla kroki, aby pobrać wymaganego pakietu.
* Certyfikat X.509 do szyfrowania kluczy tajnych wymagane do nawiązania magazynu do przechowywania kopii zapasowych. Zobacz [artykułu](service-fabric-windows-cluster-x509-security.md) wiedzieć, jak nabycia lub utwórz samopodpisany certyfikat X.509.
* Usługi sieć szkieletowa niezawodnej Stateful aplikacji utworzony przy użyciu zestawu SDK usług sieci szkieletowej w wersji 3.0 lub nowszej. Dla aplikacji przeznaczonych dla platformy .net Core 2.0, powinny zostać skompilowane aplikacji przy użyciu zestawu SDK usług sieci szkieletowej w wersji 3.1 lub nowszej.

## <a name="enabling-backup-and-restore-service"></a>Włączanie usługi Kopia zapasowa i przywracanie
Najpierw trzeba włączyć _kopii zapasowej i przywracania usługi_ w klastrze. Pobierz szablon dla klastra, który chcesz wdrożyć. Można użyć [przykładowy szablon](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Włącz _kopii zapasowej i przywracania usługi_ następujące czynności:

1. Sprawdź, czy `apiversion` ma ustawioną wartość `10-2017` w konfiguracji klastra pliku i jeśli nie, zaktualizować go jak pokazano w poniższy fragment kodu:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Teraz Włącz _kopii zapasowej i przywracania usługi_ przez dodanie poniższego `addonFeatures` w obszarze `properties` sekcji, jak pokazano w poniższy fragment kodu: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurowanie certyfikatu X.509 do szyfrowania poświadczeń. Jest to ważne w celu zapewnienia, że poświadczenia podane, jeśli istnieje, do połączenia z magazynem są szyfrowane przed wprowadzeniem trwałych. Konfigurowanie certyfikatu szyfrowania przez dodanie poniższego `BackupRestoreService` w obszarze `fabricSettings` sekcji, jak pokazano w poniższy fragment kodu: 

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

4. Po został zaktualizowany plik konfiguracji klastra z poprzednim zmian, ich zastosowania i umożliwić ukończenie wdrażania/uaktualniania. Po zakończeniu _kopii zapasowej i przywracania usługi_ uruchamiania w klastrze. Identyfikator Uri tej usługi jest `fabric:/System/BackupRestoreService` i usługa może znajdować się w sekcji usługi systemu w programie Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Włączanie okresowe tworzenie kopii zapasowej dla usługi Stateful niezawodnych i Reliable Actors
Przejdźmy kroki, aby włączyć okresowe tworzenie kopii zapasowej dla usługi Stateful niezawodnych i Reliable Actors. Tych krokach przyjęto założenie
- Czy klastra jest skonfigurowana z _kopii zapasowej i przywracania usługi_.
- Usługa niezawodnej Stateful jest wdrażane w klastrze. Na potrzeby tego przewodnika Szybki Start jest identyfikator Uri aplikacji `fabric:/SampleApp` i identyfikator Uri usługi niezawodnego Stateful należących do tej aplikacji jest `fabric:/SampleApp/MyStatefulService`. Ta usługa jest wdrażana z jedną partycją i Identyfikatora partycji jest `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Tworzenie zasad tworzenia kopii zapasowej

Pierwszym krokiem jest tworzenie zasad tworzenia kopii zapasowej opisujące harmonogram tworzenia kopii zapasowych, Magazyn docelowy kopii zapasowej danych, nazwę zasady i maksymalna przyrostowe kopie zapasowe mogą być, aby mogło nastąpić wyzwolenie pełnej kopii zapasowej. 

W magazynie kopii zapasowej Utwórz udział plików i zapewniają dostęp do odczytu i zapisu do udziału plików dla wszystkich maszyn węzeł sieci szkieletowej usług. W tym przykładzie założono udział o nazwie `BackupStore` znajduje się na `StorageServer`.

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

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Włącz okresowe kopii zapasowej
Po zdefiniowaniu zasad w celu spełnienia wymagań dotyczących ochrony danych aplikacji, zasad tworzenia kopii zapasowej powinna być skojarzona z aplikacją. W zależności od wymagań zasad tworzenia kopii zapasowej może być skojarzony z aplikacji, usługi lub partycji.

Wykonaj następujący skrypt programu PowerShell do wywoływania wymaganego interfejsu API REST, aby skojarzyć zasady tworzenia kopii zapasowej o nazwie `BackupPolicy1` utworzone w powyżej kroku z aplikacją `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Sprawdź, czy działają okresowe kopie zapasowe

Po włączeniu kopii zapasowej dla aplikacji, wszystkie partycje należących do niezawodnej Stateful services i Reliable Actors w aplikacji rozpocznie uzyskiwanie kopii zapasowej okresowo zgodnie z zasadami tworzenia kopii zapasowej skojarzone. 

![Zdarzenie kondycji BackedUp partycji][0]

### <a name="list-backups"></a>Listy kopii zapasowych

Kopie zapasowe skojarzone z wszystkie partycje należących do niezawodnej Stateful services i Reliable Actors aplikacji mogą być wyliczane przy użyciu _GetBackups_ interfejsu API. W zależności od wymagań kopie zapasowe mogą być wyliczane dla aplikacji, usługi lub partycji.

Wykonaj następujący skrypt programu PowerShell do wywołania interfejsu API HTTP wyliczyć kopie zapasowe utworzone dla wszystkich partycji wewnątrz `SampleApp` aplikacji.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

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

## <a name="preview-limitation-caveats"></a>Wyświetl podgląd ograniczenie / ograniczenia
- Nie sieci szkieletowej usług wbudowany poleceń cmdlet programu PowerShell.
- Brak obsługi interfejsu wiersza polecenia usługi sieci szkieletowej.
- Brak obsługi automatycznego przeczyszczania kopii zapasowej. Wymaga ręcznego oczyszczania kopii zapasowych.
- Brak obsługi sieci szkieletowej usług klastrów w systemie Linux.

## <a name="next-steps"></a>Następne kroki
- [Dokumentacja interfejsu API REST z kopii zapasowej](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

