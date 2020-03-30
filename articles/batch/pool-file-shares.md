---
title: Udział plików platformy Azure dla puli wsadowych platformy Azure | Dokumenty firmy Microsoft
description: Jak zainstalować udział usługi Azure Files z węzłów obliczeniowych w puli systemu Linux lub Windows w usłudze Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: 156dad25af5abd1b4d5db32569faf09a23fadfb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022515"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Używanie udziału plików platformy Azure z pulą usługi Batch

[Usługa Azure Files](../storage/files/storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu Bloku komunikatów serwera (SMB). Ten artykuł zawiera informacje i przykłady kodu do instalowania i używania udziału plików platformy Azure w węzłach obliczeniowych puli. Przykłady kodu używają zestawów SDK Batch .NET i Python, ale można wykonywać podobne operacje przy użyciu innych zestawów SDK i narzędzi usługi Batch.

Usługa Batch zapewnia natywną obsługę interfejsu API do odczytu i zapisu danych przy użyciu obiektów BLOB usługi Azure Storage. Jednak w niektórych przypadkach można uzyskać dostęp do udziału plików platformy Azure z węzłów obliczeniowych puli. Na przykład masz starsze obciążenie, które zależy od udziału plików SMB lub zadania muszą uzyskać dostęp do udostępnionych danych lub utworzyć udostępnione dane wyjściowe. 

## <a name="considerations-for-use-with-batch"></a>Zagadnienia dotyczące użycia z partią

* Należy rozważyć użycie udziału plików platformy Azure, gdy masz pule, które uruchamiają stosunkowo małą liczbę zadań równoległych. Przejrzyj [cele wydajności i skalowania,](../storage/files/storage-files-scale-targets.md) aby ustalić, czy usługa Azure Files (która używa konta usługi Azure Storage) powinna być używana, biorąc pod uwagę oczekiwany rozmiar puli i liczbę plików zasobów. 

* Udziały plików platformy Azure są [ekonomiczne](https://azure.microsoft.com/pricing/details/storage/files/) i można skonfigurować za pomocą replikacji danych do innego regionu, więc są globalnie nadmiarowe. 

* Udział plików platformy Azure można zainstalować jednocześnie z komputera lokalnego.

* Zobacz też ogólne [zagadnienia dotyczące planowania udziałów](../storage/files/storage-files-planning.md) plików platformy Azure.


## <a name="create-a-file-share"></a>Tworzenie udziału plików

[Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md) na koncie magazynu połączonym z kontem usługi Batch lub na osobnym koncie magazynu.

## <a name="mount-a-share-on-a-windows-pool"></a>Instalowanie udziału w puli systemu Windows

Ta sekcja zawiera kroki i przykłady kodu do instalowania i używania udziału plików platformy Azure w puli węzłów systemu Windows. Aby uzyskać dodatkowe tło, zobacz [dokumentację](../storage/files/storage-how-to-use-files-windows.md) dotyczącą instalowania udziału plików platformy Azure w systemie Windows. 

W usłudze Batch należy zainstalować udział za każdym razem, gdy zadanie jest uruchamiane w węźle systemu Windows. Obecnie nie jest możliwe utrwalenie połączenia sieciowego między zadaniami w węzłach systemu Windows.

Na przykład dołącz `net use` polecenie do zainstalowania udziału plików jako część każdego wiersza polecenia zadania. Aby zainstalować udział plików, potrzebne są następujące poświadczenia:

* **Nazwa użytkownika**\\\<: AZURE storageaccountname\>\\, na przykład AZURE*mystorageaccountname*
* **Hasło** \<: StorageAccountKeyWhichEnds in==>, na przykład *XXXXXXXXXXXXXXXXXXXXX==*

Następujące polecenie montuje udział pliku *myfileshare* na koncie magazynu *mystorageaccountname* jako *dysk S::*

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Dla uproszczenia przykłady tutaj przekazać poświadczenia bezpośrednio w tekście. W praktyce zdecydowanie zaleca się zarządzanie poświadczeniami przy użyciu zmiennych środowiskowych, certyfikatów lub rozwiązania, takiego jak Usługa Azure Key Vault.

Aby uprościć operację instalacji, opcjonalnie utrwalić poświadczenia w węzłach. Następnie można zainstalować udział bez poświadczeń. Wykonaj następujące dwa kroki:

1. Uruchom `cmdkey` narzędzie wiersza polecenia przy użyciu zadania startowego w konfiguracji puli. To utrzymuje poświadczenia w każdym węźle systemu Windows. Wiersz polecenia zadania startowego jest podobny do:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Zamontuj udział w każdym węźle jako część każdego zadania za pomocą programu `net use`. Na przykład następujący wiersz polecenia zadania instaluje udział plików jako dysk *S:.* Po tym następuje polecenie lub skrypt, który odwołuje się do udziału. Buforowane poświadczenia są używane w `net use`wywołaniu do . Ten krok zakłada, że używasz tej samej tożsamości użytkownika dla zadań, które zostały użyte w zadaniu uruchamiania w puli, co nie jest odpowiednie dla wszystkich scenariuszy.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Przykład języka C#
W poniższym przykładzie języka C# pokazano, jak utrwalić poświadczenia w puli systemu Windows przy użyciu zadania początkowego. Nazwa usługi pliku magazynu i poświadczenia magazynu są przekazywane jako stałe zdefiniowane. W tym miejscu zadanie startowe jest uruchamiane w ramach standardowego (nieadministratorowego) konta użytkownika automatycznego z zakresem puli.

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

Po zapisaniu poświadczeń użyj wierszy polecenia zadania, aby zainstalować udział i odwołać się do udziału w operacjach odczytu lub zapisu. Na podstawowym przykładzie wiersz polecenia zadania we wdziale kodu `dir` poniżej używa polecenia do listy plików w udziale plików. Upewnij się, że każde zadanie zadania przy użyciu tej samej [tożsamości użytkownika,](batch-user-accounts.md) które zostały użyte do uruchomienia zadania uruchamiania w puli. 

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

Udziały plików platformy Azure mogą być montowane w dystrybucjach systemu Linux przy użyciu [klienta jądra CIFS](https://wiki.samba.org/index.php/LinuxCIFS). W poniższym przykładzie pokazano, jak zainstalować udział plików w puli węzłów obliczeniowych Ubuntu 16.04 LTS. Jeśli używasz innej dystrybucji systemu Linux, ogólne kroki są podobne, ale użyj menedżera pakietów odpowiedniego dla dystrybucji. Aby uzyskać szczegółowe informacje i dodatkowe przykłady, zobacz [Korzystanie z usługi Azure Files z systemem Linux](../storage/files/storage-how-to-use-files-linux.md).

Najpierw w obszarze tożsamości użytkownika `cifs-utils` administratora zainstaluj pakiet i utwórz punkt instalacji (na przykład */mnt/MyAzureFileShare)* w lokalnym systemie plików. Folder dla punktu instalacji można utworzyć w dowolnym miejscu w systemie plików, `/mnt` ale jest to wspólna konwencja, aby utworzyć go w folderze. Pamiętaj, aby nie tworzyć punktu `/mnt` instalacji bezpośrednio w `/mnt/resource` (na Ubuntu) lub (w innych dystrybucjach).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Następnie uruchom `mount` polecenie, aby zainstalować udział plików, podając następujące poświadczenia:

* **Nazwa**użytkownika \<: storageaccountname\>, na przykład *mystorageaccountname*
* **Hasło** \<: StorageAccountKeyWhichEnds in==>, na przykład *XXXXXXXXXXXXXXXXXXXXX==*

Następujące polecenie montuje udział pliku *myfileshare* w koncie magazynu *mystorageaccountname* w */mnt/MyAzureFileShare:* 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Dla uproszczenia przykłady tutaj przekazać poświadczenia bezpośrednio w tekście. W praktyce zdecydowanie zaleca się zarządzanie poświadczeniami przy użyciu zmiennych środowiskowych, certyfikatów lub rozwiązania, takiego jak Usługa Azure Key Vault.

W puli systemu Linux można połączyć wszystkie te kroki w jednym zadaniu startowym lub uruchomić je w skrypcie. Uruchom zadanie startowe jako użytkownik administratora w puli. Ustaw zadanie początkowe, aby poczekać, aby zakończyć pomyślnie przed uruchomieniem dalszych zadań w puli, które odwołują się do udziału.

### <a name="python-example"></a>Przykład Pythona

W poniższym przykładzie języka Python pokazano, jak skonfigurować pulę Ubuntu, aby zainstalować udział w zadaniu początkowym. Punkt instalacji, punkt końcowy udziału plików i poświadczenia magazynu są przekazywane jako zdefiniowane stałe. Zadanie uruchamiania jest uruchamiane przy działach konta administratora z zakresem puli.

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

Po zamontowaniu udziału i zdefiniowaniu zadania użyj udziału w wierszach polecenia zadania. Na przykład następujące polecenie podstawowe `ls` używa do listy plików w udziale plików.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać inne opcje odczytu i zapisu danych w urcie, zobacz [omówienie funkcji usługi Batch](batch-api-basics.md) i [Utrwalić zadanie i dane wyjściowe zadania](batch-task-output.md).

* Zobacz też zestaw narzędzi [Batch Shipyard,](https://github.com/Azure/batch-shipyard) który zawiera [przepisy stoczniowe](https://github.com/Azure/batch-shipyard/tree/master/recipes) do wdrażania systemów plików dla obciążeń kontenerów wsadowych.
