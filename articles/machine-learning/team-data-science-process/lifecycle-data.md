---
title: Uzyskiwanie danych i zrozumienia etapie cyklu życia procesu nauki danych zespołu - Azure | Dokumentacja firmy Microsoft
description: Cele, zadania i materiały dostarczane do gromadzenia danych i opis etap nauki danych projektów
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 028992561b1c2481dc72af3c4edcc16f65f25105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="data-acquisition-and-understanding"></a>Pozyskiwanie danych i ich analiza

W tym artykule omówiono cele, zadań i elementów dostarczanych skojarzone z gromadzenia danych i zrozumienie etap zespołu danych nauki procesu (TDSP). Ten proces obejmuje zalecane cykl służy do struktury projektów analizy danych. Cykl życia przedstawiono główne etapy, które projekty zazwyczaj wykonywane, często wielokrotnie powtarzane:

   1. **Opis biznesowa**
   2. **Uzyskiwanie danych i zrozumienie**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacji klienta**

W tym miejscu jest wizualną reprezentacją życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Utworzyć czystego, wysokiej jakości zestawu danych, których relacji do zmiennych docelowych jest rozpoznawany. Znajdź w zestawie danych w środowisku odpowiednie analytics, możesz przystąpić do modelu.
* Tworzenie architektury rozwiązania odświeża i regularnie wyników danych potoku danych.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją trzy główne zadania zostały omówione w tym etapie:

   * **Pozyskiwanie danych** do analityczne środowiska docelowego.
   * **Eksploracja danych** do ustalenia, czy jakości danych jest odpowiednie do odpowiedzi na pytanie. 
   * **Konfigurowanie potoku danych** zdobycie new lub regularnie odświeżania danych.

### <a name="ingest-the-data"></a>Pozyskiwanie danych
Konfigurowanie procesu przenoszenia danych z lokalizacji źródłowej do lokalizacji docelowej realizującym analytics operacje, na przykład szkolenia i prognoz. Aby uzyskać szczegółowe informacje techniczne i opcje dotyczące sposobu przenoszenia danych z różnych usług danych Azure, zobacz [ładowanie danych do magazynu środowisk analizy](ingest-data.md). 

### <a name="explore-the-data"></a>Eksplorowanie danych
Przed uczenia modeli, musisz zrozumienia dźwięku danych. Zestawy danych rzeczywistych są często zakłócenia, brakuje wartości lub ma hosta inne niezgodności. Podsumowanie danych i wizualizacji służy do inspekcji jakości danych oraz informacje potrzebne do przetwarzania danych, aby była gotowa do modelowania. Ten proces jest często iteracji.

TDSP zapewnia automatyczne narzędzie o nazwie [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), aby ułatwić wizualizacji danych i przygotować raporty podsumowania danych. Zaleca się rozpoczynać IDEAR, aby eksplorować dane w celu zrozumienia początkowe dane z interaktywnie nie kodowania. Następnie można napisać kod niestandardowy Eksploracja danych i wizualizacji. Aby uzyskać wskazówki dotyczące czyszczenia danych, zobacz [uczenia maszynowego zadania, aby przygotować dane dla rozszerzonego](prepare-data.md).  

Po zakończeniu jakości danych oczyszczony, następnym krokiem jest lepiej zrozumieć wzorce, które są związane z danych. Dzięki temu można wybrać i utworzyć odpowiedni model predykcyjny docelowego. Wyszukuj dowody dla jak dobrze połączonych danych ma element docelowy. Następnie określ, czy jest wystarczającą ilość danych, aby przejść do przodu kolejne kroki modelowania. Ponownie ten proces jest często iteracji. Może być konieczne uzyskanie nowych źródeł danych z bardziej trafna lub więcej danych można rozszerzyć początkowo zidentyfikowany w poprzedniej etap w zestawie danych. 

### <a name="set-up-a-data-pipeline"></a>Konfigurowanie potoku danych
Oprócz początkowej wprowadzanie i czyszczenia danych zazwyczaj należy skonfigurować proces oceniać nowe dane lub odświeżania danych regularnie jako część procesu trwającą learning. W tym celu konfigurowania potoku danych lub przepływ pracy. [Przenoszenia danych z lokalnego wystąpienia programu SQL Server z bazą danych SQL Azure z fabryką danych Azure](move-sql-azure-adf.md) artykuł zawiera przykład sposobu konfigurowania potoku z [fabryki danych Azure](https://azure.microsoft.com/services/data-factory/). 

Na tym etapie należy opracować architektury rozwiązania potoku danych. Można utworzyć potoku równolegle z kolejnego etapu projektu analizy danych. W zależności od potrzeb biznesowych i ograniczenia istniejących systemów do których to rozwiązanie jest integrowana potoku może być jedną z następujących czynności: 

   * Na podstawie partii
   * Przesyłanie strumieniowe lub w czasie rzeczywistym 
   * Hybrydowego 

## <a name="artifacts"></a>Artefakty
Poniżej przedstawiono elementy dostarczane na tym etapie:

   * [Raport jakości danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): Ten raport zawiera dane podsumowania, relacje między każdego atrybutu i obiekt docelowy, zmiennej klasyfikacji i inne. [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) narzędzie jako część TDSP szybko mógł wygenerować ten raport o żadnych tabelarycznym zestaw danych, takich jak plik CSV lub relacyjne tabeli. 
   * **Architektura rozwiązania**: Architektura rozwiązania może być diagramu lub opis danych w potoku które używany do uruchamiania oceniania lub prognoz na nowe dane po skonstruowaniu modelu. Zawiera ona także potoku, aby ponownie ucz modelu na podstawie nowych danych. Przechowywanie dokumentów w [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) katalogu przy użyciu szablonu struktury katalogu TDSP.
   * **Punkt kontrolny decyzji**: przed rozpoczęciem pełnej funkcji inżynieryjne i konstruowania modelu można obliczyć ponownie projekt, aby ustalić, czy wartość oczekiwana jest wystarczające, aby kontynuować, pursing go. Na przykład może być gotowe kontynuować, należy zbierać więcej danych, lub Porzuć projektu, ponieważ danych nie istnieje udzielenia odpowiedzi na pytanie.

## <a name="next-steps"></a>Kolejne kroki

Oto łącza do każdego kroku w cyklu TDSP:

   1. [Opis biznesowa](lifecycle-business-understanding.md)
   2. [Uzyskiwanie danych i zrozumienie](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacji klienta](lifecycle-acceptance.md)

Firma Microsoft udostępnia pełnej end-to-end wskazówki, które pokazują wszystkie kroki w procesie w określonych scenariuszach. [Wskazówki przykład](walkthroughs.md) artykuł zawiera listę scenariuszy łącza i opisy miniatur. Wskazówki dotyczące ilustrują sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 

Przykłady tego, jak wykonać kroki opisane w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [TDSP za pomocą usługi Azure Machine Learning](http://aka.ms/datascienceprocess).
