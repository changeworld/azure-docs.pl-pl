---
title: Przy użyciu regresji liniowej w uczeniu maszynowym | Dokumentacja firmy Microsoft
description: Porównanie modeli regresji liniowej w programie Excel, jak i w usłudze Azure Machine Learning Studio
metakeywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: 162fc96c44db3c92103e12922de14b543daec9bf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836229"
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Używanie regresji liniowej w usłudze Azure Machine Learning
> *Kate Baroni* i *Ben Boatman* są architekci rozwiązań w firmy Microsoft danych usługi Insights centrum doskonałości przedsiębiorstwa. W tym artykule opisano w nich ich obsługi migracji na istniejący pakiet analizy regresji rozwiązanie oparte na chmurze za pomocą usługi Azure Machine Learning. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Cel
Nasze projektu wprowadzenie do dwóch celów pamiętać: 

1. Za pomocą analizy predykcyjnej zwiększyć dokładność naszej organizacji miesięczne projekcje przychodu 
2. Za pomocą usługi Azure Machine Learning upewnij się, optymalizacja, zwiększyć szybkość pracy i skali naszych wyników. 

Jak wiele firm naszej organizacji przechodzi przez miesięczne przychodów, funkcja prognozowania procesu. Mały zespół analitycy biznesowi został zlecił mu przy użyciu usługi Azure Machine Learning wspomagające proces i zwiększenia dokładności prognozy. Zespół przeznaczonego kilka miesięcy zbieranie danych z wielu źródeł i uruchamianie atrybuty danych za pomocą klucza dotyczy usługi sprzedaży prognozowania atrybutów identyfikacyjnych analizy statystycznej. Następnym krokiem jest aby rozpocząć tworzenie prototypów statystyczne regresji modeli danych w programie Excel. W ciągu kilku tygodni było Excel model regresji, który został outperforming bieżące pole i finance procesy prognozowania. To stała się wynik prognozowania linii bazowej. 

Następnie Wybraliśmy następnego kroku przenoszenia naszych analizy predykcyjnej za pośrednictwem usługi Azure Machine Learning, aby dowiedzieć się, jak uczenia maszynowego można ulepszyć na wydajność predykcyjnej.

## <a name="achieving-predictive-performance-parity"></a>Obsługiwanie parzystości predykcyjnej wydajności
Nasze priorytet było osiągnięcia parzystości między modelami regresji uczenia maszynowego i Excel. Dla celów szkoleniowych i testów danych, biorąc pod uwagę tych samych danych, a ten sam podział, możemy osiągnięcia parzystości predykcyjnej wydajności pomiędzy programami Excel i uczenia maszynowego. Początkowo firma Microsoft nie powiodło się. Model programu Excel outperformed modelu uczenia maszynowego. Błąd: ze względu na brak opis ustawienia podstawowego narzędzia w uczeniu maszynowym. Po zakończeniu synchronizacji z zespół produktu usługi Machine Learning możemy zdobytych lepiej zrozumieć base ustawienia wymagane dla naszych zestawów danych i osiągnięte parzystości między dwa modele. 

### <a name="create-regression-model-in-excel"></a>Utwórz model regresji w programie Excel
Nasze regresji Excel używane modelu regresji liniowej standardowe znalezione w pakiecie programu Excel Analysis ToolPak. 

Firma Microsoft obliczana *średniej % bezwzględny błąd* i używać go jako miara wydajności dla modelu. Zajęło 3 miesiące, na model pracy, przy użyciu programu Excel. Firma Microsoft wprowadzane większość uczenia do eksperymentu Machine Learning Studio, co ostatecznie było korzystne w opis wymagań.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Tworzenie porównywalnych eksperymentu w usłudze Azure Machine Learning
Możemy wykonać te kroki, aby utworzyć naszych eksperymentu w usłudze Machine Learning Studio: 

1. Przekazany zestawu danych jako plik csv do usługi Machine Learning Studio (bardzo mały plik)
2. Utworzony nowy eksperyment i [Select Columns in Dataset] [ select-columns] modułu, aby wybrać te same funkcje danych używany w programie Excel 
3. Używane [podziału danych] [ split] modułu (z *wyrażenia względne* tryb) do dzielenia danych na tym samym zestawów danych szkoleniowych, podobnie jak w programie Excel 
4. Badawcze, mające z [regresji liniowej] [ linear-regression] modułu (tylko opcje domyślne), udokumentowane i porównać wyniki w modelu regresji programu Excel

