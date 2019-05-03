---
title: 'Ręcznie wprowadź dane jednostki: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu ręcznie wprowadź dane w usłudze Azure Machine Learning w celu utworzenia małego zestawu danych, wpisując wartości. Zestaw danych może mieć wiele kolumn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028608"
---
# <a name="enter-data-manually-module"></a>Wprowadź dane ręcznie modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia małego zestawu danych, wpisując wartości. Zestaw danych może mieć wiele kolumn.
  
Ten moduł może być przydatne w scenariuszach takich jak te:  
  
- Generowanie niewielki zestaw wartości do testowania  
  
- Tworzenie krótką listę etykiet
  
- Wpisując listę nazw kolumn do wstawienia w zestawie danych

## <a name="enter-data-manually"></a>Ręcznie wprowadź dane jednostki 
  
1.  Dodaj [ręcznie wprowadź dane](./enter-data-manually.md) modułu do eksperymentu. Możesz znaleźć tego modułu w **danych wejściowych i wyjściowych** kategorii w usłudze Azure Machine Learning. 
  
2.  Aby uzyskać **DataFormat**, wybierz jedną z następujących opcji. Te opcje określają, jak można przeanalizować dane, które należy podać. Wymagania dotyczące poszczególnych formatów się znacznie różnić, dlatego należy przeczytać tematy pokrewne.  
  
    -   **ARFF**. Relacja atrybutu format pliku, używane przez Weka.   
  
    -   **CSV**. Format wartości rozdzielanych przecinkami. Aby uzyskać więcej informacji, zobacz [Konwertuj do formatu CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Format używany przez Vowpal Wabbit i innych platform uczenia maszynowego.  
  
    -   **TSV**. Format wartości rozdzielane znakami tabulacji.

     Jeśli wybierz format i nie zostaną określone dane, które spełniają specyfikacji formatu, wystąpi błąd czasu wykonywania.
  
3.  Kliknij wewnątrz **danych** pole tekstowe, aby rozpocząć wprowadzanie danych. Następujące formaty wymagają specjalnej uwagi:  
  
    - **CSV**:  Aby utworzyć wiele kolumn, wklej tekst rozdzielany przecinkami lub wpisz wiele kolumn, rozdzielając między polami.
  
        Jeśli wybierzesz **HasHeader** opcji pierwszego wiersza wartości można użyć jako nagłówek kolumny.  
  
        Jeśli wyłączysz tę opcję, nazw kolumn, Col1, Col2 i tak dalej, są używane. Można dodać lub zmienić kolumny nazwy później za pomocą [edytować metadane](./edit-metadata.md).  
  
    - **TSV**: Aby utworzyć wiele kolumn, Wklej znakami tabulacji tekst lub wpisz wiele kolumn przy użyciu kart między polami.  
  
        Jeśli wybierzesz **HasHeader** opcji pierwszego wiersza wartości można użyć jako nagłówek kolumny.  
  
        Jeśli wyłączysz tę opcję, nazw kolumn, Col1, Col2 i tak dalej, są używane. Można dodać lub zmienić kolumny nazwy później za pomocą [edytować metadane](./edit-metadata.md).  
  
    -   **ARFF**:  Wklej istniejący plik formatu ARFF. Wpisywania wartości bezpośrednio, należy dodać opcjonalne nagłówki i pola wymaganego atrybutu na początku danych. 
    
        Na przykład następujący wiersz nagłówka i atrybutu można dodać do prostej listy. Nagłówek kolumny będą `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Wpisz lub Wklej w formacie SVMLight wartości.  
  
        Na przykład poniższy przykład przedstawia pierwszych wierszy kilka darowizn krwi zestawu danych, a w formacie SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Po uruchomieniu [ręcznie wprowadź dane](./enter-data-manually.md) modułu, te wiersze są konwertowane na zestaw kolumn i indeks wartości w następujący sposób:  
  
        |Col1|Col2|Col3|Col4|Etykiety|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Naciśnij klawisz ENTER po każdym wierszu, aby rozpocząć nowy wiersz.  
  
     **Należy koniecznie naciśnij klawisz ENTER po ostatnim wierszu.** 
     
     Jeśli użytkownik naciśnie klawisz ENTER wiele razy, aby dodać wiele pusty końcowe wiersze, ostatnim pusty wiersz jest usuwany przycięty, ale inne puste wiersze są traktowane jako brakujące wartości.  
  
     Jeśli tworzysz wiersze z brakującymi wartościami, można zawsze odfiltrować je później.  
  
5.  Kliknij prawym przyciskiem myszy moduł, a następnie wybierz pozycję **Uruchom wybrane** do analizowania danych i ładuje je do obszaru roboczego jako zestaw danych.  
  
     Aby wyświetlić zestaw danych, kliknij port wyjściowy i wybierz **Visualize**.  
## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 