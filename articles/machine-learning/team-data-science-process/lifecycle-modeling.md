---
title: Modelowanie etap cyklu życia zespołowego danych dla celów naukowych
description: Cele, zadania i elementy dostarczane dla etapu modelowania projektów do nauki o danych
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
ms.openlocfilehash: c22c75b4fe900ecb96d016251c09e9ad6ec31f7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306228"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelowanie etap cyklu życia zespołowego danych dla celów naukowych

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzone z etapem modelowania procesów do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i opis**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP:

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ funkcji optymalne danych dla modelu uczenia maszynowego.
* Utwórz i model uczenia maszynowego, który prognozuje najdokładniej element docelowy.
* Utwórz model uczenia maszynowego, która jest odpowiednia w środowisku produkcyjnym.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją trzy główne zadania, które zostały omówione w tym etapie:

  * **Inżynieria funkcji**: Utwórz funkcje związane z danymi na podstawie danych pierwotnych w celu ułatwienia szkoleń modelowych.
  * **Szkolenie modelu**: Znajdź odpowiedzi na pytania najdokładniej przez porównanie ich metryki sukcesu modelu.
  * Określa, czy model jest **odpowiednich dla środowiska produkcyjnego.**

### <a name="feature-engineering"></a>Inżynieria funkcji
Inżynieria funkcji obejmuje dołączania, agregacji i przekształcanie pierwotnych zmienne, aby tworzyć funkcje używane do analizy. Jeśli mają wgląd w co umożliwiające obsługę modelu, należy zrozumieć jak powiązane są ze sobą funkcje i sposobu używania tych funkcji algorytmów uczenia maszynowego. 

Ten krok wymaga kombinację creative wiedzy na temat domeny i szczegółowe dane uzyskane w kroku eksploracji danych. Inżynieria funkcji jest działanie znajdowania i tym informacyjne zmiennych, ale w tym samym czasie próby Unikaj zbyt wielu zmiennych niepowiązanych. Zmienne i poprawy wyników; zmienne niepowiązanych wprowadzić niepotrzebny szum do modelu. Należy również generować nowe dane uzyskane podczas oceniania przez te funkcje. W rezultacie generowania tych funkcji tylko może zależeć od danych, która jest dostępna podczas oceniania. 

Wskazówki techniczne na temat funkcji podczas inżynierii udostępniać korzystać z różnych technologii danych platformy Azure, zobacz [Inżynieria funkcji w procesie nauki o danych](create-features.md). 

### <a name="model-training"></a>Szkolenie modelu
W zależności od typu zapytania, które próbujesz odpowiedzieć dostępnych wiele algorytmów modelowania. Aby uzyskać wskazówki na temat wybierania algorytmów, zobacz [jak wybór algorytmów dla usługi Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Chociaż w tym artykule korzysta z usługi Azure Machine Learning, wskazówek zawartych w nim jest przydatne dla wszystkich projektów uczenia maszynowego. 

Proces do trenowania modelu obejmuje następujące kroki: 

   * **Podział danych wejściowych** losowo do modelowania szkoleniowy zestaw danych i testowego zestawu danych.
   * **Twórz modele** przy użyciu zestawu danych szkoleniowych.
   * **Oceń** szkolenia i testowego zestawu danych. Używać szeregu konkurencyjnych algorytmów uczenia maszynowego oraz różne parametry dostrajania skojarzone (nazywane *parametrów*), przeznaczoną odpowiedzi na pytanie, będących przedmiotem zainteresowania z aktualnych danych.
   * **Określić, jakie rozwiązanie "Najważniejsze"** odpowiedź na pytanie, porównując metryki sukcesu między alternatywne metody.

> [!NOTE]
> **Należy unikać wycieku**: Jeśli dołączysz dane pochodzące spoza szkoleniowy zestaw danych umożliwiający modelu lub Algorytm uczenia maszynowego, jak tworzyć prognozy nierealistycznie dobre, może spowodować wycieku danych. Wyciek jest typową przyczyną, dlaczego dane, które analityków otrzymaj nerwowe w przypadku, gdy uzyskują predykcyjne wyniki wydawać się zbyt dobrze. Te zależności, może być trudny do wykrycia. W celu uniknięcia wycieku często wymaga iteracja między tworzenia zestawu analiz danych, tworzenie modelu i oceny dokładności wyników. 
> 
> 

Firma Microsoft zapewnia [zautomatyzowane, modelowania i raportowania narzędzie](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) za pomocą szablonu TDSP, który można uruchomić za pomocą wielu algorytmów i parametrów symulacji w celu utworzenia modelu odniesienia. Tworzy linię bazową modelowania raport podsumowujący wydajność każdego modelu wraz z kombinacją parametrów zmiennych znaczenie w tym. Ten proces jest również iteracyjna, jako jego dysku dalsze technicznego opracowywania funkcji. 

## <a name="artifacts"></a>Artefakty
Artefakty utworzone podczas tego etapu obejmują:

   * [Zestawy funkcji](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): Funkcje rozwinięte modelowania są opisane w **zestawy funkcji** części **definicji danych** raportu. Zawiera wskaźniki kod służący do generowania funkcji i opis jak funkcja został wygenerowany.
   * [Model raportu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): Dla każdego modelu, który zostanie podjęta próba standard, jest generowany na podstawie szablonu raportu, który zawiera szczegółowe informacje dotyczące każdego doświadczenia.
   * **CheckPoint decision**: Należy ocenić, czy model wykonuje wystarczająco dobrze wdrożenie go w systemie produkcyjnym. Niektóre kluczowe pytania są następujące:
     * Model odpowiedzi na pytanie, bez obaw wystarczające, biorąc pod uwagę dane testowe 
     * Należy spróbować żadnych alternatywnych metod? Należy zbierać dodatkowe dane, nie więcej technicznego opracowywania funkcji lub eksperymentować z innych algorytmów?

## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i opis](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowe przewodniki](walkthroughs.md) artykuł zawiera listę scenariuszy wraz z linkami i opisy miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Przykłady sposób wykonywania kroków w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [przetwarzania TDSP za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
