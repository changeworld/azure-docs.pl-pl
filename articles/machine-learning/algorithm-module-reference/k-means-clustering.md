---
title: 'K-Oznacza klastrowanie: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z modułu klastrowania k-means w usłudze Azure Machine Learning do uczenia modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921168"
---
# <a name="module-k-means-clustering"></a>Moduł: K-Oznacza klastrowanie

W tym artykule opisano, jak używać modułu *klastrowania K-Means* w projektancie usługi Azure Machine Learning (wersja zapoznawcza) do utworzenia nieprzeszkolonego modelu klastrowania k-oznacza. 
 
K-oznacza jest jednym z najprostszych i najbardziej znanych algorytmów uczenia się *bez nadzoru.* Algorytmu można używać do różnych zadań uczenia maszynowego, takich jak: 

* [Wykrywanie nieprawidłowych danych](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Klastrowanie dokumentów tekstowych.
* Analizowanie zestawów danych przed użyciem innych metod klasyfikacji lub regresji. 

Aby utworzyć model klastrowania, należy:

* Dodaj ten moduł do potoku.
* Łączenie zestawu danych.
* Ustaw parametry, takie jak liczba klastrów, których oczekujesz, metryka odległości do użycia podczas tworzenia klastrów itd. 
  
Po skonfigurowaniu hiperparametrów modułu można podłączyć nieprzeszkolony model do [modelu klastrowania pociągu](train-clustering-model.md). Ponieważ algorytm K-oznacza jest nienadzorowana metoda uczenia się, kolumna etykiety jest opcjonalne. 

+ Jeśli dane zawierają etykietę, można użyć wartości etykiet, aby poprowadzić wybór klastrów i zoptymalizować model. 

+ Jeśli dane nie mają etykiety, algorytm tworzy klastry reprezentujące możliwe kategorie, oparte wyłącznie na danych.  

##  <a name="understand-k-means-clustering"></a>Zrozumienie k-oznacza klastrowania
 
Ogólnie rzecz biorąc klastrowanie używa technik iteracyjnych do grupowania przypadków w zestawie danych w klastry, które mają podobne właściwości. Te grupy są przydatne do eksplorowania danych, identyfikowania anomalii w danych i ostatecznie do tworzenia prognoz. Klastrowanie modeli może również pomóc w zidentyfikowaniu relacji w zestawie danych, które mogą nie być logicznie uzyskane przez przeglądanie lub prostą obserwację. Z tych powodów klastrowanie jest często używane we wczesnych fazach zadań uczenia maszynowego, aby eksplorować dane i wykrywać nieoczekiwane korelacje.  
  
 Podczas konfigurowania modelu klastrowania przy użyciu k-oznacza metoda, należy określić numer docelowy *k,* który wskazuje liczbę *centroidów,* które mają w modelu. Centroid jest punktem, który jest reprezentatywny dla każdego klastra. Algorytm K oznacza przypisuje każdy przychodzący punkt danych do jednego z klastrów, minimalizując sumę kwadratów w klastrze. 
 
Podczas przetwarzania danych szkoleniowych, K oznacza algorytm rozpoczyna się od początkowego zestawu losowo wybranych centroidów. Centroidy służą jako punkty wyjścia dla klastrów i stosują algorytm Lloyda do iteracyjnego udoskonalenia ich lokalizacji. Algorytm K oznacza zatrzymuje tworzenie i rafinację klastrów, gdy spełnia jeden lub więcej z tych warunków:  
  
-   Centroidy stabilizują się, co oznacza, że przypisania klastra dla poszczególnych punktów nie zmieniają się, a algorytm zbiega się w rozwiązaniu.  
  
-   Algorytm zakończył uruchamianie określonej liczby iteracji.  
  
 Po zakończeniu fazy szkolenia, należy użyć [przypisz dane do klastrów](assign-data-to-clusters.md) moduł przypisać nowe przypadki do jednego z klastrów, które zostały znalezione przy użyciu algorytmu K-oznacza. Przypisanie klastra można wykonać, obliczając odległość między nowym przypadkiem a centroidem każdego klastra. Każdy nowy przypadek jest przypisany do klastra z najbliższym centroidem.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurowanie modułu klastrowania k-means
  
1.  Dodaj moduł **klastrowania K-Means** do potoku.  
  
2.  Aby określić sposób trenowania modelu, wybierz opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Jeśli znasz dokładne parametry, których chcesz użyć w modelu klastrowania, możesz podać określony zestaw wartości jako argumenty.  
  
3.  W przypadku **liczby centroidów**wpisz liczbę klastrów, od których ma zaczynać się algorytm.  
  
     Model nie jest gwarantowane do produkcji dokładnie tej liczby klastrów. Algorytm rozpoczyna się od tej liczby punktów danych i iteruje, aby znaleźć optymalną konfigurację.  
  
4.  **Inicjowanie** właściwości służy do określenia algorytmu, który jest używany do definiowania początkowej konfiguracji klastra.  
  
    -   **Pierwszy N:** Niektóre początkowe punkty danych są wybierane z zestawu danych i używane jako środek początkowy. 
    
         Ta metoda jest również nazywana *metodą Forgy.*  
  
    -   **Losowo:** Algorytm losowo umieszcza punkt danych w klastrze, a następnie oblicza początkową średnią jako środek ciężkości losowo przypisanych punktów klastra. 

         Ta metoda jest również nazywana metodą *losowej partycji.*  
  
    -   **K-Means++**: Jest to domyślna metoda inicjowania klastrów.  
  
         Algorytm **K-means++** został zaproponowany w 2007 roku przez Davida Arthura i Siergieja Vassilvitskiiego, aby uniknąć słabego klastrowania przez standardowy algorytm K-means. **K-means++** poprawia standardowe środki K przy użyciu innej metody wyboru początkowych centrów klastra.  
  
    
5.  W polu **Ilość liczb losowych**opcjonalnie wpisz wartość, która ma być używana jako materiał siewny dla inicjowania klastra. Ta wartość może mieć znaczący wpływ na wybór klastra.  
  
6.  W przypadku **opcji Metryka**wybierz funkcję do pomiaru odległości między wektorami klastra lub między nowymi punktami danych a losowo wybranym centroidem. Usługa Azure Machine Learning obsługuje następujące metryki odległości klastra:  
  
    -   **Euklides**: Odległość euklidesowa jest powszechnie stosowana jako miara rozproszenia klastra dla klastrów K. Ta metryka jest preferowana, ponieważ minimalizuje średnią odległość między punktami a centroidami.
  
7.  W przypadku **iteracji**wpisz liczbę razy algorytm powinien iterować dane szkoleniowe przed sfinalizowaniem wyboru centroidów.  
  
     Można dostosować ten parametr, aby zrównoważyć dokładność w stosunku do czasu treningu.  
  
8.  W polu **Przypisz tryb etykiety**wybierz opcję określającą sposób obsługi kolumny etykiety, jeśli jest ona obecna w zestawie danych.  
  
     Ponieważ K oznacza klastrowania jest nienadzorowane metody uczenia maszynowego, etykiety są opcjonalne. Jeśli jednak zestaw danych ma już kolumnę etykiet, można użyć tych wartości do kierowania wyborem klastrów lub można określić, że wartości będą ignorowane.  
  
    -   **Ignoruj kolumnę etykiety:** Wartości w kolumnie etykiety są ignorowane i nie są używane do tworzenia modelu.
  
    -   **Wypełnij brakujące wartości**: Wartości kolumn etykiet są używane jako funkcje ułatwiające tworzenie klastrów. Jeśli w wierszach brakuje etykiety, wartość jest przypisywana przy użyciu innych funkcji.  
  
    -   **Zastąp od najbliższego do środka**: Wartości kolumn etykiety są zastępowane przewidywanymi wartościami etykiet, przy użyciu etykiety punktu, który znajduje się najbliżej bieżącego środka ciężkości.  

8.  Wybierz opcję **Normalizuj operacje,** jeśli chcesz normalizować operacje przed treningiem.
  
     Jeśli zastosujesz normalizacji, przed treningiem punkty `[0,1]` danych są normalizowane przez MinMaxNormalizer.

10. Trenuj model.  
  
    -   Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** dodaj oznakowany zestaw danych i trenuj model przy użyciu modułu [Model klastrowania pociągu.](train-clustering-model.md)  
  
## <a name="results"></a>Wyniki

Po zakończeniu konfigurowania i szkolenia modelu, masz model, który można użyć do generowania wyników. Istnieje jednak wiele sposobów uczenia modelu i wiele sposobów wyświetlania i używania wyników: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Przechwytywanie migawki modelu w obszarze roboczym

Jeśli użyto modułu [Model klastrowania pociągu:](train-clustering-model.md)

1. Wybierz moduł **Model klastrowania pociągu** i otwórz prawy panel.

2. Wybierz kartę **Wyjścia.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać kopię uczonego modelu.

Zapisany model reprezentuje dane szkoleniowe w momencie zapisania modelu. Jeśli później zaktualizujesz dane szkoleniowe używane w potoku, nie zaktualizuje zapisany model. 

### <a name="see-the-clustering-result-dataset"></a>Wyświetlanie zestawu danych wyników klastrowania 

Jeśli użyto modułu [Model klastrowania pociągu:](train-clustering-model.md)

1. Kliknij prawym przyciskiem myszy moduł **Modelu klastrowania pociągu.**

2. Wybierz pozycję **Wizualizuj**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Wskazówki dotyczące generowania najlepszego modelu klastrowania  

Wiadomo, że proces *wysiewu,* który jest używany podczas klastrowania może znacząco wpłynąć na model. Wysiew oznacza początkowe umieszczenie punktów w potencjalnych centroidach.
 
Na przykład jeśli zestaw danych zawiera wiele odstający i odstające jest wybrany do nasion klastrów, żadne inne punkty danych będzie dobrze pasować do tego klastra i klastra może być singleton. Oznacza to, że może mieć tylko jeden punkt.  
  
Ten problem można uniknąć na kilka sposobów:  
  
-   Zmień liczbę centroidów i spróbuj wielu wartości materiału siewnego.  
  
-   Utwórz wiele modeli, zmieniając metrykę lub iterując więcej.  
  
Ogólnie rzecz biorąc w przypadku modeli klastrowania jest możliwe, że każda określona konfiguracja spowoduje, że zestaw klastrów zoptymalizowany jest lokalnie. Innymi słowy zestaw klastrów, który jest zwracany przez model odpowiada tylko bieżących punktów danych i nie jest generalizable do innych danych. Jeśli używasz innej konfiguracji początkowej, K-oznacza metoda może znaleźć inną, superior, konfiguracji. 

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
