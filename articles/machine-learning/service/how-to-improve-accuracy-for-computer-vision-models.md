---
title: Większej dokładności wizji & klasyfikacji modelach komputerów w usłudze Azure Machine Learning
description: Dowiedz się, jak poprawić dokładność klasyfikacji obrazu wizji komputera, wykrywania obiektu i modele podobieństwa obrazu przy użyciu pakietu Learning Azure maszyny do widzenia komputera.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: e134e1e544c51d6756d5021fef8c049fe7d8afb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788774"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Zwiększyć dokładność modeli wizji komputera

Z **Azure maszyny Learning pakiet dla komputera wizji**, możesz skompilować i wdrożyć obraz klasyfikacji, obiekt wykrywania i obrazu podobieństwa modeli. Dowiedz się więcej na temat tego pakietu i sposobu jego instalacji.

W tym artykule należy Dowiedz się, jak dostosować te modele, aby zwiększyć jego dokładność. 

## <a name="accuracy-of-image-classification-models"></a>Dokładność modele klasyfikacji obrazu

Pakiet wizji komputera jest wyświetlana umożliwiają dobrych wyników dla różnych zestawów danych. Jak ma wartość true dla większości machine learning projektów, pobierania najlepsze możliwe wyniki dla nowy zestaw danych wymaga jednak dokładne parametru dostrajania, a także decyzje dotyczące projektu innej obliczania. Poniższe sekcje zawierają wskazówki na temat sposobu zwiększenia dokładności dla danego zestawu danych, oznacza to, jakie parametry są najbardziej obietnicy w celu zoptymalizowania najpierw, jakie wartości dla parametrów jedną należy spróbować i jakie problemów, aby uniknąć.

Ogólnie rzecz biorąc szkolenia modele uczenia głębokie zawiera zależność między czasu szkolenia zależności dokładności modelu. Pakiet wizji komputer ma parametrów domyślnych wstępnie ustawionymi (Zobacz pierwszy wiersz w tabeli poniżej), które skupić się na szybkość szybkiego szkolenia przy jednoczesnym zwykle Wysoka dokładność modeli. Często można zwiększyć dokładność ta za pomocą, na przykład wyższej rozdzielczości obrazu lub lepszy modeli, jednak kosztem zwiększenie czasu szkoleń przez współczynnik 10 x lub więcej.

Zaleca się, że należy najpierw pracować z parametrów domyślnych, uczenie modelu przy użyciu Sprawdź wyniki, popraw podstaw prawdy adnotacjami zgodnie z potrzebami i tylko spróbuj parametrów, które spowolnić czasu szkoleń (zobacz tabelę poniżej wartości parametrów sugerowane). Opis tych parametrów, gdy jest to technicznie nie jest konieczne jest jednak zalecane.


### <a name="best-practices-and-tips"></a>Najlepsze rozwiązania i wskazówki

* Jakość danych: zestawy szkoleniowe i testu powinien być wysokiej jakości. Oznacza to, że obrazy mają adnotacje poprawnie, obrazy niejednoznaczne usunięta (na przykład gdy jest jasne dla człowieka oka Jeśli obraz pokazuje piłka tenisa lub cytrynowa) i wykluczają się wzajemnie atrybuty (to znaczy każdego obrazu należy dokładnie jeden atrybut).

* Przed uściślenie DNN, powinien uczony klasyfikatora SVM jako featurizer przy użyciu DNN wstępnie przeszkolone i stałe. To jest obsługiwane w pakiecie wizji komputera i nie wymaga długi do uczenia, ponieważ DNN, sama nie jest modyfikowany. Nawet w tym proste często uzyskuje dobrej dokładności i dlatego reprezentuje silne linii bazowej. Następnym krokiem jest następnie Dostosuj DNN, który powinien zapewnić większej dokładności.

* Jeśli obiekt odsetek jest niewielka w obrazie, może nie działać poprawnie są określane podejścia klasyfikacji obrazu. W takiej sytuacji należy wziąć pod uwagę przy użyciu obiektu metody wykrywania, takie jak komputer wizji pakietu szybciej R-CNN opartych na Tensorflow.

