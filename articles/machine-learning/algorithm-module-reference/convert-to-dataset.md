---
title: 'Konwertuj na zestaw danych: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu Konwertuj do zestawu danych w usłudze Azure Machine Learning, aby konwertować dane wejściowe do wewnętrznego formatu zestawu danych używanego przez Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516255"
---
# <a name="convert-to-dataset"></a>Konwertuj na zestaw danych

W tym artykule opisano, jak używać modułu [Konwertuj do zestawu danych](convert-to-dataset.md) w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu przekonwertowania wszelkich danych dla potoku do wewnętrznego formatu używanego przez projektanta.
  
Konwersja nie jest wymagana w większości przypadków, ponieważ Azure Machine Learning niejawnie konwertuje dane do formatu natywnego zestawu danych w przypadku wykonywania dowolnej operacji na danych. 

Jednak zapisywanie danych w formacie zestawu danych jest zalecane, jeśli wykonano pewne rodzaje normalizacji lub czyszczenia zestawu danych i chcesz się upewnić, że zmiany są używane w dalszych potokach.  
  
> [!NOTE]
> [Konwersja na zestaw danych](convert-to-dataset.md) zmienia tylko format danych. Nie zapisuje nowej kopii danych w obszarze roboczym. Aby zapisać zestaw danych, kliknij dwukrotnie port wyjściowy, wybierz pozycję **Zapisz jako zestaw danych**i wpisz nową nazwę.  
  
## <a name="how-to-use-convert-to-dataset"></a>Jak używać konwersji do zestawu danych  

Zalecamy użycie modułu [Edytowanie metadanych](edit-metadata.md) w celu przygotowania zestawu danych przed użyciem [konwersji do zestawu danych](convert-to-dataset.md).  Można dodawać lub zmieniać nazwy kolumn, dopasowywać typy danych i tak dalej.

1.  Dodaj moduł [konwersji do zestawu danych](convert-to-dataset.md) do potoku. Ten moduł można znaleźć w kategorii **Przekształcanie danych** w projektancie. 

2. Połącz ją z dowolnym modułem, który wyprowadza zestaw danych.   

    Tak długo, jak dane są [tabelaryczne](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), można przekonwertować ją na zestaw danych. Obejmuje to dane załadowane przy użyciu opcji [Importuj dane](import-data.md), dane utworzone przy użyciu polecenia [Wprowadź dane ręcznie](enter-data-manually.md)lub zestawy danych przekształcone przy użyciu [zastosowania transformacji](apply-transformation.md).

3.  Na liście rozwijanej **Akcja** wskaż, czy chcesz przeprowadzić oczyszczanie danych przed zapisaniem zestawu danych:  
  
    - **Brak**: Użyj danych jako.  
  
    - **SetMissingValue**: Ustaw określoną wartość na brakującą wartość w zestawie danych. Domyślny symbol zastępczy jest znakiem zapytania (?), ale można użyć niestandardowej opcji **braku wartości** , aby wpisać inną wartość. Na przykład, jeśli wpiszesz "taksówki" dla **niestandardowej brakującej wartości**, wszystkie "taksówki" w zestawie danych zostaną zmienione na brakującą wartość.
  
    - **ReplaceValues**: Użyj tej opcji, aby określić pojedynczą dokładną wartość, która zostanie zamieniona na inną dokładną wartość.  Nie można zastąpić brakujących wartości lub wartości niestandardowych przez ustawienie metody **replace** : **Brak**: Wybierz tę opcję, aby zastąpić brakujące wartości w wejściowym zestawie danych. Dla opcji **Nowa wartość**wpisz wartość, aby zastąpić brakujące wartości wartościami.
            - **niestandardowe**: Wybierz tę opcję, aby zastąpić wartości niestandardowe w wejściowym zestawie danych. W polu **wartość niestandardowa**wpisz wartość, która ma zostać znaleziona. Na przykład przy założeniu, że dane zawierają ciąg `obs` używany jako symbol zastępczy dla brakujących wartości, należy wpisać `obs`. Dla **nowej wartości**wpisz nową wartość, aby zastąpić oryginalny ciąg ciągiem.
  
    Należy pamiętać, że operacja **ReplaceValues** ma zastosowanie tylko do dokładnych dopasowań. Na przykład nie wpłynie to na te ciągi: `obs.`, `obsolete`.  
 
  
5.  Uruchom potok lub kliknij prawym przyciskiem myszy moduł [Konwertuj na zestaw danych](convert-to-dataset.md) i wybierz pozycję **Uruchom wybrane**.  

## <a name="results"></a>Wyniki

+  Aby zapisać wynikowy zestaw danych z nową nazwą, kliknij prawym przyciskiem myszy dane wyjściowe [konwersji do zestawu danych](convert-to-dataset.md) i wybierz polecenie **Zapisz jako zestaw danych**.  
  
## <a name="technical-notes"></a>Uwagi techniczne  

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

-   Każdy moduł, który pobiera zestaw danych jako dane wejściowe, może również przyjmować dane w woluminie CSV lub TSV. Przed wykonaniem kodu modułu zostanie wykonane wstępne przetwarzanie danych wejściowych, co jest równoznaczne z uruchomieniem modułu [Konwertuj do zestawu danych](convert-to-dataset.md) na wejściu.  
  
-   Nie można przeprowadzić konwersji z formatu SVMLight na zestaw danych.  
  
-   Podczas określania niestandardowej operacji zamiany operacja wyszukiwania i zamieniania ma zastosowanie do wartości całkowitych. częściowe dopasowania są niedozwolone. Na przykład można zastąpić 3 z-1 lub 33, ale nie można zastąpić 3 w liczbie dwucyfrowej, takiej jak 35.  
  
-   W przypadku operacji zastąpienia niestandardowego zastąpienie zostanie zakończone niepowodzeniem, jeśli używasz jako zamiennika dowolnego znaku, który nie jest zgodny z bieżącym typem danych kolumny.  

  
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
