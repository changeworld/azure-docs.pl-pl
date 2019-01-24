---
title: Uruchamianie eksperymentów i wnioskowania w sieci wirtualnej
titleSuffix: Azure Machine Learning service
description: Uruchamiaj eksperymenty uczenia maszynowego i wnioskowania zabezpieczanie wewnątrz usługi Azure Virtual Network. Dowiedz się, jak utworzyć obliczeniowych elementów docelowych do trenowania modelu oraz sposób wnioskowanie w ramach usługi Azure Virtual Network. Obejmuje ona również wymagania dotyczące zabezpieczonej sieci wirtualnych, takich jak wymaga portów przychodzących i wychodzących.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 21c4a9042a64a58f67f1f94c300b6438895eea5d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856285"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Bezpiecznego uruchamiania eksperymentów oraz wnioskowania wewnątrz usługi Azure Virtual Network

W tym artykule dowiesz się, jak można uruchamiać eksperymenty i wnioskowania wewnątrz sieci wirtualnej. Sieć wirtualna działa jak granica bezpieczeństwa, izolowanie zasobów platformy Azure z publicznego Internetu. Usługi Azure Virtual Network można również dołączyć do sieci lokalnej. Umożliwia ona bezpiecznie szkolenie modeli i uzyskiwać dostęp do Twojej wdrożonej modeli do wnioskowania.

Usługa Azure Machine Learning, zależy od innych usług platformy Azure dla zasobów obliczeniowych elementów. Zasoby obliczeniowe (celów obliczeń) są używane do uczenia i wdrażania modeli. Te obliczenia obiekty docelowe mogą być tworzone w sieci wirtualnej. Na przykład można użyć maszyny Wirtualnej do nauki o danych do nauczenia modelu, a następnie wdrożymy model dla usługi Azure Kubernetes Service. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [omówienie sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) dokumentu.

## <a name="storage-account-for-your-workspace"></a>Konto magazynu dla obszaru roboczego

Podczas tworzenia obszaru roboczego usługi Azure Machine Learning service, konieczne jest posiadanie konta usługi Azure Storage. Nie należy włączać reguły zapory dla tego konta magazynu. Usługa Azure Machine Learning wymaga nieograniczonego dostępu do konta magazynu.

Jeśli nie masz pewności, czy zostały zmodyfikowane tych ustawień lub nie można znaleźć __zmienić domyślną regułę dostępu sieciowego__ części [zapory Konfigurowanie usługi Azure Storage i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security) dokumentów i użyć kroki, aby _zezwolić na dostęp_ z __wszystkie sieci__.

## <a name="use-machine-learning-compute"></a>Użyj usługi Machine Learning obliczeń

Można użyć obliczeniowego usługi Machine Learning w sieci wirtualnej, aby poznać wymagania sieci należy użyć następujące informacje:

- Sieć wirtualna musi być w tej samej subskrypcji i regionie co obszar roboczy usługi Azure Machine Learning.

- Podsieć określona dla klastra obliczeniowego usługi Machine Learning musi mieć wystarczającej liczby nieprzypisanych adresów IP, aby uwzględnić liczbę maszyn wirtualnych przeznaczony dla klastra. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster zostanie przydzielony częściowo.

- Jeśli planowane jest do zabezpieczania sieci wirtualnej przez ograniczenie ruchu, niektóre porty musi pozostać otwarte na potrzeby usługi obliczeniowego usługi Machine Learning. Aby uzyskać więcej informacji, zobacz [wymagane porty](#mlcports) sekcji.

- Sprawdź, czy Twoje zasady zabezpieczeń lub blokuje subskrypcji lub grupy zasobów sieci wirtualnej Ogranicz uprawnienia do zarządzania sieci wirtualnej.

- Jeśli chcesz umieścić wielu klastrów obliczeniowego usługi Machine Learning w jednej sieci wirtualnej, może być konieczne zażądać zwiększenia limitu przydziału dla jednego lub więcej zasobów.

    Obliczeniowego usługi Machine Learning jest automatycznie alokowana dodatkowych zasobów sieciowych w grupie zasobów zawierająca sieć wirtualną. Dla każdego klastra obliczeniowego usługi Machine Learning usługi Azure Machine Learning przydziela następujące zasoby: 

    - Co sieciowa grupa zabezpieczeń (NSG)

    - Jeden publiczny adres IP

    - Jedna usługa równoważenia obciążenia

    Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-subscription-service-limits) subskrypcji. 

### <a id="mlcports"></a> Wymagane porty

