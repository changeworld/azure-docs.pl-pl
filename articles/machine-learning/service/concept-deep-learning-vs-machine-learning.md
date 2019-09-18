---
title: Uczenie głębokie a uczenie maszynowe
titleSuffix: Azure Machine Learning
description: Poznaj uczenie głębokie i uczenie maszynowe oraz sposób, w jaki obydwa koncepcje odnoszą się do sztucznej analizy. Uczenie głębokie można stosować w scenariuszach takich jak wykrywanie oszustw, rozpoznawanie głosu i twarzy, analiza tonacji i prognozowanie szeregów czasowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: cff31916f837141ae54f3c14dd125be6a92a5008
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035504"
---
# <a name="deep-learning-vs-machine-learning"></a>Uczenie głębokie a uczenie maszynowe

Ten artykuł pomaga porównać głębokie uczenie i uczenie maszynowe. Dowiesz się, jak te dwie koncepcje porównują i jak mieszczą się w szerszej kategorii sztucznej analizy. W tym artykule opisano również, jak można zastosować uczenie głębokie w rzeczywistych scenariuszach, takich jak wykrywanie oszustw, rozpoznawanie głosu i twarzy, analiza tonacji i prognozowanie szeregów czasowych.

## <a name="deep-learning-machine-learning-and-ai"></a>Uczenie głębokie, uczenie maszynowe i AI

