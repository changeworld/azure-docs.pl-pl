---
title: Wykonywanie zadania wydobywania danych — danych zespołu dla celów naukowych
description: Jak analitykiem danych można wykonać projektu nauki o danych w słupkowych niepoddany kontroli wersji i sposób współpracy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 984b03288b8dae644fc04a2cd78fb03a2e027f62
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722207"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Wykonania zadań dotyczących nauki o danych: eksploracji, modelowania i wdrożenia

Typowe zadania wydobywania danych obejmują eksplorację danych, modelowania i wdrożenia. W tym artykule pokazano, jak **interakcyjna Eksploracja danych, analizy i raportowania (IDEAR)** i **zautomatyzowane modelowania i raportowania (AMAR)** narzędzia, aby wykonać kilka typowych zadań do nauki o danych Interakcyjna Eksploracja danych, analizy danych, raportowania i tworzenia modelu. Opcje wdrażania modelu w środowisku produkcyjnym mogą obejmować:

- [Azure Machine Learning](../index.yml)
- [Programu SQL Server z usługami uczenia Maszynowego](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Eksploracja 

Analityk danych można wykonywać eksploracji i raportowania w programie na różne sposoby: za pomocą bibliotek i pakietów dostępnych dla języka Python (matplotlib, na przykład) lub przy użyciu języka R (ggplot lub struktury, na przykład). Analitycy danych, można dostosować takiego kodu do potrzeb eksploracji danych w określonych scenariuszach. Wymagania dotyczące postępowania z danymi strukturalnymi różnią się w przypadku danych niestrukturalnych, takich jak tekst lub obrazy. 

Produkty takie jak Azure Machine Learning również zapewniają [Zaawansowane Przygotowywanie danych](../how-to-create-register-datasets.md) do przetwarzanie i eksploracji danych, w tym tworzenie funkcji. Użytkownik należy podjąć decyzję dotyczącą na narzędzia, biblioteki i pakiety, które najlepiej suite ich potrzeb. 

Elementy dostarczane na końcu tej fazy jest raport eksploracji danych. Raport powinien zapewniać dość kompleksowych danych służący do modelowania i ocenę, czy dane znajdują się odpowiednie przejść do kroku modelowania. Narzędzia Team Data Science naukowych, omówiono w poniższych sekcjach do automatyzacji, dającą eksploracji, modelowania i raportowania udostępniają eksplorację danych standardowych i modelowania raportów. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interakcyjna Eksploracja danych, analizy i raportowania za pomocą narzędzia IDEAR

Ta R opartych na języku znaczników markdown lub narzędzia na podstawie notesu Python zapewnia elastyczne i interaktywne narzędzie do oceny i zapoznaj się z zestawami danych. Użytkownicy mogą szybko wygenerować raporty z zestawu danych przy użyciu minimalnego kodowania. Użytkownicy mogą kliknąć przycisków, aby wyeksportować wyniki eksploracji interaktywne narzędzia do raport końcowy, który może być dostarczane do klientów lub używana do podejmowania decyzji, na które zmienne do uwzględnienia w kroku kolejnych modelowania.

W tej chwili narzędzie działa tylko na ramki danych w pamięci. Pozwala określić parametry zestawu danych do eksplorowania na potrzeby pliku YAML. Aby uzyskać więcej informacji, zobacz [IDEAR w narzędzia do analizy danych przetwarzania TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> modelowanie

Istnieje wiele narzędzi i pakietów szkolenie modeli w różnych językach. Analitycy danych powinien swobodnie do użycia, który z nich znajdują się komfortowo, jednocześnie, tak długo, jak zagadnienia związane z wydajnością dotyczące dokładności i opóźnienia są spełnione dla odpowiednich firm przypadków użycia i scenariuszy produkcyjnych.

Następna sekcja pokazuje, jak narzędzie TDSP na podstawie języka R do automatyzacji, dającą modelowania. To narzędzie AMAR może służyć do generowania modeli linii bazowej szybko również parametry, które muszą być dostosowane do zapewnienia lepszego wykonywania modelu.
W poniższej sekcji dotyczącej zarządzania modelu pokazuje jak system rejestrowania i zarządzanie wielu modeli.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Model szkolenia: modelowania i raportowania za pomocą narzędzia AMAR

[Zautomatyzowane modelowania i raportowania (AMAR) Narzędzie](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) zapewnia dostosowywalny, automatyzacji, dającą narzędzie do tworzenia modelu przy użyciu parametrów zaczynają wykonywania i porównywanie dokładność tych modeli. 

Narzędzie do tworzenia modelu jest plikiem R Markdown, który można uruchomić w celu wygenerowania niezależna dane wyjściowe HTML za pomocą spisu treści ułatwiających przechodzenie przez jego różne sekcje. Trzy algorytmy są wykonywane po uruchomieniu pliku Markdown (zgrany): umorzyć regresji przy użyciu glmnet pakietu losowe lasu za pomocą pakietu randomForest i zwiększenie drzew przy użyciu pakietu xgboost). Każda z tych algorytmów tworzy uczonego modelu. Dokładność tych modeli jest porównywana i powierzchnie znaczenie względną funkcji są zgłaszane. Obecnie istnieją dwa narzędzia: jeden jest przeznaczony dla zadań Klasyfikacja binarna, a jeden jest przeznaczony dla zadań regresji. Podstawowe różnice między nimi jest parametry sterujące sposobem i dokładność metryki są określone dla tych zadań uczenia. 

Plik YAML służy do określania:

- dane wejściowe (źródła SQL lub plik danych języka R) 
- jaka część danych służy do trenowania i jakie części do testowania
- jakie algorytmy do uruchomienia 
- Wybór parametry sterujące optymalizacji modelu:
    - cross-validation 
    - ładowanie początkowe
    - złożeń krzyżowego sprawdzania poprawności
- zestawy parametrów dla każdego algorytmu. 

Liczba algorytmów, liczba złożeń optymalizacji, hiper parametrami i liczby zestawów parametrów do odchylenia za pośrednictwem również można zmodyfikować w pliku Yaml do szybkiego uruchamiania modeli. Na przykład można ich uruchomić przy użyciu mniejszej liczby złożeń CV, mniejszej liczby zestawów parametrów. Jeśli jest to zasadne, ich można również uruchomić bardziej wszechstronny z większej liczby złożeń CV lub większej liczby zestawów parametrów.

Aby uzyskać więcej informacji, zobacz [zautomatyzowane modelowania i raportowania narzędzia w narzędzia do analizy danych przetwarzania TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Zarządzanie modelami
Po zostały skompilowane wielu modeli, należy zazwyczaj ma system rejestrowania modeli i zarządzania nimi. Zazwyczaj należy kombinację skryptów lub interfejsów API i bazy danych lub wersji systemu zaplecza. Jest kilka opcji, które należy rozważyć do wykonywania tych zadań zarządzania są:

1. [Usługa Azure Machine Learning — Usługa Zarządzanie modelami](../index.yml)
2. [ModelDB z uniwersytetu MIT](https://mitdbg.github.io/modeldb/) 
3. [Programu SQL server jako system zarządzania modelu](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> wdrożenie

Wdrożenie produkcyjne umożliwia modelu do odtwarzania aktywną rolę w biznesie. Za pomocą wdrożonego modelu może służyć do podejmowania decyzji biznesowych.

### <a name="production-platforms"></a>Platform produkcyjnych
Istnieją różne podejścia i platformy, aby wdrożyć modele do produkcji. Poniżej przedstawiono kilka opcji:


- [Wdrażanie modelu w Azure Machine Learning](../how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Przed wdrożeniem jedna ma upewnij się, że opóźnienie oceniania modelu jest niskie, do użycia w środowisku produkcyjnym.
>
>

Dalsze przykłady są dostępne w instruktaży, które przedstawiają wszystkie kroki procesu **konkretnych scenariuszy**. Wymieniono i połączone z opisami miniatur w [przykładowe przewodniki](walkthroughs.md) artykułu. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji.

> [!NOTE]
> Do wdrożenia przy użyciu usługi Azure Machine Learning Studio, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Testowanie A/B
W przypadku wielu modeli w środowisku produkcyjnym, może być przydatne do wykonania [A / B, testowanie](https://en.wikipedia.org/wiki/A/B_testing) porównywanie wydajności modeli. 

 
## <a name="next-steps"></a>Następne kroki

[Śledzić postęp projektów do nauki o danych](track-progress.md) pokazuje, jak analitykiem danych można śledzić postęp projektu nauki o danych.

[Operacja i ciągłej integracji/ciągłego wdrażania modelu](ci-cd-flask.md) pokazuje, jak ciągła Integracja/ciągłe dostarczanie mogą być wykonywane przy użyciu modeli opracowanych.


