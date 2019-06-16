---
title: 'Czyszczenie brakujących danych: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu Clean Missing Data w usłudze Azure Machine Learning w celu usuwanie, zastępowanie lub wywnioskować brakujące wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028788"
---
# <a name="clean-missing-data-module"></a>Clean Missing Data modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do usuwanie, zastępowanie lub wywnioskować brakujące wartości. 

Analitycy danych często sprawdza dane dla brakujących wartości i wykonywać różne operacje, aby określić dane, lub Wstaw nowe wartości. Celem takiego działania mające jest zapobieganie problemom spowodowanym brakujące dane, które mogą wystąpić podczas uczenia modelu. 

Ten moduł obsługuje wiele typów operacji "czyszczenie" brakujące wartości, w tym:

+ Zastępowanie brakujące wartości przy użyciu symbolu zastępczego, średnia lub inne wartości
+ Całkowite usuwanie wierszy i kolumn, które nie mają wartości
+ Wnioskowanie wartości w oparciu o metody statystyczne


Za pomocą tego modułu nie zmienia źródłowego zestawu danych. Zamiast tego tworzy nowy zestaw danych, w obszarze roboczym używanego w kolejnych przepływu pracy. Można również zapisać nowe, oczyszczony zestaw danych do ponownego wykorzystania.

Ten moduł generuje również definicji transformacji używane do czyszczenia brakujące wartości. Można ponownie użyć tej transformacji, w innych zestawach danych, które mają ten sam schemat, za pomocą [Zastosuj przekształcenie](./apply-transformation.md) modułu.  

## <a name="how-to-use-clean-missing-data"></a>Jak używać Clean Missing Data

Ten moduł pozwala zdefiniować operację czyszczenia. Można także zapisać operacji czyszczenia, tak, aby stosować go później do nowych danych. Skorzystaj z następujących linków, aby uzyskać opis sposobu utworzyć i zapisać proces czyszczenia: 
 
+ Aby zastąpić brakujące wartości
  
+ Aby zastosować przekształcenie czyszczenia do nowych danych
 
> [!IMPORTANT]
> Metoda czyszczenia, używanej do obsługi brakujące wartości znacznie może mieć wpływ na wyniki. Zalecamy wypróbowanie dostosowań za pomocą różnych metod. Należy wziąć pod uwagę zarówno uzasadnienie użyj konkretnej metody i jakość wyników.

### <a name="replace-missing-values"></a>Zastąp wartości Brak  

Za każdym razem, należy zastosować [Clean Missing Data](./clean-missing-data.md) modułu do zestawu danych, tę samą operację czyszczenia jest stosowany do wszystkich kolumn, które można wybrać. W związku z tym wyczyścić różnych kolumn przy użyciu różnych metod, należy użyć osobnych wystąpień modułu.

1.  Dodaj [Clean Missing Data](./clean-missing-data.md) modułu do eksperymentu i Połącz z zestawu danych, który ma brakujące wartości.  
  
2.  Aby uzyskać **kolumn należy wyczyścić**, wybierz kolumny, które zawierają brakujące wartości, które chcesz zmienić. Możesz wybrać wiele kolumn, ale należy użyć tej samej metody zastąpienie wszystkich zaznaczonych kolumn. W związku z tym zazwyczaj należy wyczyścić kolumny ciągów i kolumnach liczbowych oddzielnie.

    Na przykład, aby sprawdzić, czy brakuje wartości we wszystkich kolumnach liczbowych:

    1. Otwórz selektor kolumn, a następnie wybierz pozycję **przy użyciu reguł**.
    2. Dla **rozpocząć z**, wybierz opcję **kolumny nr**.

        Można również uruchomić ze wszystkimi KOLUMNAMI, a następnie Wyklucz kolumny. Początkowo reguły nie są wyświetlane pierwsze kliknięcie **wszystkie kolumny**, ale możesz kliknąć pozycję **kolumny nr** a następnie kliknij przycisk **wszystkie kolumny** ponownie, aby uruchomić ze wszystkimi kolumnami, a następnie przeprowadź filtrowanie limit kolumn (wyklucz) na podstawie nazwy, typ danych, lub indeks kolumny.

    3. Dla **Include**, wybierz opcję **typ kolumny** z listy rozwijanej, a następnie wybierz **liczbowych**, lub bardziej specyficznego typu liczbowego. 
  
    Wszelkie czyszczenia lub wymiany wybranej metody muszą mieć zastosowanie do **wszystkich** kolumn w zaznaczeniu. Jeśli dane w dowolnej kolumnie nie jest zgodna z określoną operacją, moduł zwraca błąd i zatrzymuje eksperymentu.
  
