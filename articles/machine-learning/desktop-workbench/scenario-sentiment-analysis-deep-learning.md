---
title: Analiza wskaźniki nastrojów klientów za pomocą bezpośrednich uczenie przy użyciu usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak przeprowadzić analizę wskaźniki nastrojów klientów przy użyciu bezpośrednich learning w usłudze Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2018
ms.author: miprasad
ms.openlocfilehash: c780063074ec1cffbb2a667cb26ab1c86f820167
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833584"
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Analiza wskaźniki nastrojów klientów za pomocą bezpośrednich uczenie przy użyciu usługi Azure Machine Learning

Dobrze znane zadania w obszarze przetwarzania języka naturalnego jest analiza wskaźniki nastrojów klientów. Mając do dyspozycji zestaw teksty, celem jest sprawdzenie wskaźniki nastrojów klientów tego tekstu. Celem tego rozwiązania jest do użycia bezpośrednich Learning do przewidywania wskaźniki nastrojów klientów z recenzje filmów.

Rozwiązanie znajduje się pod adresem https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii

Tego łącza do publicznego repozytorium GitHub:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Omówienie przypadków użycia

Rozwinięcie danych i mnożenie urządzeń przenośnych utworzono wiele możliwości klientom express ich uczucia i stanowisk niczego i wszystkie informacje w dowolnym momencie. Tej opinii lub "wskaźniki nastrojów klientów" często jest generowana za pośrednictwem społecznościowych kanałów w formie recenzji, rozmowy, udziały, lubi tweetów itp. Wskaźniki nastrojów klientów mogą być cenne dla firm, które chcą ulepszania produktów i usług, bardziej świadomych decyzji i lepiej podwyższyć ich marek.

Można pobrać wartości z analizy wskaźniki nastrojów klientów, firmy musi mieć możliwość Moje przeważająca magazynów danych niestrukturalnych społecznościowych dla przydatnych wyników analiz. W tym przykładzie opracowywania modele uczenia bezpośrednich do wykonywania analizy wskaźniki nastrojów klientów przy użyciu AMLWorkbench recenzje filmów

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).

* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [Przewodnik Szybki start instalacji](../service/quickstart-installation.md) Aby zainstalować ten program i utworzyć obszaru roboczego.

* Dla operationalization najlepiej, jeśli masz aparatem platformy Docker zainstalowany i uruchomiony lokalnie. Jeśli nie, można użyć opcji klastra. Jednak uruchomiona usługa kontenera platformy Azure (ACS) może być kosztowne.

* To rozwiązanie zakłada, że korzystasz z usługi Azure Machine Learning Workbench w systemie Windows 10 z aparatem platformy Docker zainstalowane lokalnie. Na macOS instrukcja jest głównie takie same.

## <a name="data-description"></a>Opis elementu danych

Zestaw danych używany dla tego przykładu małym zestawie danych ręcznie, co jest i znajduje się w [folderu danych](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

Pierwsza kolumna zawiera recenzje filmów, a druga kolumna zawiera ich wskaźniki nastrojów klientów (0 - ujemną oraz 1 - dodatni). Zestaw danych jest tylko używane dla celów demonstracyjnych, ale zwykle można uzyskać wyników niezawodne wskaźniki nastrojów klientów, należy dużego zestawu danych. Na przykład [filmu ZAUFANI przegląda problemu klasyfikacji wskaźniki nastrojów klientów](https://keras.io/datasets/#datasets ) z Keras składa się z zestawu danych przeglądów 25 000 filmów z ZAUFANI, którego etykietą wskaźniki nastrojów klientów (dodatnie lub ujemne). To laboratorium jest opisano, jak przeprowadzić analizę wskaźniki nastrojów klientów przy użyciu bezpośrednich uczenia z AMLWorkbench.

## <a name="scenario-structure"></a>Scenariusz — struktura

Struktura folderów znajdują się w następujący sposób:

1. Cały kod powiązany analizy wskaźniki nastrojów klientów przy użyciu AMLWorkbench znajduje się w folderze głównym
2. dane: zawiera zestaw danych używany w rozwiązaniu
3. dokumenty: zawiera wszystkie praktyczne labs

Kolejność Hands-on Labs przeprowadzenie rozwiązania wygląda następująco:

| Kolejność| Nazwa pliku | Powiązane pliki |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | "data/sampleReviews.txt" |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | "SentimentExtraction.py" |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | "Operaionalization" |

## <a name="conclusion"></a>Podsumowanie

Podsumowując to rozwiązanie przedstawiono za pomocą bezpośrednich Learning do analizowania wskaźniki nastrojów klientów z Azure Machine Learning Workbench. Również operacjonalizacji się przy użyciu HDF5 modeli.
