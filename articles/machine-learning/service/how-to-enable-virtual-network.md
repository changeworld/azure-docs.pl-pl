---
title: Bezpieczne eksperymenty i wnioskowanie w sieci wirtualnej
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zabezpieczyć zadania eksperymentów/szkoleń oraz zadania wnioskowania/oceniania w Azure Machine Learning w ramach Virtual Network platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: 05c5d42d3c20948df4f42db50dd93abd60288c00
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639574"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Zabezpieczanie zadań eksperymentowania i wnioskowania usługi Azure ML w ramach Virtual Network platformy Azure

W tym artykule dowiesz się, jak zabezpieczyć zadania eksperymentowania/szkolenia oraz zadania wnioskowania/oceniania w Azure Machine Learning w ramach Virtual Network platformy Azure. 

**Sieć wirtualna** działa jako granica zabezpieczeń, izolowanie zasobów platformy Azure od publicznego Internetu. Możesz również dołączyć do sieci wirtualnej platformy Azure do sieci lokalnej. Dzięki dołączeniu sieci można bezpiecznie uczenie modeli i uzyskać dostęp do wdrożonych modeli w celu wnioskowania.

Usługa Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe lub [obiekty docelowe obliczeń](concept-compute-target.md)są używane do uczenia i wdrażania modeli. Obiekty docelowe można tworzyć w ramach sieci wirtualnej. Na przykład można użyć programu Microsoft Data Science Virtual Machine do uczenia modelu, a następnie wdrożenia modelu w usłudze Azure Kubernetes Service (AKS). Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Omówienie usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Ten artykuł zawiera również szczegółowe informacje dotyczące *zaawansowanych ustawień zabezpieczeń*, informacji, które nie są niezbędne dla podstawowych lub eksperymentalnych przypadków użycia. Niektóre sekcje tego artykułu zawierają informacje o konfiguracji dla różnych scenariuszy. Nie musisz wykonywać instrukcji w kolejności ani w całości.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Obszar roboczy](how-to-manage-workspace.md)usługi Azure Machine Learning. 

