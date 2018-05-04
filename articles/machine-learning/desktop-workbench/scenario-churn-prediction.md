---
title: Prognozowanie churn — klienta przy użyciu usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak przeprowadzić analytics zmian przy użyciu usługi Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 501735e1cf32359a34c01ad1c7990609a2bf4058
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Prognozowanie przenoszenie klienta przy użyciu usługi Azure Machine Learning

Zachowanie istniejących klientów jest średnio pięć razy tańsze niż koszt rekrutacji nowe. W związku z tym marketing przedstawicieli kadry kierowniczej często muszą się próby oszacować prawdopodobieństwo przenoszenie klienta w celu znalezienia odpowiednie działania w celu zminimalizowania szybkość zmian.

Celem tego rozwiązania jest aby zademonstrować analizy predykcyjnej zmian przy użyciu usługi Azure Machine Learning Workbench. To rozwiązanie zapewnia łatwy w użyciu szablonu umożliwiające tworzenie postęp dokonany w potokach danych predykcyjnej dla sprzedawców detalicznych. Szablon może być używany z różnych zestawów danych i różne definicje danych churn. Przykład praktyczne ma na celu:

1. Zrozumienie Azure Machine Learning Workbench narzędzia przygotowywania danych do czyszczenia i pozyskiwania danych relacji klienta do analizy danych churn.

2. Przekształcenie funkcji do obsługi dużo danych heterogenicznych.

3. Integracja bibliotek innych firm (takich jak `scikit-learn` i `azureml`) do opracowywania Estymacja Bayesian i oparta na drzewie klasyfikatory do prognozowania danych churn.

4. Wdrażanie.

## <a name="link-of-the-gallery-github-repository"></a>Łącze repozytorium GitHub galerii
Poniżej znajduje się łącze do publicznego repozytorium GitHub, w którym hostowana jest cały kod:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Omówienie przypadków użycia
Firmy muszą skutecznej strategii zarządzania tworzeniem klienta. Przenoszenie klienta obejmuje klientów Zatrzymywanie korzystania z usługi, przełączanie do usługi konkurencyjnej, przełączanie do środowiska niższego poziomu, usługi lub zmniejszenie interakcji użytkowników z usługi.

W tym przypadku przyjrzymy się dane firmy francuskim telekomunikacyjnych pomarańczowy do identyfikowania klientów, którzy mogą churn — w najbliższym czasie w celu usprawnienia usługi i tworzenie kampanii zasięg niestandardowych, które pomagają zachować klientów.

Firm telekomunikacyjnych stają przed konkurencyjnych rynku. Wiele nośników utratę przychodów postpaid klientów powodu do churn —. Dlatego możliwość dokładnie sprawdzić postęp dokonany klienta może być przewagi konkurencyjnej ogromnych.

Czynniki przyczyniające się do zmian klienta telekomunikacyjnych między innymi:

* Zakłócenia w świadczeniu usług często postrzegana
* Procesy usługi klienta niska w online/detalicznej
* Oferty od innych konkurencyjnych nośniki (lepszego planu rodziny, planu danych itp.).

W tym rozwiązaniu użyjemy konkretny przykład Budowanie modelu przenoszenie predykcyjnej klienta dla firm telekomunikacyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne)

* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [Przewodnik Szybki start instalacji](../service/quickstart-installation.md) Aby zainstalować ten program i utworzyć obszaru roboczego

* Dla operationalization najlepiej, jeśli masz aparatem platformy Docker zainstalowany i uruchomiony lokalnie. Jeśli nie, można użyć opcji klastra, ale należy pamiętać, że uruchomiona usługa kontenera platformy Azure (ACS) może być kosztowne.

* To rozwiązanie zakłada, że korzystasz z usługi Azure Machine Learning Workbench w systemie Windows 10 z aparatem platformy Docker zainstalowane lokalnie. Jeśli używasz macOS instrukcja jest głównie takie same.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablon:
1.  Otwórz Azure Machine Learning Workbench
2.  Na **projekty** kliknij przycisk **+** podpisywania i wybierz **nowy projekt**
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz "Klienta churn — prognozowania" i wybierz szablon
5.  Kliknij przycisk **Utwórz**

## <a name="data-description"></a>Opis elementu danych

Zestaw danych używany w rozwiązaniu jest od SIDKDD 2009 konkurencji. Jest to `CATelcoCustomerChurnTrainingSample.csv` i znajduje się w [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) folderu. Zestaw danych składa się z heterogenicznych danych zakłócenia (zmienne numeryczne/podzielone na kategorie) firmy telekomunikacyjnych francuski pomarańczowy i jest anonimowy.

Zmienne przechwycić informacje demograficzne klienta, statystyki wywołania (takie jak Średni czas trwania wywołania częstość niepowodzeń wywołania, itp.), informacje i dane statystyczne zgodne kontraktu. Postęp dokonany w zmiennej jest plikiem binarnym (0 - nie churn —, 1 — churn —).

## <a name="scenario-structure"></a>Scenariusz — struktura

Struktura folderów znajdują się w następujący sposób:

__dane__: zawiera zestaw danych używany w rozwiązaniu  

__Dokumentacja__: zawiera wszystkie praktyczne labs

Kolejność Hands-on Labs przeprowadzenie rozwiązania wygląda następująco:
1. Przygotowanie danych: Główny plik powiązany Przygotowanie danych w folderze danych jest `CATelcoCustomerChurnTrainingSample.csv`
2. Ocena i modelowania: jest główny plik powiązany modelowania i oceny w folderze głównym `CATelcoCustomerChurnModeling.py`
3. Ocena bez .dprep i modelowania: jest główny plik dla tego zadania w folderze głównym `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operationalization: Główny pliki deloyment są modelu (`model.pkl`) i `churn_schema_gen.py`

| Kolejność| Nazwa pliku | Pliki pokrewne |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | "data/CATelcoCustomerChurnTrainingSample.csv" |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | "CATelcoCustomerChurnModeling.py" |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | "CATelcoCustomerChurnModelingWithoutDprep.py" |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | "model.pkl"<br>"churn_schema_gen.py" |

Wykonaj laboratoriów w sekwencyjnie opisane powyżej.

## <a name="conclusion"></a>Podsumowanie
To przekazuje scenariuszu przedstawiono sposób wykonywania prognozowania zmian przy użyciu usługi Azure Machine Learning Workbench. Najpierw wykonane czyszczenie do obsługi danych zakłócenia i heterogenicznych, następuje engineering funkcji przy użyciu narzędzia przygotowywania danych danych. Firma Microsoft używany typu open source machine learning narzędzia do tworzenia i ocena modelu klasyfikacji, następnie używane kontenera docker lokalnej można wdrożyć modelu, dzięki czemu gotowe do produkcji.
