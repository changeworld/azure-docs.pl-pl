---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 05/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32a438d393077cfe4cb7f6ee62f3a01edfce0571
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
Organizacje mają wielkoskalowe potrzeby przetwarzania danych. Te obciążenia obliczeniowe Big obejmują projektu inżynieryjnego i analizy, obliczeń finansowych ryzyka, renderowanie obrazów, modelowania złożonych, Symulacje Monte Carlo i więcej. 

Chmury Azure umożliwia wydajne działanie obciążeń systemu Linux i Windows obliczeniowych, z zadań wsadowych równoległych do tradycyjnych symulacje HPC. Uruchamianie programu HPC i partii obciążenie infrastruktury platformy Azure przy użyciu wybranych obliczeniowe usług, menedżerów siatki Marketplace rozwiązań i aplikacje obsługiwane w dostawcy (SaaS). Platforma Azure oferuje elastyczne rozwiązania rozpraszających i skalowanie do tysięcy maszyn wirtualnych lub rdzeni i następnie w dół gdy potrzebne są mniej zasobów. 



## <a name="solution-options"></a>Opcje rozwiązania



* **Zrób rozwiązania**
    * Konfigurowanie własnego środowiska klastra w maszynach wirtualnych platformy Azure lub [zestawy skalowania maszyny wirtualnej](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Podnieś przesunięcia klastra lokalnego i wdrażanie nowego klastra w systemie Azure dodatkowej pojemności. 
    * Korzystanie z szablonów usługi Azure Resource Manager do wdrażania wiodące [obciążenie menedżerów](#workload-managers), infrastruktury i [aplikacji](#hpc-applications). 
    * Wybierz [rozmiary HPC i wirtualna GPU](#hpc-and-gpu-sizes) zawierające specjalne sprzętu i połączenia sieciowe dla obciążeń MPI lub procesora GPU. 
    * Dodaj [wysoka wydajność magazynu ](#hpc-storage) /O wykonujących obciążeń.
* **Rozwiązania hybrydowe**
    * Rozszerzenia rozwiązania lokalnego odciążania obciążenia szczytowego ("serii") do infrastruktury platformy Azure
    * Użyj chmury obliczeń na żądanie z istniejącą [Menedżera obciążenia](#workload-manager).
    * Korzystać z [rozmiary HPC i wirtualna GPU](#hpc-and-gpu-sizes) dla obciążeń MPI lub procesora GPU.
* **Duży rozwiązań obliczeniowe jako usługa**
    * Tworzenie niestandardowych rozwiązań obliczeniowe duży i przepływów pracy za pomocą [partii zadań Azure](#azure-batch) i pokrewnych [usług Azure](#related-azure-services).
    * Uruchom włączone Azure inżynieryjne i symulacji rozwiązania z producentami, w tym [Altair](http://www.altair.com/), [ponowne skalowanie](https://www.rescale.com/azure/), i [przetwarzania danych w cyklu](https://cyclecomputing.com/) (teraz [połączony z Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Użyj [superkomputera Cray](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) usługa hostowana na platformie Azure.
* **Rozwiązania z witryny Marketplace**
    * Użyj skali [aplikacji HPC](#hpc-applications) i [rozwiązań](#marketplace-solutions) oferowany [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/). 
    


Poniższe sekcje zawierają więcej informacji na temat obsługi technologii i linki do wskazówek.



## <a name="marketplace-solutions"></a>Rozwiązania z witryny Marketplace

Odwiedź stronę [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) dla obrazów systemów Linux i maszyny Wirtualnej systemu Windows i rozwiązań, przeznaczone dla HPC. Przykłady:

* [RogueWave na podstawie CentOS HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server dla HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [Aparat serwera siatki TIBCO](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Nauki danych Azure maszyny Wirtualnej systemu Windows i Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [d3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel chmury Edition dla połysk](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>Aplikacje HPC

Uruchamianie aplikacji HPC niestandardowych lub komercyjnych na platformie Azure. Kilka przykładów w tej sekcji są badany wydajne skalowanie dodatkowych maszyn wirtualnych lub obliczeniowe rdzeni. Odwiedź stronę [portalu Azure Marketplace](https://marketplace.azure.com) gotowe do wdrożenia rozwiązania.

> [!NOTE]
> Skontaktuj się z dostawcą komercyjnych stosowania licencjonowania lub inne ograniczenia dotyczące uruchamiania w chmurze. Nie wszyscy dostawcy oferują licencjonowanie oparte na płatności zgodnie z rzeczywistym użyciem. Może być potrzebny serwer licencjonowania w chmurze dla rozwiązania lub Połącz z lokalnego serwera licencji.

### <a name="engineering-applications"></a>Aplikacje zespołu inżynieryjnego


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB rozproszonego przetwarzania danych serwera](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Grafika i renderowania

* [Plik Autodesk Maya, 3ds Max i Arnold](../articles/batch/batch-rendering-service.md) w partii zadań Azure 

### <a name="ai-and-deep-learning"></a>AI i uczenie się bezpośrednich

* [Partii AI](../articles/batch-ai/overview.md) szkoleń dla modeli uczenia bezpośrednich
* [Zestaw narzędzi usług Microsoft Cognitive](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Bezpośrednie uczenia maszyny Wirtualnej](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Przepisami stoczni partii głębokie szkoleniowe](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Rozmiary HPC i procesora GPU maszyny Wirtualnej
Platforma Azure oferuje szeroką gamę rozmiary [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) maszyn wirtualnych, w tym rozmiary przeznaczone dla obciążeń obliczeniowych. Na przykład H16r i H16mr maszyn wirtualnych można połączyć się z sieci RDMA zaplecza wysokiej przepływności. Ta sieć chmury może poprawić wydajność silnie sprzężonego równoległych aplikacji do uruchamiania [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) lub Intel MPI. 

N-series maszyn wirtualnych funkcja NVIDIA GPU przeznaczone dla aplikacji obliczeniowych lub dużą ilością grafiki, w tym uczenie sztucznego analizy (AI) i wizualizacji. 

Więcej informacji:

* Wysoka wydajność obliczeniowe rozmiary [Linux](../articles/virtual-machines/linux/sizes-hpc.md) i [Windows](../articles/virtual-machines/windows/sizes-hpc.md) maszyny wirtualne 
* Rozmiary włączone GPU [Linux](../articles/virtual-machines/linux/sizes-gpu.md) i [Windows](../articles/virtual-machines/windows/sizes-gpu.md) maszyny wirtualne 

Instrukcje:

* [Konfigurowanie klastra Linux RDMA na uruchamianie aplikacji MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Konfigurowanie klastra RDMA systemu Windows z pakietem Microsoft HPC do uruchamiania aplikacji MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Użyj obliczeniowych maszyn wirtualnych w puli partii](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Wsadowe](../articles/batch/batch-technical-overview.md) Trwa obliczanie platformy usług do uruchomienia równoległego na dużą skalę i wysokiej wydajności (HPC) aplikacji wydajnie w chmurze. Harmonogramy usługi partia zadań Azure obliczeniowych działają w puli zarządzanej maszyn wirtualnych, a można automatycznie skali zasoby obliczeniowe do potrzeb zadań. 

Dostawców w modelu SaaS lub deweloperom można użyć partii zestawy SDK i narzędzia do integracji HPC aplikacji lub obciążeń kontenera platformy Azure, etap danych na platformie Azure i potoki wykonanie zadania kompilacji. 

Instrukcje:

* [Wprowadzenie do programowania z użyciem partii](../articles/batch/quick-run-dotnet.md)
* [Użyj przykłady kodu partii zadań Azure](https://github.com/Azure/azure-batch-samples)
* [Niskiego priorytetu maszyn wirtualnych za pomocą usługi partia zadań](../articles/batch/batch-low-pri-vms.md)
* [Uruchom konteneryzowanych obciążeń HPC z stoczni partii](https://github.com/Azure/batch-shipyard)
* [Uruchamianie równoległego obciążeń R w partii](https://github.com/Azure/doAzureParallel)
* [Uruchamianie zadań Spark na żądanie w partii](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Menedżerowie obciążenia

Poniżej przedstawiono przykłady menedżerów klastra i obciążenia, które można uruchomić w infrastrukturę platformy Azure. Tworzenie autonomicznych klastrów w maszynach wirtualnych platformy Azure lub serii na maszynach wirtualnych platformy Azure z klastra lokalnego. 
* [Transmitowane Alces obliczeń](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Jasny Menedżera klastra](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM spektrum Symphony i Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx) — Zobacz opcje do uruchomienia w [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) maszyny wirtualne 



## <a name="hpc-storage"></a>Magazyn HPC

Obciążeń na dużą skalę partii i HPC ma wymagań dla magazynu danych i dostępu, które znacznie przekraczają możliwości chmury tradycyjnych systemów plików. Takich jak wdrożenia rozwiązań system równoległych plików na platformie Azure [połysk](http://lustre.org/) i [BeeGFS](http://www.beegfs.com/content/).

Więcej informacji:

* [Równoległe systemów plików wirtualnych na platformie Azure](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/)
* Wysokowydajne rozwiązania magazynu chmury z [Avere](http://www.averesystems.com/about-us/about-avere) (teraz [połączony z Microsoft](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))


## <a name="related-azure-services"></a>Pokrewne usługi platformy Azure

Maszyny wirtualne platformy Azure, zestawy skalowania maszyny wirtualnej, partii i obliczeń pokrewne usługi są podstawę większości rozwiązań Azure HPC. Rozwiązania można jednak korzystać z wielu powiązanych usług Azure. Oto częściowej listy:

### <a name="storage"></a>Magazyn

* [Obiekt blob, tabel i magazyn kolejek](../articles/storage/storage-introduction.md)
* [Magazyn plików](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Dane i analiza
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Fabryka danych](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>AI i uczenia maszynowego
* [Usługi Machine Learning](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Usługa Batch — sztuczna inteligencja](../articles/batch-ai/overview.md)
* [Genomika](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Networking
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Containers
* [Container Service](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Usługa Azure Kubernetes (AKS)](../articles/aks/intro-kubernetes.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Historie klientów

Przykłady klientów, które zostały rozwiązane problemy biznesowe z rozwiązaniami Azure HPC:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA globalne P & C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [SYSTEM SZYFROWANIA PLIKÓW](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ zabezpieczeń międzynarodowych](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Wieże Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Kolejne kroki
* Więcej informacji na temat rozwiązania obliczeniowe Big [inżynierii symulacji](https://simulation.azure.com/), [renderowania](https://azure.microsoft.com/solutions/big-compute/rendering/), [rynkach bankowości i inwestycyjne](https://finance.azure.com/), i [genomika](https://enterprise.microsoft.com/en-us/industries/health/genomics/) .
* Najnowsze informacje można znaleźć na [blogu zespołu Microsoft HPC i usługi Batch](http://blogs.technet.com/b/windowshpc/) oraz [blogu platformy Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Użyj Azure zarządzane i skalowalne [partii](https://azure.microsoft.com/services/batch/) usługi do uruchamiania obciążeń obliczeniowych, bez konieczności zarządzania podstawowej infrastruktury [Dowiedz się więcej](https://azure.microsoft.com/solutions/architecture/hpc-big-compute-saas/)



