---
title: 'Potoki: Optymalizowanie machine learning w przepływach pracy'
titleSuffix: Azure Machine Learning service
description: Ten artykuł zawiera informacje na temat potoków, które można tworzyć z zestawem Azure Machine Learning SDK dla języka Python i zalety korzystania z potoków uczenia maszynowego. Machine learning (ML) potoki są używane przez analityków danych kompilacji, optymalizowanie i zarządzanie ich usługi machine learning przepływów pracy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: f49b384f6f943e8c6767a6133a835011bc1e6bac
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059329"
---
# <a name="build-reusable-ml-pipelines-in-azure-machine-learning-service"></a>Kompilacji wielokrotnego użytku potokach uczenia Maszynowego w usłudze Azure Machine Learning

Ten artykuł zawiera informacje na temat potoków, można tworzyć przy użyciu Azure Machine Learning zestaw SDK for Python i zalety korzystania z potoków uczenia maszynowego.

## <a name="what-are-machine-learning-pipelines"></a>Co to są potokach uczenia maszynowego?

Za pomocą machine learning (ML) potoki, analitykami danych, inżynierami danych i informatycy mogą współpracować przy etapy:
+ Przygotowywanie danych, takich jak normalizations i przekształcenia
+ Szkolenie modelu
+ Ocena modelu
+ Wdrożenie 

Na poniższym diagramie przedstawiono przykład potoku:

![Usługi Machine learning potoki w usłudze Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Technologie Azure potoku należy używać?

Chmura platformy Azure udostępnia kilka innych potokach, każdy z innego celu. W poniższej tabeli wymieniono różne potoki i ich używać:

| Potok | Wyniki działania | Canonical potoku |
| ---- | ---- | ---- |
| Potoki usługi Azure Machine Learning | Definiuje wielokrotnego użytku usługi machine learning przepływów pracy, które mogą służyć jako szablon dla scenariuszy uczenia maszynowego. | Dane -> modelu |
| [Potoki usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Przenoszenie danych grup, przekształcania i działania sterowania potrzebne do wykonania zadania.  | Dane -> data |
| [Potoki usługi Azure](https://azure.microsoft.com/services/devops/pipelines/) | Ciągła integracja i dostarczanie aplikacji do dowolnego/dowolna platforma w chmurze  | Kod -> aplikacji/usługi |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Dlaczego warto tworzyć potoki za pomocą usługi Azure Machine Learning?

Możesz użyć [Azure Machine Learning SDK dla języka Python](#the-python-sdk-for-pipelines) do tworzenia potoków uczenia Maszynowego, jak również przesłać i śledzić uruchomienia potoku poszczególnych.

W przypadku potoków można zoptymalizować przepływ pracy prostotę, szybkość, przenoszenia i ponownego użycia. Podczas kompilowania potoków przy użyciu usługi Azure Machine Learning, można skoncentrować się na swoje doświadczenie, uczenia maszynowego, a nie na infrastrukturze.

Przy użyciu etapy umożliwia ponowne uruchamianie tylko kroki, których potrzebujesz, jak dostosować i testowanie aplikacji logiki. Krok jest jednostki obliczeniowej w potoku. Jak pokazano na powyższym diagramie, zadanie przygotowania danych może obejmować wiele kroków. Te kroki obejmują, ale nie są ograniczone do normalizacji, przekształcania, weryfikacji i cechowania. Źródła danych i danych pośrednich są ponownie używane w potoku, w którym zapisywane obliczenia czasu i zasobów. 

Po potok został zaprojektowany, jest często więcej dostrajanie wokół pętli szkolenia potoku. Kiedy ponowne uruchomienie potoku wykonywania przechodzi do czynności, które konieczne będzie ponowne uruchomienie, takich jak skrypt szkolenia zaktualizowane i pomija, co się nie zmienił. Tym samym modelu dotyczy niezmienione skrypty używane do wykonywania tego kroku. 

Za pomocą usługi Azure Machine Learning używając różnych zestawów narzędzi i platform, takich jak PyTorch lub TensorFlow, dla każdego kroku w potoku. Współrzędne platformy Azure między różnymi [celów obliczeń](concept-azure-machine-learning-architecture.md) używasz, tak aby pośrednie dane mogą być udostępniane podrzędnego obliczeniowych elementów docelowych łatwo. 

Możesz [śledzić metryki dla eksperymentów potoku](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) bezpośrednio w witrynie Azure portal. 

## <a name="key-advantages"></a>Najważniejsze zalety

Kluczowe zalety tworzenie potoków przepływów pracy uczenia maszynowego są następujące:

|Kluczową zaletą|Opis|
|:-------:|-----------|
|**Nienadzorowane&nbsp;działa**|Należy zaplanować kilka kroków, aby uruchomić równolegle lub w kolejności, w sposób niezawodny i instalacji nienadzorowanej. Ponieważ przygotowywanie danych i modelowanie ostatnich dni lub tygodni znajduje się po uruchomieniu potok teraz mogą skupić się na inne zadania. |
|**Mieszane i różnych obliczeń**|Użyj wielu potoki, które niezawodnie są koordynowane między heterogenicznych i skalowalnych obliczeń i magazynów. Potok poszczególne kroki można uruchamiać na różnych obliczeniowych elementów docelowych, takich jak HDInsight, maszyny wirtualne do nauki o danych procesora GPU i usługi Databricks. Umożliwia to efektywne wykorzystanie Opcje obliczeniowe dostępne.|
|**Możliwość ponownego wykorzystania**|Można templatize potoki pod kątem konkretnych scenariuszy, takich jak ponownego szkolenia i oceniania partii. Wyzwalanie je z systemami zewnętrznymi za pomocą prostego wywołania REST.|
|**Śledzenie i przechowywania wersji**|Zamiast ręcznie śledzenia danych i wynik ścieżek iteracji, użyć potoki SDK jawnie nazwę i wersję danych źródła danych wejściowych i danych wyjściowych. Można również zarządzać, skrypty i dane osobno w celu zwiększenia produktywności.|

## <a name="the-python-sdk-for-pipelines"></a>Zestaw SDK języka Python dla potoków

Za pomocą języka Python do tworzenia potoków uczenia Maszynowego. Zestaw SDK usługi Azure Machine Learning oferuje imperatywne konstrukcje sekwencyjne i przekształcają kroki potoków, gdy obecny jest niezależne danych. Można korzystać z niego w aplikacji Jupyter notebooks lub w innym preferowanego zintegrowanego środowiska projektowego. 

Dane deklaratywne zależności można zoptymalizować swoje zadania. Zestaw SDK zawiera strukturę wstępnie skompilowanych modułów dla typowych zadań, takich jak transfer danych i modelu publikowania. Możesz rozszerzyć platformę, by modelu własnych Konwencji odpowiadającym poprzez implementację niestandardowej czynności, które są wielokrotnego użytku w potokach. Można również zarządzać obliczeniowych elementów docelowych i zasobów magazynu, bezpośrednio z zestawu SDK.

Możesz zapisywać potoki jako szablony i wdrożyć je do punktu końcowego REST, dzięki czemu można zaplanować zadania wsadowe ocenianie przez lub ponownego trenowania.

Aby zobaczyć, jak tworzyć własne, zobacz [dokumentacja zestawu SDK języka Python dla potoków](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) i Notes w następnej sekcji.

## <a name="example-notebooks"></a>Przykład notesów
 
Następujące notesów pokazują potoków przy użyciu usługi Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md).
