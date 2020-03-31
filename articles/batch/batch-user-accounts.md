---
title: Uruchamianie zadań w obszarze kont użytkowników — usługa Azure Batch
description: Warto skonfigurować konto użytkownika, w ramach którego ma zostać uruchomione zadanie. Dowiedz się, jakie typy kont użytkowników i jak je skonfigurować.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/18/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: fee3dc764d2052185160a4ba6b3d70854c54eeac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252274"
---
> [!NOTE] 
> Konta użytkowników omówione w tym artykule różnią się od kont użytkowników używanych w u protokółach RDP (Remote Desktop Protocol) lub Secure Shell (SSH) ze względów bezpieczeństwa. 
>
> Aby połączyć się z węzłem z uruchomieniem konfiguracji maszyny wirtualnej systemu Linux za pośrednictwem protokołu SSH, zobacz [Używanie pulpitu zdalnego na maszynie wirtualnej z systemem Linux na platformie Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Aby połączyć się z węzłami z systemem Windows za pośrednictwem protokołu RDP, zobacz [Łączenie się z maszyną wirtualną systemu Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Aby połączyć się z węzłem z uruchomieniem konfiguracji usługi w chmurze za pośrednictwem protokołu RDP, zobacz [Włączanie połączenia pulpitu zdalnego dla roli w usługach w chmurze Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Uruchamianie zadań w obszarze konta użytkowników w uł.o.

Zadanie w usłudze Azure Batch zawsze działa w ramach konta użytkownika. Domyślnie zadania są uruchamiane przy standardowych kontach użytkowników bez uprawnień administratora. Te domyślne ustawienia konta użytkownika są zazwyczaj wystarczające. W niektórych scenariuszach warto jednak skonfigurować konto użytkownika, w ramach którego ma zostać uruchomione zadanie. W tym artykule omówiono typy kont użytkowników i jak można je skonfigurować dla scenariusza.

## <a name="types-of-user-accounts"></a>Typy kont użytkowników

Usługa Azure Batch udostępnia dwa typy kont użytkowników do uruchamiania zadań:

- **Konta użytkowników automatycznych.** Konta użytkowników automatycznych są wbudowanymi kontami użytkowników, które są tworzone automatycznie przez usługę Batch. Domyślnie zadania są uruchamiane na koncie użytkownika automatycznego. Można skonfigurować specyfikację automatycznego użytkownika dla zadania, aby wskazać, w ramach którego konta użytkownika automatycznego zadanie powinno być uruchamiane. Specyfikacja użytkownika automatycznego umożliwia określenie poziomu elewacji i zakresu konta użytkownika automatycznego, które uruchomi zadanie. 

- **Nazwane konto użytkownika.** Podczas tworzenia puli można określić jedno lub więcej nazwanych kont użytkowników dla puli. Każde konto użytkownika jest tworzone w każdym węźle puli. Oprócz nazwy konta należy określić hasło konta użytkownika, poziom podniesienia uprawnień oraz, w przypadku pul systemu Linux, klucz prywatny SSH. Po dodaniu zadania można określić nazwane konto użytkownika, w ramach którego to zadanie ma być uruchamiane.

> [!IMPORTANT] 
> Usługa wsadowa w wersji 2017-01-01.4.0 wprowadza przełomową zmianę, która wymaga aktualizacji kodu, aby wywołać tę wersję. Jeśli migrujesz kod ze starszej wersji usługi Batch, należy pamiętać, że właściwość **runElevated** nie jest już obsługiwana w bibliotekach interfejsu API REST lub usługi Batch. Użyj nowej **właściwości userIdentity** zadania, aby określić poziom podniesienia uprawnień. Zobacz sekcję ["Aktualizowanie kodu do najnowszej biblioteki klienta usługi Batch, aby](#update-your-code-to-the-latest-batch-client-library) uzyskać szybkie wskazówki dotyczące aktualizowania kodu usługi Batch w przypadku korzystania z jednej z bibliotek klienckich.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Dostęp do plików i katalogów konta użytkownika

Zarówno konto użytkownika automatycznego, jak i nazwane konto użytkownika mają dostęp do odczytu/zapisu do katalogu roboczego zadania, katalogu udostępnionego i katalogu zadań z wieloma wystąpieniami. Oba typy kont mają dostęp do odczytu do katalogów uruchamiania i przygotowania zadań.

Jeśli zadanie jest uruchamiane przy użyciu tego samego konta, które zostało użyte do uruchomienia zadania początkowego, zadanie ma dostęp do odczytu i zapisu do katalogu zadań startowych. Podobnie jeśli zadanie jest uruchamiane przy użyciu tego samego konta, które było używane do uruchamiania zadania przygotowania zadania, zadanie ma dostęp do odczytu i zapisu do katalogu zadań przygotowania zadania. Jeśli zadanie jest uruchamiane na innym koncie niż zadanie uruchamiania lub przygotowanie zadania, zadanie ma dostęp do odczytu tylko do odpowiedniego katalogu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do plików i katalogów z zadania, zobacz [Tworzenie rozwiązań obliczeniowych na dużą skalę równoległych za pomocą usługi Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Podwyższony dostęp do zadań 

Poziom podniesienia poziomu konta użytkownika wskazuje, czy zadanie jest uruchamiane z podwyższonym poziomem dostępu. Zarówno konto użytkownika automatycznego, jak i nazwane konto użytkownika mogą być uruchamiane z podwyższonym poziomem uprawnień. Dwie opcje poziomu elewacji to:

- **NonAdmin:** Zadanie jest uruchamiane jako użytkownik standardowy bez podwyższonego poziomu dostępu. Domyślnym poziomem podniesienia uprawnień dla konta użytkownika usługi Batch jest zawsze **nonadmin**.
- **Administrator:** Zadanie jest uruchamiane jako użytkownik z podwyższonym poziomem dostępu i działa z pełnymi uprawnieniami administratora. 

## <a name="auto-user-accounts"></a>Konta użytkowników automatycznych

Domyślnie zadania uruchamiane w ulewce w ramach konta użytkownika automatycznego, jako użytkownik standardowy bez podwyższonego dostępu i z zakresem zadania. Gdy specyfikacja użytkownika automatycznego jest skonfigurowana dla zakresu zadań, usługa Batch tworzy konto automatycznego użytkownika tylko dla tego zadania.

Alternatywą dla zakresu zadań jest zakres puli. Gdy specyfikacja automatycznego użytkownika dla zadania jest skonfigurowana dla zakresu puli, zadanie jest uruchamiane w ramach konta użytkownika automatycznego, które jest dostępne dla dowolnego zadania w puli. Aby uzyskać więcej informacji na temat zakresu puli, zobacz sekcję "Uruchamianie zadania jako autouzużnika z zakresem puli.For more information about pool scope, see the section titled Run a task as the auto-user with pool scope.   

Domyślny zakres jest inny w węzłach Windows i Linux:

- W węzłach systemu Windows zadania są domyślnie uruchamiane w zakresie zadań.
- Węzły systemu Linux zawsze działają w zakresie puli.

Istnieją cztery możliwe konfiguracje dla specyfikacji użytkownika automatycznego, z których każda odpowiada unikatowemu kontu automatycznego użytkownika:

- Dostęp niebędący administratorem z zakresem zadania (domyślna specyfikacja automatycznego użytkownika)
- Dostęp administratora (z podwyższonym poziomem uprawnień) z zakresem zadania
- Dostęp niebędący administratorem z zakresem puli
- Dostęp administratora z zakresem puli

> [!IMPORTANT] 
> Zadania uruchomione w zakresie zadań nie mają de facto dostępu do innych zadań w węźle. Jednak złośliwy użytkownik z dostępem do konta może obejść to ograniczenie, przesyłając zadanie, które jest uruchamiane z uprawnieniami administratora i uzyskuje dostęp do innych katalogów zadań. Złośliwy użytkownik może również użyć protokołu RDP lub SSH do połączenia się z węzłem. Ważne jest, aby chronić dostęp do kluczy konta usługi Batch, aby zapobiec takiemu scenariuszowi. Jeśli podejrzewasz, że twoje konto mogło zostać przejęte, pamiętaj o wygenerowaniu kluczy.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Uruchamianie zadania jako autouzuchodź z podwyższonym poziomem dostępu

Specyfikację automatycznego użytkownika dla uprawnień administratora można skonfigurować, gdy trzeba uruchomić zadanie z podwyższonym poziomem dostępu. Na przykład zadanie uruchamiania może wymagać podwyższonego dostępu do zainstalowania oprogramowania w węźle.

> [!NOTE] 
> Ogólnie rzecz biorąc najlepiej jest używać dostępu z podwyższonym poziomem uprawnień tylko wtedy, gdy jest to konieczne. Najlepsze praktyki zalecają przyznanie minimalnego przywileju niezbędnego do osiągnięcia pożądanego rezultatu. Jeśli na przykład zadanie startowe zainstaluje oprogramowanie dla bieżącego użytkownika, a nie dla wszystkich użytkowników, można uniknąć przyznania podwyższonego poziomu dostępu do zadań. Można skonfigurować specyfikację automatycznego użytkownika dla zakresu puli i dostępu niedaministratora dla wszystkich zadań, które muszą być uruchamiane na tym samym koncie, w tym zadania początkowego. 
>
>

Poniższe fragmenty kodu pokazują, jak skonfigurować specyfikację użytkownika automatycznego. W przykładach ustawiono poziom `Admin` elewacji `Task`i zakres na . Zakres zadania jest ustawieniem domyślnym, ale jest tutaj uwzględniony na przykład.

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Uruchamianie zadania jako autouzułka z zakresem puli

Gdy węzeł jest aprowizowana, dwa konta użytkowników automatycznych w całej puli są tworzone w każdym węźle w puli, jeden z podwyższonym poziomem dostępu i jeden bez podwyższonego poziomu dostępu. Ustawienie zakresu automatycznego użytkownika do puli zakresu dla danego zadania uruchamia zadanie w ramach jednego z tych dwóch kont użytkowników automatycznych w całej puli. 

Po określeniu zakresu puli dla użytkownika automatycznego, wszystkie zadania uruchamiane z dostępem administratora są uruchamiane na tym samym koncie automatycznego użytkownika w całej puli. Podobnie zadania, które są uruchamiane bez uprawnień administratora również działają w ramach jednego konta automatycznego użytkownika w całej puli. 

> [!NOTE] 
> Dwa konta użytkowników automatycznych w całej puli są oddzielnymi kontami. Zadania uruchomione na koncie administracyjnym w całej puli nie mogą udostępniać danych z zadaniami uruchomionymi w ramach konta standardowego i odwrotnie. 
>
>

Zaletą uruchamiania w ramach tego samego konta użytkownika automatycznego jest to, że zadania są w stanie udostępniać dane innym zadaniom uruchomionym w tym samym węźle.

Udostępnianie wpisów tajnych między zadaniami jest jednym ze scenariuszy, w którym przydatne jest uruchamianie zadań w ramach jednego z dwóch kont użytkowników automatycznych w całej puli. Załóżmy na przykład, że zadanie początkowe musi aprowizować klucz tajny do węzła, którego mogą używać inne zadania. Można użyć interfejsu API ochrony danych systemu Windows (DPAPI), ale wymaga uprawnień administratora. Zamiast tego można chronić klucz tajny na poziomie użytkownika. Zadania uruchomione na tym samym koncie użytkownika mogą uzyskiwać dostęp do klucza tajnego bez podwyższonego poziomu dostępu.

Innym scenariuszem, w którym można uruchomić zadania w ramach konta użytkownika automatycznego z zakresem puli jest udział plików interfejsu MPI (Message Passing Interface). Udział plików MPI jest przydatny, gdy węzły w zadaniu MPI muszą pracować nad tymi samymi danymi pliku. Węzeł główny tworzy udział plików, do który węzły podrzędne mogą uzyskać dostęp, jeśli są uruchomione w ramach tego samego konta użytkownika automatycznego. 

Poniższy fragment kodu ustawia zakres automatycznego użytkownika do puli zakresu zadania w utrwieniu .NET. Poziom podniesienia uprawnień jest pomijany, więc zadanie jest uruchamiane w ramach standardowego konta automatycznego użytkownika w całej puli.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Konta użytkowników nazwanych

Podczas tworzenia puli można zdefiniować nazwane konta użytkowników. Nazwane konto użytkownika ma podaną nazwę i hasło. Można określić poziom podniesienia uprawnień dla nazwanego konta użytkownika. W przypadku węzłów systemu Linux można również podać klucz prywatny SSH.

Nazwane konto użytkownika istnieje we wszystkich węzłach w puli i jest dostępne dla wszystkich zadań uruchomionych w tych węzłach. Można zdefiniować dowolną liczbę nazwanych użytkowników dla puli. Po dodaniu kolekcji zadań lub zadań można określić, że zadanie jest uruchamiane w obszarze jednego z nazwanych kont użytkowników zdefiniowanych w puli.

Nazwane konto użytkownika jest przydatne, gdy chcesz uruchomić wszystkie zadania w zadaniu w ramach tego samego konta użytkownika, ale odizolować je od zadań uruchomionych w innych zadaniach w tym samym czasie. Na przykład można utworzyć nazwanego użytkownika dla każdego zadania i uruchomić zadania każdego zadania w ramach tego nazwanego konta użytkownika. Każde zadanie może następnie udostępnić klucz tajny z własnymi zadaniami, ale nie z zadaniami uruchomionymi w innych zadaniach.

Za pomocą nazwanego konta użytkownika można również uruchomić zadanie, które ustawia uprawnienia do zasobów zewnętrznych, takich jak udziały plików. Za pomocą nazwanego konta użytkownika można kontrolować tożsamość użytkownika i można użyć tej tożsamości użytkownika do ustawienia uprawnień.  

Nazwane konta użytkowników umożliwiają bez hasła SSH między węzłami systemu Linux. Można użyć nazwanego konta użytkownika z węzłami systemu Linux, które muszą uruchamiać zadania z wieloma wystąpieniami. Każdy węzeł w puli można uruchamiać zadania w ramach konta użytkownika zdefiniowanego w całej puli. Aby uzyskać więcej informacji na temat zadań z wieloma wystąpieniami, zobacz [Uruchamianie aplikacji\-MPI za pomocą zadań wielu wystąpień](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Tworzenie nazwanych kont użytkowników

Aby utworzyć nazwane konta użytkowników w uliczce, dodaj do puli kolekcję kont użytkowników. Poniższe fragmenty kodu pokazują, jak utworzyć nazwane konta użytkowników w .NET, Java i Python. Te fragmenty kodu pokazują, jak utworzyć konta o nazwie administratora i niedemordy w puli. Przykłady utworzyć pule przy użyciu konfiguracji usługi w chmurze, ale używasz tego samego podejścia podczas tworzenia puli systemu Windows lub Linux przy użyciu konfiguracji maszyny wirtualnej.

#### <a name="batch-net-example-windows"></a>Przykład wsadowy .NET (Windows)

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

#### <a name="batch-net-example-linux"></a>Przykład wsadowy .NET (Linux)

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


#### <a name="batch-java-example"></a>Przykład Java wsadowego

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

#### <a name="batch-python-example"></a>Przykład pythona wsadowego

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Uruchamianie zadania przy nazwanym koncie użytkownika z podwyższonym poziomem dostępu

Aby uruchomić zadanie jako użytkownik z podwyższonym poziomem uprawnień, ustaw właściwość **UserIdentity** zadania na nazwane `Admin`konto użytkownika utworzone z jego właściwością **ElevationLevel** ustawioną na .

Ten fragment kodu określa, że zadanie powinno być uruchamiane przy nazwanym koncie użytkownika. To nazwane konto użytkownika zostało zdefiniowane w puli podczas tworzenia puli. W takim przypadku nazwane konto użytkownika zostało utworzone z uprawnieniami administratora:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualizowanie kodu do najnowszej biblioteki klienta usługi Batch

Usługa wsadowa w wersji 2017-01-01.4.0 wprowadza przełomową zmianę, zastępując właściwość **runElevated** dostępną we wcześniejszych wersjach **właściwością userIdentity.** Poniższe tabele zawierają proste mapowanie, którego można użyć do zaktualizowania kodu z wcześniejszych wersji bibliotek klienckich.

### <a name="batch-net"></a>Batch .NET

| Jeśli kod używa...                  | Zaktualizuj go do....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`nie określono | Nie jest wymagana aktualizacja                                                                                               |

### <a name="batch-java"></a>Batch Java

| Jeśli kod używa...                      | Zaktualizuj go do....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`nie określono | Nie jest wymagana aktualizacja                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Jeśli kod używa...                      | Zaktualizuj go do....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated`nie określono | Nie jest wymagana aktualizacja                                                                                                                                  |


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowy przegląd usługi Batch, zobacz [Tworzenie dużych równoległych rozwiązań obliczeniowych za pomocą usługi Batch](batch-api-basics.md).
