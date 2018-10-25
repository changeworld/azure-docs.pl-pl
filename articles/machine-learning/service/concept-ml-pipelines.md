---
title: Tworzenie potoków uczenia maszynowego — usługi Azure Machine Learning
description: Ten artykuł zawiera informacje na temat potoków, które można tworzyć z zestawem Azure Machine Learning SDK dla języka Python i zalety korzystania z potoków uczenia maszynowego. Machine learning (ML) potoki są używane przez analityków danych kompilacji, optymalizowanie i zarządzanie ich usługi machine learning przepływów pracy.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: 45aa954d2f85267b2c7c9aa2a7ba04e436765433
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023932"
---
# <a name="pipelines-and-azure-machine-learning"></a>Potoki i usługi Azure Machine Learning

Ten artykuł zawiera informacje na temat potoków, które można tworzyć z zestawem Azure Machine Learning SDK dla języka Python i zalety korzystania z potoków uczenia maszynowego.

## <a name="what-are-machine-learning-pipelines"></a>Co to są potokach uczenia maszynowego?

Machine learning (ML) potoki są używane przez analityków danych kompilacji, optymalizowanie i zarządzanie ich usługi machine learning przepływów pracy. Typowe potoku obejmuje sekwencję czynności, które obejmują następujące obszary:

+ Przygotowywanie danych, takich jak normalizations i przekształcenia
+ Szkolenie modeli, takich jak szczegółowego dostosowywania parametrów hyper i weryfikacja
+ Wdrożenie modelu i ocena  

Na poniższym diagramie przedstawiono przykład potoku:

[ ![Usługi machine learning potoki w usłudze Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png) ] (. / media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Dlaczego warto tworzyć potoki za pomocą usługi Azure Machine Learning?

[Azure Machine Learning SDK dla języka Python](#the-python-sdk-for-pipelines) umożliwiają tworzenie potoków uczenia Maszynowego, jak również przesłać i śledzić uruchomienia potoku poszczególnych.

W przypadku potoków można zoptymalizować przepływ pracy prostotę, szybkość, przenoszenia i ponownego użycia. Podczas kompilowania potoków przy użyciu usługi Azure Machine Learning, można skoncentrować się na najlepiej znasz &mdash; uczenia maszynowego &mdash; zamiast infrastruktury.

Przy użyciu etapy umożliwia ponowne uruchamianie tylko kroki, których potrzebujesz, jak dostosować i testowanie aplikacji logiki. Krok jest jednostki obliczeniowej w potoku. Jak pokazano na powyższym diagramie, zadanie przygotowania danych może obejmować wiele kroków w tym, ale nie są ograniczone do normalizacji, przekształcania, weryfikacji i cechowania.

Gdy potok został zaprojektowany, jest często więcej dostrajanie wokół pętli szkolenia potoku. Kiedy ponowne uruchomienie potoku wykonywania przechodzi do czynności, które konieczne będzie ponowne uruchomienie, takich jak skrypt szkolenia zaktualizowane i pomija, co się nie zmienił. Tym samym modelu dotyczy niezmienione skrypty używane do wykonywania tego kroku. 

Za pomocą usługi Azure Machine Learning używając różnych zestawów narzędzi i platform, takich jak Microsoft Cognitive Toolkit lub TensorFlow dla każdego kroku w potoku. Współrzędne platformy Azure między różnymi [celów obliczeń](concept-azure-machine-learning-architecture.md) używać tak, aby pośrednie dane mogą być udostępniane podrzędnego obliczeniowych elementów docelowych łatwe. 

## <a name="key-advantages"></a>Najważniejsze zalety

Kluczowe zalety tworzenie potoków przepływów pracy uczenia maszynowego jest:

|Kluczową zaletą|Opis|
|:-------:|-----------|
|**Nienadzorowane&nbsp;działa**|Należy zaplanować kilka kroków, aby uruchomić równolegle lub w kolejności, w sposób niezawodny i instalacji nienadzorowanej. Od czasu przygotowania bazy danych i modelowania można ostatnich dni lub tygodni, możesz teraz skupić się od innych zadań potoku jest uruchomiona. |
|**Mieszane i różnych obliczeń**|Użyj wielu potoki, które niezawodnie są koordynowane między heterogenicznych i skalowalnych obliczeń i magazynów. Potok poszczególne kroki można uruchomić na różnych obliczeniowych elementów docelowych, takich jak HDInsight, maszyny wirtualne do nauki o danych procesora GPU i Databricks, efektywne wykorzystanie Opcje obliczeniowe dostępne.|
|**Możliwość ponownego wykorzystania**|Potoki mogą być szablonowana dla konkretnych scenariuszy, takich jak ponownego szkolenia i oceniania partii.  One mogą być wywoływane z systemami zewnętrznymi za pomocą prostego wywołania REST.|
|**Śledzenie i przechowywania wersji**|Zamiast ręcznego śledzenia danych i wynik ścieżek iteracji, użyć potoki SDK jawnie nazwę i wersję danych źródeł danych wejściowych i generuje również zarządzać, skrypty i dane osobno w celu zwiększenia produktywności.|

## <a name="the-python-sdk-for-pipelines"></a>Zestaw SDK języka Python dla potoków

Za pomocą języka Python do tworzenia potoków uczenia Maszynowego. Zestaw SDK usługi Azure Machine Learning oferuje imperatywne konstrukcje sekwencyjne i przekształcają kroki potoków, gdy obecny jest niezależne danych. Można korzystać z niego w aplikacji Jupyter notebooks lub w innym preferowanego środowiska IDE. 

Dane deklaratywne zależności można zoptymalizować swoje zadania. Zestaw SDK zawiera framework wstępnie skompilowanych modułów dla typowych zadań, takich jak dane transferu, obliczenia tworzenia docelowego i modelu publikowania. Struktura można rozszerzyć do modelowania własnych Konwencji odpowiadającym poprzez implementację niestandardowej czynności, które są wielokrotnego użytku w potokach.

Potoki mogą być zapisywane jako szablony i którą można wdrożyć do punktu końcowego REST, dzięki czemu można zaplanować zadania wsadowe ocenianie przez lub ponownego trenowania.

Zapoznaj się z [dokumentacja zestawu SDK języka Python dla potoków](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) i Notes w następnej sekcji, aby dowiedzieć się, jak tworzyć własne.

## <a name="example-notebooks"></a>Przykład notesów
 
Następujące notesu pokazuje potoków przy użyciu usługi Azure Machine Learning: [potoku/potoku batch-scoring.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb).
 
Pobierz ten notes:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
