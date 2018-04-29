---
title: Wykonywania zadań analizy danych - Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak naukowca danych można wykonać projektu analizy danych w trackable, niepoddany kontroli wersji, a także sposób współpracy.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: f0e0c04e48a551db6d7d7cd221a35e267dc0a63f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Wykonanie zadania nauki danych: eksploracji, modelowania i wdrażanie

Zadania nauki typowych danych obejmują Eksploracja danych, modelowania i wdrożenia. W tym artykule przedstawiono sposób użycia **interakcyjne Eksploracja danych, analizy i raportowania (IDEAR)** i **automatycznego modelowania i Raportowanie (AMAR)** narzędzia wykonać kilka typowych zadań nauki danych przykład interakcyjną eksplorację, analizy danych raportowania i tworzenia modelu. Opisano także opcje wdrażania modelu w środowisku produkcyjnym za pomocą różnych platform zestaw narzędzi i dane, takie jak następujące:

- [Azure Machine Learning](../service/index.yml)
- [Programu SQL Server z usługami ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Eksploracja 

Naukowca danych można wykonać eksploracji i raportowania w różnych sposobów: za pomocą bibliotek i pakiety dostępne dla języka Python (matplotlib na przykład) lub z R (ggplot lub struktury, na przykład). Analityków danych, można dostosować takiego kodu do potrzeb Eksploracja danych w określonych scenariuszach. Wymagania dotyczące danych strukturalnych są różne dla danych niestrukturalnych, takich jak tekstu lub obrazów. 

Produkty, takie jak Azure Machine Learning Workbench udostępniają [zaawansowane Przygotowanie danych](../desktop-workbench/tutorial-bikeshare-dataprep.md) danych wrangling i eksploracja, w tym tworzenia funkcji. Użytkownik powinny zdecydować o narzędzia, biblioteki i pakiety, które najlepiej suite ich potrzeb. 

Element dostarczany na końcu tego etapu jest raport Eksploracja danych. Raport powinien dostarczyć dość obszerne danych służący do modelowania i ocenę, czy dane są odpowiednie przejść do kroku modelowania. Narzędzia Team danych nauki procesu (TDSP) z opisem w poniższych sekcjach dla częściowo zautomatyzowanych eksploracji modelowania i raportowania udostępniają Eksploracja danych standardowych i modelowanie raportów. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Eksploracja danych interaktywnych, analizy i raportowania przy użyciu narzędzia IDEAR

Tego opartych na języku znaczników markdown R lub Python na podstawie notesu narzędzia zapewnia elastyczne i interaktywne narzędzia do oceny i Eksploruj zestawów danych. Użytkownicy mogą szybko generować raporty z zestawu danych z minimalnym kodowania. Użytkownicy mogą kliknąć przycisk, aby wyeksportować wyniki eksploracji interaktywne narzędzia do raportu końcowego, które mogą zostać dostarczone do klientów lub umożliwia podjęcie decyzji, na które zmienne, aby uwzględnić w kroku kolejnych modelowania.

W tej chwili narzędzie działa tylko na ramek danych w pamięci. Pozwala określić parametry zestawu danych do eksplorowania na potrzeby pliku yaml programu. Aby uzyskać więcej informacji, zobacz [IDEAR w narzędzia do analizy danych TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modelowanie

Istnieje wiele narzędzi i pakietów w modelach szkolenia w różnych językach. Analityków danych powinien możesz korzystać, który z nich są doświadczenia z, jak długo zagadnienia dotyczące wydajności dotyczące dokładność i opóźnienia są spełnione dla odpowiednich firm Użyj przypadków i scenariusze produkcji.

Następna sekcja przedstawia sposób użycia narzędzia oparte na R TDSP dla częściowo zautomatyzowanych modelowania. To narzędzie AMAR może służyć do generowania modeli linii bazowej szybko również parametry, które muszą być dopasowane zapewnienie lepszego wykonywania modelu.
W poniższej sekcji zarządzania modelu pokazano, jak jest system rejestracji i zarządzania wielu modeli.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Model uczenia: modelowania i raportowania przy użyciu narzędzia AMAR

[Automatycznego modelowania i narzędzie raportowania (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) to narzędzie można dostosowywać, częściowo zautomatyzowanych do wykonywania tworzenia modelu z kominów hyper parametru i porównania dokładność tych modeli. 

Narzędzie tworzenia modelu jest plikiem R Markdown, który można uruchomić w celu utworzenia niezależne danych wyjściowych HTML z spisu treści dla nawigację za pośrednictwem jego różne sekcje. Trzy algorytmy są wykonywane po uruchomieniu pliku Markdown (Dzianina): umorzyć regresji przy użyciu glmnet pakietu losowe lasu przy użyciu pakietu randomForest i zwiększania drzew przy użyciu pakietu xgboost). Każdy z tych algorytmów tworzy uczonego modelu. Dokładność tych modeli jest następnie porównywany i zgłosił względną funkcji powierzchni znaczenie. Obecnie istnieją dwa narzędzia: jeden jest przeznaczona zadania klasyfikacji binarnej i jeden dla zadania regresji. Podstawowe różnice między nimi jest sposób parametrów sterujących i dokładność metryki są określone dla tych zadań learning. 

Plik yaml programu służy do określania:

- dane wejściowe (źródła SQL lub pliku danych R) 
- jaka część danych służy do szkolenia i jakie części do testowania
- jakie algorytmy do uruchomienia 
- wybór parametrów sterujących optymalizacji modelu:
    - cross-validation 
    - Uruchamianie
    - złożeń krzyżowego sprawdzania poprawności
- Ustawia hyper parametr dla każdej algorytmu. 

Liczba algorytmów, liczba złożeń optymalizacji, hyper parametrów i liczby zestawów hyper parametru do odchylenia za pośrednictwem również może być modyfikowany w pliku yaml programu, aby szybko uruchomić modeli. Na przykład można ich uruchomić z mniejszą liczbę złożeń CV, Zmniejsz liczbę zestawów parametrów. Jeśli jest to uzasadnione, ich można również uruchomić w bardziej kompleksowe z większej liczby złożeń CV lub większej liczby zestawów parametrów.

Aby uzyskać więcej informacji, zobacz [automatycznego modelowania i raportowania narzędzia w narzędzia do analizy danych TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Zarządzanie modelami
Po wielu modeli są wbudowane, zwykle musi być system rejestracji i zarządzania modeli. Zazwyczaj należy kombinację skryptów lub interfejsów API i system bazy danych lub versioning wewnętrznej bazy danych. Kilka opcji, które warto uwzględnić te zadania zarządzania są:

1. [Usługa Azure Machine Learning — model usługi zarządzania](../service/index.yml)
2. [ModelDB z MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-seerver jako system zarządzania modelu](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a>wdrożenia

Wdrożenia produkcyjnego umożliwia modelu do pełnienia roli active w firmie. Prognoz z wdrożonym modelu może służyć do decyzje biznesowe.

### <a name="production-platforms"></a>Platform produkcyjnych
Istnieją różne podejścia i platformy, aby umieścić modeli w środowisku produkcyjnym. Poniżej przedstawiono kilka opcji:


- [Model wdrażania w usłudze Azure Machine Learning](../desktop-workbench/model-management-overview.md)
- [Wdrażania modelu w programie SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>Uwaga: Przed ich wdrożeniem, jeden musi upewnić się, że opóźnienie oceniania modelu jest niski do użycia w środowisku produkcyjnym.
>

Dalsze przykłady są dostępne w wskazówki, które pokazują wszystkie kroki procesu **określonych scenariuszy**. Wymieniono i połączone z opisami miniatur w [wskazówki przykład](walkthroughs.md) artykułu. Pokazują one sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego.

Uwaga: Do wdrożenia za pomocą usługi Azure Machine Learning Studio, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>A / B, testowanie
W przypadku wielu modeli w środowisku produkcyjnym, może być przydatne do wykonania [A / B, testowanie](https://en.wikipedia.org/wiki/A/B_testing) porównanie wydajności modeli. 

 
## <a name="next-steps"></a>Kolejne kroki

[Śledzenie postępu projektów analizy danych](track-progress.md) pokazuje, jak naukowca danych można śledzić postęp projektu analizy danych.
 