Obecnie obliczeniowego usługi Machine Learning korzysta z usługi Azure Batch aprowizację maszyn wirtualnych w określonej sieci wirtualnej. Podsieć musi zezwalać na przychodzący komunikacji z usługą Batch. Ta komunikacja służy do planowania jest uruchamiany w węzłach obliczeniowego usługi Machine Learning, a także nawiązać połączenia z usługą Azure Storage i innych zasobów. Usługa Batch dodaje sieciowe grupy zabezpieczeń na poziomie interfejsów sieciowych (NIC) dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Ruch przychodzący protokołu TCP na porty 29876 i 29877 z adresów IP roli usługi Batch. 
 
- Ruch przychodzący protokołu TCP na porcie 22, aby zezwolić na zdalny dostęp.
 
- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu.

Jeśli modyfikujesz lub dodać reguły dla ruchu przychodzącego/wychodzącego w skonfigurowane usługi Batch sieciowych grup zabezpieczeń należy zachować ostrożność. Jeśli sieciowa grupa zabezpieczeń blokuje komunikacji węzłów obliczeniowych, następnie obliczeniowego usługi Machine Learning services ustawia stan węzłów obliczeniowych na korzystanie z niej.

Nie trzeba określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Batch konfiguruje własne sieciowe grupy zabezpieczeń. Jednak jeśli określona podsieć ma skojarzone sieciowe grupy zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń ruchu przychodzącego i wychodzącego, jak wspomniano wcześniej. Poniższe zrzuty ekranu pokazują, jak wygląda konfiguracji reguły w witrynie Azure portal:

