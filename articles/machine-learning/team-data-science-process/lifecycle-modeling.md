---
title: Modelowanie etap cyklu życia zespołowego danych dla celów naukowych
description: Cele, zadania i elementy dostarczane dla etapu modelowania projektów do nauki o danych
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720473"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelowanie etap cyklu życia zespołowego danych dla celów naukowych

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzone z etapem modelowania procesów do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Zrozumienie biznesowe**
   2. **Pozyskiwanie i zrozumienie danych**
   3. **Modelu**
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

  * **Inżynieria funkcji**: Tworzenie funkcji danych z danych pierwotnych w celu ułatwienia szkolenia modeli.
  * **Uczenie modeli**: Znajdź model, który odbierze najbardziej dokładne wyniki, porównując ich metryki sukcesu.
  * Ustal, czy model jest **odpowiedni dla środowiska produkcyjnego.**

### <a name="feature-engineering"></a>Inżynieria funkcji
Inżynieria funkcji obejmuje dołączania, agregacji i przekształcanie pierwotnych zmienne, aby tworzyć funkcje używane do analizy. Jeśli mają wgląd w co umożliwiające obsługę modelu, należy zrozumieć jak powiązane są ze sobą funkcje i sposobu używania tych funkcji algorytmów uczenia maszynowego. 

Ten krok wymaga kombinację creative wiedzy na temat domeny i szczegółowe dane uzyskane w kroku eksploracji danych. Inżynieria funkcji jest działanie znajdowania i tym informacyjne zmiennych, ale w tym samym czasie próby Unikaj zbyt wielu zmiennych niepowiązanych. Zmienne i poprawy wyników; zmienne niepowiązanych wprowadzić niepotrzebny szum do modelu. Należy również generować nowe dane uzyskane podczas oceniania przez te funkcje. W rezultacie generowania tych funkcji tylko może zależeć od danych, która jest dostępna podczas oceniania. 

Aby uzyskać wskazówki techniczne dotyczące inżynierii funkcjonalnej w przypadku korzystania z różnych technologii danych platformy Azure, zobacz [Inżynieria funkcji w procesie analizy danych](create-features.md). 

### <a name="model-training"></a>Trenowanie modelu
W zależności od typu zapytania, które próbujesz odpowiedzieć dostępnych wiele algorytmów modelowania. Aby uzyskać wskazówki dotyczące wybierania algorytmów, zobacz [jak wybrać algorytmy dla Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Chociaż w tym artykule korzysta z usługi Azure Machine Learning, wskazówek zawartych w nim jest przydatne dla wszystkich projektów uczenia maszynowego. 

Proces do trenowania modelu obejmuje następujące kroki: 

   * **Dziel losowo dane wejściowe** do modelowania na zestaw danych szkoleniowych i zestaw danych testowych.
   * **Kompiluj modele** przy użyciu zestawu danych szkoleniowych.
   * **Oceń** szkolenia i zestaw danych testowych. Używaj szeregu konkurencyjnych algorytmów uczenia maszynowego wraz z różnymi, skojarzonymi parametrami strojenia (znanym jako *odchylenia parametrów*), które są skierowane do odpowiedzi na pytania związane z bieżącymi danymi.
   * **Określ najlepsze rozwiązanie** , aby odpowiedzieć na pytanie, porównując metryki sukcesu między metodami alternatywnymi.

> [!NOTE]
> **Unikaj wycieku**: można spowodować wyciek danych, jeśli dołączysz dane spoza zestawu danych szkoleniowych, które umożliwiają modelowi lub algorytmowi uczenia maszynowego wykonywanie nierealistycznie dobrych prognoz. Wyciek jest typową przyczyną, dlaczego dane, które analityków otrzymaj nerwowe w przypadku, gdy uzyskują predykcyjne wyniki wydawać się zbyt dobrze. Te zależności, może być trudny do wykrycia. W celu uniknięcia wycieku często wymaga iteracja między tworzenia zestawu analiz danych, tworzenie modelu i oceny dokładności wyników. 
> 
> 

Udostępniamy [zautomatyzowane narzędzie do modelowania i raportowania](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) z przetwarzania TDSP, które jest możliwe do uruchomienia przez wiele algorytmów i odchylenia parametrów w celu utworzenia modelu linii bazowej. Tworzy linię bazową modelowania raport podsumowujący wydajność każdego modelu wraz z kombinacją parametrów zmiennych znaczenie w tym. Ten proces jest również iteracyjna, jako jego dysku dalsze technicznego opracowywania funkcji. 

## <a name="artifacts"></a>Artefakty
Artefakty utworzone podczas tego etapu obejmują:

   * [Zestawy funkcji](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): funkcje opracowane dla modelowania są opisane w sekcji **zestawy funkcji** w raporcie **definicji danych** . Zawiera wskaźniki kod służący do generowania funkcji i opis jak funkcja został wygenerowany.
   * [Raport dotyczący modelu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): dla każdego z modeli, który został wypróbowany, standardowy, oparty na szablonach raport, który zawiera szczegółowe informacje dotyczące każdego eksperymentu.
   * **Decyzja dotycząca punktów kontrolnych**: Oceń, czy model jest wystarczająco wydajny dla środowiska produkcyjnego. Niektóre kluczowe pytania są następujące:
     * Model odpowiedzi na pytanie, bez obaw wystarczające, biorąc pod uwagę dane testowe 
     * Należy spróbować żadnych alternatywnych metod? Należy zbierać dodatkowe dane, nie więcej technicznego opracowywania funkcji lub eksperymentować z innych algorytmów?

## <a name="next-steps"></a>Następne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Zrozumienie biznesowe](lifecycle-business-understanding.md)
   2. [Pozyskiwanie i zrozumienie danych](lifecycle-data.md)
   3. [Modelu](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
