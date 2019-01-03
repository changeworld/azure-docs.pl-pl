---
title: Obliczeniowe elementy docelowe do trenowania modelu
titleSuffix: Azure Machine Learning service
description: Konfigurowanie środowisk szkolenia (celów obliczeń) dla szkolenie modelu uczenia maszynowego. Można łatwo przełączać się między środowiskami szkolenia. Rozpocznij szkolenie lokalnie. Jeśli musisz skalować w poziomie, przełącz się do elementu docelowego mocą obliczeniową opartą na chmurze.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794468"
---
# <a name="set-up-compute-targets-for-model-training"></a>Konfigurowanie celów obliczeń do trenowania modelu

Za pomocą usługi Azure Machine Learning możesz uczyć modelu w różnych zasobów obliczeniowych. Te zasoby, o nazwie obliczeniowe __celów obliczeń__, może być lokalne lub w chmurze. W tym artykule poznasz informacje dotyczące obsługiwanych obliczeniowych elementów docelowych i sposobu ich używania.

Cel obliczenia jest zasobem, w której uruchomiono skrypt szkoleniowy lub hostowane modelu po wdrożeniu go jako usługę sieci web. Można tworzyć i zarządzać obliczeniowego elementu docelowego przy użyciu zestawu SDK usługi Azure Machine Learning, witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Jeśli masz celów obliczeń utworzonych za pomocą innej usługi (na przykład klastra usługi Azure HDInsight), można użyć następujących elementów docelowych przez dołączenie do swojego obszaru roboczego usługi Azure Machine Learning.

Istnieją trzy szerokimi kategoriami obliczeniowych elementów docelowych, które obsługuje usługi Azure Machine Learning:

* __Lokalne__: Komputer lokalny lub oparte na chmurze maszynę wirtualną (VM) używanej jako środowiska tworzenia i eksperymentowania. 
* __Zarządzane obliczeń__: Usługa Azure obliczeniowego usługi Machine Learning jest obliczeń oferty, który jest zarządzany przez usługę Azure Machine Learning. Oferta umożliwia łatwe tworzenie jednego lub wielu węzłach obliczeniowych do szkolenia, testowania i wnioskowania usługi batch.
* __Dołączone obliczeń__: Możesz również przenieść obliczeń platformy Azure w chmurze i dołączyć go do usługi Azure Machine Learning. Dowiedz się więcej na temat obsługiwanych typów obliczeń i sposobu ich używania w poniższych sekcjach.


## <a name="supported-compute-targets"></a>Obsługiwane obliczeniowych elementów docelowych

Usługa Azure Machine Learning obsługuje różne w różnych lokalizacjach docelowych obliczeń. Cykl projektowania modelu Typowa rozpoczyna się od tworzenia i eksperymentów na niewielkiej ilości danych. Na tym etapie zaleca się, że używasz środowisku lokalnym komputera lokalnego lub maszyny Wirtualnej opartej na chmurze. Skalowanie w górę szkolenia na większych zestawów danych rozproszonego szkolenia, użyj usługi Azure Machine Learning obliczeniowe środowisko do utworzenia pojedynczego lub wieloma węzłami klastra tego skalowania każdorazowo możesz przesłać przebiegu. Można również dołączyć własnych zasobów obliczeniowych, mimo że pomocy technicznej dla różnych scenariuszy może różnić się, jak opisano w poniższej tabeli:

