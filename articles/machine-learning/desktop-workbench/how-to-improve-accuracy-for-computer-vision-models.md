---
title: Większej dokładności modele wizji i klasyfikacja obrazów w usłudze Azure Machine Learning
description: Dowiedz się, jak zwiększyć dokładność Klasyfikacja obrazów przetwarzania komputera, wykrywanie obiektów i modeli podobieństwa obrazów przy użyciu pakietu Learning maszyny platformy Azure do przetwarzania obrazów.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6d6c540cd8711ae89784cdc797ca56d312522a83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989315"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Zwiększyć dokładność modele przetwarzania obrazów

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Za pomocą **Azure Machine Learning pakiet dla przetwarzania obrazów**, można je tworzyć i wdrażać Klasyfikacja obrazów obiektu wykrywania i obraz modeli podobieństwa. Dowiedz się więcej na temat tego pakietu i sposobie jej instalowania.

W tym artykule dowiesz się, jak dostosować te modele w celu zwiększenia ich dokładności. 

## <a name="accuracy-of-image-classification-models"></a>Dokładność modeli klasyfikacji obrazów

Pakiet przetwarzania komputera jest wyświetlana zapewnienie dobre wyniki dla różnych zestawów danych. Jak jest tak w przypadku większości machine learning projektów, wprowadzenie najlepsze możliwe wyniki dla nowego zestawu danych wymaga jednak szczegółowego dostosowywania parametrów zachować ostrożność, a także ocena różne decyzje. Poniższe sekcje zawierają wskazówki dotyczące sposobu zwiększenia dokładności dla danego zestawu danych, oznacza to, jakie parametry są najbardziej obiecujących, aby zoptymalizować najpierw, jakie wartości tych parametrów, jeden prób i jakich pułapek, aby uniknąć.

Ogólnie rzecz biorąc szkolenie modeli uczenia głębokiego jest powiązana z kompromis między czasem szkolenia i dokładności modelu. Pakiet przetwarzania komputera ma parametrów domyślnych wstępnie ustalony (Zobacz pierwszy wiersz w tabeli poniżej), które koncentrują się na szybkość szybkie szkolenia przy jednoczesnym zazwyczaj modeli wysokiej dokładności. Często można zwiększyć dokładność ta za pomocą, na przykład, wyższą rozdzielczość obrazu lub bardziej modeli, jednak kosztem zwiększenie wypoczynkowych współczynnik 10 x lub więcej.

Zalecane jest, że możesz najpierw pracować parametrów domyślnych, wytrenuj model, wyniów, popraw adnotacje prawdziwość podstaw zgodnie z potrzebami i dopiero wtedy spróbuj parametry, które spowolnić wypoczynkowych (zobacz tabelę poniżej wartości parametrów sugerowane). Zrozumienie tych parametrów, podczas gdy jest to technicznie nie jest konieczny jest jednak zalecane.


### <a name="best-practices-and-tips"></a>Najważniejsze wskazówki i porady

* Jakość danych: zestawy szkoleniowe i testowe powinna być wysokiej jakości. Oznacza to, że obrazy są przypisane poprawnie, obrazy niejednoznaczne usunięte, (na przykład gdy jest niejasne dla ludzkiego oka Jeśli na ilustracji przedstawiono piłkę tenisa lub tarta) i atrybuty wzajemnie się wykluczają (czyli każdy obraz należy dokładnie jeden atrybut).

* Przed rafinowania DNN, klasyfikatora SVM powinni przejść szkolenie przy użyciu wstępnie przeszkolonych i stały systemu DNN featurized. To jest obsługiwane w pakiecie przetwarzania komputera i nie wymaga długich do nauczenia się, ponieważ DNN, sama nie jest modyfikowany. Nawet w tym proste często uzyskuje dobre dokładności i dlatego liną bazową silne. Następnym krokiem jest następnie dostosować DNN, który należy udostępnić większej dokładności.

* Jeśli obiekt zainteresowań jest niewielka w obrazie, metody klasyfikacji obrazów są znane nie będą działać poprawnie. W takich przypadkach należy wziąć pod uwagę przy użyciu metody wykrywania obiektu, takie jak komputera przetwarzania pakietu szybciej R-CNN opartych na Tensorflow.

