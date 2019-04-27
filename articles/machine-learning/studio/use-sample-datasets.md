---
title: Korzystanie z przykładowych zestawów danych
titleSuffix: Azure Machine Learning Studio
description: Opisy zestawów danych użytych w Przykładowe modele uwzględnione w usłudze Machine Learning Studio. Te przykładowe zestawy danych można użyć do eksperymentów.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: f86ae4977621927a09d9b83287a00dfa3bc17196
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736619"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Korzystanie z przykładowych arkuszy danych w usłudze Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

Gdy tworzysz nowy obszar roboczy w usłudze Azure Machine Learning Studio, szereg przykładowych zestawów danych i doświadczeń są domyślnie dołączone. Wiele z tych przykładowych zestawów danych są używane przez Przykładowe modele w [galerii Azure AI](https://gallery.azure.ai/). Inne są dołączone jako przykłady różnych typów danych zwykle używanych w usłudze machine learning.

Niektóre z tych zestawów danych są dostępne w usłudze Azure Blob storage. Dla tych zestawów danych Poniższa tabela zawiera bezpośredni link. Te zestawy danych można użyć w eksperymentów, za pomocą [importu danych] [ import-data] modułu.

Pozostała część tych przykładowych zestawów danych dostępnych w Twoim obszarze roboczym, w obszarze **zapisane zestawów danych**. To można znaleźć w palety modułów, z lewej strony obszaru roboczego eksperymentu w usłudze Machine Learning Studio.
Można użyć dowolnego z tych zestawów danych w eksperymencie własnych, przeciągając go do Twojego obszaru roboczego eksperymentu.

## <a name="datasets"></a>Zestawy danych

<table>

<tr>
  <th>Nazwa zestawu danych</th>
  <th>Opis elementu DataSet</th>
</tr>

<tr>
  <td>Treści dla dorosłych klasyfikacji binarnej dochodu spisu zestawu danych.</td>
  <td>
Podzbiór 1994 r. spisu bazy danych, przy użyciu pracy osoby dorosłe w wieku powyżej 16 z indeksem skorygowany dochodu > 100.
<p></p>
<b>Sposób użycia:</b> Klasyfikowanie osób używających dane demograficzne w celu przewidywania, czy osoba zarabiają ponad 50K rok.
<p></p>
<b>Odpowiednie badania:</b> Kohavi, R., Becker, B., (1996). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Airport Codes Dataset</td>
  <td>
Kody Kuwejcie Stanów Zjednoczonych.
<p></p>
Ten zestaw danych zawiera jeden wiersz dla każdego lotnisko Stanów Zjednoczonych, podając numer identyfikacyjny lotniczego oraz wraz z lokalizacji miasto i województwo.
  </td>
</tr>

<tr>
  <td>Cen samochodów, data (Raw)</td>
  <td>
Informacji na temat samochodów, producent i model, w tym ceny, funkcje, takie jak liczba cylindrów i paliwa, a także ubezpieczenia ryzyka.
<p></p>
Ocena ryzyka to początkowo skojarzone z ceną automatycznie. Następnie jest on uwzględniany rzeczywistego zagrożenia w procesie nazywanym do aktuariuszy jako symboling. Wartość + 3 wskazuje, że automatycznego jest ryzykowne i wartością -3 aby znajdował się prawdopodobnie można bezpiecznie.
<p></p>
<b>Sposób użycia:</b> Przewidywanie ryzyka przy użyciu funkcji, za pomocą regresji lub wielu zmiennych klasyfikacji. 
<p></p>
<b>Odpowiednie badania:</b> Schlimmer, J.C. (1987). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Zestaw danych na wypożyczeń rowerów</td>
  <td>
Zestaw danych wypożyczeń rowerów UCI, który opiera się na prawdziwe dane pochodzące z firmy Bikeshare wielkiej litery, która obsługuje sieci wypożyczeń rowerów w Waszyngtonie.
<p></p>
Zestaw danych zawiera jeden wiersz dla każdej godziny każdego dnia w 2011 r. i 2012, w sumie 17,379 wierszy. Zakres godzin wypożyczenia rowerów jest z zakresu od 1 do 977.

  </td>
</tr>

<tr>
  <td>Obraz RGB Billa Gatesa</td>
  <td>
Plik publicznie dostępnego obrazu są konwertowane na dane w formacie CSV.
<p></p>
Konwertowanie obrazu w kodzie znajduje się w <strong>kolor zaokrąglania korzystania z klastra K-średnich</strong> strony szczegółów modelu.
  </td>
</tr>

<tr>
  <td>Krwi darowizn danych</td>
  <td>
Podzbiór danych z bazy danych dawcy krwi transfuzji krwi Service Center Hsin Chu miasta, Tajwan.
<p></p>
Dane dawcy obejmują miesięcy od czasu ostatniego darowizn) i często i całkowita liczba pobrań, czas od ostatniego darowizn i ilości krwi wymagania.
<p></p>
<b>Sposób użycia:</b> Celem jest przewidzieć przy użyciu klasyfikacji, czy dawcy wymagania krwi w 2007 marca, gdzie 1 oznacza dawcy podczas okresu docelowego i 0 nie dawcy. 
<p></p>
<b>Odpowiednie badania:</b> Yeh, I.C., (2008). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki
<p></p>
Yeh, -Cheng, Yang King Jang, a klasyfikacja, Ming tag, "Knowledge discovery w tryb RFM modelu przy użyciu sekwencji Bernoulli'ego,"systemy eksperta z aplikacjami, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Dane raka piersi</td>
  <td>
