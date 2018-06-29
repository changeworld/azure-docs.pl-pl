---
title: Użyj przykładowych zestawów danych w usłudze Machine Learning Studio | Dokumentacja firmy Microsoft
description: Opisy zestawów danych użytych w Przykładowe modele uwzględnione w usłudze Machine Learning Studio. Te przykładowych zestawów danych można użyć do eksperymentów.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 03a0b844-e8a7-4896-996f-d3c7a0db7a50
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.openlocfilehash: 87d0eac427f2d3904278dbe8211a3fae3da891ed
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085059"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Korzystanie z przykładowych arkuszy danych w usłudze Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

Po utworzeniu nowego obszaru roboczego w usłudze Azure Machine Learning szereg przykładowych zestawów danych i eksperymenty są domyślnie dołączone. Wiele z tych przykładowych zestawów danych są używane przez Przykładowe modele w [galerii Azure AI](http://gallery.cortanaintelligence.com/). Inne są dołączone jako przykłady różnych typów danych zwykle używanych w uczeniu maszynowym.

Niektóre z tych zestawów danych są dostępne w magazynie obiektów Blob Azure. Dla tych zestawów danych Poniższa tabela zawiera bezpośrednie połączenie. Te zestawy danych można użyć w eksperymentów, za pomocą [i zaimportuj dane] [ import-data] modułu.

Pozostała część tych przykładowych zestawów danych są dostępne w obszarze roboczym w obszarze **zapisane zestawów danych**. To można znaleźć na palecie modułów z lewej strony obszaru roboczego eksperymentu w usłudze Machine Learning Studio.
Można użyć dowolnej z tych zestawów danych w eksperymencie własnych przeciągając je w obszarze roboczym eksperymentu.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th>Nazwa zestawu danych</th>
  <th>Opis zestawu danych</th>
</tr>

<tr>
  <td>Dla dorosłych klasyfikacji binarnej dochodu spisu zestawu danych.</td>
  <td>
Podzbiór bazy danych spisu 1994 r., przy użyciu osoby dorosłe pracy przez wiek 16 z indeksem skorygowaną dochodu > 100.
<p></p>
<b>Sposób użycia:</b> klasyfikowania osoby za pomocą demograficznych do prognozowania, czy osoba uzyskuje ponad 50 K rocznie.
<p></p>
<b>Powiązane Research:</b> Kohavi, R., Becker B., (1996). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Zestaw danych kodów lotnisku</td>
  <td>
Kody lotnisku Stanów Zjednoczonych.
<p></p>
Ten zestaw danych zawiera jeden wiersz dla każdego lotnisku USA, zapewniając lotnisku identyfikator i nazwa wraz z lokalizacji miejscowość i województwo.
  </td>
</tr>

<tr>
  <td>Cen samochodów, data (Raw)</td>
  <td>
Informacji na temat samochodów przez producent i model, w tym cena, funkcje, takie jak liczba cylindrów i MPG, a także oceny ryzyka ubezpieczenia.
<p></p>
Oceny ryzyka jest początkowo skojarzony z cen automatycznie. Następnie dopasowaniu do rzeczywistego zagrożenia w procesie nazywanym do aktuariuszy jako symboling. Wartość + 3 wskazuje ryzykowne jest automatycznie, czy wartość -3 jego jest prawdopodobnie bezpieczne.
<p></p>
<b>Sposób użycia:</b> prognozowania przez funkcje za pomocą funkcji klasyfikacji regresji lub multivariate oceny ryzyka. 
<p></p>
<b>Powiązane Research:</b> Schlimmer, J.C. (1987). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Zestaw danych UCI wynajem roweru</td>
  <td>
Dzierżawa roweru UCI zestawu danych, który bazuje na prawdziwe dane z Bikeshare kapitału firmy, która obsługuje sieci wynajem roweru w stanie Waszyngton kontrolera domeny.
<p></p>
Element dataset zawiera jeden wiersz dla każdej godziny dziennie 2011 i 2012, łącznie 17,379 wierszy. Zakres co godzinę roweru dzierżawy jest z zakresu od 1 do 977.

  </td>
</tr>

<tr>
  <td>Obraz RGB rachunku bramy</td>
  <td>
Plik obrazu publicznie dostępne są konwertowane na dane w formacie CSV.
<p></p>
Konwertowanie obrazu kod znajduje się w <strong>kolor podziału korzystania z klastra K-średnich</strong> strony szczegółów modelu.
  </td>
</tr>

<tr>
  <td>Krwi pobrania danych</td>
  <td>
Podzbiór danych z bazy danych dawcy krwi transfuzji krwi usługi Centrum Hsin Chu Miasto, Tajwan.
<p></p>
Dane dawcy obejmują miesięcy od czasu ostatniego pobrania) i częstotliwość lub liczbę pobrań, czas od ostatniego pobrania i ilość krwi przekazywana.
<p></p>
<b>Sposób użycia:</b> celem jest do prognozowania za pośrednictwem klasyfikacji, czy dawcy przekazywana krwi marca 2007, gdzie 1 oznacza dawcy okresie docelowego, i 0 z systemem innym niż dawcy. 
<p></p>
<b>Powiązane Research:</b> Yeh I.C., (2008). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki
<p></p>
Yeh, -Cheng, Yang króla-Jang i notatki, Ming Tao, "odnajdywaniem wiedzy w tryb RFM modelu przy użyciu sekwencji Bernoulliego,"systemy ekspertów z aplikacjami, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Dane raka piersi</td>
  <td>