3.  Aby uzyskać **Brak stosunek wartości co najmniej**, określ minimalną liczbę brakujących wartości wymaganych dla operacji do wykonania.  
  
    Użyj tej opcji w połączeniu z **maksymalny stosunek wartości Brak** do definiowania warunków, w jakich operacji czyszczenia odbywa się w zestawie danych. W przypadku zbyt wiele lub zbyt małej liczby wierszy, których brakuje wartości nie można wykonać operacji. 
  
    Liczba, która reprezentuje **współczynnik** brakujących wartości do wszystkich wartości w kolumnie. Domyślnie **Minimum brakujące wartości współczynnika** właściwość jest ustawiona na 0. Oznacza to, że brakuje wartości zostały wyczyszczone, nawet jeśli dostępny jest tylko jedna wartość Brak. 

    > [!WARNING]
    > Ten warunek, muszą być spełnione przez każdej kolumny w kolejności dla określonej operacji zastosować. Załóżmy na przykład wybrano trzy kolumny, a następnie ustaw minimalny współczynnik brakujące wartości od.2 (20%), ale tylko jedna kolumna została rzeczywiście brakujące wartości 20%. W takim przypadku operacja czyszczenia czy dotyczą tylko kolumny z ponad 20% brakuje wartości. W związku z tym inne kolumny będzie bez zmian.
    > 
    > Jeśli masz jakiekolwiek wątpliwości dotyczące tego, czy zostały zmienione brakujące wartości, wybierz opcję, **Generowanie brakującej kolumnie Wskaźnik wartości**. Kolumna jest dołączany do zestawu danych, aby wskazać, czy każda kolumna spełnione określone kryteria dla zakresów minimalną i maksymalną.  
  
4. Aby uzyskać **maksymalny stosunek wartości Brak**, określ maksymalną liczbę brakujących wartości, które mogą być obecne na można wykonać operacji.   
  
    Na przykład możesz chcieć wykonać podstawiania wartości Brak, tylko wtedy, gdy 30% lub mniej wierszy zawierają brakujące wartości, ale pozostaw wartości jako — jest przypadek, więcej niż 30% wierszy brakuje wartości.  
  
    Możesz zdefiniować liczbę jako stosunek brakujące wartości, aby wszystkie wartości w kolumnie. Domyślnie **maksymalny stosunek wartości Brak** jest ustawiona na 1. Oznacza to, że brakuje wartości zostały wyczyszczone, nawet jeśli brakuje 100% wartości w kolumnie.  
  
   
  
5. Aby uzyskać **tryb czyszczenia**, wybierz jedną z następujących opcji dla zastępowanie lub usuwanie brakujących wartości:  
  
  
    + **Wartości niestandardowe podstawienia**: Użyj tej opcji, aby określić wartość symbolu zastępczego (np. wartość 0 lub brak), która ma zastosowanie do wszystkich brakujących wartości. Wartość można określić jako zamiennika musi być zgodny z typem danych kolumny.
  
    + **Zamień na średnią**: Oblicza średnią kolumny i wykorzystuje średnią jako wartość zastąpienia dla każdej brakujących wartości w kolumnie.  
  
        Ma zastosowanie tylko do kolumn zawierających liczby całkowitej, Double lub typy danych logicznych.  
  
    + **Zamień mediana**: Oblicza wartość mediany kolumny i wykorzystuje wartość mediany zastępuje wszelkie brakujące wartości w kolumnie.  
  
        Ma zastosowanie tylko do kolumn zawierających liczby całkowitej lub podwójnej precyzji typów danych. 
  
    + **Zamień na tryb**: Oblicza tryb kolumny i korzysta z trybu jako wartość zastąpienia dla każdej brakujących wartości w kolumnie.  
  
        Stosuje się do kolumn, które mają typ danych Integer, Double, Boolean lub podzielone. 
  
    + **Usuń cały wiersz**: Całkowicie usunie wszystkie wiersze w zestawie danych, który ma co najmniej jedną wartość Brak. Jest to przydatne, jeśli brak wartości mogą być uważane za losowo Brak.  
  
    + **Usuń całą kolumnę**: Powoduje całkowite usunięcie dowolnej kolumny w zestawie danych, który ma co najmniej jedną wartość Brak.  
  
    
  
