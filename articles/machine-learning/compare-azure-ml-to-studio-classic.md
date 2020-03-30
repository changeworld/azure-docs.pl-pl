---
title: Usługa Azure Machine Learning vs. Machine Learning Studio (klasyczna)
description: Jaka jest różnica między usługą Azure Machine Learning i Machine Learning Studio (klasyczny)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371836"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Usługa Azure Machine Learning vs Machine Learning Studio (klasyczna)

W tym artykule dowiesz się różnicy między usługi Azure Machine Learning i Machine Learning Studio (klasyczny). 

Usługa Azure Machine Learning udostępnia zestawy SDK języka Python i R **oraz** projektanta "przeciągania i upuszczania" do tworzenia i wdrażania modeli uczenia maszynowego. Studio (klasyczny) oferuje tylko samodzielne doświadczenie przeciągania i upuszczania.

Zalecamy, aby nowi użytkownicy wybrali usługę Azure Machine Learning dla najszerszej gamy najnowocześniejszych narzędzi uczenia maszynowego.

## <a name="quick-comparison"></a>Szybkie porównanie

W poniższej tabeli podsumowano niektóre kluczowe różnice między usługą Azure Machine Learning i Studio (klasyczne):

| | Usługa Machine Learning Studio (klasyczna) | Azure Machine Learning |
|---| --- | --- |
| Interfejs przeciągania i upuszczania | Obsługiwane | Obsługiwane — [projektant usługi Azure Machine Learning (wersja zapoznawcza)](concept-designer.md) | 
| Experiment | Skalowalne (limit danych szkoleniowych 10 GB) | Skaluj z celem obliczeniowym |
| Cele obliczeń szkoleniowych | Zastrzeżony cel obliczeniowy, tylko obsługa procesora | Szeroka gama konfigurowalnych [celów obliczeniowych szkoleniowych.](concept-compute-target.md#train) Obejmuje obsługę procesorów graficznych i procesorów | 
| Obiekty docelowe obliczeń wdrażania | Zastrzeżony format usługi internetowej, nie można dostosowywać | Szeroka gama konfigurowalnych [celów obliczeniowych wdrażania](concept-compute-target.md#deploy). Obejmuje obsługę procesorów graficznych i procesorów |
| Rurociąg ML | Nieobsługiwane | Tworzenie [elastycznych, modułowych rurociągów](concept-ml-pipelines.md) w celu automatyzacji przepływów pracy |
| MlOps ( mlops ) | Podstawowe zarządzanie modelami i wdrażanie | Przechowywanie wersji encji (model, dane, przepływy pracy), automatyzacja przepływu pracy, integracja z narzędziami CICD [i inne](concept-model-management-and-deployment.md) |
| Format modelu | Zastrzeżony format, Studio (tylko klasyczny) | Wiele obsługiwanych formatów w zależności od typu zadania szkoleniowego |
| Zautomatyzowane szkolenie modeli i strojenie hiperparametryczne |  Nieobsługiwane | [Obsługiwane w obszarze roboczym SDK i wizualnym](concept-automated-ml.md) | 
| Wykrywanie dryfu danych | Nieobsługiwane | [Obsługiwane w SDK i wizualnym obszarze roboczym](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrowanie z machine learning studio (klasyczny)

Obecnie nie ma możliwości migracji zasobów studio (klasyczne) do projektanta usługi Azure Machine Learning (wersja zapoznawcza). Obecni użytkownicy studio (klasyczni) mogą nadal korzystać ze swoich zasobów uczenia maszynowego. Jednak zachęcamy wszystkich użytkowników do rozważenia korzystania z projektanta, który zapewnia znane środowisko przeciągania i upuszczania z ulepszonym przepływem pracy **oraz** skalowalnością, kontrolą wersji i zabezpieczeniami przedsiębiorstwa.

## <a name="get-started-with-azure-machine-learning"></a>Wprowadzenie do usługi Azure Machine Learning

Poniższe zasoby mogą pomóc w rozpoczęciu pracy z usługą Azure Machine Learning. 

- Przeczytaj [omówienie usługi Azure Machine Learning](overview-what-is-azure-ml.md).

- Utwórz [swój pierwszy eksperyment z zestawem SDK języka Python](tutorial-1st-experiment-sdk-setup.md).

- [Utwórz swój pierwszy potok projektanta](tutorial-designer-automobile-price-train-score.md) do przewidywania cen automatycznych.

![Przykład projektanta usługi Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Następne kroki

Oprócz funkcji przeciągania i upuszczania w projektancie usługa Azure Machine Learning ma inne dostępne narzędzia:  
  + [Szkolenie & wdrażania modeli modułów ML za pomocą notesów języka Python](tutorial-1st-experiment-sdk-setup.md)
  + [Użyj R Markdown, aby szkolić & wdrażania modeli ml](tutorial-1st-r-experiment.md) 
  + [Użyj automatycznego uczenia maszynowego, aby szkolić & wdrażać modele ml](tutorial-first-experiment-automated-ml.md)  
  + [Szkolenie i wdrażanie modelu za pomocą interfejsu wiersza polecenia uczenia maszynowego](tutorial-train-deploy-model-cli.md)

