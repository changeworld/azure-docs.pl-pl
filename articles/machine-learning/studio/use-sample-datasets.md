---
title: Korzystanie z przykładowych zestawów danych
titleSuffix: ML Studio (classic) - Azure
description: Opisy zestawów danych używanych w przykładowych modelach uwzględnionych w uściślii machine learning studio (klasyczny). Te przykładowe zestawy danych można użyć dla eksperymentów.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: b7b8606f7f15f8d6fdd66681a1c7ade60ff506f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217781"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Użyj przykładowych zestawów danych w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[top]: #machine-learning-sample-datasets

Podczas tworzenia nowego obszaru roboczego w usłudze Azure Machine Learning Studio (klasyczny), liczba przykładowych zestawów danych i eksperymentów są uwzględniane domyślnie. Wiele z tych przykładowych zestawów danych jest używanych przez przykładowe modele w [galerii sztucznej inteligencji platformy Azure.](https://gallery.azure.ai/) Inne są uwzględniane jako przykłady różnych typów danych zwykle używanych w uczeniu maszynowym.

Niektóre z tych zestawów danych są dostępne w magazynie obiektów Blob platformy Azure. Dla tych zestawów danych w poniższej tabeli znajduje się bezpośrednie łącze. Tych zestawów danych można użyć w eksperymentach przy użyciu modułu [Importuj dane.][import-data]

Pozostałe przykładowe zestawy danych są dostępne w obszarze roboczym w obszarze **Zapisane zestawy danych**. Można to znaleźć w palecie modułów po lewej stronie kanwy eksperymentu w udiod machine learning studio (klasyczny).
Możesz użyć dowolnego z tych zestawów danych we własnym eksperymencie, przeciągając je do kanwy eksperymentu.

## <a name="datasets"></a>Zestawy danych

<table>

<tr>
  <th>Nazwa zestawu danych</th>
  <th>Opis zestawu danych</th>
</tr>

<tr>
  <td>Zestaw danych klasyfikacji binarnej dochodów dla dorosłych</td>
  <td>
Podzbiór bazy danych Census z 1994 r., z wykorzystaniem pracujących osób dorosłych w wieku powyżej 16 lat ze skorygowanym wskaźnikiem dochodów > 100.
<p></p>
<b>Zastosowanie:</b> Klasyfikuj osoby korzystające z danych demograficznych, aby przewidzieć, czy dana osoba zarabia ponad 50 tys.
<p></p>
<b>Powiązane badania:</b> Kohavi, R., Becker, B., (1996). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki </td>
</tr>

<tr>
  <td>Zestaw danych kodów lotnisk</td>
  <td>
kody lotnisk w USA.
<p></p>
Ten zestaw danych zawiera jeden wiersz dla każdego lotniska w USA, podając numer i nazwę lotniska wraz z miastem i stanem lokalizacji.
  </td>
</tr>

<tr>
  <td>Dane o cenach samochodów (raw)</td>
  <td>
Informacje o samochodach według marki i modelu, w tym cena, funkcje, takie jak liczba cylindrów i MPG, a także ocena ryzyka ubezpieczeniowego.
<p></p>
Ocena ryzyka jest początkowo skojarzona z ceną automatyczną. Następnie jest on dostosowywany do rzeczywistego ryzyka w procesie znanym aktuariuszom jako symbol. Wartość +3 wskazuje, że auto jest ryzykowne, a wartość -3, że jest prawdopodobnie bezpieczne.
<p></p>
<b>Zastosowanie:</b> Przewiduj ocenę ryzyka według funkcji, używając regresji lub klasyfikacji wielowymiarowej. 
<p></p>
<b>Powiązane badania:</b> Schlimmer, J.C. (1987). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki </td>
</tr>

<tr>
  <td>Zestaw danych UCI do wypożyczenia roweru</td>
  <td>
Zestaw danych UCI Bike Rental, który jest oparty na prawdziwych danych firmy Capital Bikeshare, która utrzymuje sieć wypożyczeń rowerów w Waszyngtonie.
<p></p>
Zestaw danych ma jeden wiersz dla każdej godziny każdego dnia w 2011 i 2012 r., w sumie 17 379 wierszy. Liczba godzinowych wypożyczalni rowerów wynosi od 1 do 977.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB Obraz</td>
  <td>
Publicznie dostępny plik obrazu przekonwertowany na dane CSV.
<p></p>
Kod do konwersji obrazu znajduje się na <strong>kwantyzacji kolorów przy użyciu K-Means klastrowania</strong> modelu szczegółowo strony.
  </td>
</tr>

<tr>
  <td>Dane dotyczące krwiodawstwa</td>
  <td>
Podzbiór danych z bazy danych dawców krwi w Centrum Obsługi Transfuzji Krwi w Hsin-Chu City na Tajwanie.
<p></p>
Dane dawcy obejmują miesiące od ostatniego oddania krwi) oraz częstotliwość lub całkowitą liczbę oddawania krwi, czas od ostatniego oddania krwi i ilość oddanej krwi.
<p></p>
<b>Zastosowanie:</b> Celem jest przewidzenie poprzez klasyfikację, czy dawca oddał krew w marcu 2007 r., gdzie 1 wskazuje dawcę w okresie docelowym, a 0 nie dawcę. 
<p></p>
<b>Powiązane badania:</b> Yeh, I.C., (2008). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki
<p></p>
Yeh, I-Cheng, Yang, King-Jang i Ting, Tao-Ming, "Odkrycie wiedzy na temat modelu RFM przy użyciu sekwencji Bernoulliego, "Expert Systems with Applications, 2008,<a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Dane dotyczące raka piersi</td>
  <td>
