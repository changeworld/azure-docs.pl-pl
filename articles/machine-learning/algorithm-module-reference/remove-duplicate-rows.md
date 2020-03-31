---
title: 'Usuń zduplikowane wiersze: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak usunąć potencjalne duplikaty z zestawu danych za pomocą modułu Usuń zduplikowane wiersze w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456025"
---
# <a name="remove-duplicate-rows-module"></a>Usuń moduł Zduplikowane wiersze

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do usuwania potencjalnych duplikatów z zestawu danych.

Załóżmy na przykład, że dane wyglądają następująco i reprezentują wiele rekordów dla pacjentów. 

| PatientID (ida pacjentów) | Inicjały| Płeć|Wiek|Dopuszczone|
|----|----|----|----|----|
|1|F.M.| M| 53| Sty|
|2| F.A.M.| M| 53| Sty|
|3| F.A.M.| M| 24| Sty|
|3| F.M.| M| 24| Lut|
|4| F.M.| M| 23| Lut|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Oczywiście w tym przykładzie zawiera wiele kolumn z potencjalnie zduplikowanych danych. To, czy są one rzeczywiście duplikatami, zależy od twojej wiedzy na temat danych. 

+ Na przykład możesz wiedzieć, że wielu pacjentów ma taką samą nazwę. Nie można wyeliminować duplikaty przy użyciu żadnych kolumn nazwy, tylko kolumny **identyfikator.** W ten sposób tylko wiersze z zduplikowanymi wartościami identyfikatorów są odfiltrowywały, niezależnie od tego, czy pacjenci mają taką samą nazwę, czy nie.

+ Alternatywnie można zezwolić na duplikaty w polu Identyfikator i użyć innej kombinacji plików, aby znaleźć unikatowe rekordy, takie jak imię, nazwisko, wiek i płeć.  

Aby ustawić kryteria, czy wiersz jest duplikowany, czy nie, należy określić pojedynczą kolumnę lub zestaw kolumn, które mają być używane jako **klucze**. Dwa wiersze są uważane za duplikaty tylko wtedy, gdy wartości we **wszystkich** kolumnach klucza są równe. Jeśli w dowolnym wierszu brakuje wartości **kluczy,** nie będą one traktowane jako zduplikowane wiersze. Na przykład jeśli płeć i wiek są ustawione jako Klucze w powyższej tabeli, wiersze 6 i 7 nie są zduplikowane wiersze, biorąc pod uwagę, że mają brakującą wartość w Age.

Po uruchomieniu modułu tworzy zestaw danych kandydata i zwraca zestaw wierszy, które nie mają duplikatów w zestawie kolumn, które zostały określone.

> [!IMPORTANT]
> Źródłowy zestaw danych nie jest zmieniany; ten moduł tworzy nowy zestaw danych, który jest filtrowany, aby wykluczyć duplikaty, na podstawie określonych kryteriów.

## <a name="how-to-use-remove-duplicate-rows"></a>Jak używać usuwania zduplikowanych wierszy

1. Dodaj moduł do potoku. Moduł Usuń **zduplikowane wiersze** można znaleźć w obszarze **Transformacja danych**, **Manipulacja**.  

2. Połącz zestaw danych, który chcesz sprawdzić pod kątem zduplikowanych wierszy.

3. W okienku **Właściwości** w obszarze **Wyrażenie filtru wyboru Kolumny klucz**kliknij pozycję Uruchom selektor **kolumn**, aby wybrać kolumny do użycia w identyfikowaniu duplikatów.

    W tym kontekście **Key** nie oznacza unikatowy identyfikator. Wszystkie kolumny wybrane za pomocą selektora kolumn są oznaczone jako **kolumny kluczowe**. Wszystkie niezaznaczone kolumny są uważane za kolumny nieklotowe. Kombinacja kolumn wybranych jako klucze określa unikatowość rekordów. (Pomyśl o tym jako instrukcji SQL, która używa wielu równouprawnień).

    Przykłady:

    + "Chcę mieć pewność, że identyfikatory są unikatowe": wybierz tylko kolumnę Identyfikator.
    + "Chcę mieć pewność, że połączenie imienia, nazwiska i identyfikatora jest unikatowe": Wybierz wszystkie trzy kolumny.

4. Użyj pola wyboru **Zachowaj pierwszy zduplikowany wiersz,** aby wskazać, który wiersz ma być zwracany po znalezieniu duplikatów:

    + Jeśli ta opcja jest zaznaczona, zwracany jest pierwszy wiersz, a inne odrzucane. 
    + Jeśli odznaczysz tę opcję, ostatni zduplikowany wiersz zostanie zachowany w wynikach, a inne zostaną odrzucone. 

5. Prześlij potok.

6. Aby przejrzeć wyniki, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**. 

> [!TIP]
> Jeśli wyniki są trudne do zrozumienia lub jeśli chcesz wykluczyć niektóre kolumny z rozpatrzenia, można usunąć kolumny za pomocą wybierz kolumny w module [zestawu danych.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 