---
title: Wykonywanie zadań do nauki o danych — proces nauki o danych zespołowych
description: Jak analityk danych można wykonać projekt nauki o danych w sposób śledzony, kontrolowane wersji i współpracy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e69a03cd142fdbcc5864ee38a4843e1c2e44a124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477157"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Wykonywanie zadań nauki o danych: eksploracja, modelowanie i wdrażanie

Typowe zadania do nauki o danych obejmują eksplorację danych, modelowanie i wdrażanie. W tym artykule pokazano, jak używać interaktywnych narzędzi **do eksploracji, analizy i raportowania danych (IDEAR)** oraz **automatycznego modelowania i raportowania (AMAR)** w celu wykonania kilku typowych zadań do nauki o danych, takich jak interaktywne eksploracja danych, analiza danych, raportowanie i tworzenie modelu. Opcje wdrażania modelu w środowisku produkcyjnym mogą obejmować:

- [Uczenie maszynowe platformy Azure](../index.yml)
- [SQL-Server z usługami ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Eksploracja 

Analityk danych może przeprowadzać eksplorację i raportowanie na różne sposoby: przy użyciu bibliotek i pakietów dostępnych dla języka Python (na przykład matplotlib) lub z R (na przykład ggplot lub krata). Analitycy danych można dostosować taki kod, aby dopasować do potrzeb eksploracji danych dla określonych scenariuszy. Potrzeby postępowania z danymi strukturalnymi są różne niż w przypadku danych nieustrukturyzowanych, takich jak tekst lub obrazy. 

Produkty, takie jak usługa Azure Machine Learning, zapewniają również [zaawansowane przygotowanie danych](../how-to-create-register-datasets.md) do wydzielenia danych i eksploracji, w tym tworzenia funkcji. Użytkownik powinien zdecydować się na narzędzia, biblioteki i pakiety, które najlepiej suite ich potrzeb. 

Rezultatem na końcu tej fazy jest raport eksploracji danych. Sprawozdanie powinno dostarczyć dość kompleksowego obrazu danych, które mają być wykorzystane do modelowania, oraz oceny, czy dane są odpowiednie do przystąpienia do etapu modelowania. Narzędzia procesu nauki o danych zespołu (TDSP) omówione w poniższych sekcjach dotyczące półautomatycznych poszukiwań, modelowania i raportowania zapewniają również standardowe raporty eksploracji i modelowania danych. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktywna eksploracja, analiza i raportowanie danych za pomocą narzędzia IDEAR

To narzędzie oparte na markdown lub python oparte na notesie zapewnia elastyczne i interaktywne narzędzie do oceny i eksplorowania zestawów danych. Użytkownicy mogą szybko generować raporty z zestawu danych przy minimalnym kodowaniu. Użytkownicy mogą klikać przyciski, aby wyeksportować wyniki eksploracji w narzędziu interaktywnym do raportu końcowego, który może być dostarczany do klientów lub używany do podejmowania decyzji o zmiennych, które mają zostać uwzględnione w kolejnym kroku modelowania.

W tej chwili narzędzie działa tylko na ramkach danych w pamięci. Plik YAML jest potrzebny do określenia parametrów zestawu danych, który ma zostać zbadany. Aby uzyskać więcej informacji, zobacz [IDEAR w TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modelowanie

Istnieje wiele zestawów narzędzi i pakietów dla modeli szkoleniowych w różnych językach. Analitycy danych powinni mieć swobodę korzystania z tych, z którymi są zadowoleni, o ile kwestie dotyczące wydajności dotyczące dokładności i opóźnienia są spełnione w odpowiednich przypadkach użycia biznesowego i scenariuszach produkcyjnych.

W następnej sekcji pokazano, jak używać narzędzia TDSP opartego na r do modelowania półautomatyczne. To narzędzie AMAR może być używane do szybkiego generowania modeli linii bazowych, a także parametrów, które muszą być dostrojone, aby zapewnić lepszą wydajność modelu.
W poniższej sekcji zarządzania modelami pokazano, jak mieć system do rejestrowania wielu modeli i zarządzania nimi.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Szkolenie modelek: modelowanie i raportowanie za pomocą narzędzia AMAR

[Narzędzie do automatycznego modelowania i raportowania (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) zapewnia konfigurowalne, półautomatyczne narzędzie do tworzenia modeli z hiperparametrycznym zamiataniem i porównywania dokładności tych modeli. 

Narzędzie do tworzenia modelu jest plikiem R Markdown, który można uruchomić w celu uzyskania samodzielnego wyjścia HTML ze spisem treści w celu łatwego przechodzenia przez różne sekcje. Trzy algorytmy są wykonywane, gdy plik Markdown jest uruchamiany (dzianina): regresja za pomocą pakietu glmnet, losowy las przy użyciu pakietu randomForest i zwiększanie drzew przy użyciu pakietu xgboost). Każdy z tych algorytmów tworzy przeszkolony model. Dokładność tych modeli jest następnie porównywana i zgłaszane są wykresy względnego znaczenia operacji. Obecnie istnieją dwa narzędzia: jeden jest dla zadania klasyfikacji binarnej i jeden jest dla zadania regresji. Podstawowe różnice między nimi jest sposób kontroli parametry i metryki dokładności są określone dla tych zadań uczenia się. 

Plik YAML służy do określania:

- wprowadzanie danych (źródło SQL lub plik R-Data) 
- jaka część danych jest wykorzystywana do szkolenia i jaka część do testowania
- które algorytmy uruchamiać 
- wybór parametrów sterujących do optymalizacji modelu:
    - krzyżowa walidacja 
    - Bootstrap
    - fałdy krzyżowej walidacji
- zestawy hiperparametrów dla każdego algorytmu. 

Liczba algorytmów, liczba zagięć do optymalizacji, hiperparametry i liczba zestawów hiperparametrów do przeciągnięcia można również zmodyfikować w pliku Yaml, aby szybko uruchomić modele. Na przykład można je uruchomić z mniejszą liczbą zagęszków CV, mniejszą liczbą zestawów parametrów. Jeśli jest to uzasadnione, mogą być również uruchamiane bardziej kompleksowo z większą liczbą zagęszków CV lub większą liczbą zestawów parametrów.

Aby uzyskać więcej informacji, zobacz [Narzędzie do automatycznego modelowania i raportowania w narzędziach do nauki o danych TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Zarządzanie modelami
Po zbudowaniu wielu modeli zwykle trzeba mieć system do rejestrowania i zarządzania modelami. Zazwyczaj potrzebna jest kombinacja skryptów lub interfejsów API oraz bazy danych wewnętrznej bazy danych lub systemu przechowywania wersji. Kilka opcji, które można wziąć pod uwagę dla tych zadań zarządzania są:

1. [Azure Machine Learning — usługa zarządzania modelami](../index.yml)
2. [ModelDB firmy MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-server jako system zarządzania modelami](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Wdrażanie

Wdrożenie produkcyjne umożliwia modelowi odgrywanie aktywnej roli w firmie. Prognozy z wdrożonego modelu mogą być używane do podejmowania decyzji biznesowych.

### <a name="production-platforms"></a>Platformy produkcyjne
Istnieją różne podejścia i platformy do wprowadzenia modeli do produkcji. Oto kilka opcji:


- [Wdrażanie modelu w usłudze Azure Machine Learning](../how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Przed wdrożeniem należy ubezpieczyć opóźnienie oceniania modelu jest wystarczająco niska, aby użyć w produkcji.
>
>

Dalsze przykłady są dostępne w instruktażech, które pokazują wszystkie kroki w procesie dla **określonych scenariuszy**. Są one wyświetlane i połączone z opisami miniatur w [przykładowym przewodniku](walkthroughs.md) artykułu. Ilustrują one sposób łączenia narzędzi i usług w chmurze, narzędzia lokalne i usługi w przepływ pracy lub potok, aby utworzyć inteligentną aplikację.

> [!NOTE]
> Aby wdrożyć przy użyciu usługi Azure Machine Learning Studio, zobacz [Wdrażanie usługi sieci web usługi Azure Machine Learning.](../studio/deploy-a-machine-learning-web-service.md)
>
>

### <a name="ab-testing"></a>Testowanie A/B
Gdy wiele modeli jest w produkcji, może być przydatne do wykonywania [testów A / B](https://en.wikipedia.org/wiki/A/B_testing) w celu porównania wydajności modeli. 

 
## <a name="next-steps"></a>Następne kroki

[Śledzenie postępu projektów do nauki o danych](track-progress.md) pokazuje, jak analityk danych może śledzić postęp projektu do nauki o danych.

[Operacja modelu i ciągła integracja/płyta CD](ci-cd-flask.md) pokazuje, jak można wykonać ciągłość ciągłej integracji/ciągłego wdrażania z opracowanymi modelami.


