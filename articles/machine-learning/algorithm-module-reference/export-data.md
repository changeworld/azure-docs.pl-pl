---
title: 'Eksportuj dane: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modułu eksport danych w usłudze Azure Machine Learning, aby zapisywać wyniki, dane pośrednie i dane robocze z eksperymentów do miejsc docelowych magazynu w chmurze poza Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a4fb539f4c86d27813b60964794fc1f398d3f2a4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128776"
---
# <a name="export-data-module"></a>Eksportuj moduł danych

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do zapisywania wyników, danych pośrednich i danych roboczych z eksperymentów do miejsc docelowych magazynu w chmurze spoza Azure Machine Learning.

Ten moduł obsługuje Eksportowanie lub zapisywanie danych do następujących usług danych w chmurze:


- **Eksportuj do BLOB Storage platformy Azure**: Zapisuje dane w Blob service na platformie Azure. Dane w Blob service mogą być udostępniane publicznie lub zapisywane w zabezpieczonych magazynach danych aplikacji.

  
## <a name="how-to-configure-export-data"></a>Jak skonfigurować dane eksportu

1. Dodaj moduł **eksportu danych** do eksperymentu w interfejsie. Ten moduł można znaleźć w kategorii **dane wejściowe i wyjściowe** .

2. Połącz **eksport danych** z modułem zawierającym dane, które chcesz wyeksportować.

3. Kliknij dwukrotnie pozycję **Eksportuj dane** , aby otworzyć okienko **Właściwości** .

4. W polu **miejsce docelowe danych**wybierz typ magazynu w chmurze, w którym będziesz zapisywać dane. Po wprowadzeniu zmian w tej opcji wszystkie inne właściwości zostaną zresetowane. Pamiętaj, aby najpierw wybrać tę opcję.

5. Podaj nazwę konta i metodę uwierzytelniania wymagane w celu uzyskania dostępu do określonego konta magazynu.

    **Eksport do platformy Azure Blob Storage** jest jedyną opcją w prywatnej wersji zapoznawczej. Poniżej przedstawiono sposób ustawiania modułu.
    1. Usługa Azure Blob Service służy do przechowywania dużych ilości danych, w tym danych binarnych. Istnieją dwa typy obiektów BLOB Storage: publiczne obiekty blob i obiekty blob, które wymagają poświadczeń logowania.

    2. W **polu Typ uwierzytelniania**wybierz pozycję **Public (SAS)** , Jeśli wiesz, że magazyn obsługuje dostęp za pośrednictwem adresu URL sygnatury dostępu współdzielonego.

          Adres URL sygnatury dostępu współdzielonego jest specjalnym typem adresu URL, który można wygenerować za pomocą narzędzia Azure Storage, i jest dostępny tylko przez ograniczony czas.  Zawiera wszystkie informacje, które są niezbędne do uwierzytelniania i pobierania.

        W polu **Identyfikator URI**wpisz lub wklej pełny identyfikator URI, który definiuje konto i publiczny obiekt BLOB.

        W przypadku formatu pliku pliki CSV i TSV są obsługiwane.

    3. W przypadku kont prywatnych wybierz pozycję **konto**, a następnie podaj nazwę konta i klucz konta, aby eksperyment mógł zapisywać dane na koncie magazynu.

         - **Nazwa konta**: Wpisz lub wklej nazwę konta, w którym chcesz zapisać dane. Na przykład jeśli pełny adres URL konta magazynu to `http://myshared.blob.core.windows.net`, należy wpisać. `myshared`

        - **Klucz konta**: Wklej klucz dostępu do magazynu, który jest skojarzony z tym kontem.

        -  **Ścieżka do kontenera, katalogu lub obiektu BLOB**: Wpisz nazwę obiektu BLOB, w którym będą przechowywane eksportowane dane. Na przykład, aby zapisać wyniki eksperymentu do nowego obiektu BLOB o nazwie **results01. csv** w prognozach kontenera na koncie o nazwie **mymldata**, pełny adres URL dla obiektu BLOB mógłby być `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            W związku z tym w **ścieżce pola do kontenera, katalogu lub obiektu BLOB**należy określić nazwę kontenera i obiektu BLOB w następujący sposób:`predictions/results01.csv`

        - Jeśli określisz nazwę obiektu BLOB, który jeszcze nie istnieje, platforma Azure utworzy obiekt BLOB.

       -  Podczas zapisywania do istniejącego obiektu BLOB można określić, że bieżąca zawartość obiektu BLOB zostanie zastąpiona przez ustawienie właściwości, **tryb zapisu usługi Azure Blob Storage**. Domyślnie ta właściwość ma wartość **Error**, co oznacza, że błąd jest zgłaszany za każdym razem, gdy zostanie znaleziony istniejący plik BLOB o tej samej nazwie.


    4. W polu **Format pliku obiektu BLOB**wybierz format, w którym mają być przechowywane dane.

        - **WOLUMIN CSV**: Wartości rozdzielane przecinkami (CSV) to domyślny format przechowywania. Aby wyeksportować nagłówki kolumn razem z danymi, wybierz opcję, **Zapisz wiersz nagłówka obiektu BLOB**.  Aby uzyskać więcej informacji o formacie rozdzielanym przecinkami używanym w Azure Machine Learning, zobacz [konwertowanie do formatu CSV](./convert-to-csv.md).

        - **TSV**: Format wartości rozdzielanych znakami tabulacji (TSV) jest zgodny z wieloma narzędziami uczenia maszynowego. Aby wyeksportować nagłówki kolumn razem z danymi, wybierz opcję, **Zapisz wiersz nagłówka obiektu BLOB**.  

 
    5. **Użyj zbuforowanych wyników**: Zaznacz tę opcję, jeśli chcesz uniknąć ponownego zapisywania wyników w pliku obiektu BLOB przy każdym uruchomieniu eksperymentu. Jeśli nie ma żadnych innych zmian parametrów modułu, eksperyment zapisuje wyniki tylko przy pierwszym uruchomieniu modułu lub po wprowadzeniu zmian w danych.

    6. Uruchom eksperyment.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 