---
title: Używanie regresji liniowej w usłudze Machine Learning | Dokumentacja firmy Microsoft
description: Porównanie modeli regresji liniowej w programach Excel i usługi Azure Machine Learning Studio
metakeywords: ''
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 417ae6ab-de4f-4bdd-957a-d96133234656
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: b96143ff587cbf5828f4a1d2d1dc11f5bcc33412
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261398"
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Używanie regresji liniowej w usłudze Azure Machine Learning
> *Kate Baroni* i *Ben Boatman* są architekci rozwiązań w firmy Microsoft Data Insights centrum doskonałości przedsiębiorstwa. W tym artykule opisano czynności migracja istniejącego zestawu analiz regresji do rozwiązania oparte na chmurze za pomocą usługi Azure Machine Learning. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Cel
Nasz projekt do dwóch celów pamiętać: 

1. Korzystaj z analizy predykcyjnej, aby poprawić dokładność naszej organizacji miesięczne projekcje przychodu 
2. Użyj usługi Azure Machine Learning, aby potwierdzić, optymalizowanie, zwiększenia szybkości pracy i skalowanie naszych wyników. 

Podobnie jak wiele firm naszej organizacji przechodzi przez miesięczny przychód, proces prognozowania. Nasz mały zespół analityków biznesowych został nadzorowania przy użyciu usługi Azure Machine Learning do obsługi procesu i zwiększenia dokładności prognozy. Zespół poświęcony kilka miesięcy, zbieranie danych z wielu źródeł i z atrybutów danych za pośrednictwem analizy statystycznej Identyfikowanie kluczowych atrybutów, które dotyczą usług prognozowania sprzedaży. Następnym krokiem było zacząć tworzenie prototypów modele regresji statystycznej na danych w programie Excel. W ciągu kilku tygodni mieliśmy Excel model regresji, który został liderami bieżącej wartości pola i Finanse procesy prognozowania. To stały się wyniki prognozowania linii bazowej. 

Następnie skorzystaliśmy następnego kroku przenoszenia naszej analizy predykcyjnej za pośrednictwem usługi Azure Machine Learning, aby dowiedzieć się, jak Machine Learning można poprawić predykcyjne wydajności.

## <a name="achieving-predictive-performance-parity"></a>Obsługiwanie parzystości predykcyjne wydajności
Nasz priorytet było osiągnąć parzystość modele regresji uczenia maszynowego i Excel. Celów szkoleniowych i testów danych, biorąc pod uwagę te same dane, a ten sam podział, chcemy osiągnąć parzystości predykcyjne wydajności między programami Excel i usługi Machine Learning. Początkowo firma Microsoft nie powiodło się. Model programu Excel pokonał usługę model usługi Machine Learning. Niepowodzenie było ze względu na brak wiedzę na temat ustawienia bazowego narzędzia w usłudze Machine Learning. Po zakończeniu synchronizacji z zespołem produktu usługi Machine Learning firma Microsoft zdobyte lepszego zrozumienia base ustawienia wymagane dla naszych zestawów danych i osiągnąć równoważności między dwoma modelami. 

### <a name="create-regression-model-in-excel"></a>Utworzyć model regresji, w programie Excel
Nasze regresji Excel używane uczenia modelu regresji liniowej standardowego w ToolPak analizy w programie Excel. 

Firma Microsoft obliczona *Mean % bezwzględny błąd* i używać go jako miary wydajności dla modelu. Zajęło 3 miesiące na model pracy, za pomocą programu Excel. Możemy przełączyć ilości nauki do eksperymentu usługi Machine Learning Studio, który ostatecznie korzystne opis wymagań.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Tworzenie porównywalne eksperymentu w usłudze Azure Machine Learning
Firma Microsoft przestrzegane następujące kroki, aby utworzyć nasz eksperyment w usłudze Machine Learning Studio: 

1. Przekazano zestaw danych jako plik csv w usłudze Machine Learning Studio (bardzo mały plik)
2. Utworzony nowy eksperyment i używany [Select Columns in Dataset] [ select-columns] modułu, aby wybrać te same funkcje danych używane w programie Excel 
3. Używane [podziału danych] [ split] modułu (przy użyciu *wyrażenia względne* tryb) podzielić dane na tych samych zestawów danych szkoleniowych, podobnie jak w programie Excel 
4. Badawcze, mające z [regresji liniowej] [ linear-regression] modułu (tylko opcje domyślne), udokumentowane i porównać wyniki, aby nasz model regresji programu Excel