6. Opcja **wartość zastąpienia** jest dostępna w przypadku wybrania opcji **wartość podstawienia niestandardowego**. Wpisz nową wartość do użycia jako wartość zastąpienia dla wszystkich brakujących wartości w kolumnie.  
  
    Należy pamiętać, że można użyć tej opcji tylko w kolumnach, które mają typ danych Integer, Double, atrybut typu wartość logiczna lub data. Dla kolumn daty na wartość zastępczą można również wprowadzić jako liczbę 100-nanosekundowych znaczników od 1/1/0001 12:00:00  
  
7. **Generowanie brakującej kolumnie Wskaźnik wartości**: Wybierz tę opcję, aby dane wyjściowe niektóre wskazania tego, czy wartości w kolumnie spełnione kryteria dla wartości Brak czyszczenia. Ta opcja jest szczególnie przydatne podczas konfigurowania nowej operacji czyszczenia i upewnić się, że działa zgodnie z założeniami.
  
8. Uruchom eksperyment.

### <a name="results"></a>Wyniki

Moduł zwraca dane wyjściowe dwa:  

-   **Oczyszczony zestaw danych**: Zestaw składa się z wybranych kolumn, z brakującymi wartościami obsługiwane w określony sposób, wraz z kolumną wskaźnika w przypadku wybrania tej opcji.  

    Nie wybrano czyszczenia kolumn są również "przekazane".  
  
-  **Czyszczenie przekształcania**: Przekształcanie danych używane do czyszczenia, które mogą być zapisywane w obszarze roboczym i stosowane do nowych danych później.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Zastosuj zapisanej operacji czyszczenia do nowych danych  

Jeśli potrzebujesz często powtarzania operacji czyszczenia, zaleca się zapisywania swoje przepis na czyszczenia danych jako *Przekształcanie*, aby ponownie użyć tego samego zestawu danych. Zapisywanie czyszczenia transformacji jest szczególnie przydatne, jeśli należy ponownie zaimportować często, a następnie wyczyszczenie danych, który ma ten sam schemat.  
      
1.  Dodaj [Zastosuj przekształcenie](./apply-transformation.md) modułu do eksperymentu.  
  
2.  Dodaj zestaw danych, które chcesz wyczyścić, a następnie nawiązać połączenie po prawej stronie portu wejściowego zestawu danych.  
  
3.  Rozwiń **przekształca** grupy w okienku po lewej stronie interfejsu. Znajdź zapisany transformacji i przeciągnij je do eksperymentu.  
  
4.  Zapisane przekształcania nawiązać połączenie z lewym portem wejściowym [Zastosuj przekształcenie](./apply-transformation.md). 

    Po zastosowaniu zapisane transformacji nie można wybrać kolumny, do których są stosowane transformacji. Wynika to z transformacji został już zdefiniowany i automatycznie stosuje się do kolumny określone w operacji pierwotnej.

    Jednak Załóżmy, że utworzono transformacji dla podzestawu kolumn liczbowych. Ta transformacja można zastosować do zestaw kolumn mieszane typy bez zgłaszania błędu, ponieważ brakujące wartości zostaną zmienione tylko w pasujących kolumn liczbowych.

6.  Uruchom eksperyment.  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 