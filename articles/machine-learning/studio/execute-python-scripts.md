---
title: Wykonywanie skryptów w języku Python
titleSuffix: ML Studio (classic) - Azure
description: Dowiedz się, jak używać modułu Wykonywanie skryptu języka Python do używania kodu Języka Python w eksperymentach i usługach internetowych usługi machine learning studio (klasyczne).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218080"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Wykonywanie skryptów uczenia maszynowego języka Python w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python jest cennym narzędziem w skrzyni narzędzi wielu analityków danych. Jest on używany na każdym etapie typowych przepływów pracy uczenia maszynowego, w tym eksploracji danych, wyodrębniania funkcji, szkolenia modelu i sprawdzania poprawności oraz wdrażania.

W tym artykule opisano, jak można użyć modułu Wykonywanie skryptu języka Python do używania kodu języka Python w eksperymentach i usługach sieci Web usługi Azure Machine Learning Studio (klasyczne).

## <a name="using-the-execute-python-script-module"></a>Korzystanie z modułu Wykonywanie skryptu języka Python

Podstawowy interfejs pythona w Studio (klasyczny) jest za pośrednictwem modułu [Wykonywanie skryptu Pythona.][execute-python-script] Akceptuje do trzech wejść i produkuje do dwóch wyjść, podobnie jak [execute r script][execute-r-script] modułu. Kod języka Python jest wprowadzany w polu parametrów za `azureml_main`pośrednictwem specjalnie nazwanej funkcji punktu wejścia o nazwie .

