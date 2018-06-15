---
title: Uruchom zadania na kontach użytkowników w partii zadań Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie kont użytkowników do uruchamiania zadań w partii zadań Azure
services: batch
author: dlepow
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
ms.author: danlep
ms.openlocfilehash: 1b9c0514e93fa89f8776d830ef242fc4963a6f7b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316474"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Uruchomienie zadania na kontach użytkowników w partii

Zadania w partii zadań Azure jest zawsze uruchamiany na koncie użytkownika. Domyślnie zadania są uruchamiane na kontach użytkowników standardowych, bez uprawnień administratora. Domyślne ustawienia konta użytkownika są zwykle wystarczające. W niektórych scenariuszach jednak warto można skonfigurować konto użytkownika, które ma być uruchomienie zadania. W tym artykule omówiono typy kont użytkowników i jak można je skonfigurować dla danego scenariusza.

## <a name="types-of-user-accounts"></a>Typy kont użytkowników

Partia zadań Azure oferuje dwa typy kont użytkowników do uruchamiania zadań:

- **Konta użytkowników automatycznie.** Konta użytkowników automatycznie są wbudowane konta użytkowników, które są tworzone automatycznie przez usługi partia zadań. Domyślnie zadania są uruchamiane na koncie użytkownika automatycznie. Można skonfigurować specyfikacji automatycznie użytkownika dla tego zadania wskazać, które konta użytkowników automatycznie uruchamiać zadanie. Specyfikacja automatycznie użytkownika umożliwia określenie podniesienie poziomu i zakres konta użytkownika automatycznie, które zostanie uruchomione zadanie. 

- **Konto użytkownika o nazwie.** Po utworzeniu puli można określić co najmniej jednego konta użytkownika o nazwie puli. Każde konto użytkownika jest tworzony w każdym węźle w puli. Oprócz nazwy konta określ hasło konta użytkownika, podniesienie poziomu, a w przypadku pul systemu Linux, klucza prywatnego SSH. Podczas dodawania zadania można określić konta użytkownika o nazwie, w którym to zadanie powinno być ono uruchomione.

