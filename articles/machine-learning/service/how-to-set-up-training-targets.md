---
title: Konfigurowanie celów obliczeń do trenowania modelu za pomocą usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać i skonfigurować środowisk szkolenia (celów obliczeń) używane do trenowania modeli uczenia maszynowego. Usługa Azure Machine Learning pozwala na łatwe szkolenie przełącznikami. Rozpocznij szkolenie lokalnie, a jeśli musisz skalować w poziomie, przełącz się do elementu docelowego mocą obliczeniową opartą na chmurze.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 07ea61ffe3ffc17cd255b826e3506ffe2b1ce9cd
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017726"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Wybierz, a następnie użyć obliczeniowego elementu docelowego do nauczenia modelu

Za pomocą usługi Azure Machine Learning możesz uczyć modelu w różnych zasobów obliczeniowych. Te zasoby, o nazwie obliczeniowe __celów obliczeń__, może być lokalne lub w chmurze. W tym dokumencie zawiera informacje o obsługiwanych obliczeniowych elementów docelowych i sposobu ich używania.

Cel obliczenia jest zasobów, w którym jest uruchamiany skrypt szkolenia lub model jest hostowana po wdrożeniu jako usługę sieci web. Można tworzyć i zarządzać obliczeniowego elementu docelowego przy użyciu zestawu SDK usługi Azure Machine Learning, witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Jeśli masz obliczeniowych elementów docelowych, które zostały utworzone za pomocą innej usługi (na przykład klastra usługi HDInsight), możesz ich używać, dołączanie ich do obszaru roboczego usługi Azure Machine Learning.

Istnieją trzy szerokimi kategoriami obliczeniowych elementów docelowych, które obsługuje usługi Azure Machine Learning:

* __Lokalne__: komputer lokalny lub oparte na chmurze maszyny Wirtualnej używanej jako Środowisko deweloperskie/eksperymentowania. 

* __Zarządzane obliczeń__: obliczeniowego usługi Azure Machine Learning jest obliczeń oferty, który jest zarządzany przez usługę Azure Machine Learning. Umożliwia łatwe tworzenie jednego lub wielu node zasoby obliczeniowe na potrzeby szkoleń, testowania i wnioskowania usługi batch.

* __Dołączone obliczeń__: Możesz również przenieść obliczeń platformy Azure w chmurze i dołączyć go do usługi Azure Machine Learning. Dowiedz się więcej, poniżej dla typów obliczeń obsługiwanych i sposobu ich używania.


## <a name="supported-compute-targets"></a>Obsługiwane obliczeniowych elementów docelowych

Usługa Azure Machine Learning obsługuje różne w różnych lokalizacjach docelowych obliczeń. Cykl projektowania modelu Typowa rozpoczyna się od dev/eksperymentów na niewielkiej ilości danych. Na tym etapie firma Microsoft zaleca używanie środowiska lokalnego. Na przykład komputera lokalnego lub maszyny Wirtualnej opartej na chmurze. Skalowanie w górę szkolenia na większych zestawów danych lub czy rozproszonego szkolenia, firma Microsoft zaleca obliczeniowego usługi Azure Machine Learning Tworzenie klastra przy użyciu jednego lub wielu node tego skalowania każdej próbie przesłania przebiegu. Można również dołączyć własnych zasobów obliczeniowych, mimo że obsługi różnych scenariuszy mogą się różnić zależnie z poniższym opisem:

