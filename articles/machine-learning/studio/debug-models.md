---
title: Debugowanie modelu
titleSuffix: ML Studio (classic) - Azure
description: Jak debugować błędy produkowane przez moduły modelu pociągu i wynik w usłudze Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218101"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Debugowanie modelu w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Podczas uruchamiania modelu można napotkać następujące błędy:

* moduł [Model pociągu][train-model] generuje błąd 
* moduł [Score Model][score-model] daje nieprawidłowe wyniki 

W tym artykule opisano potencjalne przyczyny tych błędów.


## <a name="train-model-module-produces-an-error"></a>Moduł modelu pociągu generuje błąd

![image1](./media/debug-models/train_model-1.png)

Moduł [modelu pociągu][train-model] oczekuje dwóch wejść:

1. Typ modelu uczenia maszynowego z kolekcji modeli dostarczonych przez usługę Azure Machine Learning Studio (klasyczny).
2. Dane szkoleniowe z określoną kolumną Label, która określa zmienną do przewidzenia (przyjmuje się, że inne kolumny są funkcje).

Ten moduł może spowodować błąd w następujących przypadkach:

1. Kolumna Etykieta jest określona niepoprawnie. Może się tak zdarzyć, jeśli jako etykietę zaznaczono więcej niż jedną kolumnę lub zostanie zaznaczony niepoprawny indeks kolumny. Na przykład drugi przypadek ma zastosowanie, jeśli indeks kolumny 30 jest używany z wejściowym zestawem danych, który ma tylko 25 kolumn.

2. Zestaw danych nie zawiera żadnych kolumn funkcji. Na przykład jeśli wejściowy zestaw danych ma tylko jedną kolumnę, która jest oznaczona jako Label kolumny, nie będzie żadnych funkcji, z którymi do tworzenia modelu. W takim przypadku [moduł modelu pociągu][train-model] generuje błąd.

3. Wejściowy zestaw danych (Funkcje lub Etykieta) zawiera nieskończoność jako wartość.

## <a name="score-model-module-produces-incorrect-results"></a>Moduł modelu wyników daje niepoprawne wyniki

![image2](./media/debug-models/train_test-2.png)

W typowym eksperymencie szkolenia/testowania dla uczenia nadzorowanego moduł [Podziel dane][split] dzieli oryginalny zestaw danych na dwie części: jedna część jest używana do uczenia modelu, a jedna część służy do oceniania wydajności wykonywanego modelu. Przeszkolony model jest następnie używany do oceny danych testowych, po czym wyniki są oceniane w celu określenia dokładności modelu.

Moduł [Score Model][score-model] wymaga dwóch wejść:

1. Dane wyjściowe modelu z modułu [Model pociągu.][train-model]
2. Zestaw danych oceniania, który różni się od zestawu danych używanego do trenowania modelu.

Jest możliwe, że mimo pomyślnego wyniku eksperymentu, [wynik model][score-model] modułu generuje nieprawidłowe wyniki. Kilka scenariuszy może spowodować ten problem się zdarzyć:

1. Jeśli określony Label jest kategoryczny i model regresji jest trenowany na danych, niepoprawne dane wyjściowe będą produkowane przez [wynik modelu][score-model] modułu. Dzieje się tak, ponieważ regresja wymaga zmiennej odpowiedzi ciągłej. W takim przypadku byłoby bardziej odpowiednie do korzystania z modelu klasyfikacji. 

2. Podobnie jeśli model klasyfikacji jest przeszkolony na zestawie danych o zmiennoprzecinkowych numerów w Label kolumny, może to spowodować niepożądane wyniki. Dzieje się tak, ponieważ klasyfikacja wymaga zmiennej odpowiedzi dyskretnej, która zezwala tylko na wartości, które mają zakres w skończonej i małej, zestaw klas.

3. Jeśli zestaw danych oceniania nie zawiera wszystkich funkcji używanych do trenowania modelu, [model wyniku][score-model] generuje błąd.

4. Jeśli wiersz w zestawie danych oceniania zawiera brakującą wartość lub nieskończoną wartość dla dowolnej z jego funkcji, [model wynik][score-model] nie generuje żadnych danych wyjściowych odpowiadających tego wiersza.

5. [Model wyników][score-model] może produkować identyczne dane wyjściowe dla wszystkich wierszy w zestawie danych oceniania. Może to wystąpić, na przykład podczas próby klasyfikacji przy użyciu lasów decyzji, jeśli minimalna liczba próbek na węzeł liścia jest wybrana jako większa niż liczba dostępnych przykładów szkolenia.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

