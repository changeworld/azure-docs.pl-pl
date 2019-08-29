---
title: 'Wprowadź dane ręcznie: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu wprowadzanie danych ręcznie w usłudze Azure Machine Learning, aby utworzyć mały zestaw danych, wpisując wartości. Zestaw danych może mieć wiele kolumn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d7e6cede12f5a348f59db83b31b19c89266dfdf7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128842"
---
# <a name="enter-data-manually-module"></a>Wprowadź ręcznie moduł danych

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia małego zestawu danych przez wpisanie wartości. Zestaw danych może mieć wiele kolumn.
  
Ten moduł może być przydatny w następujących scenariuszach:  
  
- Generowanie małego zestawu wartości do testowania  
  
- Tworzenie krótkiej listy etykiet
  
- Wpisywanie listy nazw kolumn do wstawienia w zestawie danych

## <a name="enter-data-manually"></a>Ręczne wprowadzanie danych 
  
1.  Dodaj moduł [Wprowadź dane ręcznie](./enter-data-manually.md) do swojego eksperymentu. Ten moduł można znaleźć w kategorii dane **wejściowe i wyjściowe** w Azure Machine Learning. 
  
2.  W polu **Format**danych wybierz jedną z następujących opcji. Te opcje określają sposób, w jaki podane dane powinny być analizowane. Wymagania dotyczące każdego formatu różnią się znacznie, dlatego pamiętaj, aby przeczytać tematy pokrewne.  
  
    -   **ARFF**. Format pliku relacji atrybutu używany przez Weka.   
  
    -   **WOLUMIN CSV**. Format wartości rozdzielanych przecinkami. Aby uzyskać więcej informacji, zobacz [konwertowanie do formatu CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Format używany przez Vowpal Wabbit i inne platformy uczenia maszynowego.  
  
    -   **TSV**. Format wartości rozdzielanych znakami tabulacji.

     Jeśli wybierzesz format i nie podasz danych, które spełniają wymagania formatu, wystąpi błąd w czasie wykonywania.
  
3.  Kliknij wewnątrz pola tekstowego **dane** , aby rozpocząć wprowadzanie danych. Następujące formaty wymagają szczególnej uwagi:  
  
    - **WOLUMIN CSV**:  Aby utworzyć wiele kolumn, Wklej w tekście rozdzielanym przecinkami lub wpisz wiele kolumn, używając przecinków między polami.
  
        W przypadku wybrania opcji **HasHeader** można użyć pierwszego wiersza wartości jako nagłówka kolumny.  
  
        Jeśli zaznaczysz tę opcję, używane są nazwy kolumn, Kol1, Col2 i tak dalej. Nazwy kolumn można dodawać lub zmieniać później za pomocą polecenia [Edytuj metadane](./edit-metadata.md).  
  
    - **TSV**: Aby utworzyć wiele kolumn, Wklej w tekście rozdzielanym tabulatorami lub wpisz wiele kolumn, używając kart między polami.  
  
        W przypadku wybrania opcji **HasHeader** można użyć pierwszego wiersza wartości jako nagłówka kolumny.  
  
        Jeśli zaznaczysz tę opcję, używane są nazwy kolumn, Kol1, Col2 i tak dalej. Nazwy kolumn można dodawać lub zmieniać później za pomocą polecenia [Edytuj metadane](./edit-metadata.md).  
  
    -   **ARFF**:  Wklej w istniejącym pliku formatu ARFF. W przypadku wpisywania wartości bezpośrednio należy pamiętać o dodaniu opcjonalnego nagłówka i wymaganych pól atrybutów na początku danych. 
    
        Na przykład następujące wiersze nagłówka i atrybutu można dodać do prostej listy. Nagłówek `SampleText`kolumny.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Wpisz lub wklej wartość w wartościach przy użyciu formatu SVMLight.  
  
        Na przykład poniższy przykład reprezentuje pierwsze kilka wierszy zestawu danych darowizny krwi w formacie SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Po uruchomieniu modułu [wprowadzanie danych ręcznie](./enter-data-manually.md) te wiersze są konwertowane na zestaw danych kolumn i wartości indeksów w następujący sposób:  
  
        |Col1|Col2|Col3|Col4|Etykiety|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Naciśnij klawisz ENTER po każdym wierszu, aby rozpocząć nowy wiersz.  
  
     **Pamiętaj, aby nacisnąć klawisz ENTER po ostatnim wierszu.** 
     
     Po naciśnięciu klawisza ENTER wiele razy, aby dodać wiele pustych wierszy końcowych, ostatni pusty wiersz zostanie usunięty przycięty, ale inne puste wiersze są traktowane jako brakujące wartości.  
  
     W przypadku tworzenia wierszy z brakującymi wartościami można zawsze odfiltrować je w późniejszym czasie.  
  
5.  Kliknij prawym przyciskiem myszy moduł i wybierz pozycję **Uruchom wybrane** , aby przeanalizować dane i załadować je do obszaru roboczego jako zestaw danych.  
  
     Aby wyświetlić zestaw danych, kliknij port wyjściowy i wybierz polecenie **Wizualizuj**.  
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 