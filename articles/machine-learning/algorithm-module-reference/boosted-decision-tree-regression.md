---
title: 'Regresja wzmocnionego drzewa decyzyjnego: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu wzmocnione regresji drzewa decyzyjnego w usłudze Azure Machine Learning w celu utworzenia zespołu drzew regresji przy użyciu zwiększania wyniku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67e54f10074ee566ce974dbd27485904bfe0a653
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411546"
---
# <a name="boosted-decision-tree-regression-module"></a>Zwiększona modułu regresji drzewa decyzyjnego

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia zespołu drzew regresji przy użyciu zwiększania wyniku. *Ulepszanie* oznacza, że każdego drzewa jest zależny od wcześniejszego drzewa. Algorytm uczy się przez dopasowanie pozostały drzewa, które go poprzedzał. W związku z tym zwiększania wyniku w zespołu drzewa decyzyjnego zwykle zwiększyć dokładność z pewne ryzyko małych, mniej pokrycia.  
  
Ta metoda regresji jest metodą uczenia nadzorowanego i dlatego wymaga *etykietą zestawu danych*. Kolumna etykieta musi zawierać wartości liczbowe.  

> [!NOTE]
> Ten moduł służy tylko w przypadku zestawów danych, które są używane wartości liczbowych zmienne.  

Po zdefiniowaniu modelu nauczenia go za pomocą [uczenie modelu](./train-model.md).

> [!TIP]
> Chcesz dowiedzieć się więcej o drzewa, które zostały utworzone? Po model jest uczony, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md) modułu, a następnie wybierz pozycję **Visualize** się drzewa, który został utworzony w każdej iteracji. Możesz przejść do dzieli dane dla każdego drzewa i Zobacz reguły dla każdego węzła.  
  
## <a name="more-about-boosted-regression-trees"></a>Więcej informacji na temat Regresja wzmocnionego drzewa  

Ulepszanie jest jednym z kilku metod klasycznego do tworzenia modeli zespołu, wraz z workowania, losowych lasów i tak dalej.  W usłudze Azure Machine Learning drzewa decyzyjnego Użyj wydajne implementacji gradientu SKŁADNICY zwiększania wyniku algorytmu. Ulepszanie gradientu jest techniką problemów regresji uczenia maszynowego. Zbudował każdego drzewa regresji, w sposób stopniowy do pomiaru błędów w każdym kroku i popraw go w ciągu następnych przy użyciu funkcji utraty wstępnie zdefiniowane. Tak więc model predykcyjny jest faktycznie zespołu słabszy modele predykcyjne.  
  
Problemy z regresji zwiększania wyniku tworzy szereg drzew w sposób stopniowy, a następnie wybiera optymalną drzewa, przy użyciu dowolnego differentiable utraty funkcji.  
  
Aby uzyskać dodatkowe informacje zobacz następujące artykuły:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Tego artykułu w Wikipedii na ulepszanie gradientu zawiera podstawowe informacje dotyczące wzmocnionego drzewa. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Badań firmy Microsoft: Z RankNet do LambdaRank do LambdaMART: Przegląd. By J.C. Burges.

