---
title: 'Eksportowanie danych: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu programu Eksport danych w usłudze Azure Machine Learning, aby zapisać wyniki, danych pośrednich i dane robocze z eksperymentów do miejsca docelowe magazynu w chmurze poza usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028323"
---
# <a name="export-data-module"></a>Moduł danych eksportu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do zapisywania wyników, danych pośrednich i dane robocze z eksperymentów do miejsca docelowe magazynu w chmurze poza usługi Azure Machine Learning.

Ten moduł obsługuje wyeksportowaniem lub zapisaniem danych do następujących usług danych w chmurze:


- **Eksportowanie do usługi Azure Blob Storage**: Zapisuje dane do usługi obiektów Blob na platformie Azure. Dane w usłudze obiektów Blob można udostępniane publicznie lub zapisany w magazynach danych zabezpieczono aplikację.

  
## <a name="how-to-configure-export-data"></a>Jak skonfigurować Eksport danych

1. Dodaj **Eksport danych** modułu do eksperymentu w interfejsie. Możesz znaleźć tego modułu w **danych wejściowych i wyjściowych** kategorii.

2. Połącz **Eksport danych** do modułu, który zawiera dane, którą chcesz wyeksportować.

3. Kliknij dwukrotnie **Eksport danych** otworzyć **właściwości** okienka.

4. Aby uzyskać **docelową lokalizację danych**, wybierz typ magazynu w chmurze którym będziesz zapisywać dane. Jeśli wprowadzisz zmiany w tej opcji wszystkie pozostałe właściwości są resetowane. Dlatego należy wybrać tę opcję, najpierw!

5. Podaj konto nazwy i metodę uwierzytelniania wymagane do uzyskania dostępu określonego konta magazynu.

    **Eksportowanie do usługi Azure Blob Storage** jest jedyną opcją w prywatnej wersji zapoznawczej. Poniżej przedstawiono sposób ustawiania modułu.
    1. Usługi Azure blob service jest do przechowywania dużych ilości danych, w tym dane binarne. Istnieją dwa typy magazynu obiektów blob: publiczne obiekty BLOB i obiektów blob, które wymagają poświadczeń logowania.

    2. Aby uzyskać **typ uwierzytelniania**, wybierz **publiczny (SAS)** Jeśli wiesz, że magazyn obsługuje dostęp za pośrednictwem adresu URL sygnatury dostępu Współdzielonego.

          Adres URL sygnatury dostępu Współdzielonego to specjalny typ adresu URL, które mogą być generowane przy użyciu narzędzia usługi Azure storage i jest dostępna przez ograniczony czas.  Zawiera wszystkie informacje niezbędne do uwierzytelniania i pobierania.

        Aby uzyskać **URI**wpisz lub Wklej pełny identyfikator URI, który definiuje konto i publicznego obiektu blob.

        Format pliku CSV i TSV są obsługiwane.

    3. Dla kont prywatnej, wybierz **konta**i podaj nazwę konta i klucz konta, dzięki czemu eksperymentu można tworzyć na koncie magazynu.

         - **Nazwa konta**: Wpisz lub Wklej nazwę konta, w którym chcesz zapisać dane. Na przykład, jeśli jest pełny adres URL konta usługi storage `http://myshared.blob.core.windows.net`, należy wpisać `myshared`.

        - **Klucz konta**: Wklej klucz dostępu do magazynu skojarzonego z kontem.

        -  **Ścieżka do kontenera, katalogu lub obiektu blob**: Wpisz nazwę obiektu blob, w którym będą przechowywane wyeksportowane dane. Na przykład, aby zapisać wyniki eksperymentu do nowego obiektu blob o nazwie **results01.csv** w kontenerze **prognozy** w ramach konta o nazwie **mymldata**, pełnego adresu URL Obiekt blob może być `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            W związku z tym, w polu **ścieżka do kontenera, katalogu lub obiektu blob**, należy określić kontener i następuje nazwa obiektu blob jako: `predictions/results01.csv`

        - Jeśli określisz nazwę obiektu blob, który jeszcze nie istnieją, platforma Azure tworzy obiekt blob dla Ciebie.

       -  Podczas zapisywania do istniejącego obiektu blob, można określić, że bieżącą zawartość obiektu blob być zastąpione przez ustawienie dla właściwości **tryb zapisu w magazynie Azure blob storage**. Domyślnie wartość tej właściwości jest równa **błąd**, co oznacza, że błąd jest wywoływane zawsze wtedy, gdy zostanie znaleziony istniejący plik obiektów blob o takiej samej nazwie.


    4. Aby uzyskać **format pliku dla pliku obiektu blob**, wybierz format, w którym powinny być przechowywane dane.

        - **CSV**: Wartości rozdzielanych przecinkami (CSV) są domyślnym formatem magazynowania. Aby wyeksportować nagłówków kolumn, wraz z danymi, wybierz opcję, **wiersz nagłówka obiektu blob zapisu**.  Aby uzyskać więcej informacji na temat przecinkami rozdzielany formatu używanego w usłudze Azure Machine Learning, zobacz [Konwertuj do formatu CSV](./convert-to-csv.md).

        - **TSV**: Format wartości rozdzielane tabulatorami (TSV) jest zgodny z wielu narzędzi do uczenia maszyny. Aby wyeksportować nagłówków kolumn, wraz z danymi, wybierz opcję, **wiersz nagłówka obiektu blob zapisu**.  

 
    5. **Użyj wyników buforowanych**: Wybierz tę opcję, jeśli chcesz uniknąć ponownego zapisywania adresów wyniki w pliku obiektu blob każdorazowo, gdy uruchomienie eksperymentu. Jeśli nie istnieją żadne inne zmiany parametry modułu, eksperymentu zapisuje wyniki tylko za pierwszym razem zostanie uruchomiony moduł, lub gdy występują zmiany danych.

    6. Uruchom eksperyment.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 