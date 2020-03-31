---
title: Etap modelowania cyklu życia procesu nauki o danych zespołu
description: Cele, zadania i elementy dostarczane na etapie modelowania projektów nauki o danych
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
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720473"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Etap modelowania cyklu życia procesu nauki o danych zespołu

W tym artykule opisano cele, zadania i elementy dostarczane skojarzone z etapem modelowania procesu nauki o danych zespołu (TDSP). Ten proces zapewnia zalecany cykl życia, którego można użyć do struktury projektów nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj wykonują, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **wdrażania**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia TDSP:

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ optymalne funkcje danych dla modelu uczenia maszynowego.
* Utwórz pouczający model uczenia maszynowego, który najdokładniej przewiduje obiekt docelowy.
* Utwórz model uczenia maszynowego, który jest odpowiedni dla środowiska produkcyjnego.

## <a name="how-to-do-it"></a>Jak to zrobić
Na tym etapie zajęto się trzema głównymi zadaniami:

  * **Inżynieria funkcji:** Tworzenie funkcji danych z nieprzetworzonych danych w celu ułatwienia szkolenia modelu.
  * **Szkolenie modelu:** znajdź model, który odpowiada na pytanie najdokładniej, porównując ich wskaźniki sukcesu.
  * Określ, czy model **jest odpowiedni do produkcji.**

### <a name="feature-engineering"></a>Inżynieria funkcji
Inżynieria funkcji obejmuje włączenie, agregację i transformację nieprzetworzonych zmiennych w celu utworzenia funkcji używanych w analizie. Jeśli chcesz uzyskać wgląd w to, co napędza model, musisz zrozumieć, jak funkcje odnoszą się do siebie nawzajem i jak algorytmy uczenia maszynowego mają korzystać z tych funkcji. 

Ten krok wymaga kreatywnego połączenia wiedzy specjalistycznej w dziedzinie i szczegółowych informacji uzyskanych z etapu eksploracji danych. Inżynieria funkcji jest aktem równoważenia znajdowania zmiennych informacyjnych i dołączania, ale jednocześnie stara się unikać zbyt wielu niepowiązanych zmiennych. Zmienne informacyjne poprawiają wynik; niepowiązanych zmiennych wprowadzają niepotrzebny szum do modelu. Należy również wygenerować te funkcje dla wszelkich nowych danych uzyskanych podczas oceniania. W rezultacie generowanie tych funkcji może zależeć tylko od danych, które są dostępne w momencie oceniania. 

Aby uzyskać wskazówki techniczne dotyczące inżynierii funkcji podczas korzystania z różnych technologii danych platformy Azure, zobacz [Projektowanie funkcji w procesie nauki o danych.](create-features.md) 

### <a name="model-training"></a>Trenowanie modelu
W zależności od typu pytania, na które próbujesz odpowiedzieć, dostępnych jest wiele algorytmów modelowania. Aby uzyskać wskazówki dotyczące wybierania algorytmów, zobacz [Jak wybrać algorytmy dla usługi Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Mimo że w tym artykule używa usługi Azure Machine Learning, wskazówki, które zapewnia jest przydatne dla wszystkich projektów uczenia maszynowego. 

Proces szkolenia modelu obejmuje następujące kroki: 

   * **Podziel dane wejściowe** losowo do modelowania na zestaw danych szkoleniowych i zestaw danych testowych.
   * **Tworzenie modeli** przy użyciu zestawu danych szkoleniowych.
   * **Oceń** zestaw danych szkoleniowych i testowych. Użyj serii konkurencyjnych algorytmów uczenia maszynowego wraz z różnymi skojarzonymi parametrami strojenia (znanymi jako *wyciągnięcie po parametrach),* które są ukierunkowane na odpowiadanie na pytanie interesujące z bieżącymi danymi.
   * **Określ "najlepsze" rozwiązanie,** aby odpowiedzieć na pytanie, porównując metryki sukcesu między metodami alternatywnymi.

> [!NOTE]
> **Uniknąć wycieków:** Możesz spowodować wyciek danych, jeśli uwzględnisz dane spoza zestawu danych szkoleniowych, który umożliwia modelowi lub algorytmowi uczenia maszynowego dokonywanie nierealistycznie dobrych prognoz. Wyciek jest częstym powodem, dla którego analitycy danych denerwują się, gdy otrzymują wyniki predykcyjne, które wydają się zbyt piękne, aby mogły być prawdziwe. Te zależności mogą być trudne do wykrycia. Aby uniknąć wycieków często wymaga iteracji między tworzeniem zestawu danych analizy, tworzeniem modelu i oceną dokładności wyników. 
> 
> 

Zapewniamy [zautomatyzowane narzędzie do modelowania i raportowania](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) z TDSP, który jest w stanie uruchomić za pomocą wielu algorytmów i parametrów wymiata do tworzenia modelu bazowego. Tworzy również raport modelowania linii bazowej, który podsumowuje wydajność każdego modelu i kombinacji parametrów, w tym zmienne znaczenie. Proces ten jest również iteracyjny, ponieważ może napędzać dalsze funkcje inżynierii. 

## <a name="artifacts"></a>Artefakty
Artefakty produkowane na tym etapie obejmują:

   * [Zestawy funkcji](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): Funkcje opracowane dla modelowania są opisane w sekcji **Zestawy funkcji** raportu **Definicja danych.** Zawiera wskaźniki do kodu do generowania funkcji i opis, jak funkcja została wygenerowana.
   * [Raport modelu:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)Dla każdego wypróbowanego modelu jest produkowany standardowy raport oparty na szablonie, który zawiera szczegółowe informacje na temat każdego eksperymentu.
   * **Decyzja punktu kontrolnego:** Oceń, czy model działa wystarczająco do produkcji. Oto kilka kluczowych pytań, które należy zadać:
     * Czy model odpowiada na pytanie z wystarczającą pewnością, biorąc pod uwagę dane testowe? 
     * Należy spróbować alternatywnych podejść? Czy należy zbierać dodatkowe dane, wykonywać więcej funkcji inżynierii lub eksperymentować z innymi algorytmami?

## <a name="next-steps"></a>Następne kroki

Oto łącza do każdego kroku w cyklu życia TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [wdrażania](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Zapewniamy pełne instrukcje end-to-end, które pokazują wszystkie kroki w procesie dla określonych scenariuszy. [Przykładowy](walkthroughs.md) artykuł zawiera listę scenariuszy z łączami i opisami miniatur. W instruktażu pokazano, jak połączyć chmurę, narzędzia lokalne i usługi w przepływie pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w usługach TDSP korzystających z usługi Azure Machine Learning Studio, zobacz [Korzystanie z usługi TDSP z usługą Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 
