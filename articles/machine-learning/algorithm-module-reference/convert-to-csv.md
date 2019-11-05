---
title: 'Konwertuj na CSV: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Konwertuj do woluminów CSV w Azure Machine Learning skonwertować zestaw danych do formatu CSV, który można pobrać, wyeksportować lub udostępnić za pomocą modułów skryptów języka R lub Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 999f78ab08e1a2c9dd6b28d853e49fbb559fab83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493852"
---
# <a name="convert-to-csv-module"></a>Konwertuj na moduł CSV

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do konwertowania zestawu danych w formacie CSV, który można pobrać, wyeksportować lub udostępnić za pomocą modułów skryptów języka R lub Python.

### <a name="more-about-the-csv-format"></a>Więcej informacji o formacie CSV 

Format CSV, który oznacza "wartości rozdzielane przecinkami", jest formatem pliku używanym przez wiele zewnętrznych narzędzi uczenia maszynowego. Plik CSV jest typowym formatem wymiany podczas pracy z językami typu open source, takimi jak R lub Python.

Nawet w przypadku wykonywania większości zadań w Azure Machine Learning mogą wystąpić sytuacje, w których może być przydatne przekonwertowanie zestawu danych do pliku CSV w celu użycia w zewnętrznych narzędziach. Na przykład:

+ Pobierz plik CSV, aby otworzyć go w programie Excel, lub zaimportuj go do relacyjnej bazy danych.  
+ Zapisz plik CSV w magazynie w chmurze i Połącz się z nim z Power BI, aby tworzyć wizualizacje.  
+ Użyj formatu CSV, aby przygotować dane do użycia w języku R i Python. Po prostu kliknij prawym przyciskiem myszy dane wyjściowe modułu, aby wygenerować kod wymagany do uzyskania dostępu do danych bezpośrednio z poziomu środowiska Python lub notesu Jupyter. 

Podczas konwertowania zestawu danych do formatu CSV plik jest zapisywany w obszarze roboczym usługi Azure ML. Możesz użyć narzędzia usługi Azure Storage, aby otworzyć plik i użyć go bezpośrednio, lub kliknąć prawym przyciskiem myszy dane wyjściowe modułu i pobrać plik CSV na komputer lub użyć go w kodzie języka R lub Python.  

## <a name="how-to-configure-convert-to-csv"></a>Jak skonfigurować konwertowanie do formatu CSV


1.  Dodaj moduł [konwersji do woluminu CSV](./convert-to-csv.md) do potoku. Ten moduł można znaleźć w grupie **konwersje formatu danych** w projektancie. 

2. Połącz ją z dowolnym modułem, który wyprowadza zestaw danych.   
  
3.  Uruchamianie potoku.

### <a name="results"></a>Wyniki
  

Kliknij dwukrotnie dane wyjściowe [konwersji do CSV](./convert-to-csv.md), a następnie wybierz jedną z tych opcji.  

 + **Zestaw danych wyników — pobieranie >** : natychmiast otwiera kopię danych w formacie CSV, który można zapisać w folderze lokalnym. Jeśli nie określisz folderu, zostanie zastosowana domyślna nazwa pliku, a plik CSV zostanie zapisany w lokalnej bibliotece **pobierania** .


 + **Zestaw danych wyników — > Zapisz jako zestaw danych**: zapisuje plik CSV z powrotem do obszaru roboczego usługi Azure ml jako oddzielnego zestawu danych.

 + **Generuj kod dostępu do danych**: usługa Azure ml generuje dwa zestawy kodu umożliwiające uzyskiwanie dostępu do danych za pomocą języka Python lub przy użyciu środowiska R. Aby uzyskać dostęp do danych, skopiuj fragment kodu do aplikacji. (*Generowanie kodu dostępu do danych będzie wkrótce dostępne).*

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 