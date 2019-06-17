---
title: 'Importuj z usługi Azure Blob Storage: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, że w tym temacie opisano sposób użycia importowania danych z modułu usługi Azure Blob Storage w usłudze Azure Machine Learning można odczytać danych z magazynu obiektów blob platformy Azure, dzięki czemu można użyć danych w eksperymentu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029688"
---
# <a name="import-from-azure-blob-storage-module"></a>Importuj z modułu usługi Azure Blob Storage

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do odczytywania danych z magazynu obiektów blob platformy Azure, dzięki czemu można użyć danych w eksperymentu uczenia maszynowego.  

Azure Blob Service jest do przechowywania dużych ilości danych, w tym dane binarne. Obiekty BLOB platformy Azure jest możliwy z dowolnego miejsca i przy użyciu protokołu HTTP lub HTTPS. Uwierzytelnianie może być wymagane w zależności od typu magazynu obiektów blob. 

- Publiczne obiekty BLOB są dostępne dla wszystkich osób lub przez użytkowników, którzy mają adres URL sygnatury dostępu Współdzielonego.
- Prywatne obiekty BLOB wymagają identyfikatora logowania i poświadczenia.

Importowanie z magazynu obiektów blob wymaga, że dane przechowywane w obiektach blob, które używają **blokowych obiektów blob** formatu. Rozdzielana przecinkami (CSV) lub formaty rozdzielane tabulatorami (TSV), należy użyć plików przechowywanych w obiekcie blob. Podczas odczytywania pliku rekordy i nagłówki, zastosowanie atrybutu są ładowane jako wiersze do pamięci jako zestaw danych.


Zdecydowanie zaleca się profilowanie danych przed zaimportowaniem, aby upewnić się, że schemat jest zgodnie z oczekiwaniami. Proces importowania skanuje pewnej liczby głównego wiersze, aby ustalić schemat, ale nowszych wierszy może zawierać dodatkowe kolumny lub dane, które powodują błędy.



## <a name="manually-set-properties-in-the-import-data-module"></a>Ręcznie Ustaw właściwości modułu importu danych

Poniżej opisano jak ręcznie skonfigurować źródło importu.

1. Dodaj **importu danych** modułu do eksperymentu. Ten moduł w interfejsie, można znaleźć w **danych wejściowych i wyjściowych**

2. Aby uzyskać **źródła danych**, wybierz opcję **usługi Azure Blob Storage**.

3. Aby uzyskać **typ uwierzytelniania**, wybierz **publicznego (adres URL sygnatury dostępu Współdzielonego)** Jeśli wiesz, że podano informacje jako publiczne źródło danych. Adres URL sygnatury dostępu Współdzielonego jest czasowo adresu URL dla dostępu publicznego, który można wygenerować za pomocą narzędzia usługi Azure storage.

    W przeciwnym razie wybierz **konta**.

4. Jeśli dane pochodzą z **publicznych** obiektów blob, który jest możliwy przy użyciu adresu URL sygnatury dostępu Współdzielonego, nie są potrzebne dodatkowe poświadczenia, ponieważ ciąg adresu URL zawiera wszystkie informacje potrzebne do pobierania i uwierzytelniania.

    W **URI** polu wpisz lub Wklej pełny identyfikator URI, który definiuje konto i publicznego obiektu blob.



5. Jeśli dane pochodzą z **prywatnej** konta, musisz podać poświadczenia, takie jak nazwa konta i klucz.

    - Aby uzyskać **nazwa konta**, wpisz lub Wklej nazwę konta, które zawiera chcesz uzyskać dostęp do obiektu blob.

        Na przykład, jeśli jest pełny adres URL konta usługi storage `http://myshared.blob.core.windows.net`, należy wpisać `myshared`.

    - Aby uzyskać **klucz konta**, Wklej klucz dostępu do magazynu skojarzonego z kontem.

        Jeśli nie znasz klawisz dostępu, zobacz sekcję, "Zarządzanie kontem magazynu platformy Azure" w tym artykule: [O kontach magazynu Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Aby uzyskać **ścieżka do kontenera, katalogu lub obiektu blob**, wpisz nazwę konkretny obiekt blob, który ma zostać pobrane.

    Na przykład, jeśli został przekazany plik o nazwie **data01.csv** do kontenera **trainingdata** w ramach konta o nazwie **mymldata**, pełny adres URL dla pliku: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    W związku z tym, w polu **ścieżka do kontenera, katalogu lub obiektu blob**, należy wpisać: `trainingdata/data01.csv`

    Aby zaimportować wiele plików, można użyć symboli wieloznacznych `*` (gwiazdka) lub `?` (znak zapytania).

    Na przykład, zakładając, że kontener `trainingdata` zawiera wiele plików niezgodny format, można użyć do odczytania wszystkich plików, począwszy od następującą specyfikację `data`oraz łączenie ich w jeden zestaw danych:

    `trainingdata/data*.csv`

    Nie można używać symboli wieloznacznych w nazwach kontenerów. Jeśli zachodzi potrzeba importować pliki z wielu kontenerów, należy użyć oddzielnego wystąpienia **Import danych** modułu dla każdego kontenera, a następnie scalania do zestawów danych przy użyciu [Dodaj wiersze](./add-rows.md) modułu.

    > [!NOTE]
    > W przypadku wybrania opcji **użycia pamięci podręcznej wyniki**, wszelkie zmiany wprowadzone do plików w kontenerze nie wyzwalać odświeżanie danych w eksperymencie.

7. Aby uzyskać **format pliku obiektu Blob**, wybierz opcję wskazującą format danych, która jest przechowywana w obiekcie blob, tak aby usługa Azure Machine Learning można przetwarzać dane odpowiednio. Obsługiwane są następujące formaty:

    - **CSV**: Wartości rozdzielanych przecinkami (CSV) czy domyślny format magazynu eksportowanie i importowanie plików w usłudze Azure Machine Learning. Jeśli dane są już zawiera wiersz nagłówka, pamiętaj o wybraniu opcji **plik ma wiersz nagłówka**, lub nagłówek będzie traktowana jako wiersz danych.

       

    - **TSV**: Wartości rozdzielane tabulatorami (TSV) są to format używany przez wiele narzędzi do uczenia maszynowego. Jeśli dane są już zawiera wiersz nagłówka, pamiętaj o wybraniu opcji **plik ma wiersz nagłówka**, lub nagłówek będzie traktowana jako wiersz danych.

       

    - **ARFF**: Ten format obsługuje importowanie plików w formacie używanym przez zestaw narzędzi Weka. 

   

8. Uruchom eksperyment.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 