---
title: 'Usuń zduplikowane wiersze: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modułu usuwania zduplikowanych wierszy w usłudze Azure Machine Learning, aby usunąć potencjalne duplikaty z zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8b3f73c56d85eecd50633085eca0e632abaa6b4c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693667"
---
# <a name="remove-duplicate-rows-module"></a>Usuń moduł zduplikowanych wierszy

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do usuwania potencjalnych duplikatów z zestawu danych.

Załóżmy na przykład, że dane wyglądają jak poniżej, i reprezentują wiele rekordów dla pacjentów. 

| PatientID | Inicjały| Płeć|Wiek|Przyjęte|
|----|----|----|----|----|
|1|F.M.| milionów| 53| Stycznia|
|2| F.A.M.| milionów| 53| Stycznia|
|3| F.A.M.| milionów| 24| Stycznia|
|3| F.M.| milionów| 24| Lutego|
|4| F.M.| milionów| 23| Lutego|
| | F.M.| milionów| 23| |
|5| F.A.M.| milionów| 53| |
|6| F.A.M.| milionów| NaN| |
|7| F.A.M.| milionów| NaN| |

Jasno ten przykład zawiera wiele kolumn, które mogą duplikować dane. Bez względu na to, czy są one w rzeczywistości duplikatami, zależy od wiedzy o danych. 

+ Na przykład może być wiadomo, że wielu pacjentów ma taką samą nazwę. Nie można wyeliminować duplikatów przy użyciu żadnych kolumn nazw, tylko kolumny **ID** . W ten sposób tylko wiersze z zduplikowanymi wartościami identyfikatora są odfiltrowane, bez względu na to, czy pacjenci mają taką samą nazwę, czy nie.

+ Alternatywnie możesz zdecydować, aby zezwolić na duplikaty w polu ID, a także użyć innej kombinacji plików do znajdowania unikatowych rekordów, takich jak imię, nazwisko, wiek i płeć.  

Aby ustawić kryteria dla tego, czy wiersz jest zduplikowany, należy określić pojedynczą kolumnę lub zestaw kolumn do użycia jako **klucze**. Dwa wiersze są uznawane za duplikaty tylko wtedy, gdy wartości we **wszystkich** kolumnach klucza są równe. Jeśli w dowolnym wierszu brakuje wartości **kluczy**, nie będą one uznawane za zduplikowane wiersze. Na przykład jeśli płeć i wiek są ustawione jako klucze w powyższej tabeli, wiersz 6 i 7 nie są zduplikowanymi wierszami, w których brakuje wartości w wieku.

Po uruchomieniu modułu tworzy zestaw danych kandydujących i zwraca zbiór wierszy, które nie mają duplikatów we wskazanym zestawie kolumn.

> [!IMPORTANT]
> Źródłowy zestaw danych nie jest zmieniany; Ten moduł tworzy nowy zestaw danych, który jest filtrowany w celu wykluczania duplikatów na podstawie określonych kryteriów.

## <a name="how-to-use-remove-duplicate-rows"></a>Jak używać usuwania zduplikowanych wierszy

1. Dodaj moduł do potoku. Moduł **usuwanie zduplikowanych wierszy** można znaleźć w obszarze **Przekształcanie danych**, **manipulowanie**.  

2. Połącz zestaw danych, który ma zostać wyszukany dla zduplikowanych wierszy.

3. W okienku **Właściwości** w obszarze **wyrażenie filtru wyboru kolumny klucza**kliknij pozycję **Uruchom selektor kolumny**, aby wybrać kolumny, które mają być używane do identyfikowania duplikatów.

    W tym kontekście **klucz** nie oznacza unikatowego identyfikatora. Wszystkie kolumny wybrane za pomocą selektora kolumn są **wykluczane jako kolumny kluczy**. Wszystkie niewybrane kolumny są uznawane za kolumny niebędące kolumnami klucza. Kombinacja kolumn wybranych jako klucze określa unikatowość rekordów. (Należy traktować ją jako instrukcję SQL, która używa wielu dołączeń równości).

    Przykłady:

    + "Chcę mieć pewność, że identyfikatory są unikatowe": Wybierz tylko kolumnę ID.
    + "Chcę mieć pewność, że kombinacja imienia, nazwisko i identyfikator jest unikatowa": zaznacz wszystkie trzy kolumny.

4. Użyj pola wyboru **Zachowaj pierwszy zduplikowany wiersz** , aby wskazać, który wiersz ma zostać zwrócony po znalezieniu duplikatów:

    + W przypadku zaznaczenia pierwszy wiersz jest zwracany, a inne odrzucane. 
    + W przypadku usunięcia zaznaczenia tej opcji ostatni zduplikowany wiersz jest przechowywany w wynikach, a pozostałe zostaną odrzucone. 

5. Uruchamianie potoku.

6. Aby przejrzeć wyniki, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **zestaw danych wyników**, a następnie kliknij polecenie **Wizualizuj**. 

> [!TIP]
> Jeśli wyniki są trudne do zrozumienia lub jeśli chcesz wykluczyć pewne kolumny, możesz usunąć kolumny za pomocą modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych](./select-columns-in-dataset.md) ).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 