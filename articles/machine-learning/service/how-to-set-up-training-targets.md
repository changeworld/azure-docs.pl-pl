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
ms.date: 09/24/2018
ms.openlocfilehash: 7eacc475145dac61db1717f1860e22cedd022262
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231451"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Wybierz, a następnie użyć obliczeniowego elementu docelowego do nauczenia modelu

Za pomocą usługi Azure Machine Learning możesz uczyć modelu w różnych środowiskach. Te środowiska o nazwie __celów obliczeń__, może być lokalne lub w chmurze. W tym dokumencie informacje o obsługiwanych obliczeniowych elementów docelowych i sposobu ich używania.

Cel obliczenia jest zasobem, który jest uruchamiany skrypt szkolenia lub udostępnia swój model, gdy aplikacja jest wdrożona jako usługę sieci web. One mogą być tworzone i zarządzane przy użyciu zestawu SDK usługi Azure Machine Learning lub interfejsu wiersza polecenia. Jeśli masz obliczeniowych elementów docelowych, które zostały utworzone przez inny proces (na przykład, witryny Azure portal lub interfejsu wiersza polecenia platformy Azure), możesz ich używać, dołączanie ich do obszaru roboczego usługi Azure Machine Learning.

Można rozpoczynać przebiegów lokalnych na komputerze, a następnie skalowania w górę i do innych środowisk, takich jak zdalnych maszyn wirtualnych do nauki o danych z procesorem GPU lub usługi Azure Batch AI. 

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK usługi Azure Machine Learning wersji 0.168 

## <a name="supported-compute-targets"></a>Obsługiwane obliczeniowych elementów docelowych

Usługa Azure Machine Learning obsługuje następujące obliczeniowych elementów docelowych:

