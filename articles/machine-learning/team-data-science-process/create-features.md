---
title: Inżynieria funkcji w naukach o danych - Team Data Science Process
description: Wyjaśnia cele inżynierii funkcji i zawiera przykłady jego roli w procesie ulepszania danych uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721136"
---
# <a name="feature-engineering-in-data-science"></a>Inżynieria funkcji w naukach o danych
W tym artykule wyjaśniono cele inżynierii funkcji i przedstawiono przykłady jego roli w procesie ulepszania danych uczenia maszynowego. Przykłady używane do zilustrowania tego procesu są pobierane z usługi Azure Machine Learning Studio. 

To zadanie jest krokiem w [procesie nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

Inżynieria funkcji próbuje zwiększyć predykcyjną moc algorytmów uczenia się, tworząc funkcje z nieprzetworzonych danych, które ułatwiają proces uczenia się. Inżynieria i wybór funkcji jest jedną z części TDSP opisane w [co to jest cykl życia procesu nauki o danych zespołu?](overview.md) Inżynieria funkcji i wybór są częścią kroku **Rozwijaj funkcje** TDSP. 

* **inżynieria funkcji:** Ten proces próbuje utworzyć dodatkowe odpowiednie funkcje z istniejących nieprzetworzonych funkcji w danych i zwiększyć predykcyjną moc algorytmu uczenia się.
* **wybór funkcji:** Ten proces wybiera kluczowy podzbiór oryginalnych obiektów danych, starając się zmniejszyć wymiarowość problemu szkoleniowego.

Zwykle **inżynieria funkcji** jest stosowana najpierw do generowania dodatkowych operacji, a następnie krok **wyboru operacji** jest wykonywany w celu wyeliminowania nieistotnych, nadmiarowych lub wysoce skorelowanych operacji.

Dane szkoleniowe używane w uczeniu maszynowym często można ulepszyć, wyodrębniając funkcje z zebranych nieprzetworzonych danych. Przykładem inżynierii funkcji w kontekście uczenia się, jak klasyfikować obrazy znaków odręcznych jest tworzenie mapy gęstości bitów zbudowane z nieprzetworzonych danych dystrybucji bitów. Ta mapa może pomóc zlokalizować krawędzie znaków bardziej efektywnie niż po prostu przy użyciu dystrybucji nieprzetworzonej bezpośrednio.

Aby utworzyć funkcje dla danych w określonych środowiskach, zobacz następujące artykuły:

* [Tworzenie funkcji danych w programie SQL Server](create-features-sql-server.md)
* [Tworzenie funkcji dla danych w klastrze Hadoop przy użyciu zapytań hive](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Tworzenie funkcji na podstawie danych — inżynieria funkcji
Dane szkoleniowe składają się z macierzy składającej się z przykładów (rekordów lub obserwacji przechowywanych w wierszach), z których każda ma zestaw funkcji (zmienne lub pola przechowywane w kolumnach). Funkcje określone w projekcie eksperymentalnym powinny charakteryzować wzorce w danych. Chociaż wiele pól nieprzetworzonych danych może być bezpośrednio uwzględnionych w wybranym zestawie funkcji używanym do szkolenia modelu, często jest tak, że dodatkowe (zaprojektowane) funkcje muszą być zbudowane z funkcji w nieprzetworzonych danych w celu wygenerowania rozszerzonego zestawu danych szkoleniowych.

Jakie funkcje należy utworzyć w celu zwiększenia zestawu danych podczas szkolenia modelu? Zaprojektowane funkcje, które zwiększają szkolenia zapewniają informacje, które lepiej odróżnia wzorce w danych. Oczekuje się, że nowe funkcje zapewnią dodatkowe informacje, które nie są wyraźnie przechwycone lub łatwo widoczne w oryginalnym lub istniejącym zestawie funkcji. Ale ten proces jest czymś w rodzaju sztuki. Rozsądne i produktywne decyzje często wymagają pewnej wiedzy specjalistycznej w dziedzinie.

Podczas uruchamiania usługi Azure Machine Learning najłatwiej jest zrozumieć ten proces konkretnie przy użyciu przykładów dostarczonych w Studio. Tutaj przedstawiono dwa przykłady:

* Przykład regresji [Przewidywanie liczby wypożyczeń rowerów](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) w nadzorowanym eksperymencie, w którym znane są wartości docelowe
* Przykład klasyfikacji wyszukiwania tekstu przy użyciu [funkcji Mieszanie](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Przykład 1: Dodawanie operacji czasowych dla modelu regresji
Użyjmy eksperymentu "Prognozowanie popytu rowerów" w usłudze Azure Machine Learning Studio (klasyczny), aby zademonstrować, jak zaprojektować funkcje dla zadania regresji. Celem tego eksperymentu jest przewidzenie popytu na rowery, czyli liczby wypożyczeń rowerów w określonym miesiącu / dniu / godzinę. Zestaw danych "Bike Rental UCI dataset" jest używany jako nieprzetworzone dane wejściowe. Ten zestaw danych jest oparty na rzeczywistych danych firmy Capital Bikeshare, która prowadzi sieć wypożyczeń rowerów w Waszyngtonie w Stanach Zjednoczonych. Zestaw danych reprezentuje liczbę wypożyczeń rowerów w określonej godzinie dnia w latach 2011 i 2012 i zawiera 17379 wierszy i 17 kolumn. Surowy zestaw funkcji zawiera warunki pogodowe (temperatura /wilgotność / prędkość wiatru) i rodzaj dnia (święto / dzień tygodnia). Pole do przewidzenia to liczba "cnt", która reprezentuje wypożyczalnie rowerów w określonej godzinie i która waha się od 1 do 977.

W celu skonstruowania efektywnych funkcji w danych szkoleniowych cztery modele regresji są tworzone przy użyciu tego samego algorytmu, ale z czterema różnymi zestawami danych szkoleniowych. Cztery zestawy danych reprezentują te same nieprzetworzone dane wejściowe, ale z rosnącą liczbą ustawionych funkcji. Te funkcje są podzielone na cztery kategorie:

1. A = pogoda + święto + dzień powszedni + funkcje weekendowe dla przewidywanego dnia
2. B = liczba rowerów wypożyczonych w każdej z poprzednich 12 godzin
3. C = liczba rowerów wypożyczonych w każdym z poprzednich 12 dni o tej samej godzinie
4. D = liczba rowerów wypożyczonych w każdym z poprzednich 12 tygodni o tej samej godzinie i tego samego dnia

Oprócz zestawu funkcji A, który już istnieje w oryginalnych danych pierwotnych, pozostałe trzy zestawy funkcji są tworzone w procesie inżynierii funkcji. Zestaw funkcji B rejestruje najnowsze zapotrzebowanie na rowery. Zestaw funkcji C rejestruje zapotrzebowanie na rowery o określonej godzinie. Zestaw funkcji D rejestruje popyt na rowery w określonej godzinie i danym dniu tygodnia. Cztery zestawy danych szkoleniowych, z których każdy zawiera odpowiednio zestaw funkcji A, A+B, A+B+C i A+B+C+D.

W eksperymencie usługi Azure Machine Learning te cztery zestawy danych szkoleniowych są tworzone za pomocą czterech gałęzi z wstępnie przetworzonego zestawu danych wejściowych. Z wyjątkiem gałęzi po lewej stronie, każda z tych gałęzi zawiera moduł [Wykonywanie skryptu R,](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) w którym funkcje pochodne (zestaw funkcji B, C i D) są odpowiednio konstruowane i dołączane do importowanego zestawu danych. Na poniższym rysunku przedstawiono skrypt języka R używany do tworzenia zestawu funkcji B w drugiej gałęzi lewej.

![tworzenie funkcji](./media/create-features/addFeature-Rscripts.png)

Porównanie wyników wydajności czterech modeli podsumowano w poniższej tabeli: 

![porównanie wyników](./media/create-features/result1.png)

Najlepsze wyniki są pokazane przez funkcje A + B + C. Poziom błędu zmniejsza się, gdy dodatkowy zestaw funkcji są zawarte w danych szkoleniowych. Weryfikuje domniemanie, że zestaw funkcji B, C zapewniają dodatkowe istotne informacje dla zadania regresji. Ale dodanie funkcji D nie wydaje się zapewnić dodatkowe zmniejszenie poziomu błędu.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>Przykład 2: Tworzenie funkcji w eksploracji tekstu
Inżynieria funkcji jest szeroko stosowana w zadaniach związanych z wyszukiwaniem tekstu, takich jak klasyfikacja dokumentów i analiza tonacji. Na przykład, jeśli chcesz sklasyfikować dokumenty do kilku kategorii, typowym założeniem jest to, że słowo/frazy zawarte w jednej kategorii doc są mniej prawdopodobne w innej kategorii dokumentów. Innymi słowy, częstotliwość rozkładu słów/fraz jest w stanie scharakteryzować różne kategorie dokumentów. W aplikacjach wyszukiwania tekstu, ponieważ poszczególne fragmenty zawartości tekstu zwykle służą jako dane wejściowe, proces inżynierii funkcji jest potrzebny do tworzenia funkcji obejmujących częstotliwości wyrazów/fraz.

Aby osiągnąć to zadanie, stosuje się technikę zwaną **mieszaniem funkcji,** aby skutecznie przekształcić dowolne funkcje tekstowe w indeksy. Zamiast kojarzenia każdej funkcji tekstowej (słowa/frazy) z określonym indeksem, ta metoda działa, stosując funkcję mieszania do funkcji i używając ich wartości mieszania jako indeksów bezpośrednio.

W usłudze Azure Machine Learning istnieje moduł [hashing funkcji,](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) który tworzy funkcje word/phrase wygodnie. Poniższy rysunek przedstawia przykład użycia tego modułu. Wejściowy zestaw danych zawiera dwie kolumny: klasyfikację książek od 1 do 5 oraz rzeczywistą zawartość recenzji. Celem tego modułu [haszowania funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) jest pobranie kilku nowych funkcji, które pokazują częstotliwość występowania odpowiednich wyrazów/fraz w ramach danego przeglądu książki. Aby użyć tego modułu, wykonaj następujące kroki:

* Najpierw zaznacz kolumnę zawierającą tekst wejściowy ("Col2" w tym przykładzie).
* Po drugie, ustaw "Hashing bitsize" na 8, co oznacza, że zostaną utworzone 2 ^ 8 = 256 funkcje zostaną utworzone. Słowo/faza we wszystkich tekstach zostanie zasysane do 256 indeksów. Parametr "Hashing bitsize" waha się od 1 do 31. Wyrazy/frazy są mniej prawdopodobne, że zostaną zasysane do tego samego indeksu, jeśli ustawienie go jest większą liczbą.
* Po trzecie, ustaw parametr "N-grams" na 2. Ta wartość pobiera częstotliwość występowania unigramów (funkcja dla każdego pojedynczego wyrazu) i bigramów (funkcja dla każdej pary sąsiednich słów) z tekstu wejściowego. Parametr "N-gramów" waha się od 0 do 10, co wskazuje maksymalną liczbę sekwencyjnych słów, które mają zostać uwzględnione w operacji.  

![Moduł "Funkcje haszowania"](./media/create-features/feature-Hashing1.png)

Na poniższym rysunku przedstawiono wygląd tej nowej funkcji.

![Przykład "Hashing funkcji"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Podsumowanie
Zaprojektowane i wybrane funkcje zwiększają wydajność procesu szkoleniowego, który próbuje wyodrębnić kluczowe informacje zawarte w danych. Zwiększają one również moc tych modeli, aby dokładnie klasyfikować dane wejściowe i bardziej solidnie przewidywać wyniki zainteresowania. Inżynieria funkcji i wybór mogą również łączyć się, aby nauka była bardziej liczna pod względem obliczeniowym. Czyni to poprzez zwiększenie, a następnie zmniejszenie liczby funkcji potrzebnych do kalibracji lub szkolenia modelu. Matematycznie rzecz biorąc, funkcje wybrane do uczenia modelu są minimalny zestaw niezależnych zmiennych, które wyjaśniają wzorce w danych, a następnie pomyślnie przewidzieć wyniki.

Nie zawsze jest to konieczne do wykonywania inżynierii funkcji lub wyboru funkcji. To, czy jest to potrzebne, czy nie, zależy od danych do przekazania lub zebranych, wybranego algorytmu i celu eksperymentu.

