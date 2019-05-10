---
title: Tworzenie i używanie obliczeniowych elementów docelowych do trenowania modelu
titleSuffix: Azure Machine Learning service
description: Konfigurowanie środowisk szkolenia (celów obliczeń) dla szkolenie modelu uczenia maszynowego. Można łatwo przełączać się między środowiskami szkolenia. Rozpocznij szkolenie lokalnie. Jeśli musisz skalować w poziomie, przełącz się do elementu docelowego mocą obliczeniową opartą na chmurze.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: c49b9d5fdc0c17f16f1c80471a00dd53625dc6e8
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236955"
---
# <a name="set-up-compute-targets-for-model-training"></a>Konfigurowanie celów obliczeń do trenowania modelu

Za pomocą usługi Azure Machine Learning można uczenie modelu na wielu różnych zasobów lub środowisk, zbiorczo określany jako [ __celów obliczeń__](concept-azure-machine-learning-architecture.md#compute-target). Cel obliczenia może być komputer lokalny lub zasobem w chmurze, takich jak Azure obliczeniowego usługi Machine Learning, Azure HDInsight lub zdalnego maszyny wirtualnej.  Można również utworzyć obliczeniowych elementów docelowych dla modelu wdrożenia, zgodnie z opisem w ["gdzie i jak wdrożyć swoje modele"](how-to-deploy-and-where.md).

Można tworzyć i zarządzać obliczeniowego elementu docelowego przy użyciu zestawu SDK usługi Azure Machine Learning, witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Jeśli masz obliczeniowych elementów docelowych, które zostały utworzone za pomocą innej usługi (na przykład klastra usługi HDInsight), możesz ich używać, dołączanie ich do obszaru roboczego usługi Azure Machine Learning.
 
W tym artykule dowiesz się, jak używać różnych celów obliczeń do trenowania modelu.  Kroków dla wszystkich celów obliczeń, użyj tego samego przepływu pracy:
1. __Utwórz__ cel obliczenia, jeśli nie masz jeszcze jeden.
2. __Dołącz__ obliczeniowego elementu docelowego do swojego obszaru roboczego.
3. __Konfigurowanie__ obliczenia docelowych tak, aby zawierała zależności środowiska i pakiet języka Python wymagane przez skrypt.


>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK usługi Azure Machine Learning w wersji 1.0.6.

## <a name="compute-targets-for-training"></a>Obliczeniowych elementów docelowych na potrzeby szkolenia

Usługa Azure Machine Learning obsługuje różne w różnych obliczeniowych elementów docelowych. Cykl projektowania modelu Typowa rozpoczyna się od dev/eksperymentów na niewielkiej ilości danych. Na tym etapie firma Microsoft zaleca używanie środowiska lokalnego. Na przykład komputera lokalnego lub maszyny Wirtualnej opartej na chmurze. Skalowanie w górę szkolenia na większych zestawów danych lub czy rozproszonego szkolenia, firma Microsoft zaleca obliczeniowego usługi Azure Machine Learning Tworzenie klastra przy użyciu jednego lub wielu node tego skalowania każdej próbie przesłania przebiegu. Można również dołączyć własnych zasobów obliczeniowych, mimo że obsługi różnych scenariuszy mogą się różnić zależnie z poniższym opisem:


|Obliczeniowego elementu docelowego na potrzeby szkolenia| Przyspieszanie procesora GPU | Automatyczne<br/> do strojenia hiperparametrycznego | Automatyczne</br> Uczenie maszynowe | Potoków uczenia maszynowego Azure |
|----|:----:|:----:|:----:|:----:|
|[Komputer lokalny](#local)| Być może | &nbsp; | ✓ | &nbsp; |
|[Usługi Azure Machine Learning obliczeń](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Zdalnego maszyny Wirtualnej](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓ |
|[Azure Data Lake Analytics](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Usługa Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure Batch](#azbatch)| &nbsp; | &nbsp; | &nbsp; | ✓ |

**Wszystkie zasoby obliczeniowe elementy docelowe mogą być ponownie używane dla wielu zadań szkoleniowych**. Na przykład po dołączeniu maszyny Wirtualnej z systemem zdalnym do swojego obszaru roboczego, można ponownie użyć go dla wielu zadań.

> [!NOTE]
> Azure obliczeniowego usługi Machine Learning mogą być tworzone jako trwałe zasobu lub tworzone dynamicznie, na żądanie uruchomienia. Tworzenie na podstawie wykonywania usuwa obliczeniowego elementu docelowego po przebiegu szkolenia, więc nie można ponownie użyć obliczeniowych elementów docelowych, utworzone w ten sposób.

## <a name="whats-a-run-configuration"></a>Co to jest konfiguracji uruchamiania?

Szkolenia, jest często Uruchom na komputerze lokalnym, a później Uruchom ten skrypt szkolenia na innego obliczeniowego elementu docelowego. Usługa Azure Machine Learning umożliwia uruchamianie skryptu na różnych celów obliczeń bez konieczności zmiany skryptu. 

Wszystko, czego potrzebujesz, aby zrobić to definiują środowisko dla każdego obiektu docelowego obliczeniowych, przy użyciu **konfigurację uruchamiania**.  Następnie w przypadku uruchamiania eksperymentu szkolenia na innego obliczeniowego elementu docelowego należy określić konfiguracji uruchamiania dla tego środowiska obliczeniowego. 

Dowiedz się więcej o [przesyłanie eksperymentów](#submit) na końcu tego artykułu.

### <a name="manage-environment-and-dependencies"></a>Zarządzanie środowiskiem i zależności

Podczas tworzenia konfiguracji uruchamiania, należy zdecydować, jak zarządzać środowiskiem i zależności na obliczeniowego elementu docelowego. 

#### <a name="system-managed-environment"></a>System zarządzany środowiska

Użyj środowiska system zarządzany, jeśli chcesz [Conda](https://conda.io/docs/) do zarządzania środowiskiem Python i zależności skryptu dla Ciebie. Zarządzane przez system środowisko zakłada, że domyślnie i najbardziej typowe wybór. Jest przydatne dla zdalnego obliczeniowych elementów docelowych, szczególnie w przypadku, gdy nie można skonfigurować, których platformą docelową. 

Wszystko, czego potrzebujesz, aby zrobić to określić każdy pakiet zależności za pomocą [klasy CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) Conda następnie tworzy plik o nazwie **conda_dependencies.yml** w **aml_config** katalog w swojej przestrzeni roboczej z listy zależności pakietów i skonfigurowanie środowiska Python, podczas przesyłania eksperymentu szkolenia. 

Wstępnej instalacji nowego środowiska może potrwać kilka minut w zależności od rozmiaru wymagane zależności. Tak długo, jak lista pakietów pozostaje niezmieniony, czas instalacji odbywa się tylko raz.
  
Poniższy kod przedstawia przykład dla środowiska system zarządzany wymagające scikit-Dowiedz się więcej:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Środowiska zarządzanego przez użytkownika

W środowisku zarządzane przez użytkownika jesteś odpowiedzialny za konfigurowanie środowiska i instalowanie pakietu, co wymaga skrypt szkolenia na obliczeniowego elementu docelowego. Jeśli już skonfigurowano środowiska szkolenia (takich jak na komputerze lokalnym), możesz pominąć krok konfiguracji, ustawiając `user_managed_dependencies` na wartość True. Conda będą Sprawdź środowisko lub nie zainstalować wszystko dla Ciebie.

Poniższy kod przedstawia przykład Konfigurowanie przebiegów szkoleniowych w środowisku zarządzane przez użytkownika:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Konfigurowanie celów obliczeń przy użyciu języka Python

Użyj celów obliczeń poniżej, aby skonfigurować te sekcje:

* [Komputer lokalny](#local)
* [Usługi Azure Machine Learning obliczeń](#amlcompute)
* [Zdalnych maszyn wirtualnych](#vm)
* [Usługa Azure HDInsight](#hdinsight)


### <a id="local"></a>Komputer lokalny

1. **Tworzenie i dołączanie**: Nie ma potrzeby tworzenia lub dołączyć do korzystania z komputera lokalnego jako środowisko szkoleniowe obliczeniowego elementu docelowego.  

1. **Konfiguracja**:  Gdy komputer lokalny jest używany jako cel obliczenia, kod szkolenia jest uruchamiany swojej [środowisko programistyczne](how-to-configure-environment.md).  Jeśli środowisko już pakiety języka Python, czego potrzebujesz, za pomocą środowiska zarządzanego przez użytkownika.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Skoro już dołączone zasoby obliczeniowe i uruchomieniu skonfigurowany, następnym krokiem jest [przesłać przebiegu szkolenia](#submit).

### <a id="amlcompute"></a>Usługi Azure Machine Learning obliczeń

Usługa Azure obliczeniowego usługi Machine Learning jest infrastrukturę obliczeniową zarządzanego, który umożliwia użytkownikom łatwe tworzenie jednego lub wielu węzłach obliczeniowych. Obliczenia jest tworzony w regionie Twojego obszaru roboczego jako zasób, które mogą być udostępniane innym użytkownikom w obszarze roboczym. Obliczenia jest skalowany w górę automatycznie podczas przesyłania zadania, a, można umieścić w usłudze Azure Virtual Network. Zasoby obliczeniowe wykonuje w środowisku konteneryzowanych oraz pakietów zależności w modelu [kontener platformy Docker](https://www.docker.com/why-docker).

Aby rozdystrybuować procesu uczenia klastra procesora CPU lub GPU węzłów obliczeniowych w chmurze, można użyć obliczeniowego usługi Azure Machine Learning. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, które zawierają procesorów GPU, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Usługa Azure obliczeniowego usługi Machine Learning ma limity domyślne, takie jak liczba rdzeni, które mogą być przydzielone. Aby uzyskać więcej informacji, zobacz [zarządzanie i żądania przydziały dla zasobów platformy Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Na żądanie, aby zaplanować uruchomienie lub jako trwałe zasobów, można utworzyć środowiska obliczeniowego usługi Azure Machine Learning.

#### <a name="run-based-creation"></a>Tworzenie opartych na przebieg

Można utworzyć obliczeniowego usługi Azure Machine Learning jako cel obliczenia w czasie wykonywania. Zasoby obliczeniowe są tworzone automatycznie dla przebieg. Zasoby obliczeniowe są usuwane automatycznie po ukończeniu uruchomienia. 

> [!NOTE]
> Aby określić maksymalną liczbę węzłów do użycia, należy zwykle ustawić `node_count` do liczby węzłów. Ma obecnie (04/04/2019 r) usterkę, która uniemożliwia to pracę. Jako obejście tego problemu, należy użyć `amlcompute._cluster_max_node_count` właściwości konfiguracji uruchamiania. Na przykład `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> Tworzenie na podstawie wykonywania mocy obliczeniowej usługi Azure Machine Learning jest obecnie w wersji zapoznawczej. Nie należy używać tworzenie na podstawie wykonywania, jeśli jest używany, strojenia hiperparametrycznego zautomatyzowane lub zautomatyzowane uczenia maszynowego. Służące do strojenia hiperparametrycznego lub uczenia maszynowego automatyczne tworzenie [trwałego obliczeń](#persistent) docelowych w zamian.

1.  **Tworzenie, dołączanie i konfigurowanie**: Tworzenie na podstawie wykonywania wykonuje wszystkie niezbędne kroki w celu tworzenia, dołączania i skonfigurować obliczeniowego elementu docelowego za pomocą konfiguracji uruchamiania.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Skoro już dołączone zasoby obliczeniowe i uruchomieniu skonfigurowany, następnym krokiem jest [przesłać przebiegu szkolenia](#submit).

#### <a id="persistent"></a>Trwałe obliczeń

Trwałe Azure obliczeniowego usługi Machine Learning mogą zostać ponownie użyte w zadaniach. Zasoby obliczeniowe mogą być udostępniane innym użytkownikom w obszarze roboczym i są przechowywane między zadaniami.

1. **Tworzenie i dołączanie**: Aby utworzyć trwałe zasobu obliczeniowego usługi Azure Machine Learning w języku Python, określić **vm_size** i **max_nodes** właściwości. Usługa Azure Machine Learning następnie używa inteligentnych wartości domyślnych dla innych właściwości. Skalowania obliczeń, dół, zerowego węzłów, gdy nie jest używany.   Dedykowane maszyny wirtualne są tworzone na wykonanie zadań zgodnie z potrzebami.
    
    * **vm_size**: Rodzina maszyn wirtualnych węzłów utworzone przez obliczeniowego usługi Azure Machine Learning.
    * **max_nodes**: Maksymalna liczba węzłów do automatycznego skalowania do uruchomienia zadania w obliczeniowego usługi Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Można również skonfigurować kilka właściwości zaawansowane, tworząc obliczeniowego usługi Azure Machine Learning. Właściwości umożliwiają tworzenie trwałych klastra o stałym rozmiarze lub w ramach istniejącej sieci wirtualnej platformy Azure w ramach subskrypcji.  Zobacz [klasy AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) Aby uzyskać szczegółowe informacje.
    
   Lub można tworzyć i dołączać trwałe zasobu obliczeniowego usługi Azure Machine Learning [w witrynie Azure portal](#portal-create).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla trwałego obliczeniowego elementu docelowego.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Skoro już dołączone zasoby obliczeniowe i uruchomieniu skonfigurowany, następnym krokiem jest [przesłać przebiegu szkolenia](#submit).


### <a id="vm"></a>Zdalnych maszyn wirtualnych

Usługa Azure Machine Learning obsługuje również wprowadzenie własnych zasobów obliczeniowych i dołączania ich do obszaru roboczego. Jednego takiego typu zasobu jest dowolnego zdalnej maszynie Wirtualnej, tak długo, jak jest ona dostępna z usługi Azure Machine Learning. Zasób może być maszynie Wirtualnej platformy Azure, na serwerze zdalnym w organizacji lub w środowisku lokalnym. W szczególności podany adres IP i poświadczenia (nazwę użytkownika i hasło lub klucz SSH), używając wszystkie dostępne maszyny Wirtualnej dla przebiegów zdalnych.

Można użyć środowiska conda tworzonym przez system, już istniejące środowisko Python lub kontenera Docker. Do wykonania w kontenerze platformy Docker, musi mieć uruchomiony na maszynie Wirtualnej aparat platformy Docker. Ta funkcja jest szczególnie przydatne w przypadku, gdy chcesz środowiska bardziej elastyczne, oparte na chmurze dev/eksperymentowanie w usłudze niż komputer lokalny.

Użyj usługi Azure Data Science Virtual Machine (dsvm dystrybucji) jako maszyny Wirtualnej platformy Azure wybranym dla tego scenariusza. Ta maszyna wirtualna jest do nauki o danych wstępnie skonfigurowanych i Środowisko deweloperskie sztucznej Inteligencji na platformie Azure. Maszyna wirtualna oferuje zestaw wyselekcjonowanych narzędzi i platform, dla pełnego cyklu życia usługi machine learning rozwoju. Aby uzyskać więcej informacji na temat maszyny DSVM za pomocą usługi Azure Machine Learning, zobacz [skonfigurować środowisko programowania](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Utwórz**: Przed użyciem w celu nauczenia modelu, należy utworzyć maszyny wirtualnej DSVM. Aby utworzyć ten zasób, zobacz [Aprowizowanie maszyny wirtualnej do nauki o danych dla systemu Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne z systemami Ubuntu. Podczas tworzenia maszyny Wirtualnej lub wybierz istniejącą maszynę Wirtualną, należy wybrać maszynę Wirtualną, która korzysta z systemem Ubuntu.

1. **Dołącz**: Aby dołączyć istniejącą maszynę wirtualną jako cel obliczenia, należy podać w pełni kwalifikowaną nazwę domeny (FQDN), nazwę użytkownika i hasło dla maszyny wirtualnej. W tym przykładzie Zastąp \<fqdn > za pomocą publiczny adres FQDN maszyny Wirtualnej lub publiczny adres IP. Zastąp \<username > i \<hasło > za pomocą nazwy użytkownika SSH i hasło dla maszyny Wirtualnej.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Lub można dołączyć maszyny DSVM do swojego obszaru roboczego [przy użyciu witryny Azure portal](#portal-reuse).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeniowe DSVM. Platforma docker i narzędzia conda są używane do tworzenia i konfigurowania środowiska szkolenia na maszyny DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Skoro już dołączone zasoby obliczeniowe i uruchomieniu skonfigurowany, następnym krokiem jest [przesłać przebiegu szkolenia](#submit).

### <a id="hdinsight"></a>Usługa Azure HDInsight 

Usługa Azure HDInsight to popularne platformy do analizy danych big data. Ta platforma udostępnia platformę Apache Spark, która może służyć do uczenia modelu.

1. **Utwórz**:  Tworzenie klastra HDInsight, zanim go użyjesz do nauczenia modelu. Aby utworzyć platformy Spark w klastrze HDInsight, zobacz [Tworzenie klastra Spark w HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Podczas tworzenia klastra, należy określić nazwę użytkownika SSH i hasło. Zanotuj te wartości w miarę potrzeb do użycia HDInsight jako cel obliczenia.
    
    Po utworzeniu klastra połączyć się z nim nazwę hosta \<nazwa_klastra >-ssh.azurehdinsight.net, gdzie \<clustername > to nazwa, podane dla klastra. 

1. **Dołącz**: Aby dołączyć klastra usługi HDInsight jako cel obliczenia, należy podać nazwę hosta, nazwę użytkownika i hasło dla klastra HDInsight. W poniższym przykładzie użyto zestawu SDK, aby dołączyć klaster z obszarem roboczym. W tym przykładzie Zastąp \<nazwa_klastra > nazwą Twojego klastra. Zastąp \<username > i \<hasło > za pomocą nazwy użytkownika SSH i hasło klastra.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Lub możesz dołączyć klastra HDInsight z obszarem roboczym [przy użyciu witryny Azure portal](#portal-reuse).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeniowych usługi HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Skoro już dołączone zasoby obliczeniowe i uruchomieniu skonfigurowany, następnym krokiem jest [przesłać przebiegu szkolenia](#submit).


### <a id="azbatch"></a>Azure Batch 

Usługa Azure Batch umożliwia wydajne uruchamianie dużych równoległych i o wysokiej wydajności obliczeń (HPC) aplikacji w chmurze. AzureBatchStep może służyć w potoku usługi Azure Machine Learning do przesyłania zadań do puli Azure Batch maszyn.

Aby dołączyć usługi Azure Batch jako cel obliczenia, możesz użyć zestawu SDK usługi Azure Machine Learning i podaj następujące informacje:

-   **Nazwa obliczeniowego usługi Azure Batch**: Przyjazna nazwa do użycia zasobów obliczeniowych w obrębie obszaru roboczego
-   **Nazwa konta usługi Azure Batch**: Nazwa konta usługi Azure Batch
-   **Grupa zasobów**: Grupy zasobów zawierającej konto usługi Azure Batch.

Poniższy kod przedstawia sposób dołączania usługi Azure Batch jako cel obliczenia:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

batch_compute_name = 'mybatchcompute' # Name to associate with new compute in workspace

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>" # Name of the Batch account
batch_resource_group = "<batch_resource_group>" # Name of the resource group which contains this account

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-compute-in-the-azure-portal"></a>Konfigurowanie zasobów obliczeniowych w witrynie Azure portal

Możesz uzyskać dostęp do celów obliczeń, które są skojarzone z obszarem roboczym w witrynie Azure portal.  Można użyć portalu:

* [Wyświetl celów obliczeń](#portal-view) dołączone do obszaru roboczego
* [Utworzyć cel obliczenia](#portal-create) w obszarze roboczym
* [Dołącz obliczeniowego elementu docelowego](#portal-reuse) utworzony poza obszarem roboczym

Po obiekt docelowy jest utworzone i dołączone do obszaru roboczego, zostanie użyty w swojej konfiguracji uruchamiania, za pomocą `ComputeTarget` obiektu: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Wyświetl obliczeniowych elementów docelowych


Aby zobaczyć obliczeniowych elementów docelowych dla obszaru roboczego, użyj następujących kroków:

1. Przejdź do [witryny Azure portal](https://portal.azure.com) , a następnie otwórz obszar roboczy. 
1. W obszarze __aplikacje__, wybierz opcję __obliczenia__.

    ![Karta obliczeń widoku](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Utworzyć cel obliczenia

Wykonaj poprzednie kroki, aby wyświetlić listę obliczeniowych elementów docelowych. Następnie wykonaj następujące kroki, aby utworzyć cel obliczenia: 

1. Wybierz znak plus (+), aby dodać obliczeniowego elementu docelowego.

    ![Dodaj cel obliczenia](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Wprowadź nazwę dla obliczeniowego elementu docelowego. 

1. Wybierz **obliczeniowego usługi Machine Learning** jako typ obliczeń na potrzeby __szkolenia__. 

    >[!NOTE]
    >Azure obliczeniowego usługi Machine Learning jest zasobem tylko zarządzane obliczeń, utworzone w witrynie Azure portal.  Po ich utworzeniu można dołączyć wszystkie zasoby obliczeniowe.

1. Wypełnij formularz. Podanie wartości wymaganych właściwości szczególnie **rodziny maszyn wirtualnych**i **maksymalna liczba węzłów** do użycia w celu uruchomienia obliczeń.  

    ![Wypełnij formularz](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Wybierz pozycję __Utwórz__.


1. Wyświetlanie stanu operacji tworzenia, wybierając obliczeniowego elementu docelowego z listy:

    ![Wybierz cel obliczenia, aby wyświetlić stan operacji create](./media/how-to-set-up-training-targets/View_list.png)

1. Następnie zobaczysz szczegółowe informacje dotyczące obliczeniowego elementu docelowego: 

    ![Wyświetl szczegóły komputera docelowego](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Dołącz obliczeniowych elementów docelowych

Aby użyć obliczeniowych elementów docelowych utworzony poza obszarem roboczym usługi Azure Machine Learning, dołącz je. Dołączanie obliczeniowego elementu docelowego udostępnia go do obszaru roboczego.

Wykonaj kroki opisane wcześniej, aby wyświetlić listę obliczeniowych elementów docelowych. Następnie wykonaj następujące kroki, aby dołączyć obliczeniowego elementu docelowego: 

1. Wybierz znak plus (+), aby dodać obliczeniowego elementu docelowego. 
1. Wprowadź nazwę dla obliczeniowego elementu docelowego. 
1. Wybierz typ obliczenia, które można dołączyć do __szkolenia__:

    > [!IMPORTANT]
    > Nie wszystkie obliczenia typy mogą być dołączane w witrynie Azure portal. Typy obliczeń, które mogą być obecnie dołączone do trenowania:
    >
    > * Maszyny Wirtualnej z systemem zdalnym
    > * Usługa Azure Databricks (do użytku w potokach uczenia maszynowego)
    > * Usługa Azure Data Lake Analytics (do użytku w potokach uczenia maszynowego)
    > * Azure HDInsight

1. Wypełnij formularz i podaj wartości wymagane właściwości.

    > [!NOTE]
    > Firma Microsoft zaleca używanie kluczy SSH, które są bezpieczniejsze niż hasła. Hasła są narażone na ataki siłowe. Klucze SSH, zależą od podpisy kryptograficzne. Aby uzyskać informacje na temat tworzenia kluczy SSH do użycia z usługą Azure Virtual Machines zobacz następujące dokumenty:
    >
    > * [Tworzenie i używanie kluczy SSH w systemie Linux lub macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Tworzenie i używanie kluczy SSH w Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Wybierz __dołączyć__. 
1. Wyświetl stan operacji dołączania, wybierając obliczeniowego elementu docelowego z listy.

## <a name="set-up-compute-with-the-cli"></a>Konfigurowanie obliczeniowe przy użyciu interfejsu wiersza polecenia

Możesz uzyskać dostęp obliczeniowych elementów docelowych, które są skojarzone z sieci za pomocą obszaru roboczego [rozszerzenie interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) dla usługi Azure Machine Learning.  Możesz użyć interfejsu wiersza polecenia do:

* Utworzyć cel obliczenia zarządzanych
* Aktualizowanie zarządzanych obliczeniowego elementu docelowego
* Dołącz niezarządzanych obliczeniowego elementu docelowego

Aby uzyskać więcej informacji, zobacz [zarządzania zasobami](reference-azure-machine-learning-cli.md#resource-management).

## <a id="submit"></a>Prześlij szkolenia uruchamiania

Po utworzeniu konfiguracji uruchamiania, użyj do uruchamiania eksperymentu.  Wzorca kodu, aby przesłać przebiegu szkolenia jest taka sama dla wszystkich typów obliczeniowych elementów docelowych:

1. Utworzyć eksperyment do uruchomienia
1. Prześlij przebiegu.
1. Poczekaj, aż działanie zakończyć.

> [!IMPORTANT]
> Po przesłaniu wykonywania szkolenia migawkę katalogu, który zawiera skrypty szkolenia jest tworzony i wysyłane do obliczeniowego elementu docelowego. Jest on również zapisany w ramach eksperymentu w obszarze roboczym. Po zmianie plików i przesłać Uruchom ponownie, tylko zmienione pliki zostaną przekazane.
>
> Aby uniemożliwić dołączanie w migawce pliki, należy utworzyć [.gitignore](https://git-scm.com/docs/gitignore) lub `.amlignore` plików w katalogu i Dodaj pliki do niego. `.amlignore` Plików używa tej samej składni i wzorce jako [.gitignore](https://git-scm.com/docs/gitignore) pliku. Jeśli oba pliki istnieją, `.amlignore` plik ma pierwszeństwo.
> 
> Aby uzyskać więcej informacji, zobacz [migawek](concept-azure-machine-learning-architecture.md#snapshot).

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Najpierw należy utworzyć eksperyment, w obszarze roboczym.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Przesyłanie eksperymentu

Przesyłanie eksperymentu za pomocą `ScriptRunConfig` obiektu.  Ten obiekt zawiera:

* **source_directory**: Katalog źródłowy, który zawiera skrypt szkolenia
* **skrypt**: Skrypt szkolenia
* **run_config**: Konfiguracja uruchamiania, który z kolei definiuje, gdzie nastąpi szkolenia.

Na przykład, aby użyć [lokalny element docelowy](#local) konfiguracji:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Przełącz na tym samym eksperymencie co do uruchamiania w innego celu obliczeń przy użyciu różnych konfiguracji uruchamiania, takich jak [docelowej amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Możesz też:

* Przesyłanie eksperymentu za pomocą `Estimator` obiektu, jak pokazano na [modeli uczenia Maszynowego szkolenie dzięki aplikacjom](how-to-train-ml-models.md). 
* Przysyłanie eksperymentu [przy użyciu interfejsu wiersza polecenia rozszerzenia](reference-azure-machine-learning-cli.md#experiments).

## <a name="notebook-examples"></a>Przykłady notesu

Zobacz te notesy przykłady szkolenie przy użyciu różnych celów obliczeń:
* [jak-to-użyj-usługi Azure ml/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Samouczki/img klasyfikacji — część 1 — training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanych obliczeniowego elementu docelowego w celu nauczenia modelu.
* Dowiedz się, jak [efektywnie Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md) na budowanie sprawniejszych modeli.
* Dowiedz się, gdy masz trenowanego modelu, [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md).
* Widok [klasy RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) odwołanie do zestawu SDK.
* [Usługa Azure Machine Learning za pomocą usługi Azure Virtual Networks](how-to-enable-virtual-network.md)
