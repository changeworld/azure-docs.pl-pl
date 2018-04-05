---
title: Wykonywanie skryptów programu Python machine learning | Dokumentacja firmy Microsoft
description: Przedstawiono projektowania zasad podstawowej obsługi skryptów języka Python w usłudze Azure Machine Learning i scenariusze użycia podstawowe, możliwości i ograniczeń.
keywords: Python uczenia maszynowego, pandas, pandas języka python, skrypty języka python, wykonywanie skryptów języka python
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 0efa2a7604b41b791ed3717b7f2b667b7181085d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Wykonywanie skryptów uczenia maszynowego w języku Python w usłudze Azure Machine Learning Studio

W tym temacie opisano zasady projektowania podstawowy bieżącego obsługę skryptów języka Python w usłudze Azure Machine Learning. Główne dostępnych możliwości opisano również, w tym:

- wykonanie scenariusze użycia podstawowe
- wynik eksperymentu w usłudze sieci web
- Obsługa importowania istniejącego kodu
- Eksportuj wizualizacji
- Wykonaj nadzorowane funkcji wyboru
- zrozumieć następujące ograniczenia

[Python](https://www.python.org/) jest narzędziem niezbędne w piersi narzędzie z wielu analityków danych. Posiada:

* Składnia elegancki i zwięzłe 
* Obsługa platform 
* zbiór przeważająca zaawansowanych bibliotek i 
* Narzędzia deweloperskie dojrzała. 

Python jest używany we wszystkich fazach zwykle używanych w machine learning modelowania przepływu pracy:

- pozyskiwania danych i przetwarzania 
- Funkcja konstrukcji
- szkolenie modelu 
- Weryfikacja modelu
- Wdrażanie modeli

Azure Machine Learning Studio obsługuje osadzania skrypty języka Python do różnych części maszyny eksperymentu uczenia, a także bezproblemowo opublikować go jako usługi sieci web w systemie Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Projektowanie zasad skrypty języka Python w uczeniu maszynowym

Podstawowy interfejs do języka Python w usłudze Azure Machine Learning Studio odbywa się za pośrednictwem [wykonanie skryptu Python] [ execute-python-script] modułu pokazany na rysunku 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Rysunek 1. **Wykonanie skryptu Python** modułu.

[Wykonanie skryptu Python] [ execute-python-script] modułu w usłudze Azure ML Studio akceptuje maksymalnie trzech danych wejściowych i tworzy wyjścia do dwóch (omówiona w następnej sekcji), takie jak jego analogowy R [wykonanie skryptu języka R] [ execute-r-script] modułu. Kod języka Python, który będzie wykonywany jest wprowadzany do parametrów jako nazwane specjalnie punktu wejścia funkcji o nazwie `azureml_main`. W tym miejscu są używane do implementowania ten moduł zasad kluczy:

1. *Musi być idiomatyczne dla użytkowników języka Python.* Większość użytkowników Python współczynnika ich kodu jako funkcje w modułach. Dlatego przełączenie dużo wykonywanych instrukcji w module najwyższego poziomu jest stosunkowo rzadkie. W związku z tym pole skryptu ma również specjalnej funkcji Python a nie tylko sekwencji instrukcji. Obiekty widoczne w funkcji są standardowe typy biblioteka języka Python, takich jak [Pandas](http://pandas.pydata.org/) ramek danych i [NumPy](http://www.numpy.org/) tablic.
2. *Musi mieć o wysokiej wierności między lokalnym i w chmurze wykonania.* Używane do wykonywania kodu Python wewnętrznej bazy danych jest oparty na [Anaconda](https://store.continuum.io/cshop/anaconda/), powszechnie używane i platform naukowych dystrybucję oprogramowania Python. Pochodzi on z bliski 200 najbardziej typowych pakietów języka Python. W związku z tym analityków danych może debugowania i kwalifikuj ich kodu w środowisku Azure Machine Learning zgodnego Anaconda lokalnego. Następnie użyj istniejącego środowiska programowania, takiego jak [IPython](http://ipython.org/) notesu lub [narzędzi Python Tools for Visual Studio](http://aka.ms/ptvs), aby uruchomić go w ramach eksperymentu uczenia Maszynowego Azure. `azureml_main` Punktu wejścia jest podstawowego funkcja Python i dlatego *** można tworzyć bez usługi Azure ML unikatowy kod lub zainstalowany zestaw SDK.
3. *Musi być bezproblemowe zezwala na składanie z innych modułów uczenia maszynowego Azure.* [Wykonanie skryptu Python] [ execute-python-script] modułu akceptuje jako wejściach i wyjściach, standardowe zestawy danych usługi Azure Machine Learning. Podstawowej struktury przezroczyste i efektywnie pomost środowisk uruchomieniowych uczenie Maszynowe Azure i Python. Python można więc w połączeniu z istniejących uczenie Maszynowe Azure przepływów pracy, włącznie z tymi, które wywołują R i SQLite. Wynik, naukowca danych tworzą przepływy pracy który:
   * Użyj Python i Pandas danych, przetwarzanie wstępne i czyszczenia
   * strumieniowe źródło danych transformację SQL dołączenie wiele zestawów danych do funkcji formularza
   * modele Train przy użyciu algorytmy w usłudze Azure Machine Learning 
   * Oceń i po przetwarzaniu wyniki za pomocą R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Scenariusze użycia podstawowe w ML skrypty języka Python

W tej sekcji możemy badania niektórych podstawowych zastosowań [wykonanie skryptu Python] [ execute-python-script] modułu. Dane wejściowe, aby moduł Python są widoczne jako Pandas ramek danych. Funkcja musi zwracać jedną ramkę danych Pandas umieszczone wewnątrz Python [sekwencji](https://docs.python.org/2/c-api/sequence.html) przykład spójnej kolekcji, listy lub tablicy NumPy. Pierwszy element tej sekwencji jest następnie zwracany w pierwszy port wyjściowy modułu. Ten schemat jest pokazany na rysunku 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Rysunek 2. Mapowanie portów do parametrów wejściowych i zwrócić wartość portu w danych wyjściowych.

Bardziej szczegółowe semantykę sposobie porty wejściowe pobrać mapowania parametrów `azureml_main` funkcji są wyświetlane w tabeli 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabela 1. Mapowanie portów wejściowych do parametrów funkcji.

Mapowanie między porty wejściowe i parametry funkcji jest pozycyjnych:

- Pierwszy port wejściowy połączonych jest mapowana na pierwszy parametr funkcji. 
- Drugi parametr funkcji jest mapowany drugiej danej wejściowej (Jeśli połączono).

Zobacz *Python do analizy danych* (O'Reilly 2012) przez McKinney Zach. Aby uzyskać więcej informacji na temat Python Pandas i jak może służyć do modyfikowania danych efektywne. 


## <a name="translation-of-input-and-output-types"></a>Tłumaczenie typy wejściowe i wyjściowe 
Wejściowe zestawy danych w uczenie Maszynowe Azure są konwertowane na ramek danych w Pandas. Ramek danych wyjściowych jest konwertowana z powrotem do zestawów danych uczenie Maszynowe Azure. Wykonywane są następujące konwersji:

1. Ciąg, jak i numeryczne kolumn są konwertowane na — jest i brakujące wartości w zestawie danych są konwertowane do wartości "NA" w Pandas. Tej samej Konwersja odbywa się w drodze Wstecz (wartości NA Pandas są konwertowane na brakujących wartości w uczenie Maszynowe Azure).
2. Wektory indeksu w Pandas nie są obsługiwane w uczenie Maszynowe Azure. Wszystkie ramki danych wejściowych w funkcji języka Python zawsze ma 64-bitowych wartości liczbowych indeksu z zakresu od 0 do liczby wierszy pomniejszonej o 1. 
3. Azure ML zestawy danych nie może zawierać zduplikowanych nazw kolumn i nazwy kolumn, które nie są ciągami. Jeśli ramki danych wyjściowych zawiera nieliczbowy kolumn, struktura wywołuje `str` na nazwy kolumn. Podobnie zduplikowanych nazw kolumn automatycznie są zniekształcone aby upewnić się, że nazwy są unikatowe. Sufiks (2) jest dodawana do pierwszego duplikat, (3) do drugiego duplikat i tak dalej.


## <a name="operationalizing-python-scripts"></a>Operacyjnych skrypty języka Python

Wszelkie [wykonanie skryptu Python] [ execute-python-script] moduły używane w eksperymencie oceniania są wywoływane, gdy publikowane jako usługi sieci web. Przykładowo rysunek 3 przedstawia oceniania eksperymentu, która zawiera kod, aby obliczyć pojedynczego wyrażenia języka Python. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Rysunek 3. Usługa sieci Web do obliczenia wyrażenia języka Python.

Utworzone na podstawie tego eksperymentu usługi sieci web:

- przyjmuje jako dane wejściowe wyrażenia języka Python (jako ciąg)
- wysyła on interpreter języka Python 
- zwraca tabelę zawierającą zarówno wyrażenie i obliczony wynik.


## <a name="importing-existing-python-script-modules"></a>Importowanie istniejących moduły skryptu języka Python

Typowe przypadek użycia dla wielu analityków danych jest włączenie istniejących skryptów języka Python do eksperymentów uczenie Maszynowe Azure. Zamiast konieczności czy cały kod jest połączonych i wkleić w polu jednego skryptu [wykonanie skryptu Python] [ execute-python-script] modułu akceptuje plik zip, który zawiera modułów środowiska Python w trzecim portu wejściowego. Rozpakowane jest plik przez platformę wykonywania w czasie wykonywania i zawartości zostaną dodane do ścieżki biblioteki interpreter języka Python. `azureml_main` Punktu wejścia funkcji można następnie zaimportować te moduły bezpośrednio.

Na przykład należy wziąć pod uwagę pliku Hello.py zawierający funkcję prosty "tekst Hello, World".

![image6](./media/execute-python-scripts/figure4.png)

Rysunek 4. Funkcja zdefiniowana przez użytkownika w pliku Hello.py.

Następnie utwórz plik Hello.zip, który zawiera Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Rysunek 5. Plik zip zawierający zdefiniowane przez użytkownika kod języka Python.

Przekaż plik zip jako zestawu danych w usłudze Azure Machine Learning Studio. Utwórz i uruchom eksperymentu, która korzysta z kodu języka Python w pliku Hello.zip przy dołączeniu go do trzeciego port wejściowy z **wykonanie skryptu Python** modułu, jak pokazano na poniższym rysunku.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Rysunek 6. Przekazano eksperymentu przykładowego kodu Python zdefiniowane przez użytkownika jako plik zip.

Dane wyjściowe modułu pokazuje, że plik zip został rozpakowanych, a funkcja `print_hello` została uruchomiona.
 
![image10](./media/execute-python-scripts/figure7.png)

Rysunek 7. Zdefiniowane przez użytkownika funkcja używana wewnątrz [wykonanie skryptu Python] [ execute-python-script] modułu.


## <a name="working-with-visualizations"></a>Praca z wizualizacji

Wykresy utworzone za pomocą MatplotLib, który można wywołać w przeglądarce może być zwracany przez [wykonanie skryptu Python][execute-python-script]. Ale powierzchnie nie są automatycznie przekierowywane do obrazów w są one przy użyciu R. Dlatego użytkownik jawnie zapisać wszelkie powierzchni na plik PNG jeśli mają zostać zwrócone do usługi Azure Machine Learning. 

Aby wygenerować obrazów z MatplotLib, należy wykonać następującą procedurę:

* Przełącz z domyślne renderowanie Qt wewnętrznej bazy danych do "AGG" 
* Utwórz nowy obiekt rysunek 
* Pobierz osi i generowanie wszystkich powierzchni do niego 
* Zapisz wartość do pliku PNG 

Ten proces przedstawiono na poniższej rysunek 8 tworzy wykres punktowy macierzy przy użyciu funkcji scatter_matrix w Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Rysunek 8. Kod, aby zapisać dane MatplotLib obrazów.

Na rysunku nr 9 przedstawiono eksperymentu, która używa skryptu pokazana wcześniej, aby zwrócić geograficzne za pomocą drugiego port wyjściowy.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Rysunek 9. Wizualizacja powierzchni wygenerowane z kodu języka Python.

Istnieje możliwość zwraca wiele wartości zapisując je w różnych obrazów, obsługi usługi Azure Machine Learning przejmuje wszystkie obrazy i łączy je dla wizualizacji.


## <a name="advanced-examples"></a>Przykłady zaawansowane

Środowisko Anaconda zainstalowane w usłudze Azure Machine Learning zawiera pakiety wspólne, takie jak NumPy, SciPy i Scikits Dowiedz się więcej. Te pakiety można skutecznie dla różnych zadań przetwarzania danych w potoku uczenia maszynowego. Na przykład następujące eksperymentu i skrypt przedstawiający zastosowanie uczących zespół w Scikits — Dowiedz się, można obliczyć funkcji wyniki znaczenie dla zestawu danych. Wyniki mogą posłużyć do wykonania funkcji nadzorowane wyboru przed podawana do innego modelu uczenia Maszynowego.

W tym miejscu jest funkcją języka Python, używany do obliczania oceny znaczenie i kolejność funkcje oparte na wyniki:

![image11](./media/execute-python-scripts/figure8.png)

Figure 10. Funkcja funkcji rank przez wyniki.
  Następujące eksperymentu następnie oblicza i zwraca wyniki znaczenie funkcji w zestawie danych "Pima indyjskiego cukrzyca" w usłudze Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Rysunek 11. Wypróbuj rangi funkcji w zestawie danych cukrzyca indyjskiego Pima.

## <a name="limitations"></a>Ograniczenia
[Wykonanie skryptu Python] [ execute-python-script] aktualnie ma następujące ograniczenia:

1. *Wykonywanie w trybie piaskownicy.* Środowisko uruchomieniowe języka Python jest obecnie w trybie piaskownicy, a w związku z tym nie zezwala na dostęp do sieci lub lokalnego systemu plików w sposób ciągły. Wszystkie pliki zapisywane lokalnie są izolowane i usuwane po zakończeniu pracy w module. Kod języka Python, nie można uzyskać dostępu większości katalogów znajdujących się na komputerze, na którym jest uruchamiany na, wyjątku jest w bieżącym katalogu i jego podkatalogach.
2. *Brak zaawansowane programowanie i debugowanie pomocy technicznej.* Moduł Python nie obsługuje obecnie IDE funkcje, takie jak intellisense i debugowanie. Ponadto jeśli moduł nie powiedzie się w czasie wykonywania, pełny ślad stosu Python jest dostępna. Ale musi być wyświetlana w dzienniku danych wyjściowych dla modułu. Obecnie zalecamy tworzenie i debugowanie skryptów języka Python w środowisku, na przykład IPython a następnie zaimportuj kod do modułu.
3. *Dane wyjściowe ramki danych.* Punkt wejścia Python jest dozwolony tylko do zwrócenia ramki danych jako dane wyjściowe. Nie jest obecnie możliwe przywrócić dowolne obiekty Python, takie jak przeszkolone modeli bezpośrednio środowiska uruchomieniowego usługi Azure Machine Learning. Podobnie jak [wykonanie skryptu języka R][execute-r-script], który ma tego samego ograniczenia, jest możliwe w większości przypadków pickle obiekty do tablicy typu byte, a następnie wróć który wewnątrz ramki danych.
4. *Brak możliwości dostosowania instalacji języka Python*. Za pomocą mechanizmu pliku zip opisanych wcześniej jest obecnie jedynym sposobem, aby dodać niestandardowe moduły języka Python. Gdy jest to możliwe w dla małych modułów, jest skomplikowane dla dużych modułów, (zwłaszcza z natywnych bibliotek DLL) lub dużej liczby modułów. 

## <a name="conclusions"></a>Wnioski
[Wykonanie skryptu Python] [ execute-python-script] moduł pozwala naukowca danych, aby zastosować istniejący kod języka Python w przepływach pracy learning hostowanych w chmurze maszyny w usłudze Azure Machine Learning i bezproblemowo operacjonalizacji je jako część usługi sieci web. Moduł skryptu Python naturalnie współdziała z innych modułów w usłudze Azure Machine Learning. Moduł może służyć szereg zadań z Eksploracja danych, przetwarzanie wstępne i wyodrębniania funkcji, a następnie do oceny i przetwarzania końcowego wyników. Środowisko uruchomieniowe wewnętrznej bazy danych używane do wykonywania jest oparta na platformę Anaconda — dystrybucję oprogramowania Python dobrze przetestowane i powszechnie używane. To zaplecza upraszcza dla Ciebie do wewnętrznych zasobów istniejącego kodu do chmury.

Oczekujemy oferowanie dodatkowych funkcji do [wykonanie skryptu Python] [ execute-python-script] modułu, takich jak możliwość nauczenia i operacjonalizuj modele w języku Python i lepszą obsługę opracowywania i debugowania kodu w usłudze Azure Machine Learning Studio.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji możesz znaleźć w [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
