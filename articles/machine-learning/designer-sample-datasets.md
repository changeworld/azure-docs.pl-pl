---
title: Korzystanie z przykładowych zestawów danych
titleSuffix: Azure Machine Learning
description: Opisy zestawów danych używanych w przykładowych modelach uwzględnionych w projektancie Machine Learning. Możesz użyć tych przykładowych zestawów danych dla potoków.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165064"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Korzystanie z przykładowych zestawów danych w programie Azure Machine Learning Designer (wersja zapoznawcza)

W przypadku tworzenia nowego potoku w programie Azure Machine Learning Designer (wersja zapoznawcza) domyślnie są uwzględniane różne przykładowe zestawy danych. Wiele z tych przykładowych zestawów danych jest używanych przez modele przykładowe na stronie głównej projektanta. Inne są uwzględniane jako przykłady różnych typów danych zwykle używanych w uczeniu maszynowym.

Niektóre z tych zestawów danych są dostępne w usłudze Azure Blob Storage. W przypadku tych zestawów danych Poniższa tabela zawiera link bezpośredni. Tych zestawów danych można używać w potokach przy użyciu modułu [Importuj dane](./algorithm-module-reference/import-data.md) .

Pozostałe te przykładowe zestawy danych są dostępne w obszarze **zestawy danych**-**Samples** . Można to znaleźć w palecie modułów z lewej strony kanwy w projektancie. Możesz użyć dowolnego z tych zestawów danych we własnym potoku, przeciągając go do kanwy.

## <a name="datasets"></a>Zestawy danych

<table>

<tr>
  <th>Nazwa zestawu danych</th>
  <th>Opis zestawu danych</th>
</tr>

<tr>
  <td>Binarny zestaw danych klasyfikacji dochodów z spisu dla dorosłych</td>
  <td>
Podzbiór bazy danych spisu 1994 przy użyciu pracy dla dorosłych w wieku 16 ze skorygowanym indeksem dochodu > 100.
<p></p>
<b>Użycie:</b> Klasyfikuj osoby korzystające z demograficznych, aby przewidzieć, czy osoba uzyskuje ponad 50 000 rocznie.
<p></p>
<b>Powiązane badania:</b> Kohavi, R., Becker, B., (1996). Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>repozytorium. Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Dane cen samochodów (RAW)</td>
  <td>
Informacje na temat samochodów i modeli, w tym Cena, funkcje, takie jak liczba cylindrów i MPG, oraz Ocena ryzyka dla ubezpieczenia.
<p></p>
Ocena ryzyka jest początkowo skojarzona z funkcją autoprice. Następnie jest dostosowywany do rzeczywistego ryzyka w procesie znanym jako symbol aktuarialny. Wartość + 3 wskazuje, że jest to ryzykowne i wartość-3, która prawdopodobnie jest bezpieczna.
<p></p>
<b>Użycie:</b> Przewidywanie oceny ryzyka według funkcji przy użyciu regresji lub klasyfikacji wieloczynnikowa. 
<p></p>
<b>Powiązane badania:</b> Schlimmer, J.C. (1987). Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>repozytorium. Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>


<tr>
  <td>Udostępnione etykiety programu CRM pragnienie</td>
  <td>
Etykiety z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>) z przewidywania relacji klientów.
  </td>
</tr>

<tr>
  <td>Udostępnione etykiety zmian CRM</td>
  <td>
Etykiety z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>) z przewidywania relacji klientów.
  </td>
</tr>

<tr>
  <td>Udostępniony zestaw danych programu CRM</td>
  <td>
Te dane pochodzą z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train. Data. zip</a>).
<p></p>
Zestaw danych zawiera klientów 50 000 z niemieckiej firmy telekomunikacyjnej pomarańczowa. Każdy klient ma 230 funkcje anonimowe, 190, które są liczbowe i 40 są kategorii. Funkcje są bardzo rozrzedzone.
  </td>
</tr>

<tr>
  <td>Udostępnione etykiety CRM do sprzedawania</td>
  <td>
Etykiety z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>) z przewidywania relacji klientów.
  </td>
</tr>

<tr>
  <td>Dane dotyczące opóźnień lotów</td>
  <td>
