---
title: 'Importowanie z usługi Azure Blob Storage: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Informacje w tym temacie opisują sposób używania importu z modułu Azure Blob Storage w usłudze Azure Machine Learning do odczytywania danych z usługi Azure Blob Storage, dzięki czemu można używać danych w potoku uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f8d23bfbee6d3665d770d8cbbcb9440827a88e8e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693736"
---
# <a name="import-from-azure-blob-storage-module"></a>Importuj z modułu Blob Storage platformy Azure

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do odczytywania danych z usługi Azure Blob Storage, dzięki czemu można używać danych w potoku uczenia maszynowego.  

Usługa Azure Blob Service służy do przechowywania dużych ilości danych, w tym danych binarnych. Dostęp do obiektów blob platformy Azure można uzyskać z dowolnego miejsca przy użyciu protokołu HTTP lub HTTPS. Uwierzytelnianie może być wymagane w zależności od typu magazynu obiektów BLOB. 

- Dostęp do publicznych obiektów BLOB jest możliwy dla każdego lub użytkowników, którzy mają adres URL sygnatury dostępu współdzielonego.
- Prywatne obiekty blob wymagają logowania i poświadczeń.

Importowanie z magazynu obiektów BLOB wymaga, aby dane były przechowywane w obiektach Blob, które używają formatu **blokowego obiektu BLOB** . Pliki przechowywane w obiekcie blob muszą używać formatów rozdzielanych przecinkami (CSV) lub tabulatorów (TSV). Podczas odczytywania pliku rekordy i wszelkie odpowiednie nagłówki atrybutów są wczytywane jako wiersze do pamięci jako zestaw danych.


Zdecydowanie zalecamy Profilowanie danych przed ich zaimportowaniem, aby upewnić się, że schemat jest zgodnie z oczekiwaniami. Proces importowania skanuje pewną liczbę wierszy głównych w celu określenia schematu, ale późniejsze wiersze mogą zawierać dodatkowe kolumny lub dane, które powodują błędy.



## <a name="manually-set-properties-in-the-import-data-module"></a>Ręczne ustawianie właściwości w module Importuj dane

W poniższych krokach opisano sposób ręcznego konfigurowania źródła importu.

1. Dodaj moduł **Import danych** do potoku. Ten moduł można znaleźć w interfejsie, w **danych wejściowych i wyjściowych** .

2. W obszarze **Źródło danych**wybierz pozycję **Azure Blob Storage**.

3. W **polu Typ uwierzytelniania**wybierz opcję **publiczny (adres URL sygnatury dostępu współdzielonego)** , Jeśli wiesz, że informacje są dostarczane jako publiczne źródło danych. Adres URL sygnatury dostępu współdzielonego jest adresem URL związanym z dostępem publicznym, który można wygenerować za pomocą narzędzia Azure Storage.

    W przeciwnym razie wybierz pozycję **konto**.

4. Jeśli dane znajdują się w **publicznym** obiekcie blob, do którego można uzyskać dostęp za pomocą adresu URL sygnatury dostępu współdzielonego, nie potrzebujesz dodatkowych poświadczeń, ponieważ ciąg adresu URL zawiera wszystkie informacje potrzebne do pobrania i uwierzytelnienia.

    W polu **Identyfikator URI** wpisz lub wklej pełny identyfikator URI, który definiuje konto i publiczny obiekt BLOB.



5. Jeśli dane są na koncie **prywatnym** , należy podać poświadczenia, takie jak nazwa konta i klucz.

    - W polu **nazwa konta**wpisz lub wklej nazwę konta, które zawiera obiekt BLOB, do którego chcesz uzyskać dostęp.

        Na przykład jeśli pełny adres URL konta magazynu jest `http://myshared.blob.core.windows.net`, wpisz `myshared`.

    - W polu **klucz konta**Wklej klucz dostępu do magazynu, który jest skojarzony z tym kontem.

        Jeśli nie znasz klucza dostępu, zapoznaj się z sekcją "Zarządzanie kontami usługi Azure Storage" w tym artykule: [Informacje o kontach usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Dla **ścieżki do kontenera, katalogu lub obiektu BLOB**wpisz nazwę określonego obiektu BLOB, który ma zostać pobrany.

    Na przykład, jeśli plik o nazwie **data01. csv** został przekazany do kontenera **trainingdata** na koncie o nazwie **mymldata**, pełny adres URL pliku będzie: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`.

    W związku z tym, w **ścieżce pola do kontenera, katalogu lub obiektu BLOB**, należy wpisać: `trainingdata/data01.csv`

    Aby zaimportować wiele plików, można użyć symboli wieloznacznych `*` (gwiazdka) lub `?` (znak zapytania).

    Na przykład przy założeniu, że kontener `trainingdata` zawiera wiele plików zgodnego formatu, można użyć następującej specyfikacji do odczytania wszystkich plików, zaczynając od `data`, i połączyć je w jeden zestaw danych:

    `trainingdata/data*.csv`

    Nie można używać symboli wieloznacznych w nazwach kontenerów. Jeśli zachodzi konieczność zaimportowania plików z wielu kontenerów, należy użyć oddzielnego wystąpienia modułu **Importuj dane** dla każdego kontenera, a następnie scalić zestawy danych przy użyciu modułu [Dodaj wiersze](./add-rows.md) .

    > [!NOTE]
    > W przypadku wybrania opcji **Użyj zbuforowanych wyników**, wszelkie zmiany wprowadzone w plikach w kontenerze nie wyzwalają odświeżania danych w potoku.

7. W polu **Format pliku BLOB**wybierz opcję, która wskazuje format danych przechowywanych w obiekcie blob, dzięki czemu Azure Machine Learning może odpowiednio przetwarzać dane. Obsługiwane są następujące formaty:

    - **CSV**: wartości rozdzielane przecinkami (CSV) to domyślny format magazynu do eksportowania i importowania plików w Azure Machine Learning. Jeśli dane zawierają już wiersz nagłówka, pamiętaj, aby wybrać opcję, **plik ma wiersz nagłówka**lub nagłówek będzie traktowany jako wiersz danych.

       

    - **TSV**: wartości rozdzielane znakami tabulacji (tsv) są formatem używanym przez wiele narzędzi uczenia maszynowego. Jeśli dane zawierają już wiersz nagłówka, pamiętaj, aby wybrać opcję, **plik ma wiersz nagłówka**lub nagłówek będzie traktowany jako wiersz danych.

       

    - **ARFF**: ten format obsługuje importowanie plików w formacie używanym przez zestaw narzędzi Weka. 

   

8. Uruchamianie potoku.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 