Gradient zwiększania wyniku metody można także klasyfikacji problemów zmniejszając ich regresji przy użyciu funkcji odpowiednie utraty. Aby uzyskać więcej informacji na temat implementacji wzmocnionego drzewa dla zadań klasyfikacji zobacz [Two-Class Boosted Decision drzewa](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak skonfigurować wzmocnione regresji drzewa decyzyjnego

1.  Dodaj **wzmocnione drzewo decyzyjne** modułu do eksperymentu. Można znaleźć w tym module w ramach **uczenia maszynowego**, **zainicjować**w obszarze **regresji** kategorii. 
  
2.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Wybierz tę opcję, jeśli wiesz, jak chcesz skonfigurować model i zapewnić określony zbiór wartości jako argumenty.  
   
  
3. **Maksymalna liczba pozostawia na drzewie**: Wskazuje maksymalną liczbę węzłów końcowych (liści) utworzone w dowolnym drzewa.  

    Zwiększenie tej wartości, możesz potencjalnie zwiększyć rozmiar drzewa i uzyskać lepszą dokładności, ryzyko overfitting i już szkolenia czasu.  

4. **Minimalna liczba próbek na węzeł liścia**: Wskazać minimalną liczbę przypadków wymaganych do utworzenia dowolnego węzła w terminalu (liść) w drzewie.

    Zwiększenie tej wartości, można zwiększyć wartość progową do tworzenia nowych zasad. Na przykład wartość domyślną 1, jeden przypadek może spowodować nową regułę, która ma zostać utworzony. Jeśli zwiększysz wartość 5 dane szkoleniowe musi zawierać co najmniej 5 przypadki, które spełniają warunki tego samego.

5. **Nauka współczynnik**: Wpisz liczbę od 0 do 1, który definiuje rozmiar kroku podczas nauki. Tempo uczenia Określa, jak szybko lub wolno uczeń zbieżna dla wartości na optymalne rozwiązanie. Jeśli rozmiar kroku jest zbyt duży, może być przekroczeń optymalne rozwiązanie. Jeśli rozmiar kroku jest zbyt mały, szkolenie dłużej zbieżne najlepsze rozwiązanie.

6. **Liczba drzew skonstruowany**: Wskazywać sumę drzewa decyzyjne, które można utworzyć w zespole. Tworząc więcej drzewa decyzyjne, mogą potencjalnie uzyskać lepszego pokrycia, ale zwiększa czasu szkoleń.

    Ta wartość kontroluje również liczbę drzew wyświetlane, gdy wizualizacja uczonego modelu. Jeśli chcesz wyświetlić lub wydrukować drzewo Pojedyn, wartość można ustawić na 1; jednak jest generowany tylko jeden drzewa (drzewo za pomocą początkowego zestawu parametrów) i są wykonywane nie dalsze iteracji.

7. **Inicjator losowy numer**: Wpisz opcjonalny nieujemną liczbę całkowitą jako wartość losowa. Określając inicjator zapewnia odtwarzaniem we wszystkich przebiegach aktualizacji, które mają tych samych danych i parametry.

    Domyślnie Inicjator losowy jest równa 0, co oznacza, że wartości początkowej są uzyskiwane z zegara systemowego.
  
8. **Zezwalaj na nieznane poziomy podzielonych na kategorie**: Wybierz tę opcję, aby utworzyć grupę dla nieznanego wartości w zestawach szkolenia i sprawdzania poprawności. Jeśli wyłączysz tę opcję, model może zaakceptować tylko wartości, które są zawarte w danych szkoleniowych. Model może być mniej dokładny w przypadku znanych wartości, ale funkcja ta może zapewnić lepszą prognoz dotyczących nowych wartości (nieznany).

9. Dodaj zestaw danych szkoleniowych i jeden z modułów szkolenia:

    - Jeśli ustawisz **trybie trainer tworzenia** opcję **pojedynczy parametr**, użyj [Train Model](train-model.md) modułu.  
  
    

10. Uruchom eksperyment.  
  
### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić drzewa, który został utworzony w każdej iteracji, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](train-model.md) modułu, a następnie wybierz pozycję **Visualize**.
  
     Kliknij każdy drzewo, aby przejść do dzieli dane i wyświetlić reguły dla każdego węzła.  

+ Do korzystania z modelu do oceniania, połącz go [Score Model](./score-model.md)w celu prognozowania wartości dla nowe przykłady danych wejściowych.

+ Aby zapisać migawkę trenowanego modelu, kliknij prawym przyciskiem myszy **modelu Trained** dane wyjściowe z modułu szkolenia i wybierz pozycję **Zapisz jako**. Kopiuj trenowanego modelu, który można zapisać nie jest aktualizowany na kolejnych przebiegów eksperymentu.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 