> [!IMPORTANT] 
> Wersja usługi partii 2017-01-01.4.0 wprowadzono istotne zmiany wymaga zaktualizowanie wywołania tej wersji. W przypadku migrowania kodu ze starszej wersji partii należy pamiętać, że **runElevated** właściwość nie jest już obsługiwana w bibliotekach klienta interfejsu API REST lub partii. Użyj nowych **tożsamości użytkownika** właściwości zadań umożliwia określenie poziomu podniesienia uprawnień. Zobacz sekcję [zaktualizuj kod do najnowszej biblioteki klienta usługi partia zadań](#update-your-code-to-the-latest-batch-client-library) szybkie wytyczne dotyczące aktualizowanie kodu partii, korzystając z jednej z bibliotek klienta.
>
>

> [!NOTE] 
> Konta użytkowników omówione w tym artykule nie obsługują protokołu RDP (Remote Desktop) lub protokołu Secure Shell (SSH), ze względów bezpieczeństwa. 
>
> Aby nawiązać połączenie węzła z systemem Linux konfiguracji maszyny wirtualnej za pośrednictwem protokołu SSH, zobacz [Użyj pulpitu zdalnego do maszyny Wirtualnej systemu Linux na platformie Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Aby nawiązać połączenie węzłów z systemem Windows za pomocą protokołu RDP, zobacz [połączenia z maszyną wirtualną serwera Windows](../virtual-machines/windows/connect-logon.md).<br /><br />
> Aby nawiązać połączenie węzła z systemem konfiguracji usługi w chmurze za pomocą protokołu RDP, zobacz [włączyć Podłączanie pulpitu zdalnego dla roli w usług Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Kontu użytkownika dostępu do plików i katalogów

Zarówno konto użytkownika automatycznie, jak i konto użytkownika o nazwie mają dostęp odczytu/zapisu do katalogu roboczego zadania, udostępnionego katalogu i katalog zadania wielu wystąpień. Oba typy kont mają dostęp do odczytu do katalogów przygotowania uruchamiania i zadania.

Jeśli zadanie zostanie wykonane dla tego samego konta, którego użyto do uruchomienia zadania uruchamiania, zadanie ma dostęp do odczytu i zapisu do katalogu uruchomienia zadania. Podobnie jeśli zadanie zostanie wykonane dla tego samego konta, którego użyto do uruchomienia zadania przygotowanie zadania, zadanie ma dostęp do odczytu i zapisu do katalogu zadanie przygotowanie zadania. Jeśli zadanie jest uruchamiane przy użyciu innego konta niż zadanie uruchamiania lub zadanie przygotowanie zadania, zadanie ma dostęp tylko do odczytu do odpowiedniego katalogu.

Aby uzyskać więcej informacji dotyczących uzyskiwania dostępu do plików i katalogów z zadania, zobacz [Programowanie równoległe na dużą skalę obliczeniowe rozwiązań w partii](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Z podwyższonym poziomem uprawnień dostępu do zadania 

Konto użytkownika podniesienie poziomu wskazuje, czy zadanie jest uruchamiane z podwyższonym poziomem uprawnień. Zarówno konto użytkownika automatycznie, jak i konto użytkownika o nazwie można uruchomić z podwyższonym poziomem uprawnień. Dostępne są następujące dwie opcje podniesienie poziomu:

- **NonAdmin:** zadanie jest uruchamiane jako użytkownik standardowy bez podwyższonego poziomu dostępu. Domyślny poziom podniesienia uprawnień dla konta użytkownika usługi partia zadań jest zawsze **NonAdmin**.
- **Administrator:** zadania uruchamiane jako użytkownik z podwyższonym poziomem uprawnień i działa z pełnymi uprawnieniami administratora. 

## <a name="auto-user-accounts"></a>Konta użytkowników automatycznie

Domyślnie zadania są uruchamiane w trybie wsadowym na koncie użytkownika automatycznie jako użytkownik standardowy bez podwyższonego poziomu dostępu i z zakresu zadań. Po skonfigurowaniu specyfikacji użytkownika automatycznie zakresie zadania usługa partia zadań tworzy konta użytkownika automatycznie tylko tego zadania.

Alternatywą dla zakresu zadań jest zakresem puli. Po skonfigurowaniu specyfikacji użytkownika automatycznie zadania dla zakresu puli zadanie jest uruchamiane na koncie użytkownika automatycznie dostępne dla dowolnego zadania w puli. Aby uzyskać więcej informacji na temat zakresu puli, zobacz sekcji [uruchomienia zadania jako użytkownik automatycznie z zakresem puli](#run-a-task-as-the-autouser-with-pool-scope).   

Domyślny zakres różni się w węzłach systemu Windows i Linux:

- W węzłach Windows zadania są uruchamiane w zakresie zadania domyślnie.
- Węzły Linux są zawsze uruchamiane w zakresie puli.

Istnieją cztery możliwe konfiguracje specyfikacji automatycznie użytkownika, z których każdy odpowiada unikatowe auto-konto użytkownika:

- Dostęp inni niż administratorzy z zakresem zadań (domyślna Specyfikacja użytkownika automatycznie)
- Uprawnienia administratora (podwyższonymi) z zakresem zadań
- Dostęp inni niż administratorzy z zakresem puli
- Uprawnienia administratora w zakresie puli

> [!IMPORTANT] 
> Zadania uruchomione w zakresie zadania nie mają faktyczne dostępu do wykonywania innych zadań w węźle. Jednak przez złośliwego użytkownika mającego dostęp do konta można obejść to ograniczenie przesyłania zadań, jest uruchamiany z uprawnieniami administratora, który uzyskuje dostęp do innych katalogów zadań. Złośliwy użytkownik można również użyć protokołu RDP lub SSH nawiązać połączenia z węzłem. Jest ważne uniemożliwić dostęp do klucze konta wsadowego, aby zapobiec takiej sytuacji. Jeśli podejrzewasz, że Twoje konto zostało naruszone, należy ponownie wygenerować klucze.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Uruchamianie zadania jako użytkownik automatycznie z podwyższonym poziomem uprawnień

Należy uruchomić zadanie z podwyższonym poziomem uprawnień, można skonfigurować w specyfikacji użytkownika automatycznie uprawnienia administratora. Na przykład może być konieczne zadania uruchamiania z podwyższonym poziomem uprawnień dostępu do instalowania oprogramowania na węźle.

> [!NOTE] 
> Ogólnie rzecz biorąc najlepiej użyć podwyższonego poziomu dostępu tylko wtedy, gdy jest to konieczne. Najlepszym rozwiązaniem jest przyznanie minimalnych uprawnień niezbędnych do osiągnięcia żądanego wyniku. Na przykład jeśli zadanie uruchamiania instalacji oprogramowania dla bieżącego użytkownika, a nie dla wszystkich użytkowników, można uniknąć udzielanie z podwyższonym poziomem uprawnień dostępu do zadania. Można skonfigurować w specyfikacji użytkownika automatycznie puli zakresu i bez uprawnień administratora dostępu dla wszystkich zadań, które muszą działać w ramach tego samego konta, w tym zadania uruchamiania. 
>
>

Poniższe fragmenty kodu przedstawiają sposób konfigurowania specyfikacji użytkownika automatycznie. W przykładach ustawiana podniesienia uprawnień do poziomu `Admin` i zakresu `Task`. Zakres zadań jest ustawieniem domyślnym, ale został tu zawarty ze względu na przykład.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Java partii

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Uruchamianie zadania jako użytkownik automatycznie z zakresem puli

Gdy węzeł zostanie zainicjowana, konta dwóch całej puli automatycznej użytkownika są tworzone w każdym węźle w puli, z podwyższonym poziomem uprawnień, a drugi bez podwyższonego poziomu dostępu. Ustawianie zakresu użytkownika automatycznie do zakresu puli dla danego zadania uruchamia zadanie w ramach jednego z tych dwóch kont użytkowników automatycznie w całej puli. 

Po określeniu zakresu puli dla użytkownika automatycznie, wszystkie zadania, które będą uruchamiane z uprawnieniami administratora, uruchom w ramach tego samego konta użytkowników automatycznie w całej puli. Podobnie zadań, które są uruchamiane bez uprawnień administratora również uruchamiana jednego całej puli auto-konta użytkownika. 

> [!NOTE] 
> Dwa konta użytkowników automatycznie w całej puli są osobnych kont. Zadania uruchamiania konto administracyjne całej puli nie może udostępniać dane zadań uruchomionych w ramach standardowego konta i na odwrót. 
>
>

Zaletą do uruchomienia w ramach tego samego konta użytkownika automatycznie jest że zadania będą mogli udostępniać dane inne zadania uruchomione w tym samym węźle.

Udostępnianie kluczy tajnych między zadaniami jest jeden scenariusz, w których jest użyteczny uruchamianie zadań zgodnie z jednym z dwóch kont użytkowników automatycznie w całej puli. Na przykład załóżmy, że zadanie uruchamiania musi udostępniać klucz tajny na węźle, który można użyć innych zadań. Można użyć interfejsu API ochrony danych systemu Windows (DPAPI), ale wymaga uprawnień administratora. Zamiast tego można chronić klucz tajny na poziomie użytkownika. Zadania uruchomione w tym samym kontem użytkownika ma dostęp do hasła bez podwyższonego poziomu dostępu.

Inny scenariusz, w którym można uruchamiać zadania przy użyciu konta użytkownika automatycznie z zakresem puli jest udziałem plików wiadomości interfejsu (Passing Interface). MPI udziału plików jest przydatne, jeśli w węzłach zadań MPI muszą działać na tym samym pliku danych. Węzła głównego tworzy udział plików, które mają dostęp do węzłów podrzędnych, gdy działają w ramach tego samego konta użytkownika automatycznie. 

Poniższy fragment kodu ustawia zakres użytkownika automatycznie do zakresu puli zadania w partiami platformy .NET. Podniesienie poziomu zostanie pominięty, dlatego to zadanie jest uruchamiane na koncie całej puli auto użytkowników standardowych.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Konta użytkowników o nazwie

Podczas tworzenia puli, można określić konta użytkowników o nazwie. Konto użytkownika o nazwie ma nazwę i hasło, które można wprowadzić. Możesz określić poziom podniesienia uprawnień dla konta użytkownika o nazwie. Dla systemu Linux węzłów można też podać klucz prywatny SSH.

Konto użytkownika o nazwie istnieje we wszystkich węzłach w puli i jest dostępny dla wszystkich zadań uruchomionych na tych węzłach. Mogą określić dowolną liczbę użytkowników o nazwie puli. Podczas dodawania zadań lub kolekcji zadań, można określić, czy zadanie jest uruchamiane jeden z konta o nazwie użytkownika zdefiniowane w puli.

Konto użytkownika o nazwie jest przydatne, jeśli chcesz uruchamiać wszystkie zadania w ramach zadania dla tego samego konta użytkownika, przy jednoczesnym izolowaniu ich od zadań uruchomionych w innych zadań, w tym samym czasie. Na przykład można utworzyć nazwanego użytkownika dla każdego zadania i uruchamiać zadania każde zadanie w ramach tego konta użytkownika o nazwie. Każde zadanie mogło współużytkować klucz tajny z własną zadania, ale nie z zadań uruchomionych w innych zadań.

Konta użytkownika o nazwie służy również do uruchamiania zadań, która ustawia uprawnienia dotyczące zasobów zewnętrznych, takich jak udziały plików. Przy użyciu konta użytkownika o nazwie kontrolować tożsamości użytkownika i jego tożsamość użytkownika można ustawić za pomocą uprawnień.  

Konta użytkowników o nazwie Włącz SSH bez hasła między węzłami systemu Linux. Można użyć konta o nazwie użytkownika z węzłami Linux, które muszą uruchamiać wiele wystąpień zadania. Każdy węzeł w puli można uruchamiać zadania przy użyciu konta użytkownika zdefiniowane w całej puli. Aby uzyskać więcej informacji o wielu wystąpieniach zadań, zobacz [używać wielu\-wystąpienia zadania do uruchomienia aplikacji MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Utwórz konta użytkowników o nazwie

Aby utworzyć konta użytkownika o nazwie w partii, należy dodać do puli Kolekcja kont użytkowników. Poniższe fragmenty kodu przedstawiają sposób tworzenia kont użytkowników o nazwie w .NET, Java i Python. Te wstawki kodu pokazano, jak utworzyć zarówno administratora, jak i bez uprawnień administratora o nazwie kont w puli. Przykłady tworzenia pul, za pomocą konfiguracji usługi chmury, ale zastosować te same podejście, podczas tworzenia puli za pomocą konfiguracji maszyny wirtualnej systemu Windows lub Linux.

#### <a name="batch-net-example-windows"></a>Przykład .NET partii (system Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                                         
    virtualMachineSize: "small",                                                
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

#### <a name="batch-net-example-linux"></a>Przykład .NET partii (Linux)

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


#### <a name="batch-java-example"></a>Przykład Java partii

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

#### <a name="batch-python-example"></a>Przykład Python partii

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Uruchamianie zadania przy użyciu konta użytkownika o nazwie z podwyższonym poziomem uprawnień

Aby uruchomić zadanie z podwyższonym poziomem uprawnień użytkownika, należy ustawić zadania **tożsamości użytkownika** dla właściwości o nazwie konta, które utworzono za pomocą jego **ElevationLevel** ustawioną właściwość `Admin`.

Następujący fragment kodu Określa, czy zadanie powinno być uruchamiane na koncie użytkownika o nazwie. To konto użytkownika o nazwie został zdefiniowany w puli, podczas tworzenia puli. W takim przypadku utworzenia konta o nazwie użytkownika z uprawnieniami administratora:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Zaktualizuj kod do najnowszej biblioteki klienta usługi partia zadań

Wersja usługi partii 2017-01-01.4.0 wprowadzono istotne zmiany, zastępując **runElevated** właściwości dostępne w starszych wersjach z **tożsamości użytkownika** właściwości. Poniższe tabele zawierają proste mapowanie, które można zaktualizować kodu z wcześniejszych wersji biblioteki klienta.

### <a name="batch-net"></a>Batch .NET

| Jeśli korzysta z kodu...                  | Aby zaktualizować...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Nie określono | Aktualizacja nie jest wymagana                                                                                               |

### <a name="batch-java"></a>Java partii

| Jeśli korzysta z kodu...                      | Aby zaktualizować...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Nie określono | Aktualizacja nie jest wymagana                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Jeśli korzysta z kodu...                      | Aby zaktualizować...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated` Nie określono | Aktualizacja nie jest wymagana                                                                                                                                  |


## <a name="next-steps"></a>Kolejne kroki

### <a name="batch-forum"></a>Forum usługi partia zadań

[Forum usługi partia zadań Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch) w witrynie MSDN jest doskonałym miejscem do omówienia partii i zadać pytania dotyczące usługi. HEAD na za pośrednictwem przydatne Posty przypiętych i opublikuj swoje pytania powstałych podczas tworzenia własnych rozwiązań partii.