---
title: Co to jest
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio to narzędzie obsługiwane metodą „przeciągnij i upuść” przeznaczone do szybkiego budowania modeli z gotowej do użycia biblioteki algorytmów i modułów.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: 4ec9cff652bf1badf526d490547ad78de31ac5da
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677996"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co to jest usługa Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio to narzędzie do współpracy, obsługiwane metodą „przeciągnij i upuść”, które służy do budowania, testowania i wdrażania rozwiązań z zakresu analizy predykcyjnej na podstawie posiadanych danych. Usługa Machine Learning Studio publikuje modele jako usługi sieci Web, które mogą być łatwo używane w niestandardowych aplikacjach albo narzędziach do analiz biznesowych, takich jak program Excel.

Usługa Machine Learning Studio to połączenie analiz danych, analiz predykcyjnych, zasobów w chmurze oraz samych danych.


## <a name="the-machine-learning-studio-interactive-workspace"></a>Interaktywny obszar roboczy usługi Machine Learning Studio
Aby opracować model analizy predykcyjnej, zazwyczaj używane są dane z jednego lub większej liczby źródeł, przekształcania i analizowania tych danych za pośrednictwem różnych funkcji manipulowania danymi i funkcje statystyczne oraz generowanie zestawu wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wyuczonego, skutecznego modelu.

Usługa **Azure Machine Learning Studio** zapewnia interaktywny, wizualny obszar roboczy, który pozwala na budowanie, testowanie i wykonywanie kolejnych iteracji modelu analizy predykcyjnej. Użytkownik przeciąga i upuszcza ***zestawy danych*** oraz ***moduły*** analityczne na interaktywną ***kanwę***, łącząc je ze sobą w celu przygotowania eksperymentu, który uruchamia w usłudze Machine Learning Studio. W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy nadejdzie odpowiedni moment, można przekształcić ***eksperyment uczenia*** w ***eksperyment predykcyjny***, a następnie ***opublikować*** go jako usługę sieci Web, dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane żadne programowanie — wystarczy tylko wizualne łączenie zestawów danych i modułów w celu utworzenia modelu analizy predykcyjnej.

