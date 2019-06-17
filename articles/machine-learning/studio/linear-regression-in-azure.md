---
title: Analiza migracji z programu Excel
titleSuffix: Azure Machine Learning Studio
description: Porównanie modeli regresji liniowej w programach Excel i usługi Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: f6b2f4ef9a4f3f1615081a422a16ea9f2e156571
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861118"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Migrowanie analytics z programu Excel do usługi Azure Machine Learning Studio

> *Kate Baroni* i *Ben Boatman* są architekci rozwiązań w firmy Microsoft Data Insights centrum doskonałości przedsiębiorstwa. W tym artykule opisano czynności migracja istniejącego zestawu analiz regresji do rozwiązania oparte na chmurze za pomocą usługi Azure Machine Learning Studio.

## <a name="goal"></a>Cel

Nasz projekt do dwóch celów pamiętać: 

1. Korzystaj z analizy predykcyjnej, aby poprawić dokładność naszej organizacji miesięczne projekcje przychodu 
2. Użyj usługi Azure Machine Learning Studio, aby potwierdzić, optymalizowanie, zwiększenia szybkości pracy i skalowanie naszych wyników. 

Podobnie jak wiele firm naszej organizacji przechodzi przez miesięczny przychód, proces prognozowania. Nasz mały zespół analityków biznesowych został nadzorowania przy użyciu usługi Azure Machine Learning Studio obsługuje proces i zwiększenia dokładności prognozy. Zespół poświęcony kilka miesięcy, zbieranie danych z wielu źródeł i z atrybutów danych za pośrednictwem analizy statystycznej Identyfikowanie kluczowych atrybutów, które dotyczą usług prognozowania sprzedaży. Następnym krokiem było zacząć tworzenie prototypów modele regresji statystycznej na danych w programie Excel. W ciągu kilku tygodni mieliśmy Excel model regresji, który został liderami bieżącej wartości pola i Finanse procesy prognozowania. To stały się wyniki prognozowania linii bazowej. 

Następnie skorzystaliśmy następnym krokiem do naszej analizy predykcyjnej przeniesieniem Studio, aby dowiedzieć się, jak Studio można poprawić predykcyjne wydajności.

## <a name="achieving-predictive-performance-parity"></a>Obsługiwanie parzystości predykcyjne wydajności
Nasz priorytet było osiągnąć parzystość modele regresji Studio i programu Excel. Celów szkoleniowych i testów danych, biorąc pod uwagę te same dane, a ten sam podział, chcemy osiągnąć parzystości predykcyjne wydajności między programami Excel i Studio. Początkowo firma Microsoft nie powiodło się. Model programu Excel pokonał usługę modelu usługi Studio. Niepowodzenie było ze względu na brak wiedzę na temat ustawienia bazowego narzędzia w programie Studio. Po zakończeniu synchronizacji z zespołem produktu Studio firma Microsoft zdobyte lepszego zrozumienia base ustawienia wymagane dla naszych zestawów danych i osiągnąć równoważności między dwoma modelami. 

### <a name="create-regression-model-in-excel"></a>Utworzyć model regresji, w programie Excel
Nasze regresji Excel używane uczenia modelu regresji liniowej standardowego w ToolPak analizy w programie Excel. 

Firma Microsoft obliczona *Mean % bezwzględny błąd* i używać go jako miary wydajności dla modelu. Zajęło 3 miesiące na model pracy, za pomocą programu Excel. Możemy przełączyć ilości nauki do eksperymentu usługi Studio, który ostatecznie korzystne opis wymagań.

### <a name="create-comparable-experiment-in-studio"></a>Tworzenie porównywalne eksperymentów w Studio
Firma Microsoft i te kroki, aby utworzyć naszych eksperymentów w Studio: 

1. Przekazano zestaw danych jako plik csv w programie Studio (bardzo mały plik)
2. Utworzony nowy eksperyment i używany [Select Columns in Dataset] [ select-columns] modułu, aby wybrać te same funkcje danych używane w programie Excel 
3. Używane [podziału danych] [ split] modułu (przy użyciu *wyrażenia względne* tryb) podzielić dane na tych samych zestawów danych szkoleniowych, podobnie jak w programie Excel 
4. Badawcze, mające z [regresji liniowej] [ linear-regression] modułu (tylko opcje domyślne), udokumentowane i porównać wyniki, aby nasz model regresji programu Excel

