---
title: Pozyskiwanie danych i zrozumienie w zakresie zespołu danych dla celów naukowych
description: Cele, zadania i elementy dostarczane dla pozyskiwanie danych i zrozumienie etapu swoje projekty do nauki o danych
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720507"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Pozyskiwanie danych i zrozumienie etap procesu do nauki o danych zespołu

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzonych z pozyskiwanie danych i zrozumienie etap procesu do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Zrozumienie biznesowe**
   2. **Pozyskiwanie i zrozumienie danych**
   3. **Modelu**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Generuje jasny, wysokiej jakości zestaw danych, których relację do zmiennych docelowy jest zrozumiały. Znajdź zestaw danych w środowisku odpowiednie analizy, dzięki czemu można przystąpić do modelu.
* Tworzenie architektury rozwiązania z potoku danych, który odświeża i ocenia je regularnie.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją trzy główne zadania, które zostały omówione w tym etapie:

   * Pozyskiwanie **danych** w docelowym środowisku analitycznym.
   * **Eksploruj dane,** aby określić, czy jakość danych jest odpowiednia do udzielenia odpowiedzi na pytanie. 
   * **Skonfiguruj Potok danych** do oceny nowych lub regularnie odświeżanych danych.

### <a name="ingest-the-data"></a>Pozyskiwanie danych
Skonfiguruj proces, aby przenieść dane z lokalizacji źródłowej do lokalizacji docelowej, na którym jest ono wykonywane operacje analizy, takie jak szkolenia i prognozy. Aby uzyskać szczegółowe informacje techniczne i opcje przenoszenia danych przy użyciu różnych usług danych platformy Azure, zobacz artykuł [ładowanie danych do środowiska magazynu na potrzeby analizy](ingest-data.md). 

### <a name="explore-the-data"></a>Eksplorowanie danych
Przed uczyć się swoje modele, musisz dźwięku zrozumienia danych. Zestawy danych rzeczywistych często są hałas, brakuje wartości lub ma wiele innych niezgodności. Podsumowanie danych i wizualizacji umożliwia inspekcję jakość danych i podaj informacje potrzebne do przetwarzania danych, aby była gotowa do modelowania. Ten proces jest często iteracyjne.

PRZETWARZANIA TDSP zapewnia zautomatyzowane narzędzie, nazywane [pomysłem](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), które ułatwia wizualizowanie danych i przygotowywanie raportów podsumowujących dane. Zaleca się rozpoczynać IDEAR, aby eksplorować dane w celu zrozumienia danych początkowych interaktywnie przy użyciu bez kodowania. Następnie można napisać kod niestandardowy do eksploracji danych i wizualizacji. Aby uzyskać wskazówki dotyczące czyszczenia danych, zobacz [zadania przygotowujące dane do ulepszonej uczenia maszynowego](prepare-data.md).  

Po zakończeniu jakość danych oczyszczonych następnym krokiem jest lepiej zrozumieć wzorce, które są integralną częścią danych. Ta analiza danych pomaga w wyborze i opracowaniu odpowiedniego modelu predykcyjnego dla celu. Poszukaj dowodów dla jak dobrze połączonych danych jest w miejscu docelowym. Następnie określ, czy jest wystarczające dane, aby przejść do następnych kroków modelowania. Ponownie ten proces jest często iteracyjne. Może być konieczne znajdowanie nowych źródeł danych za pomocą danych bardziej precyzyjne lub istotniejsze rozszerzyć zestawu danych, początkowo zidentyfikowany w poprzednim etapie. 

### <a name="set-up-a-data-pipeline"></a>Konfigurowanie potoku danych
Oprócz początkowej pozyskiwania i czyszczenia danych zazwyczaj należy skonfigurować proces oceniać nowe dane lub odświeżania danych regularnie jako część procesu uczenia ciągły. Ocenianie może być wykonywane przy użyciu potoku danych lub przepływu pracy. Przykład [przenoszenia danych z wystąpienia lokalnego SQL Server, aby Azure SQL Database z artykułem Azure Data Factorym](move-sql-azure-adf.md) , zawiera przykładowy sposób konfigurowania potoku za pomocą [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Na tym etapie tworzenia architektury rozwiązania potoku danych. Możesz tworzyć potoku równolegle z kolejnego etapu projektu nauki o danych. W zależności od potrzeb firmy i ograniczeń istniejących systemów, do których to rozwiązanie jest zintegrowane, potok może być jedną z następujących opcji: 

   * Opartych na partiach
   * Przesyłanie strumieniowe lub w czasie rzeczywistym 
   * Hybrydowego 

## <a name="artifacts"></a>Artefakty
Poniżej wymieniono elementy dostarczane podczas tego etapu:

   * [Raport dotyczący jakości danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): ten raport zawiera podsumowania danych, relacje między poszczególnymi atrybutami i elementem docelowym, klasyfikację zmiennych i inne. Narzędzie [pomysł](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) udostępniane jako część przetwarzania TDSP może szybko wygenerować ten raport na dowolnym zestawie danych tabelarycznych, na przykład w pliku CSV lub w tabeli relacyjnej. 
   * **Architektura rozwiązania**: Architektura rozwiązania może być diagramem lub opisem potoku danych, który służy do uruchamiania oceniania lub prognoz dotyczących nowych danych po skompilowaniu modelu. Zawiera ona także potoku tak, aby ponowne szkolenie modelu na podstawie nowych danych. Zapisz dokument w katalogu [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) , gdy używasz szablonu struktury katalogów przetwarzania TDSP.
   * **Decyzja dotycząca punktów kontrolnych**: przed rozpoczęciem tworzenia inżynierów i konstruowania modelu można przeprowadzić ocenę projektu, aby określić, czy oczekiwana wartość jest wystarczająca, aby kontynuować jej działanie. Na przykład może być gotowy kontynuować, musisz zbierać więcej danych, lub Porzuć projektu, ponieważ dane nie istnieje odpowiedź na pytanie.

## <a name="next-steps"></a>Następne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Zrozumienie biznesowe](lifecycle-business-understanding.md)
   2. [Pozyskiwanie i zrozumienie danych](lifecycle-data.md)
   3. [Modelu](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oferujemy pełne instruktaże, które pokazują wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
