---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 07/02/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 77da95ce0d37e8d0b35125f0cc8dc33376a52259
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47401550"
---
Organizacje mają potrzebami obliczeniowymi na dużą skalę. Tych obciążeń funkcji dużych obliczeń obejmują projektowaniem inżynierskim i analizy, obliczeniami w zakresie ryzyka finansowego, renderowaniem obrazu, złożonym modelowaniem, Symulacje Monte Carlo i inne. 

Wydajne działanie obciążeń systemu Linux i Windows intensywnych obliczeń z zadań wsadowych równoległego do tradycyjnych symulacje HPC za pomocą chmury Azure. Uruchom usługi HPC i obciążeń usługi batch w infrastrukturze platformy Azure, dzięki różnorodnym obliczenia usług, menedżerów siatki, rozwiązaniami z witryny Marketplace i aplikacjami hostowanymi w dostawcy (SaaS). System Azure oferuje elastyczne rozwiązania do dostarczania pracy i skalowanie do tysięcy maszyn wirtualnych lub rdzeni i skalowanie w dół, jeśli potrzebnych będzie mniej zasobów. 



## <a name="solution-options"></a>Opcje rozwiązania



* **Utworzyliśmy rozwiązania**
    * Konfigurowanie własnego środowiska klastra na maszynach wirtualnych platformy Azure lub [zestawy skalowania maszyn wirtualnych](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Przenoszenie i shift lokalny klaster lub wdrożenie nowego klastra na platformie Azure za dodatkową pojemność. 
    * Szablony usługi Azure Resource Manager umożliwiają wdrażanie wiodących [obciążenie menedżerów](#workload-managers), infrastruktury i [aplikacji](#hpc-applications). 
    * Wybierz [rozmiary HPC i maszyn wirtualnych GPU](#hpc-and-gpu-sizes) zawierające wyspecjalizowanego sprzętu i połączenia sieciowe dla obciążeń MPI lub procesora GPU. 
    * Dodaj [magazyn o wysokiej wydajności](#hpc-storage) dla wyjścia — dużych obciążeń wejścia /.
* **Hybrydowe rozwiązania**
    * Rozszerzanie rozwiązania w środowisku lokalnym w celu odciążenia obciążeń maksymalnych ("serii") do infrastruktury platformy Azure
    * Za pomocą usług chmury obliczeniowej na żądanie istniejącą [Menedżera obciążenia](#workload-manager).
    * Skorzystaj z zalet [rozmiary HPC i maszyn wirtualnych GPU](#hpc-and-gpu-sizes) dla obciążeń MPI lub procesora GPU.
* **Dużych rozwiązań obliczeniowych jako usługa**
    * Twórz niestandardowe rozwiązania dużych wystąpień obliczeniowych i przepływów pracy za pomocą [usługi Azure Batch](#azure-batch) i pokrewnych [usług platformy Azure](#related-azure-services).
    * Uruchamianie z obsługą usługi Azure inżynierii i symulacje inżynierskie rozwiązań od dostawców, w tym [Altair](http://www.altair.com/), [ponowne skalowanie](https://www.rescale.com/azure/), i [Cycle Computing](https://cyclecomputing.com/) (teraz [sprzężony z Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Użyj [superkomputer Cray](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) jako usługa hostowana na platformie Azure.
* **Rozwiązaniami z witryny Marketplace**
    * Użyj skali [aplikacje HPC](#hpc-applications) i [rozwiązania](#marketplace-solutions) oferowana w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 
    


Poniższe sekcje zawierają więcej informacji na temat technologii pomocniczych i linki do wskazówek.



## <a name="marketplace-solutions"></a>Rozwiązania z witryny Marketplace

Odwiedź stronę [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) dla obrazów systemu Linux i Windows VM i rozwiązań zaprojektowanych dla HPC. Przykłady:

* [RogueWave opartych na systemie CentOS HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [Aparat serwera siatki TIBCO](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Maszyny Wirtualnej dla Windows i Linux do analizy danych platformy Azure](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel Cloud Edition for Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.intel-cloud-edition-gs)


 
## <a name="hpc-applications"></a>Aplikacje HPC

Uruchom niestandardowe lub komercyjne aplikacje HPC na platformie Azure. Kilka przykładów w tej sekcji jest badany wydajne skalowanie przy użyciu dodatkowych maszyn wirtualnych lub rdzeni obliczeniowych. Odwiedź stronę [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) rozwiązania gotowe do wdrożenia.

> [!NOTE]
> Skontaktuj się z dostawcą aplikacji komercyjnej w przypadku licencjonowania lub innych ograniczeń do uruchamiania w chmurze. Nie wszyscy dostawcy oferują licencjonowanie oparte na płatności zgodnie z rzeczywistym użyciem. Może być konieczne serwera licencyjnego w chmurze dla rozwiązania lub nawiązać połączenie z lokalnym serwerem licencji.

### <a name="engineering-applications"></a>Inżynieria aplikacji


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB rozproszonego przetwarzania serwera](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Grafika i renderowanie

* [Programy Autodesk Maya, 3ds Max i Arnold](../articles/batch/batch-rendering-service.md) w usłudze Azure Batch 

### <a name="ai-and-deep-learning"></a>Sztucznej Inteligencji i uczenia głębokiego

* [Z usługi Batch AI](../articles/batch-ai/overview.md) szkolenie modeli uczenia głębokiego
* [Zestaw narzędzi usług Microsoft Cognitive](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Maszyna wirtualna do uczenia głębokiego](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Przepisy usługa Shipyard usługi Batch na potrzeby uczenia głębokiego](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Rozmiary HPC i procesora GPU maszyny Wirtualnej
Platforma Azure oferuje szeroką gamę rozmiarów [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) maszyn wirtualnych, w tym rozmiary zaprojektowana dla obciążeń intensywnie korzystających z obliczeń. Na przykład H16r i H16mr maszyn wirtualnych można połączyć się z siecią RDMA back-end wysokiej przepływności. Tej sieci. cloud może poprawić wydajność ściśle sprzężonych równoległych aplikacje działające w środowisku [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) lub Intel MPI. 

Maszyny wirtualne z serii N są wyposażone w procesory GPU NVIDIA przeznaczone dla aplikacji intensywnie lub dużą ilością grafiki, w tym uczenie sztucznej inteligencji (AI) i wizualizacji. 

Więcej informacji:

* O wysokiej wydajności obliczeń rozmiary [Linux](../articles/virtual-machines/linux/sizes-hpc.md) i [Windows](../articles/virtual-machines/windows/sizes-hpc.md) maszyn wirtualnych 
* Rozmiary włączonymi procesorami GPU [Linux](../articles/virtual-machines/linux/sizes-gpu.md) i [Windows](../articles/virtual-machines/windows/sizes-gpu.md) maszyn wirtualnych 

Instrukcje:

* [Konfigurowanie klastra RDMA systemu Linux do uruchamiania aplikacji MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Konfigurowanie klastra Windows RDMA za pomocą pakietu Microsoft HPC Pack do uruchamiania aplikacji MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Używanie maszyn wirtualnych intensywnie korzystających z obliczeń w pulach usługi Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) jest przetwarzanie platformy usługi do uruchamiania dużych równoległych i o wysokiej wydajności aplikacji (HPC), wydajnie w chmurze. Azure Batch umożliwia planowanie intensywnych obliczeń do uruchamiania na zarządzanej puli maszyn wirtualnych oraz może automatycznie skalować zasoby obliczeniowe na potrzeby zadań. 

Dostawców SaaS lub deweloperzy mogą używać zestawów SDK usługi Batch i narzędzi do integracji aplikacji HPC lub obciążeń kontenerów za pomocą platformy Azure, przemieszczać dane na platformie Azure i tworzyć potoki wykonywania zadań. 

Instrukcje:

* [Rozpocznij tworzenie aplikacji przy użyciu usługi Batch](../articles/batch/quick-run-dotnet.md)
* [Przykłady kodu usługi Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch](../articles/batch/batch-low-pri-vms.md)
* [Uruchamianie konteneryzowanych obciążeń HPC przy użyciu usługa Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Uruchamianie równoległe obciążenia języka R w usłudze Batch](https://github.com/Azure/doAzureParallel)
* [Uruchamianie zadań Spark na żądanie w usłudze Batch](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Obciążenie menedżerów

Poniżej przedstawiono przykłady menedżerów klastra i obciążeń, które mogą być uruchamiane w infrastrukturze platformy Azure. Utwórz autonomicznych klastrów w maszynach wirtualnych platformy Azure lub przenoszenie dużego ruchu do maszyn wirtualnych platformy Azure z klastra lokalnego. 
* [Lotu Alces obliczeń](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Jasny Menedżera klastra](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony i Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Pakiet Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx) — Zobacz opcje do uruchomienia w [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) maszyn wirtualnych 



## <a name="hpc-storage"></a>Magazyn HPC

Obciążenia na dużą skalę usługi Batch i HPC mają wymagania dla magazynu danych i dostępu, które przekraczają możliwości chmury tradycyjnych systemów plików. Implementacja rozwiązania systemu plików równolegle na platformie Azure, takich jak [Lustre](http://lustre.org/) i [BeeGFS](http://www.beegfs.com/content/).

Więcej informacji:

* [Równoległe systemy plików wirtualnych na platformie Azure](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/)
* Rozwiązania przechowywania w chmurze o wysokiej wydajności z [Avere](http://www.averesystems.com/about-us/about-avere) (teraz [połączone z firmą Microsoft](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))


## <a name="related-azure-services"></a>Pokrewne usługi platformy Azure

Maszyny wirtualne platformy Azure, zestawy skalowania maszyn wirtualnych, usługi Batch i usług obliczeniowych powiązane są podstawą większości rozwiązań HPC na platformie Azure. Jednak rozwiązanie korzystać z zalet pokrewne usługi platformy Azure. Oto częściowa lista:

### <a name="storage"></a>Magazyn

* [Obiekt blob, tabela i queue storage](../articles/storage/storage-introduction.md)
* [Usługa File storage](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Dane i analiza
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Fabryka danych](../articles/data-factory/introduction.md)
* [Data Lake Storage Gen1](../articles/data-lake-store/data-lake-store-overview.md)
* [Usługi Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>Sztucznej Inteligencji i uczenia maszynowego
* [Usługi Machine Learning](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Usługa Batch — sztuczna inteligencja](../articles/batch-ai/overview.md)
* [Genomics](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Networking
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Containers
* [Container Service](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Usługa Azure Kubernetes Service (AKS)](../articles/aks/intro-kubernetes.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Historie klientów

Przykłady klientów, które zostały rozwiązane problemy biznesowe przy użyciu rozwiązania HPC platformy Azure:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA globalnego P & C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Firmie Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [D3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [SYSTEM SZYFROWANIA PLIKÓW](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Firma Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ papierów wartościowych International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [NeuroInitiative](https://customers.microsoft.com/en-us/story/neuroinitiative-health-provider-azure)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Firma towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o rozwiązaniach dużych obliczeń dla [inżynierii symulacji](https://simulation.azure.com/), [renderowania](https://azure.microsoft.com/solutions/big-compute/rendering/), [bankowości i rynków kapitałowych](https://finance.azure.com/), i [genomics](https://enterprise.microsoft.com/en-us/industries/health/genomics/) .
* Najnowsze informacje można znaleźć na [blogu zespołu Microsoft HPC i usługi Batch](http://blogs.technet.com/b/windowshpc/) oraz [blogu platformy Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Korzystanie z systemu Azure zarządzane i skalowalne [partii](https://azure.microsoft.com/services/batch/) Usługa do uruchamiania obciążeń intensywnych obliczeń bez konieczności zarządzania podstawową infrastrukturą [Dowiedz się więcej](https://azure.microsoft.com/solutions/architecture/hpc-big-compute-saas/)



