---
title: Co się dzieje w usłudze Apache Batch AI? | Microsoft Docs
description: Dowiedz się więcej o tym, co się dzieje z usługą Azure Batch AI i opcją obliczeniową usługi Azure Machine Learning.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194506"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Co się dzieje z usługą Apache Batch AI?

**Usługa Azure Batch AI zostanie wycofana w marcu.** Możliwości usługi Batch AI związane z trenowaniem i ocenianiem na dużą skalę są teraz dostępne w [usłudze Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), która stanie się ogólnodostępna 4 grudnia 2018 r.

Wśród wielu możliwości dotyczących uczenia maszynowego usługa Azure Machine Learning obejmuje chmurowy, zarządzany docelowy obiekt obliczeniowy do trenowania, wdrażania i oceniania modeli uczenia maszynowego. Ten docelowy obiekt obliczeniowy jest nazywany [środowiskiem obliczeniowym usługi Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Migrację i korzystanie z tego obiektu warto zacząć już dziś](#migrate). Interakcja z usługą Azure Machine Learning jest możliwa za pomocą wbudowanych zestawów [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md), interfejsu wiersza polecenia i [witryny Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

W tym momencie można użyć istniejącej subskrypcji usługi Azure Batch AI jako przed. Jednak nie można dodawać **nowych subskrypcji** ani nie są dokonywane nowe inwestycje.

Od marca&nbsp;31&#x2c;&nbsp;2019 r, nieużywanej subskrypcji usługi Batch AI nie będą już działać.

## <a name="compare-to-azure-machine-learning"></a>Porównaj z usługi Azure Machine Learning
Jest to usługa w chmurze, która pozwala trenować, wdrażać i automatyzować modele uczenia maszynowego oraz zarządzać nimi. Wszystko to jest możliwe na dużą skalę zapewnianą przez chmurę. Uzyskaj ogólne informacje na temat [usługi Azure Machine Learning Service w tym omówieniu](../machine-learning/service/overview-what-is-azure-ml.md).
 

Typowy cykl życia projektowania modelu obejmuje fazy przygotowywania danych, trenowania i eksperymentowania oraz wdrażania. Ten kompleksowy cykl można orkiestrować przy użyciu potoków uczenia maszynowego.

![Diagram przepływu](./media/overview-what-happened-batch-ai/lifecycle.png)


[Dowiedz się więcej na temat sposobu działania usługi i jej głównych pojęć](../machine-learning/service/concept-azure-machine-learning-architecture.md). Wiele pojęć w przepływie pracy trenowania modelu jest podobnych do pojęć istniejących w usłudze Batch AI. 

Oto mapowanie pomagające w ich zrozumieniu:
 
|Usługa Batch AI|  Usługa Azure Machine Learning|
|:--:|:---:|
|Obszar roboczy|Obszar roboczy|
|Klaster|   Obliczenia typu `AmlCompute`|
|Serwery plików|Magazyny danych|
|Eksperymenty|Eksperymenty|
|Stanowiska|Przebiegi (dozwolone zagnieżdżone przebiegi)|
 
Oto inny widok tej samej tabeli, który pomoże lepiej zwizualizować te informacje:
 
### <a name="batch-ai-hierarchy"></a>Usługa Batch AI hierarchii
![Diagram przepływu](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Hierarchii usługi w usłudze Azure Machine Learning
![Diagram przepływu](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Możliwości platformy
Usługa Azure Machine Learning Service zapewnia świetny zestaw nowych funkcji, w tym kompleksowy stos trenowanie->wdrożenie, którego można używać na potrzeby wdrażania sztucznej inteligencji bez konieczności zarządzania zasobami platformy Azure. Ta tabela zawiera porównanie obsługi funkcji do trenowania w obu usługach.

|Cecha|Usługa Batch AI|Usługa Azure Machine Learning|
|-------|:-------:|:-------:|
|Wybór rozmiaru maszyny wirtualnej |CPU/GPU    |CPU/GPU. Obsługuje również układ FPGA na potrzeby wnioskowania|
|Klaster z obsługą AI (sterowniki, Docker itp.)|  Yes |Yes|
|Przygotowanie węzła| Yes|    Nie|
|Wybór rodziny systemów operacyjnych   |Częściowo    |Nie|
|Maszyny wirtualne dedykowane i o niskim priorytecie  |Yes    |Yes|
|Skalowanie automatyczne   |Yes    |Tak (domyślnie)|
|Czas oczekiwania na skalowanie automatyczne  |Nie |Yes|
|Protokół SSH    |Yes|   Yes|
|Instalowanie na poziomie klastra |Tak (udziały plików, obiekty blob, system plików NFS, niestandardowe)   |Tak (instalowanie lub pobieranie magazynu danych)|
|Trenowanie rozproszone|  Yes |Yes|
|Tryb wykonywania zadań|    Maszyna wirtualna lub kontener|    Kontener|
|Niestandardowy obraz kontenera|    Yes |Yes|
|Dowolny zestaw narzędzi    |Yes    |Tak (uruchamianie skryptów języka Python)|
|Przygotowywanie zadań|    Yes |Jeszcze nie|
|Instalowanie poziomu zadania |Tak (udziały plików, obiekty blob, system plików NFS, niestandardowe)   |Tak (udziały plików, obiekty blob)|
|Monitorowanie zadań     |Za pomocą metody GetJob|    Za pomocą historii uruchamiania (bogatsze informacje, niestandardowe środowisko uruchomieniowe umożliwiające wypychanie dodatkowych metryk)|
|Pobieranie dzienników zadań i plików/modeli |   Za pomocą metody ListFiles i interfejsów API magazynu  |Za pomocą usługi artefaktu|
 |Obsługa narzędzia Tensorboard   |Nie|    Yes|
|Limity przydziałów na poziomie rodziny maszyn wirtualnych |Yes    |Tak (wcześniejsza pojemność jest przenoszona)|
 
Oprócz funkcji podanych w powyższej tabeli, w usłudze Azure Machine Learning Service istnieją funkcje, które tradycyjnie nie były obsługiwane w usłudze Batch AI.

|Cecha|Usługa Batch AI|Usługa Azure Machine Learning|
|-------|:-------:|:-------:|
|Przygotowywanie środowiska    |Nie |Tak (przygotowywanie i przekazywanie do usługi ACR za pomocą menedżera Conda)|
|Strojenie hiperparametryczne  |Nie|    Yes|
|Zarządzanie modelami   |Nie |Yes|
|Operacjonalizacja/wdrażanie| Nie  |Za pomocą usług AKS i ACI|
|Przygotowywanie danych   |Nie |Yes|
|Docelowe obiekty obliczeniowe    |Maszyny wirtualne platformy Azure  |Środowisko lokalne, usługa Batch AI (jako AmlCompute), usługa Databricks, usługa HDInsight|
|Zautomatyzowane uczenie maszynowe |Nie|    Yes|
|Potoki  |Nie |Yes|
|Wsadowe generowanie wyników  |Yes    |Yes|
|Obsługa portalu/interfejsu wiersza polecenia|    Yes |Yes|


## <a name="programming-interfaces"></a>Interfejsy programowania

Ta tabela przedstawia różne interfejsy programowania dostępne dla każdej usługi.
    
|Cecha|Usługa Batch AI|Usługa Azure Machine Learning|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (zarówno oparty na konfiguracji uruchamiania, jak i na narzędziu do szacowania dla typowych struktur)|
|Interfejs wiersza polecenia    |Yes    |Jeszcze nie|
|Azure Portal   |Yes    |Tak (z wyjątkiem przesyłania zadań)|
|Interfejs API REST   |Yes    |Tak, ale rozproszony na wiele mikrousług|


Uaktualnianie z usługi Batch AI (wersja zapoznawcza) do usługi GA'ed Azure Machine Learning zapewnia lepsze środowisko za pomocą pojęcia, które są łatwiejsze w obsłudze, takie jak aplikacjom i magazynów danych. Gwarantuje to również podleganie umowom SLA oraz pomoc techniczną na poziomie ogólnie dostępnych usług platformy Azure.

Usługa Azure Machine Learning, którą usługa udostępniono też nowe funkcje takie jak zautomatyzowane uczenia maszynowego do strojenia hiperparametrycznego i potoków uczenia Maszynowego, które są przydatne w najbardziej na dużą skalę obciążeń sztucznej Inteligencji. Możliwość wdrażania uczonego modelu bez przełączania do oddzielnej usługi pomaga Wykonaj pętlę do nauki o danych, od przygotowania danych (za pomocą zestawu SDK usługi Data Prep) aż do operacjonalizacji i monitorowania modelu.

<a name="migrate"></a>
## <a name="migrate"></a>Migrate (Migracja)

Dowiedz się, jak przeprowadzić migrację i jak możesz użyć mapy kodu do kodu w usłudze Azure Machine Learning w [migracja do usługi Azure Machine Learning](how-to-migrate.md) artykułu.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Usługa Batch AI to slated wycofywania na dzień 31 marca i już blokuje nowe subskrypcje zarejestrowanie korzystająca z usługi, chyba że jest na liście dozwolonych przez wywołanie wyjątku przez za pośrednictwem pomocy technicznej.  Jeśli masz jakiekolwiek pytania lub chcesz przesłać opinię na temat migracji do usługi Azure Machine Learning Service, wyślij wiadomość na adres [Azure Batch AI Training Preview](mailto:AzureBatchAITrainingPreview@service.microsoft.com).

Usługa Azure Machine Learning jest usługą ogólnie dostępną. Oznacza to, że towarzyszy jej zatwierdzona umowa SLA i różne plany pomocy technicznej do wyboru.

Cennik usługi przy użyciu infrastruktury platformy Azure za pomocą usługi Batch AI lub za pośrednictwem usługi Azure Machine Learning nie powinno się zmieniać, ponieważ opłaty będą naliczane wyłącznie cena za podstawowe zasoby obliczeniowe w obu przypadkach. Aby uzyskać więcej informacji, zobacz [kalkulator cen](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Wyświetl dostępność regionalną obu usług w witrynie [Azure Portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Kolejne kroki

+ Dowiedz się, [jak przeprowadzić migrację](how-to-migrate.md) oraz sposób mapowania kodu, możesz teraz używać do kodu w usłudze Azure Machine Learning.

+ Przeczytaj artykuł [Przegląd usługi Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Skonfiguruj docelowy obiekt obliczeniowy do trenowania modelu](../machine-learning/service/how-to-set-up-training-targets.md) za pomocą usługi Azure Machine Learning.

+ Zapoznaj się z [harmonogramem działania dla platformy Azure](https://azure.microsoft.com/updates/), aby dowiedzieć się o innych aktualizacjach usługi Azure.