Jeden z trzech zestawów powiązanych raka danych, dostarczonych przez Instytut Oncology, który pojawia się często w machine learning w literaturze dostępne. Łączy informacje diagnostyczne z funkcjami analizy laboratorium około 300 hodowlami.
<p></p>
<b>Sposób użycia:</b> Sklasyfikuj typ raka, na podstawie 9 atrybutów, z których niektóre są liniowe, a niektóre są podzielone na kategorie. 
<p></p>
<b>Odpowiednie badania:</b> O.L. Wohlberg, W.H., ulicy, W.N. & Mangasarian, (1995). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Funkcje raka piersi <td>
Zestaw danych zawiera informacje podejrzanych obszarów 102K (kandydatów) obrazów rentgenowskie, opisano każdy przy użyciu funkcji 117. Funkcje te są zastrzeżone i ich znaczenie nie została ujawniona przez twórcy zestawu danych (Siemens opieki zdrowotnej). 
  </td>
</tr>

<tr>
  <td>Informacje o raka piersi</td>
  <td>
Zestaw danych zawiera dodatkowe informacje dla każdego regionu podejrzane rentgenowskie obrazu. Każdy przykład zawiera informacje (na przykład etykieta, pacjentów identyfikator, współrzędne poprawki względem całego obrazu) o numer wiersza w zestawie danych funkcji raka piersi. Każdy pacjent ma wiele przykładów. Dla pacjentów, którzy ma raka przykłady są pozytywne, a inne ujemna. Dla pacjentów, którzy nie ma raka we wszystkich przykładach jest ujemna. Zestaw danych zawiera przykłady 102K. Zestaw danych jest obciążona Update 0.6% punktów jest dodatnia, ujemna pozostałe. Zestaw danych została udostępniona przez Siemens opieki zdrowotnej.
  </td>
</tr>

<tr>
  <td>Etykiety Appetency CRM udostępnione</td>
  <td>
