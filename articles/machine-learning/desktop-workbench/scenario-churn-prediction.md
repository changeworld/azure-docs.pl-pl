---
title: Przewidywanie zmienności klientów za pomocą usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak przeprowadzić analizy zmienności przy użyciu aplikacji Azure ML Workbench.
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
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 7c7b50098cfd1bcac534156dd905b37affab80bd
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648221"
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Przewidywanie zmienności klientów za pomocą usługi Azure Machine Learning

Średnio utrzymanie obecnych klientów jest pięciokrotnie tańsze niż rekrutacji nowe. W rezultacie kierownicy ds. marketingu często stawia próbują oszacować prawdopodobieństwo odpływu klientów i znajdowania odpowiednie działania, aby zminimalizować współczynnik zmian danych.

Celem tego rozwiązania jest aby zademonstrować analizy predykcyjnej zmian przy użyciu usługi Azure Machine Learning Workbench. To rozwiązanie zapewnia łatwy w użyciu szablon do tworzenia potoków danych prognostycznych zmian dla sprzedawców detalicznych. Szablon może być używany z różnych zestawów danych i różnych definicjach zmian. Przykład praktyczne ma na celu:

1. Informacje o aplikacji Azure Machine Learning Workbench w narzędzia przygotowywania danych do czyszczenia i pozyskiwania danych relacji klientów w celach analizy zmienności.

2. Przekształcenie funkcji obsługi hałas danych heterogenicznych.

3. Integracja bibliotek innych firm (takie jak `scikit-learn` i `azureml`) do tworzenia Bayesowskie i oparta na drzewie klasyfikatorów do prognozowania zmian.

4. Wdrożenie.

## <a name="link-of-the-gallery-github-repository"></a>Link galerii repozytorium GitHub
Oto link do publicznego repozytorium GitHub, w którym znajduje się cały kod:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Omówienie przypadków użycia
Firmy potrzebują efektywnej strategii zarządzania utratą klienta. Zmienności klientów dotyczy klientów Zatrzymywanie korzystania z usługi, przełączenie na usługę konkurencyjnej, przełączanie do środowiska niższego rzędu w usłudze lub zmniejszając z usługi.

W tym przypadku użycia przyjrzymy się dane z francuskiej firmy telekomunikacyjnej pomarańczowy do identyfikacji klientów, którzy mogą postępu dokonanego w najbliższym, aby ulepszyć usługi i zorganizować niestandardowe kampanie, pomagające w utrzymaniu klientów.

Konkurencyjnym rynku stają w obliczu telekomunikacyjnymi innych firm. Wiele operatorów traci przychody od klientów abonamentowych z powodu do zmian danych. Dlatego możliwość dokładnie sprawdzić zmienności klientów może być przewaga.

Oto niektóre czynniki przyczyniające się do utraty klientów telekomunikacyjnymi:

* Przerwy w świadczeniu usług częste postrzegany
* Niska skończmy usługi w internetowym/stacjonarnym
* Oferty od innych konkurencyjnych operatorów (lepsza plan taryfowy, itp.).

W tym rozwiązaniu użyto konkretny przykład tworzenia postęp dokonany w modelu predykcyjnego klienta telekomunikacyjnymi innych firm.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne)

* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [przewodnika Szybki start dotyczącego instalacji](../service/quickstart-installation.md) zainstalować ten program i utworzyć obszar roboczy

* Operacjonalizacji najlepiej w przypadku aparat platformy Docker zainstalowany i uruchomiony lokalnie. W przeciwnym razie użyj opcji klastra, ale należy pamiętać, że uruchamianie usługi Azure Container Service (ACS) może być kosztowne.

* To rozwiązanie zakłada, że działasz Azure Machine Learning Workbench w systemie Windows 10 za pomocą aparat platformy Docker zainstalowany lokalnie. Jeśli korzystasz z systemem macOS instrukcji są w większości takie same.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwieranie usługi Azure Machine Learning Workbench
2.  Na **projektów** kliknij **+** zalogować się i wybrać **nowy projekt**
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz "Przewidywanie zmienności klientów" i wybierz szablon
5.  Kliknij przycisk **Utwórz**

## <a name="data-description"></a>Opis danych

Zestaw danych używanych w tym rozwiązaniu jest na tle konkurencji SIDKDD 2009 r. Jest on nazywany `CATelcoCustomerChurnTrainingSample.csv` i znajduje się w [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) folderu. Zestaw danych składa się z danymi heterogenicznymi hałas (Zmienne liczbowe/podzielonych na kategorie) od firmy telekomunikacyjnej francuski pomarańczowy i są anonimowe.

Zmienne przechwytywania informacji demograficznych klientów, statystyki rozmów (np. Średni czas trwania wywołania, liczba niepowodzeń wywołań itp.), informacji, statystyk skargi kontraktu. Postęp dokonany w zmiennej jest plikiem binarnym (0 - nie postęp dokonany w, 1 — współczynnika zmian).

## <a name="scenario-structure"></a>Struktura scenariusza

Struktura folderów są rozmieszczone w następujący sposób:

__dane__: zawiera zestaw danych używanych w tym rozwiązaniu  

__Dokumentacja__: zawiera wszystkie warsztaty

Kolejność praktyczne laboratoria do przeprowadzania rozwiązanie jest w następujący sposób:
1. Przygotowywania danych: Jest główny plik związane z przygotowywania danych w folderze danych `CATelcoCustomerChurnTrainingSample.csv`
2. Modelowanie i oceny: główny plik odnoszących się do modelowania i oceny w folderze głównym znajduje się `CATelcoCustomerChurnModeling.py`
3. Modelowanie i ocena bez .dprep: główny plik dla tego zadania w folderze głównym znajduje się `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operacjonalizacja: Główne pliki występujące są modelu (`model.pkl`) i `churn_schema_gen.py`

| Zamówienie| Nazwa pliku | Pliki pokrewne |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | "data/CATelcoCustomerChurnTrainingSample.csv" |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | "CATelcoCustomerChurnModeling.py" |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | "CATelcoCustomerChurnModelingWithoutDprep.py" |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | "model.pkl"<br>"churn_schema_gen.py" |

Wykonaj laboratoria w sposób sekwencyjny opisanych powyżej.

## <a name="conclusion"></a>Podsumowanie
Praktyczne, ten scenariusz pokazano sposób wykonywania przewidywanie zmienności przy użyciu usługi Azure Machine Learning Workbench. Najpierw wykonane czyszczenie do obsługi danych hałas i niejednorodne, następuje przy użyciu narzędzia do przygotowywania danych technicznego opracowywania funkcji danych. Możemy użyć narzędzi do uczenia maszynowego typu open source do tworzenia i oceny model klasyfikacji, następnie używane kontenera docker lokalnej można wdrożyć modelu, dzięki czemu gotowe do produkcji.