### <a name="review-initial-results"></a>Przejrzyj wyniki początkowego
Na początku modelu programu Excel pokonał wyraźnie usługę modelu usługi Machine Learning Studio: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Wydajność | | |
| <ul style="list-style-type: none;"><li>Skorygowane R-kwadrat</li></ul> |0.96 |ND |
| <ul style="list-style-type: none;"><li>Współczynnik <br />Oznaczanie</li></ul> |ND |0.78<br />(dokładność niski) |
| Średni bezwzględny błąd |$9. 5M |$ 19.4 M |
| Średni bezwzględny błąd (%) |6.03% |12.2% |

Uruchomiliśmy nasz proces i wyniki przez deweloperów i analityków danych w zespole usługi Machine Learning, one szybko udostępniane niektóre przydatne porady. 

* Kiedy używasz [regresji liniowej] [ linear-regression] modułu w usłudze Machine Learning Studio, znajdują się dwie metody:
  * Online spadku gradientu: Może być bardziej odpowiednie dla problemów na dużą skalę
  * Zwykłe najmniejszych kwadratów: Jest to metody, których większość osób postrzega podczas ich usłyszeć regresji liniowej. W przypadku małych zestawów danych zwykłych najmniejszych kwadratów może być bardziej optymalnym wyborem.
* Należy wziąć pod uwagę, dostosowywanie parametr L2 uregulowania wagę do zwiększenia wydajności. Ustawiana jest na 0,001 domyślnie, ale dla naszej małej zestawu danych możemy ustawić ją na 0,005 w celu zwiększenia wydajności. 

### <a name="mystery-solved"></a>Taki rozwiązanie!
Stosowania zaleceń, możemy osiągnąć ten sam punkt odniesienia wydajności w usłudze Machine Learning Studio jako przy użyciu programu Excel: 

