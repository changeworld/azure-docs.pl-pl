---
title: Migrowanie analityki z programu Excel
titleSuffix: ML Studio (classic) - Azure
description: Porównanie modeli regresji liniowej w programie Excel i usłudze Azure Machine Learning Studio (klasyczny)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217792"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrowanie analizy z programu Excel do usługi Azure Machine Learning Studio (klasyczne)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *Kate Baroni* i *Ben Boatman* są architektami rozwiązań dla przedsiębiorstw w centrum doskonałości data insights firmy Microsoft. W tym artykule opisują swoje środowisko migracji istniejącego pakietu analizy regresji do rozwiązania opartego na chmurze przy użyciu usługi Azure Machine Learning Studio (klasyczny).

## <a name="goal"></a>Cel

Nasz projekt rozpoczął się z myślą o dwóch celach: 

1. Korzystaj z analizy predykcyjnej, aby poprawić dokładność miesięcznych prognoz przychodów naszej organizacji 
2. Użyj usługi Azure Machine Learning Studio (classic), aby potwierdzić, zoptymalizować, zwiększyć prędkość i skalę naszych wyników. 

Podobnie jak wiele firm, nasza organizacja przechodzi miesięczny proces prognozowania przychodów. Nasz mały zespół analityków biznesowych otrzymał zadanie użycia usługi Azure Machine Learning Studio (classic) do obsługi procesu i zwiększenia dokładności prognozy. Zespół spędził kilka miesięcy zbierając dane z wielu źródeł i uruchamiając atrybuty danych za pomocą analizy statystycznej identyfikując kluczowe atrybuty istotne dla prognozowania sprzedaży usług. Następnym krokiem było rozpoczęcie prototypowania modeli regresji statystycznej na danych w programie Excel. W ciągu kilku tygodni mieliśmy model regresji programu Excel, który przewyższał bieżące procesy prognozowania pola i finansowania. Stało się to wynikiem prognozowania linii bazowej. 

Następnie zrobiliśmy kolejny krok, aby przenieść nasze analizy predykcyjne do Studio (classic), aby dowiedzieć się, jak Studio (klasyczny) może poprawić wydajność predykcyjną.

## <a name="achieving-predictive-performance-parity"></a>Osiągnięcie predykcyjnej parytetu wydajności
Naszym priorytetem było osiągnięcie parytetu między modelami regresji Studio (klasycznym) i Excela. Biorąc pod uwagę te same dane i ten sam podział dla danych szkoleniowych i testowych, chcieliśmy osiągnąć predykcyjną parzystość wydajności między programem Excel i Studio (klasyczny). Początkowo zawiedliśmy. Model programu Excel wyprzedził model Studio (klasyczny). Awaria była spowodowana brakiem zrozumienia ustawienia narzędzia bazowego w Studio (klasyczny). Po synchronizacji z zespołem produktów Studio (klasyczny) uzyskaliśmy lepsze zrozumienie ustawienia podstawowego wymaganego dla naszych zestawów danych i osiągnęliśmy parytet między tymi dwoma modelami. 

### <a name="create-regression-model-in-excel"></a>Tworzenie modelu regresji w programie Excel
Nasza regresja programu Excel używała standardowego modelu regresji liniowej znajdującego się w programie Excel Analysis ToolPak. 

Obliczyliśmy *błąd Mean Absolute %* i użyliśmy go jako miary wydajności dla modelu. Zajęło 3 miesiące, aby dotrzeć do działającego modelu za pomocą programu Excel. Wnieśliśmy wiele nauki do studio (klasyczny) eksperyment, który ostatecznie był korzystny w zrozumieniu wymagań.

### <a name="create-comparable-experiment-in-studio-classic"></a>Tworzenie porównywalnego eksperymentu w Studio (klasyczny)
Poszliśmy te kroki, aby stworzyć nasz eksperyment w Studio (klasyczny): 

1. Przesłano zestaw danych jako plik csv do Studio (klasyczny) (bardzo mały plik)
2. Utworzono nowy eksperyment i użyto modułu [Wybierz kolumny w zestawie danych,][select-columns] aby wybrać te same funkcje danych, które są używane w programie Excel 
3. Używany moduł [Podziel dane][split] (z *trybem wyrażenia względnego)* do dzielenia danych na te same zestawy danych szkoleniowych, co w programie Excel 
4. Eksperymentował z modułem [regresji liniowej][linear-regression] (tylko opcje domyślne), dokumentowano i porównano wyniki z naszym modelem regresji programu Excel

### <a name="review-initial-results"></a>Przejrzyj wstępne wyniki
Na początku model Programu Excel wyraźnie przewyższał model Studio (klasyczny): 

