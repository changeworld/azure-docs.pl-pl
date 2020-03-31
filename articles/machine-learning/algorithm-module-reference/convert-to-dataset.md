---
title: 'Konwertuj na zestaw danych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Konwertuj na zestaw danych w usłudze Azure Machine Learning przekonwertować dane wejściowe na wewnętrzny format zestawu danych używany przez usługę Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456594"
---
# <a name="convert-to-dataset"></a>Konwertowanie na zestaw danych

W tym artykule opisano, jak użyć modułu Konwertuj na zestaw danych w projektancie usługi Azure Machine Learning (wersja zapoznawcza) w celu konwersji dowolnych danych dla potoku do formatu wewnętrznego projektanta.
  
Konwersja nie jest wymagana w większości przypadków. Usługa Azure Machine Learning niejawnie konwertuje dane do macierzystego formatu zestawu danych, gdy każda operacja jest wykonywana na danych. 

Firma Microsoft zaleca zapisywanie danych w formacie zestawu danych, jeśli wykonano pewnego rodzaju normalizacji lub czyszczenia na zestaw danych i chcesz upewnić się, że zmiany są używane w innych potoków.  
  
> [!NOTE]
> Konwertowanie na zestaw danych zmienia tylko format danych. Nie zapisuje nową kopię danych w obszarze roboczym. Aby zapisać zestaw danych, kliknij dwukrotnie port wyjściowy, wybierz pozycję **Zapisz jako zestaw danych**i wprowadź nową nazwę.  
  
## <a name="how-to-use-convert-to-dataset"></a>Jak używać funkcji Konwertuj na zestaw danych  

Zaleca się użycie modułu [Edytuj metadane](edit-metadata.md) do przygotowania zestawu danych przed użyciem funkcji Konwertuj na zestaw danych. W razie potrzeby można dodawać lub zmieniać nazwy kolumn, dostosowywać typy danych i wprowadzać inne zmiany.

1.  Dodaj moduł Konwertuj do zestawu danych do potoku. Ten moduł można znaleźć w kategorii **Transformacja danych** w projektancie. 

2. Podłącz go do dowolnego modułu, który wyprowadza zestaw danych.   

    Tak długo, jak dane są [tabelaryczne,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)można przekonwertować je na zestaw danych. Obejmuje to dane ładowane za pośrednictwem [importu danych,](import-data.md)dane utworzone za pomocą [narzędzia Wprowadź dane ręcznie](enter-data-manually.md)lub zestawy danych przekształcone za pomocą transformacji [zastosuj.](apply-transformation.md)

3.  Na liście rozwijanej **Akcja** przed zapisaniem zestawu danych podaj, czy chcesz wykonać jakiekolwiek oczyszczanie danych:  
  
    - **Brak:** Użyj danych w stanie innym.  
  
    - **SetMissingValue**: Ustaw określoną wartość na brakującą wartość w zestawie danych. Domyślnym symbolem zastępczym jest znak zapytania (?), ale można użyć opcji **Niestandardowa braku wartości,** aby wprowadzić inną wartość. Na przykład jeśli wprowadzisz **Taxi** for **Custom brakującą wartość**, wszystkie wystąpienia **Taxi** w zestawie danych zostaną zmienione na brakującą wartość.
  
    - **Zamień Wartości:** Użyj tej opcji, aby określić jedną dokładną wartość, która ma zostać zastąpiona dowolną inną dokładną wartością. Brakujące wartości lub wartości niestandardowe można zastąpić, ustawiając metodę **Zamień:**

      - **Brak**: Wybierz tę opcję, aby zastąpić brakujące wartości w wejściowym zestawie danych. W polu **Nowa wartość**wprowadź wartość, która ma zastąpić brakujące wartości.
      - **Niestandardowe**: Wybierz tę opcję, aby zastąpić wartości niestandardowe w wejściowym zestawie danych. W polu **Wartość niestandardowa**wprowadź wartość, którą chcesz znaleźć. Na przykład, jeśli dane `obs` zawierają ciąg używany jako symbol zastępczy dla brakujących wartości, należy wprowadzić `obs`program . W polu **Nowa wartość**wprowadź nową wartość, aby zastąpić oryginalny ciąg.
  
    Należy zauważyć, że **Operacja ReplaceValues** ma zastosowanie tylko do dokładnych dopasowań. Na przykład nie będzie to miało `obs.`wpływu `obsolete`na te ciągi: , .  
 
  
5.  Prześlij potok.  

## <a name="results"></a>Wyniki

+  Aby zapisać wynikowy zestaw danych o nowej nazwie, wybierz ikonę **Zarejestruj zestaw danych** na karcie **Wyjścia** w prawym panelu modułu.  
  
## <a name="technical-notes"></a>Uwagi techniczne  

-   Każdy moduł, który przyjmuje zestaw danych jako dane wejściowe, może również przyjmować dane w pliku CSV lub pliku TSV. Przed uruchomieniem dowolnego kodu modułu dane wejściowe są wstępnie przetworzone. Przetwarzanie wstępne jest równoznaczne z uruchomieniem modułu Konwertuj na zestaw danych na danych wejściowych.  
  
-   Nie można przekonwertować z formatu SVMLight na zestaw danych.  
  
-   Podczas określania operacji zamiany niestandardowej operacja wyszukiwania i zamieniania ma zastosowanie do pełnych wartości. Częściowe dopasowania nie są dozwolone. Na przykład można zastąpić 3 na -1 lub 33, ale nie można zastąpić 3 w dwucyfrowym numerze, takim jak 35.  
  
-   W przypadku operacji zamiany niestandardowej zastąpienie będzie dyskretnie niepowodzeniem, jeśli użyjesz jako zamiennik dowolnego znaku, który nie jest zgodny z bieżącym typem danych kolumny.  

  
## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