![Wykonywanie modułu skryptu Języka Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Przykładowy kod języka python w polu parametrów modułu](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parametry wejściowe

Dane wejściowe do modułu Python są udostępniane jako Pandas DataFrames. Funkcja `azureml_main` akceptuje maksymalnie dwa opcjonalne Pandas DataFrames jako parametry.

Mapowanie między portami wejściowymi a parametrami funkcji ma położenie:

- Pierwszy podłączony port wejściowy jest mapowany na pierwszy parametr funkcji.
- Drugie wejście (jeśli jest podłączone) jest mapowane na drugi parametr funkcji.
- Trzecie wejście służy do [importowania dodatkowych modułów Pythona](#import-modules).

Poniżej przedstawiono bardziej szczegółową semantycę sposobu `azureml_main` mapowania portów wejściowych na parametry funkcji.

![Tabela konfiguracji portów wejściowych i wynikowy podpis języka Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Wyjściowe wartości zwrotu

Funkcja `azureml_main` musi zwracać pojedynczy Pandas DataFrame spakowane w [sekwencji](https://docs.python.org/2/c-api/sequence.html) Języka Python, takich jak krotka, lista lub NumPy tablicy. Pierwszy element tej sekwencji jest zwracany do pierwszego portu wyjściowego modułu. Drugi port wyjściowy modułu jest używany do [wizualizacji](#visualizations) i nie wymaga wartości zwracanej. Schemat ten przedstawiono poniżej.

![Mapowanie portów wejściowych na parametry i zwracanie wartości do portu wyjściowego](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Tłumaczenie typów danych wejściowych i wyjściowych

Zestawy danych Studio nie są takie same jak Panda DataFrames. W rezultacie wejściowe zestawy danych w Studio (klasyczne) są konwertowane na Pandas DataFrame, a wyjściowe dataframe są konwertowane z powrotem do zestawów danych Studio (klasycznych). Podczas tego procesu konwersji wykonywane są również następujące tłumaczenia:

 **Typ danych języka Python** | **Procedura tłumaczenia studyjnego** |
| --- | --- |
| Ciągi i numeryki| Przetłumaczone tak, jak jest |
| Pandy 'NA' | Przetłumaczone jako "Brak wartości" |
| Wektory indeksu | Nieobsługiwały się* |
| Nazwy kolumn bez ciągu | Zadzwoń `str` do nazw kolumn |
| Zduplikowane nazwy kolumn | Dodaj sufiks numeryczny: (1), (2), (3) i tak dalej.

**Wszystkie klatki danych wejściowych w funkcji Pythona mają zawsze 64-bitowy indeks numeryczny od 0 do liczby wierszy minus 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importowanie istniejących modułów skryptów języka Python

Backend używany do wykonywania Pythona opiera się na [Anaconda](https://www.anaconda.com/distribution/), szeroko stosowanej naukowej dystrybucji Pythona. Zawiera blisko 200 najpopularniejszych pakietów języka Python używanych w obciążeniach zorientowanych na dane. Studio (classic) obecnie nie obsługuje korzystania z systemów zarządzania pakietami, takich jak Pip lub Conda do instalowania i zarządzania bibliotekami zewnętrznymi.  Jeśli okaże się, że trzeba włączyć dodatkowe biblioteki, użyj następującego scenariusza jako przewodnika.

Typowym przypadkiem użycia jest włączenie istniejących skryptów języka Python do eksperymentów studio (klasyczne). Moduł [Execute Python Script][execute-python-script] akceptuje plik zip zawierający moduły Języka Python na trzecim porcie wejściowym. Plik jest rozpakowany przez strukturę wykonywania w czasie wykonywania, a zawartość są dodawane do ścieżki biblioteki interpretera języka Python. Funkcja `azureml_main` punktu wejścia może następnie importować te moduły bezpośrednio. 

Na przykład należy wziąć pod uwagę plik Hello.py zawierający prostą funkcję "Hello, World".

![Funkcja zdefiniowana przez użytkownika w pliku Hello.py](./media/execute-python-scripts/figure4.png)

Następnie tworzymy plik Hello.zip, który zawiera Hello.py:

![Plik pocztowy zawierający zdefiniowany przez użytkownika kod Języka Python](./media/execute-python-scripts/figure5.png)

Przekaż plik zip jako zestaw danych do Studio (klasyczny). Następnie utwórz i uruchom eksperyment, który używa kodu Języka Python w pliku Hello.zip, dołączając go do trzeciego portu wejściowego modułu **Wykonywanie skryptu Języka Python,** jak pokazano na poniższej ilustracji.

![Przykładowy eksperyment z Hello.zip jako dane wejściowe do modułu Wykonywanie skryptu Języka Python](./media/execute-python-scripts/figure6a.png)

![Zdefiniowany przez użytkownika kod Pythona przesłany jako plik zip](./media/execute-python-scripts/figure6b.png)

Dane wyjściowe modułu pokazują, że plik zip został `print_hello` rozpakowany i że funkcja została uruchomiony.

![Wyjście modułu z funkcją zdefiniowaną przez użytkownika](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Uzyskiwanie dostępu do obiektów blob usługi Azure Storage

Możesz uzyskać dostęp do danych przechowywanych na koncie usługi Azure Blob Storage, wykonując następujące kroki:

1. Pobierz [pakiet usługi Azure Blob Storage dla języka Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokalnie.
1. Przekaż plik zip do obszaru roboczego Studio (klasycznego) jako zestaw danych.
1. Tworzenie obiektu BlobService za pomocą`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Wyłącz **bezpieczne przesyłanie wymagane** na karcie Ustawienia **konfiguracji** magazynu

![Wyłącz bezpieczne transfery wymagane w witrynie Azure portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operacjonalizacja skryptów języka Python

Wszelkie [moduły skryptu wykonywania języka Python][execute-python-script] używane w eksperymencie oceniania są wywoływane po opublikowaniu jako usługa sieci web. Na przykład na poniższej ilustracji przedstawiono eksperyment oceniania, który zawiera kod do oceny pojedynczego wyrażenia Języka Python.

![Obszar roboczy studio dla usługi sieci web](./media/execute-python-scripts/figure3a.png)

![Wyrażenie Python Pandas](./media/execute-python-scripts/python-script-with-python-pandas.png)

Usługa sieci web utworzona na podstawie tego eksperymentu podejmie następujące działania:

1. Weź wyrażenie Języka Python jako dane wejściowe (jako ciąg)
1. Wyślij wyrażenie Pythona do interpretera języka Python
1. Zwraca tabelę zawierającą zarówno wyrażenie, jak i obliczony wynik.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Praca z wizualizacjami

Wykresy utworzone za pomocą MatplotLib mogą być zwracane przez [skrypt Wykonywania języka Python][execute-python-script]. Jednak wykresy nie są automatycznie przekierowywane do obrazów, jak są one podczas korzystania z języka R. Dlatego użytkownik musi jawnie zapisać wszelkie wykresy w plikach PNG.

Aby wygenerować obrazy z matplotLib, należy wykonać następujące kroki:

1. Przełącz wewnętrznej bazy danych na "AGG" z domyślnego modułu renderowania opartego na Qt.
1. Utwórz nowy obiekt rysunkowy.
1. Pobierz oś i wygeneruj do niej wszystkie wykresy.
1. Zapisz rysunek w pliku PNG.

Ten proces jest zilustrowany na poniższych obrazach, które tworzą macierz wydruku punktowego przy użyciu funkcji scatter_matrix w pandach.

![Kod do zapisywania figur MatplotLib na obrazach](./media/execute-python-scripts/figure-v1-8.png)

![Kliknij wizualizację modułu Wykonywanie skryptu Pythona, aby wyświetlić rysunki](./media/execute-python-scripts/figure-v2-9a.png)

![Wizualizacja wykresów dla przykładowego eksperymentu przy użyciu kodu języka Python](./media/execute-python-scripts/figure-v2-9b.png)

Możliwe jest zwrócenie wielu liczb, zapisując je na różnych obrazach. Studio (klasyczne) środowisko uruchomieniowe pobiera wszystkie obrazy i łączy je w celu wizualizacji.

## <a name="advanced-examples"></a>Przykłady zaawansowane

Środowisko Anaconda zainstalowane w Studio (klasyczny) zawiera typowe pakiety, takie jak NumPy, SciPy i Scikits-Learn. Te pakiety mogą być skutecznie używane do przetwarzania danych w potoku uczenia maszynowego.

Na przykład poniższy eksperyment i skrypt ilustrują użycie uczniów zespołu w Scikits-Learn obliczyć wyniki ważności funkcji dla zestawu danych. Wyniki mogą być używane do wykonywania nadzorowanego wyboru funkcji przed wprowadzeniem do innego modelu.

Oto funkcja Pythona używana do obliczania wyników ważności i porządkowania funkcji na podstawie wyników:

![Funkcja klasyfikowania obiektów według wyników](./media/execute-python-scripts/figure8.png)

Poniższy eksperyment następnie oblicza i zwraca wyniki ważności funkcji w zestawie danych "Pima Indian Diabetes" w usłudze Azure Machine Learning Studio (klasyczny):

![Eksperymentuj, aby uszeregować funkcje w zestawie danych Pima Indian Diabetes przy użyciu języka Python](./media/execute-python-scripts/figure9a.png)

![Wizualizacja danych wyjściowych modułu Wykonywanie skryptu Pythona](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Ograniczenia

Moduł [Wykonywanie skryptu języka Python][execute-python-script] ma obecnie następujące ograniczenia:

### <a name="sandboxed-execution"></a>Wykonanie w trybie piaskownicy

Środowisko wykonawcze języka Python jest obecnie piaskownicy i nie zezwala na dostęp do sieci lub lokalnego systemu plików w sposób trwały. Wszystkie pliki zapisane lokalnie są izolowane i usuwane po zakończeniu modułu. Kod języka Python nie może uzyskać dostępu do większości katalogów na komputerze, na który działa, wyjątek jest bieżący katalog i jego podkatalogów.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Brak zaawansowanego wsparcia w zakresie rozwoju i debugowania

Moduł Języka Python obecnie nie obsługuje funkcji IDE, takich jak intellisense i debugowania. Ponadto jeśli moduł ulegnie awarii w czasie wykonywania, dostępny jest pełny śledzenia stosu języka Python. Ale musi być oglądany w dzienniku wyjściowym dla modułu. Obecnie zaleca się tworzenie i debugowanie skryptów języka Python w środowisku takim jak IPython, a następnie importowanie kodu do modułu.

### <a name="single-data-frame-output"></a>Wyjście z pojedynczą ramką danych

Punkt wejścia języka Python jest dozwolone tylko do zwrócenia pojedynczej ramki danych jako dane wyjściowe. Obecnie nie jest możliwe zwracanie dowolnych obiektów języka Python, takich jak wyszkolone modele bezpośrednio z powrotem do środowiska wykonawczego Studio (klasycznego). Podobnie jak [Wykonanie skryptu R][execute-r-script], który ma takie samo ograniczenie, jest możliwe w wielu przypadkach pikle obiektów do tablicy bajtowej, a następnie zwrócić, że wewnątrz ramki danych.

### <a name="inability-to-customize-python-installation"></a>Niemożność dostosowania instalacji języka Python

Obecnie jedynym sposobem na dodanie niestandardowych modułów Pythona jest opisany wcześniej mechanizm plików zip. Chociaż jest to możliwe w przypadku małych modułów, jest to uciążliwe dla dużych modułów (zwłaszcza modułów z natywnymi bibliotekami DLL) lub dużej liczby modułów.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
