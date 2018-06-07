---
title: Udział plików na platformę Azure dla pul partii zadań Azure | Dokumentacja firmy Microsoft
description: Jak zainstalować udział plików Azure z węzłów obliczeniowych w puli partii zadań Azure Linux lub Windows.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 88d7c0d033d7b517a396df27468de8be7ae20be9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811739"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Użyj udziału plików na platformę Azure przy użyciu puli partii

[Usługa pliki Azure](../storage/files/storage-files-introduction.md) oferuje pełni zarządzanych udziałów plików w chmurze, które są dostępne za pośrednictwem protokołu bloku komunikatów serwera (SMB). Ten artykuł zawiera informacje i kod przykłady dotyczące instalowania i używania udziału plików na platformę Azure w węzłach obliczeń puli. Przykłady kodu używać partiami platformy .NET i zestawy SDK Python, ale można wykonywać operacje podobne przy użyciu innych partii zestawy SDK i narzędzia.

Wsadowe zapewnia macierzystą obsługę interfejsu API przy użyciu obiektów blob magazynu Azure do odczytywania i zapisywania danych. Jednak w niektórych przypadkach można uzyskać dostępu do udziału plików na platformę Azure z puli węzłów obliczeniowych. Na przykład masz starszych obciążeń, do których jest zależna od udziału plików SMB lub zadań muszą uzyskać dostęp do udostępnionych danych lub generowania danych wyjściowych udostępnionego. 

## <a name="considerations-for-use-with-batch"></a>Zagadnienia dotyczące użycia z partii

* Należy rozważyć użycie udziału plików na platformę Azure, jeśli masz pule systemem małą liczbę równoległych zadań. Przegląd [cele wydajności i skalowania](../storage/files/storage-files-scale-targets.md) ustalenie, jeśli usługi pliki Azure (który używa konta usługi Azure Storage) powinny być używane z puli oczekiwanego rozmiaru i liczbie plików zasobów. 

