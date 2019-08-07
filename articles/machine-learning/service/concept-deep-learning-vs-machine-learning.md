---
title: Wprowadzenie do uczenia głębokiego i uczenia maszynowego
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o relacji między głęboką nauką a uczeniem maszynowym i sposobem ich dopasowania do kategorii sztucznej analizy. W tym artykule opisano, jak uczenie głębokie rozwiązuje scenariusze, takie jak wykrywanie oszustw, rozpoznawanie głosu i twarzy, tonacji Analytics i prognozowanie szeregów czasowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 4b66d015e8a182e305a36dacaa2e082288a3e19e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840544"
---
# <a name="introduction-to-deep-learning-vs-machine-learning"></a>Wprowadzenie do uczenia głębokiego i uczenia maszynowego

Ten artykuł ułatwia zrozumienie relacji między uczeniem szczegółowym a uczeniem maszynowym. Dowiesz się, jak te dwie koncepcje porównują i jak mieszczą się w szerszej kategorii sztucznej analizy. W tym artykule opisano, jak można zastosować uczenie głębokie w rzeczywistych scenariuszach, takich jak wykrywanie oszustw, rozpoznawanie głosu i twarzy, analiza tonacji i prognozowanie szeregów czasowych.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>Jak uczenie głębokie, uczenie maszynowe i AI są ze sobą powiązane?

