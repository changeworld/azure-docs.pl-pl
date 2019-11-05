---
title: Co to jest ML Studio (wersja klasyczna)
titleSuffix: Azure
description: Azure Machine Learning Studio (klasyczny) to narzędzie do przeciągania i upuszczania umożliwiające szybkie tworzenie modeli z gotowej do użycia biblioteki algorytmów i modułów.
services: machine-learning
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: 327bc40e5989cc3bca148f415113de32b19a7022
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500127"
---
# <a name="what-is-machine-learning-studio-classic"></a>Co to jest Machine Learning Studio (klasyczny)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klasyczny) to narzędzie do współpracy typu "przeciągnij i upuść", które służy do kompilowania, testowania i wdrażania rozwiązań analizy predykcyjnej na danych.  Klasyczna wersja Machine Learning Studio publikuje modele jako usługi sieci Web, które mogą być łatwo wykorzystane przez aplikacje niestandardowe lub narzędzia analizy biznesowej, takie jak program Excel.

Machine Learning Studio (klasyczny) to miejsce, w którym nauka danych, Analiza predykcyjna, zasoby w chmurze i Twoje dane są spełnione.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Interaktywny obszar roboczy Machine Learning Studio (klasyczny)
Aby opracować model analizy predykcyjnej, zazwyczaj używane są dane z jednego lub większej liczby źródeł, przekształcania i analizowania tych danych za pośrednictwem różnych funkcji manipulowania danymi i funkcje statystyczne oraz generowanie zestawu wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wyuczonego, skutecznego modelu.

Klasyczna wersja Azure Machine Learning Studio zapewnia interaktywny, wizualny obszar roboczy, dzięki czemu można łatwo kompilować, testować i iterować model analizy predykcyjnej. Możesz przeciągać i upuszczać ***zestawy danych*** i ***moduły*** analizy na interaktywną kanwę, łącząc je ze sobą w celu utworzenia ***eksperymentu***, który jest uruchamiany w Machine Learning Studio (klasyczny). W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy nadejdzie odpowiedni moment, można przekształcić ***eksperyment uczenia*** w ***eksperyment predykcyjny***, a następnie ***opublikować*** go jako usługę sieci Web, dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane programowanie, wizualne łączenie zestawów danych i modułów w celu utworzenia modelu analizy predykcyjnej.

