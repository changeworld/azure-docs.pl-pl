---
title: Wykonywanie skryptów uczenia maszynowego w języku Python
titleSuffix: Azure Machine Learning Studio
description: Dowiedz się, jak używać języka Python w usłudze Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750825"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Wykonywanie skryptów uczenia maszynowego w języku Python w usłudze Azure Machine Learning Studio

Język Python jest przydatnym narzędziem w piersi narzędzie wielu analityków danych. Jest on używany na każdym etapie przepływy pracy nauki typowej maszyny tym eksplorację danych, funkcja wyodrębniania, weryfikacji i szkoleń modelowych i wdrożenia.

W tym artykule opisano, jak użyć modułu wykonanie skryptu Python do użycia kodu w języku Python w usługach sieci web i eksperymentów w usłudze Azure Machine Learning Studio.

## <a name="using-the-execute-python-script-module"></a>Za pomocą modułu wykonanie skryptu Python

Podstawowy interfejs do języka Python w programie Studio odbywa się za pośrednictwem [wykonanie skryptu Python] [ execute-python-script] modułu. Akceptuje maksymalnie trzema elementami wejściowymi i tworzy maksymalnie dwóch danych wyjściowych, podobnie jak [wykonanie skryptu języka R] [ execute-r-script] modułu. Kod języka Python jest wprowadzana w polu parametru za pomocą funkcji specjalnie Nazwany punkt wejścia o nazwie `azureml_main`.

