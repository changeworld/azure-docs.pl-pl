---
title: Uruchamianie eksperymentów i wnioskowania w sieci wirtualnej
titleSuffix: Azure Machine Learning service
description: Uruchamiaj eksperymenty uczenia maszynowego i wnioskowania zabezpieczanie wewnątrz sieci wirtualnej platformy Azure. Dowiedz się, jak utworzyć obliczeniowych elementów docelowych do trenowania modelu oraz sposób wnioskowanie w ramach sieci wirtualnej. Dowiedz się więcej o wymaganiach dotyczących zabezpieczonej sieci wirtualnych, takich jak wymaga portów przychodzących i wychodzących.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: fe51f4589075cb275e867c943c5d7df3e8d5d4a0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795011"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Bezpiecznego uruchamiania eksperymentów oraz wnioskowania wewnątrz sieci wirtualnej platformy Azure

W tym artykule dowiesz się, jak można uruchamiać eksperymenty i wnioskowania wewnątrz sieci wirtualnej. Sieć wirtualna działa jak granica bezpieczeństwa, izolowanie zasobów platformy Azure z publicznego Internetu. Usługa Azure virtual network można również dołączyć do sieci lokalnej. Umożliwia ona bezpiecznie szkolenie modeli i uzyskiwać dostęp do Twojej wdrożonej modeli do wnioskowania.

Usługa Azure Machine Learning, zależy od innych usług platformy Azure za zasoby obliczeniowe. Zasoby obliczeniowe (celów obliczeń) są używane do uczenia i wdrażania modeli. Te obliczenia obiekty docelowe mogą być tworzone w sieci wirtualnej. Na przykład można użyć maszyny wirtualnej do nauki o danych firmy Microsoft do nauczenia modelu, a następnie wdrożyć model do usługi Azure Kubernetes Service (AKS). Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Omówienie usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Wymagania wstępne

W tym dokumencie przyjęto założenie, że znasz sieci wirtualnych platformy Azure i adresu IP sieci ogólnie rzecz biorąc. W tym dokumencie założono również, że utworzono sieć wirtualną i podsieć do korzystania z zasobów obliczeniowych. Jeśli nie jesteś zaznajomiony z sieciami wirtualnymi platformy Azure, przeczytaj następujące artykuły, aby dowiedzieć się więcej o usłudze:

* [Adresowanie IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Szybki start: Tworzenie sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrowanie ruchu sieciowego](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Konto magazynu dla obszaru roboczego

> [!IMPORTANT]
> Możesz umieścić konta magazynu, który jest dołączony do obszaru roboczego usługi Azure Machine Learning, za zaporą w sieci wirtualnej tylko podczas wykonywania eksperymentów. Wnioskowania wymaga nieograniczonego dostępu do konta magazynu. Jeśli nie masz pewności, jeśli zmodyfikowano tych ustawień lub nie można znaleźć __zmienić domyślną regułę dostępu sieciowego__ w [zapory Konfigurowanie usługi Azure Storage i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security). Wykonaj kroki, aby zezwolić na dostęp ze wszystkich sieci, podczas wykonywania wnioskowania.

Aby użyć eksperymentowanie w usłudze Azure Machine Learning możliwości za pomocą usługi Azure Storage za sieci wirtualnej, wykonaj następujące czynności:

1. Tworzenie obliczeń eksperymentowania, np. Usługi Machine Learning obliczenia za sieci wirtualnej, lub Dołącz obliczeń eksperymentowania z obszarem roboczym, np. Klaster HDInsight lub maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [obliczeniowego usługi użyj Machine Learning](#use-machine-learning-compute) i [Użyj maszyny wirtualnej lub klastra HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) sekcje w tym dokumencie
2. Przejdź do magazynu dołączone do obszaru roboczego. ![Obraz witryny Azure portal pokazujący usługi Azure Storage, który jest dołączony do obszaru roboczego usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Na stronie usługi Azure Storage wybierz __zapory i sieci wirtualne__. ![Obraz Azure portal przedstawiający zapory i wirtualnych sieci sekcji na stronie usługi Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Na __zapory i sieci wirtualne__ strony wybierz następujące pozycje:
    - Wybierz pozycję __Wybrane sieci__.
    - W obszarze __sieci wirtualne__ wybierz __Dodaj istniejącą sieć wirtualną__ można dodać sieci wirtualnej, w którym znajduje się obliczeń eksperymentowanie w usłudze. (Zobacz krok 1.)
    - Wybierz __dozwolonych zaufanych usług firmy Microsoft dostęp do tego konta magazynu__.
![Obraz Azure portal przedstawiający zapory i wirtualnych sieci strony w ramach usługi Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. Podczas uruchamiania eksperymentu, w kodzie eksperymentowania, zmiany konfiguracji uruchamiania, używanie usługi blob storage:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>Magazyn kluczy dla Twojego obszaru roboczego
Wystąpienia usługi Key Vault skojarzone z obszarem roboczym jest używany przez usługę Azure Machine Learning do przechowywania poświadczeń różnych rodzajów:
* Parametry połączenia konta magazynu skojarzone
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Połączenie z danymi ciągi magazynów. 

Aby użyć eksperymentowanie w usłudze Azure Machine Learning możliwości za pomocą usługi Key Vault za zaporą sieci wirtualnej, wykonaj następujące czynności:
1. Przejdź do usługi Key Vault, skojarzone z obszarem roboczym. ![Obraz witryny Azure portal pokazujący usługi Key Vault, który jest skojarzony z obszarem roboczym usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. W usłudze Key Vault stronie wybierz __zapory i sieci wirtualne__ sekcji. ![Obraz Azure portal przedstawiający zapory i wirtualnych sieci sekcji na stronie usługi Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Na __zapory i sieci wirtualne__ strony wybierz następujące pozycje:
    - Wybierz pozycję __Wybrane sieci__.
    - W obszarze __sieci wirtualne__ wybierz __Dodaj istniejące sieci wirtualne__ można dodać sieci wirtualnej, w którym znajduje się obliczeń eksperymentowanie w usłudze.
    - Wybierz __dozwolonych zaufanych usług firmy Microsoft na pomijanie zapory__.
![Obraz Azure portal przedstawiający zapory i wirtualnych sieci stronie w obszarze usługi Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Użyj usługi Machine Learning obliczeń

Aby użyć obliczeniowego usługi Azure Machine Learning w sieci wirtualnej, użyj poniższych informacji na temat wymagań dotyczących sieci:

- Sieć wirtualna musi być w tej samej subskrypcji i regionie co obszar roboczy usługi Azure Machine Learning.

- Podsieć określona dla klastra obliczeniowego usługi Machine Learning musi mieć wystarczającej liczby nieprzypisanych adresów IP, aby uwzględnić liczbę maszyn wirtualnych przeznaczony dla klastra. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster zostanie przydzielony częściowo.

- Jeśli planujesz Zabezpieczanie sieci wirtualnej, ograniczając ruch zamykaj Niektóre porty usługi obliczeniowego usługi Machine Learning. Aby uzyskać więcej informacji, zobacz [wymagane porty](#mlcports).

- Sprawdź, czy Twoje zasady zabezpieczeń lub blokuje subskrypcji lub grupy zasobów sieci wirtualnej Ogranicz uprawnienia do zarządzania sieci wirtualnej.

- Jeśli chcesz umieścić wielu klastrów obliczeniowego usługi Machine Learning w jednej sieci wirtualnej, może być konieczne zażądać zwiększenia limitu przydziału dla co najmniej jednej z Twoich zasobów.

    Obliczeniowego usługi Machine Learning jest automatycznie alokowana dodatkowych zasobów sieciowych w grupie zasobów, która zawiera sieć wirtualną. Dla każdego klastra obliczeniowego usługi Machine Learning usługi Azure Machine Learning przydziela następujące zasoby:

    - Co sieciowa grupa zabezpieczeń (NSG)

    - Jeden publiczny adres IP

    - Jedna usługa równoważenia obciążenia

  Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-subscription-service-limits) subskrypcji.

### <a id="mlcports"></a> Wymagane porty

Obecnie obliczeniowego usługi Machine Learning korzysta z usługi Azure Batch aprowizację maszyn wirtualnych w określonej sieci wirtualnej. Podsieć musi zezwalać na przychodzący komunikacji z usługą Batch. Ta komunikacja służy do planowania jest uruchamiany w węzłach obliczeniowego usługi Machine Learning i nawiązać połączenia z usługą Azure Storage i innych zasobów. Usługa Batch dodaje sieciowe grupy zabezpieczeń na poziomie interfejsów sieciowych (NIC), które są dołączone do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Ruch przychodzący protokołu TCP na porty 29876 i 29877 z __Tag usługi__ z __BatchNodeManagement__.

    ![Obraz portalu Azure przedstawiający regułę ruchu przychodzącego za pomocą BatchNodeManagement tag usługi](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (opcjonalnie) Ruch przychodzący protokołu TCP na porcie 22, aby zezwolić na zdalny dostęp. To ustawienie jest wymagane tylko, jeśli chcesz się połączyć przy użyciu protokołu SSH na publiczny adres IP.
 
- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu. 

Jeśli modyfikujesz lub dodać reguły dla ruchu przychodzącego/wychodzącego w skonfigurowane usługi Batch sieciowych grup zabezpieczeń należy zachować ostrożność. Jeśli sieciowa grupa zabezpieczeń blokuje komunikacji węzłów obliczeniowych, następnie obliczeniowego usługi Machine Learning services ustawia stan węzłów obliczeniowych na korzystanie z niej.

Nie trzeba określać sieciowe grupy zabezpieczeń na poziomie podsieci, ponieważ usługi Batch konfiguruje swoje własne sieciowych grup zabezpieczeń. Jednak jeśli określona podsieć ma skojarzone sieciowe grupy zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń ruchu przychodzącego i wychodzącego, jak wspomniano wcześniej. 

Poniższy zrzut ekranu pokazuje, jak wygląda konfiguracji reguły sieciowej grupy zabezpieczeń w witrynie Azure portal:

![Zrzut ekranu przedstawiający dla ruchu przychodzącego sieciowej grupy zabezpieczeń reguły obliczeniowego usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Zrzut ekranu przedstawiający wychodzące reguły sieciowej grupy zabezpieczeń, obliczeniowego usługi Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> Ograniczanie łączności wychodzącej z sieci wirtualnej

Jeśli chcesz użyć domyślnych reguł ruchu wychodzącego i chcesz ograniczyć wychodzącego dostępu do sieci wirtualnej, wykonaj następujące czynności:

- Odmów wychodzące połączenie internetowe za pomocą reguł sieciowej grupy zabezpieczeń 

- Ogranicz ruch wychodzący do usługi Azure Storage (przy użyciu __Tag usługi__ z __Storage.Region_Name__ np. Storage.EastUS) usługi Azure Container Registry (przy użyciu __Tag usługi__ z __AzureContainerRegistry.Region_Name__ np. AzureContainerRegistry.EastUS) i usługi Azure Machine Learning (przy użyciu __Tag usługi__ z __AzureMachineLearning__)

Poniższy zrzut ekranu pokazuje, jak wygląda konfiguracji reguły sieciowej grupy zabezpieczeń w witrynie Azure portal:

![Zrzut ekranu przedstawiający wychodzące reguły sieciowej grupy zabezpieczeń, obliczeniowego usługi Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)




### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Tworzenie obliczeniowego usługi Machine Learning w sieci wirtualnej

Aby utworzyć klastra obliczeniowego usługi Machine Learning za pomocą witryny Azure portal, wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), wybierz swój obszar roboczy usługi Azure Machine Learning.

1. W __aplikacji__ zaznacz __obliczenia__. Następnie wybierz pozycję __Dodaj obliczenie__. 

    ![Jak dodać zasoby obliczeniowe w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Aby skonfigurować ten zasób obliczeniowy do sieci wirtualnej, należy użyć tych opcji:

    - __Konfiguracja sieci__: Wybierz pozycję __Zaawansowane__.

    - __Grupa zasobów__: Wybierz grupę zasobów, która zawiera sieć wirtualną.

    - __Sieć wirtualna__: Wybierz sieć wirtualną, która zawiera podsieci.

    - __Podsieć__: Wybierz podsieć do użycia.

   ![Zrzut ekranu przedstawiający ustawienia sieci wirtualnej, machine learning obliczeniowych](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Można również utworzyć klaster obliczeniowego usługi Machine Learning, za pomocą zestawu SDK usługi Azure Machine Learning. Następujący kod umożliwia utworzenie nowego klastra obliczeniowego usługi Machine Learning w `default` podsieci sieci wirtualnej o nazwie `mynetwork`:

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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po zakończeniu procesu tworzenia, możesz uczenie modelu przy użyciu klastra. Aby uzyskać więcej informacji, zobacz [wybierz, a następnie użyć obliczeniowego elementu docelowego do trenowania](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Użyj maszyny wirtualnej lub klaster HDInsight

Aby użyć maszyny wirtualnej lub w klastrze Azure HDInsight w sieci wirtualnej z obszaru roboczego, wykonaj następujące kroki:

> [!IMPORTANT]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne uruchomione w systemie Ubuntu.

1. Tworzenie klastra HDInsight lub maszyny Wirtualnej przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure i umieścić go w sieci wirtualnej platformy Azure. Więcej informacji na ten temat można znaleźć w następujących dokumentach:
    * [Tworzenie i zarządzanie sieciami wirtualnymi platformy Azure dla maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozszerzanie HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Aby umożliwić usłudze Azure Machine Learning do komunikowania się z portem SSH na maszynie Wirtualnej lub w klastrze, należy skonfigurować wpis źródła sieciowej grupy zabezpieczeń. SSH port jest zazwyczaj port 22. Aby zezwolić na ruch z tego źródła, skorzystaj z poniższych informacji:

    * __Źródło__: wybierz pozycję __Tag usługi__.

    * __Tag usługi źródłowej__: Wybierz __AzureMachineLearning__.

    * __Źródłowe zakresy portów__: Wybierz __*__.

    * __Miejsce docelowe__: Wybierz __wszelkie__.

    * __Zakresy portów docelowych__: Wybierz __22__.

    * __Protokół__: Wybierz __wszelkie__.

    * __Akcja__: wybierz pozycję __Zezwalaj__.

   ![Zrzut ekranu przedstawiający reguły dla ruchu przychodzącego do wykonywania eksperymentów w klastrze HDInsight lub maszyny Wirtualnej w sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Zachowaj ustawienie domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślnych reguł zabezpieczeń w [grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Jeśli nie chcesz użyć domyślnych reguł ruchu wychodzącego i chcesz ograniczyć wychodzącego dostępu do sieci wirtualnej, zobacz [ograniczenie łączności wychodzącej z sieci wirtualnej](#limiting-outbound-from-vnet)
    
1. Dołączanie maszyny Wirtualnej lub HDInsight klastra do swojego obszaru roboczego usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie obliczeniowych elementów docelowych do trenowania modelu](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Użyj usługi Azure Kubernetes Service

> [!IMPORTANT]
> Sprawdź wymagania wstępne i planowanie adresowania IP dla klastra przed kontynuowaniem kroków. Aby uzyskać więcej informacji, zobacz [skonfiguruj zaawansowane sieci w usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 

> Zachowaj ustawienie domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślnych reguł zabezpieczeń w [grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Usługa Azure Kubernetes Service i usługa Azure virtual network powinny być w tym samym regionie.

Aby dodać usługi Azure Kubernetes Service w sieci wirtualnej, do obszaru roboczego, wykonaj następujące kroki w witrynie Azure portal:

1. Upewnij się, że sieciowa grupa zabezpieczeń grupy, która włączone dla usługi Azure Machine Learning za pomocą regułę ruchu przychodzącego, formanty, sieć wirtualna ma __Tag usługi__ z __AzureMachineLearning__

    ![Jak dodać zasoby obliczeniowe w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. W [witryny Azure portal](https://portal.azure.com), wybierz swój obszar roboczy usługi Azure Machine Learning.

1. W __aplikacji__ zaznacz __obliczenia__. Następnie wybierz pozycję __Dodaj obliczenie__. 

    ![Jak dodać zasoby obliczeniowe w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Aby skonfigurować ten zasób obliczeniowy do sieci wirtualnej, należy użyć tych opcji:

    - __Konfiguracja sieci__: Wybierz pozycję __Zaawansowane__.

    - __Grupa zasobów__: Wybierz grupę zasobów, która zawiera sieć wirtualną.

    - __Sieć wirtualna__: Wybierz sieć wirtualną, która zawiera podsieci.

    - __Podsieć__: Wybierz podsieć.

    - __Zakres adresów usługi Kubernetes__: Wybierz zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP notacji CIDR, do definiowania adresów IP dostępnych dla klastra. Nie nakładać się na żadne zakresy adresów IP podsieci. Na przykład: 10.0.0.0/16.

    - __Adres IP usługi DNS platformy Kubernetes__: Wybierz adres IP usługi DNS platformy Kubernetes. Ten adres IP jest przypisywany do usługi DNS platformy Kubernetes. Należy do zakresu adresów usługi Kubernetes. Na przykład: 10.0.0.10.

    - __Adres mostka platformy docker__: Wybierz adres mostka platformy Docker. Ten adres IP jest przypisywany do mostka platformy Docker. Nie może być w żadnych zakresów adresów IP podsieci i zakres adresów usługi Kubernetes. Na przykład: 172.17.0.1/16.

   ![Usługa Azure Machine Learning: Usługi Machine Learning obliczenia ustawień sieci wirtualnej](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Upewnij się, że sieciowa grupa zabezpieczeń grupy, która włączona dla punktu końcowego oceniania, dzięki czemu mogą być wywoływane z spoza sieci wirtualnej regułę ruchu przychodzącego, formanty, które ma sieci wirtualnej

    ![Jak dodać zasoby obliczeniowe w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Jeśli masz już klaster AKS w sieci wirtualnej, można dołączyć ją do obszaru roboczego. Aby uzyskać więcej informacji, zobacz [sposób wdrażania usługi AKS](how-to-deploy-to-aks.md).

Można również użyć **zestawu SDK usługi Azure Machine Learning** do dodania usługi Azure Kubernetes Service w sieci wirtualnej. Poniższy kod tworzy nowe wystąpienie usługi Azure Kubernetes Service w `default` podsieci sieci wirtualnej o nazwie `mynetwork`:

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
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

Po ukończeniu procesu tworzenia, możesz tworzyć wnioskowania w klastrze AKS za sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [sposób wdrażania usługi AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie środowisk szkolenia](how-to-set-up-training-targets.md)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Bezpiecznie Wdrażaj modele przy użyciu protokołu SSL](how-to-secure-web-service.md)