|Obliczeniowego elementu docelowego| Przyspieszanie procesora GPU | Do strojenia hiperparametrycznego automatycznych | Wybór modelu automatycznych | Może służyć w potokach|
|----|:----:|:----:|:----:|:----:|
|[Komputer lokalny](#local)| Być może | &nbsp; | ✓ | &nbsp; |
|[Maszyna wirtualna do nauki o danych (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Usługa Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Usługa Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>* Usługa azure Databricks i Azure Data Lake Analytics może __tylko__ można używać w potoku. Aby uzyskać więcej informacji na temat potoków, zobacz [potoki w usłudze Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

__[Usługa Azure Container Instances (ACI)](#aci)__  może również służyć do uczenia modeli. To bezserwerowe w chmurze niedrogich i ułatwia tworzenie i Praca z. ACI nie obsługuje przyspieszenia procesora GPU, zautomatyzowane parametr hyper dostrajania, lub zautomatyzowane wybór modelu. Ponadto nie można użyć w potoku.

Dostępne są następujące kluczowych różnic między obliczeniowych elementów docelowych:
* __Przyspieszanie GPU__: procesorów GPU jest dostępna z poziomu maszyny wirtualnej do nauki o danych i usługi Azure Batch AI. Masz dostęp do procesora GPU na komputerze lokalnym, w zależności od sprzętu, sterowników i struktur, które są zainstalowane.
* __Automatyczne strojenia hiperparametrycznego__: usługi Azure Machine Learning zautomatyzowane hiperparametrycznego optymalizacji pomoże Ci znaleźć najlepszą hiperparametrów dla modelu.
* __Automatyczne wybór modelu__: usługi Azure Machine Learning można zalecić inteligentnie wybór algorytmu i hiperparametrycznego, podczas tworzenia modelu. Wybór modelu automatycznych pomaga szybciej niż próby ręcznego różne kombinacje zbiegają się do modeli wysokiej jakości. Aby uzyskać więcej informacji, zobacz [samouczek: automatycznie wytrenuj model klasyfikacji z usługą Azure automatyczne Machine Learning](tutorial-auto-train-models.md) dokumentu.
* __Potoki__: Usługa Azure Machine Learning umożliwia łączenie różnych zadań, takich jak szkolenia i wdrażanie w potoku. Potoki można być uruchomiono równolegle lub w kolejności i udostępniają mechanizm niezawodnej automatyzacji. Aby uzyskać więcej informacji, zobacz [tworzenie potoków uczenia maszynowego przy użyciu usługi Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

Zestaw SDK usługi Azure Machine Learning, wiersza polecenia platformy Azure lub witryny Azure portal umożliwia tworzenie obliczeniowych elementów docelowych. Umożliwia także istniejących celów obliczeń, dodając (dołączanie) je do swojego obszaru roboczego.

> [!IMPORTANT]
> Nie można dołączyć istniejącego wystąpienia kontenerów platformy Azure do swojego obszaru roboczego. Zamiast tego należy utworzyć nowe wystąpienie.
>
> Nie można utworzyć usługi Azure HDInsight, Azure Databricks i Azure Data Lake Store, w obszarze roboczym. Zamiast tego należy utworzyć zasób i następnie dołączyć go do obszaru roboczego.

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

Przełączenie z jednej obliczeniowego elementu docelowego na inny obejmuje utworzenie [konfigurację uruchamiania](concept-azure-machine-learning-architecture.md#run-configuration). Konfiguracji uruchamiania definiuje sposób uruchamiania skryptu na obliczeniowego elementu docelowego.

## <a name="training-scripts"></a>Skryptów szkolenia

Po rozpoczęciu wykonywania szkolenia jest przesyłany cały katalog, który zawiera skrypty szkolenia. Migawka jest tworzonych i wysyłanych do obliczeniowego elementu docelowego. Aby uzyskać więcej informacji, zobacz [migawek](concept-azure-machine-learning-architecture.md#snapshot).

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

Dla notesu Jupyter, demonstrujący szkolenia w środowisku zarządzane przez użytkownika, zobacz [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).
  
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

Dla notesu Jupyter, demonstrujący szkolenia w środowisku zarządzane przez system, zobacz [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).

## <a id="dsvm"></a>Maszyna wirtualna do nauki o danych

Komputer lokalny nie może mieć obliczenia lub zasobów procesora GPU, wymaganych do nauczenia modelu. W takiej sytuacji możesz skalować w górę lub skalowania w poziomie procesu uczenia przez dodanie dodatkowych obliczeniowych elementów docelowych, takich jak maszyny wirtualne do nauki o danych (DSVM).

> [!WARNING]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu. Podczas tworzenia maszyny wirtualnej lub wybierz istniejącą, należy wybrać jedną, która używa systemu Ubuntu.

Poniższe kroki konfigurowania maszyn wirtualnych do nauki o danych (DSVM) jako cel szkolenia przy użyciu zestawu SDK:

1. Utwórz lub Dołącz maszynę wirtualną
    
    * Aby utworzyć nowej maszyny wirtualnej DSVM, najpierw sprawdzić Jeśli możesz mieć nauki o takiej samej nazwie, jeśli nie utworzyć nowej maszyny Wirtualnej:
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Aby dołączyć istniejącą maszynę wirtualną jako cel obliczenia, należy podać w pełni kwalifikowaną nazwę domeny, nazwa logowania i hasło dla maszyny wirtualnej.  W tym przykładzie Zastąp ```<fqdn>``` z publicznych w pełni kwalifikowaną nazwą domeny maszyny Wirtualnej lub publiczny adres IP. Zastąp ```<username>``` i ```<password>``` przy użyciu konta użytkownika SSH i hasło dla maszyny Wirtualnej:

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

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

1. Aby usunąć zasoby obliczeniowe, gdy skończysz, użyj następującego kodu:

    ```python
    dsvm_compute.delete()
    ```

Dla notesu Jupyter, który demonstruje szkolenia na maszynę wirtualną do nauki o danych, zobacz [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb).

## <a id="batch"></a>Usługa Azure Batch AI

Jeśli zajmuje dużo czasu w celu nauczenia modelu, można użyć usługi Azure Batch AI do dystrybucji szkolenia w klastrze zasobów obliczeniowych w chmurze. Usługa Batch AI można skonfigurować w taki sposób, aby umożliwić zasobów procesora GPU.

Poniższy przykład szuka istniejący klaster usługi Batch AI według nazwy. Jeśli certyfikat nie zostanie znaleziony, zostanie utworzony:

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
batchai_cluster_name = os.environ.get("BATCHAI_CLUSTER_NAME", ws.name + "gpu")
cluster_min_nodes = os.environ.get("BATCHAI_CLUSTER_MIN_NODES", 1)
cluster_max_nodes = os.environ.get("BATCHAI_CLUSTER_MAX_NODES", 3)
vm_size = os.environ.get("BATCHAI_CLUSTER_SKU", "STANDARD_NC6")
autoscale_enabled = os.environ.get("BATCHAI_CLUSTER_AUTOSCALE_ENABLED", True)


if batchai_cluster_name in ws.compute_targets():
    compute_target = ws.compute_targets()[batchai_cluster_name]
    if compute_target and type(compute_target) is BatchAiCompute:
        print('found compute target. just use it. ' + batchai_cluster_name)
else:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = autoscale_enabled,
                                                                cluster_min_nodes = cluster_min_nodes, 
                                                                cluster_max_nodes = cluster_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Aby dołączyć istniejący klaster usługi Batch AI jako cel obliczenia, należy podać identyfikator zasobu platformy Azure. Aby uzyskać identyfikator zasobu w witrynie Azure portal, użyj następujących kroków:
1. Wyszukaj `Batch AI` usługę **wszystkie usługi**
1. Kliknij nazwę obszaru roboczego, do której należy klaster
1. Wybierz klaster
1. Kliknij pozycję **właściwości**
1. Kopiuj **identyfikator**

W poniższym przykładzie użyto zestawu SDK, aby dołączyć klaster z obszarem roboczym. W tym przykładzie Zastąp `<name>` przy użyciu dowolnej nazwy dla obliczeń. Nazwa nie musi odpowiadać nazwie klastra. Zastąp `<resource-id>` za pomocą usługi Azure resource ID szczegóły przedstawiono powyżej:

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

Możesz również sprawdzić stan klastra i zadania usługi Batch AI przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:

- Sprawdź stan klastra. Możesz zobaczyć, ile węzły są uruchomione przy użyciu `az batchai cluster list`.
- Sprawdź stan zadania. Możesz zobaczyć, ile zadania są uruchamiane przy użyciu `az batchai job list`.

Trwa około 5 minut, aby utworzyć klaster usługi Batch AI.

Dla notesu Jupyter, który demonstruje szkolenia w klastrze usługi Batch AI, zobacz [ https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb).

## <a name='aci'></a>Wystąpienie kontenera platformy Azure (ACI)

Usługa Azure Container Instances są kontenerach izolowanych, krótszy czas uruchamiania, które nie wymagają od użytkownika zarządzania jakimikolwiek maszynami wirtualnymi. Usługa Azure Machine Learning korzysta z kontenerów systemu Linux, które są dostępne w westus, eastus, westeurope northeurope, westus2 i regiony southeastasia. Aby uzyskać więcej informacji, zobacz [dostępność w poszczególnych regionach](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

Poniższy przykład pokazuje, jak utworzyć cel obliczenia ACI i użyć go do nauczenia modelu przy użyciu zestawu SDK: 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Może potrwać od kilku sekund do kilku minut utworzyć cel obliczenia ACI.

Dla notesu Jupyter, który demonstruje szkolenie dotyczące wystąpienia kontenera platformy Azure, zobacz [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb).

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

## <a id="hdinsight"></a>Dołącz klastra usługi HDInsight 

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

Aby uzyskać notesu programu Jupyter, który demonstruje szkolenia z platformy Spark w HDInsight, zobacz [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb).

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

1. Wprowadź nazwę dla obliczeniowego elementu docelowego.
1. Wybierz typ obliczenia, które można dołączyć do __szkolenia__. 

    > [!IMPORTANT]
    > Nie wszystkie zasoby obliczeniowe typy mogą być tworzone przy użyciu witryny Azure portal. Obecnie są typy, które mogą być tworzone na potrzeby szkolenia:
    > 
    > * Maszyna wirtualna
    > * Usługa Batch — sztuczna inteligencja

1. Wybierz __Utwórz nowy__ i wypełnij formularz wymagane. 
1. Wybierz pozycję __Utwórz__
1. Można wyświetlić stan operacji tworzenia, wybierając obliczeniowego elementu docelowego z listy.

    ![Wyświetl listę obliczeń](./media/how-to-set-up-training-targets/View_list.png) zostanie wtedy wyświetlone szczegóły obliczeniowego elementu docelowego.
    ![Wyświetl szczegóły](./media/how-to-set-up-training-targets/vm_view.PNG)
1. Teraz można przesłać przebiegu dla następujących elementów docelowych zgodnie z opisem powyżej.

### <a name="reuse-existing-compute-in-your-workspace"></a>Ponowne używanie istniejących zasobów obliczeniowych, w obszarze roboczym

Wykonaj powyższe kroki, aby wyświetlić listę obliczeniowych elementów docelowych, a następnie wykonaj następujące kroki, aby ponownie użyć obliczeniowego elementu docelowego:

1. Kliknij przycisk **+** Zaloguj się do dodania obliczeniowego elementu docelowego.
2. Wprowadź nazwę dla obliczeniowego elementu docelowego.
3. Wybierz typ obliczenia, które można dołączyć do trenowania.

    > [!IMPORTANT]
    > Nie wszystkie obliczenia typy mogą być dołączane za pomocą portalu.
    > Obecnie są typy, które mogą być dołączane do trenowania:
    > 
    > * Maszyna wirtualna
    > * Usługa Batch — sztuczna inteligencja

1. Wybierz opcję Użyj istniejącej.
    - Podczas podłączania klastry usługi Batch AI, wybierz obliczeniowego elementu docelowego z listy rozwijanej, wybierz obszar roboczy usługi Batch AI i klastra sztucznej Inteligencji usługi Batch, a następnie kliknij **Utwórz**.
    - Podczas dołączania maszyny wirtualnej, wprowadź adres IP, kombinacja nazwy użytkownika/hasła, klucze prywatne/publiczne i numer portu, a następnie kliknij przycisk Utwórz.

    > [!NOTE]
    > Firma Microsoft zaleca używanie kluczy SSH są bezpieczniejsze niż hasła. Hasła są narażone na ataki siłowe, natomiast klucze SSH polegają na podpisy kryptograficzne. Aby uzyskać informacje dotyczące tworzenia kluczy SSH do użycia z usługą Azure Virtual Machines można znaleźć w następujących dokumentach:
    >
    > * [Tworzenie i używanie kluczy SSH w systemie Linux lub macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Tworzenie i używanie kluczy SSH w Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. Stan Stan aprowizacji można wyświetlić, wybierając obliczeniowego elementu docelowego z listy.
6. Teraz można przesłać przebiegu względem tych celów.

## <a name="examples"></a>Przykłady
Następujące notesów zademonstrowania koncepcji w tym artykule:
* [01.Getting-Started/02.Train-on-Local/02.Train-on-Local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.Getting-Started/04.Train-on-Remote-VM/04.Train-on-Remote-VM.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm)
* [01.Getting-Started/03.Train-on-aci/03.Train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci)
* [01.Getting-Started/05.Train-in-Spark/05.Train-in-Spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark)
* [Tutorials/01.Train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

Pobierz te notesy: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja usługi Azure Machine Learning w zestawie SDK](https://aka.ms/aml-sdk)
* [Tutorial: Train a model](tutorial-train-models-with-aml.md) (Samouczek: uczenie modelu)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Tworzenie potoków uczenia maszynowego przy użyciu usługi Azure Machine Learning](concept-ml-pipelines.md)