### <a name="review-initial-results"></a>Przejrzyj wyniki początkowego
Na początku modelu programu Excel pokonał wyraźnie usługę modelu Studio: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Wydajność | | |
| <ul style="list-style-type: none;"><li>Skorygowane R-kwadrat</li></ul> |0.96 |ND |
| <ul style="list-style-type: none;"><li>Współczynnik <br />Oznaczanie</li></ul> |ND |0.78<br />(dokładność niski) |
| Średni bezwzględny błąd |$9. 5M |$ 19.4 M |
| Średni bezwzględny błąd (%) |6.03% |12.2% |

Uruchomiliśmy nasz proces i wyniki przez deweloperów i analityków danych w zespole usługi Machine Learning, one szybko udostępniane niektóre przydatne porady. 

* Kiedy używasz [regresji liniowej] [ linear-regression] modułu w programie Studio znajdują się dwie metody:
  * Spadku gradientu online: Mogą być bardziej odpowiednie dla problemów na dużą skalę
  * Zwykłe najmniejszych kwadratów: Jest to metoda, których większość osób postrzega podczas ich usłyszeć regresji liniowej. W przypadku małych zestawów danych zwykłych najmniejszych kwadratów może być bardziej optymalnym wyborem.
* Należy wziąć pod uwagę, dostosowywanie parametr L2 uregulowania wagę do zwiększenia wydajności. Ustawiana jest na 0,001 domyślnie, ale dla naszej małej zestawu danych możemy ustawić ją na 0,005 w celu zwiększenia wydajności. 

### <a name="mystery-solved"></a>Taki rozwiązanie!
Stosowania zaleceń, możemy osiągnąć ten sam punkt odniesienia wydajności w programie Studio jako przy użyciu programu Excel: 

