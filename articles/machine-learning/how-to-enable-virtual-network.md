---
title: Bezpieczne eksperymenty i wnioskowanie w sieci wirtualnej
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zabezpieczyć zadania eksperymentów/szkoleń oraz zadania wnioskowania/oceniania w Azure Machine Learning w ramach Virtual Network platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: 53644066276aa8e9fb57b4802142bca3fe4b342f
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760859"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Zabezpieczanie zadań eksperymentowania i wnioskowania usługi Azure ML w ramach Virtual Network platformy Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zabezpieczyć zadania eksperymentowania/szkolenia oraz zadania wnioskowania/oceniania w Azure Machine Learning w ramach Virtual Network platformy Azure.

**Sieć wirtualna** działa jako granica zabezpieczeń, izolowanie zasobów platformy Azure od publicznego Internetu. Możesz również dołączyć do sieci wirtualnej platformy Azure do sieci lokalnej. Dzięki dołączeniu sieci można bezpiecznie uczenie modeli i uzyskać dostęp do wdrożonych modeli w celu wnioskowania.

Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe lub [obiekty docelowe obliczeń](concept-compute-target.md)są używane do uczenia i wdrażania modeli. Obiekty docelowe można tworzyć w ramach sieci wirtualnej. Na przykład można użyć programu Microsoft Data Science Virtual Machine do uczenia modelu, a następnie wdrożenia modelu w usłudze Azure Kubernetes Service (AKS). Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Omówienie usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Ten artykuł zawiera również szczegółowe informacje dotyczące *zaawansowanych ustawień zabezpieczeń*, informacji, które nie są niezbędne dla podstawowych lub eksperymentalnych przypadków użycia. Niektóre sekcje tego artykułu zawierają informacje o konfiguracji dla różnych scenariuszy. Nie musisz wykonywać instrukcji w kolejności ani w całości.

> [!TIP]
> O ile nie zostało to określone, używanie zasobów, takich jak konta magazynu lub obiekty docelowe obliczeń w sieci wirtualnej, będzie współdziałać z potokami uczenia maszynowego i niepotokowymi przepływami pracy, takimi jak uruchomienia skryptu.

> [!WARNING]
> Firma Microsoft nie obsługuje korzystania z programu Azure Machine Learning Designer ani automatycznego uczenia maszynowego (z programu Studio) z zasobami w sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Obszar roboczy](how-to-manage-workspace.md)Azure Machine Learning.

