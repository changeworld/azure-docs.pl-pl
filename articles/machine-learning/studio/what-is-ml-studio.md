---
title: Co to jest
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio to narzędzie obsługiwane metodą „przeciągnij i upuść” przeznaczone do szybkiego budowania modeli z gotowej do użycia biblioteki algorytmów i modułów.
keywords: azure machine learning, azure ml, ml studio
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: 4f69de744be3a9926e1c5d3e674966e24c3020dc
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455657"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co to jest usługa Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio to narzędzie do współpracy, obsługiwane metodą „przeciągnij i upuść”, które służy do budowania, testowania i wdrażania rozwiązań z zakresu analizy predykcyjnej na podstawie posiadanych danych. Usługa Machine Learning Studio publikuje modele jako usługi sieci Web, które mogą być łatwo używane w niestandardowych aplikacjach albo narzędziach do analiz biznesowych, takich jak program Excel.

Usługa Machine Learning Studio to połączenie analiz danych, analiz predykcyjnych, zasobów w chmurze oraz samych danych.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Interaktywny obszar roboczy usługi Machine Learning Studio
W celu opracowania modelu analizy predykcyjnej zwykle należy użyć danych z co najmniej jednego źródła, przekształcać i analizować te dane przy użyciu różnych funkcji do manipulowania danymi i funkcji statystycznych, a następnie wygenerować zestaw wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wyuczonego, skutecznego modelu.

Usługa **Azure Machine Learning Studio** zapewnia interaktywny, wizualny obszar roboczy, który pozwala na budowanie, testowanie i wykonywanie kolejnych iteracji modelu analizy predykcyjnej. Użytkownik przeciąga i upuszcza ***zestawy danych*** oraz ***moduły*** analityczne na interaktywną ***kanwę***, łącząc je ze sobą w celu przygotowania eksperymentu, który uruchamia w usłudze Machine Learning Studio. W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy nadejdzie odpowiedni moment, można przekształcić ***eksperyment uczenia*** w ***eksperyment predykcyjny***, a następnie ***opublikować*** go jako usługę sieci Web, dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane żadne programowanie — wystarczy tylko wizualne łączenie zestawów danych i modułów w celu utworzenia modelu analizy predykcyjnej.

