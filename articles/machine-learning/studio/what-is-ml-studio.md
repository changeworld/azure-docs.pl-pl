---
title: Co to jest usługa Azure Machine Learning Studio? | Microsoft Docs
description: Omówienie usługi Azure ML Studio, narzędzia obsługiwanego metodą „przeciągnij i upuść” przeznaczonego do szybkiego budowania modeli z gotowej do użycia biblioteki algorytmów i modułów.
keywords: azure machine learning, azure ml, ml studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/20/2017
ms.openlocfilehash: 17a95ab00ed7bbda85a8fba5456c758f3ee9f116
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-machine-learning-studio"></a>Co to jest usługa Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio to narzędzie do współpracy, obsługiwane metodą „przeciągnij i upuść”, które służy do budowania, testowania i wdrażania rozwiązań z zakresu analizy predykcyjnej na podstawie posiadanych danych. Usługa Machine Learning Studio publikuje modele jako usługi sieci Web, które mogą być łatwo używane w niestandardowych aplikacjach albo narzędziach do analiz biznesowych, takich jak program Excel.

Usługa Machine Learning Studio to połączenie analiz danych, analiz predykcyjnych, zasobów w chmurze oraz samych danych.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Interaktywny obszar roboczy usługi Machine Learning Studio
W celu opracowania modelu analizy predykcyjnej zwykle należy użyć danych z co najmniej jednego źródła, przekształcać i analizować te dane przy użyciu różnych funkcji do manipulowania danymi i funkcji statystycznych, a następnie wygenerować zestaw wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wyuczonego, skutecznego modelu.

Usługa **Azure Machine Learning Studio** zapewnia interaktywny, wizualny obszar roboczy, który pozwala na budowanie, testowanie i wykonywanie kolejnych iteracji modelu analizy predykcyjnej. Użytkownik przeciąga i upuszcza ***zestawy danych*** oraz ***moduły*** analityczne na interaktywną ***kanwę***, łącząc je ze sobą w celu przygotowania eksperymentu, który uruchamia w usłudze Machine Learning Studio. W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy nadejdzie odpowiedni moment, można przekształcić ***eksperyment uczenia*** w ***eksperyment predykcyjny***, a następnie ***opublikować*** go jako usługę sieci Web, dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane żadne programowanie — wystarczy tylko wizualne łączenie zestawów danych i modułów w celu utworzenia modelu analizy predykcyjnej.

> [!TIP]
> Aby pobrać i wydrukować diagram, który zawiera przegląd możliwości usługi Machine Learning Studio, zobacz [Diagram przeglądowy możliwości usługi Azure Machine Learning Studio](studio-overview-diagram.md).
> 
> 

![Diagram usługi Azure ML Studio: tworzenie eksperymentów, odczytywanie danych z wielu źródeł, zapis ocenianych danych, zapis modeli.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Wprowadzenie do usługi Machine Learning Studio
Po przejściu do usługi [Machine Learning Studio](https://studio.azureml.net) widoczna jest strona **Home** (Strona główna). Z tego miejsca można wyświetlać dokumenty, materiały wideo i seminaria internetowe, a także znajdować wartościowe zasoby.

Kliknij lewe górne menu, ![Menu](./media/what-is-ml-studio/menu.png) a zostanie wyświetlonych kilka opcji.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Kliknij pozycję **Cortana Intelligence**. Nastąpi przekierowanie na stronę główną [pakietu Cortana Intelligence](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite). Pakiet Cortana Intelligence to w pełni zarządzane rozwiązanie obsługujące dane big data i służące do przeprowadzania zaawansowanych analiz ułatwiających przekształcanie danych w inteligentne działanie. Zobacz stronę główną pakietu, aby uzyskać pełną dokumentację, w tym historie klientów.

### <a name="azure-machine-learning"></a>Azure Machine Learning
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
Kliknięcie pozycji **Galeria** powoduje przejście do witryny **[Galeria sztucznej inteligencji platformy Azure](http://gallery.cortanaintelligence.com/)**. Galeria jest miejscem, w którym społeczność programistów i analityków danych udostępnia rozwiązania utworzone przy użyciu składników pakietu Cortana Intelligence.

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

Bardziej szczegółowy przewodnik tworzenia rozwiązania analizy predykcyjnej zawiera temat [Tworzenie rozwiązania predykcyjnego za pomocą usługi Azure Machine Learning](walkthrough-develop-predictive-solution.md).

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

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
