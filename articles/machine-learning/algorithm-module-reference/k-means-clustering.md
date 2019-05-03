---
title: 'K-średnich klastrowania: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu klastrowania K-średnich w usłudze Azure Machine Learning to w opracowywaniu modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29aeb460581655190b7c3f4256647059f4642d3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029703"
---
# <a name="k-means-clustering"></a>K-średnich klastrowania

W tym artykule opisano sposób używania **klastrowania K-średnich** modułu usługi Azure Machine Learning Studio, aby utworzyć nieprzeszkolonych model klastrowania K-średnich. 
 
K-średnich jest jednym z najprostszych i znanych najlepszych *nienadzorowanych* algorytmów uczenia i może służyć do różnych zadania uczenia maszynowego, takich jak [wykrywanie nietypowych danych](https://msdn.microsoft.com/magazine/jj891054.aspx), klastrowania tekstu dokumenty i analitycznego zestawu danych przed przy użyciu innych metod klasyfikacji lub regresji. Aby utworzyć model klastrowania, Dodaj ten moduł do eksperymentu, łączenie zestawu danych i ustaw parametry, takie jak liczba klastrów oczekiwać metryki odległość, w celu utworzenia klastrów i tak dalej. 
  
Po skonfigurowaniu hiperparametrów modułu, łączyć się z pomocą techniczną firmy nieprzeszkolonych model [Train Model klastrowania](train-clustering-model.md) ponieważ algorytm K-średnich jest metodą uczenie nienadzorowane, kolumna etykiety jest opcjonalne. 

+ Danych zawierających etykiety, można użyć wartości etykiet w prowadzeniu wybór klastrów oraz optymalizacji modelu. 

+ Jeśli dane nie ma etykiety, algorytm tworzy klastry reprezentujący możliwe kategorie, wyłącznie na podstawie danych.  
  

  
##  <a name="understanding-k-means-clustering"></a>Opis k średnich klastrowania
 
Ogólnie rzecz biorąc klaster używa interakcyjnych technik w przypadkach grupy w zestawie danych w klastrach zawierających podobne charakterystyki. Te grupy są przydatne do eksplorowania danych, identyfikowanie anomalie w danych i po pewnym czasie składania prognozy. Klastrowanie modeli może również pomóc w zidentyfikowaniu relacje w zestawie danych, który nie może być logicznie pochodny przez obserwację przeglądania lub prosty. Z tego względu klastrowanie jest często używane w we wczesnej fazie zadania uczenia maszynowego do eksplorowania danych i odkrywać nieoczekiwany korelacji.  
  
 Po skonfigurowaniu model klastrowania przy użyciu metody k średnich, należy określić numer docelowej *k* określającą liczbę *centroids* ma w modelu. Centroida — oś jest punkt, który jest reprezentatywny dla każdego klastra. Algorytm K-średnich przypisuje każdego przychodzącego punktu danych do jednej z tych klastrów, minimalizując suma kwadratów w ramach klastra. 
 
Podczas przetwarzania danych szkoleniowych, algorytm K-średnich rozpoczyna się od początkowego zestawu randomnly wybrany centroids, które służą jako punktami startowymi dla każdego klastra, a następnie stosuje algorytm Lloyd's iteracyjne uściślić lokalizacje centroids. Algorytm K-średnich zatrzymuje, kompilowania i uściślenie klastrów, gdy spełnia co najmniej jeden z następujących warunków:  
  
-   Stabilizacja centroids, co oznacza, że nie jest już zmienić przypisania klastra dla poszczególnych punktów i algorytm została połączona na rozwiązanie.  
  
-   Algorytm Ukończono uruchamianie określonej liczby iteracji.  
  
 Po zakończeniu fazy szkolenia, użyj [przypisywania danych do klastrów](assign-data-to-clusters.md) modułu, aby przypisać nowe przypadki do jednego klastra, które zostało znalezione przez algorytm k średnich. Przypisanie klastra jest wykonywane przez przetwarzanie odległość między nowy przypadek środek strefy widocznego w każdym klastrze. Każdy nowy przypadek jest przypisany do klastra z dokładnością centroida — oś.  

## <a name="how-to-configure-k-means-clustering"></a>Konfigurowanie klastrowania K-średnich
  
1.  Dodaj **klastrowania K-średnich** modułu do eksperymentu.  
  
2.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Jeśli znasz dokładną parametry, których chcesz użyć w model klastrowania, możesz podać określonych wartości jako argumenty.  
  
   
  
3.  Aby uzyskać **numer Centroids**, wpisz liczbę klastrów ma algorytm zaczynać.  
  
     Model nie jest gwarantowane do produkcji dokładnie tę liczbę klastrów. Algorithn rozpoczyna się od tej liczby punktów danych i wykonuje iteracje, aby znaleźć optymalną konfigurację.  
  
     
  
4.  Właściwości **inicjowania** służy do określania algorytmu, który jest używany do definiowania początkowej konfiguracji klastra.  
  
    -   **Pierwsze N**: Niektóre początkowa liczba punktów danych są wybrane z zestawu danych i używany jako początkowa oznacza.  
  
         Nazywane również *Forgy metoda*.  
  
    -   **Losowe**: Algorytm losowo umieszcza punkt danych w klastrze, a następnie oblicza średnią początkowej jako środek strefy widocznego klastra losowo przypisany punktów.  
  
         Nazywane również *losowych partycji* metody.  
  
    -   **K — oznacza, że ++**: To jest domyślną metodą inicjowanie klastrów.  
  
         **K-średnich ++** algorytm został zaproponowany w 2007 przez David Arthur i Sergei Vassilvitskii, aby uniknąć niską klastrowania przez standard k średnich algorytmu. **K-średnich ++** poprawia standardowa K-średnich przy użyciu innej metody do wybierania centra klastra.  
  
    
5.  Dla **inicjatora liczb losowych**, opcjonalnie wpisz wartość do użycia jako zalążek inicjowania klastra. Ta wartość może mieć znaczący wpływ na wybór klastra.  
  
    
  
6.  Aby uzyskać **metryki**, wybierz funkcję pomiaru odległość między wektorów klastra lub między nowe punkty danych i losowo wybranym centroida — oś. Usługa Azure Machine Learning obsługuje następujące metryki klastra w odległości:  
  
    -   **Euklidesowa**: Odległość euklidesowa najczęściej jest używana jako środek punktowy klastra K-średnich klastrowania. Ta metryka jest preferowana, ponieważ minimalizuje mean odległość między punktami a centroids.
  

  
7.  Aby uzyskać **iteracji**, wpisz liczbę razy algorytm powinny przejść przez dane szkoleniowe przed ukończeniem zaznaczenie centroids.  
  
     Można dostosować ten parametr, aby dokładność salda i czasu szkoleń.  
  
8.  Aby uzyskać **przypisać etykietę tryb**, wybierz opcję określającą, jak kolumny etykiety, jeśli jest obecny w zestawie danych, powinien zostać obsłużony.  
  
     Ponieważ klaster K-średnich nienadzorowanych machine learning — metoda, etykiety są opcjonalne. Jednak jeśli zestaw danych zawiera już kolumnę etykiety, można użyć tych wartości, przeprowadzenie wybór klastrów lub można określić, że wartości ignorowane.  
  
    -   **Ignoruj etykiety kolumn**: Wartości w kolumnie etykiety są ignorowane i nie są używane podczas tworzenia modelu.
  
    -   **Wypełnienie brakujących wartości**: Etykieta wartości kolumny są używane jako funkcje ułatwiające tworzenie klastrów. Jeśli wszystkie wiersze brakuje etykiety, wartość jest kalkulacyjne, korzystając z innych funkcji.  
  
    -   **Zastąp z najbliższego Centrum**: Etykieta wartości w kolumnach są zastępowane wartości przewidywane etykiety, stosując etykiety punkt, który jest najbardziej zbliżony do bieżącego centroida — oś.  

8.  Wybierz opcję **normalizacji funkcji**, jeśli chcesz znormalizować funkcji przed szkolenia.
  
     Jeśli zastosujesz normalizacji przed szkolenia, punkty danych są znormalizowane zgodnie z `[0,1]` przez MinMaxNormalizer.

10. Uczenie modelu.  
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, Dodaj oznakowane zestaw danych i uczenie modelu przy użyciu [uczenie modelu klastrowania](train-clustering-model.md) modułu.  
  

### <a name="results"></a>Wyniki

Po zakończeniu konfigurowania i uczenia modelu masz modelu, który służy do generowania wyników. Istnieją różne sposoby do nauczenia modelu, a wiele sposobów, aby wyświetlać i używać wyników: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Przechwytywanie migawki modelu w Twoim obszarze roboczym

+ Jeśli użyto [Train Model klastrowania](train-clustering-model.md) modułu
    1. Kliknij prawym przyciskiem myszy [Train Model klastrowania](train-clustering-model.md) modułu.
    2. Wybierz **modelu Trained** a następnie kliknij przycisk **Zapisz jako Uczonego modelu**.


Zapisane modelu będzie reprezentować dane szkoleniowe w czasie, zapisane modelu. Jeśli później zaktualizować dane szkoleniowe, używany w eksperymencie, nie powoduje aktualizacji zapisanych modelu. 



#### <a name="see-the-clustering-result-dataset"></a>Klastrowanie zestaw wyników danych 

+ Jeśli użyto [Train Model klastrowania](train-clustering-model.md) modułu
    1. Kliknij prawym przyciskiem myszy [Train Model klastrowania](train-clustering-model.md) modułu.
    2. Wybierz **zestaw wyników danych** a następnie kliknij przycisk **Visualize**.


### <a name="tips-for-generating-the-best-clustering-model"></a>Porady dotyczące generowania najlepszy model klastra  

Wiadomo, że **rozmieszczania** proces używany podczas tworzenia klastrów może znacząco wpłynąć na modelu. Rozmieszczania oznacza, że początkowe umieszczanie punktów w potental centroids.
 
Na przykład, jeśli zestaw danych zawiera wiele wartości odstających i odstające jest wybierany w celu umieszczenia klastrów, inne punkty danych zmieszczą się również za pomocą tego klastra i klastra może być singletonem: oznacza to, że klastra z tylko jednym punktem.  
  
Istnieją różne sposoby, aby uniknąć tego problemu:  
  
-   Zmień liczbę centroids i spróbuj wiele wartości inicjatora.  
  
-   Tworzenie wielu modeli, różnicowanie metrykę lub iteracja więcej.  
  
  
Ogólnie rzecz biorąc z klastrowaniem modeli, możliwe jest dowolnym danej konfiguracji będzie skutkować zbiór lokalnie zoptymalizowane klastry. Oznacza to zbiór klastrów zwracane przez model odpowiada tylko bieżące punkty danych, a nie jest generalizacji z innymi danymi. Jeśli używasz innej konfiguracji początkowej, metoda K-średnich może się okazać różnych konfiguracji może być wyższego poziomu. 
