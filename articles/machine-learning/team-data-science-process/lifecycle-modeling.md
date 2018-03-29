---
title: Modelowanie etapie cyklu życia procesu nauki danych zespołu - Azure | Dokumentacja firmy Microsoft
description: Cele, zadań i elementów dostarczanych w etapie modelowania projektów analizy danych
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
ms.openlocfilehash: 8ef76e32e0a78f4ca492e6ac6d5755fdf19e734c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="modeling"></a>Modelowanie

W tym artykule omówiono cele, zadań i elementów dostarczanych skojarzone z etapu modelowania zespołu danych nauki procesu (TDSP). Ten proces obejmuje zalecane cykl służy do struktury projektów analizy danych. Cykl życia przedstawiono główne etapy, które projekty zazwyczaj wykonywane, często wielokrotnie powtarzane:

   1. **Opis biznesowa**
   2. **Uzyskiwanie danych i zrozumienie**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacji klienta**

W tym miejscu jest wizualną reprezentacją życia TDSP:

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ funkcji optymalne danych dla modelu uczenia maszynowego.
* Tworzenie informacji modelu uczenia maszynowego, który prognozuje najdokładniej obiektu docelowego.
* Tworzenie modelu uczenia maszynowego, który nadaje się do produkcji.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją trzy główne zadania zostały omówione w tym etapie:

  * **Inżynieria**: Tworzenie funkcji danych z danych pierwotnych w celu ułatwienia uczenia modelu.
  * **Model uczenia**: znaleźć modelu, który najdokładniej odpowiada pytanie, porównując ich metryki sukcesu.
  * Określa, czy model jest **nadające się do produkcji.**

### <a name="feature-engineering"></a>Inżynieria funkcji
Funkcja engineering obejmuje dołączania, agregacji i przekształcenie zmienne pierwotne można utworzyć funkcje używane do analizy. Chcąc wgląd w przyczyn nadmiernego modelu, należy zrozumieć, jak te funkcje są powiązane ze sobą i sposobu używania tych funkcji algorytmów uczenia maszynowego. 

Ten krok wymaga creative kombinację doświadczenia z domeny i szczegółowych danych uzyskane w kroku Eksploracja danych. Funkcja engineering jest działanie znajdowania i zmiennych informacyjny, ale w tym samym czasie unikać zbyt wiele zmiennych niepowiązanych. Zmienne informacyjny poprawy wyników; zmienne niepowiązanych powodować powstanie niepotrzebnego szumu do modelu. Należy również generowanie wszystkie nowe dane uzyskane podczas oceniania przez te funkcje. W związku z tym generowanie tych funkcji można tylko zależą od danych, która jest dostępna podczas oceniania. 

Wskazówki techniczne dotyczące funkcji podczas inżynierii upewnij używania różnych technologii danych Azure, zobacz [Inżynieria w procesie nauki danych](create-features.md). 

### <a name="model-training"></a>szkolenie modelu
W zależności od typu zapytania, który próbujesz odpowiedzi Brak dostępnych wiele algorytmów modelowania. Aby uzyskać wskazówki dotyczące wybierania algorytmów, zobacz [Wybieranie algorytmów w Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Chociaż ten artykuł korzysta z usługi Azure Machine Learning, zapewnia wskazówki przydaje się do wszystkich projektów, do uczenia maszynowego. 

Proces uczenia modelu obejmuje następujące kroki: 

   * **Podział danych wejściowych** losowo do modelowania szkoleniowy zestaw danych i testowego zestawu danych.
   * **Tworzenie modeli** przy użyciu szkoleniowy zestaw danych.
   * **Ocena** szkolenia i testowego zestawu danych. Szereg konkurencyjnych algorytmów uczenia maszynowego oraz różne parametry dostrajania skojarzony (nazywane *odchylenia parametru*) który adresowana odpowiedzi na pytanie płynących z bieżących danych.
   * **Określić rozwiązania "Najważniejsze"** udzielenia odpowiedzi na pytanie, porównując metryki sukcesu między alternatywne metody.

> [!NOTE]
> **Uniknąć wycieków**: może spowodować wycieku danych, jeśli zawierają dane pochodzące spoza umożliwia modelu lub Algorytm uczenia maszynowego w celu tworzenia prognoz nierealistycznie dobrej szkoleniowy zestaw danych. Wyciek jest typową przyczyną dlaczego dane, które służące uzyskać nerwowe, gdy otrzymują predykcyjnej wyników prawdopodobnie zbyt niskie. Te zależności, może być trudne do wykrycia. Aby uniknąć wycieków często wymaga iteracja między kompilowania zestawu danych analizy, tworzenie modelu i dokładność wyniki oceny. 
> 
> 

Firma Microsoft udostępnia [automatycznego modelowania i narzędzia raportowania](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) z TDSP, która może być przesyłany przez wiele algorytmów i parametr przeszukiwanie w celu utworzenia modelu linii bazowej. Tworzy linię bazową modelowania raport, który zawiera podsumowanie wydajności każdego modelu wraz z kombinacją parametrów zmiennej znaczenie w tym. Ten proces ma charakter iteracyjny również, jak można dysku dalsze engineering funkcji. 

## <a name="artifacts"></a>Artefakty
Artefakty utworzone na tym etapie obejmują:

   * [Zestawy funkcji](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): funkcje rozwinięte modelowania zostały opisane w **zestawy funkcji** sekcji **definicji danych** raportu. Zawiera ona wskaźniki kod służący do generowania funkcje oraz opis sposobu funkcję został wygenerowany.
   * [Model raportu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): dla każdego modelu, który zostanie podjęta próba, standard, jest generowany na podstawie szablonu raportu, który zawiera szczegóły dotyczące każdego doświadczenia.
   * **Punkt kontrolny decyzji**: oceny, czy model wykonuje wystarczająco również wdrożyć w systemie produkcji. Niektóre kluczowe pytania są:
     * Modelu odpowiedzi na pytanie, bez obaw wystarczające podanych danych testowych 
     * Czy Wypróbuj żadnych alternatywnych metod? Należy zbierać dodatkowe dane, czy więcej engineering funkcji lub wypróbować inne algorytmy?

## <a name="next-steps"></a>Kolejne kroki

Oto łącza do każdego kroku w cyklu TDSP:

   1. [Opis biznesowa](lifecycle-business-understanding.md)
   2. [Uzyskiwanie danych i zrozumienie](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacji klienta](lifecycle-acceptance.md)

Firma Microsoft udostępnia pełnej end-to-end wskazówki, które pokazują wszystkie kroki w procesie w określonych scenariuszach. [Wskazówki przykład](walkthroughs.md) artykuł zawiera listę scenariuszy łącza i opisy miniatur. Wskazówki dotyczące ilustrują sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 

Przykłady tego, jak wykonać kroki opisane w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [TDSP za pomocą usługi Azure Machine Learning](http://aka.ms/datascienceprocess). 