|  | Excel | Studio (klasyczna) |
| --- |:---:|:---:|
| Wydajność | | |
| <ul style="list-style-type: none;"><li>Skorygowany R Kwadrat</li></ul> |0.96 |Nie dotyczy |
| <ul style="list-style-type: none;"><li>Współczynnik <br />Oznaczanie</li></ul> |Nie dotyczy |0,78<br />(niska dokładność) |
| Średni błąd bezwzględny |9,5 mln do 10 mln usd |19,4 mln zł |
| Średni błąd bezwzględny (%) |6.03% |12.2% |

Kiedy uruchomiliśmy nasz proces i wyniki przez deweloperów i analityków danych w zespole uczenia maszynowego, szybko dostarczyli kilka przydatnych wskazówek. 

* W przypadku korzystania z modułu [regresji liniowej][linear-regression] w Studio (klasycznym) dostępne są dwie metody:
  * Online Gradient Descent: Może być bardziej odpowiedni dla problemów na większą skalę
  * Zwykłe najmniej kwadratów: Jest to metoda większość ludzi myśli, gdy słyszą regresji liniowej. W przypadku małych zestawów danych zwykłe najmniejsze kwadraty mogą być bardziej optymalnym wyborem.
* Rozważ podkręcenie parametru L2 Regularization Weight, aby poprawić wydajność. Domyślnie jest ustawiona na 0,001, ale dla naszego małego zestawu danych ustawiliśmy go na 0,005, aby poprawić wydajność. 

### <a name="mystery-solved"></a>Tajemnica rozwiązana!
Kiedy zastosowaliśmy zalecenia, osiągnęliśmy taką samą wydajność bazową w Studio (klasycznym), jak w przypadku programu Excel: 