Dane o wydajności w czasie lotu pasażera wykonywane z TranStats zbierania danych z działu transportu USA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">w czasie</a>).
<p></p>
Ten zestaw danych obejmuje czas od kwietnia do 2013 października. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Zestaw danych został przefiltrowany w celu pokrycia najgorętszym portów lotniczych o 70 w Stanach Zjednoczonych</li>
  <li>Anulowane loty zostały oznaczone jako opóźnione o więcej niż 15 minut</li>
  <li>Przekierowane loty zostały odfiltrowane</li>
  <li>Wybrano następujące kolumny: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, anulowane</li>
</ul>
</td>
</tr>

<tr>
  <td>Zestaw danych</td>
  <td>
Element dataset Statlog (niemieckiej karty kredytowej) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + niemiecki + Credit + Data</a>) przy użyciu pliku niemieckiego. Data.
<p></p>
Zestaw danych klasyfikuje osoby, opisane przez zestaw atrybutów, jako niskie lub wysokie ryzyko kredytowe. Każdy przykład reprezentuje osobę. Istnieje 20 funkcji, zarówno liczbowych, jak i kategorii oraz etykieta binarna (wartość ryzyka kredytowego). Wpisy o wysokim ryzyku kredytowym mają etykietę = 2, niski poziom ryzyka kredytowego ma etykietę = 1. Koszt niewłaściwego klasyfikowania przykładu niskiego ryzyka o wartości 1 oznacza, że kosztem nieznaczącego klasyfikowania przykładu wysokiego ryzyka jest 5.
  </td>
</tr>

<tr>
  <td>IMDB tytuły filmów</td>
  <td>
Zestaw danych zawiera informacje o filmach, które zostały ocenione w Tweetach w serwisie Twitter: IMDB, nazwa filmu, gatunek i rok produkcyjny. W zestawie danych znajdują się 17K filmy. Zestaw danych został wprowadzony w papierze "S". Dooms, T. de Pessemier i L. Martens. MovieTweetings: zestaw danych klasyfikacji filmu zebrany z serwisu Twitter. Warsztat na crowdsourcing i ludzi obliczeń dla systemów zalecających, CrowdRec o RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Klasyfikacje filmów</td>
  <td>
Zestaw danych to rozszerzona wersja zestawu danych tweetów filmów. Zestaw danych zawiera klasyfikacje 170K dla filmów, wyodrębnione z dobrze zorganizowanych tweetów w serwisie Twitter. Każde wystąpienie reprezentuje Tweet i jest krotką: identyfikator użytkownika, identyfikator filmu IMDB, klasyfikacja, sygnatura czasowa, liczba ulubionych dla tego tweetu i liczba przesłanych tweetów tego tweetu. Zestaw danych został udostępniony przez. wspomniane, S. Dooms, B. Loni i D. Tikk dla systemów polecających 2014.
  </td>
</tr>


<tr>
  <td>Zestaw danych pogody</td>
  <td>
Codzienne obserwacje pogodowe z NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">scalone dane z 201304 do 201310</a>).
<p></p>
Dane pogodowe obejmują obserwacje z stacji pogodowych portów lotniczych, obejmujących okres od kwietnia do października 2013. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Identyfikatory stacji pogody zostały zamapowane na odpowiednie identyfikatory portów lotniczych</li>
  <li>Stacje pogody nie są skojarzone z najgorętszym portów lotniczych 70 zostały odfiltrowane</li>
  <li>Kolumna Date została podzielona na oddzielne kolumny Year, month i Day.</li>
  <li>Wybrano następujące kolumny: AirportID, Year, month, Day, Time, TimeZone, SkyCondition, Visibility, Pogodatype, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, recordType, HourlyPrecip, ALTIMETER</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
Dane są wyprowadzane z witryny Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) na podstawie artykułów każdej usługi S & P 500 firmy, przechowywanej jako dane XML.
<p></p>
Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Wyodrębnij zawartość tekstową dla każdej konkretnej firmy</li>
  <li>Usuwanie formatowania wiki</li>
  <li>Usuń znaki inne niż alfanumeryczne</li>
  <li>Konwertuj cały tekst na małe litery</li>
  <li>Dodano znane kategorie firmy</li>
</ul>
<p></p>
Należy zauważyć, że w niektórych firmach nie znaleziono artykułu, więc liczba rekordów jest mniejsza niż 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem projektanta](tutorial-designer-automobile-price-train-score.md)

* Importowanie przykładowych zestawów danych przy użyciu modułu [Importuj dane](./algorithm-module-reference/import-data.md)