* Więcej danych szkoleniowych tym lepiej. Jako reguła elementu thumb jeden powinien mieć co najmniej 100 przykłady dla każdej klasy, czyli 100 obrazy dla "kot", 100 obrazów "kot" itp. Uczenie modelu z mniej obrazów jest możliwe, ale może nie dawać dobrych wyników.

* Obrazy szkolenia muszą znajdować się lokalnie na komputerze z procesora GPU i znajdować się na dysku SSD (nie HDD). W przeciwnym razie opóźnienie odczytu obrazu znacząco można ograniczyć szybkość szkolenia (przez nawet współczynnik 100 x).


### <a name="parameters-to-optimize"></a>Parametry w celu optymalizacji

Znajdowanie optymalne wartości dla parametrów ważne jest i często znacznie ulepszyć dokładność:
* Learning szybkość (`lr_per_mb`): parametr raczej najważniejszych uzyskać prawo. Jeśli dokładności szkolenia po uściślenia DNN wynosi powyżej ~ 5%, najprawdopodobniej tempo uczenia jest zbyt duża, a następnie lub liczbę epok szkolenia zbyt niska. Szczególnie w przypadku małych zestawów danych DNN zwykle nadmierne mieści się na dane szkoleniowe, jednak w praktyce to dobry modele na testowej Ustaw. Zwykle używamy 15 epok gdzie tempo uczenia początkowej zostanie zmniejszona dwukrotnie; w niektórych przypadkach przy użyciu więcej epok szkolenie może zwiększyć wydajność.

* Dane wejściowe rozpoznawania (`image_dims`): domyślna rozdzielczość obrazu to 224 x 224 pikseli. Przy użyciu wyższej rozdzielczości obrazu programu, na przykład, 500 x 500 pikseli lub pikselach 1000 x 1000 może znacznie poprawić dokładność, ale spowalnia uściślenia DNN. Pakiet wizji komputer oczekuje wejściowych rozdzielczości krotka (kanały kolor, szerokość obrazu, wysokość obrazu), na przykład (3 224, 224), gdzie liczba kanałów koloru ma ustawioną 3 (pasma czerwony-zielony-niebieski).

* Modelowanie architektury (`base_model_name`): Użyj DNNs głębiej, takich jak ResNet 34 lub ResNet 50 zamiast domyślnego ResNet 18 modelu. Model Resnet 50 nie jest tylko głębiej, ale jego dane wyjściowe przedostatni warstwy jest rozmiar 2048 elementów przestawnych (vs. 512 elementów przestawnych modeli ResNet 18 i ResNet 34). Ta zwiększona wymiary mogą być szczególnie przydatne, gdy utrzymywanie DNN stałej i zamiast tego szkolenia SVM klasyfikatora.

* Rozmiar minibatch (`mb_size`): wysoki minibatch rozmiary doprowadzi do skrócić czas szkolenia jednak kosztem zwiększone użycie pamięci DNN. W związku z tym podczas wybierania głębiej modeli (na przykład ResNet – 50 i ResNet-18) i/lub wyższej rozdzielczości obrazu (500\*500 pikseli i 224\*224 pikseli), co zwykle ma zmniejszyć rozmiar minibatch, aby uniknąć błędów braku pamięci. Podczas zmiany rozmiaru minibatch, często tempo uczenia musi również być dostosowane, jak podano w poniższej tabeli.
* Częstotliwość odrzucania (`dropout_rate`) i L2 regularizer (`l2_reg_weight`): nadmierne dopasowywania DNN można zmniejszyć przy użyciu przerwany współczynnika 0,5 (wartość domyślna to 0,5 w pakiecie wizji komputera) lub więcej oraz przez odpowiednie zwiększenie wagi regularizer (wartość domyślna to 0,0005 w komputerze wizji Pakiet). Należy jednak zauważyć, szczególnie w przypadku małych zestawów danych DNN dopasowywania uwierzytelniając się trudny i często niemożliwe uniknąć.