![Diagram Azure Machine Learning Studio: Tworzenie eksperymentów, odczytywanie danych z wielu źródeł, zapisywanie danych oceny i pisanie modeli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Jak Machine Learning Studio (klasyczny) różni się od Azure Machine Learning?

[Azure Machine Learning](../service/overview-what-is-azure-ml.md) udostępnia zestawy SDK **i** projektanta Azure Machine Learning (wersja zapoznawcza), aby szybko przygotowywać dane, uczeniować i wdrażać modele uczenia maszynowego. Projektant oferuje podobne środowisko przeciągania i upuszczania do programu Studio (klasyczne). Jednak, w przeciwieństwie do własnościowej platformy obliczeniowej programu Studio (klasycznej), projektant używa własnych zasobów obliczeniowych i jest w pełni zintegrowany z Azure Machine Learning.

Oto krótkie porównanie:

|| Machine Learning Studio (klasyczny) | Azure Machine Learning |
|---| --- | --- |
| Przeciąganie i upuszczanie interfejsu | Tak | Tak — [Azure Machine Learning Designer (wersja zapoznawcza)](../service/concept-designer.md) |
| Doświadczenia | Skalowalne (limit danych szkolenia 10 GB) | Skalowanie za pomocą elementu docelowego obliczeń |
| Moduły dla interfejsu metodą "przeciągnij i upuść" | Ilość | Początkowy zestaw popularnych [modułów](../algorithm-module-reference/module-reference.md)|
|Szkoleniowe cele obliczeniowe| Własnościowy obiekt docelowy obliczeń, tylko obsługa procesora CPU| Azure Machine Learning obsługuje maszyny wirtualne z maszynami wirtualnymi obliczeniowymi (procesor GPU lub procesor CPU) i notesy.<br/>([Inne obliczenia obsługiwane w zestawie SDK](../service/concept-compute-target.md#train))|
|Inferencing cele obliczeń| Własny format usługi sieci Web, niedostosowywalny |  Azure Kubernetes Service i AML COMPUTE <br/>([Inne obliczenia obsługiwane w zestawie SDK](../service/how-to-deploy-and-where.md)) |
| Potok ML | Brak obsługi | Obsługiwane [potoki](../service/concept-ml-pipelines.md) |
| MLOps | Podstawowe Zarządzanie modelami i wdrażanie | Konfigurowalne i śledzenie wersji i śledzenia potoków wdrożenia |
| Format modelu | Format własnościowy, tylko Studio | Format standardowy w zależności od typu zadania szkoleniowego |
|Automatyczne szkolenie modelu i dostrajanie parametrów | Nie | Jeszcze nie w projektancie <br/> ([Obsługiwane w zestawie SDK i stronie docelowej obszaru roboczego](../service/concept-automated-ml.md)) | 

Wypróbuj projektanta przy użyciu [samouczka: przewidywanie ceny samochodów dla urządzeń przenośnych za pomocą projektanta](../service/tutorial-designer-automobile-price-train-score.md)

> [!NOTE]
> Nie można wdrożyć modeli utworzonych w programie Studio (Classic) ani nimi zarządzać za pomocą Azure Machine Learning. Jednak modele utworzone i wdrożone w projektancie mogą być zarządzane za pomocą obszaru roboczego Azure Machine Learning.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Pobierz diagram omówienia Machine Learning Studio (klasyczny)
Pobierz diagram **omówienia możliwości Microsoft Azure Machine Learning Studio (klasyczny)** i uzyskaj ogólny widok możliwości Machine Learning Studio (klasyczny). Aby mieć ten diagram zawsze w pobliżu, wydrukuj go w rozmiarze tabloidu (11 cali x 17 cali).

**Pobierz diagram tutaj: [Microsoft Azure Machine Learning Studio (klasyczny) przegląd](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) ** możliwości
![Microsoft Azure Machine Learning Studio (klasyczny) — Omówienie](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Składniki eksperymentu programu Studio (klasycznego)
Eksperyment składa się z zestawów danych, które dostarczają dane do modułów analitycznych łączonych w celu utworzenia modelu analizy predykcyjnej. Prawidłowy eksperyment ma następujące cechy:

* Obejmuje co najmniej jeden zestaw danych i jeden moduł
* Zestawy danych mogą być połączone tylko z modułami
* Moduły mogą być połączone z zestawami danych lub innymi modułami
* Wszystkie porty wejściowe modułów muszą mieć jakieś połączenie z przepływem danych
* Konieczne jest ustawienie wszystkich wymaganych parametrów dla każdego modułu

Eksperymenty można tworzyć od podstaw albo przy użyciu przykładowego eksperymentu jako szablonu. Aby uzyskać więcej informacji, zobacz [Kopiowanie przykładowych eksperymentów w celu tworzenia nowych eksperymentów uczenia maszynowego](sample-experiments.md).

Aby zapoznać się z przykładem tworzenia eksperymentu, zobacz [Tworzenie prostego eksperymentu w Azure Machine Learning Studio (klasyczny)](create-experiment.md).

Aby uzyskać pełniejszy Przewodnik tworzenia rozwiązania do analizy predykcyjnej, zobacz Tworzenie [rozwiązania predykcyjnego za pomocą Azure Machine Learning Studio (klasyczny)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Zestawy danych
Zestaw danych to dane przekazane do Machine Learning Studio (klasyczne), dzięki czemu mogą być używane w procesie modelowania. Wiele przykładowych zestawów danych jest dołączonych do Machine Learning Studio (klasyczny), dzięki którym można eksperymentować z programem, a w miarę potrzeb można przekazać więcej zestawów danych. Oto przykładowe zestawy danych dostępne w Studio:

* **MPG data for various automobiles** (Dane MPG dotyczące różnych samochodów) — wartości przebiegu w milach na galon paliwa (MPG) dla samochodów zidentyfikowanych na podstawie liczby cylindrów, mocy itp.
* **Breast cancer data** (Dane dot. raka piersi) — dane z diagnoz raka piersi.
* **Forest fires data** (Dane dot. pożarów lasów) — rozmiary pożarów lasów w północno-wschodniej Portugalii.

Podczas tworzenia eksperymentu możesz wybrać z listy zestawów danych dostępnych po lewej stronie kanwy.

Aby zapoznać się z listą przykładowych zestawów danych zawartych w Machine Learning Studio (klasyczny), zobacz [Korzystanie z przykładowych zestawów dane w Azure Machine Learning Studio (klasyczne)](use-sample-datasets.md).

### <a name="modules"></a>Moduły
Moduł jest algorytmem, który można wykonać na danych.  Klasyczna wersja Machine Learning Studio ma wiele modułów niż funkcje transferu danych przychodzących do szkoleń, oceniania i procesów walidacji. Oto przykładowe dołączone moduły:

* [Konwertuj na ARFF][convert-to-arff] — konwertuje serializowany zestaw danych .NET na format pliku relacji atrybutu (ARFF).
* [Obliczanie statystyk podstawowych][elementary-statistics] — oblicza statystyki podstawowe, takie jak średnia, odchylenie standardowe itp.
* [Regresja liniowa][linear-regression] — tworzy model regresji liniowej na podstawie gradientu online.
* [Model oceny — ocenia][score-model] model klasyfikacji lub regresji.

Podczas tworzenia eksperymentu możesz wybrać z listy modułów dostępnych po lewej stronie kanwy.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie parametry tego modułu są wyświetlane w okienku **Properties** (Właściwości) po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

Aby uzyskać pomoc dotyczącą przechodzenia przez dużą bibliotekę dostępnych algorytmów uczenia maszynowego, zobacz [jak wybrać algorytmy dla Microsoft Azure Machine Learning Studio (klasyczne)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Wdrażanie usługi sieci Web analizy predykcyjnej
Gdy model analizy predykcyjnej jest gotowy, możesz go wdrożyć jako usługę sieci Web bezpośrednio z poziomu Machine Learning Studio (klasyczne). Aby uzyskać więcej informacji na temat tego procesu, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Następne kroki
Podstaw analizy predykcyjnej i uczenia maszynowego możesz się nauczyć, korzystając z [przewodnika Szybki start krok po kroku](create-experiment.md), a także [rozwijając przykłady](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