![Diagram usługi Azure Machine Learning Studio: tworzenie eksperymentów, odczytywanie danych z wielu źródeł, zapisywanie ocenianych danych, zapisywanie modeli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Pobieranie diagramu przeglądowego usługi Machine Learning Studio
Pobierz diagram **Przegląd możliwości usługi Microsoft Azure Machine Learning Studio** i uzyskaj ogólny obraz możliwości usługi Machine Learning Studio. Aby mieć ten diagram zawsze w pobliżu, wydrukuj go w rozmiarze tabloidu (11 cali x 17 cali).

**Pobierz diagram tutaj: [Przegląd możliwości usługi Microsoft Azure Machine Learning Studio](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Przegląd możliwości usługi Microsoft Azure Machine Learning Studio](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Wprowadzenie do usługi Machine Learning Studio
Po pierwszym wprowadzeniu [Machine Learning Studio](https://studio.azureml.net)zostanie wyświetlona strona **główna** . W tym miejscu możesz wyświetlić dokumentację, klipy wideo i seminaria internetowe oraz znaleźć inne cenne zasoby.

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
Kliknięcie pozycji **Galeria** powoduje przejście do witryny **[Galeria sztucznej inteligencji platformy Azure](https://gallery.azure.ai/)** . Galeria jest miejscem, w którym społeczność programistów i analityków danych udostępnia rozwiązania utworzone przy użyciu składników pakietu Cortana Intelligence.

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

Podczas tworzenia eksperymentu możesz wybrać z listy zestawów danych dostępnych po lewej stronie kanwy.

Listę przykładowych zestawów danych dostępnych w usłudze Azure Machine Learning Studio zawiera temat [Use the sample data sets in Azure Machine Learning Studio](use-sample-datasets.md) (Korzystanie z przykładowych zestawów danych w usłudze Azure Machine Learning Studio).

### <a name="modules"></a>Moduły
Moduł jest algorytmem, który można wykonać na danych. Usługa Machine Learning Studio zawiera szereg modułów, które pełnią różne funkcje — począwszy od transferu danych przychodzących, aż po uczenie, ocenę i walidację. Oto przykładowe dołączone moduły:

* [Konwertuj na ARFF][convert-to-arff] — konwertuje serializowany zestaw danych .NET na format pliku relacji atrybutu (ARFF).
* [Obliczanie statystyk podstawowych][elementary-statistics] — oblicza statystyki podstawowe, takie jak średnia, odchylenie standardowe itp.
* [Regresja liniowa][linear-regression] — tworzy model regresji liniowej na podstawie gradientu online.
* [Model oceny — ocenia][score-model] model klasyfikacji lub regresji.

Podczas tworzenia eksperymentu można wybierać moduły z listy dostępnej po lewej stronie kanwy.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie parametry tego modułu są wyświetlane w okienku **Properties** (Właściwości) po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

Aby uzyskać pomoc w nawigowaniu po dużej bibliotece algorytmów uczenia maszynowego, zobacz [Jak wybierać algorytmy w usłudze Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Wdrażanie usługi sieci Web analizy predykcyjnej
Gdy model analizy predykcyjnej jest gotowy, można go wdrożyć jako usługę sieci Web bezpośrednio z usługi Machine Learning Studio. Dodatkowe szczegóły dotyczące tego procesu zawiera temat [Wdrażanie usługi sieci Web Azure Machine Learning](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Czym różni się usługa Machine Learning Studio od usługi Azure Machine Learning Service?

[Usługa Azure Machine Learning](../service/overview-what-is-azure-ml.md) udostępnia zestawy SDK **i** interfejs wizualny (wersja zapoznawcza), aby szybko przygotowywać dane, uczeniować i wdrażać modele uczenia maszynowego. Ten interfejs wizualny (wersja zapoznawcza) zapewnia podobne środowisko przeciągania i upuszczania do programu Studio. Jednak, w przeciwieństwie do własnościowej platformy obliczeniowej programu Studio, interfejs wizualny używa własnych zasobów obliczeniowych i jest w pełni zintegrowany z usługą Azure Machine Learning.

Oto krótkie porównanie.

|| Machine Learning Studio | Usługa Azure Machine Learning:<br/>Interfejs wizualny|
|---| --- | --- |
|| Ogólnie dostępna (GA) | W wersji zapoznawczej|
|Moduły dla interfejsu| Ilość | Początkowy zestaw popularnych modułów|
|Szkoleniowe cele obliczeniowe| Własnościowy obiekt docelowy obliczeń, tylko obsługa procesora CPU| Obsługuje Azure Machine Learning obliczeń, procesora GPU i procesora CPU.<br/>(Inne obliczenia obsługiwane w zestawie SDK)|
|Cele obliczeń wdrożenia| Własny format usługi sieci Web, niedostosowywalny | Opcje zabezpieczeń przedsiębiorstwa & usługi Azure Kubernetes. <br/>([Inne obliczenia](../service/how-to-deploy-and-where.md) obsługiwane w zestawie SDK) |
|Automatyczne szkolenie modelu i dostrajanie parametrów | Nie | Jeszcze nie w interfejsie wizualizacji. <br/> (Obsługiwane w zestawach SDK i Azure Portal). | 

Wypróbuj interfejs wizualny (wersja zapoznawcza [) z samouczkiem: Przewidywanie ceny samochodów za pomocą interfejsu wizualnego](../service/ui-tutorial-automobile-price-train-score.md)

> [!NOTE]
> Nie można wdrożyć modeli utworzonych w programie Studio ani nimi zarządzać za pomocą usługi Azure Machine Learning. Jednak modele utworzone i wdrożone w interfejsie wizualnym usługi mogą być zarządzane za pomocą obszaru roboczego usługi Azure Machine Learning.

## <a name="free-trial"></a>Bezpłatna wersja próbna

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Kolejne kroki
Podstaw analizy predykcyjnej i uczenia maszynowego możesz się nauczyć, korzystając z [przewodnika Szybki start krok po kroku](create-experiment.md), a także [rozwijając przykłady](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