### <a name="parameter-definitions"></a>Definicje parametru

- **Learning szybkość**: krok rozmiar używane podczas uczenia spadku gradientu. Jeśli zestaw jest zbyt niski, a następnie modelu zajmie wiele epok do uczenia, jeśli zestaw jest zbyt wysoka, a następnie modelu nie zostaną zebrane dobrym rozwiązaniem. Zazwyczaj harmonogramu jest używana, gdzie tempo uczenia zostanie zmniejszona po określonej liczbie epok. Przykład E.For learning szybkości harmonogram `[0.05]*7 + [0.005]*7 + [0.0005]` odpowiada za pomocą tempo uczenia początkowej 0,05 dla pierwszych siedem epok, następuje 10 x tempo uczenia zmniejszenie 0,005 dla innego epok siedem i koniec dostrajanie modelu dla pojedynczego epoka. 100 x obniżonej stawki learning 0,0005.

- **Rozmiar minibatch**: GPU może przetwarzać wiele obrazów równolegle w celu przyspieszenia obliczeń. Te obrazy przetworzonych równoległe są też określane jako minibatch. Im większy rozmiar minibatch szybsze szkolenia będzie, jednak kosztem zwiększone użycie pamięci DNN.

### <a name="recommended-parameter-values"></a>Wartości parametrów zalecane

W poniższej tabeli przedstawiono innym parametrem zestawy, które były wyświetlane do tworzenia modeli Wysoka dokładność na różnych zadań klasyfikacji obrazu. Optymalne parametry zależą od określonego zestawu danych i dokładne procesora GPU używane, dlatego tabeli powinien być widoczny tylko przyjąć. Po wypróbowaniu tych parametrów, należy także uwzględnić rozdzielczości obrazów więcej niż 500 x 500 pikseli lub lepszy modeli, takie jak Resnet 101 lub Resnet 152.

Pierwszy wiersz w tabeli odpowiada domyślnych parametrów, które są ustawione w pakiecie wizji komputera. Wszystkich innych wierszy trwać dłużej, jednak uczenia (wskazane w pierwszej kolumnie) na korzyść większą dokładność (zobacz drugiej kolumny dla jego średni dokładność za pośrednictwem trzy zestawy danych wewnętrznego). Na przykład parametry w ostatnim wierszu zająć 5 – 15 x dłużej do uczenia, jednak spowodowała zwiększenie dokładności (uśrednionej) na trzy zestawy testów wewnętrznych z 82.6% % 92.8.

Modele głębiej i wyższej rozdzielczości zajmują więcej pamięci DNN, i dlatego rozmiar minibatch musi można zmniejszyć z modelu zwiększenia złożoności w celu uniknięcia poza z pamięci błędów. Jak widać w poniższej tabeli, jest korzystne zmniejszyć tempo uczenia przez dwukrotne zawsze, gdy minibatch zmniejszenie rozmiaru przy tym samym mnożnik. Może być konieczne uzyskać zmniejszyć rozmiar minibatch dalej GPU z mniejszych ilości pamięci.

| Czas szkolenia (wstępnie oszacowana) | Przykład dokładności | Rozmiar minibatch (*mb_size*) | Learning szybkość (*lr_per_mb*) | Rozdzielczość obrazu (*image_dims*) | Architektura DNN (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (odwołanie w) | 82.6% | 32 | [0,05] \*7 + [0,005]\*7 + [0,0005]  | (3 224, 224) | ResNet18_ImageNet_CNTK |
| 2-5 x    | 90.2% | 16 | [0, 025N] \*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2-5 x    | wynosi 87.5% | 16 | [0, 025N] \*7 + [0.0025]\*7 + [0.00025] | (3 224, 224) | ResNet50_ImageNet_CNTK |
| 5 – 15 x        | 92.8% |  8 | [0,01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące pakietu Azure Machine Learning wizji komputera:
+ Zapoznaj się z dokumentacją odwołania

+ Dowiedz się więcej o [inne pakiety języka Python dla usługi Azure Machine Learning](reference-python-package-overview.md)