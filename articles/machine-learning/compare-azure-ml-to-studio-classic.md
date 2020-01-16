---
title: Azure Machine Learning a Machine Learning Studio (wersja klasyczna)
description: Jak Azure Machine Learning różni się od Machine Learning Studio (klasyczny)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974135"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Różnice Azure Machine Learning od Machine Learning Studio (klasyczne)

W tym artykule porównano funkcje, możliwości i interfejs Azure Machine Learning do Machine Learning Studio (klasyczny). 

## <a name="about-machine-learning-studio-classic"></a>Informacje o Machine Learning Studio (klasyczne)
[Machine Learning Studio (klasyczny)](studio/what-is-ml-studio.md) to współpracująca, przeciągnij i upuść obszar roboczy wizualizacji, w którym można kompilować, testować i wdrażać rozwiązania do uczenia maszynowego bez konieczności pisania kodu. Używa wstępnie skompilowanych i wstępnie skonfigurowanych algorytmów uczenia maszynowego oraz modułów obsługi danych, a także własnościowej platformy obliczeniowej.

## <a name="about-azure-machine-learning"></a>Informacje o usłudze Azure Machine Learning

Tymczasem [Azure Machine Learning](overview-what-is-azure-ml.md) udostępnia interfejs sieci Web o nazwie projektant (wersja zapoznawcza) **oraz** kilka zestawów SDK i interfejsu wiersza polecenia, aby szybko przygotowywać dane, uczeniować i wdrażać modele uczenia maszynowego. Dzięki Azure Machine Learning skalowaniu możesz korzystać z wielu platform, zaawansowanych funkcji, takich jak automatyczne Uczenie maszynowe i obsługa potoku.

Program Azure Machine Learning Designer oferuje podobne środowisko przeciągania i upuszczania do programu Studio (klasyczne). Jednak, w przeciwieństwie do własnościowej platformy obliczeniowej programu Studio (klasycznej), projektant używa własnych zasobów obliczeniowych, jest skalowalny i jest w pełni zintegrowany z Azure Machine Learning.  

> [!TIP]
> Klienci korzystający obecnie z usługi lub oceniają Machine Learning Studio (klasyczną) mogą wypróbować program [Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer) (wersja zapoznawcza), który zapewnia przeciąganie i upuszczanie __modułów ml oraz__ skalowalność, kontrolę wersji i zabezpieczenia przedsiębiorstwa.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Porównanie: Azure Machine Learning a Machine Learning Studio (klasyczne)

Oto krótkie porównanie.

||  Projektant Azure Machine Learning|Studio (klasyczna) |
|---| --- | --- |
||Projektant jest w wersji zapoznawczej, Azure Machine Learning jest GA|Ogólnie dostępna (GA) | 
|Interfejs „przeciągnij i upuść”| Tak | Tak|
|Eksperyment| Skalowanie za pomocą elementu docelowego obliczeń|Skala (limit danych szkoleniowych 10 GB) | 
|Moduły dla interfejsu| [Wiele popularnych modułów](algorithm-module-reference/module-reference.md) | Ilość |
|Szkoleniowe cele obliczeniowe| AML COMPUTE (procesor GPU/procesor CPU)|Własnościowy obiekt docelowy obliczeń, tylko procesor CPU|
|Inferencing cele obliczeń| Usługa Azure Kubernetes na potrzeby wnioskowania w czasie rzeczywistym <br/>AML obliczeniowe na potrzeby wnioskowania partii|Własny format usługi sieci Web, niedostosowywalny | 
|Potok ML| Tworzenie potoku <br/> Opublikowany potok <br/> Punkt końcowy potoku <br/> [Dowiedz się więcej o potoku ML](concept-ml-pipelines.md)|Brak obsługi | 
|Operacje na ML| Konfigurowalne wdrożenia, model i wersja potoku|Podstawowe Zarządzanie modelami i wdrażanie | 
|Model| Standardowy format, różne zależy od zadania szkoleniowego|Własny, nieprzenośny format.| 
|Zautomatyzowane szkolenia modeli|Jeszcze nie w projektancie, ale możliwe za pomocą interfejsu i zestawów SDK.| Nie | 

## <a name="get-started-with-azure-machine-learning"></a>Rozpocznij pracę z usługą Azure Machine Learning

Następujące zasoby mogą pomóc Ci rozpocząć pracę z usługą Azure Machine Learning

- Przeczytaj [omówienie Azure Machine Learning](tutorial-first-experiment-automated-ml.md) 

- [Utwórz swój pierwszy potok projektanta](tutorial-designer-automobile-price-train-score.md) , aby przewidzieć ceny dla samochodów.

![Przykład projektanta Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Następne kroki

Oprócz funkcji przeciągania i upuszczania w projektancie Azure Machine Learning dostępne są inne narzędzia:  
  + [Korzystanie z notesów Python do uczenia & Wdrażanie modeli ML](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie R Markdown do uczenia & wdrażania modeli ML](tutorial-1st-r-experiment.md) 
  + [Używanie automatycznej uczenia maszynowego do uczenia & Wdrażanie modeli ML](tutorial-designer-automobile-price-train-score.md) 
  + [Korzystanie z interfejsu wiersza polecenia Machine Learning w celu uczenia i wdrożenia modelu](tutorial-train-deploy-model-cli.md)