![Zrzut ekranu przedstawiający dla ruchu przychodzącego sieciowej grupy zabezpieczeń reguły obliczeniowego usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Zrzut ekranu przedstawiający wychodzące reguły sieciowej grupy zabezpieczeń, obliczeniowego usługi Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Tworzenie obliczeniowego usługi Machine Learning w sieci wirtualnej

Do utworzenia klastra obliczeniowego usługi Machine Learning za pomocą **witryny Azure portal**, wykonaj następujące czynności:

1. Z [witryny Azure portal](https://portal.azure.com), wybierz swój obszar roboczy usługi Azure Machine Learning.

1. Z __aplikacji__ zaznacz __obliczenia__. Następnie wybierz pozycję __Dodaj obliczenie__. 

    ![Jak dodać zasoby obliczeniowe w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Aby skonfigurować ten zasób obliczeniowy do sieci wirtualnej, należy użyć tych opcji:

    - __Konfiguracja sieci__: Wybierz pozycję __Zaawansowane__.

    - __Grupa zasobów__: Wybierz grupę zasobów, która zawiera sieć wirtualną.

    - __Sieć wirtualna__: Wybierz sieć wirtualną, która zawiera podsieci.

    - __Podsieć__: Wybierz podsieć do użycia.

    ![Zrzut ekranu przedstawiający ustawienia sieci wirtualnej, machine learning obliczeniowych](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Można również utworzyć obliczeniowego usługi Machine Learning klaster przy użyciu **zestawu SDK usługi Azure Machine Learning**. Następujący kod umożliwia utworzenie nowego klastra obliczeniowego usługi Machine Learning w `default` podsieci sieci wirtualnej o nazwie `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure Virtual Network name, subnet, and resource group
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

Po zakończeniu procesu tworzenia, możesz uczenie modelu przy użyciu klastra. Aby uzyskać więcej informacji, zobacz [wybierz, a następnie użyć obliczeniowego elementu docelowego do trenowania](how-to-set-up-training-targets.md) dokumentu.

## <a name="use-a-virtual-machine-or-hdinsight"></a>Użyj maszyny wirtualnej lub HDInsight

Aby użyć maszyny wirtualnej lub klastra HDInsight w sieci wirtualnej z obszaru roboczego, należy użyć następujące czynności:

> [!IMPORTANT]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu.

1. Tworzenie klastra HDInsight lub maszyny Wirtualnej przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure, itp. i umieścić go w usłudze Azure Virtual Network. Więcej informacji na ten temat można znaleźć w następujących dokumentach:
    * [Tworzenie i zarządzanie sieciami wirtualnymi platformy Azure dla maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozszerzanie przy użyciu sieci wirtualnych Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Aby umożliwić usłudze Azure Machine Learning do komunikowania się z portem SSH na maszynie Wirtualnej lub w klastrze, należy skonfigurować wpis źródła sieciowej grupy zabezpieczeń. SSH port jest zazwyczaj port 22. Aby zezwolić na ruch z tego źródła, skorzystaj z poniższych informacji:

    * __Źródło__: Wybierz pozycję __Tag usługi__

    * __Tag usługi źródłowej__: Wybierz __AzureMachineLearning__

    * __Źródłowe zakresy portów__: Wybierz pozycję __*__

    * __Miejsce docelowe__: Wybierz __wszystkie__

    * __Zakresy portów docelowych__: Wybierz __22__

    * __Protokół__: Wybierz __wszystkie__

    * __Akcja__: Wybierz __Zezwalaj__

   ![Zrzut ekranu przedstawiający reguły dla ruchu przychodzącego do wykonywania eksperymentów na maszynie Wirtualnej lub HDInsight wewnątrz sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Zachowaj ustawienie domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły sekcji Zabezpieczenia [grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) dokumentacji.
    
1. Dołączanie maszyny Wirtualnej lub HDInsight klastra do swojego obszaru roboczego usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie obliczeniowych elementów docelowych do trenowania modelu](how-to-set-up-training-targets.md) dokumentu.

## <a name="use-azure-kubernetes-service-aks"></a>Użyj usługi Azure Kubernetes Service (AKS)

> [!IMPORTANT]
> Sprawdź wymagania wstępne i planowanie adresowania IP dla klastra, przed kontynuowaniem wykonując kroki wymienione poniżej. Możesz zapoznać się z [skonfiguruj zaawansowane sieci w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking)
> 
> Zachowaj ustawienie domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły sekcji Zabezpieczenia [grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) dokumentacji.
>
> Usługa Azure Kubernetes Service i Azure Virtual Network powinny być w tym samym regionie.

Aby dodać usługi Azure Kubernetes Service w sieci wirtualnej, do obszaru roboczego, użyj wykonaj kroki opisane w __witryny Azure portal__:

1. Z [witryny Azure portal](https://portal.azure.com), wybierz swój obszar roboczy usługi Azure Machine Learning.

1. Z __aplikacji__ zaznacz __obliczenia__. Następnie wybierz pozycję __Dodaj obliczenie__. 

    ![Jak dodać zasoby obliczeniowe w usłudze Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Aby skonfigurować ten zasób obliczeniowy do sieci wirtualnej, należy użyć tych opcji:

    - __Konfiguracja sieci__: Wybierz pozycję __Zaawansowane__.

    - __Grupa zasobów__: Wybierz grupę zasobów, która zawiera sieć wirtualną.

    - __Sieć wirtualna__: Wybierz sieć wirtualną, która zawiera podsieci.

    - __Podsieć__: Wybierz podsieć.

    - __Zakres adresów usługi Kubernetes__: Wybierz zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP notacji CIDR, do definiowania adresów IP dostępnych dla klastra. Nie nakładać się na żadne zakresy adresów IP podsieci. Na przykład: 10.0.0.0/16.

    - __Adres IP usługi DNS platformy Kubernetes__: Wybierz adres IP usługi DNS platformy Kubernetes. Ten adres IP jest przypisywany do usługi DNS platformy Kubernetes. Należy do zakresu adresów usługi Kubernetes. Na przykład: 10.0.0.10.

    - __Adres mostka platformy docker__: Wybierz adres mostka platformy Docker. Ten adres IP jest przypisywany do mostka platformy Docker. Nie może być w dowolnym zakresy adresów IP podsieci lub zakresu adresów usługi Kubernetes. Na przykład: 172.17.0.1/16

   ![Usługa Azure Machine Learning: Ustawienia sieci wirtualnej obliczeń uczenia maszynowego](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Jeśli masz już klaster AKS w sieci wirtualnej, można dołączyć ją do obszaru roboczego. Aby uzyskać więcej informacji, zobacz [sposób wdrażania usługi AKS](how-to-deploy-to-aks.md).

Można również użyć **zestawu SDK usługi Azure Machine Learning** można dodać usługi Kubernetes na platformie Azure w sieci wirtualnej. Poniższy kod tworzy nowe usługi Azure Kubernetes Service w `default` podsieci sieci wirtualnej o nazwie `mynetwork`:

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

Po zakończeniu procesu tworzenia, możesz tworzyć wnioskowania w klastrze AKS za sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [sposób wdrażania usługi AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie środowisk szkolenia](how-to-set-up-training-targets.md)
* [Gdzie można wdrażać modele](how-to-deploy-and-where.md)
* [Bezpieczne modeli wdrożonych przy użyciu protokołu SSL](how-to-secure-web-service.md)