### <a name="review-initial-results"></a>Przejrzyj wyniki początkowej
Na początku model programu Excel wyraźnie outperformed modelu usługi Machine Learning Studio: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Wydajność | | |
| <ul style="list-style-type: none;"><li>Dostosowana R-kwadrat</li></ul> |0.96 |ND |
| <ul style="list-style-type: none;"><li>Współczynnik <br />Oznaczanie</li></ul> |ND |0.78<br />(dokładność niski) |
| Oznacza błąd absolutny |$9. 5M |$ 19.4 M |
| Oznacza błąd absolutny (%) |6.03% |12.2% |

Wystąpił naszych procesu i wyniki przez programistów i analityków danych na zespół usługi Machine Learning, one szybko udostępniane niektóre przydatne porady. 

* Jeśli używasz [regresji liniowej] [ linear-regression] modułu w usłudze Machine Learning Studio, znajdują się dwie metody:
  * Spadku gradientu online: Bardziej odpowiednie dla dużą skalę problemów może być
  * Zwykłej najmniejszych kwadratów: Jest to metoda większość osób postrzega podczas ich usłyszeć regresji liniowej. W przypadku małych zestawów danych zwykłej najmniejszych kwadratów może być bardziej optymalnym wyborem.
* Należy rozważyć Dostosowywanie parametr wagi uregulowania L2 w celu zwiększenia wydajności. Ustawiana 0,001 domyślnie, ale dla naszych niewielki zestaw danych możemy ustawić ją na 0,005 w celu zwiększenia wydajności. 

### <a name="mystery-solved"></a>Taki rozwiązanie!
Stosowania zaleceń, firma Microsoft uzyskuje do linii bazowej wydajności w usłudze Machine Learning Studio jako przy użyciu programu Excel: 

