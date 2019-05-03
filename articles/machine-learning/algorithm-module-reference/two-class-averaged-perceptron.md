---
title: 'Las decyzyjny z regresji: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu Perceptron uśredniane Two-Class w usłudze Azure Machine Learning w celu utworzenia na podstawie algorytmu uśrednionej perceptron model uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029238"
---
# <a name="two-class-averaged-perceptron-module"></a>Moduł Perceptron uśredniane Two-Class

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia na podstawie algorytmu uśrednionej perceptron model uczenia maszynowego.  
  
Ten algorytm klasyfikacji, jest metodą uczenia nadzorowanego i wymaga *oznakowane zestawu danych*, która zawiera kolumnę etykiety. Można nauczenia modelu, zapewniając modelu i oznakowane zestawu danych jako dane wejściowe [uczenie modelu](./train-model.md). Następnie można uczonego modelu do prognozowania wartości dla nowe przykłady danych wejściowych.  

### <a name="about-averaged-perceptron-models"></a>Modele uśrednionej perceptron — informacje

*Uśredniane metoda perceptron* to wersja wczesna i proste sieci neuronowych. W tym podejściu dane wejściowe są przydzielane do kilku możliwych danych wyjściowych oparte na liniowy funkcji, a następnie łączone z zestawem wagi, które są uzyskiwane z wektora funkcji — dlatego nazwy "perceptron."

Prostsze modeli perceptron są odpowiednie do nauki wzorce oddzielenia liniowo, natomiast sieci neuronowych (szczególnie głębokich sieciach neuronowych) umożliwia modelowanie złożonych granice klasy. Jednak perceptrons są realizowane szybciej i ponieważ one przetwarzają przypadków szeregowo perceptrons mogą być używane z ciągłe szkolenia.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak skonfigurować Perceptron uśredniane Two-Class

1.  Dodaj **Perceptron uśredniane Two-Class** modułu do eksperymentu.  

2.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować modelu, należy podać określonych wartości jako argumenty.
  
3.  Dla **uczenia współczynnik**, określ wartość dla *uczenia współczynnik*. Tempo uczenia wartości kontrolki rozmiar kroku, który jest używany w stochastycznego spadku gradientu każdorazowo modelu jest testowana i poprawić.
  
     Podejmując szybkość, z mniejszych, można przetestować model częściej, o ryzyku, użytkownik może utknąć w lokalnych pułap możliwości. W kroku powiększania sześcianu, będzie możliwe dokonanie konwergencji szybciej, ryzyko przekroczenia minimalne wartość true.
  
4.  Aby uzyskać **maksymalna liczba iteracji**, wpisz numer razy ma algorytm zbadanie danych szkoleniowych.  
  
     Zatrzymywanie wcześnie często zapewnia lepsze generalizacji. Zwiększenie liczby iteracji zwiększa dopasowywania ryzyko overfitting.
  
5.  Dla **inicjatora liczb losowych**możesz wpisać wartość całkowitą jako inicjatora. Za pomocą inicjatora jest zalecane, jeśli chcesz upewnić się, że uruchomieniu odtwarzaniem doświadczenia w.  
  
1.  Połącz zestaw danych szkoleniowych i jeden z modułów szkolenia:
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](train-model.md) modułu.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu, wraz z wagi funkcji z szkolenia, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md).


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 