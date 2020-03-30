---
title: 'Wprowadź dane ręcznie: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Wprowadź dane ręcznie w usłudze Azure Machine Learning, aby utworzyć mały zestaw danych, wpisując wartości. Zestaw danych może mieć wiele kolumn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367520"
---
# <a name="enter-data-manually-module"></a>Wprowadź dane Ręcznie moduł

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Moduł **Wprowadź dane ręcznie** umożliwia utworzenie małego zestawu danych przez wpisanie wartości. Zestaw danych może mieć wiele kolumn.
  
Ten moduł może być pomocny w scenariuszach, takich jak:  
  
- Generowanie niewielkiego zestawu wartości do testowania.  
- Tworzenie krótkiej listy etykiet.  
- Wpisywanie listy nazw kolumn do wstawienia do zestawu danych.

## <a name="create-a-dataset"></a>Tworzenie zestawu danych 
  
1. Dodaj moduł [Wprowadź dane ręcznie](./enter-data-manually.md) do potoku. Ten moduł można znaleźć w kategorii **Wprowadzanie danych i Dane wyjściowe** w usłudze Azure Machine Learning. 
  
1. W przypadku **danychFormat**wybierz jedną z następujących opcji. Te opcje określają sposób analizowania danych, które należy przeanalizować. Wymagania dla każdego formatu różnią się znacznie, więc należy przeczytać powiązane tematy.  
  
   - **ARFF**: Format pliku relacji atrybutów używany przez Weka.   
   - **CSV**: Format wartości rozdzielonych przecinkami. Aby uzyskać więcej informacji, zobacz [Konwertowanie na plik CSV](./convert-to-csv.md).    
   - **SVMLight**: Format używany przez Vowpal Wabbit i inne struktury uczenia maszynowego.    
   - **TSV**: Format wartości oddzielonych kartami.

   Jeśli wybierzesz format i nie podasz danych, które spełniają specyfikacje formatu, wystąpi błąd środowiska uruchomieniowego.
  
1. Kliknij wewnątrz pola tekstowego **Dane,** aby rozpocząć wprowadzanie danych. Następujące formaty wymagają szczególnej uwagi:  
  
   - **CSV**: Aby utworzyć wiele kolumn, wklej tekst oddzielony przecinkami lub wpisz wiele kolumn za pomocą przecinków między polami.
  
     Jeśli wybierzesz opcję **HasHeader,** możesz użyć pierwszego wiersza wartości jako nagłówka kolumny.  
  
     Jeśli usuniesz zaznaczenie tej opcji, zostaną użyte nazwy kolumn (Col1, Col2 itd.). Nazwy kolumn można później dodawać lub zmieniać, korzystając z [funkcji Edytuj metadane](./edit-metadata.md).  
  
   - **TSV:** Aby utworzyć wiele kolumn, wklej tekst oddzielony kartą lub wpisz wiele kolumn za pomocą kart między polami.  
  
     Jeśli wybierzesz opcję **HasHeader,** możesz użyć pierwszego wiersza wartości jako nagłówka kolumny.  
  
     Jeśli usuniesz zaznaczenie tej opcji, zostaną użyte nazwy kolumn (Col1, Col2 itd.). Nazwy kolumn można później dodawać lub zmieniać, korzystając z [funkcji Edytuj metadane](./edit-metadata.md).  
  
   - **ARFF**: Wklej w istniejącym pliku formatu ARFF. Jeśli wpisujesz wartości bezpośrednio, należy dodać opcjonalny nagłówek i wymagane pola atrybutów na początku danych. 

     Na przykład następujące wiersze nagłówka i atrybutów można dodać do prostej listy. Nagłówek kolumny będzie `SampleText`. Należy zauważyć, że string typu nie jest obsługiwany.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Wpisz lub wklej wartości przy użyciu formatu SVMLight.  
  
     Na przykład poniższa próbka przedstawia pierwsze kilka wierszy zestawu danych krwiodawstwa w formacie SVMLight:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Po uruchomieniu modułu [Wprowadź dane ręcznie](./enter-data-manually.md) wiersze te są konwertowane na zestaw kolumn i wartości indeksu w następujący sposób:  
  
     |Col1|Col2|Okręg wyborczy Col3|Okręg wyborczy Col4|Etykiety|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Wybierz klawisz Enter po każdym wierszu, aby rozpocząć nowy wiersz.      
     
   Jeśli wybierzesz opcję Wprowadź wiele razy, aby dodać wiele pustych wierszy końcowych, puste wiersze zostaną usunięte lub przycięte.  
  
   Jeśli tworzysz wiersze z brakującymi wartościami, zawsze możesz je odfiltrować później.  
  
1. Podłącz port wyjściowy do innych modułów i uruchom potok.  
  
   Aby wyświetlić zestaw danych, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 