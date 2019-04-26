---
title: Inżynieria funkcji w zakresie analizy danych — danych zespołu dla celów naukowych
description: Wyjaśnia, w celu technicznego opracowywania funkcji i zawiera przykłady swojej roli w procesie rozszerzenia danych, uczenia maszynowego.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8b0973007a78b492cff1c5ffc2ce1e43116a0847
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398642"
---
# <a name="feature-engineering-in-data-science"></a>Inżynieria funkcji w zakresie analizy danych
W tym artykule wyjaśniono celów technicznego opracowywania funkcji i zawiera przykłady swojej roli w procesie rozszerzenia danych, uczenia maszynowego. Przykłady umożliwiają zilustrowanie tego procesu są pobierane z usługi Azure Machine Learning Studio. 

To zadanie jest to krok w [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Funkcja inżynierów próby zwiększają możliwości predykcyjnych algorytmów uczenia, tworząc funkcje z nieprzetworzone dane, które pomagają w wykonywaniu procesu uczenia. Inżynieria i wybór funkcji jest częścią środowiska TDSP opisane w temacie [co to jest cykl życia zespołowego danych dla celów naukowych?](overview.md) Funkcja inżynieria i wybór są częściami **opracowywanie funkcji** krok przetwarzania TDSP. 

* **inżynieria cech**: Ten proces próbuje utworzyć dodatkowe funkcje odpowiednie na podstawie istniejących funkcji nieprzetworzonych danych i zwiększają możliwości predykcyjnych algorytmu uczenia.
* **Wybór funkcji**: Ten proces wybiera klucza podzbiór funkcji, oryginalnym danych w celu podjęcia próby zmniejszenia wymiarach problemu szkolenia.

Zwykle **Inżynieria funkcji** najpierw stosowany jest do generowania dodatkowe funkcje, a następnie **wybór funkcji** kroku jest przeprowadzana w celu wyeliminowania funkcji nie ma znaczenia, nadmiarowe lub bardzo skorelowany.

Dane szkoleniowe, używane w uczeniu maszynowym, często może zostać poprawione przez wyodrębnianie funkcji z nieprzetworzone dane zebrane. Przykład funkcji zaprojektowanych w kontekście uczenia klasyfikowania obrazów znaków pisma odręcznego jest tworzenie nieco mapy gęstość skonstruowany na podstawie danych pierwotnych bit dystrybucji. Tę mapę można lokalizowania krawędziami znaki efektywniej niż po prostu bezpośrednio przy użyciu surowego dystrybucji.

Aby utworzyć funkcji dla danych w określonych środowiskach, zobacz następujące artykuły:

* [Tworzenie funkcji dla danych w programie SQL Server](create-features-sql-server.md)
* [Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań Hive](create-features-hive.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Tworzenie funkcji na podstawie danych - Inżynieria funkcji
Dane szkoleniowe składa się z macierzy składa się z przykładami (rekordy lub przechowywane w wierszach uwagi), z których każdy ma zestaw funkcji (zmiennych lub przechowywane w kolumnach pól). Funkcje określone w projekcie doświadczenia powinny scharakteryzowania wzorce w danych. Chociaż wiele pól nieprzetworzone dane mogą być bezpośrednio zawarte w zestawie wybraną funkcję używane do trenowania modelu, często jest przypadek, dodatkowe funkcje (zaprojektowanych) muszą zostać utworzone na podstawie funkcji w danych pierwotnych, można wygenerować zestawu danych rozszerzonych szkolenia.

Jakiego rodzaju funkcje należy utworzyć w taki sposób, aby poprawić zestaw danych, podczas uczenia modelu? Zaprojektowanych funkcji, które rozszerzają szkolenia zapewniają informacje odróżniające lepiej wzorce w danych. Nowe funkcje są powinien zapewniać dodatkowe informacje, które nie jest wyraźnie przechwycony lub łatwo widoczna w zestawie oryginalny lub istniejącej funkcji. Ale ten proces jest coś, co sztuki. Dźwięk i produktywne decyzje często wymagają pewne doświadczenie domeny.

Począwszy od usługi Azure Machine Learning, najłatwiej zapoznanie się z nim tego procesu, konkretnie przy użyciu przykładów, które podano w Studio. Dwa przykłady przedstawiono poniżej:

* Przykład regresji [prognozowania liczby wypożyczenia rowerów](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) w eksperymencie nadzorowane, gdy wiadomo, że wartości docelowej
* Using przykład klasyfikacji wyszukiwania tekstu [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Przykład 1: Dodawanie funkcji danych czasowych dla modelu regresji
Użyjmy eksperymentu "prognozowaniu popytu rowerów" w usłudze Azure Machine Learning Studio na pokazują, jak projektować funkcje dla zadania regresji. Celem tego eksperymentu jest przewidzieć zapotrzebowanie na rowerów, czyli liczbę wypożyczenia rowerów w ramach określonego miesiąc/dzień/godz. Zestaw danych "wypożyczeń rowerów UCI zestawu danych" jest używany jako nieprzetworzone dane wejściowe. Ten zestaw danych jest oparty na prawdziwe dane pochodzące z firmy Bikeshare wielkiej litery, która obsługuje sieci wypożyczeń rowerów w Waszyngton w Stanach Zjednoczonych. Zestaw danych reprezentuje liczbę wypożyczenia rowerów w ramach określonej godziny dziennie w ciągu lat i 2011 roku 2012 i zawiera 17379 wierszy i kolumn 17. Zestaw funkcji pierwotne zawiera warunki pogodowe (temperatury/wilgotności/wiatru) i typ dnia (dzień wolny/dzień tygodnia). Pole do prognozowania jest liczba "cnt", która reprezentuje wypożyczenia rowerów w ciągu godziny określonej i który zakresu od 1 do 977.

W celu konstruowania skuteczne funkcje w danych szkoleniowych, cztery regresji, który modeli są tworzone przy użyciu tego samego algorytmu, ale z czterech różnych szkolenia zestawami danych. Cztery zestawy danych reprezentują ten sam nieprzetworzone dane wejściowe, ale z coraz większą liczbę funkcji należy ustawić. Te funkcje są pogrupowane w cztery kategorie:

1. Element = pogody, dniem wolnym od pracy i dni tygodnia i weekendy funkcji przewidywane dzień
2. B = liczbę rowerów, które zostały wypożyczone w każdej z ostatnich 12 godzin
3. C = liczbę rowerów, które zostały wypożyczone w każdej z poprzednich 12 dni, w tym samym godzinie
4. D = liczbę rowerów, które zostały wypożyczone w każdej z poprzednich 12 tygodni w jednej godziny i tego samego dnia

Oprócz funkcji set A, który już istnieje w oryginalnych danych pierwotnych, trzech zestawów funkcji są tworzone za pomocą funkcji inżynierów procesów. Funkcja ustawiona przechwytywania B bardzo ostatnie żądanie rowery. Funkcja ustawiona przechwytywania C zapotrzebowanie na rowery o określonej godzinie. Funkcja ustawić żądanie przechwycenia D rowery danej godziny i określonego dnia tygodnia. Cztery zestawów danych szkoleniowych każdego zawiera element zestaw funkcji, A + B, A + B + C i A + B + C + D, odpowiednio.

W eksperymencie usługi Azure Machine Learning tych czterech zestawów danych szkoleniowych są tworzone za pomocą czterech gałęziami za pomocą wstępnie przetworzonego wejściowego zestawu danych. Z wyjątkiem pierwszej gałęzi, każda z tych gałęzi zawiera [wykonanie skryptu języka R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modułu, w którym pochodnej funkcji (funkcja zestawu B, C i D) odpowiednio są zbudowane i dołączany do zaimportowanego zestawu danych. Na poniższym rysunku pokazano skrypt języka R, użyty do utworzenia zestawu funkcji B w drugim gałęzi po lewej stronie.

![Tworzenie funkcji](./media/create-features/addFeature-Rscripts.png)

Porównanie wyników wydajności z czterech modeli podsumowano w poniższej tabeli: 

![wynik porównania](./media/create-features/result1.png)

Najlepsze wyniki są wyświetlane przy użyciu funkcji A + B + C. Należy pamiętać, współczynnik błędów zmniejsza, gdy zestaw dodatkowych funkcji znajdują się w danych szkoleniowych. Weryfikuje założenie, że zestaw funkcji B, C zapewniają dodatkowe informacje istotne dla zadań regresji. Ale dodanie funkcji D nie wydaje się zapewnienie każde dodatkowe zmniejszenie współczynnik błędów.

## <a name="example2"></a> Przykład 2: Tworzenie funkcji w wyszukiwania tekstu
Technicznego opracowywania funkcji jest szeroko stosowany w zadania związane z wyszukiwania tekstu, takie jak analiza dokumentu klasyfikacji i wskaźniki nastrojów klientów. Na przykład można klasyfikować dokumenty na kilka kategorii, należy założeniu typowej jest możliwość word fraz uwzględnione w jednej kategorii doc mniej prawdopodobne w innej kategorii dokumentu. Innymi słowy częstotliwość dystrybucji słów/fraz jest w stanie celu scharakteryzowania kategorie innego dokumentu. W aplikacjach wyszukiwania tekstu ponieważ poszczególne zawartość tekstową zwykle służą jako dane wejściowe funkcji inżynierów procesów jest wymagane do utworzenia funkcje dotyczące częstotliwości słowo lub dana fraza.

Do wykonania tego zadania, to technika o nazwie **Tworzenie skrótu funkcji** jest stosowany do efektywnie przekształcając funkcje dowolnego tekstu indeksów. Zamiast kojarzenie każdej funkcji tekstu (wyrazy/zwroty) do określonego indeksu to funkcje metoda przez zastosowanie funkcji mieszania, funkcji oraz ich wartości skrótu jako wskaźników bezpośrednio.

W usłudze Azure Machine Learning jest [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modułu, który tworzy te słowo lub dana fraza funkcje zostaną w wygodny sposób. Poniższej ilustracji przedstawiono przykład użycia tego modułu. Wejściowy zestaw danych zawiera dwie kolumny: ocena książki w zakresie od 1 do 5 oraz zawartość, przejrzyj rzeczywiste. Celem tej [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) moduł jest pobrać wiele nowych funkcji przedstawiające częstotliwości występowania odpowiedniego wyrazów / Przejrzyj "r" w ramach wybranej książki. Aby użyć tego modułu, wykonaj następujące czynności:

* Najpierw wybierz kolumnę, która zawiera tekst wejściowy ("Col2" w tym przykładzie).
* Po drugie, równa "Hashing bitsize" 8, co oznacza, że 2 ^ 8 = 256 funkcji zostanie utworzony. Word/fazie cały tekst będzie skrótu do 256 indeksów. Parametr "Hashing bitsize" z zakresu od 1 do 31. Wyrazy "r" są mniej prawdopodobne skrótu do tego samego indeksu, jeśli zostanie ustawiona jako większą liczbę.
* Po trzecie Ustaw parametr "N-gramy" do 2. Ta wartość pobiera częstotliwość występowania unigrams (funkcja dla każdego pojedynczego wyrazu) i bigrams (funkcja dla każdej pary słów sąsiadujące) z tekstu wejściowego. Parametr "N-gramy" zakresu od 0 do 10, która określa maksymalną liczbę kolejnych wyrazów, które mają zostać uwzględnione w funkcji.  

![Moduł "Funkcji Hashing"](./media/create-features/feature-Hashing1.png)

Na poniższej ilustracji przedstawiono, jakie tych nowych funkcji wyglądały.

![Przykład "Funkcji Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Podsumowanie
Funkcje zaprojektowanych i wybranego zwiększenia efektywności procesu uczenia, który próbuje wyodrębnić kluczowe informacje zawarte w danych. Mogą również zwiększyć możliwości tych modeli do klasyfikowania dokładnie dane wejściowe i bardziej niezawodnie przewidywanie wyników zainteresowania. Funkcja inżynieria i wybór można także połączyć się bardziej praktyce tractable szkoleniowe. Robi to poprawa, a następnie zmniejszenie liczby funkcje niezbędne do kalibrować lub wytrenuj model. Funkcje wybrane do nauczenia modelu, ze sobą matematycznie rzecz biorąc, są minimalny zestaw zmienne niezależne, wyjaśnić wzorce w danych, które następnie pomyślnie przewidywania wyników.

Nie zawsze jest koniecznie do wykonywania funkcji inżynierii lub funkcji wyboru cech. Czy jest potrzebne, czy nie zależy od danych ręcznie lub zebrane algorytmu, który został wybrany, a celem eksperymentu.

