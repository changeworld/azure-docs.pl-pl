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
ms.date: 11/07/2018
ms.openlocfilehash: 3b5afd2a39c14d71e123f93d503692356a655a87
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965841"
---
# <a name="pipelines-and-azure-machine-learning"></a>Potoki i usługi Azure Machine Learning

Ten artykuł zawiera informacje na temat potoków, które można tworzyć z zestawem Azure Machine Learning SDK dla języka Python i zalety korzystania z potoków uczenia maszynowego.

## <a name="what-are-machine-learning-pipelines"></a>Co to są potokach uczenia maszynowego?

Za pomocą machine learning (ML) potoki, analitykami danych, inżynierami danych i informatycy mogą współpracować przy etapy:
+ Przygotowywanie danych, takich jak normalizations i przekształcenia
+ Szkolenie modelu
+ Ocena modelu
+ Wdrożenie 

Na poniższym diagramie przedstawiono przykład potoku:

[ ![Usługi machine learning potoki w usłudze Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png) ] (. / media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Dlaczego warto tworzyć potoki za pomocą usługi Azure Machine Learning?

[Azure Machine Learning SDK dla języka Python](#the-python-sdk-for-pipelines) umożliwiają tworzenie potoków uczenia Maszynowego, jak również przesłać i śledzić uruchomienia potoku poszczególnych.

W przypadku potoków można zoptymalizować przepływ pracy prostotę, szybkość, przenoszenia i ponownego użycia. Podczas kompilowania potoków przy użyciu usługi Azure Machine Learning, można skoncentrować się na najlepiej znasz &mdash; uczenia maszynowego &mdash; zamiast infrastruktury.

Przy użyciu etapy umożliwia ponowne uruchamianie tylko kroki, których potrzebujesz, jak dostosować i testowanie aplikacji logiki. Krok jest jednostki obliczeniowej w potoku. Jak pokazano na powyższym diagramie, zadanie przygotowania danych może obejmować wiele kroków w tym, ale nie są ograniczone do normalizacji, przekształcania, weryfikacji i cechowania. Źródła danych i danych pośrednich są ponownie używane w potoku, w którym zapisywane obliczenia czasu i zasobów. 

Gdy potok został zaprojektowany, jest często więcej dostrajanie wokół pętli szkolenia potoku. Kiedy ponowne uruchomienie potoku wykonywania przechodzi do czynności, które konieczne będzie ponowne uruchomienie, takich jak skrypt szkolenia zaktualizowane i pomija, co się nie zmienił. Tym samym modelu dotyczy niezmienione skrypty używane do wykonywania tego kroku. 

Za pomocą usługi Azure Machine Learning używając różnych zestawów narzędzi i platform, takich jak Microsoft Cognitive Toolkit lub TensorFlow dla każdego kroku w potoku. Współrzędne platformy Azure między różnymi [celów obliczeń](concept-azure-machine-learning-architecture.md) używać tak, aby pośrednie dane mogą być udostępniane podrzędnego obliczeniowych elementów docelowych łatwe. 

Możesz [śledzić metryki dla eksperymentów potoku](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) bezpośrednio w witrynie Azure portal. 

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

Dane deklaratywne zależności można zoptymalizować swoje zadania. Zestaw SDK zawiera strukturę wstępnie skompilowanych modułów dla typowych zadań, takich jak transfer danych i publikowanie modelu. Struktura można rozszerzyć do modelowania własnych Konwencji odpowiadającym poprzez implementację niestandardowej czynności, które są wielokrotnego użytku w potokach. Obliczeniowych elementów docelowych i zasobów magazynu mogą być także zarządzane bezpośrednio z zestawu SDK.

Potoki mogą być zapisywane jako szablony i którą można wdrożyć do punktu końcowego REST, dzięki czemu można zaplanować zadania wsadowe ocenianie przez lub ponownego trenowania.

Zapoznaj się z [dokumentacja zestawu SDK języka Python dla potoków](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) i Notes w następnej sekcji, aby dowiedzieć się, jak tworzyć własne.

## <a name="example-notebooks"></a>Przykład notesów
 
Następujące notesów pokazują potoków przy użyciu usługi Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md).
