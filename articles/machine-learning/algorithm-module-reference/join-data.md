---
title: 'Dołącz do danych: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć sprzężenia modułu łączenie danych w usłudze Azure Machine Learning do scalania zestawów danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518016"
---
# <a name="join-data"></a>Łączenie danych

W tym artykule opisano sposób używania **łączenia danych** modułu w interfejsie visual usługi Azure Machine Learning można scalić dwóch zestawów danych przy użyciu operacji tworzenia sprzężenia stylu dla bazy danych.  

## <a name="how-to-configure-join-data"></a>Jak skonfigurować Dołącz do danych

Aby wykonać sprzężenia na dwa zestawy danych, musi być powiązana według kolumny klucza. Obsługiwane są również klucze złożone, korzystając z wielu kolumn. 

1. Dodaj zestawy danych, aby połączyć, a następnie przeciągnij **Dołącz dane** modułu do eksperymentu. 

    Można znaleźć modułu w **przekształcania danych** kategorii, w obszarze **manipulowania**.

1. Zestawy danych, aby połączyć **Dołącz dane** modułu. 
 
1. Wybierz **uruchamianie selektora kolumn** wyboru kolumn kluczy. Pamiętaj wybrać kolumny do lewego i prawego wejść.

    Dla pojedynczego klucza:

    Wybrać pojedynczą kolumnę klucza dla obu danych wejściowych.
    
    Aby uzyskać klucz złożony:

    Zaznacz wszystkie kolumny klucza po lewej stronie dane wejściowe i dane wejściowe w tej samej kolejności. **Łączenia danych** modułu dołączy tabel, gdy wszystkie kolumny klucza są zgodne. Zaznacz opcję **zezwala na istnienie duplikatów i zachować kolejność kolumn w zaznaczeniu** Jeśli kolejność kolumn nie jest taka sama jak oryginalnej tabeli. 

    ![Selektor kolumn](media/module/join-data-column-selector.png)


1. Wybierz **Uwzględnij wielkość liter** opcję, jeśli chcesz zachować rozróżnianie wielkości liter w sprzężeniu kolumny tekstu. 
   
1. Użyj **typ przyłączenia** listę rozwijaną, aby określić, jak zestawy danych powinny być one łączone.  
  
    * **Sprzężenie wewnętrzne**: *Sprzężenia wewnętrznego* jest najbardziej typowych operacji tworzenia sprzężenia. Zwraca połączony wierszy tylko wtedy, gdy wartości kolumn klucza są zgodne.  
  
    * **Lewe sprzężenie zewnętrzne**: A *lewe sprzężenie zewnętrzne* zwraca połączone wiersze dla wszystkich wierszy z tabeli po lewej stronie. Gdy wiersz w tabeli po lewej stronie zawiera pasujących wierszy w tabeli po prawej, zwracany wiersz zawiera brakujące wartości dla wszystkich kolumn, które pochodzą z tabelą po prawej stronie. Można także określić wartość zastąpienia dla brakujących wartości.  
  
    * **Pełne sprzężenie zewnętrzne**: A *pełne sprzężenie zewnętrzne* zwraca wszystkie wiersze z tabeli po lewej stronie (**Tabela1**) i z tabelą po prawej stronie (**table2**).  
  
         Dla poszczególnych wierszy w każdej z tabel, które mają pasujących wierszy w drugiej wynik zawiera wiersz zawierający brakujące wartości.  
  
    * **LEFT Join rozdzielana**: A *left join częściową* zwraca tylko wartości z tabeli po lewej stronie, gdy wartości kolumny klucza są zgodne.  

1. Dla opcji **zachować klucza prawego kolumn w tabeli dołączonym do**:

    * Wybierz tę opcję, aby wyświetlić klucze z obu tabel danych wejściowych.
    * Usuń zaznaczenie opcji do zwrócenia tylko kolumny klucza z danych wejściowych po lewej stronie.

1. Uruchom eksperyment, lub wybierz moduł łączenia danych i wybrane **Uruchom wybrane** do wykonania sprzężenia.

1. Aby wyświetlić wyniki, kliknij prawym przyciskiem myszy **Dołącz dane** > **zestaw wyników danych** > **Visualize**.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 