+ Ogólna wiedza o działaniu [usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i [sieci IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Wstępnie istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

## <a name="use-a-storage-account-for-your-workspace"></a>Korzystanie z konta magazynu dla obszaru roboczego

Aby użyć konta usługi Azure Storage dla obszaru roboczego w sieci wirtualnej, wykonaj następujące czynności:

1. Utwórz zasób obliczeniowy (na przykład wystąpienie obliczeniowe Machine Learning lub klaster) za siecią wirtualną lub Dołącz zasób obliczeniowy do obszaru roboczego (na przykład klaster usługi HDInsight, maszyna wirtualna lub klaster usług Azure Kubernetes). Zasób obliczeniowy może być przeznaczony do eksperymentowania lub wdrażania modelu.

   Aby uzyskać więcej informacji, zobacz sekcję [Korzystanie z Machine Learning obliczeń](#amlcompute), [Używanie maszyny wirtualnej lub klastra usługi HDInsight](#vmorhdi)oraz korzystanie z sekcji [Azure Kubernetes Service](#aksvnet) w tym artykule.

1. W Azure Portal przejdź do magazynu dołączonego do obszaru roboczego.

   [![magazyn, który jest dołączony do obszaru roboczego Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stronie **Magazyn platformy Azure** wybierz pozycję __zapory i sieci wirtualne__.

   ![Obszar "zapory i sieci wirtualne" na stronie usługi Azure Storage w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stronie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    - Wybierz pozycję __Wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz łącze __Dodaj istniejące sieci wirtualne__ . Ta akcja powoduje dodanie sieci wirtualnej, w której znajdują się obliczenia (zobacz krok 1).

        > [!IMPORTANT]
        > Konto magazynu musi znajdować się w tej samej sieci wirtualnej co wystąpienia obliczeniowe lub klastry używane do uczenia lub wnioskowania.

    - Zaznacz pole wyboru __Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu__ .

    > [!IMPORTANT]
    > Podczas pracy z zestawem SDK Azure Machine Learning środowisko programistyczne musi mieć możliwość nawiązania połączenia z kontem usługi Azure Storage. Gdy konto magazynu znajduje się w sieci wirtualnej, zapora musi zezwalać na dostęp ze swojego adresu IP środowiska deweloperskiego.
    >
    > Aby włączyć dostęp do konta magazynu, odwiedź __zapory i sieci wirtualne__ dla konta magazynu *z przeglądarki sieci Web na kliencie deweloperskim*. Następnie użyj pola wyboru __Dodaj adres IP klienta__ , aby dodać adres IP klienta do __zakresu adresów__. Możesz również użyć pola __zakres adresów__ , aby ręcznie wprowadzić adres IP środowiska deweloperskiego. Po dodaniu adresu IP klienta może on uzyskać dostęp do konta magazynu przy użyciu zestawu SDK.

   [![okienku "zapory i sieci wirtualne" w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> _Konto magazynu domyślnego_ można umieścić dla Azure Machine Learning lub _kont magazynu innych niż domyślne_ w sieci wirtualnej.
>
> Domyślne konto magazynu jest automatycznie inicjowane podczas tworzenia obszaru roboczego.
>
> W przypadku kont magazynu innych niż domyślne parametr `storage_account` w [funkcji`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) umożliwia określenie niestandardowego konta magazynu według identyfikatora zasobu platformy Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Korzystanie z Azure Data Lake Storage generacji 2

Azure Data Lake Storage Gen 2 to zestaw funkcji do analizy danych Big Data, opartych na usłudze Azure Blob Storage. Może służyć do przechowywania danych używanych do uczenia modeli przy użyciu Azure Machine Learning. 

Aby użyć Data Lake Storage generacji 2 w sieci wirtualnej obszaru roboczego Azure Machine Learning, wykonaj następujące czynności:

1. Utwórz konto Azure Data Lake Storage Gen 2. Aby uzyskać więcej informacji, zobacz [Tworzenie konta magazynu Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Wykonaj kroki 2-4 w poprzedniej sekcji, [Użyj konta magazynu dla obszaru roboczego](#use-a-storage-account-for-your-workspace), aby umieścić konto w sieci wirtualnej.

Korzystając z Azure Machine Learning z Data Lake Storage Gen 2 w sieci wirtualnej, należy użyć następujących wskazówek:

* Jeśli używasz __zestawu SDK do tworzenia zestawu danych__, a system z uruchomionym kodem __nie znajduje się w sieci wirtualnej__, użyj parametru `validate=False`. Ten parametr pomija walidację, która kończy się niepowodzeniem, jeśli system nie znajduje się w tej samej sieci wirtualnej co konto magazynu. Aby uzyskać więcej informacji, zobacz metodę [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) .

* W przypadku używania wystąpienia obliczeniowego Azure Machine Learning lub klastra obliczeniowego do uczenia modelu przy użyciu zestawu danych musi on znajdować się w tej samej sieci wirtualnej co konto magazynu.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Korzystanie z wystąpienia magazynu kluczy z obszarem roboczym

Wystąpienie magazynu kluczy skojarzone z obszarem roboczym jest używane przez Azure Machine Learning do przechowywania następujących poświadczeń:
* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia do magazynów danych

Aby korzystać z funkcji eksperymentowania Azure Machine Learning z Azure Key Vault za siecią wirtualną, wykonaj następujące czynności:

1. Przejdź do magazynu kluczy skojarzonego z obszarem roboczym.

   [![magazynu kluczy skojarzonego z obszarem roboczym Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na stronie **Key Vault** w lewym okienku wybierz pozycję __zapory i sieci wirtualne__.

   ![Sekcja "zapory i sieci wirtualne" w okienku Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stronie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    - W obszarze __Zezwalaj na dostęp z__, wybierz opcję __wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz pozycję __Dodaj istniejące sieci wirtualne__ , aby dodać sieć wirtualną, w której znajduje się obliczenie eksperymentu.
    - W obszarze __Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory__wybierz pozycję __tak__.

   [![sekcji "zapory i sieci wirtualne" w okienku Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>Użyj środowisko obliczeniowe usługi Machine Learning

> [!NOTE]
> Wystąpienia obliczeniowe (wersja zapoznawcza) są obecnie dostępne tylko dla obszarów roboczych z regionem **Północno-środkowe stany USA** lub **Południowe Zjednoczone Królestwo**z obsługą innych regionów już wkrótce.
> Użyj jednego z tych regionów, aby utworzyć wystąpienie obliczeniowe, które można dodać do sieci wirtualnej.

Aby użyć wystąpienia obliczeniowego Azure Machine Learning lub klastra obliczeniowego w sieci wirtualnej, należy spełnić następujące wymagania dotyczące sieci:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
> * Podsieć określona dla wystąpienia obliczeniowego lub klastra musi mieć wystarczającą liczbę nieprzypisanych adresów IP w celu uwzględnienia liczby elementów, które są przeznaczone dla maszyny wirtualnej. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster obliczeniowy zostanie częściowo przydzielony.
> * Sprawdź, czy zasady zabezpieczeń lub blokady w ramach subskrypcji lub grupy zasobów sieci wirtualnej ograniczają uprawnienia do zarządzania siecią wirtualną. Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu, pozostaw kilka otwartych portów dla usługi obliczeniowej. Aby uzyskać więcej informacji, zobacz sekcję [wymagane porty](#mlcports) .
> * Jeśli chcesz umieścić wiele wystąpień obliczeniowych lub klastrów w jednej sieci wirtualnej, może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z zasobów.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co Azure Machine Learning wystąpienie obliczeniowe lub klaster. Jeśli tworzysz wystąpienie obliczeniowe w tej samej sieci wirtualnej, konieczne będzie odłączenie kont magazynu z sieci wirtualnej, utworzenie wystąpienia obliczeniowego w sieci wirtualnej, a następnie dołączenie kont magazynu z powrotem do sieci wirtualnej.

Wystąpienie obliczeniowe Machine Learning lub klaster automatycznie przydziela dodatkowe zasoby sieciowe do grupy zasobów zawierającej sieć wirtualną. Dla każdego wystąpienia obliczeniowego lub klastra usługa przydziela następujące zasoby:

* Jedna sieciowa Grupa zabezpieczeń
* Jeden publiczny adres IP
* Jeden moduł równoważenia obciążenia

Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) subskrypcji.


### <a id="mlcports"></a>Wymagane porty

Środowisko obliczeniowe usługi Machine Learning obecnie używa usługi Azure Batch do aprowizacji maszyn wirtualnych w określonej sieci wirtualnej. Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch. Ta komunikacja służy do planowania przebiegów w węzłach środowisko obliczeniowe usługi Machine Learning oraz do komunikowania się z usługą Azure Storage i innymi zasobami. Usługa Batch dodaje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) na poziomie interfejsów sieciowych dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Ruch przychodzący TCP na portach 29876 i 29877 z __tagu usługi__ __BatchNodeManagement__.

    ![Reguła przychodząca korzystająca z tagu usługi BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Obowiązkowe Ruch przychodzący TCP na porcie 22, aby zezwolić na dostęp zdalny. Tego portu należy używać tylko w przypadku, gdy chcesz nawiązać połączenie przy użyciu protokołu SSH w publicznym adresie IP.

- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu.

- Dla ruchu przychodzącego TCP wystąpienia obliczeniowego na porcie 44224 z __tagu usługi__ __AzureMachineLearning__.

Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli sieciowej grupy zabezpieczeń blokuje komunikację z węzłami obliczeniowymi, usługa COMPUTE ustawia stan węzłów obliczeniowych na niezdatny do użytku.

Nie musisz określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Azure Batch konfiguruje własny sieciowych grup zabezpieczeń. Jeśli jednak określona podsieć ma skojarzone sieciowych grup zabezpieczeń lub zaporę, skonfiguruj reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego wymienione wcześniej.

Konfiguracja reguły sieciowej grupy zabezpieczeń w Azure Portal przedstawiono na następujących obrazach:

[![reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Wychodzące reguły sieciowej grupy zabezpieczeń dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Ogranicz łączność wychodzącą z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, wykonaj następujące czynności:

- Odmowa wychodzącego połączenia internetowego przy użyciu reguł sieciowej grupy zabezpieczeń.

- W przypadku __wystąpienia obliczeniowego__ lub __klastra obliczeniowego__należy ograniczyć ruch wychodzący do następujących elementów:
   - Azure Storage, korzystanie z __tagu usługi__ __magazynu__
   - Azure Container Registry, przy użyciu __tagu usługi__ __AzureContainerRegistry__
   - Azure Machine Learning, przy użyciu __tagu usługi__ __AzureMachineLearning__
   
- W przypadku __wystąpienia obliczeniowego__Dodaj również następujące elementy:
   - Azure Resource Manager, przy użyciu __tagu usługi__ __AzureResourceManager__
   - Azure Active Directory, przy użyciu __tagu usługi__ __usługi azureactivedirectory__

Konfiguracja reguły sieciowej grupy zabezpieczeń w Azure Portal jest pokazana na poniższym obrazie:

[![wychodzące reguły sieciowej grupy zabezpieczeń dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Jeśli planujesz użycie domyślnych obrazów platformy Docker dostarczonych przez firmę Microsoft i włączenie zależności zarządzanych przez użytkownika, należy również użyć __znacznika usługi__ __MicrosoftContainerRegistry. Region_Name__ (na przykład MicrosoftContainerRegistry. wschód).
>
> Ta konfiguracja jest konieczna, gdy kod jest podobny do następującego fragmentu kodu w ramach skryptów szkoleniowych:
>
> __Szkolenia RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Szkolenia szacowania__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

Jeśli używasz wymuszonego tunelowania przy użyciu środowisko obliczeniowe usługi Machine Learning, Dodaj [trasy zdefiniowane przez użytkownika (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsieci zawierającej zasób obliczeniowy.

* Ustanów UDR dla każdego adresu IP, który jest używany przez usługę Azure Batch w regionie, w którym znajdują się zasoby. Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi w celu planowania zadań. Aby uzyskać listę adresów IP usługi Batch, użyj jednej z następujących metod:

    * Pobierz [zakresy adresów IP i Tagi usług platformy Azure](https://www.microsoft.com/download/details.aspx?id=56519) , a następnie wyszukaj plik pod kątem `BatchNodeManagement.<region>`, gdzie `<region>` jest Twoim regionem świadczenia usługi Azure.

    * Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , aby pobrać te informacje. Poniższy przykład pobiera informacje o adresie IP i filtruje informacje dla regionu Wschodnie stany USA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Ruch wychodzący do usługi Azure Storage nie może być blokowany przez lokalne urządzenie sieciowe. W tym celu adresy URL mają postać `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`i `<account>.blob.core.windows.net`.

Po dodaniu UDR, zdefiniuj trasę dla każdego powiązanego prefiksu adresu IP partii i ustaw __Typ następnego przeskoku__ na __Internet__. Na poniższej ilustracji przedstawiono przykład tego UDR w Azure Portal:

![Przykład UDR dla prefiksu adresu](./media/how-to-enable-virtual-network/user-defined-route.png)

Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch w sieci wirtualnej](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Tworzenie klastra obliczeniowego w sieci wirtualnej

Aby utworzyć klaster środowisko obliczeniowe usługi Machine Learning, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz obszar roboczy Azure Machine Learning.

1. W sekcji __aplikacja__ wybierz pozycję __obliczenia__, a następnie wybierz pozycję __Dodaj obliczenia__.

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności:

    a. W obszarze __Konfiguracja sieci__wybierz pozycję __Zaawansowane__.

    b. Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.

    d. Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną, która zawiera podsieć.

    d. Z listy rozwijanej __podsieć__ wybierz podsieć, która ma zostać użyta.

   ![Ustawienia sieci wirtualnej dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Klaster środowisko obliczeniowe usługi Machine Learning można również utworzyć za pomocą zestawu Azure Machine Learning SDK. Poniższy kod tworzy nowy klaster środowisko obliczeniowe usługi Machine Learning w podsieci `default` sieci wirtualnej o nazwie `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po zakończeniu procesu tworzenia nauczysz model przy użyciu klastra w eksperymentie. Aby uzyskać więcej informacji, zobacz [Wybieranie i używanie elementu docelowego obliczeń do szkoleń](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Użyj Azure Databricks

Aby używać Azure Databricks w sieci wirtualnej z obszarem roboczym, muszą zostać spełnione następujące wymagania:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co klaster Azure Databricks.
> * Poza niektórymi __datakostki — prywatnymi__ i niestandardowymi __—__ podsieciami używanymi Azure Databricks, wymagana jest również __Domyślna__ podsieć utworzona dla sieci wirtualnej.

Aby uzyskać szczegółowe informacje na temat używania Azure Databricks z siecią wirtualną, zobacz [wdrażanie Azure Databricks w Virtual Network platformy Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Korzystanie z maszyny wirtualnej lub klastra usługi HDInsight

> [!IMPORTANT]
> Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu.

Aby użyć maszyny wirtualnej lub klastra usługi Azure HDInsight w sieci wirtualnej z obszarem roboczym, wykonaj następujące czynności:

1. Utwórz maszynę wirtualną lub klaster usługi HDInsight przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure i umieść klaster w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji zobacz następujące artykuły:
    * [Tworzenie sieci wirtualnych platformy Azure dla maszyn wirtualnych z systemem Linux i zarządzanie nimi](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Zwiększanie usługi HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Aby zezwolić Azure Machine Learning na komunikowanie się z portem SSH na maszynie wirtualnej lub w klastrze, skonfiguruj wpis źródła dla sieciowej grupy zabezpieczeń. Port SSH zazwyczaj jest portem 22. Aby zezwolić na ruch z tego źródła, wykonaj następujące czynności:

    * Z listy rozwijanej __Źródło__ wybierz pozycję __tag usługi__.

    * Z listy rozwijanej __tag usługi źródłowej__ wybierz pozycję __AzureMachineLearning__.

    * Z listy rozwijanej __zakresy portów źródłowych__ wybierz pozycję __*__ .

    * Z listy rozwijanej __Lokalizacja docelowa__ wybierz __dowolne__.

    * Z listy rozwijanej __zakresy portów docelowych__ wybierz pozycję __22__.

    * W obszarze __Protokół__wybierz opcję __dowolny__.

    * W obszarze __Akcja__wybierz pozycję __Zezwalaj__.

   ![Reguły ruchu przychodzącego na potrzeby przeprowadzania eksperymentów na maszynie wirtualnej lub w klastrze usługi HDInsight w sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, zobacz sekcję [ograniczanie łączności wychodzącej z sieci wirtualnej](#limiting-outbound-from-vnet) .

1. Dołącz maszynę wirtualną lub klaster usługi HDInsight do obszaru roboczego Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Korzystanie z usługi Azure Kubernetes Service (AKS)

Aby dodać AKS w sieci wirtualnej do obszaru roboczego, wykonaj następujące czynności:

> [!IMPORTANT]
> Przed rozpoczęciem poniższej procedury postępuj zgodnie z wymaganiami wstępnymi w temacie [Konfigurowanie zaawansowanej sieci w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) How-to i planowanie adresowania IP klastra.
>
> Wystąpienie AKS i Sieć wirtualna platformy Azure muszą znajdować się w tym samym regionie. W przypadku zabezpieczenia kont usługi Azure Storage używanych przez obszar roboczy w sieci wirtualnej, muszą one znajdować się w tej samej sieci wirtualnej co wystąpienie AKS.

1. W [Azure Portal](https://portal.azure.com)upewnij się, że sieciowej grupy zabezpieczeń kontrolujący sieć wirtualną ma regułę ruchu przychodzącego, która jest włączona dla Azure Machine Learning przy użyciu __AzureMachineLearning__ jako **źródła**.

    [![Azure Machine Learning Dodaj okienko obliczania](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Wybierz obszar roboczy Azure Machine Learning.

1. W sekcji __aplikacja__ wybierz pozycję __obliczenia__, a następnie wybierz pozycję __Dodaj obliczenia__.

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności:

    - W obszarze __Konfiguracja sieci__wybierz pozycję __Zaawansowane__.

    - Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.

    - Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną, która zawiera podsieć.

    - Z listy rozwijanej __podsieć__ wybierz podsieć.

    - W polu __zakres adresów usługi Kubernetes__ Wprowadź zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP notacji CIDR (Classless Inter-Domain Routing) do definiowania adresów IP, które są dostępne dla klastra. Nie może się nakładać na żadne zakresy adresów IP podsieci (na przykład 10.0.0.0/16).

    - W polu __adres IP usługi KUBERNETES DNS__ wprowadź adres IP usługi DNS Kubernetes. Ten adres IP jest przypisywany do usługi DNS Kubernetes. Musi ona należeć do zakresu adresów usługi Kubernetes (na przykład 10.0.0.10).

    - W polu __adres mostka platformy Docker__ wprowadź adres mostka platformy Docker. Ten adres IP jest przypisany do mostka platformy Docker. Nie może być w żadnym z zakresów adresów IP podsieci lub zakres adresów usługi Kubernetes (na przykład 172.17.0.1/16).

   ![Azure Machine Learning: środowisko obliczeniowe usługi Machine Learning ustawień sieci wirtualnej](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Upewnij się, że grupa sieciowej grupy zabezpieczeń kontrolująca sieć wirtualną ma włączoną regułę zabezpieczeń dla punktu końcowego oceniania, tak aby można ją było wywołać spoza sieci wirtualnej.
   > [!IMPORTANT]
   > Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![reguły zabezpieczeń dla ruchu przychodzącego](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Możesz również użyć zestawu SDK Azure Machine Learning, aby dodać usługę Azure Kubernetes w sieci wirtualnej. Jeśli masz już klaster AKS w sieci wirtualnej, dołącz go do obszaru roboczego, zgodnie z opisem w artykule [wdrażanie w AKS](how-to-deploy-and-where.md). Poniższy kod tworzy nowe wystąpienie AKS w podsieci `default` sieci wirtualnej o nazwie `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Po zakończeniu procesu tworzenia można uruchomić wnioskowanie lub ocenianie modelu w klastrze AKS za siecią wirtualną. Aby uzyskać więcej informacji, zobacz [How to Deploy to AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Używanie prywatnych adresów IP w usłudze Azure Kubernetes Service

Domyślnie publiczny adres IP jest przypisywany do wdrożeń AKS. W przypadku używania AKS wewnątrz sieci wirtualnej można zamiast tego użyć prywatnego adresu IP. Prywatne adresy IP są dostępne tylko z wewnątrz sieci wirtualnej lub połączonych sieci.

Prywatny adres IP jest włączony przez skonfigurowanie AKS do korzystania z _wewnętrznego modułu równoważenia obciążenia_. 

> [!IMPORTANT]
> Nie można włączyć prywatnego adresu IP podczas tworzenia klastra usługi Azure Kubernetes. Musi być włączona jako aktualizacja istniejącego klastra.

Poniższy fragment kodu przedstawia sposób **tworzenia nowego klastra AKS**, a następnie aktualizowania go do korzystania z prywatnego modułu równoważenia obciążenia IP/wewnętrznego:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Interfejs wiersza polecenia platformy Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Zawartość pliku `body.json`, do którego odwołuje się polecenie, jest podobna do następującego dokumentu JSON:

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> Obecnie nie można skonfigurować modułu równoważenia obciążenia podczas wykonywania operacji __Attach__ w istniejącym klastrze. Najpierw należy dołączyć klaster, a następnie wykonać operację aktualizacji w celu zmiany usługi równoważenia obciążenia.

Aby uzyskać więcej informacji na temat używania wewnętrznego modułu równoważenia obciążenia z programem AKS, zobacz [Korzystanie z wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service](/azure/aks/internal-lb).

## <a name="use-azure-firewall"></a>Korzystanie z zapory platformy Azure

W przypadku korzystania z zapory platformy Azure należy skonfigurować regułę sieci, aby zezwalać na ruch do i z następujących adresów:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Podczas dodawania reguły należy ustawić wartość dowolny __Protokół__ i porty do `*`.

Aby uzyskać więcej informacji na temat konfigurowania reguły sieci, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie środowisk szkoleniowych](how-to-set-up-training-targets.md)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Bezpieczne wdrażanie modeli przy użyciu protokołu SSL](how-to-secure-web-service.md)