|  | Excel | Studio (Initial) | Studio z najmniejszych kwadratów |
| --- |:---:|:---:|:---:|
| Wartość etykietą |Rzeczywiste (numeryczne) |tym samym |tym samym |
| Uczeń |Excel -> dane analizy -> regresji |Regresji liniowej. |Regresja liniowa |
| Opcje uczeń |ND |Domyślne |zwykłe najmniejszych kwadratów<br />L2 = 0,005 |
| Zestaw danych |26 wierszy, funkcje 3, 1 etykiety. Wszystkie liczbowe. |tym samym |tym samym |
| Podziel: pociągu |Excel ćwiczenie najpierw 18 wierszy, przetestowane na ostatnie 8 wierszy. |tym samym |tym samym |
| Podziel: testu |Formuła regresji stosowany do wierszy ostatnie 8 w programie Excel |tym samym |tym samym |
| **Wydajność** | | | |
| Dostosowana R-kwadrat |0.96 |ND | |
| Współczynnik determinacji |ND |0.78 |0.952049 |
| Oznacza błąd absolutny |$9. 5M |$ 19.4 M |$9. 5M |
| Oznacza błąd absolutny (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Ponadto współczynników programu Excel w porównaniu do obciążenia funkcji w Azure trenowanego modelu:

|  | Współczynniki programu Excel | Wagi funkcji platformy Azure |
| --- |:---:|:---:|
| ODCIĘTA/odchylenia |19470209.88 |19328500 |
| Funkcja A programu |0.832653063 |0.834156 |
| Funkcja B |11071967.08 |11007300 |
| Funkcja C |25383318.09 |25140800 |

## <a name="next-steps"></a>Następne kroki
Możemy korzystać z usługi sieci web uczenie maszynowe programu Excel. Nasze analitycy biznesowi zależne od programu Excel i niezbędna sposób wywoływania usługi sieci web usługi Machine Learning z wierszem danych programu Microsoft Excel i zwracać wartości prognozowanej do programu Excel. 

Firma Microsoft również chciała zoptymalizować nasz model przy użyciu opcji i algorytmy dostępne w usłudze Machine Learning Studio.

### <a name="integration-with-excel"></a>Integracja z programem Excel
Nasze rozwiązanie było operacjonalizacji nasz model regresji uczenia maszynowego, tworząc usługi sieci web z trenowanego modelu. W ciągu kilku minut Usługa sieci web została utworzona i może nazywamy go bezpośrednio z programu Excel w celu zwrócenia wartości prognozowanej przychodu. 

*Pulpitu nawigacyjnego usług sieci Web* znajdują się w skoroszycie programu Excel do pobrania. Skoroszyt zawiera wstępnie sformatowane informacje interfejsu API i schematów usługi sieci web osadzone. Po kliknięciu *Pobierz skoroszyt programu Excel*, zostanie otwarty skoroszyt i zapisaniu go na komputerze lokalnym. 

![][1]

W skoroszycie, otwórz skopiuj wstępnie zdefiniowane parametry do niebieski sekcji parametru, jak pokazano poniżej. Po wprowadzeniu parametry Excel uwidacznia z usługą sieci web uczenie maszynowe i przewidywane etykiety scored będą wyświetlane w sekcji zielony przewidywane wartości. Skoroszyt będą w dalszym ciągu tworzenia prognoz dotyczących parametrów na podstawie uczonego modelu dla wszystkich elementów wiersza objęte parametrów. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [konsumowania usługi sieci Web Azure Machine Learning z programu Excel](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optymalizacja i dalszych eksperymentów.
Teraz, gdy było linii bazowej z modelu programu Excel, przenieśliśmy dalej w celu zoptymalizowania naszych modelu uczenia maszynowego liniowej regresji. My używamy modułu [na podstawie filtru wybór funkcji] [ filter-based-feature-selection] do poprawy naszych wyboru początkowe dane elementów i pomogły osiągnąć lepszą wydajność, 4.6% oznacza błąd absolutny. Dla przyszłych projektów użyjemy tej funkcji, który może zapisać nam tygodnie w iteracji atrybuty danych można znaleźć prawidłowego zestawu funkcji służących do modelowania. 

Następnie planujemy Dołącz algorytmy dodatkowe, takie jak [Estymacja Bayesian] [ bayesian-linear-regression] lub [Boosted drzew decyzyjnych] [ boosted-decision-tree-regression] w naszym doświadczeniu porównanie wydajności. 

Jeśli chcesz wypróbować regresji dobrej zestawu danych, aby spróbować jest dataset próbki energii wydajność regresji ma wiele atrybutów wartości liczbowych. Zestaw danych jest dostarczane jako część przykładowych zestawów danych w usłudze Machine Learning Studio. Szereg modułów szkoleniowych służy do prognozowania ogrzewania obciążenia lub chłodzenia obciążenia. Wykres poniżej znajduje się porównanie wydajności różnych regresji uzyskuje informacje o względem zapotrzebowania na energię przewidywania zestawu danych dla Zmienna docelowa Cooling obciążenia: 

| Modelowanie | Oznacza błąd absolutny | Błąd kwadrat średniej głównego | Względny błąd absolutny | Względna kwadrat błąd | Współczynnik determinacji |
| --- | --- | --- | --- | --- | --- |
| Drzewo decyzyjne boosted |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Regresja liniowa (spadku gradientu) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regresja sieci neuronowej |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Regresja liniowa (zwykłej najmniejszych kwadratów) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Takeaways klucza
Dowiedzieliśmy się znacznie przez z uruchomionych regresji programu Excel i usługi Azure Machine Learning experiments równolegle. Tworzenie linii bazowej modelu w programie Excel i należy go porównać z modelami za pomocą uczenia maszynowego [regresji liniowej] [ linear-regression] pomógł nam informacje uczenie maszynowe Azure i możemy odnaleźć możliwości, aby zwiększyć wydajność, a wybór modelu danych. 

Również znaleźć zaleca używania [na podstawie filtru wybór funkcji] [ filter-based-feature-selection] w celu przyspieszenia przewidywania przyszłych projektów. Stosując Wybieranie funkcji do danych, można utworzyć ulepszone modelu w uczeniu maszynowym z lepszą ogólną wydajność. 

Możliwość przekazywania predykcyjnej analityczne prognozowania z uczenia maszynowego do programu Excel systemically umożliwia znaczne zwiększenie pomyślnie Udostępnianie wyników do odbiorców szerokie biznesowych użytkownika. 

## <a name="resources"></a>Zasoby
Poniżej przedstawiono niektóre zasoby pomagające w pracy z regresji: 

* Regresja w programie Excel. Jeśli nigdy nie podjęto regresji w programie Excel, w tym samouczku ułatwia: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Funkcja prognozowania vs regresji. Tyler Chessman zapisano artykułu blog, objaśnienia dotyczące czasu serii prognozowania w programie Excel, zawiera opis dla początkujących dobrej regresji liniowej. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Regresja liniowa zwykłej najmniejszych kwadratów: Wady, problemów i problemów. Wprowadzenie oraz omówienie regresji: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

