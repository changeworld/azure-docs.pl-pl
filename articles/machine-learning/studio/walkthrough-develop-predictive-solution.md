---
title: Rozwiązanie predykcyjne do oceny ryzyka kredytowego — w usłudze Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Szczegółowy przewodnik pokazujący sposób tworzenia rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning Studio.
keywords: ryzyko kredytowe, rozwiązanie analizy predykcyjnej, ocena ryzyka
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: a5dd8432ca79cca8de37f5d64027e0b9c498f3b9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311946"
---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning-studio"></a>Wskazówki: Tworzenie rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning Studio

W tym przewodniku przedstawiono szczegółowo proces opracowywania rozwiązania do analizy predykcyjnej w usłudze Machine Learning Studio. Opracowaliśmy prosty model w usłudze Machine Learning Studio, a następnie wdrożyliśmy go w postaci usługi sieci Web Azure Machine Learning, w której model może tworzyć prognozy przy użyciu nowych danych. 

W tym przewodniku przyjęto założenie, że co najmniej raz użyto wcześniej usługi Machine Learning Studio oraz że znasz niektóre pojęcia związane z uczeniem maszynowym. Nie zakładamy jednak, że jesteś ekspertem w którejkolwiek z tych dziedzin.

Jeśli nigdy wcześniej nie używano usługi **Azure Machine Learning Studio**, możesz najpierw skorzystać z samouczka [Tworzenie pierwszego eksperymentu dotyczącego przetwarzania danych w usłudze Azure Machine Learning Studio](create-experiment.md). Ten samouczek przedstawia pracę z usługą Machine Learning Studio po raz pierwszy. Podstawowe informacje przedstawione w tym samouczku obejmują przeciąganie modułów i upuszczanie ich w obszarze eksperymentu, łączenie modułów ze sobą, uruchamianie eksperymentu oraz przeglądanie wyników. Inne narzędzie, które może być pomocne przy rozpoczynaniu pracy, to diagram zawierający omówienie możliwości usługi Machine Learning Studio. Możesz go pobrać i wydrukować tutaj: [Diagram przeglądowy możliwości usługi Azure Machine Learning Studio](studio-overview-diagram.md).
 
Jeśli uczenie maszynowe to dla Ciebie nowość, możesz skorzystać z serii pomocnych filmów wideo. Jest ona zatytułowana [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) (Analiza danych dla początkujących) i oferuje znakomite wprowadzenie do uczenia maszynowego przedstawione przy użyciu codziennego języka i pojęć.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>Problem

Załóżmy, że chcesz przewidzieć ryzyko kredytowe osoby na podstawie informacji przekazanych we wniosku kredytowym.  

Ocenia ryzyka kredytowego to złożony problem, ale ułatwimy ją nieco dzięki informacjom podanym w tym przewodniku. Użyjemy jej jako przykładu sposobu tworzenia rozwiązania do analizy predykcyjnej przy użyciu usługi Microsoft Azure Machine Learning. W tym celu skorzystamy z usługi Azure Machine Learning Studio i usługi sieci Web Machine Learning.  

## <a name="the-solution"></a>Rozwiązanie

Pracę z tym szczegółowym przewodnikiem rozpoczniemy od publicznie dostępnych danych ryzyka kredytowego oraz utworzenia i uczenia modelu predykcyjnego na podstawie tych danych. Następnie wdrożymy model jako usługę sieci Web, aby inne osoby mogły jej używać do oceny ryzyka kredytowego.

Aby utworzyć to rozwiązanie do oceny ryzyka kredytowego, wykonamy następujące kroki:  

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. [Tworzenie eksperymentu](walkthrough-3-create-new-experiment.md)
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. [Uzyskiwanie dostępu do usługi sieci Web](walkthrough-6-access-web-service.md)

> [!TIP] 
> Funkcjonalną kopię eksperymentu opracowywanego w tym przewodniku można znaleźć w witrynie [Galeria sztucznej inteligencji platformy Azure](https://gallery.cortanaintelligence.com). Otwórz stronę **[Walkthrough — Credit risk prediction](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)** (Przewodnik — przewidywanie ryzyka kredytowego) i kliknij przycisk **Open in Studio** (Otwórz w usłudze Studio), aby pobrać kopię eksperymentu do obszaru roboczego usługi Machine Learning Studio.
> 
> Ten przewodnik jest oparty na uproszczonej wersji przykładowego eksperymentu [Binary Classification: Credit risk prediction](https://go.microsoft.com/fwlink/?LinkID=525270) (Klasyfikacja binarna: przewidywanie ryzyka kredytowego) dostępnego również w witrynie [Gallery](http://gallery.cortanaintelligence.com/).
