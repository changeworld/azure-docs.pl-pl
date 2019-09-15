---
title: Co to są potoki ML
titleSuffix: Azure Machine Learning service
description: Ten artykuł zawiera informacje na temat potoków, które można tworzyć z zestawem Azure Machine Learning SDK dla języka Python i zalety korzystania z potoków uczenia maszynowego. Machine learning (ML) potoki są używane przez analityków danych kompilacji, optymalizowanie i zarządzanie ich usługi machine learning przepływów pracy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 2547ca3af3039af53cb5fd5b244669b9152c21b3
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993284"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>Co to są potoki ML w usłudze Azure Machine Learning?

Dowiedz się, jak można tworzyć potoki usługi Machine Learning i zarządzać nimi za pomocą usług Azure Machine Learning. 

Za pomocą machine learning (ML) potoki, analitykami danych, inżynierami danych i informatycy mogą współpracować przy etapy:
+ Przygotowywanie danych, takich jak normalizations i przekształcenia
+ Szkolenie modelu
+ Ocena modelu
+ Wdrożenie

Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md).

![Potoki uczenia maszynowego w usłudze Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Której technologii potoku platformy Azure należy używać?

Chmura systemu Azure udostępnia kilka innych potoków, z których każdy ma inny cel. W poniższej tabeli wymieniono różne potoki i ich zastosowania:

| Potok | Wyniki działania | Rura kanoniczna |
| ---- | ---- | ---- |
| Potoki Azure Machine Learning | Definiuje przepływy pracy uczenia maszynowego wielokrotnego użytku, które mogą być używane jako szablon scenariuszy uczenia maszynowego. | Model > danych |
| [Potoki usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Grupuje działania związane z przenoszeniem, przekształcaniem i kontrolą danych, które są konieczne do wykonania zadania.  | Dane > danych |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Ciągła integracja i dostarczanie aplikacji na dowolną platformę/dowolną chmurę  | Usługa Code-> App/Service |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Dlaczego warto tworzyć potoki za pomocą usługi Azure Machine Learning?

Potoki usługi Machine Learning optymalizują przepływ pracy przy użyciu szybkości, przenośności i ponownego wykorzystania, dzięki czemu możesz skupić się na wiedzy, uczeniu maszynowym, a nie na infrastrukturze i automatyzacji.

Potoki są zbudowane z wielu **kroków**, które są odrębnymi jednostkami obliczeniowymi w potoku. Każdy krok można uruchamiać niezależnie i używać izolowanych zasobów obliczeniowych.
Niezależne kroki umożliwiają wielu analitykom danych jednoczesne działanie w tym samym potoku bez zasobów obliczeniowych, a także ułatwia korzystanie z różnych typów i rozmiarów obliczeniowych dla każdego kroku.

Po zaprojektowaniu potoku jest często bardziej precyzyjne dostosowywanie pętli szkoleniowej potoku. Po ponownym uruchomieniu potoku przebieg zostanie przesunięty do odrębnych kroków, które należy uruchomić ponownie, takich jak zaktualizowany skrypt szkoleniowy, i pomija to, co nie zostało zmienione. Tym samym modelu dotyczy niezmienione skrypty używane do wykonywania tego kroku. Ta funkcja ponownego wykorzystania pozwala uniknąć kosztownego i intensywnego wykonywania kroków, takich jak pozyskiwanie i Przekształcanie danych, jeśli dane podstawowe nie uległy zmianie.

Za pomocą Azure Machine Learning można używać różnych zestawów narzędzi i platform, takich jak PyTorch lub TensorFlow, dla każdego etapu w potoku. Platforma Azure koordynuje między różnymi używanymi [obiektami docelowymi obliczeniowymi](concept-azure-machine-learning-architecture.md) , aby można było łatwo udostępniać dane pośrednie z podrzędnymi obiektami docelowymi obliczeniowymi.

[Metryki dla eksperymentów potoku można śledzić](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) bezpośrednio w Azure Portal lub na [stronie docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com). Po opublikowaniu potoku można skonfigurować punkt końcowy REST, który umożliwia ponowne uruchomienie potoku z poziomu dowolnej platformy lub stosu.

## <a name="key-advantages"></a>Najważniejsze zalety

Najważniejsze zalety używania potoków dla przepływów pracy usługi Machine Learning to:

|Kluczową zaletą|Opis|
|:-------:|-----------|
|**Nienadzorowane&nbsp;działa**|Zaplanuj wykonywanie kroków równolegle lub w kolejności w sposób niezawodny i nienadzorowany. Przygotowanie i modelowanie danych może trwać ostatni dzień lub tygodnie, a potoki umożliwiają skoncentrowanie się na innych zadaniach w trakcie działania procesu. |
|**Obliczenia heterogenicznych**|Używaj wielu potoków, które są niezawodne, skoordynowane w heterogenicznych i skalowalnych zasobach obliczeniowych i lokalizacjach magazynu. Wydajnie korzystaj z dostępnych zasobów obliczeniowych, uruchamiając poszczególne etapy potoku w różnych obiektach docelowych obliczeń, takich jak HDInsight, maszyny wirtualne do nauki o danych procesora GPU i kostki datacegły.|
|**Możliwość ponownego wykorzystania**|Tworzenie szablonów potoku dla konkretnych scenariuszy, takich jak ponowne szkolenie i ocenianie partii. Wyzwalaj opublikowane potoki z systemów zewnętrznych za pośrednictwem prostych wywołań REST.|
|**Śledzenie i przechowywania wersji**|Zamiast ręcznego śledzenia danych i ścieżek wyników podczas iteracji Użyj zestawu SDK potoków, aby jawnie nazwać i w wersji źródła danych, dane wejściowe i wyjściowe. Możesz również zarządzać skryptami i danymi osobno w celu zwiększenia produktywności.|
|**Społeczności**|Potoki umożliwiają analitykom danych współpracę we wszystkich obszarach procesu projektowania uczenia maszynowego, jednocześnie umożliwiając jednocześnie pracę nad krokami potoku.|

## <a name="the-python-sdk-for-pipelines"></a>Zestaw SDK języka Python dla potoków

[Użyj zestawu SDK języka Python,](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) aby utworzyć potoki ml w preferowanym, zintegrowanym środowisku programistycznym (IDE) lub w notesach Jupyter. Zestaw SDK usługi Azure Machine Learning oferuje imperatywne konstrukcje sekwencyjne i przekształcają kroki potoków, gdy obecny jest niezależne danych. 

Dane deklaratywne zależności można zoptymalizować swoje zadania. Zestaw SDK zawiera strukturę wstępnie utworzonych modułów dla typowych zadań, takich jak transfer danych i publikowanie modeli. Można zwiększyć strukturę w celu modelowania własnych Konwencji przez implementację niestandardowych kroków wielokrotnego użytku między potokami. Można także zarządzać elementami docelowymi obliczeń i zasobami magazynu bezpośrednio z zestawu SDK.

Zapisz potoki jako szablony i wdróż je w punkcie końcowym REST dla zadań wsadowych lub do ponownego szkolenia.

Istnieją dwa pakiety języka Python dla potoków z Azure Machine Learning: [potoki usługi Azure-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) i uczenie maszynowe — [etapy](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Aby szybko rozpocząć pracę, użyj jednego z wstępnie utworzonych modułów, takich jak:

* Uruchamianie skryptu języka Python w kroku z [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)
* Transferowanie danych między opcjami magazynu za pomocą [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)
* Tworzenie kroku potoku AutoML z [AutoMLStep](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep)

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md).

+ Dowiedz się, jak [uruchamiać przewidywania wsadowe w przypadku dużych ilości danych](tutorial-pipeline-batch-scoring-classification.md).

+ Zobacz [dokumenty referencyjne zestawu SDK dla potoków](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py).

+ Wypróbuj przykładowe notesy Jupyter, które przedstawiają [potoki Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Dowiedz się, jak [uruchamiać notesy w celu eksplorowania tej usługi](samples-notebooks.md).
