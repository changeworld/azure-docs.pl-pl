---
title: 'Usuń zduplikowane wiersze: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu programu Usuń zduplikowane wiersze w usłudze Azure Machine Learning, aby usunąć potencjalne duplikaty z zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029298"
---
# <a name="remove-duplicate-rows-module"></a>Usuń zduplikowane wiersze moduł

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do usuwania potencjalne duplikaty zestawu danych.

Załóżmy na przykład dane wygląda podobnie do następującej i reprezentuje wiele rekordów dla pacjentów. 

| PatientID | Inicjały| Płeć|Wiek|Dopuszczone|
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

Wyraźnie widać w tym przykładzie zawiera wiele kolumn z potencjalnie zduplikowanych danych. Czy są one faktycznie duplikaty zależy od swojej wiedzy na temat danych. 

+ Na przykład wiedzieć, że wielu pacjentów mają taką samą nazwę. W takich sytuacjach przydałaby wyeliminować duplikaty, przy użyciu żadnej kolumny nazwy tylko **identyfikator** kolumny. Dzięki temu tylko wiersze zawierające zduplikowane wartości identyfikatorów są odfiltrowywane, niezależnie od tego, czy pacjentów mają taką samą nazwę, czy nie.

+ Alternatywnie można zdecydować, zezwala na istnienie duplikatów w polu Identyfikator i użyj pewne inne kombinacje plików, aby znaleźć unikatowe rekordy, takie jak imię, ostatnia nazwa, wieku i płci.  

Aby ustawić kryteria dotyczące tego, czy wiersz jest zduplikowany, należy określić pojedynczej kolumny lub zestaw kolumn do użycia jako **klucze**. Dwa wiersze są traktowane jako duplikaty tylko wtedy, gdy wartości w **wszystkich** kolumny klucza są takie same. Jeśli brak wartości dla dowolnego wiersza **klucze**, ich nie zostanie uwzględniony zduplikowane wiersze. Na przykład jeśli płci i wieku są skonfigurowane tak, jak klucze w powyższej tabeli, wiersz 6 i 7 nie są zduplikowane wiersze, biorąc pod uwagę mają brakujących wartości w wieku.

Podczas uruchamiania modułu jest tworzony zestaw danych Release candidate i zwraca zestaw wierszy zawierających bez duplikatów w zestawie kolumn, wskazana.

> [!IMPORTANT]
> Zestaw danych źródłowych nie ulega zmianie; Ten moduł tworzy nowy zestaw danych, która jest filtrowana w celu wykluczenia duplikaty, na podstawie kryteriów, które określisz.

## <a name="how-to-use-remove-duplicate-rows"></a>Jak używać Usuń zduplikowane wiersze

1. Dodaj moduł do eksperymentu. Możesz znaleźć **Usuń zduplikowane wiersze** moduł **przekształcania danych**, **manipulowania**.  

2. Połącz z zestawu danych, który chcesz sprawdzić, czy zduplikowane wiersze.

3. W **właściwości** okienku w obszarze **wyrażenia filtru wybór kolumny klucza**, kliknij przycisk **uruchamianie selektora kolumn**, aby wybrać kolumny, używany do identyfikowania duplikaty.

    W tym kontekście **klucz** nie oznacza Unikatowy identyfikator. Wszystkie kolumny, możesz wybrać, za pomocą selektora kolumny, które zostały oznaczone jako **kolumny klucza**. Wszystkie niezaznaczonych kolumn są traktowane jako kolumny klucza. Połączenie kolumn, które można wybrać jako klucze określa unikatowości rekordów. (Go traktować jako instrukcji SQL, który używa wielu sprzężeń equalities.)

    Przykłady:

    + "Chcemy się upewnić, że nazwy są unikatowe:" Wybierz tylko kolumny Identyfikatora.
    + "Chcę upewnić się, że kombinacja imienia, nazwiska i identyfikator jest unikatowy": Zaznacz wszystkie trzy kolumny.

4. Użyj **Zachowaj pierwszy wiersz zduplikowane** pole wyboru, aby wskazać, który wiersz do zwrócenia, jeśli zostaną znalezione duplikaty:

    + Jeśli zaznaczone, zwracany jest pierwszy wiersz, a następnie odrzucone przez inne osoby. 
    + Jeśli ta opcja nie zostanie zaznaczona, ostatni wiersz zduplikowane są przechowywane w wynikach, a inne są odrzucane. 

5. Uruchom eksperyment.

6. Aby przejrzeć wyniki, kliknij prawym przyciskiem myszy moduł, wybierz **zestaw wyników danych**i kliknij przycisk **Visualize**. 

> [!TIP]
> Jeśli wyniki są trudne do zrozumienia, lub jeśli chcesz wykluczyć niektóre kolumny pod uwagę, możesz usunąć kolumny przy użyciu [Select Columns in Dataset](./select-columns-in-dataset.md) modułu.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 