---
title: 'Konwertuj na CSV: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak za pomocą modułu konwersji do woluminów CSV w usłudze Azure Machine Learning skonwertować zestaw danych do formatu CSV, który można pobrać, wyeksportować lub udostępnić za pomocą modułów skryptów języka R lub Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2b8f235014efe440e518f1f9c4d0d454f6918955
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128909"
---
# <a name="convert-to-csv-module"></a>Konwertuj na moduł CSV

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do konwertowania zestawu danych w formacie CSV, który można pobrać, wyeksportować lub udostępnić za pomocą modułów skryptów języka R lub Python.

### <a name="more-about-the-csv-format"></a>Więcej informacji o formacie CSV 

Format CSV, który oznacza "wartości rozdzielane przecinkami", jest formatem pliku używanym przez wiele zewnętrznych narzędzi uczenia maszynowego. Plik CSV jest typowym formatem wymiany podczas pracy z językami typu open source, takimi jak R lub Python.

Nawet w przypadku wykonywania większości zadań w Azure Machine Learning mogą wystąpić sytuacje, w których może być przydatne przekonwertowanie zestawu danych do pliku CSV w celu użycia w zewnętrznych narzędziach. Na przykład:

+ Pobierz plik CSV, aby otworzyć go w programie Excel, lub zaimportuj go do relacyjnej bazy danych.  
+ Zapisz plik CSV w magazynie w chmurze i Połącz się z nim z Power BI, aby tworzyć wizualizacje.  
+ Użyj formatu CSV, aby przygotować dane do użycia w języku R i Python. Po prostu kliknij prawym przyciskiem myszy dane wyjściowe modułu, aby wygenerować kod wymagany do uzyskania dostępu do danych bezpośrednio z poziomu środowiska Python lub notesu Jupyter. 

Podczas konwertowania zestawu danych do formatu CSV plik jest zapisywany w obszarze roboczym usługi Azure ML. Możesz użyć narzędzia usługi Azure Storage, aby otworzyć plik i użyć go bezpośrednio, lub kliknąć prawym przyciskiem myszy dane wyjściowe modułu i pobrać plik CSV na komputer lub użyć go w kodzie języka R lub Python.  

## <a name="how-to-configure-convert-to-csv"></a>Jak skonfigurować konwertowanie do formatu CSV

1.  Dodaj do eksperymentu moduł [konwersji do woluminu CSV](./convert-to-csv.md) . Ten moduł można znaleźć w grupie **konwersje formatu danych** w interfejsie. 

2. Połącz ją z dowolnym modułem, który wyprowadza zestaw danych.   
  
3.  Uruchom eksperyment.

### <a name="results"></a>Wyniki
  

Kliknij dwukrotnie dane wyjściowe [konwersji do CSV](./convert-to-csv.md), a następnie wybierz jedną z tych opcji.  

 + **Zestaw danych wyników — pobieranie >** : Natychmiast otwiera kopię danych w formacie CSV, który można zapisać w folderze lokalnym. Jeśli nie określisz folderu, zostanie zastosowana domyślna nazwa pliku, a plik CSV zostanie zapisany w lokalnej bibliotece **pobierania** .


 + **Zestaw danych wyników — > Zapisz jako zestaw danych**: Zapisuje plik CSV z powrotem do obszaru roboczego Azure ML jako oddzielnego zestawu danych.

 + **Generuj kod dostępu do danych**: Usługa Azure ML generuje dwa zestawy kodu, aby uzyskać dostęp do danych przy użyciu języka Python lub w języku R. Aby uzyskać dostęp do danych, skopiuj fragment kodu do aplikacji. (*Generowanie kodu dostępu do danych będzie wkrótce dostępne).*

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 