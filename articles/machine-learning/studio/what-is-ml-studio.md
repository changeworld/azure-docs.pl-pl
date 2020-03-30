---
title: Co to jest ML Studio (klasyczny)
titleSuffix: Azure
description: Usługa Azure Machine Learning Studio (klasyczna) to narzędzie do szybkiego tworzenia modeli z gotowej do użycia biblioteki algorytmów i modułów.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: c1772ceb514e46542129759711f2d45db39abf82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371967"
---
# <a name="what-is-machine-learning-studio-classic"></a>Co to jest Machine Learning Studio (klasyczny)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klasyczny) to narzędzie współpracy, przeciągania i upuszczania, którego można używać do tworzenia, testowania i wdrażania rozwiązań analizy predykcyjnej na danych. Usługa Azure Machine Learning Studio (klasyczna) publikuje modele jako usługi sieci web, które mogą być łatwo używane przez niestandardowe aplikacje lub narzędzia analizy biznesowej, takie jak Excel.

Machine Learning Studio (klasyczny) to miejsce, w którym spotykają się analizy danych, analizy predykcyjne, zasoby w chmurze i dane.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Interaktywny obszar roboczy Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Aby opracować model analizy predykcyjnej, zazwyczaj używasz danych z jednego lub więcej źródeł, przekształcasz i analizujesz te dane za pomocą różnych funkcji manipulowania danymi i funkcji statystycznych oraz generujesz zestaw wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wyuczonego, skutecznego modelu.

Usługa Azure Machine Learning Studio (klasyczna) zapewnia interaktywny, wizualny obszar roboczy, który można łatwo tworzyć, testować i iterować w modelu analizy predykcyjnej. Zestaw danych i ***moduły*** analizy są przeciągane i ***upuszczane*** na interaktywne kanwę, łącząc je ze sobą w celu utworzenia ***eksperymentu,*** który jest uruchamiany w ustępuje w uściślii Machine Learning Studio (klasyczny). W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy nadejdzie odpowiedni moment, można przekształcić ***eksperyment uczenia*** w ***eksperyment predykcyjny***, a następnie opublikować go jako ***usługę sieci Web***, dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane programowanie, wizualnie połączyć zestawy danych i moduły do konstruowania modelu analizy predykcyjnej.

![Diagram usługi Azure Machine Learning Studio (klasyczny): tworzenie eksperymentów, odczytywanie danych dla wielu źródeł, zapisywanie ocenionych danych, zapisywanie modeli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Pobierz diagram przeglądu programu Machine Learning Studio (klasyczny)
Pobierz diagram przeglądowy możliwości usługi **Microsoft Azure Machine Learning Studio (klasyczny)** i uzyskaj widok wysokiego poziomu możliwości usługi Machine Learning Studio (classic). Aby mieć ten diagram zawsze w pobliżu, wydrukuj go w rozmiarze tabloidu (11 cali x 17 cali).

**Pobierz diagram tutaj: Omówienie możliwości usługi [Microsoft Azure Machine Learning Studio (klasyczne) — omówienie](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![możliwości usługi Microsoft Azure Machine Learning Studio (klasyczne)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Składniki eksperymentu studyjnego (klasycznego)
Eksperyment składa się z zestawów danych, które dostarczają dane do modułów analitycznych łączonych w celu utworzenia modelu analizy predykcyjnej. Prawidłowy eksperyment ma następujące cechy:

* Obejmuje co najmniej jeden zestaw danych i jeden moduł
* Zestawy danych mogą być połączone tylko z modułami
* Moduły mogą być połączone z zestawami danych lub innymi modułami
* Wszystkie porty wejściowe modułów muszą mieć jakieś połączenie z przepływem danych
* Konieczne jest ustawienie wszystkich wymaganych parametrów dla każdego modułu

Eksperymenty można tworzyć od podstaw albo przy użyciu przykładowego eksperymentu jako szablonu. Aby uzyskać więcej informacji, zobacz [Kopiowanie przykładowych eksperymentów w celu tworzenia nowych eksperymentów uczenia maszynowego](sample-experiments.md).

Na przykład tworzenia eksperymentu zobacz [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio (klasycznym).](create-experiment.md)

Aby uzyskać pełniejszy przewodnik po utworzeniu rozwiązania do analizy predykcyjnej, zobacz [Opracowywanie rozwiązania predykcyjnego za pomocą usługi Azure Machine Learning Studio (klasyczny)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Zestawy danych
Zestaw danych to dane, które zostały przekazane do usługi Machine Learning Studio (classic), dzięki czemu mogą być używane w procesie modelowania. Wiele przykładowych zestawów danych są dołączone do usługi Machine Learning Studio (classic) do eksperymentowania z i można przekazać więcej zestawów danych, jak ich potrzebujesz. Oto przykładowe zestawy danych dostępne w Studio:

* **MPG data for various automobiles** (Dane MPG dotyczące różnych samochodów) — wartości przebiegu w milach na galon paliwa (MPG) dla samochodów zidentyfikowanych na podstawie liczby cylindrów, mocy itp.
* **Breast cancer data** (Dane dot. raka piersi) — dane z diagnoz raka piersi.
* **Forest fires data** (Dane dot. pożarów lasów) — rozmiary pożarów lasów w północno-wschodniej Portugalii.

Podczas tworzenia eksperymentu można wybierać spośród listy zestawów danych dostępnych po lewej stronie kanwy.

Aby uzyskać listę przykładowych zestawów danych uwzględnionych w usłudze Machine Learning Studio (klasyczny), zobacz [Korzystanie z przykładowych zestawów danych w usłudze Azure Machine Learning Studio (klasycznym).](use-sample-datasets.md)

### <a name="modules"></a>Moduły
Moduł jest algorytmem, który można wykonać na danych. Usługa Azure Machine Learning Studio (klasyczna) ma wiele modułów, począwszy od funkcji transferu danych przychodzących do szkolenia, oceniania i sprawdzania poprawności procesów. Oto przykładowe dołączone moduły:

* [Convert to ARFF][convert-to-arff] (Konwertowanie na ARFF) — konwertuje serializowany zestaw danych .NET na plik formatu Attribute-Relation File Format (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Obliczanie statystyk podstawowych) — oblicza podstawowe statystyki, takie jak średnia, odchylenie standardowe itp.
* [Linear Regression][linear-regression] (Regresja liniowa) — tworzy model regresji liniowej online na podstawie spadku gradientu.
* [Score Model][score-model] (Ocena modelu) — ocenia nauczony model klasyfikacji lub regresji.

Podczas tworzenia eksperymentu można wybierać spośród listy modułów dostępnych po lewej stronie kanwy.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie parametry tego modułu są wyświetlane w okienku **Properties** (Właściwości) po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

Aby uzyskać pomoc w poruszaniu się po dużej dostępnej bibliotece algorytmów uczenia maszynowego, zobacz [Jak wybrać algorytmy dla programu Microsoft Azure Machine Learning Studio (klasyczny).](algorithm-choice.md)

## <a name="deploying-a-predictive-analytics-web-service"></a>Wdrażanie usługi sieci Web analizy predykcyjnej
Gdy model analizy predykcyjnej jest gotowy, można wdrożyć go jako usługę sieci web bezpośrednio z Machine Learning Studio (klasyczny). Aby uzyskać więcej informacji na temat tego procesu, zobacz [Wdrażanie usługi sieci Web usługi Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Następne kroki
Podstaw analizy predykcyjnej i uczenia maszynowego możesz się nauczyć, korzystając z [przewodnika Szybki start krok po kroku](create-experiment.md), a także [rozwijając przykłady](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
