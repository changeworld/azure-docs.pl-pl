---
title: Wykonywanie skryptów uczenia maszynowego w języku Python
titleSuffix: Azure Machine Learning Studio
description: Wskazano projektowania zasad podstawowych Obsługa skryptów języka Python w usłudze Azure Machine Learning Studio i scenariusze użycia podstawowego, możliwości i ograniczenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: a040991ca4b3a08dec90f4fc6944b006ebea2135
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487850"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Wykonywanie skryptów uczenia maszynowego w języku Python w usłudze Azure Machine Learning Studio

W tym temacie opisano zasady projektowania bazowego bieżącej obsługi skryptów w języku Python w usłudze Azure Machine Learning. Możliwości głównej opisano również, w tym:

- wykonaj podstawowe użycia scenariuszy
- Ocena eksperymentu w usłudze sieci web
- Obsługa importowania istniejącego kodu
- Eksportuj wizualizacji
- Wykonaj nadzorowanych funkcji wyboru cech
- zrozumieć pewne ograniczenia

[Python](https://www.python.org/) jest narzędziem niezbędne w piersi narzędzie wielu analityków danych. Ma ona:

* prosty i zwięzłej składni, 
* Obsługa wielu platform 
* Ogromna kolekcja zaawansowanych bibliotek i 
* narzędzia programistyczne dla dorosłych. 

Python jest używany we wszystkich fazach zwykle używanych w machine learning modelowania przepływu pracy:

- pozyskiwanie i przetwarzanie 
- konstrukcja funkcji
- Szkolenie modelu 
- Walidacja modelu
- Wdrażanie modeli

Usługa Azure Machine Learning Studio obsługuje osadzanie skryptów w języku Python na różne części maszyny eksperymentu uczenia i również bezproblemowo publikować je w postaci usług sieci web w systemie Microsoft Azure.




## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Zasady projektowania skryptów języka Python w usłudze Machine Learning

Podstawowy interfejs do języka Python w usłudze Azure Machine Learning Studio jest za pośrednictwem [wykonanie skryptu Python] [ execute-python-script] modułu przedstawionej na rysunku 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Rysunek 1. **Wykonanie skryptu Python** modułu.

[Wykonanie skryptu Python] [ execute-python-script] modułu w usłudze Azure ML Studio akceptuje maksymalnie trzy danych wejściowych i tworzy maksymalnie dwóch danych wyjściowych (co opisano w poniższej sekcji), takie jak jego analogowy R [wykonywania języka R Skrypt] [ execute-r-script] modułu. Wykonanie kodu języka Python jest wprowadzana w polu parametr jako specjalnie nazwane punktu wejścia funkcji o nazwie `azureml_main`. Poniżej przedstawiono zasady projektowania klucza używaną do zaimplementowania tego modułu:

1. *Musi być idiomatyczną dla użytkowników języka Python.* Większość użytkowników Python wziąć pod uwagę ich kod jako funkcje w modułach. Dlatego umieszczenie wiele instrukcji wykonywalnych w module najwyższego poziomu jest stosunkowo rzadkie. Co w efekcie polu skrypt pobiera również specjalnie nazwane funkcja języka Python zamiast po prostu sekwencji instrukcji. Obiekty udostępniane w funkcji są typów biblioteki standardowej języka Python, takie jak [Pandas](http://pandas.pydata.org/) ramki danych i [NumPy](http://www.numpy.org/) tablic.
2. *Musi mieć o dużej wierności między lokalnych i w chmurze wykonań.* Zaplecza służącego do wykonania kodu w języku Python, opiera się na [Anaconda](https://store.continuum.io/cshop/anaconda/), powszechnie używane naukowych dystrybucji języka Python dla wielu platform. Chodzi o blisko 200 najpopularniejsze pakiety języka Python. W związku z tym analitykom danych można debugowania i kwalifikowania ich kodu w środowisku lokalnym usługi Azure Machine Learning zgodnego Anaconda. Następnie użyj istniejącego środowiska programowania, takich jak [IPython](http://ipython.org/) notesu lub [narzędzi Python Tools for Visual Studio](https://aka.ms/ptvs), aby go uruchomić w ramach eksperymentu usługi uczenie Maszynowe Azure. `azureml_main` Punkt wejścia jest wanilii funkcji języka Python, a tym samym *** można tworzyć bez usługi Azure ML kodu lub zainstalowany zestaw SDK.
3. *Musi być bezproblemowo konfigurowalna z innymi modułami usługi Azure Machine Learning.* [Wykonanie skryptu Python] [ execute-python-script] moduł przyjmuje jako dane wejściowe i wyjściowe, standardowe zestawy danych usługi Azure Machine Learning. Podstawowej struktury przejrzystą i efektywnie pomost środowisk uruchomieniowych uczenie Maszynowe Azure i języka Python. Dlatego języka Python może służyć w połączeniu z istniejącymi przepływami pracy usługi uczenie Maszynowe Azure, łącznie z tymi, które wywołują z języków R i bazy danych SQLite. Wynik, badacz danych tworzą przepływy pracy który:
   * Używanie języka Python i Pandas dla przetwarzania wstępnego i czyszczenie danych
   * źródła danych do przekształcania SQL, dołączenie do wielu zestawów danych do postaci funkcji
   * szkolenie modeli przy użyciu algorytmów w usłudze Azure Machine Learning 
   * Oceń i po przetworzeniu wyniki za pomocą języka R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Scenariusze użycia podstawowego ml skryptów w języku Python

W tej sekcji, firma Microsoft przeglądów niektóre z podstawowych zastosowań [wykonanie skryptu Python] [ execute-python-script] modułu. Dane wejściowe do modułu Python są widoczne jako Pandas ramki danych. Funkcja musi zwracać jedną ramkę danych Pandas spakowane w języku Python [sekwencji](https://docs.python.org/2/c-api/sequence.html) takich jak krotki, listy lub tablicy NumPy. Pierwszy element tej sekwencji jest następnie zwracany pierwszy port wyjściowy modułu. Ten schemat jest pokazany na rysunku 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Rysunek 2. Mapowanie portów dla parametrów wejściowych i zwrócenia wartości do portu wyjściowego.

Bardziej szczegółowe semantykę jak porty wejściowe są mapowane na parametrów `azureml_main` funkcji są wyświetlane w tabeli 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabela 1. Mapowanie portów wejściowych do parametrów funkcji.

Mapowanie między porty wejściowe i parametry funkcji jest pozycyjne:

- Pierwszy połączonych port wejściowy jest zamapowana na pierwszy parametr funkcji. 
- Drugie dane wejściowe (jeśli jest podłączony) jest mapowany na drugi parametr funkcji.

Zobacz *języka Python do analizy danych* (O'Reilly 2012) przez McKinney Zach. Aby uzyskać więcej informacji na temat Pandas języka Python i jak może służyć do modyfikowania danych, efektywnie i wydajnie. 


## <a name="translation-of-input-and-output-types"></a>Tłumaczenie typów wejściowych i wyjściowych 
Wejściowe zestawy danych w usłudze Azure ML są konwertowane na ramki danych w Pandas. Dane wyjściowe ramki danych są konwertowane do zestawów danych usługi Azure ML. Poniższe konwersje są wykonywane:

1. Kolumny ciągów i numeryczne są konwertowane odpowiednio — jest i brakujących wartości w zestawie danych są konwertowane do wartości "Nazwa" w Pandas. Tej samej konwersji się dzieje w sposób Wstecz (Pandas NA wartości są konwertowane na brakujących wartości w usłudze Azure ML).
2. Indeks wektorów w Pandas nie są obsługiwane w usłudze Azure ML. Wszystkie ramki danych wejściowych w funkcji języka Python zawsze mieć 64-bitowe indeksu numerycznego z zakresu od 0 do liczby wierszy, minus 1. 
3. Usługa Azure ML zestawy danych nie może mieć zduplikowanych nazw kolumn i nazwy kolumn, które nie są ciągami. Jeśli dane wyjściowe ramki danych zawiera nieliczbową kolumn, struktura wywołuje `str` na nazwy kolumn. Podobnie zduplikowanych nazw kolumn są automatycznie zniekształcone, aby upewnić się, że nazwy są unikatowe. Do pierwszego duplikat, (3) jest dodawany sufiks (2) do drugiego duplikat i tak dalej.


## <a name="operationalizing-python-scripts"></a>Operacjonalizacji skryptów w języku Python

Wszelkie [wykonanie skryptu Python] [ execute-python-script] moduły używane w eksperymencie oceniania są wywoływane, gdy publikowane jako usługi sieci web. Przykładowo rysunek 3 przedstawia oceniania eksperyment, który zawiera kod, aby obliczyć pojedynczego wyrażenia języka Python. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Rysunek 3. Usługa sieci Web do oceny wyrażenia języka Python.

Usługi sieci web utworzone na podstawie tego doświadczenia:

- przyjmuje jako dane wejściowe do wyrażenia języka Python (jako ciąg)
- wysyła je do interpretera języka Python 
- zwraca tabelę zawierającą wyrażenie i obliczony wynik.


## <a name="importing-existing-python-script-modules"></a>Importowanie istniejących modułów skryptu języka Python

Typowy przypadek użycia dla wielu analityków danych jest włączenie istniejących skryptów języka Python do eksperymentów usługi uczenie Maszynowe Azure. Zamiast konieczności, że cały kod być łączone i wkleić w polu jednego skryptu, [wykonanie skryptu Python] [ execute-python-script] moduł przyjmuje plik zip, który zawiera moduły języka Python na trzeci portu wejściowego. Plik jest rozpakowano przez środowisko wykonywania w czasie wykonywania i zawartości są dodawane do ścieżki biblioteki interpreter języka Python. `azureml_main` Punktu wejścia funkcji można następnie zaimportować moduły bezpośrednio.

Na przykład należy wziąć pod uwagę pliku Hello.py zawierający prostej funkcji "Hello, World".

![image6](./media/execute-python-scripts/figure4.png)

Rysunek 4. Funkcja zdefiniowana przez użytkownika w pliku Hello.py.

Następnie utwórz plik Hello.zip, który zawiera Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Rysunek 5. Plik zip zawierający kod Python zdefiniowanych przez użytkownika.

Przekazywanie pliku zip jako zestaw danych do usługi Azure Machine Learning Studio. Następnie utwórz i uruchom eksperyment, który używa kodu w języku Python w pliku Hello.zip, dołączając ją do trzeciego portem wejściowym **wykonanie skryptu Python** modułu, jak pokazano na ilustracji.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Rysunek 6. Przekazano przykładowego eksperymentu przy użyciu kodu języka Python zdefiniowanych przez użytkownika jako plik zip.

Dane wyjściowe modułu pokazują, czy plik zip został rozpakowanych i że funkcja `print_hello` została uruchomiona.
 
![image10](./media/execute-python-scripts/figure7.png)

Rysunek 7. Funkcja zdefiniowana przez użytkownika używana wewnątrz [wykonanie skryptu Python] [ execute-python-script] modułu.


## <a name="working-with-visualizations"></a>Praca z wizualizacjami

Wykresy utworzone za pomocą MatplotLib, które mogą być wizualizowane w przeglądarce może zostać zwrócony przez [wykonanie skryptu Python][execute-python-script]. Ale powierzchnie nie nastąpi automatyczne przekierowanie do obrazów w są one podczas używania języka R. Więc użytkownik musi jawnie zapisać żadnych wykresów na pliki PNG jeśli mają zostać zwrócone do usługi Azure Machine Learning. 

Do generowania obrazów z MatplotLib, należy wykonać następującą procedurę:

* Przełącz z domyślne renderowanie Qt wewnętrznej bazy danych do "AGG" 
* Utwórz nowy obiekt rysunek 
* Pobierz oś oraz generować wykresy wszystkich do niego 
* Zapisz plik PNG rysunku 

Ten proces przedstawiono na poniższym rysunku 8, który tworzy wykres punktowy macierzy przy użyciu funkcji scatter_matrix w Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Rysunek 8. Kod, aby zapisać rysunki MatplotLib obrazów.

Rysunek 9 przedstawiono eksperyment, który używa skryptu przedstawionego wcześniej, aby zwrócić geograficzne za pomocą drugiego port wyjściowy.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Rysunek 9. Wizualizacja powierzchni wygenerowany na podstawie kodu w języku Python.

Istnieje możliwość zwrócić wiele wartości, zapisując je na różne obrazy, środowisko uruchomieniowe usługi Azure Machine Learning przejmuje wszystkie obrazy i łączy je do wizualizacji.


## <a name="advanced-examples"></a>Zaawansowane przykłady

Środowisko Anaconda, zainstalowane w usłudze Azure Machine Learning obejmuje popularnych pakietów takich jak NumPy SciPy i Dowiedz się Scikits. Te pakiety można skutecznie dla różnych zadań przetwarzania danych w potoku uczenia maszynowego. Na przykład poniższego eksperymentu oraz skrypt ilustrują użycie uczących zespołu w Scikits — Dowiedz się, jak funkcja wyniki znaczenie dla zestawu danych wystąpień obliczeniowych. Wyniki może służyć do wykonywania wybór funkcji nadzorowanych przed podawana do innego modelu uczenia Maszynowego.

W tym miejscu jest funkcją języka Python, używany do obliczania wyników znaczenie i kolejność, w oparciu o wyniki funkcji:

![image11](./media/execute-python-scripts/figure8.png)

Rysunek 10. Funkcja funkcji rank, wyniki.
 
Poniższego eksperymentu następnie oblicza i zwraca wyniki znaczenie funkcji w zestawie danych "Pima indyjskim choroby" w usłudze Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Rysunek 11. Wypróbuj do rangi funkcji w zestawie danych choroby indyjskim Pima.

## <a name="limitations"></a>Ograniczenia
[Wykonanie skryptu Python] [ execute-python-script] obecnie ma następujące ograniczenia:

1. *Wykonywania w trybie piaskownicy.* Środowisko uruchomieniowe języka Python jest obecnie w trybie piaskownicy, a w rezultacie nie zezwala na dostęp do sieci lub do lokalnego systemu plików w sposób ciągły. Wszystkie pliki zapisane lokalnie są izolowane i usunięte po zakończeniu modułu. Większość katalogów na komputerze, na których ono działa, wyjątek jest w bieżącym katalogu i jego podkatalogach kodu w języku Python nie ma dostępu.
2. *Brak zaawansowanego tworzenia i obsługi debugowania.* Moduł Python aktualnie nie obsługuje funkcje środowiska IDE, takie jak intellisense i debugowanie. Ponadto w przypadku awarii w czasie wykonywania moduł pełny ślad stosu Python jest dostępna. Ale musi być wyświetlana w dzienniku danych wyjściowych dla modułu. Firma Microsoft zaleca obecnie programowanie i debugowanie skryptów w języku Python w środowisku, takie jak IPython a następnie zaimportuj kod do modułu.
3. *Dane wyjściowe ramki danych jednego.* Punkt wejścia Python jest dozwolony tylko do zwrócenia ramkę danych jako dane wyjściowe. Nie jest obecnie możliwe przywrócić dowolne obiekty języka Python, takie jak przeszkolone modele bezpośrednio środowiska uruchomieniowego usługi Azure Machine Learning. Podobnie jak [wykonanie skryptu języka R][execute-r-script], która ma takie samo ograniczenie występuje, jest możliwe w wielu przypadkach pickle obiektów w tablicy bajtów, a następnie wróć, wewnątrz ramki danych.
4. *Brak możliwości dostosowania instalacji języka Python*. Obecnie jedynym sposobem dodawania niestandardowych modułów języka Python jest za pośrednictwem mechanizmu pliku zip, opisanego wcześniej. Gdy jest to możliwe dla małych modułów, jest kłopotliwe dla dużych modułów, (zwłaszcza z natywnych bibliotek DLL) lub dużej liczby modułów. 

## <a name="conclusions"></a>Wnioski
[Wykonanie skryptu Python] [ execute-python-script] moduł pozwala bezproblemowo włączenie istniejącego kodu języka Python do uczenia maszynowego hostowanymi w chmurze przepływów w usłudze Azure Machine Learning i do wydobywania danych operacjonalizowanie je jako część usługi sieci web. Moduł skryptu języka Python naturalnie współdziała z innymi modułami w usłudze Azure Machine Learning. Moduł może służyć do szeroką gamę zadań przy użyciu eksplorację danych do przetwarzania wstępnego i wyodrębniania funkcji, a następnie do oceny i przetwarzanie końcowe wyników. Środowisko uruchomieniowe wewnętrznej bazy danych, używane do wykonywania opiera się na Anaconda, dobrze przetestowana i powszechnie używanych dystrybucji języka Python. To zaplecze upraszcza dla Ciebie dołączyć istniejące zasoby kodu do chmury.

Oczekujemy, że zapewnia dodatkowe funkcje, aby [wykonanie skryptu Python] [ execute-python-script] modułu, takie jak możliwość uczenia i operacjonalizuj modele w języku Python i lepszą obsługę programowania i debugowanie kodu w usłudze Azure Machine Learning Studio.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji możesz znaleźć w [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
