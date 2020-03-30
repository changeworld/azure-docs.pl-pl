---
title: 'Czyste brakujące dane: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak usunąć, zastąpić lub wywnioskować brakujące wartości za pomocą modułu Czyste brakujące dane w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 14c3bc968da9d398fbc14eda74378047cf28277b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477701"
---
# <a name="clean-missing-data-module"></a>Moduł Clean Missing Data

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do usuwania, zastępowania lub wnioskowania brakujących wartości. 

Analitycy danych często sprawdzają dane pod kątem brakujących wartości, a następnie wykonują różne operacje, aby naprawić dane lub wstawić nowe wartości. Celem takich operacji czyszczenia jest zapobieganie problemom spowodowanym przez brakujące dane, które mogą pojawić się podczas szkolenia modelu. 

Ten moduł obsługuje wiele typów operacji dla "czyszczenia" brakujących wartości, w tym:

+ Zastępowanie brakujących wartości symbolem zastępczym, średnią lub inną wartością
+ Całkowite usunięcie wierszy i kolumn z brakującymi wartościami
+ Wywnioskowanie wartości na podstawie metod statystycznych


Użycie tego modułu nie powoduje zmiany zestawu danych źródłowych. Zamiast tego tworzy nowy zestaw danych w obszarze roboczym, który można użyć w kolejnym przepływie pracy. Można również zapisać nowy, oczyszczony zestaw danych do ponownego użycia.

Ten moduł również wyprowadza definicję transformacji używane do czyszczenia brakujących wartości. Transformację można ponownie użyć w przypadku innych zestawów danych, które mają ten sam schemat, za pomocą modułu [Zastosuj transformację.](./apply-transformation.md)  

## <a name="how-to-use-clean-missing-data"></a>Jak korzystać z czystych brakujących danych

Ten moduł umożliwia zdefiniowanie operacji czyszczenia. Można również zapisać operację czyszczenia, aby można było zastosować ją później do nowych danych. Zobacz następujące sekcje dotyczące tworzenia i zapisywania procesu czyszczenia: 
 