|  | Excel | Studio (Initial) | Studio z najmniejszych kwadratów |
| --- |:---:|:---:|:---:|
| Wartość etykietą |Rzeczywiste (liczbowe od) |Ten sam |Ten sam |
| Uczeń |Excel -> Data Analysis -> regresji |Regresji liniowej. |Regresja liniowa |
| Opcje learner |ND |Domyślne |zwykłe najmniejszych kwadratów<br />L2 = 0,005 |
| Zestaw danych |26 ostatnich wierszy, funkcje 3, 1 etykiety. Wszystkie numeryczne. |Ten sam |Ten sam |
| Podziel: szkolenie |Excel uczony w wierszach najpierw 18, przetestowana na ostatnie 8 wierszy. |Ten sam |Ten sam |
| Podziel: Test |Formuła regresji, które dotyczą ostatnie 8 wierszy w programie Excel |Ten sam |Ten sam |
| **Wydajność** | | | |
| Skorygowane R-kwadrat |0.96 |ND | |
| Determinacji |ND |0.78 |0.952049 |
| Średni bezwzględny błąd |$9. 5M |$ 19.4 M |$9. 5M |
| Średni bezwzględny błąd (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Ponadto współczynniki programu Excel względem również obciążenia funkcji, w Azure uczonego modelu:

|  | Współczynniki programu Excel | Wagi funkcji platformy Azure |
| --- |:---:|:---:|
| ODCIĘTA/odchylenie |19470209.88 |19328500 |
| Funkcja A |0.832653063 |0.834156 |
| Funkcja B |11071967.08 |11007300 |
| Funkcja języka C |25383318.09 |25140800 |

## <a name="next-steps"></a>Następne kroki
Chcemy korzystać z usługi sieci web Machine Learning w programie Excel. Nasze analitykom biznesowym zależą od programu Excel i potrzebowaliśmy sposobu wywoływania usługi sieci web Machine Learning z wierszem danych programu Excel i zwraca wartością prognozowaną do programu Excel. 

Chcieliśmy również w celu zoptymalizowania nasz model przy użyciu opcji i algorytmów w usłudze Machine Learning Studio.

### <a name="integration-with-excel"></a>Integracja z programem Excel
Nasze rozwiązanie polegało na operacjonalizować model regresji naszej usługi Machine Learning, tworząc usługi sieci web za pomocą nauczonego modelu. W ciągu kilku minut Usługa sieci web została utworzona, a firma Microsoft może wywołać bezpośrednio z programu Excel w celu zwrócenia wartości przewidywane przychodów. 

*Pulpicie nawigacyjnym usługi sieci Web* znajdują się w skoroszycie programu Excel do pobrania. Skoroszyt zawiera wstępnie sformatowane informacje interfejsu API i schematu usługi sieci web osadzone. Po kliknięciu *pobrać skoroszyt programu Excel*, zostanie otwarty skoroszyt i zapisz go na komputerze lokalnym. 

![][1]

Z otwartego skoroszytu skopiuj wstępnie zdefiniowane parametry do niebieski sekcji parametrów, jak pokazano poniżej. Wprowadzone parametry wywołuje programu Excel do usługi sieci web Machine Learning i przewidywane ocenami etykiety będą wyświetlane w sekcji zielony przewidywanych wartości. Skoroszyt, będą w dalszym ciągu Tworzenie prognoz dotyczących parametrów na podstawie uczonego modelu dla wszystkich elementów wiersza wprowadzone w obszarze parametrów. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [korzystanie z usługi sieci Web Azure Machine Learning z poziomu programu Excel](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optymalizacja i dalszych eksperymentów
Teraz, mieliśmy punktu odniesienia z nasz model programu Excel, przenieśliśmy wyprzedzeniem zoptymalizować nasz Model uczenia maszynowego liniowej regresji. Użyliśmy moduł [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] do poprawy naszych wyboru danych początkowych elementów i pomogło nam to osiągnąć lepszą wydajność, 4.6% oznacza bezwzględny błąd. W przyszłych projektach użyjemy tej funkcji, który może zapisać nam tygodnie w iteracji atrybutów danych, aby znaleźć właściwy zestaw funkcji służących do modelowania. 

Następnie planujemy uwzględnienie dodatkowych algorytmów, takich jak [Bayesowskie] [ bayesian-linear-regression] lub [wzmocnione drzewa decyzyjne] [ boosted-decision-tree-regression] w naszym doświadczeniu do porównania wydajność. 

Jeśli chcesz poeksperymentować z regresji, dobry zestaw danych, aby spróbować jest regresji wydajności energetycznej przykładowego zestawu danych, która ma wiele atrybutów liczbowych. Zestaw danych jest dostarczany jako część przykładowych zestawów danych w usłudze Machine Learning Studio. Różnorodne szkolenia modułów służy do prognozowania ogrzewania obciążenia lub obciążenia chłodzenie. Wykres poniżej znajduje się porównanie wydajności różnych regresji uczy się przed energię Prognozowanie zestawu danych dla zmiennej docelowej Cooling obciążenia: 

| Modelowanie | Średni bezwzględny błąd | Średnia głównego kwadrat błąd | Względny błąd absolutny | Względna kwadrat błąd | Determinacji |
| --- | --- | --- | --- | --- | --- |
| Wzmocnione drzewo decyzyjnego |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Regresja liniowa (spadku gradientu) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regresja sieci neuronowych |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Regresja liniowa (zwykłe najmniejszych kwadratów) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Najważniejsze wnioski
Dowiedzieliśmy się znacznie przez z uruchomionego regresji programu Excel i usługi Azure Machine Learning experiments równolegle. Tworzenie modelu odniesienia w programie Excel i porównanie z modeli za pomocą usługi Machine Learning [regresji liniowej] [ linear-regression] pomogło nam Dowiedz się, usługa Azure Machine Learning i wykryliśmy możliwości poprawy danych wydajność zaznaczenie i modelu. 

Dowiedzieliśmy się także, zaleca się używać [na podstawie filtru funkcji wyboru cech] [ filter-based-feature-selection] aby przyspieszyć prognozowania przyszłych projektów. Stosując wybór funkcji do swoich danych, można utworzyć ulepszone modelu w usłudze Machine Learning o lepszej wydajności ogólnej. 

Możliwość przesyłania predykcyjne analizy prognozowania z usługi Machine Learning do programu Excel systemically umożliwia znaczny wzrost w pomyślnie Udostępnianie wyników firm szerokiego grona użytkowników użytkownika. 

## <a name="resources"></a>Zasoby
Poniżej przedstawiono niektóre zasoby pomagające w pracy z regresji: 

* Regresja w programie Excel. Jeśli nigdy nie sprawdzone regresji w programie Excel, ten samouczek ułatwia: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Prognozowanie vs regresji. Tyler Chessman napisał artykuł z bloga wyjaśniające, jak czas serii prognozowania w programie Excel zawiera opis dobre dla początkujących regresji liniowej. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Regresja liniowa zwykłych najmniejszych kwadratów: Błędów, problemów i pułapek. Wprowadzenie i dyskusji regresji: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