Jeden z trzech danych związanych z raka dostarczonych przez Instytut Oncology, który pojawia się często w machine learning materiały. Łączy informacje diagnostyczne z funkcjami z analizy laboratorium hodowlami około 300.
<p></p>
<b>Sposób użycia:</b> klasyfikowania typ raka, oparte na atrybutach 9, niektóre z nich liniowej i niektóre są podzielone na kategorie. 
<p></p>
<b>Powiązane Research:</b> O.L. Wohlberg, W.H., ulicy, W.N. i Mangasarian, (1995). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Funkcje raka piersi <td>
Zestaw danych zawiera informacje regionów podejrzane 102K (kandydatów) rentgenowskie obrazów, każdy opisany przez 117 funkcje. Funkcje są zastrzeżone i ich znaczenie nie została ujawniona przez twórców zestawu danych (opieki zdrowotnej Siemens). 
  </td>
</tr>

<tr>
  <td>Informacje o raka piersi</td>
  <td>
Zestaw danych zawiera dodatkowe informacje dla każdego podejrzanego obszaru rentgenowskie obrazu. Każdy przykład zawiera informacje (na przykład etykieta, pacjenta identyfikator, współrzędnych poprawki względem całego obrazu) o jej numer wiersza w zestawie danych funkcji raka piersi. Każdy pacjenta ma wiele przykładów. Dla pacjentów, którzy mają raka Oto kilka przykładów dodatnią, a inne ujemna. Dla pacjentów, którzy nie mają raka wszystkie przykłady jest ujemna. Element dataset zawiera przykłady 102K. Zestaw danych jest ukierunkowane % 0,6 punktów jest dodatnia, pozostała jest ujemna. Zestaw danych została udostępniona przez opieki zdrowotnej Siemens.
  </td>
</tr>

<tr>
  <td>Etykiety Appetency CRM udostępnionych</td>
  <td>
