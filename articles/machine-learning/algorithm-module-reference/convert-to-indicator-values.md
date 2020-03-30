---
title: Konwertowanie na wartości wskaźnika
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Konwertuj na wartości wskaźnikowe w usłudze Azure Machine Learning, aby przekonwertować kolumny zawierające wartości kategorii na serię kolumn wskaźników binarnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477667"
---
# <a name="convert-to-indicator-values"></a>Konwertowanie na wartości wskaźnika
W tym artykule opisano moduł projektanta usługi Azure Machine Learning.

Użyj **konwersji do wartości wskaźnika** modułu w projektancie usługi Azure Machine Learning do konwersji kolumn, które zawierają wartości kategorii do serii kolumn wskaźników binarnych.  

Moduł ten wyprowadza również definicję transformacji używanej do konwersji na wartości wskaźnika. Transformację można ponownie użyć w innych zestawach danych, które mają ten sam schemat, za pomocą modułu [Zastosuj transformację.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Jak skonfigurować konwersję na wartości wskaźnikowe

1.  Znajdź **wartość Konwertuj na wskaźniki** i przeciągnij ją do wersji roboczej potoku. Ten moduł można znaleźć w kategorii **Transformacja danych.**
    > [!NOTE]
    > Moduł Edytuj [metadane](edit-metadata.md) można użyć przed modułem **Konwertuj na wartości indiciator, aby oznaczyć** kolumny docelowe jako kategoryczne.

1. Podłącz moduł **Konwertuj na wartości wskaźnika** do zestawu danych zawierającego kolumny, które chcesz przekonwertować. 

1. Wybierz **pozycję Edytuj kolumnę,** aby wybrać jedną lub więcej kolumn kategorii.

1. Wybierz opcję **Zastąpienie kolumn kategorii,** jeśli chcesz wyprowadzić **tylko** nowe kolumny logiczne. Domyślnie ta opcja jest wyłączona.
    

    > [!TIP]
    >  Jeśli wybierzesz opcję zastępowanie, kolumna źródłowcza nie zostanie faktycznie usunięta ani zmodyfikowana. Zamiast tego nowe kolumny są generowane i prezentowane w zestawie danych wyjściowych, a kolumna źródłowa pozostaje dostępna w obszarze roboczym. Jeśli chcesz wyświetlić oryginalne dane, możesz użyć modułu Dodaj kolumny w dowolnym [momencie,](add-columns.md) aby dodać kolumnę źródłą z powrotem.

1. Prześlij potok.

## <a name="results"></a>Wyniki

Załóżmy, że masz kolumnę z wynikami, które wskazują, czy serwer ma wysokie, średnie lub niskie prawdopodobieństwo awarii.  

| Identyfikator serwera | Wynik błędu |
| --------- | ------------- |
| 10301     | Małe           |
| 10302     | Medium        |
| 10303     | Wysoka          |

Po **zastosowaniu konwersji do wartości wskaźnika**projektant konwertuje pojedynczą kolumnę etykiet na wiele kolumn zawierających wartości logiczne:  

| Identyfikator serwera | Wynik błędu - Niski | Wynik błędu - Średni | Wynik błędu - Wysoki |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Oto jak działa konwersja:  

-   W **kolumnie Wynik niepowodzenie,** która opisuje ryzyko, istnieją tylko trzy możliwe wartości (Wysoki, Średni i Niski) i nie brakuje wartości. Tak więc, dokładnie trzy nowe kolumny są tworzone.  

-   Nowe kolumny wskaźników są nazwane na podstawie nagłówków kolumn i wartości kolumny źródłowej, przy użyciu tego wzorca: * \<kolumna źródłona> - \<wartość danych>*.  

-   Powinno być 1 w dokładnie jednej kolumnie wskaźnika i 0 we wszystkich innych kolumnach wskaźników, ponieważ każdy serwer może mieć tylko jedną ocenę ryzyka.  

Teraz można użyć trzech kolumn wskaźnika jako funkcji w modelu uczenia maszynowego.

Moduł zwraca dwa wyjścia:

- **Zestaw danych wyników**: Zestaw danych z przekonwertowanymi kolumnami wartości wskaźników. Kolumny niewybrane do czyszczenia są również "przekazywane".
- **Transformacja wartości wskaźnika:** transformacja danych używana do konwersji na wartości wskaźnikowe, które można zapisać w obszarze roboczym i zastosować do nowych danych później.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Stosowanie operacji zapisanych wartości wskaźnika do nowych danych

Jeśli trzeba często powtarzać operacje wartości wskaźnika, można zapisać kroki manipulowania danymi jako *przekształcenie,* aby ponownie użyć go z tym samym zestawem danych. Jest to przydatne, jeśli często należy ponownieimportować, a następnie wyczyścić dane, które mają ten sam schemat.

1. Dodaj [zastosuj transformację](apply-transformation.md) modułu do potoku.

1. Dodaj zestaw danych, który chcesz wyczyścić, i połącz zestaw danych z portem wejściowym po prawej stronie.

1. Rozwiń grupę **Transformacja danych** w lewym okienku projektanta. Znajdź zapisaną transformację i przeciągnij ją do potoku.

1. Połącz zapisaną transformację z lewym portem wejściowym [Zastosuj transformację](apply-transformation.md).

   Po zastosowaniu zapisanej transformacji nie można wybrać kolumn, które mają być przekształcone. Jest tak, ponieważ transformacja została zdefiniowana i stosuje się automatycznie do typów danych określonych w oryginalnej operacji.

1. Prześlij potok.
 
## <a name="technical-notes"></a>Uwagi techniczne  

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="usage-tips"></a>Wskazówki dotyczące użytkowania

-   Tylko kolumny oznaczone jako kategoryczne można przekonwertować na kolumny wskaźnikowe. Jeśli zostanie wyświetlony następujący błąd, prawdopodobnie jedna z wybranych kolumn nie jest kategoryczna:  

     Błąd 0056: Kolumna z nazwą kolumny> \<nie należy do dozwolonej kategorii.  

     Domyślnie większość kolumn ciągów jest traktowana jako operacje ciągu, więc należy je wyraźnie oznaczyć jako kategoryczne przy użyciu [funkcji Edytuj metadane](edit-metadata.md).  

-   Nie ma limitu liczby kolumn, które można przekonwertować na kolumny wskaźnikowe. Jednak ponieważ każda kolumna wartości może przynieść wiele kolumn wskaźnika, można przekonwertować i przejrzeć tylko kilka kolumn naraz.  

-   Jeśli kolumna zawiera brakujące wartości, dla brakującej kategorii zostanie utworzona osobna kolumna wskaźnika o tej nazwie: * \<kolumna źródłona>- Brak*  

-   Jeśli kolumna konwertowana na wartości wskaźnikowe zawiera liczby, muszą one być oznaczone jako kategoryczne, jak każda inna kolumna obiektowa. Po wykonaniu tej sprawy liczby są traktowane jako wartości dyskretne. Na przykład, jeśli masz kolumnę numeryczną z wartościami MPG od 25 do 30, dla każdej dyskretnej wartości zostanie utworzona nowa kolumna wskaźnika:  

    | Marka       | Autostrada mpg -25 | Autostrada mpg -26 | Autostrada mpg -27 | Autostrada mpg -28 | Autostrada mpg -29 | Autostrada mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Samochody Contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Aby uniknąć dodawania zbyt wielu wymiarów do zestawu danych. Zaleca się, aby najpierw sprawdzić liczbę wartości w kolumnie i bin lub ilościowo danych odpowiednio.  


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
