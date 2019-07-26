---
title: Uruchamianie eksperymentów i wnioskowania w sieci wirtualnej
titleSuffix: Azure Machine Learning service
description: Uruchom eksperymenty uczenia maszynowego i zabezpieczenia wnioskowania w sieci wirtualnej platformy Azure. Dowiedz się, jak tworzyć cele obliczeniowe dla szkolenia modeli oraz jak w sieci wirtualnej. Dowiedz się więcej o wymaganiach dotyczących zabezpieczonych sieci wirtualnych, takich jak Wymagaj portów przychodzących i wychodzących.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 07/10/2019
ms.openlocfilehash: 412eaac2f82a6d09761dcac53192916df215831f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358793"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Bezpieczne uruchamianie eksperymentów i wnioskowania wewnątrz sieci wirtualnej platformy Azure

W tym artykule dowiesz się, jak uruchamiać eksperymenty i wnioskowanie w sieci wirtualnej. Sieć wirtualna działa jako granica zabezpieczeń, izolowanie zasobów platformy Azure od publicznego Internetu. Możesz również dołączyć do sieci wirtualnej platformy Azure do sieci lokalnej. Pozwala ona na bezpieczne uczenie modeli i uzyskiwanie dostępu do wdrożonych modeli na potrzeby wnioskowania. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej dotyczący danych produkcyjnych.

Usługa Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe (cele obliczeniowe) są używane do uczenia i wdrażania modeli. Te obiekty docelowe obliczeń można tworzyć wewnątrz sieci wirtualnej. Na przykład można użyć Data Science Virtual Machine firmy Microsoft do uczenia modelu, a następnie wdrożenia modelu w usłudze Azure Kubernetes Service (AKS). Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Omówienie usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Wymagania wstępne

W tym dokumencie przyjęto założenie, że znasz usługi Azure Virtual Networks i sieć IP. W tym dokumencie założono również, że utworzono sieć wirtualną i podsieć, która ma być używana z zasobami obliczeniowymi. Jeśli nie znasz usługi Azure Virtual Networks, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o usłudze:

* [Adresowanie IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Szybki start: Tworzenie sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrowanie ruchu sieciowego](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Konto magazynu dla obszaru roboczego

> [!IMPORTANT]
> __Domyślne konto magazynu__ dla usługi Azure Machine Learning można umieścić w sieci wirtualnej __tylko podczas__przeprowadzania eksperymentów.
>
> W przypadku __kont magazynu innych niż domyślne na potrzeby eksperymentowania__lub jeśli używasz konta magazynu do wnioskowania , musisz mieć __nieograniczony dostęp do konta magazynu__.
> 
> Jeśli nie masz pewności, czy zostały zmodyfikowane te ustawienia, zobacz __Zmiana domyślnej reguły dostępu do sieci__ w artykule [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security). Wykonaj kroki, aby zezwolić na dostęp ze wszystkich sieci podczas wnioskowania, lub ocenianie modelu.

Aby użyć ustawienia domyślne konto usługi Azure Storage dla obszaru roboczego w sieci wirtualnej, wykonaj następujące czynności:

1. Tworzenie obliczeń eksperymentów przykładowych. Środowisko obliczeniowe usługi Machine Learning za siecią wirtualną lub dołączenie obliczeń eksperymentowania do obszaru roboczego. Klaster usługi HDInsight lub maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [używanie środowisko obliczeniowe usługi Machine Learning](#use-machine-learning-compute) i [Używanie maszyny wirtualnej lub klastra usługi HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) w tym dokumencie.
2. Przejdź do magazynu dołączonego do obszaru roboczego. ![Obraz Azure Portal przedstawiający usługę Azure Storage dołączoną do obszaru roboczego usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Na stronie Magazyn platformy Azure wybierz pozycję __zapory i sieci wirtualne__. ![Ilustracja przedstawiająca Azure Portal w sekcji zapory i sieci wirtualne na stronie usługi Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Na stronie __zapory i sieci wirtualne__ wybierz następujące wpisy:
    - Wybierz pozycję __Wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz pozycję __Dodaj istniejącą sieć wirtualną__ , aby dodać sieć wirtualną, w której znajduje się obliczenie eksperymentu. (Zobacz krok 1).
    - Wybierz opcję __Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu__.
![Obraz Azure Portal przedstawiający zapory i sieci wirtualne w obszarze usługi Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. Podczas działania eksperymentu w kodzie eksperymentu Zmień konfigurację uruchamiania, aby używała magazynu obiektów blob:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>Magazyn kluczy dla obszaru roboczego
Key Vault wystąpienie skojarzone z obszarem roboczym jest używane przez usługę Azure Machine Learning do przechowywania poświadczeń różnych rodzajów:
* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia z magazynami danych. 

Aby korzystać z możliwości eksperymentowania Azure Machine Learning z Key Vault za siecią wirtualną, wykonaj następujące kroki:
1. Przejdź do Key Vault skojarzonego z obszarem roboczym. ![Obraz Azure Portal pokazujący Key Vault, który jest skojarzony z obszarem roboczym usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. Na stronie Key Vault wybierz pozycję __zapory i sieci wirtualne__ . ![Obraz Azure Portal przedstawiający sekcję zapory i sieci wirtualne na stronie Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Na stronie __zapory i sieci wirtualne__ wybierz następujące wpisy:
    - Wybierz pozycję __Wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz pozycję __Dodaj istniejące sieci wirtualne__ , aby dodać sieć wirtualną, w której znajduje się obliczenie eksperymentu.
    - Wybierz opcję __Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory__.
![Obraz Azure Portal przedstawiający zapory i sieci wirtualne w obszarze Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Użyj środowisko obliczeniowe usługi Machine Learning

Aby użyć obliczeń Azure Machine Learning w sieci wirtualnej, należy użyć następujących informacji o wymaganiach sieci:

- Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy usługi Azure Machine Learning.

- Podsieć określona dla klastra środowisko obliczeniowe usługi Machine Learning musi mieć wystarczającą liczbę nieprzypisanych adresów IP w celu uwzględnienia liczby maszyn wirtualnych przeznaczonych dla klastra. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster zostanie częściowo przydzielony.

- Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu, pozostaw kilka otwartych portów dla usługi środowisko obliczeniowe usługi Machine Learning. Aby uzyskać więcej informacji, zobacz [wymagane porty](#mlcports).

- Sprawdź, czy zasady zabezpieczeń lub blokady w ramach subskrypcji lub grupy zasobów sieci wirtualnej ograniczają uprawnienia do zarządzania siecią wirtualną.

- Jeśli zamierzasz umieścić wiele klastrów środowisko obliczeniowe usługi Machine Learning w jednej sieci wirtualnej, może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego zasobu.

    Środowisko obliczeniowe usługi Machine Learning automatycznie przydziela dodatkowe zasoby sieciowe do grupy zasobów zawierającej sieć wirtualną. Dla każdego klastra środowisko obliczeniowe usługi Machine Learning usługa Azure Machine Learning przydziela następujące zasoby:

    - Jedna sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń)

    - Jeden publiczny adres IP

    - Jeden moduł równoważenia obciążenia

  Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-subscription-service-limits) subskrypcji.

### <a id="mlcports"></a>Wymagane porty

Środowisko obliczeniowe usługi Machine Learning obecnie używa usługi Azure Batch do aprowizacji maszyn wirtualnych w określonej sieci wirtualnej. Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch. Ta komunikacja służy do planowania przebiegów w węzłach środowisko obliczeniowe usługi Machine Learning oraz do komunikowania się z usługą Azure Storage i innymi zasobami. Batch dodaje sieciowych grup zabezpieczeń na poziomie interfejsów sieciowych dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Ruch przychodzący TCP na portach 29876 i 29877 z __tagu usługi__ __BatchNodeManagement__.

    ![Obraz Azure Portal przedstawiający regułę ruchu przychodzącego przy użyciu tagu usługi BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- obowiązkowe Ruch przychodzący TCP na porcie 22, aby zezwolić na dostęp zdalny. Ten port jest potrzebny tylko wtedy, gdy chcesz nawiązać połączenie przy użyciu protokołu SSH w publicznym adresie IP.
 
- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu. 

Należy zachować ostrożność, jeśli modyfikujesz lub dodasz reguły ruchu przychodzącego/wychodzącego w sieciowych grup zabezpieczeń skonfigurowanym przez partię. Jeśli sieciowej grupy zabezpieczeń blokuje komunikację z węzłami obliczeniowymi, usługa środowisko obliczeniowe usługi Machine Learning Services ustawia stan węzłów obliczeniowych na niezdatny do użytku.

Nie musisz określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Batch konfiguruje własny sieciowych grup zabezpieczeń. Jeśli jednak określona podsieć ma skojarzone sieciowych grup zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego wymienione wcześniej. 

Poniższy zrzut ekranu przedstawia sposób, w jaki Konfiguracja reguły sieciowej grupy ZABEZPIECZEŃa jest wyszukiwana w Azure Portal:

![Zrzut ekranu reguł sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Zrzut ekranu reguł sieciowej grupy zabezpieczeń dla ruchu wychodzącego dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Ograniczanie łączności wychodzącej z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, wykonaj następujące czynności:

- Odmowa wychodzącego połączenia internetowego przy użyciu reguł sieciowej grupy zabezpieczeń 

- Ogranicz ruch wychodzący do usługi Azure Storage (przy użyciu __znacznika usługi__ __Storage. Region_Name__ na przykład. Storage. wschód), Azure Container Registry (przy użyciu __znacznika usługi__ __AzureContainerRegistry. Region_Name.__ AzureContainerRegistry. Wschodnia) i usługa Azure Machine Learning (przy użyciu __znacznika usługi__ __AzureMachineLearning__)

Poniższy zrzut ekranu przedstawia sposób, w jaki Konfiguracja reguły sieciowej grupy ZABEZPIECZEŃa jest wyszukiwana w Azure Portal:

![Zrzut ekranu reguł sieciowej grupy zabezpieczeń dla ruchu wychodzącego dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

Jeśli używasz wymuszonego tunelowania przy użyciu Azure Machine Learning COMPUTE, musisz dodać [trasy zdefiniowane przez użytkownika (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsieci zawierającej zasób obliczeniowy.

* Zdefiniowana przez użytkownika trasa dla każdego adresu IP używanego przez usługę Azure Batch w regionie, w którym znajdują się zasoby. Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi w celu planowania zadań. Aby uzyskać listę adresów IP usługi Batch, skontaktuj się z pomocą techniczną platformy Azure.

* Ruch wychodzący do usługi Azure Storage (w odniesieniu `<account>.queue.core.windows.net`do adresów `<account>.blob.core.windows.net`URL formularza `<account>.table.core.windows.net`, i) nie może być blokowany przez lokalne urządzenie sieciowe.

Po dodaniu tras zdefiniowanych przez użytkownika Zdefiniuj trasy dla każdego powiązanego prefiksu adresu IP partii i ustaw __Typ następnego__ przeskoku na __Internet__. Na poniższej ilustracji przedstawiono przykład tego UDR w Azure Portal:

![Przykładowa trasa zdefiniowana przez użytkownika dla prefiksu adresu](./media/how-to-enable-virtual-network/user-defined-route.png)

Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie puli Azure Batch w sieci wirtualnej](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) .

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Tworzenie środowisko obliczeniowe usługi Machine Learning w sieci wirtualnej

Aby utworzyć klaster środowisko obliczeniowe usługi Machine Learning przy użyciu Azure Portal, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz obszar roboczy usługi Azure Machine Learning.

1. W sekcji __aplikacja__ wybierz pozycję __obliczenia__. Następnie wybierz pozycję __Dodaj obliczenia__. 

    ![Jak dodać obliczenia w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, użyj następujących opcji:

    - __Konfiguracja sieci__: Wybierz pozycję __Zaawansowane__.

    - __Grupa zasobów__: Wybierz grupę zasobów zawierającą sieć wirtualną.

    - __Sieć wirtualna__: Wybierz sieć wirtualną, która zawiera podsieć.

    - __Podsieć__: Wybierz podsieć, która ma zostać użyta.

   ![Zrzut ekranu przedstawiający ustawienia sieci wirtualnej na potrzeby obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po zakończeniu procesu tworzenia można nauczyć model przy użyciu klastra. Aby uzyskać więcej informacji, zobacz [Wybieranie i używanie elementu docelowego obliczeń do szkoleń](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Korzystanie z maszyny wirtualnej lub klastra usługi HDInsight

Aby użyć maszyny wirtualnej lub klastra usługi Azure HDInsight w sieci wirtualnej z obszarem roboczym, wykonaj następujące kroki:

> [!IMPORTANT]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu.

1. Utwórz maszynę wirtualną lub klaster usługi HDInsight przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure i umieść go w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz następujące dokumenty:
    * [Tworzenie sieci wirtualnych platformy Azure dla maszyn wirtualnych z systemem Linux i zarządzanie nimi](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Zwiększanie usługi HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Aby umożliwić usłudze Azure Machine Learning komunikację z portem SSH na maszynie wirtualnej lub w klastrze, należy skonfigurować wpis źródłowy dla sieciowej grupy zabezpieczeń. Port SSH zazwyczaj jest portem 22. Aby zezwolić na ruch z tego źródła, Skorzystaj z następujących informacji:

    * __Źródło__: wybierz pozycję __Tag usługi__.

    * __Tag usługi źródłowej__: Wybierz pozycję __AzureMachineLearning__.

    * __Zakresy portów źródłowych__: Wybierz __*__ opcję.

    * __Miejsce docelowe__: Wybierz __dowolny__.

    * __Zakresy portów docelowych__: Wybierz pozycję __22__.

    * __Protokół__: Wybierz __dowolny__.

    * __Akcja__: wybierz pozycję __Zezwalaj__.

   ![Zrzut ekranu reguł ruchu przychodzącego służący do przeprowadzania eksperymentów na maszynie wirtualnej lub w klastrze usługi HDInsight w sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, zobacz Ograniczanie [łączności wychodzącej z sieci wirtualnej](#limiting-outbound-from-vnet)
    
1. Dołącz maszynę wirtualną lub klaster HDInsight do obszaru roboczego usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Korzystanie z usługi Azure Kubernetes Service

> [!IMPORTANT]
> Przed wykonaniem kroków Sprawdź wymagania wstępne i zaplanuj adresowanie IP w klastrze. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zaawansowanej sieci w usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Usługa Azure Kubernetes i Sieć wirtualna platformy Azure powinny znajdować się w tym samym regionie.

Aby dodać usługę Azure Kubernetes w sieci wirtualnej do obszaru roboczego, wykonaj następujące kroki w Azure Portal:

1. Upewnij się, że grupa sieciowej grupy zabezpieczeń, która kontroluje sieć wirtualną, ma włączoną regułę ruchu przychodzącego dla usługi Azure Machine Learning przy użyciu __tagu usługi__ __AzureMachineLearning__

    ![Jak dodać obliczenia w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. W [Azure Portal](https://portal.azure.com)wybierz obszar roboczy usługi Azure Machine Learning.

1. W sekcji __aplikacja__ wybierz pozycję __obliczenia__. Następnie wybierz pozycję __Dodaj obliczenia__. 

    ![Jak dodać obliczenia w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, użyj następujących opcji:

    - __Konfiguracja sieci__: Wybierz pozycję __Zaawansowane__.

    - __Grupa zasobów__: Wybierz grupę zasobów zawierającą sieć wirtualną.

    - __Sieć wirtualna__: Wybierz sieć wirtualną, która zawiera podsieć.

    - __Podsieć__: Wybierz podsieć.

    - __Zakres adresów usługi Kubernetes__: Wybierz zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP notacji CIDR do zdefiniowania adresów IP dostępnych dla tego klastra. Nie może się nakładać na żadne zakresy adresów IP podsieci. Na przykład: 10.0.0.0/16.

    - __Kubernetes adres IP usługi DNS__: Wybierz adres IP usługi Kubernetes DNS. Ten adres IP jest przypisywany do usługi DNS Kubernetes. Musi znajdować się w zakresie adresów usługi Kubernetes. Na przykład: 10.0.0.10.

    - __Adres mostka platformy Docker__: Wybierz adres mostka platformy Docker. Ten adres IP jest przypisany do mostka platformy Docker. Nie może być w żadnym z zakresów adresów IP podsieci ani zakresu adresów usługi Kubernetes. Na przykład: 172.17.0.1/16.

   ![Usługa Azure Machine Learning: środowisko obliczeniowe usługi Machine Learning ustawień sieci wirtualnej](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Upewnij się, że grupa sieciowej grupy zabezpieczeń, która kontroluje sieć wirtualną, ma włączoną regułę ruchu przychodzącego dla punktu końcowego oceniania, tak aby można ją było wywołać spoza sieci wirtualnej

    ![Jak dodać obliczenia w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Jeśli masz już klaster AKS w sieci wirtualnej, możesz dołączyć go do obszaru roboczego. Aby uzyskać więcej informacji, zobacz [How to Deploy to AKS](how-to-deploy-to-aks.md).

Możesz również użyć **zestawu SDK Azure Machine Learning** , aby dodać usługę Azure Kubernetes w sieci wirtualnej. Poniższy kod tworzy nowe wystąpienie usługi Azure Kubernetes w `default` podsieci sieci wirtualnej o nazwie: `mynetwork`

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

Po zakończeniu procesu tworzenia można wywnioskować/uzyskać wynik w klastrze AKS za siecią wirtualną. Aby uzyskać więcej informacji, zobacz [How to Deploy to AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie środowisk szkoleniowych](how-to-set-up-training-targets.md)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Bezpieczne wdrażanie modeli przy użyciu protokołu SSL](how-to-secure-web-service.md)