Jeden z trzech zbiorów danych związanych z rakiem dostarczonych przez Instytut Onkologii, który pojawia się często w literaturze uczenia maszynowego. Łączy informacje diagnostyczne z funkcjami z analizy laboratoryjnej około 300 próbek tkanek.
<p></p>
<b>Zastosowanie:</b> Klasyfikuj typ raka, na podstawie 9 atrybutów, z których niektóre są liniowe, a niektóre są kategoryczne. 
<p></p>
<b>Powiązane badania:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki </td>
</tr>

<tr>
  <td>Cechy raka piersi <td>
Zestaw danych zawiera informacje o 102K podejrzanych regionach (kandydatów) obrazów rentgenowskich, z których każdy jest opisany przez 117 funkcji. Funkcje są zastrzeżone, a ich znaczenie nie jest ujawniane przez twórców zestawu danych (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Rak piersi Informacje</td>
  <td>
Zestaw danych zawiera dodatkowe informacje dla każdego podejrzanego regionu obrazu rentgenowskiego. Każdy przykład zawiera informacje (na przykład etykieta, identyfikator pacjenta, współrzędne poprawki względem całego obrazu) o odpowiednim numerze wiersza w zestawie danych Funkcje raka piersi. Każdy pacjent ma kilka przykładów. Dla pacjentów, którzy mają raka, niektóre przykłady są pozytywne, a niektóre są negatywne. Dla pacjentów, którzy nie mają raka, wszystkie przykłady są negatywne. Zestaw danych ma przykłady 102K. Zestaw danych jest stronniczy, 0,6% punktów jest dodatnich, reszta jest ujemna. Zestaw danych został udostępniony przez Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>Udostępnione etykiety apeczności CRM</td>
  <td>
Etykiety z wyzwania przewidywania relacji z klientami KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">(orange_small_train_appetency.labels).</a>
  </td>
</tr>

<tr>
  <td>Udostępnione etykiety rezygnacji CRM</td>
  <td>
Etykiety z wyzwania przewidywania relacji z klientami KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">(orange_small_train_churn.labels).</a>
  </td>
</tr>

<tr>
  <td>Udostępnione zestaw danych CRM</td>
  <td>
Dane te pochodzą z wyzwania przewidywania relacji z klientami KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Zestaw danych zawiera 50K klientów z francuskiej firmy telekomunikacyjnej Orange. Każdy klient ma 230 zanonimizowanych funkcji, z których 190 jest numerycznych, a 40 jest kategorycznych. Funkcje są bardzo rzadkie.
  </td>
</tr>

<tr>
  <td>Etykiety sprzedaży dodatkowej CRM udostępnione</td>
  <td>
Etykiety z wyzwania przewidywania relacji z klientami KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">(orange_large_train_upselling.labels).</a>
  </td>
</tr>

<tr>
  <td>Dane regresji efektywności energetycznej</td>
  <td>
Kolekcja symulowanych profili energetycznych, oparta na 12 różnych kształtach budynków. Budynki różnią się ośmioma elementami. Obejmuje to obszar przeszklenia, rozmieszczenie powierzchni szyb i orientację.
<p></p>
<b>Zastosowanie:</b> Użyj regresji lub klasyfikacji, aby przewidzieć ocenę efektywności energetycznej opartą na jednej z dwóch rzeczywistych ocen wartościowych. W przypadku klasyfikacji wieloklasowej jest zaokrąglanie zmiennej odpowiedzi do najbliższej liczby całkowitej. 
<p></p>
<b>Powiązane badania:</b> Xifara, A. & Tsanas, A. (2012). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki </td>
</tr>

<tr>
  <td>Dane dotyczące opóźnień lotów</td>
  <td>
Dane dotyczące wydajności lotu pasażerskiego na czas pochodzą z gromadzenia danych TranStats Departamentu Transportu USA<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">(On-Time).</a>
<p></p>
Zestaw danych obejmuje okres od kwietnia do października 2013 r. Przed przekazaniem do usługi Azure Machine Learning Studio (klasyczny) zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Zestaw danych został przefiltrowany w celu uwzględnienia tylko 70 najbardziej ruchliwych lotnisk w kontynentalnych Stanach Zjednoczonych</li>
  <li>Odwołane loty zostały oznaczone jako opóźnione o ponad 15 minut</li>
  <li>Przekierowane loty zostały odfiltrowane</li>
  <li>Wybrano następujące kolumny: Rok, Miesiąc, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Anulowano</li>
</ul>
</td>
</tr>

<tr>
  <td>Wydajność lotu na czas (raw)</td>
  <td>
Zapisy przylotów i odlotów samolotów w Stanach Zjednoczonych od października 2011 r.
<p></p>
<b>Zastosowanie:</b> Przewiduj opóźnienia lotów. 
<p></p>
<b>Powiązane badania:</b> Z departamentu transportu <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>USA .
  </td>
</tr>

<tr>
  <td>Dane pożarów lasów</td>
  <td>
Zawiera dane pogodowe, takie jak wskaźniki temperatury i wilgotności oraz prędkość wiatru. Dane pochodzą z obszaru północno-wschodniej Portugalii, w połączeniu z rekordami pożarów lasów.
<p></p>
<b>Zastosowanie:</b> Jest to trudne zadanie regresji, gdzie celem jest przewidywanie spalonego obszaru pożarów lasów. 
<p></p>
<b>Powiązane badania:</b> Cortez, P., & Morais, A. (2008). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki
<p></p>
[Cortez i Morais, 2007] P. Cortez i A. Morais. Podejście eksploracji danych do przewidywania pożarów lasów przy użyciu danych meteorologicznych. W J. Neves, M. F. Santos i J. Machado Eds., Nowe trendy w sztucznej inteligencji, Obrady 13 EPIA 2007 - Portugalska Konferencja na temat sztucznej inteligencji, grudzień, Guimarães, Portugalia, s. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Dostępne pod <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>adresem: .
  </td>
</tr>

<tr>
  <td>Niemiecki zestaw danych UCI karty kredytowej</td>
  <td>
Zestaw danych UCI Statlog (German Credit Card)<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">(Statlog+German+Credit+Data)</a>przy użyciu pliku german.data.
<p></p>
Zestaw danych klasyfikuje osoby, opisane przez zestaw atrybutów, jako niskie lub wysokie ryzyko kredytowe. Każdy przykład reprezentuje osobę. Istnieje 20 funkcji, zarówno numeryczne i kategoryczne, jak i etykieta binarna (wartość ryzyka kredytowego). Wpisy wysokiego ryzyka kredytowego mają etykietę = 2, wpisy o niskim ryzyku kredytowym mają etykietę = 1. Koszt błędnej klasyfikacji przykładu niskiego ryzyka jako wysokiego wynosi 1, podczas gdy koszt błędnej klasyfikacji przykładu wysokiego ryzyka jako niski wynosi 5.
  </td>
</tr>

<tr>
  <td>Tytuły filmów IMDB</td>
  <td>
Zestaw danych zawiera informacje o filmach, które zostały ocenione w tweetach na Twitterze: identyfikator filmu IMDB, nazwa filmu, gatunek i rok produkcji. W zestawie danych znajduje się 17K filmów. Zestaw danych został wprowadzony w dokumencie "S. Dooms, T. De Pessemier i L. Martens. MovieTweetings: Zestaw danych ocena filmu zebrane z Twitter. Warsztaty na temat crowdsourcingu i obliczeń ludzkich dla systemów polecaczy, CrowdRec na RecSys 2013."
  </td>
</tr>

<tr>
  <td>Iris dwa dane klasy</td>
  <td>
Jest to prawdopodobnie najbardziej znana baza danych, która znajduje się w literaturze rozpoznawania wzorców. Zestaw danych jest stosunkowo mały i zawiera 50 przykładów każdego pomiaru płatków z trzech odmian tęczówki.
<p></p>
<b>Zastosowanie:</b> Przewiduj typ tęczówki na podstawie pomiarów.  
<p></p>
<b>Powiązane badania:</b> Fisher, R.A. (1988). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki </td>
</tr>

<tr>
  <td>Tweety filmowe</td>
  <td>
Zestaw danych jest rozszerzoną wersją zestawu danych Movie Tweetings. Zestaw danych ma oceny 170K dla filmów, wyodrębnione z dobrze zorganizowanych tweetów na Twitterze. Każde wystąpienie reprezentuje tweet i jest krotka: identyfikator użytkownika, IMDB movie ID, ocena, sygnatura czasowa, liczba ulubionych dla tego tweeta, a liczba retweets tego tweet. Zestaw danych został udostępniony przez A. Said, S. Dooms, B. Loni i D. Tikk dla Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td>Dane MPG dla różnych samochodów</td>
  <td>
Ten zestaw danych jest nieco zmodyfikowaną wersją zestawu danych dostarczoną przez bibliotekę StatLib z Carnegie Mellon University. Zestaw danych został użyty na wystawie American Statistical Association z 1983 roku.
<p></p>
Dane zawierają informacje o zużyciu paliwa w różnych samochodach w milach na galon. Zawiera również informacje, takie jak liczba cylindrów, skok silnika, moc, masa całkowita i przyspieszenie.
<p></p>
<b>Zastosowanie:</b> Przewiduj zużycie paliwa na podstawie trzech wielowartościowych atrybutów dyskretnych i pięciu atrybutów ciągłych. 
<p></p>
<b>Powiązane badania:</b> StatLib, Carnegie Mellon University, (1993). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki </td>
</tr>

<tr>
  <td>Pima Indians Cukrzyca Binary Klasyfikacja data zestaw danych</td>
  <td>
Podzbiór danych z bazy danych Narodowego Instytutu Cukrzycy i Chorób Przewodu Pokarmowego i Nerek. Zestaw danych został przefiltrowany, aby skupić się na pacjentki pima indyjskiego dziedzictwa. Dane obejmują dane medyczne, takie jak poziom glukozy i insuliny, a także czynniki stylu życia.
<p></p>
<b>Zastosowanie:</b> Przewidzieć, czy podmiot ma cukrzycę (klasyfikacja binarna). 
<p></p>
<b>Powiązane badania:</b> Sigillito, V. (1990). Repozytorium uczenia maszynowego UCI <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml"</a>. Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki </td>
</tr>

<tr>
  <td>Dane klientów restauracji</td>
  <td>
Zestaw metadanych dotyczących klientów, w tym dane demograficzne i preferencje.
<p></p>
<b>Zastosowanie:</b> Ten zestaw danych w połączeniu z dwoma pozostałymi zestawami danych restauracji służy do szkolenia i testowania systemu polecania. 
<p></p>
<b>Powiązane badania:</b> Bache, K. i Lichman, M. (2013). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki.
  </td>
</tr>

<tr>
  <td>Dane dotyczące funkcji restauracji</td>
  <td>
Zestaw metadanych dotyczących restauracji i ich funkcji, takich jak rodzaj jedzenia, styl jadalni i lokalizacja.
<p></p>
<b>Zastosowanie:</b> Ten zestaw danych w połączeniu z dwoma pozostałymi zestawami danych restauracji służy do szkolenia i testowania systemu polecania. 
<p></p>
<b>Powiązane badania:</b> Bache, K. i Lichman, M. (2013). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki.
  </td>
</tr>

<tr>
  <td>Oceny restauracji</td>
  <td>
Zawiera oceny podane przez użytkowników do restauracji w skali od 0 do 2.
<p></p>
<b>Zastosowanie:</b> Ten zestaw danych w połączeniu z dwoma pozostałymi zestawami danych restauracji służy do szkolenia i testowania systemu polecania. 
<p></p>
<b>Powiązane badania:</b> Bache, K. i Lichman, M. (2013). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki.
  </td>
</tr>

<tr>
  <td>Stal wyżarzanie wieloklasowy zestaw danych</td>
  <td>
Ten zestaw danych zawiera serię rekordów z prób wyżarzania stali. Zawiera fizyczne atrybuty (szerokość, grubość, typ (cewka, arkusz itp.) powstałych typów stali.
<p></p>
<b>Zastosowanie:</b> Wytypuj dowolny z dwóch atrybutów klasy numerycznej; twardości lub wytrzymałości. Można również analizować korelacje między atrybutami.
<p></p>
Gatunki stali są zgodne z ustalonym standardem, określonym przez SAE i inne organizacje. Szukasz określonego "klasy" (zmiennej klasy) i chcesz zrozumieć wartości potrzebne. 
<p></p>
<b>Powiązane badania:</b> Sterling, D. & Buntine, W. (NA). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informatyki i Informatyki
<p></p>
Przydatny przewodnik po gatunkach stali można znaleźć tutaj:<a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Dane teleskopu</td>
  <td>
Zapis wysokoenergetycznych wybuchów cząstek gamma wraz z szumem tła, oba symulowane za pomocą procesu Monte Carlo.
<p></p>
Celem symulacji było zwiększenie dokładności naziemnych teleskopów gamma Cherenkov. Odbywa się to za pomocą metod statystycznych w celu rozróżnienia między pożądanym sygnałem (prysznice promieniowania Cherenkov) a hałasem tła (hadroniczne prysznice zainicjowane przez promienie kosmiczne w górnej atmosferze).
<p></p>
Dane zostały wstępnie przetworzone w celu utworzenia wydłużonego klastra z długą osią jest zorientowany w kierunku centrum kamery. Cechy tej elipsy (często nazywane parametrami Hillas) należą do parametrów obrazu, które mogą być używane do dyskryminacji.
<p></p>
<b>Zastosowanie:</b> Wytypuj, czy obraz prysznica reprezentuje sygnał, czy szum tła.
<p></p>
<b>Uwagi:</b> Prosta dokładność klasyfikacji nie ma znaczenia dla tych danych, ponieważ klasyfikowanie zdarzenia w tle jako sygnału jest gorsze niż klasyfikowanie zdarzenia sygnału jako tła. Do porównania różnych klasyfikatorów należy użyć wykresu ROC. Prawdopodobieństwo zaakceptowania zdarzenia w tle jako sygnału musi być poniżej jednego z następujących progów: 0,01, 0,02, 0,05, 0,1 lub 0,2.
<p></p>
Należy również pamiętać, że liczba zdarzeń w tle (h, dla hadronicznych pryszniców) jest niedoszacowana. W rzeczywistych pomiarach klasa h lub noise reprezentuje większość zdarzeń. 
<p></p>
<b>Powiązane badania:</b> Bock, R.K. (1995). Repozytorium uczenia <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>maszynowego UCI . Irvine, CA: Uniwersytet Kalifornijski, Szkoła Informacji </td>
</tr>

<tr>
  <td>Zestaw danych pogodowych</td>
  <td>
Godzinowe obserwacje pogody lądowej z NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">połączone dane z 201304 do 201310</a>).
<p></p>
Dane pogodowe obejmują obserwacje wykonane ze stacji pogodowych lotniska, obejmujące okres kwiecień-październik 2013 r. Przed przekazaniem do usługi Azure Machine Learning Studio (klasyczny) zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Identyfikatory stacji pogodowych zostały zmapowane na odpowiednie identyfikatory lotniska</li>
  <li>Stacje meteorologiczne niezwiązane z 70 najbardziej ruchliwych lotnisk zostały odfiltrowane</li>
  <li>Kolumna Data została podzielona na osobne kolumny Rok, Miesiąc i Dzień</li>
  <li>Wybrano następujące kolumny: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, Prędkość wiatru, kierunek wiatru, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Wysokościomierz</li>
</ul>
  </td>
</tr>

<tr>
  <td>Zestaw danych Wikipedii SP 500</td>
  <td>
Dane pochodzą z Wikipedii (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) na podstawie artykułów każdej firmy S&P 500, przechowywanych jako dane XML.
<p></p>
Przed przekazaniem do usługi Azure Machine Learning Studio (klasyczny) zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Wyodrębnianie zawartości tekstowej dla każdej konkretnej firmy</li>
  <li>Usuwanie formatowania wiki</li>
  <li>Usuwanie znaków innych niż alfanumeryczne</li>
  <li>Konwertowanie całego tekstu na małe litery</li>
  <li>Dodano znane kategorie firm</li>
</ul>
<p></p>
Należy zauważyć, że w przypadku niektórych firm nie można odnaleźć artykułu, więc liczba rekordów jest mniejsza niż 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Zestaw danych zawiera dane klientów i wskazania dotyczące ich odpowiedzi na bezpośrednią kampanię korespondującą. Każdy wiersz reprezentuje klienta. Zestaw danych zawiera dziewięć funkcji dotyczących danych demograficznych użytkowników i zachowania w przeszłości oraz trzy kolumny etykiet (wizyta, konwersja i wydatki).  Wizyta to kolumna binarna, która wskazuje, że klient odwiedził po kampanii marketingowej. Konwersja oznacza, że klient coś kupił. Wydatki to kwota, która została wydana.  Zestaw danych został udostępniony przez Kevina Hillstroma dla MineThatData E-Mail Analytics And Data Mining Challenge.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funkcje przykładów testów w zestawie danych informacyjnych RCV1-V2 Reuters. Zestaw danych zawiera artykuły z wiadomościami 781K wraz z ich identyfikatorami (pierwsza kolumna zestawu danych). Każdy artykuł jest tokenized, stopworded, i stemmed. Zestaw danych został udostępniony przez Davida. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funkcje przykładów szkoleń w zestawie danych informacyjnych RCV1-V2 Reuters. Zestaw danych zawiera artykuły z wiadomościami 23K wraz z ich identyfikatorami (pierwsza kolumna zestawu danych). Każdy artykuł jest tokenized, stopworded, i stemmed. Zestaw danych został udostępniony przez Davida. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Zestaw danych z konkursu KDD Cup 1999 Knowledge Discovery and Data Mining Tools Competition (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Zestaw danych został pobrany i przechowywany w magazynie obiektów Blob platformy Azure<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">(network_intrusion_detection.csv)</a>i zawiera zarówno zestawy danych szkoleniowych, jak i testowych. Zestaw danych szkoleniowych ma około 126K wierszy i 43 kolumny, w tym etykiety. Trzy kolumny są częścią informacji o etykiecie, a 40 kolumn, składających się z funkcji liczbowych i ciąg/kategoryczne, jest dostępnych do szkolenia modelu. Dane testowe ma około 22.5K przykłady testów z tych samych 43 kolumn, jak w danych szkoleniowych.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Przypisania tematów artykułów informacyjnych w zestawie danych informacyjnych RCV1-V2 Reuters. Artykuł z wiadomościami można przypisać do kilku tematów. Format każdego wiersza&lt;to&gt; &lt;" nazwa&gt; tematu dokument id 1". Zestaw danych zawiera przypisania tematów 2,6 mln. Zestaw danych został udostępniony przez Davida. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Dane te pochodzą z wyzwania oceny wyników uczniów KDD Cup 2010<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">(ocena wyników uczniów).</a> Wykorzystane dane to zestaw szkoleniowy Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Zestaw danych wyzwania z KDD Cup 2010 Educational Data Mining Challenge. Znajdź go na <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Zestaw danych został pobrany i przechowywany w magazynie obiektów Blob platformy Azure<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">(student_performance.txt)</a>i zawiera pliki dziennika z systemu korepetytorów dla uczniów. Dostarczone funkcje obejmują identyfikator problemu i jego krótki opis, identyfikator studenta, sygnaturę czasowa i liczbę prób, które uczeń podjął przed rozwiązaniem problemu we właściwy sposób. Oryginalny zestaw danych ma rekordy 8,9 mln; ten zestaw danych został pobrany próbkowane w dół do pierwszych wierszy 100K. Zestaw danych zawiera 23 kolumny oddzielone kartami różnych typów: numeryczne, kategoryczne i sygnatury czasowe.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozpocznij eksperymenty przykładami](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