![Diagram relacji: AI a uczenie maszynowe a uczenie głębokie](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Rozważ następujące definicje, aby zrozumieć głębokie uczenie i uczenie maszynowe a PLIKÓW

- **Uczenie głębokie** to podzestaw uczenia maszynowego, który jest oparty na sztucznych sieciach neuronowych. _Proces uczenia_ jest _głęboki_ , ponieważ struktura sztucznych sieci neuronowych składa się z wielu warstw danych wejściowych, wyjściowych i ukrytych. Każda warstwa zawiera jednostki, które przekształcają dane wejściowe w informacje, które następne warstwy mogą używać dla pewnego zadania predykcyjnego. Dzięki tej strukturze komputer może poznać swoje własne przetwarzanie danych.

- **Uczenie maszynowe** to podzbiór sztucznej analizy wykorzystującej techniki (takie jak uczenie głębokie), które umożliwiają maszynom używanie środowiska w celu usprawnienia zadań. _Proces uczenia_ jest oparty na następujących krokach:

   1. Źródło danych do algorytmu. (W tym kroku można podać dodatkowe informacje do modelu, na przykład przez wykonanie wyodrębniania funkcji).
   1. Użyj tych danych do uczenia modelu.
   1. Przetestuj i Wdróż model.
   1. Korzystaj ze wdrożonego modelu, aby wykonać zautomatyzowane zadanie predykcyjne. (Innymi słowy, wywołaj i użyj wdrożonego modelu, aby otrzymać przewidywania zwracane przez model).

- **Sztuczna inteligencja (AI)** to technika, która umożliwia komputerom naśladowanie analizy ludzkiej. Obejmuje to uczenie maszynowe. 
 
Ważne jest zrozumienie relacji między AI, uczeniem maszynowym i uczeniem głębokiego. Uczenie maszynowe to sposób na osiągnięcie sztucznej analizy. Korzystając z technik uczenia maszynowego i uczenia głębokiego, można tworzyć systemy komputerowe i aplikacje, które wykonują zadania, które są często powiązane z inteligencją ludzką. Te zadania obejmują postrzeganie wizualne, rozpoznawanie mowy, podejmowanie decyzji i tłumaczenie języka.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Techniki uczenia głębokiego a uczenie maszynowe 

Teraz, gdy masz Omówienie uczenia maszynowego i uczenia głębokiego, porównujemy dwie techniki. W usłudze Machine Learning algorytm musi być powiadamiany o tym, jak wprowadzić dokładne przewidywania, wykorzystując więcej informacji (na przykład przez przeprowadzenie wyodrębniania funkcji). W ramach uczenia głębokiego algorytm może dowiedzieć się, jak przeprowadzić dokładne prognozowanie za pomocą własnego przetwarzania danych, dzięki strukturze sieci sztucznej neuronowych.

Poniższa tabela zawiera porównanie dwóch technik w bardziej szczegółowy sposób:

| |Wszystkie Uczenie maszynowe |Tylko uczenie głębokie|
|---|---|---|
|  **Liczba punktów danych** | Może korzystać z małych ilości danych udostępnianych przez użytkowników. | Wymaga dużej ilości danych szkoleniowych, aby wykonywać zwięzłe wnioski. |
|  **Zależności sprzętowe** | Może współpracować z maszynami z niską końcówką. Nie wymaga to dużej ilości mocy obliczeniowej. | Zależy od maszyn wysokiej klasy. Jest to z założenia wiele operacji mnożenia macierzy. Procesor GPU może efektywnie zoptymalizować te operacje. |
|  **Proces cechowania** | Wymaga, aby funkcje były dokładnie identyfikowane i tworzone przez użytkowników. | Poznaj funkcje wysokiego poziomu z danych i tworzenie nowych funkcji. |
|  **Podejście szkoleniowe** | Dzieli zadania na małe fragmenty, a następnie łączy otrzymane wyniki w jeden wniosek. | Rozwiązuje problem na całej trasie. |
|  **Czas wykonywania** | Trwa znacznie nieco więcej czasu na wyuczenie, w zakresie od kilku sekund do kilku godzin. | Trwa zbyt długo, aby uczenie się, ponieważ algorytm uczenia głębokiego obejmuje wiele parametrów. |
|  **Dane wyjściowe** | Dane wyjściowe są zwykle wartościami liczbowymi, takimi jak wynik lub Klasyfikacja. | Wynikiem może być tekst, wynik, element lub dźwięk. |

## <a name="deep-learning-use-cases"></a>Przypadki użycia głębokiego uczenia

Ze względu na sztuczną strukturę sieci neuronowych, głębokie uczenie programu Excel na identyfikowaniu wzorców w danych bez struktury, takich jak obrazy, dźwięk, wideo i tekst. Z tego powodu Szczegółowa nauka szybko przekształca wiele branż, takich jak opieka zdrowotna, energia, finanse i transport. Te branże teraz uznają tradycyjne procesy biznesowe. 

Niektóre z najpopularniejszych aplikacji do uczenia głębokiego są opisane w poniższych akapitach.

### <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych

Jednym z nich jest rozpoznawanie jednostek nazwanych, które jest sposobem wyodrębnienia pewnych typów informacji z danych bez struktury i nieoznaczonych etykietami. Te informacje mogą być osobami, miejscami, firmami lub elementami. Informacje mogą być następnie przechowywane w schemacie strukturalnym, aby utworzyć listę adresów lub służyć jako wzorzec dla aparatu weryfikacji tożsamości.

### <a name="object-detection"></a>Wykrywanie obiektów

Uczenie głębokie zostało zastosowane w wielu przypadkach użycia wykrywania obiektów. Wykrywanie obiektów obejmuje dwie części: klasyfikację obrazów, a następnie lokalizację obrazu. _Klasyfikacja_ obrazu identyfikuje obiekty obrazu, takie jak samochody lub osoby. Lokalizacja _obrazu zapewnia_ określoną lokalizację tych obiektów. 

Wykrywanie obiektów jest już używane w branżach, takich jak gry, handel detaliczny, turystyka i samoobsługowe samochody.

### <a name="image-caption-generation"></a>Generowanie podpisu obrazu

Podobnie jak w przypadku rozpoznawania obrazów, w przypadku obrazków obrazów dla danego obrazu system musi wygenerować podpis opisujący zawartość obrazu. Gdy można wykryć i oznaczyć obiekty na fotografiach, następnym krokiem jest przekształcenie tych etykiet w opisowe, spójne zdania. Ogólnie rzecz biorąc, systemy podpisów obrazów używają bardzo dużych splotowychych sieci neuronowych do wykrywania obiektów w fotografiach, a następnie używania reaktualnej sieci neuronowych (RNN) do przekształcania etykiet na spójne zdania.

### <a name="machine-translation"></a>Tłumaczenie maszynowe

Tłumaczenie maszynowe przyjmuje słowa, frazy lub zdania z jednego języka i automatycznie tłumaczy je na inny język. Automatyczne tłumaczenie maszynowe było wykonywane przez długi czas, ale uczenie głębokie realizuje imponujące wyniki w dwóch określonych obszarach: automatyczne tłumaczenie tekstu (oraz Tłumaczenie mowy na tekst) i automatyczne tłumaczenie obrazów. 

Dzięki prawidłowej transformacji danych w sieci głębokiej można zrozumieć sygnały tekstowe, dźwiękowe i wizualne. Tłumaczenie maszynowe może służyć do identyfikowania fragmentów dźwięku w większych plikach audio i transkrypcja wymawianego wyrazu lub obrazu jako tekstu.

### <a name="text-analytics"></a>Analiza tekstu

Jednym z ważnych zadań uczenia głębokiego jest odnajdywanie elektroniczne. Firmy używają analizy tekstu, która opiera się na głębokiej uczeniu do wykrywania handlu niejawnego i zgodności z przepisami obowiązującymi w instytucji rządowych. Fundusze żywopłotów używają analizy tekstu, aby przejść do szczegółowych repozytoriów dokumentów w celu uzyskania wglądu w przyszłą wydajność inwestycji i tonacji rynku. Przypadek użycia dla analizy tekstu oparty na głębokiej uczeniu jest przeznaczony do analizowania ogromnych ilości danych tekstowych i wykonywania analiz lub do agregacji.

## <a name="artificial-neural-networks"></a>Sztuczne sieci neuronowych

Sztuczne sieci neuronowych są tworzone przez warstwy połączonych węzłów. Modele uczenia głębokiego używają sieci neuronowych, które mają dużą liczbę warstw. 

W poniższych sekcjach opisano najpopularniejsze Popularne sieci sztucznej neuronowych Network typologies.

### <a name="feedforward-neural-network"></a>Sieć feedforward neuronowych

Sieć feedforward neuronowych jest najbardziej podstawowym typem sztucznej sieci neuronowych. W sieci feedforward informacje są przesyłane tylko jeden kierunek z warstwy wejściowej do warstwy wyjściowej. Feedforward sieci neuronowych przekształcają dane wejściowe, umieszczając je w szeregu ukrytych warstw. Każda warstwa składa się z zestawu neurons, a każda warstwa jest w pełni podłączona do wszystkich neurons w warstwie. Ostatnia w pełni łączona warstwa (warstwa wyjściowa) reprezentuje wygenerowane prognozy.

### <a name="recurrent-neural-network"></a>Rebieżąca sieć neuronowych

Recurrent neuronowych Networks to szeroko wykorzystywana sieć sztuczna neuronowych. Te sieci zapisują dane wyjściowe warstwy i odsyłają ją z powrotem do warstwy wejściowej w celu ułatwienia przewidywania wyniku warstwy. Neuronowych sieci mają doskonałe możliwości uczenia się. Są one szeroko używane do wykonywania złożonych zadań, takich jak uczenie pisma ręcznego i rozpoznawanie języka.

### <a name="convolutional-neural-networks"></a>Splotowych neuronowych sieci

Sieć splotowych neuronowych to szczególnie skuteczna sztuczna sieć neuronowych, która przedstawia unikatową architekturę. Warstwy są zorganizowane w trzy wymiary: Szerokość, Wysokość i głębokość. Neurons w jednej warstwie nie łączy się ze wszystkimi neurons w następnej warstwie, ale tylko do małego regionu neurons warstwy. Końcowe dane wyjściowe są skracane do jednego wektora wyników prawdopodobieństwa, zorganizowane wzdłuż wymiaru głębokości. 

Splotowych neuronowych sieci są używane w takich obszarach jak rozpoznawanie i Klasyfikacja obrazu.

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach pokazano, jak korzystać z technologii uczenia głębokiego w [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri):

- [Klasyfikowanie odręcznych cyfr przy użyciu modelu TensorFlow](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [Klasyfikowanie odręcznych cyfr przy użyciu TensorFlow szacowania i Keras](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [Klasyfikowanie obrazów przy użyciu modelu Pytorch](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [Klasyfikowanie odręcznych cyfr przy użyciu modelu łańcucha](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)
