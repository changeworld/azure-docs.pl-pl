---
title: 'Dane sprzężenia: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak scalić zestawy danych za pomocą modułu dołączanie danych w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477582"
---
# <a name="join-data"></a>Łączenie danych

W tym artykule opisano, jak używać modułu **Dołącz dane** w projektancie usługi Azure Machine Learning (wersja zapoznawcza) do scalania dwóch zestawów danych przy użyciu operacji sprzężenia w stylu bazy danych.  

## <a name="how-to-configure-join-data"></a>Jak skonfigurować dane dołączania

Aby wykonać sprzężenie na dwóch zestawach danych, powinny one być powiązane przez kolumnę klucza. Obsługiwane są również klucze złożone przy użyciu wielu kolumn. 

1. Dodaj zestawy danych, które chcesz połączyć, a następnie przeciągnij moduł **Dołącz dane** do potoku. 

    Moduł można znaleźć w kategorii **Transformacja danych** w obszarze **Manipulacja**.

1. Połącz zestawy danych z modułem **Dołącz dane.** 
 
1. Wybierz **selektor kolumny Uruchom,** aby wybrać kolumny kluczy. Pamiętaj, aby wybrać kolumny dla lewego i prawego wejścia.

    Dla pojedynczego klawisza:

    Wybierz pojedynczą kolumnę klucza dla obu wejść.
    
    Dla klucza złożonego:

    Zaznacz wszystkie kolumny kluczy z lewego wejścia i prawego wejścia w tej samej kolejności. Moduł **Dołącz dane** dołączy do tabel, gdy wszystkie kolumny kluczy są zgodne. Zaznacz opcję **Zezwalaj na duplikaty i zachowaj kolejność kolumn w zaznaczeniu,** jeśli kolejność kolumn nie jest taka sama jak w oryginalnej tabeli. 

    ![selektor kolumn](media/module/join-data-column-selector.png)


1. Zaznacz opcję **Dopasuj wielkość liter,** jeśli chcesz zachować wielkość liter w sprzęcie kolumny tekstowej. 
   
1. Lista rozwijana **typu sprzężenia** służy do określania sposobu łączenia zestawów danych.  
  
    * **Sprzężenie wewnętrzne:** *Sprzężenie wewnętrzne* jest najczęstszą operacją sprzężenia. Zwraca połączone wiersze tylko wtedy, gdy wartości kolumn klucza są zgodne.  
  
    * **Lewe sprzężenie zewnętrzne:** *Lewe sprzężenie zewnętrzne* zwraca połączone wiersze dla wszystkich wierszy z lewej tabeli. Gdy wiersz w lewej tabeli nie ma pasujących wierszy w prawej tabeli, zwrócony wiersz zawiera brakujące wartości dla wszystkich kolumn, które pochodzą z prawej tabeli. Można również określić wartość zastępczą dla brakujących wartości.  
  
    * **Pełne sprzężenie** *zewnętrzne: Pełne sprzężenie zewnętrzne* zwraca wszystkie wiersze z lewej**tabeli (tabela1)** i z prawej**tabeli (tabela2).**  
  
         Dla każdego z wierszy w każdej tabeli, które nie mają pasujących wierszy w drugiej, wynik zawiera wiersz zawierający brakujące wartości.  
  
    * **Lewe półskładnienie:** *Lewe półskładnik* zwraca tylko wartości z lewej tabeli, gdy wartości kolumn klucza są zgodne.  

1. Dla opcji **Zachowaj prawe kolumny kluczy w tabeli połączonej:**

    * Wybierz tę opcję, aby wyświetlić klucze z obu tabel wejściowych.
    * Usuń zaznaczenie, aby zwrócić tylko kolumny kluczy z lewego wejścia.

1. Prześlij potok.

1. Aby wyświetlić wyniki, kliknij prawym przyciskiem myszy **pozycję Dołącz do danych** i wybierz polecenie **Wizualizuj**.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 