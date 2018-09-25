---
title: Analiza tonacji za pomocą uczenia głębokiego przy użyciu usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak przeprowadzić analizę tonacji przy użyciu uczenia głębokiego przy użyciu aplikacji Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2018
ms.author: miprasad
ROBOTS: NOINDEX
ms.openlocfilehash: e2fbb0b7b0dede198be0e57ffcd2b58a7da7fce7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947775"
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Analiza tonacji za pomocą uczenia głębokiego przy użyciu usługi Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Analiza tonacji jest dobrze znanych zadania w obszarze przetwarzania języka naturalnego. Biorąc pod uwagę zestaw teksty, celem jest określenie tonacji ten tekst. Celem tego rozwiązania jest na potrzeby uczenia głębokiego Prognozowanie tonacji z recenzje filmu.

Rozwiązanie znajduje się w https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii

Kliknij ten link do publicznego repozytorium GitHub:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Omówienie przypadków użycia

Rozłożenie danych i powszechne stosowanie urządzeń mobilnych utworzono wiele możliwości dla klientów do ich uczucia i postawy o wszystkich w dowolnym momencie. Tej opinii lub "opinie" często jest generowany za pośrednictwem kanałów społecznościowych w formie przeglądy, rozmowy dotyczące, udziały, polubień tweetów, itp. Tonacja mogą być cenne dla firm do ulepszania swoich produktów i usług, podejmowanie bardziej świadomych decyzji i lepiej promowanie swoich marek.

Można pobrać wartości z analizy tonacji, firmy musi mieć możliwość analizować duże magazyny dane bez określonej struktury społecznościowych uzyskiwanie przydatnych wyników analiz. W tym przykładzie opracowujemy modeli uczenia głębokiego dla przeprowadzenia analizy tonacji recenzje filmu przy użyciu AMLWorkbench

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).

* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [przewodnika Szybki start dotyczącego instalacji](quickstart-installation.md) zainstalować ten program i utworzyć obszar roboczy.

* Operacjonalizacji najlepiej w przypadku aparat platformy Docker zainstalowany i uruchomiony lokalnie. W przeciwnym razie możesz użyć opcji klastra. Jednak uruchamiania usługi Azure Container Service (ACS) może być kosztowne.

* To rozwiązanie zakłada, że działasz Azure Machine Learning Workbench w systemie Windows 10 za pomocą aparat platformy Docker zainstalowany lokalnie. W systemie macOS instrukcja są w większości takie same.

## <a name="data-description"></a>Opis danych

Zestaw danych używany w tym przykładzie jest małym zestawem danych specjalnie ręcznie i znajduje się w [folderu danych](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

Pierwsza kolumna zawiera recenzje filmów i druga kolumna zawiera ich tonacji (0 - ujemne, jak i 1 - dodatni). Zestaw danych jest jedynie używany dla celów demonstracyjnych, ale zazwyczaj można pobrać wyniki tonacji niezawodne, konieczny jest duży zestaw danych. Na przykład [filmu ZAUFANI przeglądy problemu klasyfikacji opinii](https://keras.io/datasets/#datasets ) z biblioteki Keras składa się z zestawu danych o przeglądach 25 000 filmów z ZAUFANI, którego etykietą tonacji (dodatnia lub ujemna). Zamiarem tego laboratorium ma dowiesz się, jak przeprowadzić analizę tonacji przy użyciu uczenia głębokiego z AMLWorkbench.

## <a name="scenario-structure"></a>Struktura scenariusza

Struktura folderów są rozmieszczone w następujący sposób:

1. Cały kod, które są związane z analiza tonacji za pomocą AMLWorkbench znajduje się w folderze głównym
2. dane: zawiera zestaw danych używanych w tym rozwiązaniu
3. Dokumentacja: zawiera wszystkie warsztaty

Kolejność praktyczne laboratoria do przeprowadzania rozwiązanie jest w następujący sposób:

| Zamówienie| Nazwa pliku | Pliki powiązane |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | "data/sampleReviews.txt" |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | "SentimentExtraction.py" |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | "Operaionalization" |

## <a name="conclusion"></a>Podsumowanie

Podsumowując to rozwiązanie przedstawiono korzystanie z uczenia głębokiego w celu wykonania analiza tonacji przy użyciu usługi Azure Machine Learning Workbench. Również operacjonalizować się przy użyciu modeli HDF5.
