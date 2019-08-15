---
title: Tworzenie i używanie obliczeniowych elementów docelowych do trenowania modelu
titleSuffix: Azure Machine Learning service
description: Skonfiguruj środowiska szkoleniowe (cele obliczeniowe) dla szkolenia modelu uczenia maszynowego. Można łatwo przełączać się między środowiskami szkoleniowymi. Zacznij szkolenie lokalnie. Jeśli konieczne jest skalowanie w poziomie, przełącz się do elementu docelowego obliczeń opartego na chmurze.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: 72155e072acb8006b48f6951fc60081126c80691
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990463"
---
# <a name="set-up-compute-targets-for-model-training"></a>Konfigurowanie celów obliczeń do trenowania modelu 

Dzięki usłudze Azure Machine Learning można nauczyć model na różnych zasobach lub środowiskach, zbiorczo nazywanymi [__obiektami docelowymi obliczeń__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.  Możesz również utworzyć cele obliczeniowe dla wdrożenia modelu, zgodnie z opisem w artykule ["gdzie i jak wdrażać modele"](how-to-deploy-and-where.md).

Można utworzyć obiekt docelowy obliczeń i zarządzać nim przy użyciu zestawu SDK Azure Machine Learning, Azure Portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenia Azure Machine Learning VS Code. Jeśli masz obliczeniowych elementów docelowych, które zostały utworzone za pomocą innej usługi (na przykład klastra usługi HDInsight), możesz ich używać, dołączanie ich do obszaru roboczego usługi Azure Machine Learning.
 
W tym artykule dowiesz się, jak używać różnych obiektów docelowych obliczeń do uczenia modelu.  Kroki dla wszystkich obiektów docelowych obliczeń są zgodne z tym samym przepływem pracy:
1. __Utwórz__ obiekt docelowy obliczeń, jeśli jeszcze go nie masz.
2. __Dołącz__ obiekt docelowy obliczeń do obszaru roboczego.
3. __Skonfiguruj__ cel obliczeń tak, aby zawierał środowisko Python i zależności pakietów, które są niezbędne przez skrypt.


>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK Azure Machine Learning 1.0.39.

## <a name="compute-targets-for-training"></a>Cele obliczeniowe dla szkolenia

Usługa Azure Machine Learning ma różne wsparcie dla różnych obiektów docelowych obliczeń. Cykl projektowania modelu Typowa rozpoczyna się od dev/eksperymentów na niewielkiej ilości danych. Na tym etapie firma Microsoft zaleca używanie środowiska lokalnego. Na przykład komputera lokalnego lub maszyny Wirtualnej opartej na chmurze. Skalowanie w górę szkolenia na większych zestawów danych lub czy rozproszonego szkolenia, firma Microsoft zaleca obliczeniowego usługi Azure Machine Learning Tworzenie klastra przy użyciu jednego lub wielu node tego skalowania każdej próbie przesłania przebiegu. Można również dołączyć własnych zasobów obliczeniowych, mimo że obsługi różnych scenariuszy mogą się różnić zależnie z poniższym opisem:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning obliczeń można utworzyć jako zasób trwały lub utworzyć dynamicznie w przypadku żądania uruchomienia. Tworzenie na podstawie uruchomienia powoduje usunięcie obiektu docelowego obliczeń po zakończeniu szkolenia, dlatego nie można ponownie użyć obiektów docelowych obliczeń utworzonych w ten sposób.

## <a name="whats-a-run-configuration"></a>Co to jest Konfiguracja przebiegu?

Podczas szkolenia często rozpoczyna się na komputerze lokalnym, a następnie uruchamia ten skrypt szkoleniowy na innym miejscu docelowym obliczeń. Za pomocą usługi Azure Machine Learning można uruchomić skrypt na różnych obiektach docelowych obliczeń bez konieczności zmiany skryptu. 

Wystarczy zdefiniować środowisko dla każdego elementu docelowego obliczeń z **konfiguracją uruchomieniową**.  Następnie, gdy chcesz uruchomić eksperyment szkoleniowy w innym miejscu docelowym obliczeń, określ konfigurację uruchamiania dla tego obliczenia.

Dowiedz się [](#submit) więcej o przesyłaniu eksperymentów na końcu tego artykułu.

### <a name="manage-environment-and-dependencies"></a>Zarządzanie środowiskiem i zależnościami

Podczas tworzenia konfiguracji uruchamiania należy zdecydować, jak zarządzać środowiskiem i zależnościami w obiekcie docelowym obliczeń. 

#### <a name="system-managed-environment"></a>System zarządzany środowiska

Użyj środowiska zarządzanego przez system, jeśli chcesz, aby [Conda](https://conda.io/docs/) zarządzać środowiskiem Python i zależnościami skryptów. Domyślnie przyjęto środowisko zarządzane przez system i najbardziej typowy wybór. Jest to przydatne w przypadku zdalnych obiektów docelowych obliczeń, szczególnie w przypadku, gdy nie można skonfigurować tego obiektu docelowego. 

Wszystko, co musisz zrobić, określa każdą zależność pakietu przy użyciu [klasy CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) , a następnie Conda tworzy plik o nazwie **conda_dependencies. yml** w katalogu **aml_config** w obszarze roboczym z listą zależności pakietu i konfiguruje środowisko języka Python podczas przesyłania eksperymentu szkoleniowego. 

Początkowa konfiguracja nowego środowiska może potrwać kilka minut w zależności od rozmiaru wymaganych zależności. Tak długo, jak lista pakietów pozostaje niezmieniona, czas instalacji odbywa się tylko raz.
  
Poniższy kod przedstawia przykład środowiska zarządzanego przez system wymagające scikit-Dowiedz się:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Środowiska zarządzanego przez użytkownika

W przypadku środowiska zarządzanego przez użytkownika użytkownik jest odpowiedzialny za skonfigurowanie środowiska i zainstalowanie każdego pakietu potrzebnych do wykonywania skryptów szkoleniowych w miejscu docelowym obliczeń. Jeśli środowisko szkoleniowe zostało już skonfigurowane (na przykład na komputerze lokalnym), możesz pominąć krok instalacji, ustawiając `user_managed_dependencies` wartość true. Conda nie sprawdzi Twojego środowiska ani nie zainstaluje żadnego z nich.

Poniższy kod przedstawia przykład konfigurowania przebiegów szkoleniowych dla środowiska zarządzanego przez użytkownika:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]

## <a name="whats-an-estimator"></a>Co to jest szacowania?

Aby ułatwić uczenie modeli przy użyciu popularnych platform, zestaw SDK języka Python, Azure Machine Learning, zawiera alternatywne abstrakcyjne streszczenie, klasy szacowania. Ta klasa umożliwia łatwe konstruowanie konfiguracji uruchomieniowych. Można utworzyć i użyć generycznej [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptów szkoleniowych, które korzystają z wybranej platformy szkoleniowej (na przykład scikit-Learning).

W przypadku zadań PyTorch, TensorFlow i łańcucha, Azure Machine Learning również udostępnia odpowiednie [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)i [łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) szacowania, aby uprościć korzystanie z tych platform.

Aby uzyskać więcej informacji, zobacz [uczenie modeli ml z szacowania](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Co to jest potok ML?

Potoki ML umożliwiają optymalizację przepływu pracy dzięki prostoty, szybkości, przenośności i ponownego użycia. Podczas kompilowania potoków z Azure Machine Learning możesz skupić się na wiedzy, uczeniu maszynowym, a nie na infrastrukturze i automatyzacji.

Potoki ML są zbudowane z wielu **kroków**, które są odrębnymi jednostkami obliczeniowymi w potoku. Każdy krok można uruchamiać niezależnie i używać izolowanych zasobów obliczeniowych. Dzięki temu wiele analityków danych może współdziałać w tym samym potoku bez nadmiernie nieopodatkowanych zasobów obliczeniowych, a także ułatwia korzystanie z różnych typów i rozmiarów obliczeniowych dla każdego kroku.

> [!TIP]
> Potoki ML mogą korzystać z konfiguracji uruchamiania lub szacowania podczas uczenia modeli.

Chociaż potoki ML mogą szkolić modele, mogą również przygotowywać dane przed szkoleniem i wdrażać modele po szkoleniu. Jednym z głównych przypadków użycia potoków jest ocenianie wsadowe. Aby uzyskać więcej informacji, [Zobacz potoki: Optymalizuj przepływy pracy](concept-ml-pipelines.md)uczenia maszynowego.

## <a name="set-up-in-python"></a>Konfiguracja w języku Python

Skorzystaj z poniższych sekcji, aby skonfigurować te elementy docelowe obliczeń:

* [Komputer lokalny](#local)
* [Usługi Azure Machine Learning obliczeń](#amlcompute)
* [Zdalne maszyny wirtualne](#vm)
* [Usługa Azure HDInsight](#hdinsight)


### <a id="local"></a>Komputer lokalny

1. **Utwórz i Dołącz**: Nie ma potrzeby tworzenia lub dołączania obiektu docelowego obliczeń, aby używać komputera lokalnego jako środowiska szkoleniowego.  

1. **Konfiguracja**:  Gdy komputer lokalny jest używany jako obiekt docelowy obliczeń, kod szkoleniowy jest uruchamiany w [środowisku deweloperskim](how-to-configure-environment.md).  Jeśli to środowisko ma już potrzebne pakiety języka Python, użyj środowiska zarządzanego przez użytkownika.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).

### <a id="amlcompute"></a>Usługi Azure Machine Learning obliczeń

Azure Machine Learning COMPUTE to infrastruktura obliczeniowa, która umożliwia użytkownikowi łatwe tworzenie obliczeń jednego lub wielowęzłowego. Obliczenia są tworzone w regionie obszaru roboczego jako zasób, który może być współużytkowany z innymi użytkownikami w obszarze roboczym. Obliczenia są skalowane automatycznie podczas przesyłania zadania i mogą być umieszczane w Virtual Network platformy Azure. Obliczenia są wykonywane w środowisku kontenerowym i pakiety zależności modelu w [kontenerze platformy Docker](https://www.docker.com/why-docker).

Aby rozdystrybuować procesu uczenia klastra procesora CPU lub GPU węzłów obliczeniowych w chmurze, można użyć obliczeniowego usługi Azure Machine Learning. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, które obejmują procesory GPU, zobacz [rozmiary maszyny wirtualnej zoptymalizowanej według procesora GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning COMPUTE ma limity domyślne, takie jak liczba rdzeni, które można przydzielić. Aby uzyskać więcej informacji, zobacz [Zarządzanie przydziałami zasobów platformy Azure i ich żądania](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Środowisko obliczeniowe Azure Machine Learning można utworzyć na żądanie po zaplanowaniu uruchomienia lub jako zasób trwały.

#### <a name="run-based-creation"></a>Tworzenie opartych na przebieg

Azure Machine Learning obliczeń można utworzyć jako obiekt docelowy obliczeń w czasie wykonywania. Obliczenia są tworzone automatycznie dla danego przebiegu. Obliczenia są usuwane automatycznie po zakończeniu przebiegu. 

> [!NOTE]
> Aby określić maksymalną liczbę węzłów, które mają być używane, zwykle ustawiono `node_count` liczbę węzłów. Obecnie istnieje (04/04/2019) usterka, która uniemożliwia korzystanie z tej usługi. Aby obejść ten element, `amlcompute._cluster_max_node_count` należy użyć właściwości konfiguracji uruchomieniowej. Na przykład `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> Tworzenie Azure Machine Learning obliczeń opartych na uruchomieniu jest obecnie dostępne w wersji zapoznawczej. Nie należy używać tworzenia opartego na uruchomieniach, jeśli jest używane automatyczne dostrajanie parametrów lub automatyczne Uczenie maszynowe. Aby skorzystać z dostrajania podparametru lub automatycznej uczenia maszynowego, należy zamiast tego utworzyć trwały element docelowy [obliczeń](#persistent) .

1.  **Tworzenie, dołączanie i Konfigurowanie**: W przypadku tworzenia, dołączania i konfigurowania obiektu docelowego obliczeń przy użyciu konfiguracji przebiegu wykonywane są wszystkie czynności niezbędne do utworzenia, dołączenia i skonfigurowania celu.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).

#### <a id="persistent"></a>Trwałe obliczenia

Trwałe Obliczanie Azure Machine Learning może być ponownie używane między zadaniami. Obliczenia mogą być współużytkowane z innymi użytkownikami w obszarze roboczym i są przechowywane między zadaniami.

1. **Utwórz i Dołącz**: Aby utworzyć trwały Azure Machine Learning zasobów obliczeniowych w języku Python, określ właściwości **vm_size** i **max_nodes** . Azure Machine Learning następnie używa inteligentnych ustawień domyślnych dla innych właściwości. Obliczenia są skalowane automatycznie do zerowych węzłów, gdy nie są używane.   Dedykowane maszyny wirtualne są tworzone w celu uruchamiania zadań zgodnie z potrzebami.
    
    * **vm_size**: Rodzina maszyn wirtualnych węzłów utworzonych przez Azure Machine Learning COMPUTE.
    * **max_nodes**: Maksymalna liczba węzłów do automatycznego skalowania w górę do momentu uruchomienia zadania na Azure Machine Learning obliczeń.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Podczas tworzenia Azure Machine Learning obliczeń można także skonfigurować kilka zaawansowanych właściwości. Właściwości umożliwiają tworzenie trwałego klastra o stałym rozmiarze lub w ramach istniejącej Virtual Network platformy Azure w ramach subskrypcji.  Aby uzyskać [szczegółowe informacje](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) , zobacz klasę AmlCompute.
    
   Można też utworzyć i dołączyć trwały zasób obliczeniowy Azure Machine Learning [w Azure Portal](#portal-create).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla trwałego elementu docelowego obliczeń.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).


### <a id="vm"></a>Zdalne maszyny wirtualne

Usługa Azure Machine Learning obsługuje również wprowadzenie własnych zasobów obliczeniowych i dołączania ich do obszaru roboczego. Jeden taki typ zasobu jest dowolną zdalną maszyną wirtualną, o ile jest dostępny z usługi Azure Machine Learning. Zasób może być maszyną wirtualną platformy Azure, serwerem zdalnym w organizacji lub lokalnym. W odniesieniu do adresów IP i poświadczeń (nazwy użytkownika i hasła lub klucza SSH) można użyć dowolnej dostępnej maszyny wirtualnej do zdalnego uruchomienia.

Można użyć środowiska conda tworzonym przez system, już istniejące środowisko Python lub kontenera Docker. Aby można było wykonać operację na kontenerze platformy Docker, na maszynie wirtualnej musi być uruchomiony aparat platformy Docker. Ta funkcja jest szczególnie przydatne w przypadku, gdy chcesz środowiska bardziej elastyczne, oparte na chmurze dev/eksperymentowanie w usłudze niż komputer lokalny.

W tym scenariuszu Użyj usługi Azure Data Science Virtual Machine (DSVM) jako maszyny wirtualnej platformy Azure. Ta maszyna wirtualna jest wstępnie skonfigurowanym środowiskiem programistycznym do analizy danych i AI na platformie Azure. Maszyna wirtualna oferuje świadome wybór narzędzi i struktur na potrzeby tworzenia uczenia maszynowego w pełnym cyklu życia. Aby uzyskać więcej informacji na temat używania DSVM z Azure Machine Learning, zobacz [Konfigurowanie środowiska deweloperskiego](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Utwórz**: Utwórz element DSVM przed użyciem go do uczenia modelu. Aby utworzyć ten zasób, zapoznaj [się z tematem obsługa Data Science Virtual Machine dla systemu Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu. Podczas tworzenia maszyny wirtualnej lub wybrania istniejącej maszyny wirtualnej należy wybrać maszynę wirtualną, która używa Ubuntu.

1. **Dołącz**: Aby dołączyć istniejącą maszynę wirtualną jako obiekt docelowy obliczeń, należy podać w pełni kwalifikowaną nazwę domeny (FQDN), nazwę użytkownika i hasło dla maszyny wirtualnej. W przykładzie Zastąp \<wartość FQDN > nazwą publicznej nazwy FQDN maszyny wirtualnej lub publicznym adresem IP. Zastąp \<> nazwy \<użytkownika i hasła > nazwą użytkownika ssh i hasłem dla maszyny wirtualnej.

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

   Możesz też dołączyć DSVM do obszaru roboczego [przy użyciu Azure Portal](#portal-reuse).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeń DSVM. Platformy Docker i Conda są używane do tworzenia i konfigurowania środowiska szkoleniowego na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).

### <a id="hdinsight"></a>Usługa Azure HDInsight 

Usługa Azure HDInsight to popularna platforma do analizy danych Big Data. Platforma zapewnia Apache Spark, która może służyć do uczenia modelu.

1. **Utwórz**:  Utwórz klaster usługi HDInsight przed użyciem go do uczenia modelu. Aby utworzyć klaster platformy Spark w usłudze HDInsight, zobacz [Tworzenie klastra Spark w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Podczas tworzenia klastra należy określić nazwę użytkownika i hasło SSH. Zwróć uwagę na te wartości, ponieważ są one potrzebne do korzystania z usługi HDInsight jako elementu docelowego obliczeń.
    
    Po utworzeniu klastra Połącz się z nim za pomocą nazwy hosta \<ClusterName >-SSH.azurehdinsight.NET, gdzie \<ClusterName > jest nazwą dostarczoną dla klastra. 

1. **Dołącz**: Aby dołączyć klaster usługi HDInsight jako obiekt docelowy obliczeń, należy podać nazwę hosta, nazwa użytkownika i hasło dla klastra usługi HDInsight. W poniższym przykładzie użyto zestawu SDK, aby dołączyć klaster z obszarem roboczym. W przykładzie Zastąp \<wartość ClusterName > nazwą klastra. Zastąp \<> nazwy \<użytkownika i hasła > nazwą użytkownika i hasłem SSH dla klastra.

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

   Można też dołączyć klaster usługi HDInsight do obszaru roboczego [przy użyciu Azure Portal](#portal-reuse).

1. **Konfiguracja**: Utwórz konfigurację uruchomieniową dla elementu docelowego obliczeń HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Teraz, po dołączeniu obliczeń i skonfigurowaniu przebiegu, następnym krokiem jest [przesłanie tego przebiegu szkoleniowego](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch służy do wydajnego uruchamiania aplikacji równoległych i o wysokiej wydajności obliczeniowych (HPC) w chmurze. AzureBatchStep można użyć w potoku Azure Machine Learning do przesyłania zadań do Azure Batch puli maszyn.

Aby dołączyć Azure Batch jako obiekt docelowy obliczeń, należy użyć zestawu SDK Azure Machine Learning i podać następujące informacje:

-   **Nazwa obliczenia Azure Batch**: Przyjazna nazwa, która ma być używana na potrzeby obliczeń w obszarze roboczym
-   **Nazwa konta Azure Batch**: Nazwa konta Azure Batch
-   **Grupa zasobów**: Grupa zasobów zawierająca konto Azure Batch.

Poniższy kod ilustruje sposób dołączania Azure Batch jako obiekt docelowy obliczeń:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-portal"></a>Konfiguracja w Azure Portal

Możesz uzyskać dostęp do obiektów docelowych obliczeń skojarzonych z obszarem roboczym w Azure Portal.  Możesz użyć portalu, aby:

* [Wyświetl cele obliczeń](#portal-view) dołączone do obszaru roboczego
* [Tworzenie obiektu docelowego obliczeń](#portal-create) w obszarze roboczym
* [Dołącz obiekt docelowy obliczeń](#portal-reuse) , który został utworzony poza obszarem roboczym

Po utworzeniu elementu docelowego i dołączeniu go do obszaru roboczego będzie on używany w konfiguracji przebiegu z `ComputeTarget` obiektem: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Wyświetl cele obliczeń


Aby zobaczyć cele obliczeń dla obszaru roboczego, wykonaj następujące czynności:

1. Przejdź do [Azure Portal](https://portal.azure.com) i Otwórz obszar roboczy. 
1. W obszarze __aplikacje__wybierz pozycję __obliczenia__.

    ![Karta obliczeń widoku](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Tworzenie obiektu docelowego obliczeń

Wykonaj poprzednie kroki, aby wyświetlić listę elementów docelowych obliczeń. Następnie wykonaj następujące kroki, aby utworzyć obiekt docelowy obliczeń: 

1. Wybierz znak plus (+), aby dodać obiekt docelowy obliczeń.

    ![Dodaj element docelowy obliczeń](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Wprowadź nazwę dla elementu docelowego obliczeń. 

1. Wybierz **środowisko obliczeniowe usługi Machine Learning** jako typ obliczeń do użycia na potrzeby __szkolenia__. 

    >[!NOTE]
    >Azure Machine Learning COMPUTE to jedyny zasób obliczeniowy, który można utworzyć w Azure Portal.  Wszystkie inne zasoby obliczeniowe mogą być dołączane po ich utworzeniu.

1. Wypełnij formularz. Podaj wartości dla wymaganych właściwości, szczególnie **rodziny maszyn wirtualnych**i **maksymalną liczbę węzłów** do użycia w celu uruchomienia obliczeń.  

    ![Wypełnij formularz](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Wybierz pozycję __Utwórz__.


1. Wyświetl stan operacji tworzenia, wybierając obiekt docelowy obliczeń z listy:

    ![Wybierz element docelowy obliczeń, aby wyświetlić stan operacji tworzenia](./media/how-to-set-up-training-targets/View_list.png)

1. Zobaczysz szczegóły dotyczące obiektu docelowego obliczeń: 

    ![Wyświetl szczegóły komputera docelowego](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Dołącz cele obliczeń

Aby używać obiektów docelowych obliczeń utworzonych poza obszarem roboczym usługi Azure Machine Learning, należy je dołączyć. Dołączenie obiektu docelowego obliczeń sprawia, że jest on dostępny dla Twojego obszaru roboczego.

Wykonaj kroki opisane wcześniej, aby wyświetlić listę elementów docelowych obliczeń. Następnie wykonaj następujące kroki, aby dołączyć obiekt docelowy obliczeń: 

1. Wybierz znak plus (+), aby dodać obiekt docelowy obliczeń. 
1. Wprowadź nazwę dla elementu docelowego obliczeń. 
1. Wybierz typ obliczeń do dołączenia do __szkolenia__:

    > [!IMPORTANT]
    > Nie wszystkie typy obliczeniowe mogą być dołączane z Azure Portal. Typy obliczeń, które są obecnie dołączone do szkolenia, obejmują:
    >
    > * Zdalna maszyna wirtualna
    > * Azure Databricks (do użycia w potokach uczenia maszynowego)
    > * Azure Data Lake Analytics (do użycia w potokach uczenia maszynowego)
    > * Azure HDInsight

1. Wypełnij formularz i podaj wartości wymaganych właściwości.

    > [!NOTE]
    > Firma Microsoft zaleca korzystanie z kluczy SSH, które są bezpieczniejsze niż hasła. Hasła są podatne na ataki z wymuszeniem. Klucze SSH korzystają z podpisów kryptograficznych. Aby uzyskać informacje na temat sposobu tworzenia kluczy SSH do użycia z usługą Azure Virtual Machines, zobacz następujące dokumenty:
    >
    > * [Tworzenie i używanie kluczy SSH w systemie Linux lub macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Tworzenie i używanie kluczy SSH w Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Wybierz __dołączyć__. 
1. Wyświetl stan operacji dołączania, wybierając obiekt docelowy obliczeń z listy.

## <a name="set-up-with-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia

Możesz uzyskać dostęp do obiektów docelowych obliczeń skojarzonych z obszarem roboczym przy użyciu [rozszerzenia interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) dla usługi Azure Machine Learning.  Interfejsu wiersza polecenia można użyć do:

* Tworzenie zarządzanego obiektu docelowego obliczeń
* Aktualizowanie zarządzanego obiektu docelowego obliczeń
* Dołącz niezarządzany cel obliczeń

Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Konfiguracja przy użyciu VS Code

Możesz uzyskiwać dostęp do obiektów docelowych obliczeń skojarzonych z obszarem roboczym i zarządzać nimi, korzystając z [rozszerzenia vs Code](how-to-vscode-tools.md#create-and-manage-compute-targets) dla usługi Azure Machine Learning.

## <a id="submit"></a>Prześlij przebieg szkoleniowy

Po utworzeniu konfiguracji przebiegu należy użyć jej do uruchomienia eksperymentu.  Wzorzec kodu do przesyłania przebiegu szkoleniowego jest taki sam dla wszystkich typów obiektów docelowych obliczeń:

1. Utwórz eksperyment do uruchomienia
1. Prześlij przebiegu.
1. Poczekaj, aż działanie zakończyć.

> [!IMPORTANT]
> Po przesłaniu przebiegu szkoleniowego tworzona jest migawka katalogu zawierającego skrypty szkoleniowe i wysyłane do obiektu docelowego obliczeń. Jest on również przechowywany w ramach eksperymentu w obszarze roboczym. Jeśli zmienisz pliki i prześlesz ponownie uruchomienie, zostaną przekazane tylko zmienione pliki.
>
> Aby uniemożliwić Dołączanie plików do migawki, Utwórz plik [. gitignore](https://git-scm.com/docs/gitignore) lub `.amlignore` w katalogu i Dodaj do niego pliki. Plik używa tej samej składni i wzorców co plik [. gitignore.](https://git-scm.com/docs/gitignore) `.amlignore` Jeśli istnieją oba pliki, `.amlignore` ma pierwszeństwo.
> 
> Aby uzyskać więcej informacji, zobacz [migawki](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Najpierw utwórz eksperyment w obszarze roboczym.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Przesyłanie eksperymentu

Prześlij eksperyment z `ScriptRunConfig` obiektem.  Ten obiekt obejmuje:

* **katalog_źródłowy**: Katalog źródłowy zawierający skrypt szkoleniowy
* **skrypt**: Identyfikowanie skryptu szkoleniowego
* **run_config**: Konfiguracja uruchamiania, która z kolei definiuje, gdzie nastąpi szkolenie.

Na przykład, aby użyć konfiguracji [lokalnej lokalizacji docelowej](#local) :

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Przełączenie tego samego eksperymentu w celu uruchomienia go w innym miejscu docelowym obliczeń przy użyciu innej konfiguracji uruchomieniowej, takiej jak [amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Możesz też:

* Prześlij eksperyment z `Estimator` obiektem, jak pokazano w [pouczeniu modeli ml z szacowania](how-to-train-ml-models.md).
* Prześlij eksperyment [przy użyciu rozszerzenia interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md#experiments).
* Prześlij eksperyment za pośrednictwem [rozszerzenia vs Code](how-to-vscode-tools.md#train-and-tune-models).

## <a name="github-tracking-and-integration"></a>Śledzenie i integracja z usługą GitHub

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Na przykład bieżący identyfikator zatwierdzenia dla repozytorium jest rejestrowany jako część historii.

## <a name="notebook-examples"></a>Przykłady notesu

Zobacz te notesy, aby poznać przykłady szkoleń z różnymi obiektami docelowymi obliczeń:
* [jak-to-użyj-usługi Azure ml/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Samouczki/img klasyfikacji — część 1 — training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) , aby tworzyć lepsze modele.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* Wyświetl odwołanie do zestawu SDK [klasy RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) .
* [Korzystanie z usługi Azure Machine Learning z sieciami wirtualnymi platformy Azure](how-to-enable-virtual-network.md)