|Obliczeniowego elementu docelowego| Przyspieszanie procesora GPU | Automatyczne<br/> do strojenia hiperparametrycznego | Automatyczne</br> Uczenie maszynowe | Przyjazne potoku|
|----|:----:|:----:|:----:|:----:|
|[Komputer lokalny](#local)| Być może | &nbsp; | ✓ | &nbsp; |
|[Usługi Azure Machine Learning obliczeń](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Zdalnego maszyny Wirtualnej](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Usługa Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Usługa Azure Databricks i Azure Data Lake Analytics, należy używać tylko w potoku._<br/>
> Aby uzyskać więcej informacji na temat potoków, zobacz [potoki w usłudze Azure Machine Learning](concept-ml-pipelines.md).
>
> Środowiska obliczeniowego usługi Azure Machine Learning musi zostać utworzona z w obrębie obszaru roboczego. Nie można dołączyć istniejącego wystąpienia do obszaru roboczego.
>
> Innych celów obliczeń muszą być tworzone poza usługi Azure Machine Learning i następnie dołączone do obszaru roboczego.
>
> Ucząc model niektóre obliczeniowe elementy docelowe zależą od obrazów kontenerów platformy Docker. Obraz podstawowy procesor GPU musi być używana w usług platformy Microsoft Azure tylko. Do trenowania modelu usługi obejmują:
> * Usługi Azure Machine Learning obliczeń
> * Azure Kubernetes Service
> * Maszyna wirtualna do nauki o danych Windows (DSVM)

## <a name="workflow"></a>Przepływ pracy

Przepływ pracy do tworzenia i wdrażania modelu za pomocą usługi Azure Machine Learning obejmuje następujące kroki:

1. Tworzenie skryptów szkolenia w języku Python uczenia maszynowego.
1. Tworzenie i konfigurowanie obliczeniowego elementu docelowego lub dołączyć istniejący cel obliczenia.
1. Przesyłanie skryptów szkolenia, do obliczeniowego elementu docelowego.
1. Sprawdź wyniki, aby znaleźć najlepszy model.
1. W rejestrze modelu, należy zarejestrować model.
1. Wdróż model.

> [!NOTE]
> Skrypt szkolenia nie jest powiązane z określonym obliczeniowego elementu docelowego. Możesz początkowo uczenie się na komputerze lokalnym, a następnie przełącz celów obliczeń bez konieczności ponownego pisania skryptów szkolenia.
> 
> Jeśli cel obliczenia jest kojarzony z obszarem roboczym, tworząc zarządzanej obliczenia lub przez dołączenie istniejącego obliczeń, podaj nazwę, aby zasobów obliczeniowych. Nazwa powinna wynosić od 2 do 16 znaków.

Aby przełączyć się z jednego obliczeniowych elementów docelowych, musisz mieć [konfigurację uruchamiania](concept-azure-machine-learning-architecture.md#run-configuration). Konfiguracji uruchamiania definiuje sposób uruchamiania skryptu na obliczeniowego elementu docelowego.

## <a name="training-scripts"></a>Skryptów szkolenia

Po rozpoczęciu wykonywania szkolenia tworzy migawkę katalogu, który zawiera skrypty szkolenia i wysyła je do obliczeniowego elementu docelowego. Aby uzyskać więcej informacji, zobacz [migawek](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Komputer lokalny

Lokalnie ucząc używasz zestawu SDK do przesyłania operacji uczenia. Możesz uczyć się przy użyciu środowiska zarządzanego przez użytkownika lub przez system.

### <a name="user-managed-environment"></a>Środowiska zarządzanego przez użytkownika

W środowisku zarządzane przez użytkownika Upewnij się, że wszystkie niezbędne pakiety są dostępne w środowisku Python, gdzie możesz uruchomić skrypt. Poniższy fragment kodu znajduje się przykład sposobu konfigurowania szkolenie dotyczące środowiska usługi zarządzane przez użytkownika:

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>System zarządzany środowiska

System zarządzany środowisk zależy od conda Zarządzanie zależnościami. Conda tworzy plik o nazwie **conda_dependencies.yml** zawiera listę zależności. Możesz poprosić systemu do tworzenia nowego środowiska conda i uruchamiać skrypty istnieje. System zarządzany środowisk może nastąpić później, tak długo, jak plik conda_dependencies.yml pozostaje niezmieniony. 

Początkowej konfiguracji się nowego środowiska może potrwać kilka minut, na podstawie rozmiaru wymagane zależności. Poniższy fragment kodu przedstawia sposób tworzenia środowiska kontrolowany przez system, który zależy od scikit-Dowiedz się więcej:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Usługi Azure Machine Learning obliczeń

Usługa Azure obliczeniowego usługi Machine Learning jest infrastrukturę obliczeniową zarządzanego, który umożliwia użytkownikom łatwe tworzenie jednego lub wielu węzłach obliczeniowych. Obliczenia jest tworzony w regionie Twojego obszaru roboczego jako zasób, które mogą być udostępniane innym użytkownikom w obszarze roboczym. Obliczenia jest skalowany w górę automatycznie podczas przesyłania zadania, a, można umieścić w sieci wirtualnej platformy Azure. Obliczenia wykonuje w środowisku konteneryzowanych i pakiety zależności model w kontenerze platformy Docker.

Aby rozdystrybuować procesu uczenia klastra procesora CPU lub GPU węzłów obliczeniowych w chmurze, można użyć obliczeniowego usługi Azure Machine Learning. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, które zawierają procesorów GPU, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Usługa Azure obliczeniowego usługi Machine Learning ma limity domyślne, takie jak liczba rdzeni, które mogą być przydzielone. Aby uzyskać więcej informacji, zobacz [zarządzanie i żądania przydziały dla zasobów platformy Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Na żądanie, aby zaplanować uruchomienie lub jako trwałe zasobów, można utworzyć środowiska obliczeniowego usługi Azure Machine Learning.

### <a name="run-based-creation"></a>Tworzenie opartych na przebieg

Możesz utworzyć środowiska obliczeniowego usługi Azure Machine Learning jako cel obliczenia w czasie wykonywania. Zasoby obliczeniowe są tworzone automatycznie dla przebieg i jest skalowany w górę do liczby **max_nodes** określoną w pliku config wykonywania. Zasoby obliczeniowe zostanie automatycznie usunięte po ukończeniu uruchomienia.

> [!IMPORTANT]
> Tworzenie na podstawie wykonywania środowiska obliczeniowego usługi Azure Machine Learning jest obecnie w wersji zapoznawczej. Nie należy używać tworzenie na podstawie wykonywania, jeśli jest używany, strojenia hiperparametrycznego zautomatyzowane lub zautomatyzowane uczenia maszynowego. Służące do strojenia hiperparametrycznego lub zautomatyzowane uczenia maszynowego, należy utworzyć środowiska obliczeniowego usługi Azure Machine Learning, przed przesłaniem przebiegu.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Trwałe obliczeniowe: Podstawowa

Stałe środowiska obliczeniowego usługi Azure Machine Learning można można używać we wszystkich zadań. Zasoby obliczeniowe mogą być udostępniane innym użytkownikom w obszarze roboczym i są przechowywane między zadaniami.

Aby utworzyć trwałe zasobu środowiska obliczeniowego usługi Azure Machine Learning, należy określić **vm_size** i **max_nodes** właściwości. Usługa Azure Machine Learning następnie używa inteligentnych wartości domyślnych dla innych właściwości. Skalowania obliczeń do zera węzłów, gdy nie jest używany i tworzy dedykowane maszyny wirtualne do uruchamiania zadań, zgodnie z potrzebami. 

* **vm_size**: Rodzina maszyn wirtualnych węzłów utworzone przez obliczeniowego usługi Azure Machine Learning.
* **max_nodes**: Maksymalna liczba węzłów w celu automatycznego skalowania do uruchomienia zadania w obliczeniowego usługi Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Trwałe obliczeniowe: Advanced

Można również skonfigurować kilka właściwości zaawansowane, podczas tworzenia środowiska obliczeniowego usługi Azure Machine Learning. Właściwości umożliwiają tworzenie trwałych klastra o stałym rozmiarze lub w ramach istniejącej sieci wirtualnej platformy Azure w ramach subskrypcji.

Wraz z **vm_size** i **max_nodes** właściwości, można także użyć następujących właściwości:

* **min_nodes**: Minimalna liczba węzłów, które można skalować w dół do uruchamiania zadania na środowiska obliczeniowego usługi Azure Machine Learning. Minimalne domyślną jest zero (0) węzłów.
* **vm_priority**: Typ maszyny Wirtualnej do użycia podczas tworzenia zasobu środowiska obliczeniowego usługi Azure Machine Learning. Wybieranie między **dedykowanych** (ustawienie domyślne) i **lowpriority**. Maszyny wirtualne o niskim priorytecie używać nadmiarowej pojemności na platformie Azure. Te maszyny wirtualne są tańsze, ale przebiegi można pre-empted, gdy są używane te maszyny wirtualne.
* **idle_seconds_before_scaledown**: Kwota bezczynności czas oczekiwania po zakończeniu przebiegu i przed automatyczne skalowanie do liczby **min_nodes**. Czas bezczynności, po domyślna to 120 sekund.
* **vnet_resourcegroup_name**: Grupa zasobów __istniejących__ sieci wirtualnej. Środowiska obliczeniowego usługi Azure Machine Learning jest tworzony w tej sieci wirtualnej.
* **vnet_name**: Nazwa sieci wirtualnej. Sieć wirtualna musi znajdować się w tym samym regionie, co Twój obszar roboczy usługi Azure Machine Learning.
* **subnet_name**: Nazwa podsieci w sieci wirtualnej. Zasoby środowiska obliczeniowego usługi Azure Machine Learning są przypisywane adresy IP z tego zakresu podsieci.

> [!TIP]
> Podczas tworzenia trwałe zasobu środowiska obliczeniowego usługi Azure Machine Learning można zaktualizować właściwości, takie jak liczba **min_nodes** lub **max_nodes**. Aby zaktualizować odnośną właściwość, należy wywołać `update()` funkcji dla właściwości.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>Zdalnego maszyny Wirtualnej

Usługa Azure Machine Learning obsługuje również wprowadzenie własnych zasobów obliczeniowych i dołączania ich do obszaru roboczego. Jednego takiego typu zasobu jest dowolnego zdalnej maszynie Wirtualnej, tak długo, jak jest ona dostępna z usługi Azure Machine Learning. Zasób może być maszynie Wirtualnej platformy Azure, na serwerze zdalnym w organizacji lub w środowisku lokalnym. W szczególności podany adres IP i poświadczenia (nazwę użytkownika i hasło lub klucz SSH), używając wszystkie dostępne maszyny Wirtualnej dla przebiegów zdalnych.
Można użyć środowiska conda tworzonym przez system, istniejącego środowiska Python lub kontenera Docker. Podczas wykonywania za pomocą kontenera platformy Docker, musisz mieć aparat platformy Docker, uruchomione na maszynie Wirtualnej. Zdalnego funkcjonalność maszyny Wirtualnej jest szczególnie przydatne w przypadku, gdy chcesz oparte na chmurze środowiska tworzenia i eksperymentowania, jest bardziej elastyczny niż komputer lokalny.

> [!TIP]
> Użyj maszyny DSVM jako maszyny Wirtualnej platformy Azure wybranym dla tego scenariusza. Ta maszyna wirtualna jest do wydobywania danych wstępnie skonfigurowanych i Środowisko deweloperskie sztucznej Inteligencji w maszynie Wirtualnej platformy Azure oferuje zestaw wyselekcjonowanych narzędzi i platform maszyny pełnego cyklu programowania uczenia. Aby uzyskać więcej informacji na temat maszyny DSVM za pomocą usługi Azure Machine Learning, zobacz [skonfigurować środowisko programowania](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne z systemami Ubuntu. Podczas tworzenia maszyny Wirtualnej lub wybierz istniejącą maszynę Wirtualną, należy wybrać maszynę Wirtualną, która korzysta z systemem Ubuntu.

Poniższe kroki konfigurowania maszyny wirtualnej DSVM jako cel szkolenia przy użyciu zestawu SDK:

1. Aby dołączyć istniejącą maszynę wirtualną jako cel obliczenia, należy podać w pełni kwalifikowaną nazwę domeny (FQDN), nazwę użytkownika i hasło dla maszyny wirtualnej. W tym przykładzie Zastąp \<fqdn > za pomocą publiczny adres FQDN maszyny Wirtualnej lub publiczny adres IP. Zastąp \<username > i \<hasło > za pomocą nazwy użytkownika SSH i hasło dla maszyny Wirtualnej.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Utwórz konfigurację dla elementu docelowego obliczeniowe DSVM. Platforma docker i narzędzia conda są używane do tworzenia i konfigurowania środowiska szkolenia na maszyny DSVM.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Usługa Azure Databricks

Usługa Azure Databricks to oparta na platformie Apache Spark środowisko w chmurze platformy Azure. Środowisko może służyć jako cel obliczenia, gdy uczenie modeli przy użyciu potoku usługi Azure Machine Learning.

> [!IMPORTANT]
> Usługa Azure Databricks obliczeniowego elementu docelowego należy używać tylko w potoku uczenia maszynowego.
>
> Zanim użyjesz do nauczenia modelu, należy utworzyć obszar roboczy usługi Azure Databricks. Aby utworzyć tych zasobów, zobacz [uruchamianie zadania Spark w usłudze Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Aby dołączyć usługi Azure Databricks, jako cel obliczenia, możesz użyć zestawu SDK usługi Azure Machine Learning i podaj następujące informacje:

* __Nazwa obliczeniowego__: Nazwa do przypisania do tego zasobu obliczeniowego.
* __Nazwa obszaru roboczego usługi Databricks__: Nazwa obszaru roboczego usługi Azure Databricks.
* __Token dostępu__: Token dostępu używany do uwierzytelniania usługi Azure Databricks. Aby wygenerować token dostępu, zobacz [uwierzytelniania](https://docs.azuredatabricks.net/api/latest/authentication.html).

Poniższy kod przedstawia sposób dołączania usługi Azure Databricks, jako cel obliczenia:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create the attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Usługa Azure Data Lake Analytics to platforma analizy danych big data w chmurze platformy Azure. Platforma może służyć jako cel obliczenia, gdy uczenie modeli przy użyciu potoku usługi Azure Machine Learning.

> [!IMPORTANT]
> Azure Data Lake Analytics obliczeniowego elementu docelowego należy używać tylko w potoku uczenia maszynowego.
>
> Zanim użyjesz do nauczenia modelu, musisz utworzyć konto usługi Azure Data Lake Analytics. Aby utworzyć ten zasób, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Aby dołączyć usługi Data Lake Analytics, jako cel obliczenia, możesz użyć zestawu SDK usługi Azure Machine Learning i podaj następujące informacje:

* __Nazwa obliczeniowego__: Nazwa do przypisania do tego zasobu obliczeniowego.
* __Grupa zasobów__: Grupy zasobów zawierającej konto usługi Data Lake Analytics.
* __Nazwa konta__: Nazwa konta usługi Data Lake Analytics.

Poniższy kod przedstawia sposób dołączania usługi Data Lake Analytics, jako cel obliczenia:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Usługa Azure Machine Learning potoków tylko pracę z danymi, które są przechowywane w magazynie danych domyślnego konta usługi Data Lake Analytics. Jeśli dane, których potrzebujesz, znajduje się w magazynie innych niż domyślne, możesz użyć [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) operację, aby skopiować dane, zanim uczenie modelu.

## <a id="hdinsight"></a>Usługa Azure HDInsight 

Usługa Azure HDInsight to popularne platformy do analizy danych big data. Ta platforma udostępnia platformę Apache Spark, która może służyć do uczenia modelu.

> [!IMPORTANT]
> Zanim użyjesz do nauczenia modelu, należy utworzyć klaster HDInsight. Aby utworzyć platformy Spark w klastrze HDInsight, zobacz [Tworzenie klastra Spark w HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Podczas tworzenia klastra, należy określić nazwę użytkownika SSH i hasło. Zanotuj te wartości w miarę potrzeb do użycia HDInsight jako cel obliczenia.
>
> Po utworzeniu klastra ma nazwę FQDN \<nazwa_klastra >. azurehdinsight.net, gdzie \<clustername > to nazwa, podane dla klastra. Wymagany jest adres FQDN (lub publiczny adres IP klastra) jest używany klaster jako cel obliczenia.

Aby skonfigurować HDInsight jako cel obliczenia, należy podać nazwę FQDN, nazwy użytkownika i hasło dla klastra HDInsight. W poniższym przykładzie użyto zestawu SDK, aby dołączyć klaster z obszarem roboczym. W tym przykładzie Zastąp \<fqdn > za pomocą publiczny adres FQDN klastra lub publiczny adres IP. Zastąp \<username > i \<hasło > za pomocą nazwy użytkownika SSH i hasło klastra.

> [!NOTE]
> Aby znaleźć nazwę FQDN dla klastra, przejdź do witryny Azure portal, a następnie wybierz klaster usługi HDInsight. W obszarze __Przegląd__, nazwę FQDN można zobaczyć __adresu URL__ wpisu. Aby uzyskać nazwę FQDN, Usuń https:\// prefiksu na początku wybrać odpowiedni wpisu.

![Uzyskaj nazwę FQDN dla klastra usługi HDInsight w witrynie Azure portal](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Przesyłanie przebiegów szkoleniowych

Istnieją dwa sposoby, aby przesłać przebiegu szkolenia:

* Prześlij szkolenia, uruchom za pomocą `ScriptRunConfig` obiektu.
* Prześlij szkolenia, uruchom za pomocą `Pipeline` obiektu.

> [!IMPORTANT]
> Usługa Azure Databricks i Azure Datalake Analytics obliczeniowe elementy docelowe należy używać tylko w potoku.
>
> Nie można użyć lokalnego obliczeniowego elementu docelowego w potoku.

### <a name="scriptrunconfig-object"></a>Obiekt ScriptRunConfig

Wzorca kodu, aby przesłać szkoleniowe uruchamianie za pomocą `ScriptRunConfig` obiekt jest taki sam dla wszystkich typów obliczeniowych elementów docelowych:

1. Utwórz `ScriptRunConfig` obiektu za pomocą konfiguracji uruchamiania dla obliczeniowego elementu docelowego.
1. Prześlij przebiegu.
1. Poczekaj, aż działanie zakończyć.

W poniższym przykładzie użyto konfiguracji docelowego kontrolowany przez system lokalny obliczeniowe utworzone wcześniej:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Potok obiektów

Wzorca kodu, aby przesłać szkoleniowe uruchamianie za pomocą `Pipeline` obiekt jest taki sam dla wszystkich typów obliczeniowych elementów docelowych:

1. Dodaj krok, aby `Pipeline` obiektu dla zasobów obliczeniowych.
1. Przesłać przebiegu przy użyciu potoku.
1. Poczekaj, aż działanie zakończyć.

W poniższym przykładzie użyto usługi Azure Databricks obliczeniowego elementu docelowego utworzonego wcześniej:

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Aby uzyskać więcej informacji na temat potoków uczenia maszynowego, zobacz [potoków i Azure Machine Learning](concept-ml-pipelines.md).

Na przykład zobacz Notesy programu Jupyter, które pokazują, jak i wytrenuj model przy użyciu potoku [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Dostęp oblicza się w witrynie Azure portal

Możesz uzyskać dostęp do celów obliczeń, które są skojarzone z obszarem roboczym w witrynie Azure portal. 

### <a name="view-compute-targets"></a>Wyświetl obliczeniowych elementów docelowych

Aby zobaczyć obliczeniowych elementów docelowych dla obszaru roboczego, użyj następujących kroków:

1. Przejdź do [witryny Azure portal](https://portal.azure.com) , a następnie otwórz obszar roboczy.
1. W obszarze __aplikacje__, wybierz opcję __obliczenia__.

    ![Wyświetl obliczeniowych elementów docelowych](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Utworzyć cel obliczenia

Wykonaj poprzednie kroki, aby wyświetlić listę obliczeniowych elementów docelowych. Następnie wykonaj następujące kroki, aby utworzyć cel obliczenia:

1. Wybierz znak Plus (+), aby dodać obliczeniowego elementu docelowego.

    ![Dodaj cel obliczenia](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Wprowadź nazwę dla obliczeniowego elementu docelowego.
1. Wybierz **obliczeniowego usługi Machine Learning** jako typ obliczeń na potrzeby __szkolenia__.

    > [!IMPORTANT]
    > Środowiska obliczeniowego usługi Azure Machine Learning można tworzyć tylko jako zasób obliczeniowy zarządzane do trenowania.

1. Wypełnij formularz. Podanie wartości wymaganych właściwości szczególnie **rodziny maszyn wirtualnych**i **maksymalna liczba węzłów** do użycia w celu uruchomienia obliczeń. 
1. Wybierz pozycję __Utwórz__.
1. Wyświetlanie stanu operacji tworzenia, wybierając obliczeniowego elementu docelowego z listy:

    ![Wybierz cel obliczenia, aby wyświetlić stan operacji create](./media/how-to-set-up-training-targets/View_list.png)

1. Następnie zobaczysz szczegółowe informacje dotyczące obliczeniowego elementu docelowego:

    ![Wyświetl szczegóły komputera docelowego](./media/how-to-set-up-training-targets/compute-target-details.png)

Możesz teraz przesłać przebiegu zgodnie z docelowymi wartościami komputera, zgodnie z wcześniejszym opisem.


### <a name="reuse-existing-compute-targets"></a>Ponowne używanie istniejących celów obliczeń

Wykonaj kroki opisane wcześniej, aby wyświetlić listę obliczeniowych elementów docelowych. Następnie wykonaj następujące kroki, aby ponownie użyć obliczeniowego elementu docelowego:

1. Wybierz znak Plus (+), aby dodać obliczeniowego elementu docelowego.
1. Wprowadź nazwę dla obliczeniowego elementu docelowego.
1. Wybierz typ obliczenia, które można dołączyć do __szkolenia__:

    > [!IMPORTANT]
    > Nie wszystkie obliczenia typy mogą być dołączane w witrynie Azure portal.
    > Typy obliczeń, które mogą być obecnie dołączone do trenowania:
    >
    > * Maszyny Wirtualnej z systemem zdalnym
    > * Azure Databricks
    > * Azure Data Lake Analytics
    > * Azure HDInsight

1. Wypełnij formularz i podaj wartości wymagane właściwości.

    > [!NOTE]
    > Firma Microsoft zaleca używanie kluczy SSH, które są bezpieczniejsze niż hasła. Hasła są narażone na ataki siłowe. Klucze SSH, zależą od podpisy kryptograficzne. Aby uzyskać informacje na temat tworzenia kluczy SSH do użycia z usługą Azure Virtual Machines zobacz następujące dokumenty:
    >
    > * [Tworzenie i używanie kluczy SSH w systemie Linux lub macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Tworzenie i używanie kluczy SSH w Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Wybierz __dołączyć__.
1. Wyświetl stan operacji dołączania, wybierając obliczeniowego elementu docelowego z listy.

Teraz możesz przesłać celów obliczeń w przebiegu względem tych zgodnie z wcześniejszym opisem.

## <a name="notebook-examples"></a>Przykłady notesu

Przykłady można znaleźć w notesach w następujących lokalizacjach:

* [jak-to-użyj-usługi Azure ml/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Samouczki/img klasyfikacji — część 1 — training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja usługi Azure Machine Learning w zestawie SDK](https://aka.ms/aml-sdk)
* [Samouczek: trenowanie modelu](tutorial-train-models-with-aml.md)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Tworzenie potoków uczenia maszynowego przy użyciu usługi Azure Machine Learning](concept-ml-pipelines.md)