Etykiety z zawodów KDD Cup 2009 klienta relacji prognozowania wyzwanie (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM zmian etykiety udostępnione</td>
  <td>
Etykiety z zawodów KDD Cup 2009 klienta relacji prognozowania wyzwanie (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Zestaw danych CRM, udostępnione</td>
  <td>
Te dane pochodzą z zawodów KDD Cup 2009 klienta relacji prognozowania wyzwanie (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Zestaw danych zawiera 50 tysięcy klientów z firmy telekomunikacyjnej francuski pomarańczowy. Każdy klient ma 230 funkcje anonimowe, 190, które są liczbowe i 40 są podzielone na kategorie. Funkcje są bardzo rozrzedzone.
  </td>
</tr>

<tr>
  <td>Etykiety grup CRM udostępnione</td>
  <td>
Etykiety z zawodów KDD Cup 2009 klienta relacji prognozowania wyzwanie (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Dane wydajności energetycznej regresji</td>
  <td>
Zbiór profilów symulowane energii, na podstawie 12 tworzenie różnych kształtów. Budynki są zróżnicowane według osiem funkcji. W tym szyby obszaru, szyby obszaru dystrybucji i orientacji.
<p></p>
<b>Sposób użycia:</b> Przewidywanie ocenę wydajności energetycznej jako jedną z dwóch rzeczywiste cenionym odpowiedzi, należy użyć klasyfikacji lub regresji. W przypadku klasyfikacji wieloklasowej jest round zmiennej odpowiedzi do najbliższej liczby całkowitej. 
<p></p>
<b>Odpowiednie badania:</b> Xifara, A. & Tsanas, A. (2012). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Flight opóźnia danych</td>
  <td>
Lot pasażerski na czas poświęcony ze zbierania danych TranStats Stanów Zjednoczonych dane dotyczące wydajności Departamentu transportu (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">na czas</a>).
<p></p>
Zestaw danych obejmuje okres kwietnia — październik 2013. Przed przekazaniem do usługi Azure Machine Learning Studio, zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Zestaw danych zostały przefiltrowane na pokrycie tylko 70 najczęściej odwiedzane lotniskach w kontynentalnych Stanach Zjednoczonych</li>
  <li>Anulowano lotów zostały oznaczone jako opóźniony o ponad 15 minut.</li>
  <li>Kierunku lotów zostały odfiltrowane.</li>
  <li>Wybrano następujące kolumny: Rok, miesiąc, DayofMonth, DayOfWeek, operatora, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay ArrDel15, zostało anulowane</li>
</ul>
</td>
</tr>

<tr>
  <td>Flight punktualności lotów (Raw)</td>
  <td>
Rekordy przybyć lotu samolot i odejść na terenie Stanów Zjednoczonych z października 2011.
<p></p>
<b>Sposób użycia:</b> Prognozowanie opóźnień lotów. 
<p></p>
<b>Odpowiednie badania:</b> Od działu transportu USA <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Las generowane dane</td>
  <td>
Zawiera dane o pogodzie, takich jak indeksy temperatury i wilgotności i prędkość wiatru. Dane treningowe pochodzą z obszaru północno-wschodniej Portugalii, w połączeniu z rekordami pożarów lasów.
<p></p>
<b>Sposób użycia:</b> Jest zadaniem trudnym regresji, której celem jest przewidzieć obszaru nagrany pożarów lasów. 
<p></p>
<b>Odpowiednie badania:</b> Cortez, P. & Morais, A. (2008). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki
<p></p>
[Cortez i Morais, 2007] P. Cortez i A. Morais. Dane wyszukiwania podejście do przewidywania pożarów lasów przy użyciu Meteorological danych. In J. Neves, M. F. Santos i J. Machado Eds., nowe trendy w sztuczną inteligencję postępowania 13 2007 EPIA — portugalski konferencji na sztucznej inteligencji grudnia Guimarães, Portugalia, str. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Dostępne pod adresem: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Niemiecki karty kredytowej na zestaw danych</td>
  <td>
Zestaw danych na Statlog (karta kredytowa niemiecki) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog niemiecki + środki + dane +</a>), przy użyciu pliku german.data.
<p></p>
Zestaw danych klasyfikuje osób, opisanego przez zestaw atrybutów jako środków niskie lub wysokie ryzyko. Każdy przykład reprezentuje osobę. Istnieją 20 funkcji, zarówno wartości liczbowych, jak i podzielonych na kategorie i binarny etykietę (wartość ryzyko kredytowe). Środki wysokiego ryzyka wpisy mają etykiety = 2, środki niskie ryzyko wpisy mają etykiety = 1. Koszt misclassifying to przykład niskiego ryzyka jako wysokie wynosi 1, natomiast misclassifying przykład o wysokim ryzyku, jako niski koszt wynosi 5.
  </td>
</tr>

<tr>
  <td>ZAUFANI filmu tytułów</td>
  <td>
Zestaw danych zawiera informacje na temat filmów, które zostały ocenione w tweety w usłudze Twitter: ZAUFANI filmu identyfikator, tytuł filmu, gatunku i roku produkcji. Brak filmów 17K w zestawie danych. Zestaw danych została wprowadzona w dokumencie "S. Dooms T. De Pessemier i L. Martens. MovieTweetings: filmu ocena zestawu danych pobranych z usługi Twitter. Warsztaty Crowdsourcing i ludzi obliczeń systemów polecania CrowdRec na RecSys 2013".
  </td>
</tr>

<tr>
  <td>Dane klasy Iris dwóch</td>
  <td>
Być może jest znaną bazy danych ma zostać odnaleziona w literaturze rozpoznawania wzorca. Zestaw danych jest stosunkowo mały, zawierający 50 przykłady pomiarów płatka z trzech odmian iris.
<p></p>
<b>Sposób użycia:</b> Prognozowania typu irysa z pomiarów.  
<p></p>
<b>Odpowiednie badania:</b> Fishera, R.A. (1988). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Film Tweetów</td>
  <td>
Zestaw danych jest rozszerzona wersja zestawu Tweetings filmu. Zestaw danych zawiera 170K klasyfikację filmów, programów wyodrębnione z odpowiedniej strukturze tweety w serwisie Twitter. Każde wystąpienie reprezentuje tweet i jest spójną kolekcją: identyfikator użytkownika, identyfikator filmu ZAUFANI, klasyfikacji, sygnatury czasowej, liczba Ulubione dla tego tweetu i liczba retweets tego tweetu. Zestaw danych została udostępniona przez powiedział A., S. Dooms, Loni b i D. Tikk polecania systemów wyzwanie 2014.
  </td>
</tr>

<tr>
  <td>Dane MPG dotyczące różnych samochodów</td>
  <td>
Ten zestaw danych jest nieco zmodyfikowaną wersję zestawu danych, dostarczone przez bibliotekę StatLib Carnegie Mellon University. Zestaw danych był używany w 1983 American statystyczne skojarzenia specyfikacji.
<p></p>
Zużycie paliwa dotyczące różnych samochodów w przebiegu w milach na galon wykazy danych. Zawiera także informacje, takie jak liczba cylindrów, aparat przemieszczenia, napędową, łączna waga oraz zwiększanie ich szybkości.
<p></p>
<b>Sposób użycia:</b> Przewidywanie zużycia paliwa w oparciu o trzy wielowartościowych atrybutów dyskretnych i pięciu atrybutów ciągłych. 
<p></p>
<b>Odpowiednie badania:</b> StatLib, Carnegie Mellon University (1993). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Klasyfikacja binarna choroby Indian Pima zestawu danych</td>
  <td>
Podzbiór danych pochodzących z National Institute of choroby i przewodu i chorób nerki bazy danych. Zestaw danych został przefiltrowany skoncentrować się na żeńskiego pacjentów dziedzictwa Rupia Pima. Dane obejmują danych medycznych, takich jak glukozy i poziomy inulinowego, a także lifestyle czynników.
<p></p>
<b>Sposób użycia:</b> Przewidywania, czy temat ma choroby (Klasyfikacja binarna). 
<p></p>
<b>Odpowiednie badania:</b> Sigillito, V. (1990). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml"</a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Dane klienta restauracji</td>
  <td>
Zestaw metadanych dotyczących klientów, w tym dane demograficzne i preferencje klientów.
<p></p>
<b>Sposób użycia:</b> Użyj tego zestawu danych w połączeniu z innymi dwóch restauracji zestawów danych, do nauczenia i przetestowania polecania systemu. 
<p></p>
<b>Odpowiednie badania:</b> Bache, K. i Lichman, M. (2013). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski School informacji i informatyki.
  </td>
</tr>

<tr>
  <td>Dane funkcji restauracji</td>
  <td>
Zestaw metadanych o restauracjach i ich funkcje, takie jak typ żywności, filozofów stylu i lokalizacji.
<p></p>
<b>Sposób użycia:</b> Użyj tego zestawu danych w połączeniu z innymi dwóch restauracji zestawów danych, do nauczenia i przetestowania polecania systemu. 
<p></p>
<b>Odpowiednie badania:</b> Bache, K. i Lichman, M. (2013). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski School informacji i informatyki.
  </td>
</tr>

<tr>
  <td>Klasyfikacje restauracji</td>
  <td>
Zawiera klasyfikacji podanej przez użytkowników do restauracji w skali od 0 do 2.
<p></p>
<b>Sposób użycia:</b> Użyj tego zestawu danych w połączeniu z innymi dwóch restauracji zestawów danych, do nauczenia i przetestowania polecania systemu. 
<p></p>
<b>Odpowiednie badania:</b> Bache, K. i Lichman, M. (2013). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski School informacji i informatyki.
  </td>
</tr>

<tr>
  <td>Stal Annealing wielu klas dataset</td>
  <td>
Ten zestaw danych zawiera szereg rekordy z stali annealing wersji próbnych. Zawiera fizyczne atrybuty (szerokość, grubość, typu (cewka, Arkusz itp.) powstałe stali typów.
<p></p>
<b>Sposób użycia:</b> Przewidywanie dowolne dwa atrybuty klasy liczbowych; twardości lub sile. Może również analizować korelacji między atrybutami.
<p></p>
Stali standardowy zestaw, postępuj zgodnie z definicją SAE i innych organizacji. Szukasz konkretnego "klasy" (zmienna klasy) i aby zrozumieć wartości wymagane. 
<p></p>
<b>Odpowiednie badania:</b> Szterling, D. & Buntine, W. (NA). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski, szkoły informacji i informatyki
<p></p>
Gatunki stali przydatny przewodnik można znaleźć tutaj: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleskopu danych</td>
  <td>
Rekord cząstki gamma wysokiej energii bursts oraz hałas w tle, zarówno symulowane przy użyciu Monte Carlo procesu.
<p></p>
Celem symulacji było, aby poprawić dokładność teleskopy gamma Cherenkov atmospheric oparte na ziemi. Odbywa się przy użyciu metody statystyczne do rozróżniania żądaną sygnał (promieniowania Cherenkov prysznicem) oraz hałas w tle (hadronic prysznicem inicjowane przez promieniach cosmic w powietrzu górnego).
<p></p>
Dane zostały wstępnie przetworzony do utworzenia klastra wydłużonym z długiego osi jest zorientowany center aparatu. Cechy tego wielokropka (często nazywane parametrami Hillas) znajdują się wśród parametrów obrazu, które mogą służyć do dyskryminacji.
<p></p>
<b>Sposób użycia:</b> Przewidywania, czy obraz przyjęcie reprezentuje hałas w tle lub sygnału.
<p></p>
<b>Uwagi:</b> Dokładność klasyfikacji prostego nie jest zrozumiały dla tych danych od klasyfikacji zdarzeń tła, sygnał jest niższa niż klasyfikowania zdarzenie sygnału jako tło. Porównanie różnych klasyfikatorów wykres ROC powinno być używane. Prawdopodobieństwo, że akceptowania zdarzenia tła sygnału musi być jeden z progów: 0,01, 0,02, 0,05, 0,1 lub 0,2.
<p></p>
Należy również zauważyć, że liczba zdarzeń tła (h, hadronic prysznicem) jest niedoceniane. W pomiarów rzeczywistego klasy h lub szumu reprezentuje większość zdarzeń. 
<p></p>
<b>Odpowiednie badania:</b> Bock, R.K. (1995). UCI usługi Machine Learning repozytorium <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml </a>. Irvine, CA: Uniwersytet kalifornijski-o informacje o szkole </td>
</tr>

<tr>
  <td>Zestaw danych o pogodzie</td>
  <td>
Co godzinę obserwacje lądową pogodowe z agencji NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">scalane dane z 201304 201310</a>).
<p></p>
Dane o pogodzie obejmuje uwagi z stacje pogody port lotniczy, obejmujące okres kwietnia — październik 2013. Przed przekazaniem do usługi Azure Machine Learning Studio, zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Identyfikatory pogodowa byli zamapowani na lotnisku odpowiednich identyfikatorów</li>
  <li>Pogoda stacji nie jest skojarzona z 70 lotniskach najczęściej odwiedzane zostały odfiltrowane.</li>
  <li>Kolumna dat podzielono na osobne kolumny, rok, miesiąc i dzień</li>
  <li>Wybrano następujące kolumny: AirportID, roku, miesiąca, dnia, godziny, strefa czasowa, SkyCondition, widoczność, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, prędkość wiatru, WindDirection, ValueForWindCharacter StationPressure, PressureTendency, PressureChange, SeaLevelPressure, są parametry RecordType, HourlyPrecip, wysokościomierza</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
Dane pochodzą z Wikipedii (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) oparte na artykuły każdej S & P 500 firmy przechowywane w postaci danych XML.
<p></p>
Przed przekazaniem do usługi Azure Machine Learning Studio, zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Wyodrębnij zawartość tekstu dla każdej firmy</li>
  <li>Usuwanie formatowania stron typu wiki</li>
  <li>Usuń znaki inne niż alfanumeryczne</li>
  <li>Konwertuj cały tekst na małe litery.</li>
  <li>Kategorie znanych firmy zostały dodane.</li>
</ul>
<p></p>
Nie można odnaleźć należy pamiętać, że dla niektórych firmom artykułu, więc to liczba rekordów to 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Zestaw danych zawiera dane klientów i dotyczące ich odpowiedzi na bezpośrednie kampanii. Każdy wiersz reprezentuje klienta. Zestaw danych zawiera dziewięć funkcje dotyczące użytkownika, danych demograficznych i przeszłymi zachowanie i trzy etykiety kolumn (odwiedzić, konwersji i wydatków).  Odwiedź stronę to kolumna typu binary, który wskazuje, że klient odwiedzone po kampanii marketingowej. Konwersja wskazuje, że klient zakupu. Wydania jest kwota, która została wydana.  Zestaw danych została udostępniona przez Kevina Hillstrom dla MineThatData E-Mail Analytics i dane wyszukiwania wyzwanie.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funkcje przykłady testów w zestawie danych wiadomości Reuters RCV1 V2. Zestaw danych zawiera artykuły z wiadomościami 781K wraz z ich identyfikatorów (pierwszej kolumny zestawu danych). Każdego artykułu jest stokenizowana stopworded i pęd. Zestaw danych została udostępniona przez David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funkcje przykładów szkoleniowych w zestawie danych wiadomości Reuters RCV1 V2. Zestaw danych zawiera artykuły z wiadomościami 23K wraz z ich identyfikatorów (pierwszej kolumny zestawu danych). Każdego artykułu jest stokenizowana stopworded i pęd. Zestaw danych została udostępniona przez David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Zestaw danych z zawodów KDD Cup 1999 Knowledge Discovery i analizą danych narzędzi konkurencji (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Zestaw danych został pobrany i przechowywane w usłudze Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) i obejmuje zarówno uczenie i testowanie zestawów danych. Zestaw danych szkoleniowych ma około 126 tysięcy wierszy i kolumn 43, w tym etykiety. Trzy kolumny to część informacji etykiety, a kolumny 40, składający się z funkcji liczbowe i ciągu/podzielonych na kategorie, są dostępne dla uczenia modelu. Dane testowe ma około 22,5 K test przykłady z tego samego 43 kolumnami, jak dane szkoleniowe.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Temat przypisań artykuły w zestawie danych wiadomości Reuters RCV1 V2. Artykuł wiadomości można przypisać do różnych tematów. Format każdy wiersz jest "&lt;nazwy tematu&gt; &lt;identyfikator dokumentu&gt; 1". Zestaw danych zawiera 2,6 mln temat przypisania. Zestaw danych została udostępniona przez David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Te dane pochodzą z zawodów KDD Cup 2010 uczniów wyzwanie oceny wydajności (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">oceny wydajności dla uczniów</a>). Dane używane jest zestaw treningowy Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, S. A., Ritter, Gordon, G.J. & Koedinger, K.R. (2010). Algebry I 2008 2009. Rzuć wyzwanie zestawu danych z zawodów KDD Cup 2010 edukacyjnych danych wyszukiwania wyzwanie. Na stronie Znajdź <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Zestaw danych został pobrany i przechowywane w usłudze Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) i zawiera pliki dziennika z uczniów z inteligentnymi systemu. Podane funkcje obejmują identyfikator problemu i jego krótki opis, Legitymację, znacznik czasu i liczbę prób dla uczniów, przed rozwiązywanie problemu w odpowiedni sposób. Oryginalny zestaw danych zawiera rekordy 8,9 M; Ten zestaw danych został próbkowana w dół do pierwszych wierszy 100 tys. Zestaw danych zawiera 23 rozdzielane znakami tabulacji kolumnami różnych typów: liczbowe, podzielone na kategorie i sygnaturę czasową.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Stwórz eksperymenty wraz z przykładami](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
