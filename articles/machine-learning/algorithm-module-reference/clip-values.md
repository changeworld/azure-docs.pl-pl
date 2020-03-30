---
title: Obcinanie wartości
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Wartości clip w usłudze Azure Machine Learning do wykrywania wartości odstających i przycinania lub zastępowania ich wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456611"
---
# <a name="clip-values"></a>Obcinanie wartości

W tym artykule opisano moduł projektanta usługi Azure Machine Learning (wersja zapoznawcza).

Moduł Wartości clip służy do identyfikowania i opcjonalnie zastępowania wartości danych, które są powyżej lub poniżej określonego progu średnią, stałą lub inną wartością zastępczą.  

Należy podłączyć moduł do zestawu danych, który ma numery, które chcesz przyciąć, wybierz kolumny do pracy, a następnie ustaw próg lub zakres wartości i metodę zastępczą. Moduł może wyprowadzać tylko wyniki lub zmienione wartości dołączone do oryginalnego zestawu danych.

## <a name="how-to-configure-clip-values"></a>Jak skonfigurować wartości klipu

Przed rozpoczęciem zidentyfikuj kolumny, które chcesz przyciąć, oraz metodę użycia. Zaleca się przetestowanie dowolnej metody przycinania na małym podzbiorze danych.

Moduł stosuje te same kryteria i metodę zastępowania do **wszystkich** kolumn, które można uwzględnić w zaznaczeniu. W związku z tym należy wykluczyć kolumny, które nie chcesz zmieniać.

Jeśli chcesz zastosować metody przycinania lub różne kryteria do niektórych kolumn, należy użyć nowego wystąpienia **Clip Values** dla każdego zestawu podobnych kolumn.

1.  Dodaj moduł **Wartości clip** do potoku i połącz go z zestawem danych, który chcesz zmodyfikować. Ten moduł można znaleźć w obszarze **Transformacja danych**w kategorii **Skala i Zmniejszanie.** 
  
1.  W **polu Lista kolumn**użyj selektora kolumn, aby wybrać kolumny, do których będą stosowane wartości **klipu.**  
  
1.  W przypadku **zestawu progów**wybierz jedną z następujących opcji z listy rozwijanej. Te opcje określają sposób ustawiania górnych i dolnych granic dla wartości dopuszczalnych w porównaniu z wartościami, które muszą zostać przycięte.  
  
    - **ClipPeaks**: Podczas przycinania wartości według szczytów, należy określić tylko górną granicę. Wartości większe niż ta wartość graniczna są zastępowane.
  
    -  **ClipSubpeaks**: Podczas przycinania wartości według podpomnień określa się tylko dolną granicę. Wartości, które są mniejsze niż wartość graniczna są zastępowane.  
  
    - **ClipPeaksAndSubpeaks**: Podczas przycinania wartości według szczytów i podpomnień można określić zarówno górną, jak i dolną granicę. Wartości, które znajdują się poza tym zakresem są zastępowane. Wartości zgodne z wartościami granic nie są zmieniane.
  
1.  W zależności od wyboru w poprzednim kroku można ustawić następujące wartości progowe: 

    + **Niższy próg**: wyświetlany tylko w przypadku **wybrania opcji ClipSubPeaks**
    + **Górny próg**: wyświetlany tylko w przypadku **wybrania opcji ClipPeaks**
    + **Próg**: Wyświetlany tylko wtedy, gdy wybierzesz **ClipPeaksAndSubPeaks**

    Dla każdego typu progu wybierz opcję **Stała** lub **Percentyl**.

1. W przypadku **wybrania opcji Stała**wpisz w polu tekstowym wartość maksymalną lub minimalną. Załóżmy na przykład, że znasz wartość 999 została użyta jako wartość zastępcza. Można wybrać **opcję Stała** dla górnego progu i wpisz 999 w wartości **stałej dla górnego progu**.
  
