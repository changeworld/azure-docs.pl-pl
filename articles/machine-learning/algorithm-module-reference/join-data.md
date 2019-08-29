---
title: 'Dołącz dane: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak scalić zestawy danych przy użyciu modułu Join Data JOIN w Azure Machine Learning Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ms.openlocfilehash: 5fdb822a445e8a62cd36e6dd05aa218b9b41ce9e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128737"
---
# <a name="join-data"></a>Łączenie danych

W tym artykule opisano, jak używać modułu dołączania **danych** w interfejsie wizualnym usługi Azure Machine Learning Service do scalania dwóch zestawów danych przy użyciu operacji łączenia w stylu bazy danych.  

## <a name="how-to-configure-join-data"></a>Jak skonfigurować dane sprzężenia

Aby wykonać sprzężenie na dwóch zestawach danych, powinny one być powiązane z kolumną klucza. Obsługiwane są również klucze złożone używające wielu kolumn. 

1. Dodaj zestawy danych, które chcesz połączyć, a następnie przeciągnij moduł **Dołącz dane** do eksperymentu. 

    Moduł można znaleźć w kategorii przekształcanie **danych** w obszarze manipulowanie.

1. Połącz zestawy danych z modułem **Join Data** . 
 
1. Wybierz pozycję **Uruchom selektor kolumny** , aby wybrać kolumny klucza. Pamiętaj, aby wybrać kolumny dla wejść w lewo i w prawo.

    Dla pojedynczego klucza:

    Wybierz jedną kolumnę klucza dla obu danych wejściowych.
    
    Dla klucza złożonego:

    Zaznacz wszystkie kolumny kluczy od lewej i prawej strony w tej samej kolejności. Moduł **łączenia danych** zostanie dołączony do tabel, gdy wszystkie kolumny klucza są zgodne. Zaznacz opcję **Zezwalaj na duplikowanie i Zachowaj kolejność kolumn w zaznaczeniu** , jeśli kolejność kolumn nie jest taka sama jak oryginalna tabela. 

    ![Selektor kolumn](media/module/join-data-column-selector.png)


1. Wybierz opcję **Uwzględnij wielkość** liter, jeśli chcesz zachować czułość wielkości liter w sprzężeniu kolumny tekstowej. 
   
1. Użyj listy rozwijanej **Typ sprzężenia** , aby określić sposób łączenia zestawów danych.  
  
    * **Sprzężenie wewnętrzne**: *Sprzężenie wewnętrzne* jest najpopularniejszą operacją Join. Zwraca połączone wiersze tylko wtedy, gdy wartości kolumn klucza są zgodne.  
  
    * **Lewe sprzężenie zewnętrzne**: *Lewe sprzężenie zewnętrzne* zwraca dołączone wiersze dla wszystkich wierszy z tabeli po lewej. Gdy wiersz w lewej tabeli nie ma pasujących wierszy w prawej tabeli, zwracany wiersz zawiera brakujące wartości dla wszystkich kolumn, które pochodzą z prawej tabeli. Możesz również określić wartość zastępczą dla brakujące wartości.  
  
    * **Pełne sprzężenie zewnętrzne**: *Pełne sprzężenie zewnętrzne* zwraca wszystkie wiersze z tabeli po lewej (**Tabela1**) i z prawej tabeli (**tabela2**).  
  
         W przypadku każdego z wierszy w tabeli, które nie mają pasujących wierszy w drugim, wynik zawiera wiersz zawierający brakujące wartości.  
  
    * **Lewe**odłączenie: *Lewe* odłączenie zwraca tylko wartości z tabeli po lewej, gdy wartości kolumn klucza są zgodne.  

1. Dla opcji **Zachowaj kolumny klucza prawego w połączonej tabeli**:

    * Wybierz tę opcję, aby wyświetlić klucze z obu tabel wejściowych.
    * Usuń zaznaczenie, aby zwrócić tylko kolumny klucza z lewej strony.

1. Uruchom eksperyment lub wybierz moduł łączenia danych i wybrane **uruchomienie wybrane** do wykonania sprzężenia.

1. Aby wyświetlić wyniki, kliknij prawym przyciskiem myszy > wizualizację**zestawu** > danych wyniki sprzężenia danych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 