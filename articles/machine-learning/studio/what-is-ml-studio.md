---
title: Co to jest
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio to narzędzie obsługiwane metodą „przeciągnij i upuść” przeznaczone do szybkiego budowania modeli z gotowej do użycia biblioteki algorytmów i modułów.
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
ms.date: 04/20/2019
ms.openlocfilehash: 7a2bd8ebf2e96cf2251d98501bbf42007d43a660
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683005"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co to jest usługa Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio to narzędzie do współpracy, obsługiwane metodą „przeciągnij i upuść”, które służy do budowania, testowania i wdrażania rozwiązań z zakresu analizy predykcyjnej na podstawie posiadanych danych. Usługa Machine Learning Studio publikuje modele jako usługi sieci Web, które mogą być łatwo używane w niestandardowych aplikacjach albo narzędziach do analiz biznesowych, takich jak program Excel.

Usługa Machine Learning Studio to połączenie analiz danych, analiz predykcyjnych, zasobów w chmurze oraz samych danych.


## <a name="the-machine-learning-studio-interactive-workspace"></a>Interaktywny obszar roboczy usługi Machine Learning Studio
W celu opracowania modelu analizy predykcyjnej zwykle należy użyć danych z co najmniej jednego źródła, przekształcać i analizować te dane przy użyciu różnych funkcji do manipulowania danymi i funkcji statystycznych, a następnie wygenerować zestaw wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wyuczonego, skutecznego modelu.

Usługa **Azure Machine Learning Studio** zapewnia interaktywny, wizualny obszar roboczy, który pozwala na budowanie, testowanie i wykonywanie kolejnych iteracji modelu analizy predykcyjnej. Użytkownik przeciąga i upuszcza ***zestawy danych*** oraz ***moduły*** analityczne na interaktywną ***kanwę***, łącząc je ze sobą w celu przygotowania eksperymentu, który uruchamia w usłudze Machine Learning Studio. W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy nadejdzie odpowiedni moment, można przekształcić ***eksperyment uczenia*** w ***eksperyment predykcyjny***, a następnie ***opublikować*** go jako usługę sieci Web, dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane żadne programowanie — wystarczy tylko wizualne łączenie zestawów danych i modułów w celu utworzenia modelu analizy predykcyjnej.

![Diagram usługi Azure Machine Learning Studio: tworzenie eksperymentów, odczytywanie danych z wielu źródeł, zapisywanie ocenianych danych, zapisywanie modeli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Pobieranie diagramu przeglądowego usługi Machine Learning Studio
Pobierz diagram **Przegląd możliwości usługi Microsoft Azure Machine Learning Studio** i uzyskaj ogólny obraz możliwości usługi Machine Learning Studio. Aby mieć ten diagram zawsze w pobliżu, wydrukuj go w rozmiarze tabloidu (11 cali x 17 cali).

**Pobierz diagram tutaj: [Przegląd możliwości usługi Microsoft Azure Machine Learning Studio](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Przegląd możliwości usługi Microsoft Azure Machine Learning Studio](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Wprowadzenie do usługi Machine Learning Studio
Po przejściu do usługi [Machine Learning Studio](https://studio.azureml.net) widoczna jest strona **Home** (Strona główna). Z tego miejsca można wyświetlać dokumenty, materiały wideo i seminaria internetowe, a także znajdować wartościowe zasoby.

Kliknij lewe górne menu, ![Menu](./media/what-is-ml-studio/menu.png) a zostanie wyświetlonych kilka opcji.
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
Kliknięcie pozycji **Galeria** powoduje przejście do witryny **[Galeria sztucznej inteligencji platformy Azure](https://gallery.azure.ai/)**. Galeria jest miejscem, w którym społeczność programistów i analityków danych udostępnia rozwiązania utworzone przy użyciu składników pakietu Cortana Intelligence.

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

Bardziej szczegółowy przewodnik tworzenia rozwiązania analizy predykcyjnej zawiera temat [Tworzenie rozwiązania predykcyjnego za pomocą usługi Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

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

Aby uzyskać pomoc w nawigowaniu po dużej bibliotece algorytmów uczenia maszynowego, zobacz [Jak wybierać algorytmy w usłudze Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Wdrażanie usługi sieci Web analizy predykcyjnej
Gdy model analizy predykcyjnej jest gotowy, można go wdrożyć jako usługę sieci Web bezpośrednio z usługi Machine Learning Studio. Dodatkowe szczegóły dotyczące tego procesu zawiera temat [Wdrażanie usługi sieci Web Azure Machine Learning](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Czym różni się usługa Machine Learning Studio od usługi Azure Machine Learning Service?

[Usługa Azure Machine Learning Service](../service/overview-what-is-azure-ml.md) udostępnia zestawy SDK i usługi, które służą do szybkiego przygotowywania danych oraz trenowania i wdrażania modeli uczenia maszynowego. Podnieś produktywność i zoptymalizuj koszty, korzystając ze środowiska obliczeniowego z automatycznym skalowaniem i potoków. Korzystaj z tych możliwości za pośrednictwem platform typu open source języka Python, takich jak PyTorch, TensorFlow i scikit-learn. 

Używaj usługi Machine Learning Studio, gdy chcesz w szybki i prosty sposób poeksperymentować z modelami uczenia maszynowego, a wbudowane algorytmy uczenia maszynowego są wystarczające dla Twoich rozwiązań.

Używaj usługi uczenia maszynowego, jeśli pracujesz w środowisku języka Python, chcesz mieć większą kontrolę nad algorytmami uczenia maszynowego lub chcesz użyć bibliotek uczenia maszynowego typu „open source”.

> [!NOTE]
> Modele utworzone w usłudze Azure Machine Learning Studio nie mogą być wdrażane ani zarządzane przez usługę Azure Machine Learning.

## <a name="free-trial"></a>Bezpłatna wersja próbna

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Kolejne kroki
Podstaw analizy predykcyjnej i uczenia maszynowego możesz się nauczyć, korzystając z [przewodnika Szybki start krok po kroku](create-experiment.md), a także [rozwijając przykłady](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
