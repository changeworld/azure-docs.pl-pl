---
title: Uruchamianie zadań w ramach konta użytkownika — Azure Batch | Dokumentacja firmy Microsoft
description: Należy skonfigurować konta użytkowników do uruchamiania zadań w usłudze Azure Batch
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 000495ab84990f15885c254b472be7863c75da58
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877520"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Uruchamianie zadań w ramach kont użytkowników w usłudze Batch

Zadania w usłudze Azure Batch jest zawsze uruchamiany przy użyciu konta użytkownika. Domyślnie zadania są uruchamiane w ramach kont użytkowników standardowych, bez uprawnień administratora. Domyślne ustawienia konta użytkownika są zwykle wystarczające. W niektórych scenariuszach jednak warto mieć możliwość skonfigurowania konta użytkownika, pod którym ma zostać zadania do uruchomienia. W tym artykule omówiono typy kont użytkowników i jak można je skonfigurować dla danego scenariusza.

## <a name="types-of-user-accounts"></a>Typy kont użytkowników

Usługa Azure Batch udostępnia dwa typy kont użytkowników do uruchamiania zadań:

- **Konta użytkowników automatycznie.** Konta użytkowników automatycznie są wbudowane konta użytkowników, które są tworzone automatycznie przez usługę Batch. Domyślnie zadania są uruchamiane na koncie użytkownika automatycznie. Można skonfigurować specyfikacji automatycznie użytkownika dla zadania wskazać, pod które konto użytkownika automatycznie uruchamiać zadanie. Specyfikacja użytkownika automatycznie pozwala określić poziom podniesienia uprawnień i zakres konto użytkownika automatycznie uruchomi zadanie. 

- **Konto użytkownika o nazwie.** Podczas tworzenia puli można określić co najmniej jednego konta użytkownika o nazwie dla puli. Każde konto użytkownika jest tworzony w każdym węźle puli. Oprócz nazwy konta określ hasło konta użytkownika, podniesienia uprawnień poziom, a w przypadku pul systemu Linux, prywatny klucz SSH. Po dodaniu zadania, można określić konto użytkownika nazwanego, na którym jest uruchamiany tego zadania.