![Diagram relacji: AI a uczenie maszynowe a uczenie głębokie](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Aby zrozumieć różnicę między głęboką nauką a uczeniem maszynowym a usługą Machine Learning a programem AI, Dowiedz się więcej na temat każdego z tych pól analizy:

- **Uczenie głębokie** to podzestaw uczenia maszynowego oparty na sztucznych sieciach neuronowych, które umożliwiają komputerowi uczenie się samego siebie. W takim przypadku _proces uczenia_ jest nazywany _głębokie_, ponieważ struktura sztucznych sieci neuronowych składa się z wielu warstw danych wejściowych, wyjściowych i ukrytych. Każda warstwa zawiera jednostki, które przekształcają dane wejściowe w informacje, które następne warstwy mogą wykorzystać do wykonania pewnego zadania predykcyjnego. Dzięki tej strukturze komputer może poznać swoje własne przetwarzanie danych.

- **Uczenie maszynowe** to podzbiór sztucznej analizy zawierającej techniki (takie jak uczenie głębokie), które umożliwiają maszynom ulepszanie zadań przy użyciu środowiska. W takim przypadku _proces uczenia_ jest oparty na następujących krokach:

    1. Podawanie przez siebie algorytmu danych przez udostępnienie większej ilości informacji (na przykład przez przeprowadzenie wyodrębniania funkcji).
    2. Użyj tych danych do uczenia modelu.
    3. Przetestuj i Wdróż model.
    4. Użyj wdrożonego modelu do wykonania pewnego zautomatyzowanego zadania predykcyjnego.

- **Sztuczna inteligencja (AI)** to technika, która umożliwia komputerom naśladowanie analizy ludzkiej. Obejmuje to uczenie maszynowe. Ważne jest zrozumienie relacji między _AI a usługą Machine Learning a_uczeniem głębokim: Uczenie maszynowe to sposób na osiągnięcie sztucznej analizy, co oznacza użycie technik uczenia maszynowego i uczenia głębokiego, które umożliwiają tworzenie systemów komputerowych i aplikacji, które mogą wykonywać zadania często związane z analizą ludzką, taką jak wizualne percepcja, rozpoznawanie mowy, podejmowanie decyzji i tłumaczenie między językami.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>Porównanie głębokiej uczenia i uczenia maszynowego

Teraz, gdy masz Omówienie uczenia maszynowego i uczenia głębokiego, Porównaj te dwie techniki. W przypadku uczenia maszynowego algorytm musi zostać poinformował, jak wprowadzić dokładne prognozowanie, dostarczając więcej informacji. w przypadku uczenia głębokiego algorytm jest w stanie dowiedzieć się, że przez własne przetwarzanie danych, dzięki strukturze sieci sztucznej neuronowych.

W poniższej tabeli porównano dwa techniki bardziej szczegółowo:

| |Wszystkie Uczenie maszynowe |Tylko uczenie głębokie|
|---|---|---|
|  **Liczba punktów danych** | Może korzystać z małych ilości danych udostępnianych przez użytkowników | Wymaga dużej ilości danych szkoleniowych, aby wykonywać zwięzłe wnioski |
|  **Zależności sprzętowe** | Może współpracować z maszynami z niską końcówką. Nie wymaga to dużej ilości mocy obliczeniowej | Zależy od maszyn wysokiej klasy. Jest to z założenia wiele operacji mnożenia macierzy. Te operacje mogą być efektywnie zoptymalizowane przy użyciu procesora GPU |
|  **Proces cechowania** | Wymaga, aby funkcje były dokładnie identyfikowane i tworzone przez użytkowników | Poznaje funkcje wysokiego poziomu z danych i tworzy nowe funkcje |
|  **Podejście szkoleniowe** | Dzieli zadania na małe fragmenty, a następnie łączy otrzymane wyniki w jeden wniosek | Rozwiązuje problem na całej trasie |
|  **Czas wykonywania** | Porównawcze przeszkolenie trwa krócej, od kilku sekund do kilku godzin | Trwa zbyt długo, aby szkolić, ponieważ istnieje wiele parametrów w algorytmie uczenia głębokiego |
|  **Dane wyjściowe** | Dane wyjściowe są zwykle wartościami liczbowymi, takimi jak Ocena lub Klasyfikacja | Dane wyjściowe mogą być dowolne z wyniku, tekstu, elementu lub dźwięku |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Przypadki użycia głębokiego uczenia: Jakie problemy rozwiązuje?

Ze względu na sztuczną strukturę sieci neuronowych, głębokie uczenie programu Excel na identyfikowaniu wzorców w danych bez struktury, takich jak obrazy, dźwięk, wideo i tekst. Z tego powodu błyskawicznie przekształcamy wiele branż, takich jak opieka zdrowotna, energia, FinTech, transport i inne, aby remyśleć tradycyjne procesy biznesowe. Niektóre z najpopularniejszych aplikacji do uczenia głębokiego są opisane w poniższych akapitach.

### <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych

Jednym z zastosowań sieci głębokiej uczenia jest rozpoznawanie jednostek nazwanych, które jest sposobem wyodrębnienia danych z nieoznaczonych nieprawidłowymi danymi, takich jak osoby, miejsca, firmy lub rzeczy. Te informacje mogą być następnie przechowywane w schemacie strukturalnym, aby utworzyć listę adresów lub służyć jako wzorzec dla aparatu weryfikacji tożsamości.

### <a name="object-detection"></a>Wykrywanie obiektów

Uczenie głębokie zostało zastosowane w wielu przypadkach użycia wykrywania obiektów. Wykrywanie obiektów jest w rzeczywistości procesem dwuczęściowym: klasyfikacją obrazów, a następnie lokalizacją obrazu. Klasyfikacja obrazu Określa, jakie obiekty w obrazie są podobne do samochodu lub osoby, podczas gdy lokalizacja obrazu zapewnia konkretną lokalizację tych obiektów. Wykrywanie obiektów jest już używane w branży gier, detalicznych, turystycznych i samopracujących samochodów.

### <a name="image-caption-generation"></a>Generowanie podpisu obrazu

Podobnie jak w przypadku zadania rozpoznawania obrazu, oznacza to, że w przypadku danego obrazu system musi wygenerować podpis opisujący zawartość obrazu. Po wykryciu obiektów w fotografiach i wygenerowaniu etykiet dla tych obiektów można zobaczyć, że następnym krokiem jest przekształcenie tych etykiet w spójny opis zdania. Ogólnie rzecz biorąc, systemy obejmują korzystanie z bardzo dużych splotowychych sieci neuronowych do wykrywania obiektów w fotografiach, a następnie do rebieżącej sieci neuronowych (RNN), aby przekształcić etykiety w spójne zdanie.

### <a name="machine-translation"></a>Tłumaczenie maszynowe

Tłumaczenie maszynowe przyjmuje słowa, frazy lub zdania z jednego języka i automatycznie tłumaczy je na inny język. Automatyczne tłumaczenie maszynowe było wykonywane przez długi czas, ale uczenie głębokie to osiągnięcie najlepszych wyników w dwóch określonych obszarach: automatyczne tłumaczenie tekstu (i zamiana mowy na tekst) i automatyczne tłumaczenie obrazów. Dzięki prawidłowej transformacji danych sieć głęboka jest w stanie zrozumieć sygnały tekstowe, dźwiękowe i wizualne. Tłumaczenie maszynowe może służyć do identyfikowania fragmentów dźwięku w większych plikach audio i transkrypcja wymawianego wyrazu lub obrazu jako tekstu.

### <a name="text-analytics"></a>Analiza tekstu

Jednym z ważnych zadań, które może wykonać uczenie głębokie, jest odnajdywanie elektroniczne. Firmy używają głębokiej analizy tekstu opartej na uczeniu do wykrywania handlu niejawnego i zapewnienia zgodności z przepisami rządowych. Fundusze żywopłotów używają analizy tekstu, aby przejść do szczegółowych repozytoriów dokumentów w celu uzyskania wglądu w przyszłą wydajność inwestycji i tonacji rynku. Przypadek użycia dla analizy tekstu opartej na uczeniu głębokiego pozwala analizować duże ilości danych tekstowych w celu przeprowadzania analiz lub do agregacji.

## <a name="what-are-artificial-neural-networks"></a>Co to są sztuczne sieci neuronowych?

Sztuczna sieć neuronowych to model uczenia głębokiego inspirowany sposobem biologicznej sieci neuronowych w informacjach o procesie mózgów ludzkich. Istnieje wiele typów sztucznych sieci neuronowych, które są obecnie używane. Najpopularniejsze typologies sieci sztucznej neuronowych zostały omówione poniżej.

### <a name="feedforward-neural-network"></a>Sieć feedforward neuronowych

Sieć feedforward neuronowych to najbardziej podstawowy typ sztucznej sieci neuronowychowej, w którym informacje są przesyłane tylko jeden kierunek z warstwy wejściowej do warstwy wyjściowej. Feedforward sieci neuronowych przekształcają dane wejściowe, umieszczając je w szeregu ukrytych warstw. Każda warstwa składa się z zestawu neurons, gdzie każda warstwa jest w pełni podłączona do wszystkich neurons w warstwie. Na koniec istnieje Ostatnia w pełni łączona warstwa — warstwa wyjściowa, która przedstawia wygenerowane prognozy.

### <a name="recurrent-neural-network"></a>Rebieżąca sieć neuronowych

Przechodzące sieci neuronowych są bardziej szeroko używanymi rodzajami sieci sztucznej neuronowych, które działają na zasadzie zapisywania danych wyjściowych warstwy i powracają do warstwy wejściowej, aby pomóc w przewidywaniu wyniku warstwy. Te sieci neuronowych mają większe możliwości uczenia się i są szeroko wykorzystywane do bardziej złożonych zadań, takich jak uczenie pisma ręcznego lub rozpoznawania języka.

### <a name="convolutional-neural-networks"></a>Splotowych neuronowych sieci

Sieć splotowych neuronowych jest szczególnie skutecznym typem sieci sztucznej neuronowych, która przedstawia unikatową architekturę. Najpierw warstwy są zorganizowane w trzy wymiary: Szerokość, Wysokość i głębokość. Dodatkowo neurons w jednej warstwie nie łączą się ze wszystkimi neurons w następnej warstwie, ale tylko w niewielkim regionie. Na koniec ostateczne dane wyjściowe zostaną zredukowane do jednego wektora oceny prawdopodobieństwa, zorganizowane wzdłuż wymiaru głębokości. Te sieci neuronowych są używane w takich obszarach jak rozpoznawanie i Klasyfikacja obrazu.

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach pokazano, jak korzystać z technologii uczenia głębokiego w [usłudze Azure Machine Learning](/azure/machine-learning/service/):

- [Klasyfikowanie odręcznych cyfr przy użyciu modelu TensorFlow](how-to-train-tensorflow.md)
- [Klasyfikowanie odpisanych cyfrowo za pomocą TensorFlow szacowania i Keras](how-to-train-keras.md)
- [Klasyfikowanie obrazów przy użyciu modelu Pytorch](how-to-train-pytorch.md)
- [Klasyfikowanie odręcznych cyfr przy użyciu modelu łańcucha](how-to-train-pytorch.md)