* Dane szkoleniowe więcej tym lepiej. Reguła z-mówi jeden powinien mieć co najmniej 100 przykłady dla każdej klasy, czyli 100 obrazów "dog", 100 obrazów "cat" itp. Uczenie modelu przy użyciu mniejszej liczby obrazów jest możliwe, ale może nie dawać dobre wyniki.

* Uczone obrazy muszą znajdować się lokalnie na komputerze z procesorem GPU i znajdować się na dysku SSD (a nie HDD). W przeciwnym razie opóźnienie odczytu obrazu może znacznie zmniejszyć szkolenia szybkość (zwiększając nawet 100 razy).


### <a name="parameters-to-optimize"></a>Parametrów w celu optymalizacji

Znajdowanie optymalne wartości tych parametrów jest ważna, a następnie można często zwiększyć dokładność znacznie:
* Nauka szybkość (`lr_per_mb`): prawdopodobnie najważniejszych parametru, aby uzyskać odpowiednie. Jeśli dokładności na szkolenie ustawiona po DNN refinement ponad OK. 5%, najprawdopodobniej tempo uczenia jest zbyt duża, lub liczba epok szkolenia zbyt niska. Szczególnie w przypadku małych zestawów danych DNN zwykle nadmiernie mieści się na dane szkoleniowe, jednak w praktyce doprowadzi to do dobrych modeli na testowej Ustaw. Zazwyczaj używamy 15 epok gdzie tempo uczenia początkowej zmniejsza się dwukrotnie; w niektórych przypadkach szkolenia przy użyciu więcej epok może poprawić wydajność.

* Wprowadź rozdzielczość (`image_dims`): domyślna rozdzielczość obrazu to 224 x 224 pikseli. Przy użyciu wyższa rozdzielczość obrazu programu, na przykład, 500 x 500 pikseli lub pikselach 1000 x 1000 może znacznie poprawić dokładność, ale spowalnia DNN refinement. Pakiet przetwarzania komputera oczekuje rozdzielczości wejściowej jako krotki (kanałów koloru, szerokość obrazu, wysokość obrazu), na przykład, (3, 224, 224), gdzie liczba kanałów koloru musi zostać ustawione na 3 (pasma czerwony, zielony, niebieski).

* Modelowanie architektury (`base_model_name`): spróbuj użyć bardziej sieci, takich jak siecią ResNet-34 lub siecią ResNet-50 zamiast domyślnego modelu siecią ResNet-18. Model siecią Resnet-50 nie jest tylko bardziej, ale jego dane wyjściowe przedostatni warstwy jest rozmiar 2048 wartości zmiennoprzecinkowe (vs. 512 wartości zmiennoprzecinkowe modeli siecią ResNet-18 i siecią ResNet-34). Ta zwiększona wymiary mogą być szczególnie korzystne, gdy utrzymywanie DNN rozwiązane i zamiast tego szkolenia klasyfikatora SVM.

* Rozmiar minibatch (`mb_size`): wysoki minibatch rozmiary doprowadzi do krótszy czas szkolenia jednak kosztem zwiększone zużycie pamięci DNN. W związku z tym podczas wybierania bardziej modeli (na przykład siecią ResNet-50 porównaniu z siecią ResNet-18) i/lub wyższa rozdzielczość obrazu (500\*500 pikseli i 224\*224 pikseli), zwykle ma jedną zmniejszyć rozmiar minibatch, aby uniknąć błędów braku pamięci. Podczas zmiany rozmiaru minibatch, często tempo uczenia musi również można dostosować, jak podano w poniższej tabeli.
* Częstotliwość odrzucania (`dropout_rate`) i L2 regularizer (`l2_reg_weight`): dopasowywania nadmiernego DNN można zmniejszyć przy użyciu kursu przerwany najmniej 0,5 (wartość domyślna to 0,5 w pakiecie przetwarzania komputera) i przez odpowiednie zwiększenie wagi regularizer (wartość domyślna to 0,0005 w wizualizacji komputerowej Pakiet). Pamiętaj jednak, szczególnie w przypadku małych zestawów danych DNN nadmiernego jest trudny i często nie można uniknąć.


