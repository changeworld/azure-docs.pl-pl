---
title: Udział plików platformy Azure dla pul usługi Azure Batch | Dokumentacja firmy Microsoft
description: Jak zainstalować udział plików platformy Azure z węzłów obliczeniowych w puli systemu Linux lub Windows w usłudze Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 914bc11736b08dab6b334307dc188b5d153c7331
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341319"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Udział plików platformy Azure za pomocą puli usługi Batch

[Usługa Azure Files](../storage/files/storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu bloku komunikatów serwera (SMB). Ten artykuł zawiera informacje i przykłady kodów do instalowania i korzystania z udziału plików platformy Azure w węzłach obliczeniowych puli. Przykłady kodu używać platformy .NET usługi Batch i języka Python SDK, ale można wykonywać operacje podobne, korzystając z innych zestawów SDK usługi Batch i narzędzi.

Usługi Batch zapewnia macierzystą obsługę interfejsu API, która pozwala na odczytywanie i zapisywanie danych przy użyciu obiektów blob usługi Azure Storage. Jednak w niektórych przypadkach można uzyskać dostęp do udziału plików platformy Azure z węzłów obliczeniowych puli. Na przykład obciążenie starszej wersji, który zależy od udziału plików SMB ma lub zadań muszą uzyskiwać dostęp do udostępnionych danych i generuje udostępnionych danych wyjściowych. 

## <a name="considerations-for-use-with-batch"></a>Uwagi dotyczące użycia z usługą Batch

* Należy rozważyć użycie udziału plików platformy Azure, w przypadku pul systemem małą liczbę równoległych zadań podrzędnych. Przegląd [cele dotyczące wydajności i skali](../storage/files/storage-files-scale-targets.md) ustalenie, jeśli należy użyć usługi Azure Files, (która korzysta z konta usługi Azure Storage), Twoja rozmiar oczekiwany puli i liczbie plików zasobów. 

* Udziały plików platformy Azure są [efektywny kosztowo sposób](https://azure.microsoft.com/pricing/details/storage/files/) i może być konfigurowana przy użyciu danych replikacji do innego regionu są więc globalnie nadmiarowy. 

* Można zainstalować udział plików platformy Azure, jednocześnie z komputera lokalnego.

* Zobacz również ogólne [zagadnienia związane z planowaniem](../storage/files/storage-files-planning.md) udziałów plików platformy Azure.


## <a name="create-a-file-share"></a>Tworzenie udziału plików

[Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md) na koncie magazynu, który jest połączony z kontem usługi Batch lub oddzielnego konta magazynu.

## <a name="mount-a-share-on-a-windows-pool"></a>Instalowanie udziału w puli Windows

Ta sekcja zawiera instrukcje i przykłady kodu, aby zainstalować i używać plików platformy Azure, Udostępnij w puli węzłów Windows. Do dodatkowych informacji uzupełniających, zobacz [dokumentacji](../storage/files/storage-how-to-use-files-windows.md) montażu Azure udziału plików w Windows. 

W usłudze Batch należy zainstalować ten udział każdorazowo zadanie jest uruchamiane w węźle Windows. Obecnie nie jest możliwe zachować połączenie sieciowe między zadaniami w węzłach Windows.

Na przykład zawierać `net use` polecenie, aby zainstalować udział plików w ramach każdego wiersza polecenia zadania. Aby zainstalować udział plików, potrzebne są następujące poświadczenia:

* **Nazwa użytkownika**: AZURE\\\<storageaccountname\>, na przykład AZURE\\*mystorageaccountname*
* **Hasło**: < StorageAccountKeyWhichEnds w == >, na przykład *XXXXXXXXXXXXXXXXXXXXX ==*

Poniższe polecenie instaluje udziału plików *myfileshare* na koncie magazynu *mystorageaccountname* jako *S:* dysku:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Dla uproszczenia poświadczenia są przekazywane podane tutaj przykłady bezpośrednio w tekście. W praktyce zdecydowanie zaleca się zarządzanie poświadczeń przy użyciu zmiennych środowiskowych, certyfikatów i rozwiązań, takich jak usługi Azure Key Vault.

Aby uprościć operację instalowania, opcjonalnie utrwalić poświadczenia w węzłach. Następnie możesz zainstalować ten udział bez poświadczeń. Wykonaj następujące dwa kroki:

1. Uruchom `cmdkey` narzędzie wiersza polecenia za pomocą zadania uruchamiania w konfiguracji puli. Będzie się powtarzał poświadczenia w każdym węźle Windows. Wiersza polecenia zadania uruchamiania są podobne do:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Instalowanie udziału w każdym węźle w ramach każdego zadania przy użyciu `net use`. Na przykład następujący wiersz polecenia zadania instalowania udziału plików jako *S:* dysku. To może następować polecenia lub skryptu, który odwołuje się do udziału. Buforowane poświadczenia są używane w wywołaniu `net use`. W tym kroku przyjęto założenie, że używasz tej samej tożsamości użytkownika dla zadania, które są używane w zadanie podrzędne uruchamiania w puli, która nie jest odpowiednie w przypadku wszystkich scenariuszy.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Przykład w języku C#
Następujące C# przykład pokazuje, jak można utrwalić poświadczenia w ramach puli Windows za pomocą zadania uruchamiania. Nazwa usługi pliku magazynu i poświadczenia magazynu są przekazywane jako zdefiniowanych stałych. W tym miejscu zadanie podrzędne uruchamiania jest uruchamiana (inni niż administrator) auto-konta użytkownika standardowego o zakresie puli.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Po zapisaniu poświadczenia umożliwia Twojej wiersze poleceń podzadań zainstalować ten udział i odwoływać się do udziału w odczytu lub operacji zapisu. Jako przykład basic, korzysta z wiersza polecenia zadania do następującego fragmentu kodu `dir` polecenia, aby wyświetlić listę plików w udziale plików. Pamiętaj uruchomić zadanie każdego zadania, korzystając z tych samych [tożsamość użytkownika](batch-user-accounts.md) używane do uruchomienia zadania podrzędnego uruchamiania w puli. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Instalowanie udziału w puli systemu Linux

Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu [klient jądra CIFS](https://wiki.samba.org/index.php/LinuxCIFS). Poniższy przykład pokazuje sposób instalacji udziału plików w puli węzłów obliczeniowych systemu Ubuntu 16.04 LTS. Jeśli używasz różnych dystrybucji systemu Linux, ogólne kroki są podobne, ale użyj Menedżera pakietów odpowiednich dla dystrybucji. Aby uzyskać szczegóły i dodatkowe przykłady, zobacz [użycia usługi Azure Files z systemem Linux](../storage/files/storage-how-to-use-files-linux.md).

Najpierw, przy użyciu tożsamości użytkownika administratora należy zainstalować `cifs-utils` pakietu i utworzyć punkt instalacji (na przykład *wolumin/mnt/MyAzureFileShare*) w lokalnym systemie plików. Folder dla punktu instalacji można utworzyć dowolne miejsce w systemie plików, ale jest typową Konwencją do utworzenia w obszarze `/mnt` folderu. Pamiętaj, aby nie utworzyć punkt instalacji bezpośrednio w `/mnt` (w systemie Ubuntu) lub `/mnt/resource` (na inne dystrybucje).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Następnie uruchom `mount` polecenie, aby zainstalować udział plików, podając następujące poświadczenia:

* **Nazwa użytkownika**: \<storageaccountname\>, na przykład *mystorageaccountname*
* **Hasło**: < StorageAccountKeyWhichEnds w == >, na przykład *XXXXXXXXXXXXXXXXXXXXX ==*

Poniższe polecenie instaluje udziału plików *myfileshare* na koncie magazynu *mystorageaccountname* na *wolumin/mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Dla uproszczenia poświadczenia są przekazywane podane tutaj przykłady bezpośrednio w tekście. W praktyce zdecydowanie zaleca się zarządzanie poświadczeń przy użyciu zmiennych środowiskowych, certyfikatów i rozwiązań, takich jak usługi Azure Key Vault.

W ramach puli systemu Linux możesz połączyć wszystkie te czynności, zadanie podrzędne uruchamiania jednego lub uruchamiać je w skrypcie. Uruchom zadanie podrzędne uruchamiania jako użytkownika administrator w puli. Ustaw zadanie podrzędne uruchamiania oczekiwania do pomyślnego ukończenia przed uruchomieniem kolejne zadania w puli, odwołujący się do tego udziału.

### <a name="python-example"></a>Przykładem w języku Python

W poniższym przykładzie Python pokazano, jak skonfigurować pulę Ubuntu, aby zainstalować udział w zadanie podrzędne uruchamiania. Punkt instalacji, punkt końcowy udziału plików i poświadczenia magazynu są przekazywane jako zdefiniowanych stałych. Zadanie podrzędne uruchamiania jest uruchamiana automatycznie — konta administratora o zakresie puli.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Po instalowania udziału i definiując zadania, korzystają z udziału w swojej wierszach polecenia zadań podrzędnych. Na przykład następujące polecenie podstawowe używa `ls` Aby wyświetlić listę plików w udziale plików.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać inne opcje odczytywać i zapisywać dane w usłudze Batch, zobacz [omówienie funkcji usługi Batch](batch-api-basics.md) i [utrwalanie danych wyjściowych zadań i zadań podrzędnych](batch-task-output.md).

* Zobacz też [usługa Batch Shipyard](https://github.com/Azure/batch-shipyard) toolkit, który zawiera [przepisy usługa Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) do wdrażania systemów plików dla obciążeń kontenerów usługi Batch.