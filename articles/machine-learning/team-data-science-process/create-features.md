---
title: Inżynieria w nauce danych | Dokumentacja firmy Microsoft
description: Zawiera opis na potrzeby funkcji inżynierii oraz przykłady swoją rolę w procesie rozszerzenie danych usługi machine learning.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 3fde69e8-5e7b-49ad-b3fb-ab8ef6503a4d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: b4194ef5ab1c2c09206ea0acf78cb539bc2fc0b7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836521"
---
# <a name="feature-engineering-in-data-science"></a>Inżynieria w nauce danych
W tym artykule opisano na potrzeby funkcji inżynieryjne i zawiera przykłady swoją rolę w procesie rozszerzenie danych usługi machine learning. Przykłady użyta w celu przedstawienia tego procesu są pobierane z usługi Azure Machine Learning Studio. 

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **menu** łącza do artykułów, które opisują sposób tworzenia funkcji dla danych w różnych środowiskach. To zadanie jest krokiem w [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Funkcja engineering próby zwiększyć możliwości predykcyjnej algorytmów uczenia przez tworzenie funkcji z danych pierwotnych ułatwić proces uczenia. Inżynieryjne i wybór funkcji jest częścią TDSP opisane w temacie [co to jest cyklu życia procesu nauki danych zespołu?](overview.md) Funkcja inżynieryjne i wyboru są częściami **opracowywania funkcji** krok TDSP. 

* **Inżynieria**: ten proces próbuje utworzyć dodatkowe istotne cechy z istniejących funkcji pierwotnych danych i zwiększyć możliwości predykcyjnej Algorytm uczenia.
* **Wybór funkcji**: tego procesu wybiera klucza podzbioru cech oryginalnych danych w celu zmniejszenia wymiarach problemu szkolenia.

Zwykle **Inżynieria** zostanie zastosowana jako pierwsza, aby wygenerować dodatkowe funkcje, a następnie **funkcji wyboru** kroku jest wykonywana w celu wyeliminowania nie ma znaczenia, nadmiarowe lub dużej skorelowane funkcji.

Dane szkoleniowe używane w uczeniu maszynowym wspomagają często wyodrębniania funkcji w zebranych danych pierwotnych. Przykład funkcji odtworzone w kontekście obrazów znaków odręcznie klasyfikowania uczenia jest tworzenie nieco mapy gęstość zbudowane z bitowego pierwotnych danych dystrybucji. Ta mapa można lokalizowania krawędzi znaki efektywniej niż po prostu bezpośrednio przy użyciu rozkładu raw.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Tworzenie funkcji z danych - Inżynieria
Dane szkoleniowe składa się z macierzy składa się z przykładami (rekordy lub obserwacji przechowywane w wierszach), z których każda ma zestaw funkcji (zmienne lub pól przechowywane w kolumnach). Oczekiwano funkcji określony w projekcie doświadczenia charakteryzujących wzorce w danych. Mimo że wiele pól danych pierwotnych mogą być bezpośrednio uwzględnione w wybranej funkcji używany do nauczenia modelu, często jest przypadek dodatkowe funkcje (odtworzone) muszą zostać utworzone na podstawie funkcje nieprzetworzone dane można wygenerować obiektu dataset rozszerzone szkolenia.

Jakiego rodzaju funkcje należy utworzyć w celu zwiększenia zestawu danych podczas uczenia modelu? Odtworzone funkcji, które zwiększają szkolenia dostarczają informacji lepiej odróżniającą wzorce w danych. Nowe funkcje są powinien zapewniać dodatkowe informacje, które nie jest wyraźnie przechwycony lub łatwo widoczna w zestawie oryginalnej lub istniejących funkcji. Jednak ten proces jest coś grafiki. Decyzje dźwięku i produktywności często wymagają doświadczenia z niektórych domen.

Począwszy od usługi Azure Machine Learning, najłatwiej ujmij ten proces, konkretnie przy użyciu przykłady podane w Studio. Poniżej przedstawiono dwa przykłady:

* Przykład regresji [prognozowanie liczby dzierżawy roweru](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) w nadzorowanym eksperymentu, gdy wiadomo, że wartości docelowej
* Using przykład klasyfikacji wyszukiwania tekstu [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Przykład 1: Dodawanie funkcji danych czasowych dla modelu regresji
Użyjmy eksperymentu "żądanie Prognozowanie rowerów" w usłudze Azure Machine Learning Studio aby zademonstrować sposób opracowywać funkcje zadania regresji. Celem tego eksperymentu jest do prognozowania popytu na rowerów, oznacza to, że liczba roweru dzierżawy w ramach określonej miesiąc/dzień/godziny. Zestaw danych "dzierżawa roweru UCI zestawu danych" jest używana jako nieprzetworzone dane wejściowe. Ten zestaw danych jest oparty na prawdziwe dane z firmy Bikeshare kapitału, która obsługuje sieci wynajem roweru w stanie Waszyngton DC w Stanach Zjednoczonych. Zestaw danych reprezentuje liczbę roweru dzierżawy w ramach określoną godzinę dnia w latach i 2011 roku 2012 i zawiera 17379 wierszy i kolumn 17. Zestaw funkcji raw zawiera pogodą (knie temperatury/wilgotności szybkość) i typ dzień (dni wolnych/dzień tygodnia). Pole do prognozowania jest licznik "cnt" reprezentuje dzierżawy roweru w ciągu godziny określonych i które od 1 do 977.

W celu tworzenia skuteczne funkcje dane szkoleniowe cztery regresji, modeli są tworzone za pomocą tego samego algorytmu, ale z czterech różnych szkolenia zestawami danych. Cztery zestawy danych reprezentują tego samego pierwotnych danych wejściowych, ale z coraz więcej funkcji ustawiona. Te funkcje są podzielone na kategorie cztery:

1. A = pogody + świątecznych dzień tygodnia + weekendowe funkcje przewidywane dzień
2. B = liczba rowerów, które zostały dzierżawione w każdym z ostatnich 12 godzin
3. C = Liczba rowerów, które zostały dzierżawione w każdym z ostatnich 12 dni na tę samą godzinę
4. D = liczba rowerów, które zostały dzierżawione w każdym z poprzednich 12 tygodni na tę samą godzinę i tego samego dnia

Oprócz A zestaw funkcji, który już istnieje w oryginalnych danych pierwotnych, trzy inne zestawy funkcji są tworzone za pomocą funkcji inżynierii procesu. Zestaw funkcji przechwytywania B bardzo ostatnie żądanie dla rowerów. Skonfigurować funkcji przechwytywania C popytu na rowerów o określonej godzinie. Funkcja ustawiona D przechwytywania żądanie dla rowerów w szczególności godziny i dnia tygodnia. Szkolenie cztery zestawy danych każdy zawiera odpowiednio A zestaw funkcji, A + B, A + B + C i A + B + C + D.

W eksperymencie usługi Azure Machine Learning te cztery zestawy danych szkoleniowych są tworzone za pomocą czterech oddziałów z wstępnie przetworzonych wejściowego zestawu danych. Z wyjątkiem pierwszej gałęzi, każdy z tych gałęzi zawiera [wykonanie skryptu języka R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modułu, w którym pochodnej funkcji (funkcja ustawiona B, C i D) są odpowiednio zbudowane i dołączona do importowanych obiektów dataset. Na poniższym rysunku pokazano skrypt języka R, użyty do utworzenia zestawu funkcji B w drugim gałęzi po lewej stronie.

![Tworzenie funkcji](./media/create-features/addFeature-Rscripts.png)

Porównanie wyników wydajności cztery modele podsumowano w poniższej tabeli: 

![wynik porównania](./media/create-features/result1.png)

Najlepsze wyniki są wyświetlane przez funkcje A + B + C. Współczynnik błędów zmniejsza, kiedy Uwaga zestaw dodatkowych funkcji znajdują się w danych szkoleniowych. Sprawdza założenie, że zestaw funkcji B, C zapewniają dodatkowe istotne informacje o zadaniu regresji. Ale dodawanie funkcji D nie wydaje się Podaj wszelkie dodatkowe zmniejszenie współczynnik błędów.

## <a name="example2"></a> Przykład 2: Tworzenie funkcji wyszukiwania tekstu
Funkcja engineering jest powszechnie stosowane w zadania związane z wyszukiwania tekstu, takich jak analiza dokumentu klasyfikacji i wskaźniki nastrojów klientów. Na przykład można klasyfikować dokumenty na kilka kategorii, typowe założeń jest możliwość word fraz zawarte w jednej kategorii doc mniej prawdopodobne w innej kategorii dokumentu. Innymi słowy częstotliwość dystrybucji słowa/fraz jest w stanie scharakteryzowania kategoriami innego dokumentu. W aplikacjach wyszukiwania tekstu ponieważ pojedynczych zawartość tekstową zwykle służą jako dane wejściowe funkcji inżynierii procesu jest potrzebne do utworzenia funkcje dotyczące częstotliwości word/frazę.

Do wykonania tego zadania, nazywany technika **Tworzenie skrótu funkcji** jest stosowany do wydajnie Włączanie funkcji dowolnego tekstu do indeksów. Zamiast kojarzenie każdej funkcji tekst (słowa/wyrażenia) do konkretnego indeksu, to funkcje — metoda stosowania funkcji skrótu do funkcji i używając ich wartości skrótu jako indeksy bezpośrednio.

W usłudze Azure Machine Learning [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) moduł, który tworzy te word/frazy funkcje wygodny sposób. Poniższej ilustracji przedstawiono przykład za pomocą tego modułu. Wejściowy zestaw danych zawiera dwie kolumny: Klasyfikacja książki zakresu od 1 do 5 i przeglądu rzeczywistej zawartości. Celem tego [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modułu jest pobrać licznych nowe funkcje przedstawiające częstotliwość wystąpienie odpowiedniej wyrazy / Przejrzyj wyrażenia w ramach wybranej książki. Aby użyć tego modułu, wykonaj następujące kroki:

* Najpierw wybierz kolumnę, która zawiera tekst wejściowy ("Col2" w tym przykładzie).
* Po drugie, ustaw "Hashing bitsize" na 8, co oznacza, że 2 ^ 8 = 256 funkcje zostaną utworzone. Word/fazie cały tekst zostanie wartość skrótu, aby 256 indeksów. Parametr "Hashing bitsize" zakresu od 1 do 31. Wyrazy / wyrażenia jest mniej prawdopodobne skrótu do tego samego indeksu, jeśli zostanie ustawiona na większą liczbę.
* Trzecie Ustaw parametr "N-gramów" do 2. Ta wartość pobiera częstotliwość występowania unigrams (funkcja dla każdego pojedynczego wyrazu) i bigrams (funkcja dla każdej pary słów sąsiadujące) z teksu wejściowego. Parametr "N-gramów" zakresu od 0 do 10, który wskazuje maksymalną liczbę kolejnych słowa do uwzględnienia w funkcji.  

![Moduł "funkcji Hashing"](./media/create-features/feature-Hashing1.png)

Na poniższym rysunku pokazano, jakie tych nowych funkcji przypominają.

![Przykład "funkcji Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Podsumowanie
Odtworzone i wybranych funkcji zwiększyć wydajność procesu szkolenia, która podejmuje próbę wyodrębnić klucza informacje zawarte w danych. Poprawiają również uprawnienia tych modeli dokładnie klasyfikowania danych wejściowych oraz do przewidywania wyników odsetek bardziej niezawodnie. Funkcja inżynieryjne i wyboru można także połączyć dokonanie więcej praktyce tractable learning. Robi to udoskonalanie, a następnie zmniejszenie liczby potrzeby kalibrować lub uczenie modelu przy użyciu funkcji. Funkcje wybrane do nauczenia modelu ze sobą matematycznie rzecz biorąc, są minimalny zbiór zmienne niezależne, które wyjaśniają wzorce w danych, a następnie pomyślnie przewidywania wyników.

Nie zawsze jest zawsze do wykonywania funkcji engineering lub funkcji wyboru cech. Określa, czy jest potrzebna, lub nie zależy od danych do strony lub zbierane algorytm wybrany, a celem eksperymentu.