1. W przypadku wybrania **opcji Percentyl**należy ograniczyć wartości kolumn do zakresu percentyla. 

    Załóżmy na przykład, że chcesz zachować tylko wartości w zakresie 10-80 percentyla i zastąpić wszystkie inne. Należy wybrać **percentyl**, a następnie wpisz 10 dla **wartości percentyla dla dolnego progu**i wpisz 80 dla wartości **percentyla dla górnego progu**. 

    Zobacz sekcję na [percentyle,](#examples-for-clipping-using-percentiles) aby uzyskać kilka przykładów używania zakresów percentyla.  
  
1.  Zdefiniuj wartość zastępczą.

    Liczby, które dokładnie odpowiadają określonym granicom, są uważane za znajdujące się wewnątrz dozwolonego zakresu wartości i w związku z tym nie są zastępowane. Wszystkie liczby, które nie mieszczą się w określonym zakresie, są zastępowane wartością zastępczą. 
  
    + **Wartość zastępcza dla pików**: Definiuje wartość, która ma zastąpić wszystkie wartości kolumn, które są większe niż określony próg.  
    + **Wartość zastępcza dla podpowień**: Definiuje wartość, która ma być używana jako substytut dla wszystkich wartości kolumn, które są mniejsze niż określony próg.  
    + Jeśli używasz **ClipPeaksAndSubpeaks** opcji, można określić oddzielne wartości zastępcze dla górnej i dolnej wartości przycięte.  

    Obsługiwane są następujące wartości zastępcze:  
  
    -   **Próg**: Zastępuje przycięte wartości określoną wartością progową.  
  
    -   **Średnia:** Zastępuje przycięte wartości średnią wartości kolumn. Średnia jest obliczane przed przycięciem wartości.  
  
    -   **Mediana**: Zastępuje przycięte wartości medianą wartości kolumn. Mediana jest obliczany przed przycięciem wartości.   
  
    -   **Brak**. Zastępuje przycięte wartości brakującą (pustą) wartością.  
  
1.  **Dodaj kolumny wskaźnika:** Wybierz tę opcję, jeśli chcesz wygenerować nową kolumnę, która informuje, czy określona operacja przycinania zastosowana do danych w tym wierszu. Ta opcja jest przydatna podczas testowania nowego zestawu wartości przycinania i podstawiania.  
  
1. **Zastąp flagę:** Wskazuje, jak mają być generowane nowe wartości. Domyślnie **wartość przycięcia** tworzy nową kolumnę z wartościami szczytowymi przyciętymi do żądanego progu. Nowe wartości zastępują oryginalną kolumnę.  
  
    Aby zachować oryginalną kolumnę i dodać nową kolumnę z przyciętymi wartościami, usuń zaznaczenie tej opcji.  
  
1.  Prześlij potok.  
  
    Kliknij prawym przyciskiem myszy moduł **Clip Values** i wybierz polecenie **Wizualizuj** lub wybierz moduł i **przełącz** się na kartę Wyjścia w prawym panelu, kliknij ikonę histogramu w **wyjściach portów,** aby przejrzeć wartości i upewnić się, że operacja przycinania spełniła Twoje oczekiwania.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Przykłady przycinania przy użyciu percentyli

Aby zrozumieć, jak działa przycinanie przez percentyle, należy wziąć pod uwagę zestaw danych z 10 wierszy, które mają jedno wystąpienie każdej wartości 1-10.  
  
- Jeśli procent jest używany jako górny próg, w wartości 90 percentyla, 90 procent wszystkich wartości w zestawie danych musi być mniejsza niż ta wartość.  
  
- Jeśli procent jest używany jako dolny próg, w wartości 10 percentyla, 10 procent wszystkich wartości w zestawie danych musi być mniejsza niż ta wartość.  
  
1.  W przypadku **zestawu progów**wybierz pozycję **ClipPeaksAndSubPeaks**.  
  
1.  W przypadku **górnego progu**wybierz pozycję **Percentyl**, a dla **liczby percentyla**wpisz 90.  
  
1.  W polu **Górna wartość zastępcza**wybierz pozycję **Braku wartości**.  
  
1.  W przypadku **dolnego progu**wybierz **percentyl**, a dla **liczby percentyla**wpisz 10.  
  
1.  W **polu Niższa wartość zastępcza**wybierz pozycję **Braku wartości**.  
  
1.  Usuń zaznaczenie opcji **Zastąpienie flagi**i wybierz opcję **Dodaj kolumnę wskaźnika**.  
  
Teraz spróbuj tego samego potoku przy użyciu 60 jako górny próg percentyla i 30 jako dolny próg percentyla i użyj wartości progowej jako wartości zastępczej. W poniższej tabeli porównano te dwa wyniki:  
  
1.  Wymień na brak; Górny próg = 90; Niższy próg = 20  
  
1.  Zastąp na próg; Górny percentyl = 60; Niższy percentyl = 40  
  
|Oryginalne dane|Zamień na brakujące|Zamień na próg|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|Prawda<br /><br /> Prawda<br /><br /> 3, FAŁSZ<br /><br /> 4, FAŁSZ<br /><br /> 5, FAŁSZ<br /><br /> 6, FAŁSZ<br /><br /> 7, FAŁSZ<br /><br /> 8, FAŁSZ<br /><br /> 9, FAŁSZ<br /><br /> Prawda|4, PRAWDA<br /><br /> 4, PRAWDA<br /><br /> 4, PRAWDA<br /><br /> 4, PRAWDA<br /><br /> 5, FAŁSZ<br /><br /> 6, FAŁSZ<br /><br /> 7, PRAWDA<br /><br /> 7, PRAWDA<br /><br /> 7, PRAWDA<br /><br /> 7, PRAWDA| 
 
## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
