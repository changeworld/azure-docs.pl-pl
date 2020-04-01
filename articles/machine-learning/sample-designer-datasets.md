---
title: Używanie przykładowych zestawów danych w projektancie usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o przykładowych zestawach danych uwzględnionych w projektancie usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037304"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Użyj przykładowych zestawów danych w projektancie usługi Azure Machine Learning (wersja zapoznawcza)

Podczas tworzenia nowego potoku w projektancie usługi Azure Machine Learning (wersja zapoznawcza), liczba przykładowych zestawów danych są uwzględniane domyślnie. Te przykładowe zestawy danych są używane przez przykładowe potoki na stronie głównej projektanta. 

Przykładowe zestawy danych są dostępne w kategorii- **Przykłady.****Samples** Można to znaleźć w palecie modułów po lewej stronie kanwy w projektancie. Można użyć dowolnego z tych zestawów danych w własnym potoku, przeciągając go do kanwy.

## <a name="datasets"></a>Zestawy danych


| Nazwa&nbsp;zestawu danych&nbsp;&nbsp;&nbsp;&nbsp;| Opis zestawu danych |
|-------------|:--------------------|
| Zestaw danych klasyfikacji binarnej dochodów dla dorosłych | Podzbiór bazy danych Census z 1994 r., z wykorzystaniem pracujących osób dorosłych w wieku powyżej 16 lat ze skorygowanym wskaźnikiem dochodów > 100.<br/>**Użycie:** klasyfikowanie osób korzystających z danych demograficznych w celu przewidywania, czy dana osoba zarabia ponad 50 tys.<br/> **Powiązane badania**: Kohavi, R., Becker, B., (1996). [Repozytorium uczenia maszynowego UCI](https://archive.ics.uci.edu/ml). Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki|
|Dane o cenach samochodów (raw)|Informacje o samochodach według marki i modelu, w tym cena, funkcje, takie jak liczba cylindrów i MPG, a także ocena ryzyka ubezpieczeniowego.<br/> Ocena ryzyka jest początkowo skojarzona z ceną automatyczną. Następnie jest on dostosowywany do rzeczywistego ryzyka w procesie znanym aktuariuszom jako symbol. Wartość +3 wskazuje, że auto jest ryzykowne, a wartość -3, że jest prawdopodobnie bezpieczne.<br/>**Użycie:**</b> Przewidzieć ocenę ryzyka według funkcji, przy użyciu regresji lub klasyfikacji wielowymiarowej.<br/>**Powiązane**badania</b> : Schlimmer, J.C. (1987). [Repozytorium uczenia maszynowego UCI](https://archive.ics.uci.edu/ml). Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki. |
| Udostępnione etykiety apeczności CRM |Etykiety z wyzwania przewidywania relacji z klientami KDD Cup 2009[(orange_small_train_appetency.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)|
|Udostępnione etykiety rezygnacji CRM|Etykiety z wyzwania przewidywania relacji z klientami KDD Cup 2009[(orange_small_train_churn.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)|
|Udostępnione zestaw danych CRM | Dane te pochodzą z wyzwania przewidywania relacji z klientami KDD Cup 2009 ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Zestaw danych zawiera 50K klientów z francuskiej firmy telekomunikacyjnej Orange. Każdy klient ma 230 zanonimizowanych funkcji, z których 190 jest numerycznych, a 40 jest kategorycznych. Funkcje są bardzo rzadkie. |
|Etykiety sprzedaży dodatkowej CRM udostępnione|Etykiety z wyzwania przewidywania relacji z klientami KDD Cup 2009 ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Dane dotyczące opóźnień lotów|Dane dotyczące wydajności lotu pasażerskiego na czas pochodzą z gromadzenia danych TranStats Departamentu Transportu USA[(On-Time).](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)<br/>Zestaw danych obejmuje okres od kwietnia do października 2013 r. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób: <br/>- Zestaw danych został przefiltrowany, aby objąć tylko 70 najbardziej ruchliwych lotnisk w kontynentalnych Stanach Zjednoczonych <br/>- Odwołane loty zostały oznaczone jako opóźnione o ponad 15 minut <br/>- Przekierowane loty zostały odfiltrowane <br/>- Wybrano następujące kolumny: Rok, Miesiąc, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Anulowano|
|Niemiecki zestaw danych UCI karty kredytowej|Zestaw danych UCI Statlog (German Credit Card)[(Statlog+German+Credit+Data)](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))przy użyciu pliku german.data.<br/>Zestaw danych klasyfikuje osoby, opisane przez zestaw atrybutów, jako niskie lub wysokie ryzyko kredytowe. Każdy przykład reprezentuje osobę. Istnieje 20 funkcji, zarówno numeryczne i kategoryczne, jak i etykieta binarna (wartość ryzyka kredytowego). Wpisy wysokiego ryzyka kredytowego mają etykietę = 2, wpisy o niskim ryzyku kredytowym mają etykietę = 1. Koszt błędnej klasyfikacji przykładu niskiego ryzyka jako wysokiego wynosi 1, podczas gdy koszt błędnej klasyfikacji przykładu wysokiego ryzyka jako niski wynosi 5.|
|Tytuły filmów IMDB|Zestaw danych zawiera informacje o filmach, które zostały ocenione w tweetach na Twitterze: identyfikator filmu IMDB, nazwa filmu, gatunek i rok produkcji. W zestawie danych znajduje się 17K filmów. Zestaw danych został wprowadzony w dokumencie "S. Dooms, T. De Pessemier i L. Martens. MovieTweetings: Zestaw danych ocena filmu zebrane z Twitter. Warsztaty na temat crowdsourcingu i obliczeń ludzkich dla systemów polecaczy, CrowdRec na RecSys 2013."|
|Oceny filmów|Zestaw danych jest rozszerzoną wersją zestawu danych Movie Tweetings. Zestaw danych ma oceny 170K dla filmów, wyodrębnione z dobrze zorganizowanych tweetów na Twitterze. Każde wystąpienie reprezentuje tweet i jest krotka: identyfikator użytkownika, IMDB movie ID, ocena, sygnatura czasowa, liczba ulubionych dla tego tweeta, a liczba retweets tego tweet. Zestaw danych został udostępniony przez A. Said, S. Dooms, B. Loni i D. Tikk dla Recommender Systems Challenge 2014.|
|Zestaw danych pogodowych|Godzinowe obserwacje pogody lądowej z NOAA ([połączone dane z 201304 do 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Dane pogodowe obejmują obserwacje wykonane ze stacji pogodowych lotniska, obejmujące okres kwiecień-październik 2013 r. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:    <br/> - Identyfikatory stacji meteorologicznych zostały zmapowane na odpowiednie identyfikatory lotniska    <br/> - Stacje pogodowe niezwiązane z 70 najbardziej ruchliwych lotnisk zostały odfiltrowane    <br/> - Kolumna Data została podzielona na osobne kolumny Rok, Miesiąc i Dzień    <br/> - Wybrano następujące kolumny: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Widoczność, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter, Altimeter|
|Zestaw danych Wikipedii SP 500|Dane pochodzą z Wikipedii (https://www.wikipedia.org/) na podstawie artykułów każdej firmy S&P 500, przechowywanych jako dane XML.    <br/>Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:    <br/> - Wyodrębnij zawartość tekstową dla każdej konkretnej firmy    <br/> - Usuń formatowanie wiki    <br/> - Usuń znaki niealnenumeryczne    <br/> - Konwertowanie całego tekstu na małe litery    <br/> - Dodano znane kategorie firm    <br/>Należy zauważyć, że w przypadku niektórych firm nie można odnaleźć artykułu, więc liczba rekordów jest mniejsza niż 500.|

## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego dzięki [samouczkowi: Przewidywanie ceny samochodów z projektantem](tutorial-designer-automobile-price-train-score.md)

* Dowiedz się, jak zmodyfikować istniejące [przykłady projektanta,](samples-designer.md) aby dostosować je do swoich potrzeb.
