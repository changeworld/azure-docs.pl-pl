---
title: 'K-średnich klastrowania: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu K-średnich klastrowanie w usłudze Azure Machine Learning to w opracowywaniu modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e9b7c8f2cf86245322679198b84b50d2c5edce8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464669"
---
# <a name="module-k-means-clustering"></a>Moduł: Klastrowanie metodą k-średnich

W tym artykule opisano sposób używania *klastrowania K-średnich* modułu usługi Azure Machine Learning Studio, aby utworzyć nieprzeszkolonych model klastrowania K-średnich. 
 
K-średnich jest jednym z najprostszych i znanych najlepszych *nienadzorowanych* algorytmów uczenia. Algorytm można użyć różnych usługi machine learning zadań, takich jak: 

* [Wykrywanie nietypowych danych](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Klastrowanie dokumenty tekstowe.
* Analizowanie zestawów danych, zanim użyjesz inne metody klasyfikacji lub regresji. 

Aby utworzyć model klastrowania, możesz:

* Ten moduł należy dodać do eksperymentu.
* Połącz z zestawu danych.
* Ustaw parametry, takie jak liczba klastrów oczekiwać metryki odległość, w celu utworzenia klastrów i tak dalej. 
  
Po skonfigurowaniu hiperparametrów modułu połączyć nieprzeszkolonych model [Train Model klastrowania](train-clustering-model.md). Ponieważ algorytm K-średnich jest metodą uczenie nienadzorowane, kolumna etykiety jest opcjonalny. 

+ Danych zawierających etykiety, można użyć wartości etykiet w prowadzeniu wybór klastrów oraz optymalizacji modelu. 

+ Jeśli dane nie ma etykiety, algorytm tworzy klastry reprezentujący możliwe kategorie, wyłącznie na podstawie danych.  

##  <a name="understand-k-means-clustering"></a>Zrozumienie K-średnich klastrowania
 
Ogólnie rzecz biorąc klaster używa interakcyjnych technik w przypadkach grupy w zestawie danych w klastrach, które posiadają podobnej charakterystyce. Te grupy są przydatne do eksplorowania danych, identyfikowanie anomalie w danych i po pewnym czasie składania prognozy. Klastrowanie modeli może również pomóc w zidentyfikowaniu relacje w zestawie danych, który nie może być logicznie pochodny przez obserwację przeglądania lub prosty. Z tego względu klastrowanie jest często używane w we wczesnej fazie zadania uczenia maszynowego do eksplorowania danych i odkrywać nieoczekiwany korelacji.  
  
 Po skonfigurowaniu model klastrowania przy użyciu metody K-średnich, należy określić numer docelowej *k* oznacza liczbę *centroids* ma w modelu. Centroida — oś jest punkt, który jest reprezentatywny dla każdego klastra. Algorytm K-średnich przypisuje każdego przychodzącego punktu danych do jednej z tych klastrów, minimalizując suma kwadratów w ramach klastra. 
 
Podczas przetwarzania danych szkoleniowych, algorytm K-średnich rozpoczyna się od początkowego zestawu centroids losowo wybrany. Centroids służyć jako punktami startowymi dla klastrów, a ich stosowanie algorytmu Lloyd's iteracyjne uściślić ich lokalizacji. Algorytm K-średnich zatrzymuje, kompilowania i uściślenie klastrów, gdy spełnia co najmniej jeden z następujących warunków:  
  
-   Stabilizacja centroids, co oznacza, że nie jest już zmienić przypisania klastra dla poszczególnych punktów i algorytm została połączona na rozwiązanie.  
  
-   Algorytm Ukończono uruchamianie określonej liczby iteracji.  
  
 Po zakończeniu fazy szkolenia, należy użyć [przypisywania danych do klastrów](assign-data-to-clusters.md) modułu, aby przypisać nowe przypadki do jednego klastra, w których odnaleźć przy użyciu algorytmu K-średnich. Wykonuje przypisania klastra obliczeń odległość między nowy przypadek środek strefy widocznego w każdym klastrze. Każdy nowy przypadek jest przypisany do klastra z dokładnością centroida — oś.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurowanie modułu K-średnich klastrowania
  
1.  Dodaj **klastrowania K-średnich** modułu do eksperymentu.  
  
2.  Aby określić, jak model, który ma być uczony, wybierz pozycję **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Jeśli znasz dokładną parametry, których chcesz użyć w model klastrowania, możesz podać określonych wartości jako argumenty.  
  
3.  Aby uzyskać **numer Centroids**, wpisz liczbę klastrów ma algorytm zaczynać.  
  
     Model nie jest gwarantowane do produkcji dokładnie tę liczbę klastrów. Algorytm rozpoczyna się od tej liczby punktów danych i wykonuje iteracje, aby znaleźć optymalną konfigurację.  
  
4.  Właściwości **inicjowania** służy do określania algorytmu, który jest używany do definiowania początkowej konfiguracji klastra.  
  
    -   **Pierwsze N**: Niektóre początkowa liczba punktów danych są wybrane z zestawu danych i używany jako początkowa oznacza. 
    
         Ta metoda jest również nazywany *Forgy metoda*.  
  
    -   **Losowe**: Algorytm losowo umieszcza punkt danych w klastrze, a następnie oblicza średnią początkowej jako środek strefy widocznego klastra losowo przypisany punktów. 

         Ta metoda jest również nazywany *losowych partycji* metody.  
  
    -   **K — oznacza, że ++** : To jest domyślną metodą inicjowanie klastrów.  
  
         **K — oznacza, że ++** algorytm został zaproponowany w 2007 David Arthur i Sergei Vassilvitskii, aby uniknąć niską klastrowania przez standardowy algorytm K-średnich. **K — oznacza, że ++** poprawia standardowa K-średnich przy użyciu innej metody do wybierania centra klastra.  
  
    
5.  Dla **inicjatora liczb losowych**, opcjonalnie wpisz wartość do użycia jako zalążek inicjowania klastra. Ta wartość może mieć znaczący wpływ na wybór klastra.  
  
6.  Aby uzyskać **metryki**, wybierz funkcję pomiaru odległość między wektorów klastra lub między nowe punkty danych i losowo wybranym centroida — oś. Usługa Azure Machine Learning obsługuje następujące metryki klastra w odległości:  
  
    -   **Euklidesowa**: Odległość euklidesowa najczęściej jest używana jako środek punktowy klastra K-średnich klastrowania. Ta metryka jest preferowana, ponieważ minimalizuje mean odległość między punktami a centroids.
  
7.  Aby uzyskać **iteracji**, wpisz liczbę razy algorytm powinny przejść przez dane szkoleniowe, przed jego Kończenie znajdujących się w zaznaczenia centroids.  
  
     Można dostosować ten parametr, aby równoważyć dokładności względem czasu szkoleń.  
  
8.  Aby uzyskać **przypisać etykietę tryb**, wybierz opcję określającą, jak kolumny etykiety, jeśli jest obecny w zestawie danych, powinien zostać obsłużony.  
  
     Ponieważ klaster K-średnich nienadzorowanych machine learning — metoda, etykiety są opcjonalne. Jednak jeśli zestaw danych zawiera już kolumnę etykiety, można użyć tych wartości przeprowadzenie wybór klastrów lub można określić, że wartości ignorowane.  
  
    -   **Ignoruj etykiety kolumn**: Wartości w kolumnie etykiety są ignorowane i nie są używane podczas tworzenia modelu.
  
    -   **Wypełnienie brakujących wartości**: Etykieta wartości kolumny są używane jako funkcje ułatwiające tworzenie klastrów. Jeśli wszystkie wiersze brakuje etykiety, wartość jest kalkulacyjne, korzystając z innych funkcji.  
  
    -   **Zastąp z najbliższego Centrum**: Etykieta wartości w kolumnach są zastępowane wartości przewidywane etykiety, stosując etykiety punkt, który jest najbardziej zbliżony do bieżącego centroida — oś.  

8.  Wybierz **normalizacji funkcji** opcję, jeśli chcesz znormalizować funkcji przed szkolenia.
  
     Jeśli zastosujesz normalizacji przed szkolenia, punkty danych są znormalizowane zgodnie z `[0,1]` przez MinMaxNormalizer.

10. Uczenie modelu.  
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, Dodaj oznakowane zestaw danych i uczenie modelu przy użyciu [uczenie modelu klastrowania](train-clustering-model.md) modułu.  
  
### <a name="results"></a>Wyniki

Po zakończeniu konfigurowania i uczenia modelu masz modelu, który służy do generowania wyników. Istnieją różne sposoby do nauczenia modelu, a wiele sposobów, aby wyświetlać i używać wyników: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Przechwytywanie migawki modelu w Twoim obszarze roboczym

Jeśli użyto [Train Model klastrowania](train-clustering-model.md) modułu:

1. Kliknij prawym przyciskiem myszy **Train Model klastrowania** modułu.

2. Wybierz **modelu Trained**, a następnie wybierz pozycję **Zapisz jako Uczonego modelu**.

Zapisany model reprezentuje dane szkoleniowe w czasie, zapisane modelu. Jeśli później zaktualizować dane szkoleniowe, używany w eksperymencie, jednak nie powoduje aktualizacji zapisanych modelu. 

#### <a name="see-the-clustering-result-dataset"></a>Klastrowanie zestaw wyników danych 

Jeśli użyto [Train Model klastrowania](train-clustering-model.md) modułu:

1. Kliknij prawym przyciskiem myszy **Train Model klastrowania** modułu.

2. Wybierz **zestaw wyników danych**, a następnie wybierz pozycję **Visualize**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Porady dotyczące generowania najlepszy model klastra  

Wiadomo, że *rozmieszczania* procesu, który jest używany podczas tworzenia klastrów może znacząco wpłynąć na modelu. Rozmieszczania oznacza, że początkowe umieszczanie punktów w potencjalne centroids.
 
Na przykład jeśli zestaw danych zawiera wiele wartości odstających i odstające jest wybierany w celu umieszczenia klastrów, inne punkty danych zmieszczą się również za pomocą tego klastra, a klaster może być klasą pojedynczą. Oznacza to może mieć tylko jeden punkt.  
  
Można uniknąć tego problemu, na kilka sposobów:  
  
-   Zmień liczbę centroids i spróbuj wiele wartości inicjatora.  
  
-   Tworzenie wielu modeli, różnicowanie metrykę lub iteracja więcej.  
  
Ogólnie rzecz biorąc z klastrowaniem modeli, możliwe jest dowolnym danej konfiguracji będzie skutkować zbiór lokalnie zoptymalizowane klastry. Innymi słowy zestaw klastrów, który jest zwracany przez model odpowiada tylko bieżące punkty danych i nie jest generalizacji z innymi danymi. Jeśli używasz innej konfiguracji początkowej, metoda K-średnich może się okazać różnych konfiguracji może być wyższego poziomu. 
