---
title: 'Konwertuj do formatu CSV: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać Konwertuj na moduł CSV w usłudze Azure Machine Learning można przekonwertować zestawu danych do formatu CSV, który może być pobrany, eksportowania lub udostępniania z modułami skryptu języka R lub Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028728"
---
# <a name="convert-to-csv-module"></a>Konwertuj na moduł CSV

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do konwertowania zestawu danych do formatu CSV, które mogą być pobierane, wyeksportować lub udostępnione moduły skryptów języka R lub Python.

### <a name="more-about-the-csv-format"></a>Więcej informacji na temat formatu CSV 

Format CSV, który oznacza "CSV", jest to format pliku używany przez wiele narzędzi do uczenia maszynowego zewnętrznych. CSV to popularny format wymiany podczas pracy z języków open source, takich jak R lub Python.

Nawet jeśli to zrobisz, większość pracy w usłudze Azure Machine Learning, istnieją czasy, kiedy może okazać się przydatna można przekonwertować zestawu danych do pliku CSV do użycia w zewnętrznych narzędzi. Na przykład:

+ Pobierz plik CSV, aby otworzyć go za pomocą programu Excel lub zaimportować go do relacyjnej bazy danych.  
+ Zapisz plik CSV w magazynie w chmurze i połączyć je z usługi Power BI do tworzenia wizualizacji.  
+ Użyj formatu CSV, aby przygotować dane do użycia w języków R i Python. Po prostu kliknij prawym przyciskiem myszy dane wyjściowe z modułu do generowania kodu, na potrzeby dostępu do danych bezpośrednio z poziomu języka Python lub notesu programu Jupyter. 

Podczas konwersji zestawu danych do formatu CSV, plik jest zapisany w obszarze roboczym usługi Azure ML. Narzędzia usługi Azure storage służy do otwierania i używania pliku bezpośrednio lub kliknij prawym przyciskiem myszy danych wyjściowych modułu i Pobierz plik CSV do komputera lub użyć go w kodzie R lub Python.  

## <a name="how-to-configure-convert-to-csv"></a>Jak skonfigurować Konwertuj do formatu CSV

1.  Dodaj [Konwertuj do formatu CSV](./convert-to-csv.md) modułu do eksperymentu. Możesz znaleźć tego modułu w **konwersje formatu danych** grupy w interfejsie. 

2. Podłącz je do każdego modułu, który wyprowadza zestawu danych.   
  
3.  Uruchom eksperyment.

### <a name="results"></a>Wyniki
  

Kliknij dwukrotnie dane wyjściowe [Konwertuj do formatu CSV](./convert-to-csv.md)i wybierz jedną z tych opcji.  

 + **Zestaw wyników danych -> Pobierz**: Natychmiast otwiera kopii danych w formacie CSV, który można zapisać w folderze lokalnym. Jeśli folder nie jest określony, domyślna nazwa pliku jest stosowana, a pliku CSV są zapisywane w lokalnym **pliki do pobrania** biblioteki.


 + **Zestaw wyników danych -> Zapisz jako zestaw danych**: Zapisuje plik CSV do obszaru roboczego usługi uczenie Maszynowe Azure jako oddzielny zestaw danych.

 + **Generuj kod dostępu do danych**: Uczenie Maszynowe systemu Azure generuje dwa rodzaje kodu, które umożliwiają dostęp do danych, przy użyciu języka Python lub przy użyciu języka R. Dostęp do danych, należy skopiować fragment kodu do aplikacji. (*Generuj kod dostępu do danych pojawią się wkrótce.* )

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 