![Wykonanie modułu skrypt w języku Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Przykładowy kod języka python w module parametrów](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parametry wejściowe

Dane wejściowe do modułu Python są dostępne jako elementy Pandas Dataframe. `azureml_main` Funkcja akceptuje maksymalnie dwa opcjonalne elementy Pandas Dataframe jako parametry.

Mapowanie między porty wejściowe i parametry funkcji jest pozycyjne:

- Pierwszy połączonych port wejściowy jest zamapowana na pierwszy parametr funkcji.
- Drugie dane wejściowe (jeśli jest podłączony) jest mapowany na drugi parametr funkcji.
- Trzeci dane wejściowe są używane do [zaimportować dodatkowe moduły języka Python](#import-modules).

Bardziej szczegółowe semantykę jak porty wejściowe są mapowane na parametrów `azureml_main` funkcji zostały wymienione poniżej.

![Tabela konfiguracji portu wejściowego i wynikowy podpis języka Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Dane wyjściowe zwracane wartości

`azureml_main` Funkcja musi zwracać pojedynczy DataFrame Pandas spakowane w języku Python [sekwencji](https://docs.python.org/2/c-api/sequence.html) takich jak krotki, listy lub tablicy NumPy. Pierwszy element tej sekwencji jest zwracana do pierwszy port wyjściowy modułu. Drugi port wyjściowy modułu jest używany dla [wizualizacje](#visualizations) i nie wymaga wartości zwracanej. Poniżej przedstawiono ten schemat.

![Mapowania portów dla parametrów wejściowych i zwrócenia wartości do portu wyjściowego](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Tłumaczenie typów danych wejściowych i wyjściowych

Studio zestawów danych nie są takie same jak elementy Dataframe Panda. W wyniku są konwertowane na Pandas DataFrame wejściowych zestawów danych w programie Studio, a dane wyjściowe elementy Dataframe są konwertowane Studio zestawów danych. W trakcie tego procesu konwersji wykonał również następujące tłumaczenia:

 **Typ danych języka Python** | **Procedura tłumaczenia Studio** |
| --- | --- |
| Ciągi i wartości numeryczne| Przetłumaczony się |
| Pandas 'NA' | Przetłumaczone jako "Brak wartość" |
| Indeks wektorów | Nieobsługiwana * |
| Nazwy kolumn bez parametrów | Wywołaj `str` na nazwy kolumn |
| Powielone nazwy kolumn | Dodaj sufiks numeryczny: (1), (2), (3), i tak dalej.

**Wszystkie ramki danych wejściowych w funkcji języka Python zawsze mieć 64-bitowe indeksu numerycznego z zakresu od 0 do liczby wierszy, minus 1*

## <a id="import-modules"></a>Importowanie istniejących modułów skryptu języka Python

Wewnętrznej bazy danych używane do wykonywania języka Python, opiera się na [Anaconda](https://store.continuum.io/cshop/anaconda/), powszechnie używane naukowych dystrybucji języka Python. Chodzi o blisko 200 najpopularniejsze pakiety języka Python, używany w przypadku obciążeń opartych na danych. Studio nie obsługuje obecnie korzystanie z pakietu zarządzania systemów, takich jak narzędzia Pip lub Conda, aby zainstalować i zarządzać bibliotekami zewnętrznych.  Jeśli trzeba zastosować dodatkowe biblioteki, użyj następujący scenariusz jako wskazówki.

Typowy przypadek użycia jest włączenie istniejących skryptów języka Python do eksperymentów w Studio. [Wykonanie skryptu Python] [ execute-python-script] moduł przyjmuje plik zip zawierający moduły języka Python na trzeci portu wejściowego. Plik jest rozpakowano przez środowisko wykonywania w czasie wykonywania i zawartości są dodawane do ścieżki biblioteki interpreter języka Python. `azureml_main` Punktu wejścia funkcji można następnie zaimportować moduły bezpośrednio. 

Na przykład należy wziąć pod uwagę pliku Hello.py zawierający prostej funkcji "Hello, World".

![Funkcja zdefiniowana przez użytkownika w pliku Hello.py](./media/execute-python-scripts/figure4.png)

Następnie utwórz plik Hello.zip, który zawiera Hello.py:

![Plik zip zawierający kod Python zdefiniowanych przez użytkownika](./media/execute-python-scripts/figure5.png)

Przekazywanie pliku zip jako zestaw danych w programie Studio. Następnie utwórz i uruchom eksperyment, który używa kodu w języku Python w pliku Hello.zip, dołączając ją do trzeciego portem wejściowym **wykonanie skryptu Python** modułu, jak pokazano na poniższej ilustracji.

![Przykładowego eksperymentu przy użyciu Hello.zip jako dane wejściowe do modułu wykonanie skryptu Python](./media/execute-python-scripts/figure6a.png)

![Kod języka Python zdefiniowanych przez użytkownika przekazany jako plik zip](./media/execute-python-scripts/figure6b.png)

Dane wyjściowe modułu pokazują, czy plik zip został rozpakowanych i że funkcja `print_hello` została uruchomiona.

![Dane wyjściowe modułu przedstawiający funkcji zdefiniowanej przez użytkownika](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Uzyskiwanie dostępu do usługi Azure Storage blob

Aby uzyskać dostęp — dane przechowywane na koncie usługi Azure Blob Storage, wykonując następujące kroki:

1. Pobierz [pakietów usługi Azure Blob Storage dla języka Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokalnie.
1. Przekaż plik zip do obszaru roboczego usługi Studio, jako zestaw danych.
1. Tworzenie obiektu BlobService z `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Wyłącz **Wymagany bezpieczny transfer** w magazynie **konfiguracji** karcie Ustawienia

![Wyłącz bezpieczny transfer wymagane w witrynie Azure portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operacjonalizacji skryptów w języku Python

Wszelkie [wykonanie skryptu Python] [ execute-python-script] moduły używane w eksperymencie oceniania są wywoływane, gdy publikowane jako usługi sieci web. Na przykład na poniższej ilustracji przedstawiono oceniania eksperyment, który zawiera kod, aby obliczyć pojedynczego wyrażenia języka Python.

![Obszar roboczy Studio dla usługi sieci web](./media/execute-python-scripts/figure3a.png)

![Wyrażenie języka Python Pandas](./media/execute-python-scripts/python-script-with-python-pandas.png)

Utworzone na podstawie tego eksperymentu usługi sieci web może wykonać następujące czynności:

1. Wykonaj wyrażenia języka Python jako danych wejściowych (jako ciąg)
1. Wyślij wyrażenia języka Python do interpretera języka Python
1. zwraca tabelę zawierającą wyrażenie i obliczony wynik.

## <a id="visualizations"></a>Praca z wizualizacjami

Wykresy utworzone za pomocą MatplotLib mogą być zwrócone przez [wykonanie skryptu Python][execute-python-script]. Jednak wykresy automatycznie nie są przekierowywane do obrazów, ponieważ są one podczas używania języka R. Dlatego użytkownik musi jawnie zapisać żadnych wykresów w pliki PNG.

Aby wygenerować obrazów MatplotLib, należy wykonać następujące czynności:

1. Przełącz wewnętrznej bazy danych do "AGG" z domyślne renderowanie Qt.
1. Utwórz nowy obiekt rysunku.
1. Pobierz oś oraz generować wykresy wszystkich do niego.
1. Zapisz plik PNG rysunku.

Ten proces przedstawiono na poniższych ilustracjach tworzonych przy użyciu funkcji scatter_matrix w Pandas macierzy wykresu punktowego.

![Kod, aby zapisać rysunki MatplotLib obrazów](./media/execute-python-scripts/figure-v1-8.png)

![Kliknij wizualizację w module wykonanie skryptu Python, aby wyświetlić wartości](./media/execute-python-scripts/figure-v2-9a.png)

![Wizualizacja powierzchni do przykładowego eksperymentu przy użyciu kodu języka Python](./media/execute-python-scripts/figure-v2-9b.png)

Istnieje możliwość zwrócić wiele wartości, zapisując je na różne obrazy. Środowisko uruchomieniowe programu Studio zbiera wszystkie obrazy i łączy je do wizualizacji.

## <a name="advanced-examples"></a>Zaawansowane przykłady

Środowisko Anaconda, zainstalowane w programie Studio zawiera popularnych pakietów takich jak NumPy SciPy i Scikits-Learn. Te pakiety można skutecznie do przetwarzania danych w usłudze machine learning potoku.

Na przykład poniższego eksperymentu oraz skrypt ilustrują użycie uczących zespołu w Scikits — Dowiedz się, jak funkcja wyniki znaczenie dla zestawu danych wystąpień obliczeniowych. Wyniki może służyć do wykonywania funkcji nadzorowanych wyboru przed podawana do innego modelu.

W tym miejscu jest funkcją języka Python, używany do obliczania wyników znaczenie i kolejność, w oparciu o wyniki funkcji:

![Funkcja do funkcji rank, wyniki](./media/execute-python-scripts/figure8.png)

Poniższego eksperymentu następnie oblicza i zwraca wyniki znaczenie funkcji w zestawie danych "Pima indyjskim choroby" w usłudze Azure Machine Learning Studio:

![Eksperymentowanie do rangi funkcji w zestawie danych choroby indyjskim Pima przy użyciu języka Python](./media/execute-python-scripts/figure9a.png)

![Wizualizacja danych wyjściowych modułu wykonanie skryptu Python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Ograniczenia

[Wykonanie skryptu Python] [ execute-python-script] modułu obecnie ma następujące ograniczenia:

### <a name="sandboxed-execution"></a>Wykonywania w trybie piaskownicy

Środowisko uruchomieniowe języka Python jest obecnie w trybie piaskownicy, a nie zezwala na dostęp do sieci lub w lokalnym systemie plików w sposób ciągły. Wszystkie pliki zapisane lokalnie są izolowane i usunięte po zakończeniu modułu. Większość katalogów na komputerze, na których ono działa, wyjątek jest w bieżącym katalogu i jego podkatalogach kodu w języku Python nie ma dostępu.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Brak zaawansowanego tworzenia i obsługi debugowania

Moduł Python aktualnie nie obsługuje funkcje środowiska IDE, takie jak intellisense i debugowanie. Ponadto w przypadku awarii w czasie wykonywania moduł pełny ślad stosu Python jest dostępna. Ale musi być wyświetlana w dzienniku danych wyjściowych dla modułu. Firma Microsoft zaleca obecnie programowanie i debugowanie skryptów w języku Python w środowisku, takie jak IPython a następnie zaimportuj kod do modułu.

### <a name="single-data-frame-output"></a>Dane wyjściowe ramki danych jednego

Punkt wejścia Python jest dozwolony tylko do zwrócenia ramkę danych jako dane wyjściowe. Nie jest obecnie możliwe przywrócić dowolne obiekty języka Python, takie jak przeszkolone modele bezpośrednio środowisko uruchomieniowe programu Studio. Podobnie jak [wykonanie skryptu języka R][execute-r-script], która ma takie samo ograniczenie występuje, jest możliwe w wielu przypadkach pickle obiektów w tablicy bajtów, a następnie wróć, wewnątrz ramki danych.

### <a name="inability-to-customize-python-installation"></a>Brak możliwości dostosowania instalacji języka Python

Obecnie jedynym sposobem dodawania niestandardowych modułów języka Python jest za pośrednictwem mechanizmu pliku zip, opisanego wcześniej. Gdy jest to możliwe dla małych modułów, jest kłopotliwe dla dużych modułów (szczególnie moduły z natywnych bibliotek DLL) lub dużej liczby modułów.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji możesz znaleźć w [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script