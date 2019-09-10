---
title: Interfejs wizualny
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej na temat warunków, koncepcji i przepływu pracy tworzących interfejs wizualny (wersja zapoznawcza) dla usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 70141e3ad438ff7ff6d4486e6c27ff4163416e95
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860398"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Co to jest interfejs wizualny usługi Azure Machine Learning Service? 

Interfejs wizualny (wersja zapoznawcza) dla usługi Azure Machine Learning umożliwia Przygotowywanie danych, uczenie, testowanie, wdrażanie, zarządzanie i śledzenie modeli uczenia maszynowego bez konieczności pisania kodu.

Nie ma potrzeby programowania, można wizualnie połączyć [zestawy danych](#dataset) i [moduły](#module) , aby utworzyć model.

Interfejs wizualny używa [obszaru roboczego](concept-workspace.md) usługi Azure Machine Learning, aby:

+ Zapisuj artefakty [eksperymentu](#experiment) w obszarze roboczym.
+ Dostęp do [zestawów danych](#dataset).
+ Użyj [zasobów obliczeniowych](#compute) w obszarze roboczym, aby uruchomić eksperyment. 
+ Zarejestruj [modele](concept-azure-machine-learning-architecture.md#models).
+ [Wdrażaj](#deployment) modele jako usługi sieci Web w zasobach obliczeniowych w obszarze roboczym.

![Przegląd interfejsu wizualizacji](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Przepływ pracy

Interfejs wizualny zapewnia interaktywną, wizualną kanwę umożliwiającą szybkie kompilowanie, testowanie i iterację modelu. 

+ Przeciąganie i upuszczanie [modułów](#module) na kanwę.
+ Połącz moduły ze sobą, aby utworzyć [eksperyment](#experiment).
+ Uruchom eksperyment, korzystając z zasobu COMPUTE w obszarze roboczym usługi Machine Learning.
+ Wykonaj iterację projektu modelu, edytując eksperyment i uruchamiając go ponownie.
+ Gdy wszystko będzie gotowe, przekonwertuj **eksperyment szkoleniowy** na **eksperyment predykcyjny**.
+ [Wdróż](#deployment) eksperyment predykcyjny jako usługę sieci Web, dzięki czemu model może być dostępny dla innych osób.

## <a name="experiment"></a>Eksperyment

Utwórz eksperyment od podstaw lub Użyj istniejącego przykładowego eksperymentu jako szablonu.  Za każdym razem, gdy uruchamiasz eksperyment, artefakty są przechowywane w obszarze roboczym.

Eksperyment składa się z zestawów danych i modułów analitycznych połączonych ze sobą w celu skonstruowania modelu. Prawidłowy eksperyment ma następujące cechy:

* Zestawy danych mogą być połączone tylko z modułami.
* Moduły mogą być połączone z zestawami danych lub innymi modułami.
* Wszystkie porty wejściowe dla modułów muszą mieć połączenie z przepływem danych.
* Należy ustawić wszystkie wymagane parametry dla każdego modułu.


Aby dowiedzieć się, jak rozpocząć pracę z interfejsem wizualnym, zobacz [samouczek: Przewidywanie ceny samochodów za pomocą interfejsu](ui-tutorial-automobile-price-train-score.md)wizualnego.

## <a name="dataset"></a>Zestaw danych

Zestaw danych to dane, które zostały przekazane do interfejsu wizualizacji do użycia w procesie modelowania. Kilka przykładowych zestawów danych jest dostępnych do eksperymentowania z programem i można przekazać im więcej zestawów danych.

## <a name="module"></a>Moduł

Moduł jest algorytmem, który można wykonać na danych. Interfejs wizualny ma wiele modułów niż funkcje transferu danych przychodzących do szkoleń, oceniania i procesów walidacji.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie Parametry modułu są wyświetlane w okienku właściwości po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

![Właściwości modułu](media/ui-concept-visual-interface/properties.png)

Aby uzyskać pomoc dotyczącą przechodzenia przez bibliotekę dostępnych algorytmów uczenia maszynowego, zobacz [algorytm & — informacje o module](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Zasoby obliczeniowe

Użyj zasobów obliczeniowych z obszaru roboczego, aby uruchomić eksperyment lub hostować wdrożone modele jako usługi sieci Web. Są obsługiwane obliczeniowych elementów docelowych:


| Obliczeniowego elementu docelowego | Szkolenia | Wdrożenie |
| ---- |:----:|:----:|
| Obliczeniowe platformy Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Elementy docelowe obliczeń są dołączone do [obszaru roboczego](concept-workspace.md)Machine Learning. Obiektami docelowymi obliczeń można zarządzać w obszarze roboczym w [Azure Portal](https://portal.azure.com) lub na [stronie docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com).

## <a name="deployment"></a>Wdrożenie

Gdy model analizy predykcyjnej będzie gotowy, należy wdrożyć go jako usługę sieci Web bezpośrednio z poziomu interfejsu wizualizacji.

Usługi sieci Web zapewniają interfejs między aplikacją a modelem oceniania. Aplikacja zewnętrzna może komunikować się z modelem oceniania w czasie rzeczywistym. Wywołanie usługi sieci Web zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi sieci Web polega na przekazaniu klucza interfejsu API utworzonego podczas wdrażania tej usługi. Usługa sieci Web jest oparta na architekturze REST, popularnej architektury dla projektów programowania w sieci Web.

Aby dowiedzieć się, jak wdrożyć model, [zobacz Samouczek: Wdróż model uczenia maszynowego za pomocą interfejsu](ui-tutorial-automobile-price-deploy.md)wizualnego.

## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego przy [użyciu samouczka: Przewidywanie ceny samochodów za pomocą interfejsu wizualnego](ui-tutorial-automobile-price-train-score.md)
* Użyj jednego z przykładów i zmodyfikuj, aby wymusić Twoje potrzeby:
    * [Przykład 1 — regresja: Przewidywana cena](ui-sample-regression-predict-automobile-price-basic.md)
    * [Przykład 2 — Regresja: Przewidywanie cen i porównywanie algorytmów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Przykład 3 — Klasyfikacja: Przewidywanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
    * [Przykład 4 — Klasyfikacja: Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Przykład 5 — Klasyfikacja: Przewidywanie zmian, pragnienie i kupowanie](ui-sample-classification-predict-churn.md)