|Obliczeniowego elementu docelowego| Przyspieszanie procesora GPU | Do strojenia hiperparametrycznego automatycznych | Zautomatyzowane uczenie maszynowe | Przyjazne potoku|
|----|:----:|:----:|:----:|:----:|
|[Komputer lokalny](#local)| Być może | &nbsp; | ✓ | &nbsp; |
|[Usługi Azure Machine Learning obliczeń](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Zdalnego maszyny Wirtualnej](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Usługa Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Usługa Azure Databricks i Azure Data Lake Analytics może __tylko__ można używać w potoku. Aby uzyskać więcej informacji na temat potoków, zobacz [potoki w usłudze Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

> [!IMPORTANT]
> Usługa Azure obliczeniowego usługi Machine Learning musi zostać utworzona z w obrębie obszaru roboczego. Nie można dołączyć istniejącego wystąpienia do obszaru roboczego.
>
> Innych celów obliczeń muszą być tworzone poza usługi Azure Machine Learning i następnie dołączone do obszaru roboczego.

> [!NOTE]
> Niektóre obliczeniowe elementy docelowe zależą od obrazów kontenerów platformy Docker podczas uczenia modelu. Obraz podstawowy procesor GPU musi być używana w usług platformy Microsoft Azure tylko. Do trenowania modelu, te usługi są:
>
> * Usługi Azure Machine Learning obliczeń
> * Azure Kubernetes Service
> * Maszyna wirtualna do nauki o danych.

## <a name="workflow"></a>Przepływ pracy

Przepływ pracy do tworzenia i wdrażania modelu za pomocą usługi Azure Machine Learning obejmuje następujące kroki:

1. Twórz usługi machine learning szkolenia skryptów w języku Python.
1. Tworzenie i konfigurowanie lub dołączyć istniejący cel obliczenia.
1. Przesyłanie skryptów szkolenia, do obliczeniowego elementu docelowego.
1. Sprawdź wyniki, aby znaleźć najlepszy model.
1. W rejestrze modelu, należy zarejestrować model.
1. Wdróż model.

> [!IMPORTANT]
> Skrypt szkolenia nie jest powiązane z określonym obliczeniowego elementu docelowego. Możesz początkowo uczenie się na komputerze lokalnym, a następnie przełącz celów obliczeń bez konieczności ponownego pisania skryptów szkolenia.

> [!TIP]
> Zawsze, gdy cel obliczenia jest kojarzona z obszaru roboczego, tworząc zarządzane obliczenia lub dołączenie istniejącego obliczeń, musisz podać nazwę zasobów obliczeniowych. To może zawierać od 2 do 16 znaków.

Przełączenie z jednej obliczeniowego elementu docelowego na inny obejmuje utworzenie [konfigurację uruchamiania](concept-azure-machine-learning-architecture.md#run-configuration). Konfiguracji uruchamiania definiuje sposób uruchamiania skryptu na obliczeniowego elementu docelowego.

## <a name="training-scripts"></a>Skryptów szkolenia

Po rozpoczęciu wykonywania szkolenia migawkę do katalogu zawierającego skryptów szkolenia jest tworzony i wysyłane do obliczeniowego elementu docelowego. Aby uzyskać więcej informacji, zobacz [migawek](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Komputer lokalny

Podczas szkolenia lokalnie, używasz zestawu SDK do przesyłania operacji uczenia. Możesz uczyć przy użyciu środowiska zarządzanego przez użytkownika lub przez system.

### <a name="user-managed-environment"></a>Środowiska zarządzanego przez użytkownika

W środowisku zarządzane przez użytkownika odpowiedzialność za zapewnienie, że wszystkie niezbędne pakiety są dostępne w środowisku Python, który chcesz uruchomić skrypt w. Poniższy fragment kodu znajduje się przykład konfiguracji szkolenie dotyczące środowiska usługi zarządzane przez użytkownika:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>System zarządzany środowiska

System zarządzany środowisk zależy od conda Zarządzanie zależnościami. Conda tworzy plik o nazwie `conda_dependencies.yml` zawiera listę zależności. Następnie możesz poprosić systemu do tworzenia nowego środowiska conda i uruchamiać skrypty w nim. System zarządzany środowiska mogą być później ponownie, tak długo, jak `conda_dependencies.yml` pliki pozostaje bez zmian. 

Początkowej konfiguracji się nowego środowiska może potrwać kilka minut, w zależności od rozmiaru wymagane zależności. Poniższy fragment kodu przedstawia tworzenie środowiska usługi zarządzane przez system, który jest zależny od scikit-Dowiedz się więcej:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Usługi Azure Machine Learning obliczeń

Usługa Azure obliczeniowego usługi Machine Learning jest infrastrukturę obliczeniową zarządzanego, który umożliwia użytkownikowi łatwo utworzyć pojedynczego — do wielu - node obliczeń. Jest on tworzony __w regionie Twojego obszaru roboczego__ i zasobów, która może być współużytkowana z innymi użytkownikami w obszarze roboczym. Skaluje się automatycznie, gdy zadanie jest przesyłane i można umieścić w usłudze Azure Virtual Network. Wykonuje w __konteneryzowanych środowiska__, pakowanie zależności swój model w kontenerze platformy Docker.

Aby rozdystrybuować procesu uczenia klastra procesora CPU lub GPU węzłów obliczeniowych w chmurze, można użyć obliczeniowego usługi Azure Machine Learning. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, które zawierają procesorów GPU, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) dokumentacji.

> [!NOTE]
> Azure obliczeniowego usługi Machine Learning ma limity domyślne elementów, takich jak liczba rdzeni, które mogą być przydzielone. Aby uzyskać więcej informacji, zobacz [zarządzanie i żądania przydziały dla zasobów platformy Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas) dokumentu.

Można utworzyć obliczeniowego usługi Azure Machine Learning na żądanie, aby zaplanować uruchomienie lub jako trwałe zasobu.

### <a name="run-based-creation"></a>Tworzenie opartych na przebieg

Można utworzyć obliczeniowego usługi Azure Machine Learning jako cel obliczenia w czasie wykonywania. W takim przypadku zasoby obliczeniowe są tworzone automatycznie dla użytkownika Uruchom następujący skrypt, można skalować do max_nodes określoną w pliku config wykonywania i jest następnie __usunięte automatycznie__ po ukończeniu uruchomienia.

> [!IMPORTANT]
> Tworzenie na podstawie wykonywania mocy obliczeniowej usługi Azure Machine Learning jest obecnie w stanie wersji zapoznawczej. Tworzenie na podstawie wykonywania nie należy używać, jeśli używasz do strojenia Hiperparametrycznego lub zautomatyzowane uczenia maszynowego. Jeśli musisz użyć do strojenia Hiperparametrycznego lub zautomatyzowane uczenia maszynowego, Utwórz obliczeniowych usługi Azure Machine Learning przed przesłaniem przebiegu.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Trwałe obliczeń (Basic)

Trwałe Azure obliczeniowego usługi Machine Learning mogą być ponownie używane w wielu zadaniach. Mogą być udostępniane innym użytkownikom w obszarze roboczym i są przechowywane między zadaniami.

Aby utworzyć trwałe zasobu obliczeniowego usługi Azure Machine Learning, należy określić `vm_size` i `max_nodes` parametrów. Usługa Azure Machine Learning następnie używa inteligentnych wartości domyślnych dla pozostałych parametrów.  Na przykład zasoby obliczeniowe ustawiono automatyczne skalowanie w dół, aby zero węzłów, gdy nie jest używany i tworzenia dedykowanych maszyn wirtualnych do uruchamiania zadań, zgodnie z potrzebami. 

* **vm_size**: rodziny maszyn wirtualnych węzłów utworzone przez obliczeniowego usługi Azure Machine Learning.
* **max_nodes**: Maksymalna liczba węzłów w celu automatycznego skalowania podczas uruchamiania zadania na obliczeniowego usługi Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Trwałe obliczeń (zaawansowane)

Można również skonfigurować kilka właściwości zaawansowane, tworząc obliczeniowego usługi Azure Machine Learning.  Właściwości te umożliwiają tworzenie trwałych klastra o stałym rozmiarze lub w ramach istniejącej sieci wirtualnej platformy Azure w ramach subskrypcji.

Oprócz `vm_size` i `max_nodes`, można użyć następujących właściwości:

* **min_nodes**: co najmniej węzłów (domyślnie 0 węzłów) można skalować w dół w trakcie uruchamiania zadania na obliczeniowego usługi Azure Machine Learning.
* **vm_priority**: Wybieranie między "dedykowane" (ustawienie domyślne) i "lowpriority" maszyny wirtualne, tworząc obliczeniowego usługi Azure Machine Learning. Maszyn wirtualnych z niskim priorytetem Użyj nadmiarowej pojemności platformy Azure i dlatego są tańsze, ale o podwyższonym ryzyku są wyparte przebieg.
* **idle_seconds_before_scaledown**: czas bezczynności (ustawienie domyślne 120 sekund) oczekiwania po zakończeniu wykonywania przed skalowania automatycznego w celu min_nodes.
* **vnet_resourcegroup_name**: Grupa zasobów __istniejących__ sieci wirtualnej. Usługa Azure obliczeniowego usługi Machine Learning jest tworzony w tej sieci wirtualnej.
* **vnet_name**: Nazwa sieci wirtualnej. Sieć wirtualna musi znajdować się w tym samym regionie, co Twój obszar roboczy usługi Azure Machine Learning.
* **subnet_name**: Nazwa podsieci w sieci wirtualnej. Zasoby platformy Azure obliczeniowego usługi Machine Learning zostaną przypisane adresy IP z tego zakresu podsieci.

> [!TIP]
> Po utworzeniu trwałe zasobu obliczeniowego usługi Azure Machine Learning istnieje również możliwość aktualizowania jego właściwości, takie jak min_nodes lub max_nodes. Po prostu Wywołaj `update()` funkcji dla niego.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
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

Usługa Azure Machine Learning obsługuje również wprowadzenie własnych zasobów obliczeniowych i dołączania ich do obszaru roboczego. Jeden taki typ zasobu jest dowolnego zdalnej maszynie Wirtualnej, tak długo, jak jest ona dostępna z usługi Azure Machine Learning. Możliwe, serwera zdalnego w organizacji lub w środowisku lokalnym lub Maszynie wirtualnej platformy Azure. W szczególności podany adres IP i poświadczenia (nazwę użytkownika/hasło lub klucz SSH), można użyć dowolnej dostępne maszyny Wirtualnej dla przebiegów zdalnych.
Można użyć środowiska conda tworzonym przez system, już istniejące środowisko Python lub kontenera Docker. Wykonania za pomocą kontenera platformy Docker wymaga aparatu platformy Docker, uruchomione na maszynie Wirtualnej. Ta funkcja jest szczególnie przydatne w przypadku, gdy chcesz środowiska bardziej elastyczne, oparte na chmurze dev/eksperymentowanie w usłudze niż komputer lokalny.

> [!TIP]
> Zalecane jest używanie maszyny wirtualnej do nauki o danych jako maszyny Wirtualnej platformy Azure wybranym w tym scenariuszu. Jest do nauki o danych wstępnie skonfigurowanych i sztucznej Inteligencji środowiska programowania na platformie Azure dzięki szerokiemu wyborowi wyselekcjonowanych narzędzi i platform, aby uzyskać pełny cykl życia programowania uczenia Maszynowego. Aby uzyskać więcej informacji na temat korzystania z maszyny wirtualnej do nauki o danych z usługi Azure Machine Learning, zobacz [skonfigurować środowisko programowania](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm) dokumentu.

> [!WARNING]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu. Podczas tworzenia maszyny wirtualnej lub wybierz istniejącą, należy wybrać jedną, która używa systemu Ubuntu.

Poniższe kroki konfigurowania maszyn wirtualnych do nauki o danych (DSVM) jako cel szkolenia przy użyciu zestawu SDK:

1. Aby dołączyć istniejącą maszynę wirtualną jako cel obliczenia, należy podać w pełni kwalifikowaną nazwę domeny, nazwa logowania i hasło dla maszyny wirtualnej.  W tym przykładzie Zastąp ```<fqdn>``` z publicznych w pełni kwalifikowaną nazwą domeny maszyny Wirtualnej lub publiczny adres IP. Zastąp ```<username>``` i ```<password>``` przy użyciu konta użytkownika SSH i hasło dla maszyny Wirtualnej:

    ```python
    from azureml.core.compute import RemoteCompute

    dsvm_compute = RemoteCompute.attach(ws,
                                    name="attach-dsvm",
                                    username='<username>',
                                    address="<fqdn>",
                                    ssh_port=22,
                                    password="<password>")

    dsvm_compute.wait_for_completion(show_output=True)

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Usługa Azure Databricks

Usługa Azure Databricks to oparta na platformie Apache Spark środowisko w chmurze platformy Azure. Może służyć jako cel obliczenia podczas uczenia modeli za pomocą potoku usługi Azure Machine Learning.

> [!IMPORTANT]
> Usługa Azure Databricks obliczeniowego elementu docelowego należy używać tylko w potoku uczenia maszynowego.
>
> Przed użyciem w celu nauczenia modelu, należy utworzyć obszar roboczy usługi Azure Databricks. Aby utworzyć tych zasobów, zobacz [uruchamianie zadania Spark w usłudze Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentu.

Aby dołączyć usługi Azure Databricks, jako cel obliczenia, możesz użyć zestawu SDK usługi Azure Machine Learning i podaj następujące informacje:

* __Nazwa obliczeniowego__: nazwa, którą chcesz przypisać do tego zasobu obliczeniowego.
* __Identyfikator zasobu__: identyfikator zasobu obszaru roboczego usługi Azure Databricks. Następujący tekst jest przykładem formatu dla tej wartości:

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.Databricks/workspaces/<databricks-workspace-name>
    ```

    > [!TIP]
    > Aby uzyskać identyfikator zasobu, użyj następującego polecenia wiersza polecenia platformy Azure. Zastąp `<databricks-ws>` nazwą obszaru roboczego usługi Databricks:
    > ```azurecli-interactive
    > az resource list --name <databricks-ws> --query [].id
    > ```

* __Token dostępu__: token dostępu używany do uwierzytelniania usługi Azure Databricks. Aby wygenerować token dostępu, zobacz [uwierzytelniania](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentu.

Poniższy kod przedstawia sposób dołączania usługi Azure Databricks, jako cel obliczenia:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_resource_id = os.environ.get("AML_DATABRICKS_RESOURCE_ID", "<databricks_resource_id>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_resource_id {}'.format(databricks_resource_id))
    print('databricks_access_token {}'.format(databricks_access_token))
    databricks_compute = DatabricksCompute.attach(
             workspace=ws,
             name=databricks_compute_name,
             resource_id=databricks_resource_id,
             access_token=databricks_access_token
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Usługa Azure Data Lake Analytics to platforma analiz danych big data w chmurze platformy Azure. Może służyć jako cel obliczenia podczas uczenia modeli za pomocą potoku usługi Azure Machine Learning.

> [!IMPORTANT]
> Azure Data Lake Analytics obliczeniowego elementu docelowego należy używać tylko w potoku uczenia maszynowego.
>
> Przed użyciem w celu nauczenia modelu, musisz utworzyć konto usługi Azure Data Lake Analytics. Aby utworzyć ten zasób, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentu.

Aby dołączyć usługi Data Lake Analytics, jako cel obliczenia, możesz użyć zestawu SDK usługi Azure Machine Learning i podaj następujące informacje:

* __Nazwa obliczeniowego__: nazwa, którą chcesz przypisać do tego zasobu obliczeniowego.
* __Identyfikator zasobu__: identyfikator zasobu konta usługi Data Lake Analytics. Następujący tekst jest przykładem formatu dla tej wartości:

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.DataLakeAnalytics/accounts/<datalakeanalytics-name>
    ```

    > [!TIP]
    > Aby uzyskać identyfikator zasobu, użyj następującego polecenia wiersza polecenia platformy Azure. Zastąp `<datalakeanalytics>` nazwą swojej nazwy konta usługi Data Lake Analytics:
    > ```azurecli-interactive
    > az resource list --name <datalakeanalytics> --query [].id
    > ```

Poniższy kod przedstawia sposób dołączania usługi Data Lake Analytics, jako cel obliczenia:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_id = os.environ.get("AML_ADLA_RESOURCE_ID", "<adla_resource_id>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_id))
    adla_compute = AdlaCompute.attach(
             workspace=ws,
             name=adla_compute_name,
             resource_id=adla_resource_id
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Potoki usługi Azure Machine Learning może pracować tylko z danych przechowywanych w magazynie danych domyślnego konta usługi Data Lake Analytics. Jeśli dane potrzebne do pracy z znajduje się w magazynie innych niż domyślne, można użyć [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) do kopiowania danych przed szkolenia.

## <a id="hdinsight"></a>Usługa Azure HDInsight 

HDInsight to popularne platformy do analizy danych big data. Udostępnia platformę Apache Spark, która może służyć do uczenia modelu.

> [!IMPORTANT]
> Przed użyciem w celu nauczenia modelu, należy utworzyć klaster HDInsight. Aby utworzyć platformy Spark w klastrze HDInsight, zobacz [Tworzenie klastra Spark w HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) dokumentu.
>
> Podczas tworzenia klastra, należy określić nazwę użytkownika SSH i hasło. Należy zauważyć te wartości, gdy ich potrzebujesz używając HDInsight jako cel obliczenia.
>
> Po utworzeniu klastra został w pełni kwalifikowaną nazwę domeny (FQDN) z `<clustername>.azurehdinsight.net`, gdzie `<clustername>` jest nazwa podana dla klastra. Należy ten adres (lub publiczny adres IP klastra), aby używać go jako cel obliczenia

Aby skonfigurować HDInsight jako cel obliczenia, należy podać w pełni kwalifikowaną nazwę domeny, nazwa logowania klastra i hasło dla klastra HDInsight. W poniższym przykładzie użyto zestawu SDK, aby dołączyć klaster z obszarem roboczym. W tym przykładzie Zastąp `<fqdn>` z publicznej nazwy FQDN klastra lub publiczny adres IP. Zastąp `<username>` i `<password>` przy użyciu konta użytkownika SSH i hasło klastra:

> [!NOTE]
> Aby znaleźć nazwę FQDN dla klastra, można znaleźć w witrynie Azure portal, a następnie wybierz klaster usługi HDInsight. Z __Przegląd__ sekcja, nazwy FQDN jest częścią __adresu URL__ wpisu. Wystarczy usunąć `https://` od początku wartości.
>
> ![Zrzut ekranu przedstawiający Przegląd klastra HDInsight z wpisem adresu URL wyróżniony](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Prześlij szkolenia uruchamiania

Istnieją dwa sposoby, aby przesłać przebiegu szkolenia:

* Przesyłanie `ScriptRunConfig` obiektu.
* Przesyłanie `Pipeline` obiektu.

> [!IMPORTANT]
> Usługa Azure Databricks i Azure Datalake Analytics obliczeniowe elementy docelowe należy używać tylko w potoku.
> Nie można użyć lokalnego obliczeniowego elementu docelowego w potoku.

### <a name="submit-using-scriptrunconfig"></a>Przesyłanie przy użyciu `ScriptRunConfig`

Wzorzec kod przesyłania szkoleniowe jest wykonywane przy użyciu `ScriptRunConfig` jest taki sam, niezależnie od tego, obliczeniowego elementu docelowego:

* Utwórz `ScriptRunConfig` obiekt, za pomocą konfiguracji uruchamiania dla obliczeniowego elementu docelowego.
* Prześlij przebiegu.
* Poczekaj, aż działanie zakończyć.

W poniższym przykładzie użyto konfiguracji docelowego kontrolowany przez system lokalny obliczeniowe utworzone wcześniej w tym dokumencie:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>Przesyłanie przy użyciu potoku

Kod wzorzec przesyłania szkoleniowe, który jest uruchamiany przy użyciu potoku jest taki sam niezależnie od obliczeniowego elementu docelowego:

* Dodaj krok do potoku dla zasobów obliczeniowych.
* Prześlij przebiegu przy użyciu potoku.
* Poczekaj, aż działanie zakończyć.

W poniższym przykładzie użyto usługi Azure Databricks obliczeniowego elementu docelowego utworzone wcześniej w tym dokumencie:

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
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Aby uzyskać więcej informacji na temat potoków uczenia maszynowego, zobacz [potoków i Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

Na przykład notesów programu Jupyter, które pokazują szkolenie przy użyciu potoku, zobacz [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Wyświetlanie i konfigurowanie obliczeń przy użyciu witryny Azure portal

Można wyświetlić co obliczeniowe elementy docelowe są skojarzone z obszarem roboczym w witrynie Azure portal. Aby przejść do listy, wykonaj następujące kroki:

1. Odwiedź stronę [witryny Azure portal](https://portal.azure.com) i przejdź do obszaru roboczego.
2. Kliknij pozycję __obliczenia__ łącze w obszarze __aplikacje__ sekcji.

    ![Karta obliczeń widoku](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Utworzyć cel obliczenia

Wykonaj powyższe kroki, aby wyświetlić listę obliczeniowych elementów docelowych, a następnie wykonaj następujące kroki, aby utworzyć cel obliczenia:

1. Kliknij przycisk __+__ Zaloguj się do dodania obliczeniowego elementu docelowego.

    ![Dodaj obliczenie ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Wprowadź nazwę dla obliczeniowego elementu docelowego
1. Wybierz **obliczeniowego usługi Machine Learning** jako typ obliczeń na potrzeby __szkolenia__

    > [!IMPORTANT]
    > Można tworzyć tylko obliczeniowego usługi Azure Machine Learning jako zarządzane zasoby obliczeniowe na potrzeby szkolenia

1. Wypełnij formularz wymagany, szczególnie rodziny maszyn wirtualnych i maksymalna liczba węzłów na potrzeby uruchamiając zasoby obliczeniowe 
1. Wybierz pozycję __Utwórz__
1. Stan operacji tworzenia można wyświetlić, wybierając obliczeniowego elementu docelowego z listy

    ![Wyświetl listę obliczeń](./media/how-to-set-up-training-targets/View_list.png)

1. Zostanie wtedy wyświetlone szczegóły obliczeniowego elementu docelowego.

    ![Wyświetl szczegóły](./media/how-to-set-up-training-targets/compute-target-details.png)

1. Teraz można przesłać przebiegu dla następujących elementów docelowych zgodnie z opisem powyżej


### <a name="reuse-existing-compute-in-your-workspace"></a>Ponowne używanie istniejących zasobów obliczeniowych, w obszarze roboczym

Wykonaj powyższe kroki, aby wyświetlić listę obliczeniowych elementów docelowych, a następnie wykonaj następujące kroki, aby ponownie użyć obliczeniowego elementu docelowego:

1. Kliknij przycisk **+** Zaloguj się do dodania obliczeniowego elementu docelowego
2. Wprowadź nazwę dla obliczeniowego elementu docelowego
3. Wybierz typ obliczenia, które można dołączyć do __szkolenia__

    > [!IMPORTANT]
    > Nie wszystkie obliczenia typy mogą być dołączane za pomocą portalu.
    > Obecnie są typy, które mogą być dołączane do trenowania:
    > 
    > * Zdalnego maszyny Wirtualnej
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. Wypełnij formularz, wymagane

    > [!NOTE]
    > Firma Microsoft zaleca używanie kluczy SSH są bezpieczniejsze niż hasła. Hasła są narażone na ataki siłowe, natomiast klucze SSH polegają na podpisy kryptograficzne. Aby uzyskać informacje dotyczące tworzenia kluczy SSH do użycia z usługą Azure Virtual Machines można znaleźć w następujących dokumentach:
    >
    > * [Tworzenie i używanie kluczy SSH w systemie Linux lub macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Tworzenie i używanie kluczy SSH w Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Wybierz opcję dołączenia
1. Stan operacji dołączania można wyświetlić, wybierając obliczeniowego elementu docelowego z listy
1. Teraz można przesłać przebiegu dla następujących elementów docelowych zgodnie z opisem powyżej

## <a name="examples"></a>Przykłady
Zapoznaj się z notesów w następujących lokalizacjach:
* [jak-to-użyj-usługi Azure ml/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [Samouczki/img klasyfikacji — część 1 — training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja usługi Azure Machine Learning w zestawie SDK](https://aka.ms/aml-sdk)
* [Tutorial: Train a model](tutorial-train-models-with-aml.md) (Samouczek: uczenie modelu)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Tworzenie potoków uczenia maszynowego przy użyciu usługi Azure Machine Learning](concept-ml-pipelines.md)