|  | Excel | Studio (klasyczny) (początkowy) | Studio (klasyczne) w / Najmniej kwadratów |
| --- |:---:|:---:|:---:|
| Wartość oznaczona etykietą |Wartości rzeczywiste (numeryczne) |Tym samym |Tym samym |
| Ucznia |Excel -> Analiza danych - regresja > |Regresja liniowa. |Regresja liniowa |
| Opcje ucznia |Nie dotyczy |Wartość domyślna |zwykłe najmniejsze kwadraty<br />L2 = 0,005 |
| Zestaw danych |26 rzędów, 3 cechy, 1 etykieta. Wszystkie numeryczne. |Tym samym |Tym samym |
| Split: Pociąg |Program Excel trenował w pierwszych 18 wierszach, testowany w ostatnich 8 wierszach. |Tym samym |Tym samym |
| Podział: Test |Formuła regresji programu Excel zastosowana do ostatnich 8 wierszy |Tym samym |Tym samym |
| **Wydajność** | | | |
| Skorygowany R Kwadrat |0.96 |Nie dotyczy | |
| Współczynnik oznaczalności |Nie dotyczy |0,78 |0.952049 |
| Średni błąd bezwzględny |9,5 mln do 10 mln usd |19,4 mln zł |9,5 mln do 10 mln usd |
| Średni błąd bezwzględny (%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

Ponadto współczynniki programu Excel w porównaniu dobrze do wagi funkcji w modelu uczonym platformy Azure:

|  | Współczynniki programu Excel | Wagi obiektów platformy Azure |
| --- |:---:|:---:|
| Przecięcie/odchylenie |19470209.88 |19328500 |
| Funkcja A |0.832653063 |0.834156 |
| Funkcja B |11071967.08 |11007300 |
| Funkcja C |25383318.09 |25140800 |

## <a name="next-steps"></a>Następne kroki
Chcieliśmy korzystać z usługi sieci web machine learning w programie Excel. Nasi analitycy biznesowi polegają na programie Excel i potrzebowaliśmy sposobu, aby wywołać usługę internetową usługi machine learning z wierszem danych programu Excel i zwrócić przewidywaną wartość do programu Excel. 

Chcieliśmy również zoptymalizować nasz model, wykorzystując opcje i algorytmy dostępne w Studio (klasyczny).

### <a name="integration-with-excel"></a>Integracja z programem Excel
Naszym rozwiązaniem było operacjonalizacja naszego modelu regresji uczenia maszynowego przez utworzenie usługi sieci web z przeszkolonego modelu. W ciągu kilku minut usługa sieci web została utworzona i możemy wywołać ją bezpośrednio z programu Excel, aby zwrócić przewidywaną wartość przychodu. 

Sekcja *Pulpit nawigacyjny usług sieci Web* zawiera skoroszyt programu Excel do pobrania. Skoroszyt jest wstępnie sformatowany z osadzonym interfejsem API usługi sieci web i osadzonymi informacjami o schemacie. Po *kliknięciu przycisku Pobierz skoroszyt programu Excel*zostanie otwarty i będzie można go zapisać na komputerze lokalnym. 

![Pobieranie skoroszytu programu Excel z pulpitu nawigacyjnego usług sieci Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Po otwarciu skoroszytu skopiuj wstępnie zdefiniowane parametry do niebieskiej sekcji Parametr, jak pokazano poniżej. Po wprowadzeniu parametrów program Excel wywołuje usługę sieci web usługi Machine Learning, a przewidywane etykiety punktów będą wyświetlane w zielonej sekcji Przewidywane wartości. Skoroszyt będzie nadal tworzyć prognoz dla parametrów na podstawie przeszkolonego modelu dla wszystkich elementów wiersza wprowadzonych w obszarze Parametry. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [Korzystanie z usługi azure machine learning web z programu Excel](consuming-from-excel.md). 

![Skoroszyt programu Excel szablon łączący się z wdrożoną usługą sieci Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optymalizacja i dalsze eksperymenty
Teraz, gdy mieliśmy linię bazową z naszym modelem programu Excel, posunęliśmy się do przodu, aby zoptymalizować nasz model regresji liniowej uczenia maszynowego. Użyliśmy modułu [Filter-Based Feature Selection,][filter-based-feature-selection] aby poprawić nasz wybór początkowych elementów danych i pomogło nam to osiągnąć poprawę wydajności o 4,6% Średni błąd bezwzględny. W przyszłych projektach użyjemy tej funkcji, która może zaoszczędzić nam tygodni w iteracji za pomocą atrybutów danych, aby znaleźć odpowiedni zestaw funkcji do użycia do modelowania. 

Następnie planujemy uwzględnić dodatkowe algorytmy, takie jak [Bayesian][bayesian-linear-regression] lub [Wzmocnione drzewa decyzyjne][boosted-decision-tree-regression] w naszym eksperymencie, aby porównać wydajność. 

Jeśli chcesz eksperymentować z regresji, dobry zestaw danych do wypróbowania jest energy efficiency regresji przykładowy zestaw danych, który ma wiele atrybutów liczbowych. Zestaw danych jest dostarczany jako część przykładowych zestawów danych w Studio (klasyczny). Można użyć różnych modułów szkoleniowych do przewidywania obciążenia grzewczego lub obciążenia chłodzenia. Poniższy wykres przedstawia porównanie wydajności różnych regresji wylicza się z zestawu danych efektywności energetycznej przewidywanie dla zmiennej docelowej Obciążenie chłodzenia: 

| Model | Średni błąd bezwzględny | Średni do kwadratu katalogu głównego | Względny błąd bezwzględny | Względny kwadratowy błąd | Współczynnik oznaczalności |
| --- | --- | --- | --- | --- | --- |
| Wzmocnione drzewo decyzyjne |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Regresja liniowa (gradientowe zejście) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regresja sieci neuronowej |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Regresja liniowa (zwykłe najmniejsze kwadraty) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Kluczowe dania na wynos
Wiele się nauczyliśmy, uruchamiając regresję programu Excel i eksperymenty studyjne (klasyczne). Utworzenie modelu bazowego w programie Excel i porównanie go z modelami przy użyciu [regresji liniowej][linear-regression] uczenia maszynowego pomogło nam nauczyć się studio (klasyczne) i odkryliśmy możliwości poprawy wyboru danych i wydajności modelu. 

Odkryliśmy również, że zaleca się użycie [wyboru funkcji opartych na filtrze,][filter-based-feature-selection] aby przyspieszyć przyszłe projekty przewidywania. Stosując wybór funkcji do danych, można utworzyć ulepszony model w Studio (klasyczny) z lepszą ogólną wydajnością. 

Możliwość przenoszenia predykcyjnego prognozowania analitycznego ze Studia (klasycznego) do programu Excel systemowo pozwala na znaczny wzrost możliwości pomyślnego dostarczania wyników szerokiemu gronu odbiorców użytkowników biznesowych. 

## <a name="resources"></a>Resources
Oto kilka zasobów ułatwia nych w pracy z regresją: 

* Regresja w programie Excel. Jeśli nigdy nie próbowałeś regresji w programie Excel, ten samouczek ułatwia:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regresja a prognozowanie. Tyler Chessman napisał artykuł na blogu wyjaśniający, jak to zrobić prognozowanie szeregów czasowych w Excelu, który zawiera dobry opis regresji liniowej dla początkujących. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Zwykła najmniejsza regresja liniowa: wady, problemy i pułapki. Aby zapoznać się z wprowadzeniem i omówieniem regresji: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

