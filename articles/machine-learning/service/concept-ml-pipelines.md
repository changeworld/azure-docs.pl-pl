---
title: 'Potoki: Optymalizowanie przepływów pracy uczenia maszynowego'
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
ms.openlocfilehash: a9965dbbca939f566048312af921061a188ee50d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884242"
---
# <a name="build-reusable-ml-pipelines-in-azure-machine-learning-service"></a>Kompilowanie potoków ML wielokrotnego użytku w usłudze Azure Machine Learning

W tym artykule dowiesz się więcej na temat potoków uczenia maszynowego, które można skompilować za pomocą zestawu SDK Azure Machine Learning dla języka Python, oraz zalety korzystania z potoków.

## <a name="what-are-machine-learning-pipelines"></a>Co to są potokach uczenia maszynowego?

Za pomocą machine learning (ML) potoki, analitykami danych, inżynierami danych i informatycy mogą współpracować przy etapy:
+ Przygotowywanie danych, takich jak normalizations i przekształcenia
+ Szkolenie modelu
+ Ocena modelu
+ Wdrożenie

Na poniższym diagramie przedstawiono przykładowy proces potoku:

![Potoki uczenia maszynowego w usłudze Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Której technologii potoku platformy Azure należy używać?

Chmura systemu Azure udostępnia kilka innych potoków, z których każdy ma inny cel. W poniższej tabeli wymieniono różne potoki i ich zastosowania:

| Potok | Wyniki działania | Rura kanoniczna |
| ---- | ---- | ---- |
| Potoki Azure Machine Learning | Definiuje przepływy pracy uczenia maszynowego wielokrotnego użytku, które mogą być używane jako szablon scenariuszy uczenia maszynowego. | Model > danych |
| [Potoki usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Grupuje działania związane z przenoszeniem, przekształcaniem i kontrolą danych, które są konieczne do wykonania zadania.  | Dane > danych |
| [Potoki platformy Azure](https://azure.microsoft.com/services/devops/pipelines/) | Ciągła integracja i dostarczanie aplikacji na dowolną platformę/dowolną chmurę  | Usługa Code-> App/Service |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Dlaczego warto tworzyć potoki za pomocą usługi Azure Machine Learning?

Możesz użyć [zestawu SDK Azure Machine Learning dla języka Python](#the-python-sdk-for-pipelines) , aby utworzyć potoki ml, a także przesłać i śledzić pojedyncze uruchomienia potoku.

W przypadku potoków można zoptymalizować przepływ pracy prostotę, szybkość, przenoszenia i ponownego użycia. Podczas kompilowania potoków z Azure Machine Learning możesz skupić się na wiedzy, uczeniu maszynowym, a nie na infrastrukturze i automatyzacji.

Potoki są zbudowane z wielu **kroków**, które są odrębnymi jednostkami obliczeniowymi w potoku. Każdy krok można uruchamiać niezależnie i używać izolowanych zasobów obliczeniowych. Dzięki temu wiele analityków danych może współdziałać w tym samym potoku bez nadmiernie nieopodatkowanych zasobów obliczeniowych, a także ułatwia korzystanie z różnych typów i rozmiarów obliczeniowych dla każdego kroku.

Po zaprojektowaniu potoku jest często bardziej precyzyjne dostosowywanie pętli szkoleniowej potoku. Po ponownym uruchomieniu potoku przebieg zostanie przesunięty do odrębnych kroków, które należy uruchomić ponownie, takich jak zaktualizowany skrypt szkoleniowy, i pomija to, co nie zostało zmienione. Tym samym modelu dotyczy niezmienione skrypty używane do wykonywania tego kroku. Ta funkcja pomaga uniknąć kosztownych i intensywnie czasochłonnych kroków, takich jak pozyskiwanie i Przekształcanie danych, jeśli dane podstawowe nie uległy zmianie.

Za pomocą Azure Machine Learning można używać różnych zestawów narzędzi i platform, takich jak PyTorch lub TensorFlow, dla każdego etapu w potoku. Platforma Azure koordynuje między różnymi używanymi [obiektami docelowymi obliczeniowymi](concept-azure-machine-learning-architecture.md) , aby można było łatwo udostępniać dane pośrednie z podrzędnymi obiektami docelowymi obliczeniowymi.

Możesz [śledzić metryki dla eksperymentów potoku](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) bezpośrednio w witrynie Azure portal. Po opublikowaniu potoku można skonfigurować punkt końcowy REST, który umożliwia ponowne uruchomienie potoku z poziomu dowolnej platformy lub stosu.

## <a name="key-advantages"></a>Najważniejsze zalety

Najważniejsze zalety używania potoków dla przepływów pracy usługi Machine Learning to:

|Kluczową zaletą|Opis|
|:-------:|-----------|
|**Nienadzorowane&nbsp;działa**|Zaplanuj wykonywanie kroków równolegle lub w kolejności w sposób niezawodny i nienadzorowany. Przygotowanie i modelowanie danych może trwać ostatni dzień lub tygodnie, a potoki umożliwiają skoncentrowanie się na innych zadaniach w trakcie działania procesu. |
|**Obliczenia heterogenicznych**|Używaj wielu potoków, które są niezawodne, skoordynowane w heterogenicznych i skalowalnych zasobach obliczeniowych i lokalizacjach magazynu. Uruchamiaj poszczególne etapy potoku w różnych obiektach docelowych obliczeń, takich jak HDInsight, maszyny wirtualne do nauki o danych procesora GPU i kostki datakostek. Pozwala to efektywnie wykorzystać dostępne opcje obliczeń.|
|**Możliwość ponownego wykorzystania**|Tworzenie szablonów potoku dla konkretnych scenariuszy, takich jak ponowne szkolenie i ocenianie partii. Wyzwalaj opublikowane potoki z systemów zewnętrznych za pośrednictwem prostych wywołań REST.|
|**Śledzenie i przechowywania wersji**|Zamiast ręcznego śledzenia danych i ścieżek wyników podczas iteracji Użyj zestawu SDK potoków, aby jawnie nazwać i w wersji źródła danych, dane wejściowe i wyjściowe. Możesz również zarządzać skryptami i danymi osobno w celu zwiększenia produktywności.|
|**Społeczności**|Potoki umożliwiają analitykom danych współpracę we wszystkich obszarach procesu projektowania uczenia maszynowego, jednocześnie umożliwiając jednocześnie pracę nad krokami potoku.|

## <a name="the-python-sdk-for-pipelines"></a>Zestaw SDK języka Python dla potoków

Za pomocą języka Python do tworzenia potoków uczenia Maszynowego. Zestaw SDK usługi Azure Machine Learning oferuje imperatywne konstrukcje sekwencyjne i przekształcają kroki potoków, gdy obecny jest niezależne danych. Możesz korzystać z niego w notesach Jupyter lub w innym preferowanym środowisku IDE.

Dane deklaratywne zależności można zoptymalizować swoje zadania. Zestaw SDK zawiera strukturę wstępnie utworzonych modułów dla typowych zadań, takich jak transfer danych i publikowanie modeli. Można zwiększyć strukturę w celu modelowania własnych Konwencji, implementując niestandardowe kroki, które są wielokrotnego użytku między potokami. Można także zarządzać elementami docelowymi obliczeń i zasobami magazynu bezpośrednio z zestawu SDK.

Potoki można zapisywać jako szablony i wdrażać je w punkcie końcowym REST, aby można było zaplanować zadania wsadowe lub przeszkolenie.

Aby dowiedzieć się, jak utworzyć własne, zobacz [Dokumentacja zestawu SDK języka Python dla potoków](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) i notesu w następnej sekcji.

## <a name="example-notebooks"></a>Przykład notesów

Następujące notesów pokazują potoków przy użyciu usługi Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md).