> [!IMPORTANT] 
> W przypadku usługi Batch wersji 2017-01-01.4.0 wprowadzono istotnej zmiany, które wymagają aktualizacji update kodu do wywołania tej wersji. Jeśli jesteś Migrowanie kodu ze starszej wersji usługi Batch, należy pamiętać, że **runElevated** właściwość nie jest już obsługiwana w bibliotekach klienta interfejsu API REST lub partii. Użyj nowego **tożsamości użytkownika** właściwości zadania, aby określić poziom podniesienia uprawnień. Zobacz sekcję pod tytułem [aktualizowania kodu do najnowszej biblioteki klienta usługi Batch](#update-your-code-to-the-latest-batch-client-library) szybkie wytyczne dotyczące aktualizowania kodu usługi Batch, korzystając z jednej z bibliotek klienckich.
>
>

> [!NOTE] 
> Konta użytkowników, omówionych w tym artykule nie obsługują protokołu RDP (Remote Desktop) lub protokołu Secure Shell (SSH), ze względów bezpieczeństwa. 
>
> Aby połączyć z węzła z systemem Linux konfiguracji maszyny wirtualnej za pośrednictwem protokołu SSH, zobacz [użycie pulpitu zdalnego do maszyny Wirtualnej z systemem Linux na platformie Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Aby połączyć z węzłów z systemem Windows za pośrednictwem protokołu RDP, zobacz [nawiązywanie połączenia z maszyny Wirtualnej z systemem Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Aby połączyć z węzła z systemem konfiguracji usługi w chmurze za pośrednictwem protokołu RDP, zobacz [Włącz Podłączanie pulpitu zdalnego dla roli w usługach Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Kontu użytkownika dostępu do plików i katalogów

Zarówno konto użytkownika automatycznie, jak i konto użytkownika o nazwie dostęp odczytu/zapisu do katalogu roboczego zadania podrzędnego, udostępniony katalog i katalog zadania obejmujące wiele wystąpień. Oba rodzaje kont mają dostęp do odczytu do katalogów przygotowania uruchamiania i zadania.

Jeśli zadanie jest uruchamiane w ramach tego samego konta, którego użyto do uruchamiania zadania uruchamiania, zadanie ma dostęp do odczytu i zapisu do katalogu uruchamiania zadań. Podobnie jeśli zadanie jest uruchamiane w ramach tego samego konta, którego użyto do uruchamiania zadania przygotowania zadania, zadanie ma dostęp do odczytu i zapisu do katalogu zadania przygotowania zadania. Jeśli zadanie jest uruchamiane przy użyciu innego konta niż zadania podrzędnego uruchamiania lub zadanie przygotowania zadania, zadanie ma dostęp tylko do odczytu do odpowiednich katalogu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do plików i katalogów z zadania, zobacz [tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Podwyższonego poziomu dostępu dla zadań 

Konto użytkownika podniesienie poziomu wskazuje, czy zadanie jest uruchamiane z dostępem z podwyższonym poziomem uprawnień. Zarówno konto użytkownika automatycznie, jak i konto użytkownika nazwanego, można uruchomić z podwyższonego poziomu dostępu. Istnieją dwie opcje podniesienie poziomu:

- **Tekst NonAdmin:** Zadanie jest uruchamiane jako użytkownik standardowy bez podwyższonego poziomu dostępu. Domyślny poziom podniesienia uprawnień dla konta użytkownika usługi Batch jest zawsze **tekst NonAdmin**.
- **Administrator:** Zadanie jest uruchamiane jako użytkownik z dostępem z podwyższonym poziomem uprawnień i działa z pełnymi uprawnieniami administratora. 

## <a name="auto-user-accounts"></a>Konta użytkowników automatycznie

Domyślnie zadania uruchamiane w usłudze Batch przy użyciu konta użytkownika automatycznie jako użytkownik standardowy bez podwyższonego poziomu dostępu, a także w zakresie zadania. Gdy specyfikacja automatycznie niezwiązanych z użytkownikiem jest skonfigurowany dla zakresu zadań, usługa Batch tworzy konto użytkownika automatycznie tylko tego zadania.

Alternatywa dla zakresu zadań jest zakres puli. Gdy specyfikacji automatycznie użytkownika dla zadania jest skonfigurowany dla zakresu w puli, zadanie jest uruchamiane na koncie użytkownika automatycznie dostępne dla dowolnego zadania w puli. Aby uzyskać więcej informacji na temat zakresu w puli zobacz sekcję pod tytułem wykonywania zadania jako użytkownik automatycznie zakresie puli.   

Domyślny zakres różni się w węzłach Windows i Linux:

- W węzłach Windows zadania są uruchamiane w ramach zakresu zadań domyślnie.
- Węzłów systemu Linux są zawsze uruchamiane w ramach zakresu w puli.

Istnieją cztery możliwe konfiguracje specyfikacji automatycznie niezwiązanych z użytkownikiem, z których każdy odnosi się do unikatowego auto-konta użytkownika:

- Dostęp inni niż administratorzy z zakresem zadań (domyślna Specyfikacja użytkownika automatycznie)
- Administrator (podwyższone uprawnienia) dostępu w zakresie zadania
- Dostęp inni niż administratorzy z zakresem puli
- Dostęp administratora o zakresie puli

> [!IMPORTANT] 
> Zadania uruchomione w ramach zakresu zadań nie mają de facto dostępu do innych zadań w węźle. Jednak przez złośliwego użytkownika mającego dostęp do konta można obejść to ograniczenie, przesyłając zadanie, które jest uruchamiane z uprawnieniami administratora i uzyskuje dostęp do katalogów innych zadań. Złośliwy użytkownik może również użyć protokołu RDP lub SSH Połącz się z węzłem. Jest ważne w celu ochrony dostępu do kluczy konta usługi Batch, aby zapobiec takiej sytuacji. Jeśli podejrzewasz, że Twoje konto zostało naruszone, należy ponownie wygenerować klucze.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Uruchamianie zadania jako użytkownik automatycznie z podwyższonego poziomu dostępu

Należy uruchomić zadanie z podwyższonym poziomem uprawnień, można skonfigurować w specyfikacji użytkownika automatycznie uprawnienia administratora. Na przykład zadanie podrzędne uruchamiania może być konieczne podwyższonego poziomu dostępu do zainstalowania oprogramowania na węźle.

> [!NOTE] 
> Ogólnie rzecz biorąc zaleca się używać dostępu z podwyższonym poziomem uprawnień, tylko wtedy, gdy jest to konieczne. Najlepszym rozwiązaniem jest przyznanie minimalne uprawnienia niezbędne do osiągnięcia pożądanego rezultatu. Na przykład jeśli zadanie podrzędne uruchamiania zainstaluje oprogramowanie dla bieżącego użytkownika zamiast dla wszystkich użytkowników, można uniknąć udzielania podwyższonego poziomu dostępu do zadań. Można skonfigurować specyfikacji użytkownika automatycznie puli zakresu i bez uprawnień administratora dostęp do wszystkich zadań, które mają zostać uruchomione w ramach tego samego konta, w tym zadania podrzędnego uruchamiania. 
>
>

Poniższe fragmenty kodu przedstawiają sposób konfigurowania specyfikacji użytkownika automatycznie. Przykłady zestawu podniesienia uprawnień poziom `Admin` i zakres do `Task`. Zakres zadania jest ustawieniem domyślnym, ale został tu zawarty, przykładach.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Uruchamianie zadania jako użytkownik automatycznie zakresie puli

Gdy węzeł zostanie zaaprowizowana, dwa całej puli automatycznej użytkownika konta są tworzone w każdym węźle w puli, z podwyższonym poziomem uprawnień i jedną bez podwyższonego poziomu dostępu. Ustawianie zakresu użytkownika automatycznie do zakresu puli dla danego zadania uruchamia zadanie w ramach jednego z tych dwóch kont użytkowników automatycznie w całej puli. 

Po określeniu zakresu puli dla użytkownika automatycznie, wszystkie zadania, które będą uruchamiane z uprawnieniami administratora, uruchamiane przy użyciu tego samego konta użytkowników automatycznie w całej puli. Podobnie zadania, które są uruchamiane bez uprawnień administratora również uruchomić w ramach jednego całej puli auto-konta użytkownika. 

> [!NOTE] 
> Dwa konta użytkowników automatycznie w całej puli są osobne konta. Zadania uruchomione w ramach konta administracyjnego w całej puli nie może udostępniać dane zadania uruchomione w ramach konta standardowe i na odwrót. 
>
>

Zaletą uruchamiania w ramach tego samego konta użytkownika automatycznie jest, zadania i mogą udostępniać dane inne zadania uruchomione w tym samym węźle.

Udostępnianie kluczy tajnych między zadaniami jest jeden scenariusz, w których jest użyteczny uruchamianie zadań zgodnie z jednym z dwóch kont użytkowników automatycznie w całej puli. Na przykład załóżmy, że zadanie uruchamiania potrzebuje aprowizować wpisu tajnego na węzeł, który można użyć innych zadań. Można użyć interfejsu API ochrony danych Windows (DPAPI), ale wymaga uprawnień administratora. Zamiast tego można chronić klucz tajny na poziomie użytkownika. Zadania uruchomione w ramach tego samego konta użytkownika mają dostęp do klucza tajnego bez podwyższonego poziomu dostępu.

Inny scenariusz, w których warto uruchamiać zadania przy użyciu konta użytkownika automatycznie zakresie puli to udział plików interfejsu przekazywania komunikatów (MPI). Udział plików MPI jest przydatne, gdy węzły w ramach zadania MPI potrzebne do pracy na tych samych danych plików. Węzeł główny tworzy udział plików, których węzły podrzędne może uzyskać dostęp, jeśli są uruchomione na tym samym koncie użytkownika automatycznie. 

Poniższy fragment kodu ustawia zakres użytkownika automatycznie do zakresu puli zadania na platformie .NET usługi Batch. Podniesienie poziomu zostanie pominięty, dzięki czemu zadanie jest uruchamiane na koncie całej puli auto użytkownika standardowego.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Konta użytkowników o nazwie

Konta użytkowników o nazwie można zdefiniować podczas tworzenia puli. Konto użytkownika o nazwie ma nazwę i hasło. Można określić poziom podniesienia uprawnień dla konta użytkownika o nazwie. W przypadku węzłów systemu Linux możesz podać prywatny klucz SSH.

Konto użytkownika o nazwie istnieje na wszystkich węzłach w puli i jest dostępny dla wszystkich zadań uruchomionych na tych węzłach. Może zdefiniować dowolną liczbę nazwanych użytkowników dla puli. Po dodaniu zadania lub kolekcji zadań, można określić, czy zadanie jest uruchamiane jedno z kont nazwanego użytkownika zdefiniowane w puli.

Konto użytkownika o nazwie jest przydatne, gdy użytkownik chce uruchamiać wszystkie zadania w zadaniu, w ramach tego samego konta użytkownika, przy jednoczesnym izolowaniu ich od zadania uruchomione w innych zadań w tym samym czasie. Na przykład można utworzyć nazwanego użytkownika dla każdego zadania i uruchamiania zadań każde zadanie w ramach tego konta użytkownika o nazwie. Każde zadanie może następnie udostępnić wpisu tajnego z własnych zadań, ale nie z zadań uruchomionych w innych zadań.

Konto użytkownika o nazwie umożliwia również uruchamianie zadań, która ustawia uprawnienia zasobów zewnętrznych, takich jak udziały plików. Przy użyciu konta użytkownika nazwanego kontrolowanie tożsamości użytkownika i użyć tej tożsamości użytkownika, aby ustawić uprawnienia.  

Konta użytkowników o nazwie włączyć protokół SSH bez hasła, między węzłami systemu Linux. Można użyć konta o nazwie użytkownika przy użyciu węzłów systemu Linux, które mają zostać uruchomione zadania obejmujące wiele wystąpień. Każdy węzeł w puli, można uruchomić zadania przy użyciu konta użytkownika zdefiniowane w całej puli. Aby uzyskać więcej informacji na temat zadania obejmujące wiele wystąpień, zobacz [używać wielu\-wystąpienia zadania do uruchamiania aplikacji MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Tworzenie kont użytkowników o nazwie

Aby utworzyć nazwanego użytkownika konta w usłudze Batch, należy dodać do puli zbiór kont użytkowników. Poniższe fragmenty kodu przedstawiają sposób tworzenia kont użytkowników o nazwie w .NET, Java i Python. Te fragmenty kodu przedstawiają sposób tworzenia administratora i bez uprawnień administratora o nazwie kontami w puli. Przykłady tworzenia pul za pomocą konfiguracji usługi w chmurze, ale Użyj tej samej metody, podczas tworzenia puli Windows lub Linux, za pomocą konfiguracji maszyny wirtualnej.

#### <a name="batch-net-example-windows"></a>Przykład dla środowiska .NET usługi Batch (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Przykład dla środowiska .NET usługi Batch (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Przykładzie usługi Batch w języku Java

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Przykład języka Python usługi Batch

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Uruchom zadanie w ramach konta użytkownika o nazwie z podwyższonego poziomu dostępu

Aby uruchomić zadanie z podwyższonymi uprawnieniami użytkownika, należy ustawić zadania podrzędnego **tożsamości użytkownika** właściwości konta użytkownika o nazwie, który został utworzony za pomocą jego **ElevationLevel** właściwością `Admin`.

Ten fragment kodu Określa, że zadanie powinno być uruchamiane w ramach konta użytkownika o nazwie. To konto użytkownika o nazwie został zdefiniowany w puli, podczas tworzenia puli. W tym przypadku konta użytkownika o nazwie został utworzony z uprawnieniami administratora:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Zaktualizuj swój kod do najnowszej biblioteki klienta usługi Batch

Wersja usługi Batch 2017-01-01.4.0 wprowadza istotną zmianę, zastępując **runElevated** właściwości dostępne we wcześniejszych wersjach z **tożsamości użytkownika** właściwości. Poniższe tabele zawierają mapowania proste, który służy do aktualizowania kodu ze starszych wersji bibliotek klienckich.

### <a name="batch-net"></a>Batch .NET

| Jeśli kod używa...                  | Aby zaktualizować...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Nie określono | Aktualizacja nie jest wymagana                                                                                               |

### <a name="batch-java"></a>Batch Java

| Jeśli kod używa...                      | Aby zaktualizować...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Nie określono | Aktualizacja nie jest wymagana                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Jeśli kod używa...                      | Aby zaktualizować...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin))`             |
| `run_elevated` Nie określono | Aktualizacja nie jest wymagana                                                                                                                                  |


## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe omówienie usługi Batch, zobacz [tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).
