---
title: Pozyskiwanie danych i zrozumienie w zakresie zespołu danych dla celów naukowych
description: Cele, zadania i elementy dostarczane dla pozyskiwanie danych i zrozumienie etapu swoje projekty do nauki o danych
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e29f36897dd52fcb09456768a799209a385d74fe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303528"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Pozyskiwanie danych i zrozumienie etap procesu do nauki o danych zespołu

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzonych z pozyskiwanie danych i zrozumienie etap procesu do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i opis**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Generuje jasny, wysokiej jakości zestaw danych, których relację do zmiennych docelowy jest zrozumiały. Znajdź zestaw danych w środowisku odpowiednie analizy, dzięki czemu można przystąpić do modelu.
* Tworzenie architektury rozwiązania z potoku danych, który odświeża i ocenia je regularnie.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją trzy główne zadania, które zostały omówione w tym etapie:

   * **Pozyskiwania danych** do analitycznych środowiska docelowego.
   * **Eksplorowanie danych** do ustalenia, czy dobrej jakości danych jest odpowiednie do odpowiedzi na pytanie. 
   * **Skonfiguruj potok danych** zdobycie nowych lub regularnie odświeżane dane.

### <a name="ingest-the-data"></a>Pozyskiwanie danych
Skonfiguruj proces, aby przenieść dane z lokalizacji źródłowej do lokalizacji docelowej, na którym jest ono wykonywane operacje analizy, takie jak szkolenia i prognozy. Aby uzyskać szczegółowe informacje techniczne i opcje dotyczące sposobu przenoszenia danych z różnymi usługami danych platformy Azure, zobacz [ładowanie danych w środowiskach magazynowania do analizy](ingest-data.md). 

### <a name="explore-the-data"></a>Eksplorowanie danych
Przed uczyć się swoje modele, musisz dźwięku zrozumienia danych. Zestawy danych rzeczywistych często są hałas, brakuje wartości lub ma wiele innych niezgodności. Podsumowanie danych i wizualizacji umożliwia inspekcję jakość danych i podaj informacje potrzebne do przetwarzania danych, aby była gotowa do modelowania. Ten proces jest często iteracyjne.

Przetwarzanie TDSP zapewnia zautomatyzowane narzędzia o nazwie [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), aby zwizualizować dane i przygotowania danych, raporty podsumowujące. Zaleca się rozpoczynać IDEAR, aby eksplorować dane w celu zrozumienia danych początkowych interaktywnie przy użyciu bez kodowania. Następnie można napisać kod niestandardowy do eksploracji danych i wizualizacji. Aby uzyskać wskazówki dotyczące czyszczenia danych, zobacz [zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego](prepare-data.md).  

Po zakończeniu jakość danych oczyszczonych następnym krokiem jest lepiej zrozumieć wzorce, które są integralną częścią danych. Dzięki temu można wybrać i rozwijać odpowiedni model predykcyjny Twojego miejsca docelowego. Poszukaj dowodów dla jak dobrze połączonych danych jest w miejscu docelowym. Następnie określ, czy jest wystarczające dane, aby przejść do następnych kroków modelowania. Ponownie ten proces jest często iteracyjne. Może być konieczne znajdowanie nowych źródeł danych za pomocą danych bardziej precyzyjne lub istotniejsze rozszerzyć zestawu danych, początkowo zidentyfikowany w poprzednim etapie. 

### <a name="set-up-a-data-pipeline"></a>Konfigurowanie potoku danych
Oprócz początkowej pozyskiwania i czyszczenia danych zazwyczaj należy skonfigurować proces oceniać nowe dane lub odświeżania danych regularnie jako część procesu uczenia ciągły. Możesz to zrobić, konfigurując potoku danych lub przepływu pracy. [Przenieść dane z lokalnego wystąpienia programu SQL Server do usługi Azure SQL Database za pomocą usługi Azure Data Factory](move-sql-azure-adf.md) artykuł zawiera przykład sposobu konfigurowania potoku za pomocą [usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Na tym etapie tworzenia architektury rozwiązania potoku danych. Możesz tworzyć potoku równolegle z kolejnego etapu projektu nauki o danych. W zależności od potrzeb biznesowych i ograniczenia istniejących systemów do których to rozwiązanie jest zintegrowany potok może być jedną z następujących czynności: 

   * Opartych na partiach
   * Przesyłanie strumieniowe lub w czasie rzeczywistym 
   * Hybrydowego 

## <a name="artifacts"></a>Artefakty
Poniżej wymieniono elementy dostarczane podczas tego etapu:

   * [Raport jakość danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Ten raport zawiera podsumowania danych, relacje między każdy atrybut i wartości docelowej, zmiennych klasyfikacji i nie tylko. [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) narzędzie jako część przetwarzania TDSP można szybko wygenerować ten raport na dowolnym tabelarycznych zestawie danych, np. plik CSV lub tabeli relacyjnej. 
   * **Architektura rozwiązania**: Architektura rozwiązania może być diagramu lub opis potoku danych, który można używać do uruchamiania oceny lub prognozy na nowe dane, po skonstruowaniu modelu. Zawiera ona także potoku tak, aby ponowne szkolenie modelu na podstawie nowych danych. Store dokumentu w [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) katalogu przy użyciu szablonu strukturę katalogu przetwarzania TDSP.
   * **CheckPoint decision**: Przed rozpoczęciem, technicznego opracowywania funkcji pełnego i konstruowania modelu, można ponownie oceń projekt, aby ustalić, czy wartość oczekiwana jest wystarczające, aby kontynuować, należy go wykonać. Na przykład może być gotowy kontynuować, musisz zbierać więcej danych, lub Porzuć projektu, ponieważ dane nie istnieje odpowiedź na pytanie.

## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i opis](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowe przewodniki](walkthroughs.md) artykuł zawiera listę scenariuszy wraz z linkami i opisy miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Przykłady sposób wykonywania kroków w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [przetwarzania TDSP za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