* Udziały plików platformy Azure są [ekonomiczne](https://azure.microsoft.com/pricing/details/storage/files/) i można je skonfigurować danych replikacji do innego regionu są tak globalnie nadmiarowe. 

* Można zainstalować udział plików na platformę Azure jednocześnie z komputera lokalnego.

* Zobacz też ogólne [zagadnienia związane z planowaniem](../storage/files/storage-files-planning.md) udziały plików platformy Azure.


## <a name="create-a-file-share"></a>Tworzenie udziału plików

[Tworzenie udziału plików](../storage/files/storage-how-to-create-file-share.md) konta magazynu, który jest połączony z kontem usługi partia zadań lub oddzielnego konta magazynu.

## <a name="mount-a-share-on-a-windows-pool"></a>Instalowanie udziału w puli systemu Windows

Ta sekcja zawiera kroki i przykłady kodu, aby zainstalować i używać plików na platformę Azure udziału w puli węzłów z systemem Windows. Aby uzyskać dodatkowe, zobacz [dokumentacji](../storage/files/storage-how-to-use-files-windows.md) dla platformy Azure instalowanie udziału plików w systemie Windows. 

W zadaniu wsadowym musisz zainstalować udział zawsze zadanie jest uruchamiane w węźle systemu Windows. Obecnie nie jest możliwy do utrwalenia połączenie sieciowe między zadaniami w węzłach systemu Windows.

Na przykład obejmować `net use` polecenie, aby zainstalować udział plików w ramach każdego zadania wiersza polecenia. Aby zainstalować udział plików, potrzebne są następujące:

* **Nazwa użytkownika**: AZURE\\\<storageaccountname\>, na przykład AZURE\\*mystorageaccountname*
* **Hasło**: < StorageAccountKeyWhichEnds w == >, na przykład *XXXXXXXXXXXXXXXXXXXXX ==*

Następujące polecenie na komputerze instalująca udział pliku *myfileshare* na koncie magazynu *mystorageaccountname* jako *S:* dysku:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Dla uproszczenia przykłady w tym miejscu, należy przekazać poświadczenia bezpośrednio w tekście. W praktyce zdecydowanie zaleca się zarządzanie poświadczeń przy użyciu zmiennych środowiskowych, certyfikatów i rozwiązania, takie jak usługi Azure Key Vault.

Aby uprościć operacji instalowania, opcjonalnie utrwalić poświadczenia w węzłach. Następnie mogą zainstalować udziały bez poświadczeń. Wykonaj poniższe dwa kroki:

1. Uruchom `cmdkey` w konfiguracji puli za pomocą zadania uruchamiania narzędzia wiersza polecenia. Ta sytuacja będzie trwać poświadczeń w każdym węźle systemu Windows. Wiersz polecenia zadania uruchamiania jest podobny do:

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. Instalowanie udziału w każdym węźle w ramach poszczególnych zadań za pomocą `net use`. Na przykład następujące polecenie w wierszu zadań instaluje udziału plików jako *S:* dysku. To może następować polecenia lub skryptu, który odwołuje się do udziału. Buforowane poświadczenia są używane w wywołaniu `net use`. W tym kroku przyjęto założenie, że używasz tej samej tożsamości użytkownika dla zadań, które są używane w zadaniu start w puli, która nie jest odpowiednia dla wszystkich scenariuszy.

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>Przykład C#
W poniższym przykładzie C# pokazano, jak można utrwalić poświadczenia w puli systemu Windows za pomocą zadania uruchamiania. Nazwa usługi Magazyn plików i magazynu poświadczeń są przekazywane jako zdefiniowanych stałych. W tym miejscu start zadanie jest uruchamiane (z systemem innym niż administrator) auto-konta użytkownika standardowego w zakresie puli.

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

Po przechowywane poświadczenia, należy użyć programu wierszy polecenia zadania instalowanie udziału i odwołać udział w odczytu lub operacje zapisu. Jako przykład basic, używa zadania wiersza polecenia w następujący fragment kodu `dir` polecenia, aby wyświetlić listę plików w udziale plików. Upewnij się, że do uruchamiania każdego zadanie korzystającej z tego samego [tożsamości użytkownika](batch-user-accounts.md) używane do uruchamiania zadania uruchamiania w puli. 

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

Udziały plików platformy Azure można zainstalować w dystrybucje systemu Linux przy użyciu [CIFS jądra klienta](https://wiki.samba.org/index.php/LinuxCIFS). Poniższy przykład przedstawia sposób instalacji udziału plików w puli Ubuntu 16.04 LTS węzłów obliczeniowych. Jeśli używasz innej dystrybucji systemu Linux, ogólne kroki są podobne, ale użyj Menedżera pakietów odpowiednie do dystrybucji. Aby uzyskać szczegóły i dodatkowe przykłady, zobacz [używanych plików Azure z systemem Linux](../storage/files/storage-how-to-use-files-linux.md).

Najpierw należy w ramach tożsamości użytkownika administrator, należy zainstalować `cifs-utils` pakietu, a następnie utworzyć punkt instalacji (na przykład */mnt/MyAzureFileShare*) w lokalnym systemie plików. Folder punktu instalacji można utworzyć dowolne miejsce w systemie plików, ale jest typowe Konwencji, aby je utworzyć w obszarze `/mnt` folderu. Należy pamiętać, aby nie utworzyć punkt instalacji bezpośrednio na `/mnt` (na Ubuntu) lub `/mnt/resource` (na inne dystrybucji).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Następnie uruchom `mount` polecenie, aby zainstalować udział plików, podając te poświadczenia:

* **Nazwa użytkownika**: \<storageaccountname\>, na przykład *mystorageaccountname*
* **Hasło**: < StorageAccountKeyWhichEnds w == >, na przykład *XXXXXXXXXXXXXXXXXXXXX ==*

Następujące polecenie na komputerze instalująca udział pliku *myfileshare* na koncie magazynu *mystorageaccountname* w */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Dla uproszczenia przykłady w tym miejscu, należy przekazać poświadczenia bezpośrednio w tekście. W praktyce zdecydowanie zaleca się zarządzanie poświadczeń przy użyciu zmiennych środowiskowych, certyfikatów i rozwiązania, takie jak usługi Azure Key Vault.

W puli Linux połączyć wszystkie te kroki w zadaniu pojedynczego uruchomienia lub uruchamiać je w skrypcie. Uruchom zadanie uruchamiania jako użytkownika administrator w puli. Ustaw zadanie start oczekiwania do pomyślnego wykonania przed uruchomieniem kolejne zadania w puli, odwołujący się do udziału.

### <a name="python-example"></a>Przykład Python

W poniższym przykładzie Python pokazuje, jak skonfigurować pulę Ubuntu, aby zainstalować udział w zadania uruchamiania. Punkt instalacji, punkt końcowy udziału plików i magazynu poświadczeń są przekazywane jako zdefiniowanych stałych. Zadania uruchamiania jest uruchamiana automatycznie — konta administratora z zakresem puli.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Po instalowanie udziału i definiowanie zadania, należy użyć udziału w sieci wierszy polecenia zadania. Na przykład następujące polecenie podstawowe używa `ls` do listy plików w udziale plików.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Kolejne kroki

* Inne opcje do odczytywania i zapisywania danych w partii, zobacz [Przegląd funkcji partii](batch-api-basics.md) i [utrwalić danych wyjściowych i zadań](batch-task-output.md).

* Zobacz też [stoczni partii](https://github.com/Azure/batch-shipyard) zestawu narzędzi, w tym [przepisami stoczni](https://github.com/Azure/batch-shipyard/tree/master/recipes) do wdrażania systemów plików dla obciążeń kontenera partii.