![Diagram usługi Azure Machine Learning Studio: tworzenie eksperymentów, odczytywanie danych z wielu źródeł, zapisywanie ocenianych danych, zapisywanie modeli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="get-started-with-machine-learning-studio"></a>Wprowadzenie do usługi Machine Learning Studio
Po przejściu do usługi [Machine Learning Studio](https://studio.azureml.net) widoczna jest strona **Home** (Strona główna). Z tego miejsca można wyświetlać dokumenty, materiały wideo i seminaria internetowe, a także znajdować wartościowe zasoby.

Kliknij lewe górne menu, ![Menu](./media/what-is-ml-studio/menu.png) a zostanie wyświetlonych kilka opcji.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Kliknij pozycję **Cortana Intelligence**. Nastąpi przekierowanie na stronę główną [pakietu Cortana Intelligence](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite). Pakiet Cortana Intelligence to w pełni zarządzane rozwiązanie obsługujące dane big data i służące do przeprowadzania zaawansowanych analiz ułatwiających przekształcanie danych w inteligentne działanie. Zobacz stronę główną pakietu, aby uzyskać pełną dokumentację, w tym historie klientów.

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Są tu dostępne dwie opcje: **Home** (Strona główna), pierwsza wyświetlona strona, oraz **Studio**.

Kliknij pozycję **Studio**, a nastąpi przekierowanie do usługi **Azure Machine Learning Studio**. Na początek pojawi się prośba o zalogowanie się z użyciem konta Microsoft albo konta służbowego. Po zalogowaniu po lewej stronie zostaną wyświetlone następujące karty:

* **PROJECTS** (Projekty) — kolekcje eksperymentów, zestawów danych, notesów i innych zasobów reprezentujących pojedynczy projekt
* **EXPERIMENTS** (Eksperymenty) — eksperymenty, które zostały utworzone i uruchomione lub zapisane jako wersje robocze
* **WEB SERVICES** (Usługi sieci Web) — usługi sieci Web, które zostały wdrożone z eksperymentów
* **NOTEBOOKS** (Notesy) — utworzone notesy Jupyter
* **DATASETS** (Zestawy danych) — zestawy danych, które zostały przekazane do Studia
* **TRAINED MODELS** (Nauczone modele) — modele nauczone w eksperymentach i zapisane w Studio
* **SETTINGS** (Ustawienia) — zbiór ustawień, które służą do konfigurowania Twoich zasobów oraz Twojego konta

### <a name="gallery"></a>Galeria
Kliknięcie pozycji **Galeria** powoduje przejście do witryny **[Galeria sztucznej inteligencji platformy Azure](http://gallery.azure.ai/)**. Galeria jest miejscem, w którym społeczność programistów i analityków danych udostępnia rozwiązania utworzone przy użyciu składników pakietu Cortana Intelligence.

Aby uzyskać więcej informacji o galerii, zobacz [Udostępnianie i odnajdywanie zasobów w galerii Azure AI](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Składniki eksperymentu
Eksperyment składa się z zestawów danych, które dostarczają dane do modułów analitycznych łączonych w celu utworzenia modelu analizy predykcyjnej. Prawidłowy eksperyment ma następujące cechy:

* Obejmuje co najmniej jeden zestaw danych i jeden moduł
* Zestawy danych mogą być połączone tylko z modułami
* Moduły mogą być połączone z zestawami danych lub innymi modułami
* Wszystkie porty wejściowe modułów muszą mieć jakieś połączenie z przepływem danych
* Konieczne jest ustawienie wszystkich wymaganych parametrów dla każdego modułu

Eksperymenty można tworzyć od podstaw albo przy użyciu przykładowego eksperymentu jako szablonu. Aby uzyskać więcej informacji, zobacz [Kopiowanie przykładowych eksperymentów w celu tworzenia nowych eksperymentów uczenia maszynowego](sample-experiments.md).

Przykład tworzenia prostego eksperymentu zawiera temat [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md).

Bardziej szczegółowy przewodnik tworzenia rozwiązania analizy predykcyjnej zawiera temat [Tworzenie rozwiązania predykcyjnego za pomocą usługi Azure Machine Learning](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Zestawy danych
Zestaw danych to dane przekazane do usługi Machine Learning Studio, dzięki czemu mogą być używane w procesie modelowania. W usłudze Machine Learning Studio dostępny jest szereg przykładowych zestawów danych, które mogą służyć do wykonywania eksperymentów, a dodatkowe zestawy danych można przekazywać w miarę potrzeby. Oto przykładowe zestawy danych dostępne w Studio:

* **MPG data for various automobiles** (Dane MPG dotyczące różnych samochodów) — wartości przebiegu w milach na galon paliwa (MPG) dla samochodów zidentyfikowanych na podstawie liczby cylindrów, mocy itp.
* **Breast cancer data** (Dane dot. raka piersi) — dane z diagnoz raka piersi.
* **Forest fires data** (Dane dot. pożarów lasów) — rozmiary pożarów lasów w północno-wschodniej Portugalii.

Podczas tworzenia eksperymentu można wybierać zestawy danych z listy dostępnej po lewej stronie kanwy.

Listę przykładowych zestawów danych dostępnych w usłudze Azure Machine Learning Studio zawiera temat [Use the sample data sets in Azure Machine Learning Studio](use-sample-datasets.md) (Korzystanie z przykładowych zestawów danych w usłudze Azure Machine Learning Studio).

### <a name="modules"></a>Moduły
Moduł jest algorytmem, który można wykonać na danych. Usługa Machine Learning Studio zawiera szereg modułów, które pełnią różne funkcje — począwszy od transferu danych przychodzących, aż po uczenie, ocenę i walidację. Oto przykładowe dołączone moduły:

* [Convert to ARFF][convert-to-arff] (Konwertowanie na ARFF) — konwertuje serializowany zestaw danych .NET na plik formatu Attribute-Relation File Format (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Obliczanie statystyk podstawowych) — oblicza podstawowe statystyki, takie jak średnia, odchylenie standardowe itp.
* [Linear Regression][linear-regression] (Regresja liniowa) — tworzy model regresji liniowej online na podstawie spadku gradientu.
* [Score Model][score-model] (Ocena modelu) — ocenia nauczony model klasyfikacji lub regresji.

Podczas tworzenia eksperymentu można wybierać moduły z listy dostępnej po lewej stronie kanwy.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie parametry tego modułu są wyświetlane w okienku **Properties** (Właściwości) po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

Aby uzyskać pomoc w nawigowaniu po dużej bibliotece algorytmów uczenia maszynowego, zobacz [How to choose algorithms for Microsoft Azure Machine Learning](algorithm-choice.md) (Jak wybierać algorytmy w usłudze Microsoft Azure Machine Learning).

## <a name="deploying-a-predictive-analytics-web-service"></a>Wdrażanie usługi sieci Web analizy predykcyjnej
Gdy model analizy predykcyjnej jest gotowy, można go wdrożyć jako usługę sieci Web bezpośrednio z usługi Machine Learning Studio. Dodatkowe szczegóły dotyczące tego procesu zawiera temat [Wdrażanie usługi sieci Web Azure Machine Learning](publish-a-machine-learning-web-service.md).


## <a name="key-machine-learning-terms-and-concepts"></a>Kluczowe terminy i pojęcia dotyczące uczenia maszynowego
Terminy dotyczące uczenia maszynowego mogą być mylące. Poniżej znajdują się pomocne definicje kluczowych terminów. Skorzystaj z możliwości przekazania komentarzy na końcu artykułu, aby poinformować nas o dowolnym terminie, który powinien zostać zdefiniowany.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Eksploracja danych, analiza opisowa i analiza predykcyjna

**Eksploracja danych** to proces zbierania informacji o dużych i często nieustrukturyzowanych zestawach danych w celu znalezienia właściwości na potrzeby ukierunkowanej analizy.

**Wyszukiwanie danych** oznacza zautomatyzowaną eksplorację danych.

**Analiza opisowa** to proces polegający na analizowaniu zestawu danych, aby podsumować, co się stało. Analizy biznesowe — takie jak raporty ze sprzedaży, metryki sieci Web i analizy sieci społecznościowych — są w większości opisowe.

**Analiza predykcyjna** to proces tworzenia modeli z danych historycznych lub bieżących w celu przewidywania przyszłych rezultatów.

### <a name="supervised-and-unsupervised-learning"></a>Uczenie nadzorowane i nienadzorowane
 Algorytmy **uczenia nadzorowanego** są uczone z użyciem danych oznaczonych — innymi słowy, danych złożonych z przykładów żądanych odpowiedzi. Na przykład model, który identyfikuje użycie karty kredytowej w celu oszustwa, może być uczony z użyciem zestawu danych zawierającego oznaczone etykietą punkty danych dotyczące znanych oszustw i poprawnego użycia karty. Większość uczenia maszynowego jest nadzorowana.

 **Uczenie nienadzorowane** jest stosowane w przypadku danych bez oznaczeń, a celem jest znalezienie relacji w danych. Na przykład celem może być znalezienie grup demograficznych klientów o podobnych nawykach zakupowych.

### <a name="model-training-and-evaluation"></a>Uczenie i ewaluacja modelu
Model uczenia maszynowego to abstrakcja pytania, na które chcesz znaleźć odpowiedź, lub wyniku, który chcesz przewidzieć. Modele są uczone i ewaluowane na podstawie istniejących danych.

#### <a name="training-data"></a>Dane szkoleniowe
Ucząc model za pomocą danych, używasz znanego zestawu danych i wprowadzasz zmiany w modelu na podstawie właściwości danych, aby uzyskać najdokładniejszą odpowiedź. W usłudze Azure Machine Learning model jest budowany na podstawie modułu algorytmu, który przetwarza dane szkoleniowe i moduły funkcjonalne, takie jak moduł oceny.

Jeśli model wykrywania oszustw jest uczony w trybie uczenia nadzorowanego, użyj zestawu transakcji, które są oznaczone jako oszustwa lub prawidłowe transakcje. Podziel zestaw danych losowo i użyj części danych w celu nauczenia modelu, a drugiej części w celu przetestowania lub ewaluacji modelu.

#### <a name="evaluation-data"></a>Dane do ewaluacji
Po uzyskaniu nauczonego modelu należy go poddać ewaluacji, wykorzystując pozostałe dane testowe. W tym celu używane są dane, dla których znane są już wyniki, dlatego można ustalić, czy model przewiduje dokładnie.

## <a name="other-common-machine-learning-terms"></a>Inne typowe terminy dotyczące uczenia maszynowego
* **algorytm**: autonomiczny zestaw reguł przeznaczony do rozwiązywania problemów poprzez przetwarzanie danych, opeacje matematyczne lub automatyczną logikę.
* **wykrywanie anomalii**: model, który oznacza flagami nietypowe zdarzenia lub wartości, ułatwiając znalezienie problemów. Na przykład wykrywanie oszustw dotyczących karty kredytowej polega na wyszukiwaniu nietypowych zakupów.
* **dane podzielone na kategorie**: dane uporządkowane według kategorii, które mogą zostać podzielone na grupy. Na przykład zestaw danych podzielonych na kategorie dotyczący samochodów może określać rok produkcji, markę, model i cenę.
* **klasyfikacja**: model organizacji punktów danych w kategorie na podstawie zestawu danych, dla którego grupy kategorii są już znane.
* **inżynieria cech**: proces wyodrębniania lub wybierania cech związanych z zestawem danych w celu ulepszenia zestawu danych i poprawienia wyników. Na przykład dane dotyczące opłat lotniczych można wzbogacić o dni tygodnia i daty świąt. Zobacz [Feature selection and engineering in Azure Machine Learning](../team-data-science-process/create-features.md) (Wybór i inżynieria cech w usłudze Azure Machine Learning).
* **moduł**: część funkcjonalna w modelu usługi Machine Learning Studio, na przykład moduł Enter Data (Wprowadź dane), który umożliwia wprowadzanie i edycję niewielkich zestawów danych. Algorytm również jest typem modułu w usłudze Machine Learning Studio.
* **model**: model uczenia nadzorowanego jest wynikiem eksperymentu uczenia maszynowego, który obejmuje dane szkoleniowe, moduł algorytmu i moduły funkcjonalne, np. moduł Score Model (Oceń model).
* **dane liczbowe**: dane, które mają znaczenie jako pomiary (dane ciągłe) lub liczniki (dane dyskretne). Nazywane również *danymi ilościowymi*.
* **partycja**: metoda, która służy do dzielenia danych na próbki. Aby uzyskać więcej informacji, zobacz [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx) (Partycja i próbka).
* **przewidywanie**: prognoza wartości z modelu uczenia maszynowego. Widoczny może być również termin „przewidywany wynik”. Jednak przewidywane wyniki nie są ostatecznym modelem wyjściowym. Po uzyskaniu wyniku jest wykonywana ewaluacja modelu.
* **regresja**: model do przewidywania wartości w oparciu o zmienne niezależne, na przykład przewidywanie ceny samochodu na podstawie jego marki i roku produkcji.
* **wynik**: przewidywana wartość wygenerowana na podstawie klasyfikacji uzyskanej w wyniku uczenia lub modelu regresji przy użyciu [modułu Score Model (Oceń model)](https://msdn.microsoft.com/library/azure/dn905995.aspx) w usłudze Machine Learning Studio. Modele klasyfikacji zwracają również wynik prawdopodobieństwa wystąpienia przewidzianej wartości. Po wygenerowaniu wyniku z modelu można dokonać ewaluacji dokładności modelu przy użyciu [modułu Evaluate Model (Ewaluuj model)](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **próbka**: część zestawu danych reprezentująca cały zestaw. Próbki mogą być wybierane losowo lub na podstawie konkretnych cech zestawu danych.

## <a name="next-steps"></a>Następne kroki
Podstaw analizy predykcyjnej i uczenia maszynowego możesz się nauczyć, korzystając z [przewodnika Szybki start krok po kroku](create-experiment.md), a także [rozwijając przykłady](sample-experiments.md).


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