|  | Excel | Studio (Initial) | Studio z najmniejszych kwadratów |
| --- |:---:|:---:|:---:|
| Wartość etykietą |Rzeczywiste (liczbowe od) |Ten sam |Ten sam |
| Uczeń |Excel -> Data Analysis -> regresji |Regresji liniowej. |Regresja liniowa |
| Opcje learner |ND |Ustawienia domyślne |zwykłe najmniejszych kwadratów<br />L2 = 0,005 |
| Zestaw danych |26 ostatnich wierszy, funkcje 3, 1 etykiety. Wszystkie numeryczne. |Ten sam |Ten sam |
| Split: Szkolenie |Excel uczony w wierszach najpierw 18, przetestowana na ostatnie 8 wierszy. |Ten sam |Ten sam |
| Split: Testowanie |Formuła regresji, które dotyczą ostatnie 8 wierszy w programie Excel |Ten sam |Ten sam |
| **Wydajność** | | | |
| Skorygowane R-kwadrat |0.96 |ND | |
| Determinacji |ND |0.78 |0.952049 |
| Średni bezwzględny błąd |$9. 5M |$ 19.4 M |$9. 5M |
| Średni bezwzględny błąd (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Ponadto współczynniki programu Excel względem również obciążenia funkcji, w Azure uczonego modelu:

|  | Współczynniki programu Excel | Azure Feature Weights |
| --- |:---:|:---:|
| ODCIĘTA/odchylenie |19470209.88 |19328500 |
| Funkcja A |0.832653063 |0.834156 |
| Funkcja B |11071967.08 |11007300 |
| Funkcja języka C |25383318.09 |25140800 |

## <a name="next-steps"></a>Następne kroki
Chcemy korzystać z usługi sieci web Machine Learning w programie Excel. Nasze analitykom biznesowym zależą od programu Excel i potrzebowaliśmy sposobu wywoływania usługi sieci web Machine Learning z wierszem danych programu Excel i zwraca wartością prognozowaną do programu Excel. 

Chcieliśmy również w celu zoptymalizowania nasz model przy użyciu opcji i algorytmy dostępne w programie Studio.

### <a name="integration-with-excel"></a>Integracja z programem Excel
Nasze rozwiązanie polegało na operacjonalizować model regresji naszej usługi Machine Learning, tworząc usługi sieci web za pomocą nauczonego modelu. W ciągu kilku minut Usługa sieci web została utworzona, a firma Microsoft może wywołać bezpośrednio z programu Excel w celu zwrócenia wartości przewidywane przychodów. 

*Pulpicie nawigacyjnym usługi sieci Web* znajdują się w skoroszycie programu Excel do pobrania. Skoroszyt zawiera wstępnie sformatowane informacje interfejsu API i schematu usługi sieci web osadzone. Po kliknięciu *pobrać skoroszyt programu Excel*, zostanie otwarty skoroszyt i zapisz go na komputerze lokalnym. 

![Pobieranie skoroszytu programu Excel na pulpicie nawigacyjnym usługi sieci Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Z otwartego skoroszytu skopiuj wstępnie zdefiniowane parametry do niebieski sekcji parametrów, jak pokazano poniżej. Wprowadzone parametry wywołuje programu Excel do usługi sieci web Machine Learning i przewidywane ocenami etykiety będą wyświetlane w sekcji zielony przewidywanych wartości. Skoroszyt, będą w dalszym ciągu Tworzenie prognoz dotyczących parametrów na podstawie uczonego modelu dla wszystkich elementów wiersza wprowadzone w obszarze parametrów. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [korzystanie z usługi sieci Web Azure Machine Learning z poziomu programu Excel](consuming-from-excel.md). 

![Łączenie z usługą sieci web wdrożonej skoroszyt programu Excel szablonu](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optymalizacja i dalszych eksperymentów
Teraz, mieliśmy punktu odniesienia z nasz model programu Excel, przenieśliśmy wyprzedzeniem zoptymalizować nasz Model uczenia maszynowego liniowej regresji. Użyliśmy moduł [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] do poprawy naszych wyboru danych początkowych elementów i pomogło nam to osiągnąć lepszą wydajność, 4.6% oznacza bezwzględny błąd. W przyszłych projektach użyjemy tej funkcji, który może zapisać nam tygodnie w iteracji atrybutów danych, aby znaleźć właściwy zestaw funkcji służących do modelowania. 

Następnie planujemy uwzględnienie dodatkowych algorytmów, takich jak [Bayesowskie] [ bayesian-linear-regression] lub [wzmocnione drzewa decyzyjne] [ boosted-decision-tree-regression] w naszym doświadczeniu do porównania wydajność. 

Jeśli chcesz poeksperymentować z regresji, dobry zestaw danych, aby spróbować jest regresji wydajności energetycznej przykładowego zestawu danych, która ma wiele atrybutów liczbowych. Zestaw danych jest dostarczany jako część przykładowych zestawów danych w programie Studio. Różnorodne szkolenia modułów służy do prognozowania ogrzewania obciążenia lub obciążenia chłodzenie. Wykres poniżej znajduje się porównanie wydajności różnych regresji uczy się przed energię Prognozowanie zestawu danych dla zmiennej docelowej Cooling obciążenia: 

| Modelowanie | Średni bezwzględny błąd | Średnia głównego kwadrat błąd | Względny błąd absolutny | Względna kwadrat błąd | Determinacji |
| --- | --- | --- | --- | --- | --- |
| Wzmocnione drzewo decyzyjnego |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Regresja liniowa (spadku gradientu) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regresja sieci neuronowej |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Regresja liniowa (zwykłe najmniejszych kwadratów) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Najważniejsze wnioski
Dowiedzieliśmy się znacznie przez uruchamianie regresji programu Excel i eksperymentów w Studio równolegle. Tworzenie modelu odniesienia w programie Excel i porównanie z modeli za pomocą usługi Machine Learning [regresji liniowej] [ linear-regression] pomogło nam informacje Studio i wykryliśmy możliwości poprawy wybór danych i modelu wydajność. 

Dowiedzieliśmy się także, zaleca się używać [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] aby przyspieszyć prognozowania przyszłych projektów. Stosując wybór funkcji do swoich danych, możesz utworzyć model ulepszone w Studio o lepszej wydajności ogólnej. 

Możliwość przesyłania predykcyjne analizy prognozowania z programu Studio do programu Excel systemically umożliwia znaczny wzrost w pomyślnie Udostępnianie wyników firm szerokiego grona użytkowników użytkownika. 

## <a name="resources"></a>Zasoby
Poniżej przedstawiono niektóre zasoby pomagające w pracy z regresji: 

* Regresja w programie Excel. Jeśli nigdy nie sprawdzone regresji w programie Excel, ten samouczek ułatwia: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Prognozowanie vs regresji. Tyler Chessman napisał artykuł z bloga wyjaśniające, jak czas serii prognozowania w programie Excel zawiera opis dobre dla początkujących regresji liniowej. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Zwykłe najmniej Squares regresji liniowej: Wady, problemów i pułapek. Wprowadzenie i dyskusji regresji: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