### <a name="parameter-definitions"></a>Parametr definicji

- **Nauka współczynnik**: krok rozmiaru używanego podczas nauki spadku gradientu. Jeśli zestaw jest zbyt niska, a następnie modelu będzie wiele epok do szkolenia, jeśli zestaw jest zbyt duże, a następnie modelu nie będzie zbieżne dobrym rozwiązaniem. Zwykle harmonogramu jest używany, gdy tempo uczenia zmniejsza się po określonej liczbie epok. Przykład E.For szkoleniowe stawka harmonogramu `[0.05]*7 + [0.005]*7 + [0.0005]` odpowiada za pomocą tempo uczenia początkowej 0,05 dla pierwszych siedem epok, następuje 10 x tempo uczenia mniejsze 0,005 dla innego epok siedmiu i na koniec dostrajanie modelu dla pojedynczego epoki x 100 zmniejszona tempo uczenia z 0,0005.

- **Rozmiar minibatch**: procesorów GPU może przetwarzać wiele obrazów równolegle w celu przyspieszenia obliczeń. Obrazy te równoległego przetwarzania są również nazywane minibatch. Im wyższa minibatch rozmiar szkolenia szybsze będzie, jednak kosztem zwiększone zużycie pamięci DNN.

### <a name="recommended-parameter-values"></a>Wartości parametrów zalecane

Poniższa tabela zawiera zestawy różnych parametrów, które były wyświetlane w celu utworzenia modeli wysokiej dokładności na szeroką gamę zadań klasyfikacji obrazów. Optymalne parametry są zależne od konkretnego zestawu danych i dokładnie GPU, używane, dlatego należy rozpatrywać tabeli tylko wskazówką. Po wykonaniu tych parametrów, należy wziąć pod uwagę także rozdzielczości obrazów więcej niż 500 x 500 pikseli lub bardziej modeli, takie jak siecią Resnet-101 lub siecią Resnet-152.

Pierwszy wiersz w tabeli odnosi się do domyślnych parametrów, które są ustawiane w pakiecie przetwarzania komputera. Wszystkie pozostałe wiersze dłużej może jednak uczyć (wskazane w pierwszej kolumnie) na korzyści wynikające z większą dokładność (patrz drugiej kolumny, średnia dokładność trzy wewnętrznego zestawów danych). Na przykład parametry w ostatnim wierszu zająć dłużej szkolenie, x 5 – 15 jednak spowodowało większą dokładność (uśrednionej) na trzech zestawów testów wewnętrznych z 82.6% 92.8%.

Lepiej modeli i wyższej rozdzielczości wejściowe zajmują więcej pamięci DNN i dlatego rozmiar minibatch musi być ograniczona za pomocą modelu zwiększenia złożoności w celu uniknięcia poza z pamięci błędów. Jak widać w poniższej tabeli, korzystne jest, zmniejsz tempo uczenia przez dwukrotne zawsze wtedy, gdy minibatch zmniejsza rozmiar przez tego samego mnożnika. Uzyskaj obniżone rozmiar minibatch może być konieczne dalsze na procesorach GPU znajdujących się za pomocą mniejszej ilości pamięci.

| Szkolenie w czasie (oszacowana) | Przykład dokładności | Rozmiar minibatch (*mb_size*) | Nauka szybkość (*lr_per_mb*) | Rozpoznawanie obrazów (*image_dims*) | Architektura systemu DNN (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (odwołanie w) | 82.6% | 32 | [0,05] \*7 + [0,005]\*7 + [0,0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 90.2% | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | wynosi 87.5% | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5 – 15 x        | 92.8% |  8 | [0,01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące pakietu Azure Machine Learning dla przetwarzania obrazów:
+ Zapoznaj się z dokumentacją odwołania

+ Dowiedz się więcej o [inne pakiety języka Python dla usługi Azure Machine Learning](reference-python-package-overview.md)