+ Ogólna wiedza o działaniu [usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i [sieci IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

+ Wstępnie istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi. 

## <a name="use-a-storage-account-for-your-workspace"></a>Korzystanie z konta magazynu dla obszaru roboczego

Aby użyć konta usługi Azure Storage dla obszaru roboczego w sieci wirtualnej, wykonaj następujące czynności:

1. Utwórz wystąpienie obliczeniowe eksperymentu (na przykład wystąpienie środowisko obliczeniowe usługi Machine Learning) za siecią wirtualną lub Dołącz wystąpienie obliczeniowe eksperymentu do obszaru roboczego (na przykład klastra usługi HDInsight lub maszyny wirtualnej). 

   Aby uzyskać więcej informacji, zobacz sekcję "Używanie środowisko obliczeniowe usługi Machine Learning wystąpienia" i "Korzystanie z maszyny wirtualnej lub klastra usługi HDInsight" w tym artykule.

1. W Azure Portal przejdź do magazynu dołączonego do obszaru roboczego. 

   ![Magazyn dołączony do obszaru roboczego usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)

1. Na stronie **Magazyn platformy Azure** wybierz pozycję __zapory i sieci wirtualne__. 

   ![Obszar "zapory i sieci wirtualne" na stronie usługi Azure Storage w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stronie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    - Wybierz pozycję __Wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz łącze __Dodaj istniejące sieci wirtualne__ . Ta akcja powoduje dodanie sieci wirtualnej, w której znajduje się wystąpienie obliczeniowe eksperymentu (zobacz krok 1).
    - Zaznacz pole wyboru __Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu__ .

   ![Okienko "zapory i sieci wirtualne" w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

1. W trakcie działania eksperymentu w kodzie eksperymentu Zmień konfigurację uruchomieniową tak, aby korzystała z usługi Azure Blob Storage:

    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> _Domyślne konto magazynu_ dla usługi Azure Machine Learning można umieścić w sieci wirtualnej _tylko na potrzeby eksperymentowania_.
>
> _Konta magazynu inne niż domyślne_ można umieścić w sieci wirtualnej _tylko na potrzeby eksperymentowania_.
>
> Zarówno domyślne, jak i inne niż domyślne konta magazynu używane na potrzeby _wnioskowania_ muszą mieć _nieograniczony dostęp do konta magazynu_.
>
> Jeśli nie masz pewności, czy ustawienia zostały zmodyfikowane, zobacz sekcję "Zmiana domyślnej reguły dostępu do sieci" w artykule [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](https://docs.microsoft.com/azure/storage/common/storage-network-security). Postępuj zgodnie z instrukcjami, aby zezwolić na dostęp ze wszystkich sieci podczas wnioskowania, lub na ocenianie modelu.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Korzystanie z wystąpienia magazynu kluczy z obszarem roboczym

Wystąpienie magazynu kluczy skojarzone z obszarem roboczym jest używane przez usługę Azure Machine Learning do przechowywania następujących poświadczeń:
* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia do magazynów danych

Aby korzystać z funkcji eksperymentowania Azure Machine Learning z Azure Key Vault za siecią wirtualną, wykonaj następujące czynności:
1. Przejdź do magazynu kluczy skojarzonego z obszarem roboczym. 

   ![Magazyn kluczy skojarzony z obszarem roboczym usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)

1. Na stronie **Key Vault** w lewym okienku wybierz pozycję __zapory i sieci wirtualne__. 

   ![Sekcja "zapory i sieci wirtualne" w okienku Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stronie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    - W obszarze __Zezwalaj na dostęp z__, wybierz opcję __wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz pozycję __Dodaj istniejące sieci wirtualne__ , aby dodać sieć wirtualną, w której znajduje się wystąpienie obliczeniowe eksperymentu.
    - W obszarze __Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory__wybierz pozycję __tak__.

   ![Sekcja "zapory i sieci wirtualne" w okienku Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)

## <a name="use-a-machine-learning-compute-instance"></a>Użyj wystąpienia środowisko obliczeniowe usługi Machine Learning

Aby użyć wystąpienia obliczeniowego Azure Machine Learning w sieci wirtualnej, należy wziąć pod uwagę następujące wymagania dotyczące sieci:

- Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy usługi Azure Machine Learning.

- Podsieć określona dla klastra obliczeniowego musi mieć wystarczającą liczbę nieprzypisanych adresów IP w celu uwzględnienia liczby maszyn wirtualnych przeznaczonych dla klastra. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster zostanie częściowo przydzielony.

- Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu, pozostaw kilka otwartych portów dla usługi obliczeniowej. Aby uzyskać więcej informacji, zobacz sekcję [wymagane porty](#mlcports) .

- Sprawdź, czy zasady zabezpieczeń lub blokady w ramach subskrypcji lub grupy zasobów sieci wirtualnej ograniczają uprawnienia do zarządzania siecią wirtualną.

- Jeśli chcesz umieścić wiele klastrów obliczeniowych w jednej sieci wirtualnej, może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego zasobu.

    Środowisko obliczeniowe usługi Machine Learning wystąpienie automatycznie przydziela dodatkowe zasoby sieciowe do grupy zasobów zawierającej sieć wirtualną. Dla każdego klastra obliczeniowego usługa przydziela następujące zasoby:

    - Jedna sieciowa Grupa zabezpieczeń

    - Jeden publiczny adres IP

    - Jeden moduł równoważenia obciążenia

  Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-subscription-service-limits) subskrypcji.

### <a id="mlcports"></a>Wymagane porty

Środowisko obliczeniowe usługi Machine Learning obecnie używa usługi Azure Batch do aprowizacji maszyn wirtualnych w określonej sieci wirtualnej. Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch. Ta komunikacja służy do planowania przebiegów w węzłach środowisko obliczeniowe usługi Machine Learning oraz do komunikowania się z usługą Azure Storage i innymi zasobami. Usługa Batch dodaje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) na poziomie interfejsów sieciowych dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Ruch przychodzący TCP na portach 29876 i 29877 z __tagu usługi__ __BatchNodeManagement__.

    ![Reguła przychodząca korzystająca z tagu usługi BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Obowiązkowe Ruch przychodzący TCP na porcie 22, aby zezwolić na dostęp zdalny. Tego portu należy używać tylko w przypadku, gdy chcesz nawiązać połączenie przy użyciu protokołu SSH w publicznym adresie IP.

- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu.

Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli sieciowej grupy zabezpieczeń blokuje komunikację z węzłami obliczeniowymi, usługa COMPUTE ustawia stan węzłów obliczeniowych na niezdatny do użytku.

Nie musisz określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Azure Batch konfiguruje własny sieciowych grup zabezpieczeń. Jeśli jednak określona podsieć ma skojarzone sieciowych grup zabezpieczeń lub zaporę, skonfiguruj reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego wymienione wcześniej.

Konfiguracja reguły sieciowej grupy zabezpieczeń w Azure Portal przedstawiono na następujących obrazach:

![Reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Wychodzące reguły sieciowej grupy zabezpieczeń dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Ogranicz łączność wychodzącą z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, wykonaj następujące czynności:

- Odmowa wychodzącego połączenia internetowego przy użyciu reguł sieciowej grupy zabezpieczeń.

- Ogranicz ruch wychodzący do następujących:
   - Azure Storage, przy użyciu __znacznika usługi__ __Storage. Region_Name__ (na przykład Storage. wschód)
   - Azure Container Registry, przy użyciu __znacznika usługi__ __AzureContainerRegistry. Region_Name__ (na przykład AzureContainerRegistry. wschód)
   - Usługa Azure Machine Learning przy użyciu __znacznika usługi__ __AzureMachineLearning__

Konfiguracja reguły sieciowej grupy zabezpieczeń w Azure Portal jest pokazana na poniższym obrazie:

![Wychodzące reguły sieciowej grupy zabezpieczeń dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

Jeśli używasz wymuszonego tunelowania z środowisko obliczeniowe usługi Machine Learning, Dodaj [trasy zdefiniowane przez użytkownika (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsieci zawierającej zasób obliczeniowy.

* Ustanów UDR dla każdego adresu IP, który jest używany przez usługę Azure Batch w regionie, w którym znajdują się zasoby. Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi w celu planowania zadań. Aby uzyskać listę adresów IP usługi Batch, użyj jednej z następujących metod:

    * Pobierz [zakresy adresów IP platformy Azure i Tagi usług](https://www.microsoft.com/download/details.aspx?id=56519) i Przeszukaj plik `BatchNodeManagement.<region>`, gdzie `<region>` jest Twoim regionem świadczenia usługi Azure.

    * Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , aby pobrać te informacje. Poniższy przykład pobiera informacje o adresie IP i filtruje informacje dla regionu Wschodnie stany USA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Ruch wychodzący do usługi Azure Storage nie może być blokowany przez lokalne urządzenie sieciowe. W odróżnieniu od tego adresy URL `<account>.table.core.windows.net`mają `<account>.queue.core.windows.net`postać, `<account>.blob.core.windows.net`i.

Po dodaniu UDR, zdefiniuj trasę dla każdego powiązanego prefiksu adresu IP partii i ustaw __Typ następnego__ przeskoku na __Internet__. Na poniższej ilustracji przedstawiono przykład tego UDR w Azure Portal:

![Przykład UDR dla prefiksu adresu](./media/how-to-enable-virtual-network/user-defined-route.png)

Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch w sieci wirtualnej](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-machine-learning-compute-cluster-in-a-virtual-network"></a>Tworzenie klastra środowisko obliczeniowe usługi Machine Learning w sieci wirtualnej

Aby utworzyć klaster środowisko obliczeniowe usługi Machine Learning, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz obszar roboczy usługi Azure Machine Learning.

1. W sekcji __aplikacja__ wybierz pozycję __obliczenia__, a następnie wybierz pozycję __Dodaj obliczenia__.

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności:

    a. W obszarze __Konfiguracja sieci__wybierz pozycję __Zaawansowane__.

    b. Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.

    c. Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną, która zawiera podsieć.

    d. Z listy rozwijanej podsieć wybierz podsieć, która ma zostać użyta.

   ![Ustawienia sieci wirtualnej dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Klaster środowisko obliczeniowe usługi Machine Learning można również utworzyć za pomocą zestawu Azure Machine Learning SDK. Poniższy kod tworzy nowy klaster środowisko obliczeniowe usługi Machine Learning w `default` podsieci sieci wirtualnej o nazwie: `mynetwork`

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

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Korzystanie z maszyny wirtualnej lub klastra usługi HDInsight

> [!IMPORTANT]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu.

Aby użyć maszyny wirtualnej lub klastra usługi Azure HDInsight w sieci wirtualnej z obszarem roboczym, wykonaj następujące czynności:

1. Utwórz maszynę wirtualną lub klaster usługi HDInsight przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure i umieść klaster w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji zobacz następujące artykuły:
    * [Tworzenie sieci wirtualnych platformy Azure dla maszyn wirtualnych z systemem Linux i zarządzanie nimi](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Zwiększanie usługi HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Aby umożliwić usłudze Azure Machine Learning komunikowanie się z portem SSH na maszynie wirtualnej lub w klastrze, należy skonfigurować wpis źródła dla sieciowej grupy zabezpieczeń. Port SSH zazwyczaj jest portem 22. Aby zezwolić na ruch z tego źródła, wykonaj następujące czynności:

    * Z listy rozwijanej __Źródło__ wybierz pozycję __tag usługi__.

    * Z listy rozwijanej __tag usługi źródłowej__ wybierz pozycję __AzureMachineLearning__.

    * Z listy rozwijanej __zakresy portów źródłowych__ wybierz pozycję __*__ .

    * Z listy rozwijanej __Lokalizacja__ docelowa wybierz __dowolne__.

    * Z listy rozwijanej __zakresy portów docelowych__ wybierz pozycję __22__.

    * W obszarze __Protokół__wybierz opcję __dowolny__.

    * W obszarze __Akcja__wybierz pozycję __Zezwalaj__.

   ![Reguły ruchu przychodzącego na potrzeby przeprowadzania eksperymentów na maszynie wirtualnej lub w klastrze usługi HDInsight w sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, zobacz sekcję [ograniczanie łączności wychodzącej z sieci wirtualnej](#limiting-outbound-from-vnet) .

1. Dołącz maszynę wirtualną lub klaster HDInsight do obszaru roboczego usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Korzystanie z usługi Azure Kubernetes Service (AKS)

Aby dodać AKS w sieci wirtualnej do obszaru roboczego, wykonaj następujące czynności:

> [!IMPORTANT]
> Przed rozpoczęciem poniższej procedury Sprawdź wymagania wstępne i zaplanuj adresowanie IP klastra. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zaawansowanej sieci w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
>
> Wystąpienie AKS i Sieć wirtualna platformy Azure muszą znajdować się w tym samym regionie.

1. W [Azure Portal](https://portal.azure.com)upewnij się, że sieciowej grupy zabezpieczeń kontrolujący sieć wirtualną ma regułę ruchu przychodzącego, która jest włączona dla usługi Azure Machine Learning przy użyciu __AzureMachineLearning__ jako **źródła**.

    ![Dodawanie okienka Obliczanie usługi Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. Wybierz obszar roboczy usługi Azure Machine Learning.

1. W sekcji __aplikacja__ wybierz pozycję __obliczenia__, a następnie wybierz pozycję __Dodaj obliczenia__.

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności:

    - W obszarze __Konfiguracja sieci__wybierz pozycję __Zaawansowane__.

    - Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.

    - Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną, która zawiera podsieć.

    - Z listy rozwijanej podsieć wybierz podsieć.

    - W polu __zakres adresów usługi Kubernetes__ Wprowadź zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP notacji CIDR (Classless Inter-Domain Routing) do definiowania adresów IP, które są dostępne dla klastra. Nie może się nakładać na żadne zakresy adresów IP podsieci (na przykład 10.0.0.0/16).

    - W polu __adres IP usługi KUBERNETES DNS__ wprowadź adres IP usługi DNS Kubernetes. Ten adres IP jest przypisywany do usługi DNS Kubernetes. Musi ona należeć do zakresu adresów usługi Kubernetes (na przykład 10.0.0.10).

    - W polu __adres mostka platformy Docker__ wprowadź adres mostka platformy Docker. Ten adres IP jest przypisany do mostka platformy Docker. Nie może być w żadnym z zakresów adresów IP podsieci lub zakres adresów usługi Kubernetes (na przykład 172.17.0.1/16).

   ![Usługa Azure Machine Learning: środowisko obliczeniowe usługi Machine Learning ustawień sieci wirtualnej](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Upewnij się, że grupa sieciowej grupy zabezpieczeń kontrolująca sieć wirtualną ma włączoną regułę zabezpieczeń dla punktu końcowego oceniania, tak aby można ją było wywołać spoza sieci wirtualnej.
   > [!IMPORTANT]
   > Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
  
   ![Reguła zabezpieczeń dla ruchu przychodzącego](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

Możesz również użyć zestawu SDK Azure Machine Learning, aby dodać usługę Azure Kubernetes w sieci wirtualnej. Jeśli masz już klaster AKS w sieci wirtualnej, dołącz go do obszaru roboczego, zgodnie z opisem w artykule [wdrażanie w AKS](how-to-deploy-to-aks.md). Poniższy kod tworzy nowe wystąpienie AKS w `default` podsieci sieci wirtualnej o nazwie: `mynetwork`

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

Po zakończeniu procesu tworzenia można uruchomić wnioskowanie lub ocenianie modelu w klastrze AKS za siecią wirtualną. Aby uzyskać więcej informacji, zobacz [How to Deploy to AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie środowisk szkoleniowych](how-to-set-up-training-targets.md)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Bezpieczne wdrażanie modeli przy użyciu protokołu SSL](how-to-secure-web-service.md)