+ [Aby zastąpić brakujące wartości](#replace-missing-values)
  
+ [Aby zastosować transformację czyszczenia do nowych danych](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Metoda czyszczenia używana do obsługi brakujących wartości może znacząco wpłynąć na wyniki. Zaleca się eksperymentowanie z różnymi metodami. Należy wziąć pod uwagę zarówno uzasadnienie stosowania określonej metody, jak i jakość wyników.

### <a name="replace-missing-values"></a>Zastępowanie brakujących wartości  

Za każdym razem, gdy zastosujesz moduł [Czyste brakujące dane](./clean-missing-data.md) do zestawu danych, ta sama operacja czyszczenia jest stosowana do wszystkich wybranych kolumn. W związku z tym jeśli trzeba wyczyścić różne kolumny przy użyciu różnych metod, należy użyć oddzielnych wystąpień modułu.

1.  Dodaj [moduł Czyste brakujące dane](./clean-missing-data.md) do potoku i połącz zestaw danych, w których brakuje wartości.  
  
2.  Aby **wyczyścić kolumny,** wybierz kolumny zawierające brakujące wartości, które chcesz zmienić. Można wybrać wiele kolumn, ale należy użyć tej samej metody zastępowania we wszystkich wybranych kolumnach. W związku z tym zazwyczaj należy wyczyścić kolumny ciągów i kolumny liczbowe oddzielnie.

    Na przykład, aby sprawdzić brakujące wartości we wszystkich kolumnach liczbowych:

    1. Wybierz moduł **Czyste brakujące dane** i kliknij **kolumnę Edytuj** w prawym panelu modułu.

    3. W polu **Dołącz**wybierz pozycję **Typy kolumn** z listy rozwijanej, a następnie wybierz pozycję **Numeryczne**. 
  
    Każda wybrana metoda czyszczenia lub wymiany musi mieć zastosowanie do **wszystkich** kolumn w zaznaczeniu. Jeśli dane w dowolnej kolumnie jest niezgodny z określoną operacją, moduł zwraca błąd i zatrzymuje potoku.
  
3.  W polu **Minimalny brakujący współczynnik wartości**określ minimalną liczbę brakujących wartości wymaganych do wykonania operacji.  
  
    Ta opcja jest używana w połączeniu z **maksymalnym brakującym współczynnikiem wartości,** aby zdefiniować warunki, w których operacja czyszczenia jest wykonywana w zestawie danych. Jeśli istnieje zbyt wiele lub zbyt mało wierszy, których brakuje wartości, nie można wykonać operacji. 
  
    Wprowadzona liczba reprezentuje **stosunek** brakujących wartości do wszystkich wartości w kolumnie. Domyślnie **minimalna wartość współczynnika braku** wartości jest ustawiona na 0. Oznacza to, że brakujące wartości są czyszczone, nawet jeśli brakuje tylko jednej wartości. 

    > [!WARNING]
    > Warunek ten musi być spełniony przez każdą kolumnę, aby określona operacja została zastosowana. Załóżmy na przykład, że wybrano trzy kolumny, a następnie ustawiono minimalny stosunek brakujących wartości do .2 (20%), ale tylko jedna kolumna ma 20% brakujących wartości. W takim przypadku operacja oczyszczania będzie stosowana tylko do kolumny z ponad 20% brakujących wartości. W związku z tym inne kolumny będą niezmienione.
    > 
    > Jeśli masz jakiekolwiek wątpliwości co do tego, czy brakujące wartości zostały zmienione, wybierz opcję, **Wygeneruj brakujące kolumny wskaźnika wartości**. Kolumna jest dołączana do zestawu danych, aby wskazać, czy każda kolumna spełnia określone kryteria dla minimalnych i maksymalnych zakresów.  
  
4. W polu **Maksymalny brakujący współczynnik wartości**należy określić maksymalną liczbę brakujących wartości, które mogą być obecne dla operacji, która ma zostać wykonana.   
  
    Na przykład można wykonać podstawianie brakujących wartości tylko wtedy, gdy 30% lub mniej wierszy zawiera brakujące wartości, ale pozostawić wartości jako — jest, jeśli więcej niż 30% wierszy ma brakujące wartości.  
  
    Liczbę definiuje się jako stosunek brakujących wartości do wszystkich wartości w kolumnie. Domyślnie **maksymalny brakujący stosunek wartości** jest ustawiony na 1. Oznacza to, że brakujące wartości są czyszczone, nawet jeśli brakuje 100% wartości w kolumnie.  
  
   
  
5. W **trybie czyszczenia**wybierz jedną z następujących opcji wymiany lub usunięcia brakujących wartości:  
  
  
    + **Niestandardowa wartość podstawienia:** Ta opcja służy do określania wartości zastępczej (takiej jak 0 lub NA), która ma zastosowanie do wszystkich brakujących wartości. Wartość określona jako zamiennik musi być zgodna z typem danych kolumny.
  
    + **Zamień na średnią:** Oblicza średnią kolumny i używa średniej jako wartości zastępczej dla każdej brakującej wartości w kolumnie.  
  
        Dotyczy tylko kolumn, które mają typy danych liczba całkowita, podwójna lub logiczna.  
  
    + **Zamień na medianę**: Oblicza wartość mediany kolumny i używa wartości mediany jako zamiennika brakującej wartości w kolumnie.  
  
        Dotyczy tylko kolumn, które mają typy danych liczba całkowita lub podwójne. 
  
    + **Zamień na tryb:** Oblicza tryb kolumny i używa trybu jako wartości zastępczej dla każdej brakującej wartości w kolumnie.  
  
        Dotyczy kolumn, które mają różne, podwójne, logiczne lub kategoryczne typy danych. 
  
    + **Usuń cały wiersz:** Całkowicie usuwa dowolny wiersz w zestawie danych, w którego brakuje jednej lub więcej wartości. Jest to przydatne, jeśli brakującą wartość można uznać za losowo brakuje.  
  
    + **Usuń całą kolumnę:** Całkowicie usuwa dowolną kolumnę w zestawie danych, w których brakuje jednej lub więcej wartości.  
  
    
  
6. Opcja **Wartość zastępcza** jest dostępna, jeśli wybrano opcję **Niestandardowa wartość podstawki**. Wpisz nową wartość, która ma być używana jako wartość zastępcza dla wszystkich brakujących wartości w kolumnie.  
  
    Należy zauważyć, że tej opcji można używać tylko w kolumnach, które mają liczbę całkowitą, podwójną, logiczną lub ciąg.
  
7. **Wygeneruj brakujące kolumny wskaźnika wartości:** Wybierz tę opcję, jeśli chcesz wygenerować kilka wskazówek, czy wartości w kolumnie spełniają kryteria czyszczenia brakujących wartości. Ta opcja jest szczególnie przydatna podczas konfigurowania nowej operacji czyszczenia i chcesz upewnić się, że działa zgodnie z projektem.
  
8. Prześlij potok.

### <a name="results"></a>Wyniki

Moduł zwraca dwa wyjścia:  

-   **Oczyszczony zestaw danych**: Zestaw danych składający się z wybranych kolumn, z brakującymi wartościami obsługiwanymi zgodnie z określoną, wraz z kolumną wskaźnika, jeśli wybrano tę opcję.  

    Kolumny niewybrane do czyszczenia są również "przekazywane".  
  
-  **Transformacja czyszczenia:** transformacja danych używana do czyszczenia, która może być zapisana w obszarze roboczym i później zastosowana do nowych danych.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Stosowanie zapisanej operacji czyszczenia do nowych danych  

Jeśli chcesz często powtarzać operacje czyszczenia, zalecamy zapisanie przepisu na oczyszczanie danych jako *przekształcenie,* aby ponownie użyć go z tym samym zestawem danych. Zapisywanie transformacji czyszczenia jest szczególnie przydatne, jeśli często należy ponownie zaimportować, a następnie wyczyścić dane, które mają ten sam schemat.  
      
1.  Dodaj [zastosuj transformację](./apply-transformation.md) modułu do potoku.  
  
2.  Dodaj zestaw danych, który chcesz wyczyścić, i połącz zestaw danych z portem wejściowym po prawej stronie.  
  
3.  Rozwiń grupę **Przekształcanie** w lewym okienku projektanta. Znajdź zapisaną transformację i przeciągnij ją do potoku.  

4.  Połącz zapisaną transformację z lewym portem wejściowym [Zastosuj transformację](./apply-transformation.md). 

    Po zastosowaniu zapisanej transformacji nie można wybrać kolumn, do których jest stosowana transformacja. Dzieje się tak, ponieważ transformacja została już zdefiniowana i automatycznie stosuje się do kolumn określonych w oryginalnej operacji.

    Załóżmy jednak, że transformacja została utworzona na podzbiorze kolumn liczbowych. Transformację można zastosować do zestawu danych typów kolumn mieszanych bez wywoływania błędu, ponieważ brakujące wartości są zmieniane tylko w pasujących kolumnach liczbowych.

6.  Prześlij potok.  

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 