Etykiety z żądania prognozowania relacji klienta 2009 Pucharze KDD (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Etykiety przenoszenie CRM udostępnionych</td>
  <td>
Etykiety z żądania prognozowania relacji klienta 2009 Pucharze KDD (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM zestawu danych udostępnionych</td>
  <td>
Te dane pochodzą z żądania prognozowania relacji klienta 2009 Pucharze KDD (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Zestaw danych zawiera 50K klientów firmy telekomunikacyjnych francuski pomarańczowy. Każdy klient ma 230 funkcje anonimowe, 190, które są numeryczne i 40 są podzielone na kategorie. Funkcje są bardzo rozrzedzone.
  </td>
</tr>

<tr>
  <td>Etykiety grup CRM udostępnionych</td>
  <td>
Etykiety z żądania prognozowania relacji klienta 2009 Pucharze KDD (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Regresja zapotrzebowania na energię danych</td>
  <td>
Zbiór profilów symulowane energii, na podstawie 12 innego budynku kształtów. Budynki są zróżnicowane przez osiem funkcje. W tym szyb obszaru, szyb obszaru dystrybucji i orientacji.
<p></p>
<b>Sposób użycia:</b> przewidzieć wydajności energii klasyfikacja na podstawie jako jedną z dwóch wartości rzeczywistych odpowiedzi za pomocą klasyfikacji lub regresji. Klasa wielu klasyfikacji jest round zmiennej odpowiedzi do najbliższej liczby całkowitej. 
<p></p>
<b>Powiązane Research:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Transmitowane opóźnienia danych</td>
  <td>
Transmitowane pasażerów na czas dane wydajności z kolekcji danych TranStats USA Stanowych (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">na czas</a>).
<p></p>
Zestaw danych obejmuje okres czasu kwietnia — październik 2013. Przed przekazaniem do usługi Azure Machine Learning Studio, zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Zestaw danych został przefiltrowany dotyczyć tylko 70 zajętej lotniskach kontynentalnym stany USA</li>
  <li>Anulowano lotach zostały oznaczone jako opóźniony o więcej niż 15 minut.</li>
  <li>Kierunku lotach zostały odfiltrowane.</li>
  <li>Wybrano następujące kolumny: rok, miesiąc, DayofMonth, DayOfWeek, operatora, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, anulowane</li>
</ul>
</td>
</tr>

<tr>
  <td>Na czas osiągów (Raw)</td>
  <td>
Rejestruje samolotowy transmitowane odbiorów i odejście na terenie Stanów Zjednoczonych z października 2011.
<p></p>
<b>Sposób użycia:</b> prognozowania transmitowane opóźnienia. 
<p></p>
<b>Powiązane Research:</b> z działu transportu USA <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Dane generowane lasu</td>
  <td>
Zawiera dane pogodzie, takich jak indeksy temperatury i wilgotności i szybkość knie. Dane są pobierane z obszaru północno-wschodniej Portugalii, w połączeniu z rekordów pożarów lasów.
<p></p>
<b>Sposób użycia:</b> jest to zadanie regresji trudne, której celem jest przewidzieć obszaru palony pożarów lasów. 
<p></p>
<b>Powiązane Research:</b> Cortez, P. & Morais, A. (2008). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki
<p></p>
[Cortez i Morais, 2007] Str. Cortez i A. Morais. Dane wyszukiwania podejście do prognozowania pożarów lasów przy użyciu danych meteorologicznych. W J. Neves, M. F. Santos i J. Machado Eds., nowe tendencji sztucznego analizy, postępowania 13 2007 EPIA — portugalski konferencji sztucznego analizy, grudnia, Guimarães, Portugalia str. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Dostępne pod adresem: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Niemiecki karty kredytowej UCI zestawu danych</td>
  <td>
Zestaw danych Statlog UCI (karta kredytowa niemiecki) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + niemiecki + środki + danych</a>), przy użyciu pliku german.data.
<p></p>
Zestaw danych klasyfikuje osób opisanego przez zestaw atrybutów jako niski lub wysokiego ryzyka. Każdy przykład reprezentuje osoby. Istnieją funkcje 20, zarówno numeryczne i podzielone na kategorie i etykietę binarne (wartość ryzyko kredytowe). Środki wysokiego ryzyka zapisów etykiety = 2, środki niskiego ryzyka zapisów etykiety = 1. Koszt misclassifying jest przykład niskiego ryzyka tak dużych jest 1, 5 jest misclassifying przykład wysokiego ryzyka, jako niskie koszty.
  </td>
</tr>

<tr>
  <td>ZAUFANI filmu tytułów</td>
  <td>
Zestaw danych zawiera informacje na temat filmów, które zostały sklasyfikowane w serwisie Twitter tweetów: ZAUFANI filmu identyfikator, nazwę filmu genre i roku produkcji. Brak 17K filmów w zestawie danych. Zestaw danych została wprowadzona w dokumencie "S. Dooms T. De Pessemier i L. Martens. MovieTweetings: filmu klasyfikacji zestawu danych zbieranych z serwisem Twitter. Workshop na Crowdsourcing i ludzkich obliczanie systemów polecania CrowdRec na RecSys 2013".
  </td>
</tr>

<tr>
  <td>Iris dwie klasy danych</td>
  <td>
Jest to prawdopodobnie znaną bazy danych ma zostać odnaleziona w materiały rozpoznawania wzorca. Zestaw danych jest stosunkowo mały, zawierający 50 przykłady pomiarów Motyw Płatek z trzech iris odmian.
<p></p>
<b>Sposób użycia:</b> prognozowania typu iris z pomiarów.  
<p></p>
<b>Powiązane Research:</b> Fishera, R.A. (1988). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Tweetów film</td>
  <td>
Zestaw danych jest rozszerzona wersja Tweetings filmu zestawu danych. Element dataset zawiera 170K Klasyfikacje filmów wyodrębniony z dobrze tweetów w serwisie Twitter. Każde wystąpienie reprezentuje tweet i jest krotka: identyfikator użytkownika, ZAUFANI filmu ID, klasyfikacji, sygnatura czasowa, numer Ulubione tweet i liczba retweets to tweet. Zestaw danych została udostępniona A. powiedział, S. Dooms, B. Loni i D. Tikk dla polecania systemów wyzwanie 2014.
  </td>
</tr>

<tr>
  <td>Dane MPG dotyczące różnych samochodów</td>
  <td>
Ten zestaw danych jest nieco zmodyfikowaną wersję udostępniane przez bibliotekę StatLib Carnegie Mellon University zestawu danych. Zestaw danych został użyty w 1983 American statystyczne skojarzenia specyfikacji.
<p></p>
Zużycie paliwa dla różnych samochodów w milach na galon wykazy danych. Zawiera również informacje, takie jak liczba cylindrów, aparat przemieszczenie moc, całkowitej wagi i przyspieszenia.
<p></p>
<b>Sposób użycia:</b> prognozowania zużycie paliwa na podstawie trzech wielowartościowego atrybutów dyskretnych i pięciu atrybutów ciągłych. 
<p></p>
<b>Powiązane Research:</b> StatLib Carnegie Mellon University (1993). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Klasyfikacji binarnej cukrzyca Indian Pima zestawu danych</td>
  <td>
Podzbiór danych z National Institute cukrzyca i przewodu i choroby nerek bazy danych. Zestaw danych został przefiltrowany skoncentrować się na żeńskiego pacjentów dziedzictwa indyjskiego Pima. Dane obejmują medyczne dane, takie jak glukozy i inulinowego poziomy, a także lifestyle czynników.
<p></p>
<b>Sposób użycia:</b> prognozowania, czy podmiot ma cukrzyca (klasyfikacji binarnej). 
<p></p>
<b>Powiązane Research:</b> Sigillito, V. (1990). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml"</a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki </td>
</tr>

<tr>
  <td>Dane klienta restauracji</td>
  <td>
Zestaw metadane dotyczące klientów, w tym demograficznymi i preferencje.
<p></p>
<b>Sposób użycia:</b> Użyj tego zestawu danych w połączeniu z innymi dwóch restauracji zestawy danych, do nauczenia i przetestowania systemu polecania. 
<p></p>
<b>Powiązane Research:</b> Bache, K. i Lichman, M. (2013). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki.
  </td>
</tr>

<tr>
  <td>Dane funkcji restauracji</td>
  <td>
Zestaw metadane dotyczące restauracji i ich funkcje, takie jak typ żywności, lokali stylu i lokalizacji.
<p></p>
<b>Sposób użycia:</b> Użyj tego zestawu danych w połączeniu z innymi dwóch restauracji zestawy danych, do nauczenia i przetestowania systemu polecania. 
<p></p>
<b>Powiązane Research:</b> Bache, K. i Lichman, M. (2013). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki.
  </td>
</tr>

<tr>
  <td>Klasyfikacje restauracji</td>
  <td>
Zawiera klasyfikacji podanej przez użytkowników restauracji w skali od 0 do 2.
<p></p>
<b>Sposób użycia:</b> Użyj tego zestawu danych w połączeniu z innymi dwóch restauracji zestawy danych, do nauczenia i przetestowania systemu polecania. 
<p></p>
<b>Powiązane Research:</b> Bache, K. i Lichman, M. (2013). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki.
  </td>
</tr>

<tr>
  <td>Stali Annealing wielu klasy dataset</td>
  <td>
Ten zestaw danych zawiera szereg rekordy z stali termiczne odprężanie prób. Zawiera fizyczne atrybuty (szerokość, grubość, typ (cewka, Arkusz itp.) powstałe w ten sposób stali typów.
<p></p>
<b>Sposób użycia:</b> dwa atrybuty klasy liczbowych; twardości lub siły przewidzieć. Może również analizować korelacji między atrybutami.
<p></p>
Stali wykonaj standardowego zestawu, zdefiniowany przez SAE i innych organizacji. Szukasz określonej "klasy" (zmienna klasy) i chcesz poznać wartości potrzebnych. 
<p></p>
<b>Powiązane Research:</b> szterlinga, D. & Buntine, W. (NA). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University Kalifornijskiej, szkoły informacji i informatyki
<p></p>
Przydatny przewodnik dotyczący stali klas można znaleźć tutaj: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleskopu danych</td>
  <td>
Rekord cząstki gamma wysokiej energii ulega zapaleniu wraz z szumu tła, zarówno symulowane za pomocą Monte Carlo procesu.
<p></p>
Celem symulacji było zwiększyć dokładność teleskopy gamma Cherenkov atmosferycznych na podstawie podstaw. Jest to zrobić za pomocą metod statystycznych do rozróżniania żądany sygnał (promieniowania Cherenkov natryski) oraz szumu tła (hadronic natryski inicjowane przez promienie cosmic w górnym atmosferze).
<p></p>
Dane zostały wstępnie przetworzonych do utworzenia klastra wydłużonym z długiego osi jest zorientowany Centrum aparatu. Właściwości tego elipsy (często nazywane parametry Hillas) są parametry obrazu, które mogą być używane dla dyskryminacji.
<p></p>
<b>Sposób użycia:</b> prognozowania, czy obraz przyjęcie reprezentuje szumu sygnał lub w tle.
<p></p>
<b>Uwagi:</b> proste klasyfikacji dokładność nie jest zrozumiały dla tych danych, ponieważ klasyfikacji zdarzeń tła jako sygnału jest większa niż klasyfikowania zdarzenie sygnału jako tło. Porównanie różnych klasyfikatory wykres ROC powinien być używany. Prawdopodobieństwo akceptowania zdarzeń tła jako sygnału musi być poniżej jedną z następujących progów: 0,01, 0,02, 0,05, 0,1 lub 0,2.
<p></p>
Należy również zauważyć, że liczba zdarzeń tła (h, natryski hadronic) jest zgłosił. W rzeczywistym pomiarów h lub szumu klasy reprezentuje większość zdarzeń. 
<p></p>
<b>Powiązane Research:</b> Bock, R.K. (1995). UCI Machine Learning repozytorium <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, urząd certyfikacji: University z Kalifornijskiej, szkoły informacji </td>
</tr>

<tr>
  <td>Pogody zestawu danych</td>
  <td>
Co godzinę uwag lądowej pogody NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">scalane dane z 201304 201310</a>).
<p></p>
Dane pogody obejmuje uwagi z lotniska pogody stacje, obejmujący okres czas, kwietnia — październik 2013. Przed przekazaniem do usługi Azure Machine Learning Studio, zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Identyfikatory pogody stacji nie zostały zamapowane na lotnisku odpowiednich identyfikatorów</li>
  <li>Stacje pogody nie są skojarzone z 70 lotniskach zajętej zostały odfiltrowane.</li>
  <li>Data kolumny podzielono na osobne kolumny rok, miesiąc i dzień</li>
  <li>Wybrano następujące kolumny: AirportID, rok, miesiąc, dzień, czas, strefy czasowej, SkyCondition, widoczność, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, Prędkość wiatru, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, wysokościomierza</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
Danych jest określana na podstawie Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) oparte na artykuły każdej S & P 500 firmy przechowywanych danych XML.
<p></p>
Przed przekazaniem do usługi Azure Machine Learning Studio, zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Wyodrębnienie zawartości tekstowej dla każdej firmy</li>
  <li>Usunięcie formatowania stron typu wiki</li>
  <li>Usuń znaki inne niż alfanumeryczne</li>
  <li>Konwertuj cały tekst na małe litery.</li>
  <li>Kategorie znane firmy zostały dodane</li>
</ul>
<p></p>
Nie można odnaleźć należy pamiętać, że w przypadku niektórych firm artykułu, więc to liczba rekordów jest mniej niż 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Zestaw danych zawiera dane klientów i wskazówek dotyczących ich odpowiedzi na bezpośrednie kampanii. Każdy wiersz reprezentuje klienta. Zestaw danych zawiera funkcje dziewięć o demograficznymi użytkownika oraz zachowanie i trzech etykiet kolumn (odwiedź konwersji oraz wydatków).  Odwiedź stronę to kolumna typu binary, który wskazuje, że klient odwiedzi po kampanię marketingową. Konwersja oznacza, że klient zakupiono produkt. Spędzają jest przeznaczony wielkość.  Zestaw danych została udostępniona przez Hillstrom Kevina dla MineThatData E-Mail analizy danych wyszukiwania żądania i.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funkcje przykłady testów w zestawie danych wiadomości Reuters RCV1 V2. Element dataset zawiera artykuły 781K wraz z ich identyfikatorów (pierwszej kolumny zestawu danych). Każdego artykułu jest stokenizowana stopworded i zakończonej. Zestaw danych została udostępniona przez Dominika. D. Nowak.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funkcje przykładów szkoleniowych w zestawie danych wiadomości Reuters RCV1 V2. Element dataset zawiera artykuły 23K wraz z ich identyfikatorów (pierwszej kolumny zestawu danych). Każdego artykułu jest stokenizowana stopworded i zakończonej. Zestaw danych została udostępniona przez Dominika. D. Nowak.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Zestaw danych z KDD Pucharze 1999 wiedzy odnajdywania i wyszukiwania danych narzędzi konkurencji (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Zestaw danych został pobrany i przechowywane w magazynie obiektów Blob platformy Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) i obejmuje zarówno uczenie i testowanie zestawów danych. Zestaw danych szkoleniowych ma około 126K wierszy i kolumn 43, w tym etykiety. Trzy kolumny są częścią informacji etykiety, a kolumny 40, składające się z funkcje numeryczne i ciągu/podzielone na kategorie, są dostępne do uczenia modelu. Dane testowe ma około 22,5 K test przykłady można podać te same kolumny 43 jak danych szkoleniowych.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Temat przypisania dla nowych artykułów w zestawie danych wiadomości Reuters RCV1 V2. Artykuł wiadomości można przypisać do różnych tematów. Format każdy wiersz jest "&lt;nazwa tematu&gt; &lt;identyfikator dokumentu&gt; 1". Zestaw danych zawiera przypisania tematu 2.6M. Zestaw danych została udostępniona przez Dominika. D. Nowak.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Te dane pochodzą z żądania oceny wydajności uczniów 2010 Pucharze KDD (<a href="http://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">oceny wydajności uczniowie</a>). Danych używany jest zestaw szkoleniowy Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, S. A., Ritter, Gordon, G.J. & Koedinger, K.R. (2010). Algebraiczną I 2008 2009. Żądanie zestawu danych z KDD Pucharze 2010 edukacyjnych danych wyszukiwania żądania. Znajdź go w <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Zestaw danych został pobrany i przechowywane w magazynie obiektów Blob platformy Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) i zawiera pliki dziennika z studenta Korepetycje systemu. Podany cechy identyfikator problemu i jego krótki opis, ID studenta znacznik czasu i liczby prób uczniów przed rozwiązywanie problemu w sposób prawo. Oryginalny zestaw danych zawiera rekordy 8,9 M; Ten zestaw danych został próbkowany w dół do pierwszych wierszy 100 KB. Element dataset zawiera 23 tabulatorem kolumny z różnych typów: liczbowe podzielone na kategorie i sygnatura czasowa.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
