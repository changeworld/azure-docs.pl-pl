---
title: 'Konwersja do CSV: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Konwertuj na CSV w usłudze Azure Machine Learning, aby przekonwertować zestaw danych na format CSV, który można pobrać, wyeksportować lub udostępnić modułom skryptów języka R lub Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477684"
---
# <a name="convert-to-csv-module"></a>Konwertuj na moduł CSV

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do konwertowania zestawu danych na format CSV, który można pobrać, wyeksportować lub udostępnić modułom skryptów Języka R lub Pythona.

### <a name="more-about-the-csv-format"></a>Więcej informacji o formacie CSV 

Format CSV, który oznacza "wartości oddzielone przecinkami", jest formatem pliku używanym przez wiele zewnętrznych narzędzi uczenia maszynowego. CSV jest typowym formatem wymiany podczas pracy z językami open source, takimi jak R lub Python.

Nawet jeśli większość pracy w usłudze Azure Machine Learning, istnieją chwile, kiedy może okazać się przydatne do konwersji zestawu danych do csv do użycia w narzędziach zewnętrznych. Przykład:

+ Pobierz plik CSV, aby otworzyć go w programie Excel, lub zaimportować go do relacyjnej bazy danych.  
+ Zapisz plik CSV w magazynie w chmurze i połącz się z nim z usługą Power BI, aby utworzyć wizualizacje.  
+ Użyj formatu CSV, aby przygotować dane do użycia w językach R i Python. 

Podczas konwertowania zestawu danych do pliku CSV csv jest zapisywany w obszarze roboczym usługi Azure ML. Można użyć narzędzia magazynu platformy Azure, aby otworzyć i używać pliku bezpośrednio. Można również uzyskać dostęp do pliku CSV w projektancie, wybierając moduł **Konwertuj na CSV,** a następnie wybierz ikonę histogramu na karcie Wyjścia w prawym panelu, aby wyświetlić dane **wyjściowe.** Plik CSV można pobrać z folderu Wyniki do katalogu lokalnego.  

## <a name="how-to-configure-convert-to-csv"></a>Jak skonfigurować konwersję na csv


1.  Dodaj moduł Konwertuj na CSV do potoku. Ten moduł można znaleźć w grupie **Transformacja danych** w projektancie. 

2. Podłącz go do dowolnego modułu, który wyprowadza zestaw danych.   
  
3.  Prześlij potok.

### <a name="results"></a>Wyniki
  

Wybierz kartę **Wyjścia** w prawym panelu **Konwertuj na CSV**i wybierz jedną z tych ikon w obszarze **Wyjścia portowe**.  

+ **Zarejestruj zestaw danych:** Wybierz ikonę i zapisz plik CSV z powrotem w obszarze roboczym usługi Azure ML jako oddzielny zestaw danych. Zestaw danych można znaleźć jako moduł w drzewie modułów w sekcji **Moje zestawy danych.**

 + **Wyświetlanie danych wyjściowych**: Wybierz ikonę oka i postępuj zgodnie z instrukcją, aby przeglądać folder **Results_dataset** i pobrać plik data.csv.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 