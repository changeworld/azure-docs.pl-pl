---
title: Tworzenie modeli ML z projektantem
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o terminach, pojęciach i przepływie pracy, który składa się na projektanta usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 78a6e7fa8d030185f537136a3a2124d8bc59d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037627"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Co to jest projektant usługi Azure Machine Learning (wersja zapoznawcza)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Projektant usługi Azure Machine Learning umożliwia wizualne łączenie [zestawów danych](#datasets) i [modułów](#module) na interaktywnej kanwie w celu tworzenia modeli uczenia maszynowego. Aby dowiedzieć się, jak rozpocząć pracę z projektantem, zobacz [Samouczek: Przewidywanie ceny samochodu z projektantem](tutorial-designer-automobile-price-train-score.md)

![Przykład projektanta usługi Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

Projektant używa [obszaru roboczego](concept-workspace.md) usługi Azure Machine Learning do organizowania zasobów udostępnionych, takich jak:

+ [Potoki](#pipeline)
+ [Zestawy danych](#datasets)
+ [Zasoby obliczeniowe](#compute)
+ [Zarejestrowane modele](concept-azure-machine-learning-architecture.md#models)
+ [Opublikowane potoki](#publish)
+ [Punkty końcowe w czasie rzeczywistym](#deploy)

## <a name="model-training-and-deployment"></a>Szkolenie i wdrażanie modeli

Projektant udostępnia wizualne kanwę do tworzenia, testowania i wdrażania modeli uczenia maszynowego. Z projektantem możesz:

+ Zestawy danych i [moduły przeciągania](#datasets) i upuszczania na kanwę. [modules](#module)
+ Połącz moduły, aby utworzyć [projekt rurociągu](#pipeline-draft).
+ Prześlij [przebieg potoku](#pipeline-run) przy użyciu zasobów obliczeniowych w obszarze roboczym usługi Azure Machine Learning.
+ Konwertuj **potoki szkoleniowe** **na potoki wnioskowania**.
+ [Opublikuj](#publish) potoki do **punktu końcowego potoku** REST, aby przesłać nowe przebiegi potoku z różnymi parametrami i zestawami danych.
    + Opublikuj **potok szkoleniowy,** aby ponownie użyć jednego potoku do szkolenia wielu modeli podczas zmiany parametrów i zestawów danych.
    + Opublikuj **potok wnioskowania partii,** aby prognozować nowe dane przy użyciu wcześniej uczonego modelu.
+ [Wdrażanie](#deploy) **potoku wnioskowania w czasie rzeczywistym** do punktu końcowego w czasie rzeczywistym, aby przewidywać nowe dane w czasie rzeczywistym.

![Diagram przepływu pracy do szkolenia, wnioskowania wsadowego i wnioskowania w czasie rzeczywistym w projektancie](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Potok

[Potok](concept-azure-machine-learning-architecture.md#ml-pipelines) składa się z zestawów danych i modułów analitycznych, które można połączyć ze sobą. Potoki mają wiele zastosowań: można wykonać potok, który trenuje jeden model lub taki, który trenuje wiele modeli. Można utworzyć potok, który sprawia, że prognoz w czasie rzeczywistym lub w partii lub utworzyć potok, który czyści tylko dane. Potoki umożliwiają ponowne wykorzystanie pracy i organizowanie projektów.

### <a name="pipeline-draft"></a>Wersja robocza rurociągu

Podczas edytowania potoku w projektancie postęp jest zapisywany jako **wersja robocza potoku**. Pochylenia potoku można edytować w dowolnym momencie, dodając lub usuwając moduły, konfigurując obiekty docelowe obliczeń, tworząc parametry i tak dalej.

Prawidłowy potok ma następujące cechy:

* Zestawy danych mogą łączyć się tylko z modułami.
* Moduły mogą łączyć się tylko z zestawami danych lub innymi modułami.
* Wszystkie porty wejściowe dla modułów muszą mieć pewne połączenie z przepływem danych.
* Należy ustawić wszystkie wymagane parametry dla każdego modułu.

Gdy będziesz gotowy do uruchomienia wersji roboczej potoku, prześlij przebieg potoku.

### <a name="pipeline-run"></a>Przebieg rurociągu

Za każdym razem, gdy uruchamiasz potok, konfiguracja potoku i jego wyniki są przechowywane w obszarze roboczym jako **uruchomienie potoku.** Można wrócić do dowolnego przebiegu potoku, aby sprawdzić go do celów rozwiązywania problemów lub inspekcji. **Sklonuj** przebieg potoku, aby utworzyć nową wersje roboczą potoku do edycji.

Przebiegi potoku są pogrupowane w [eksperymenty](concept-azure-machine-learning-architecture.md#experiments) w celu zorganizowania historii uruchamiania. Eksperyment można ustawić dla każdego uruchomienia potoku. 

## <a name="datasets"></a>Zestawy danych

Zestaw danych uczenia maszynowego ułatwia dostęp do danych i pracę z nim. W projektancie znajduje się wiele przykładowych zestawów danych, z których można eksperymentować. Możesz [zarejestrować](how-to-create-register-datasets.md) więcej zestawów danych, zgodnie z potrzebami.

## <a name="module"></a>Moduł

Moduł jest algorytmem, który można wykonać na danych. Projektant ma wiele modułów, począwszy od funkcji transferu danych przychodzących do szkolenia, oceniania i sprawdzania poprawności procesów.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie parametry tego modułu są wyświetlane w okienku Properties (Właściwości) po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model. Można ustawić zasoby obliczeniowe dla poszczególnych modułów w projektancie. 

![Właściwości modułu](./media/concept-designer/properties.png)

Aby uzyskać pomoc w poruszaniu się po bibliotece dostępnych algorytmów uczenia maszynowego, zobacz [Omówienie odwołania do modułu algorytm &](algorithm-module-reference/module-reference.md)

## <a name="compute-resources"></a><a name="compute"></a>Zasoby obliczeniowe

Użyj zasobów obliczeniowych z obszaru roboczego, aby uruchomić potok i hostować wdrożone modele jako punkty końcowe czasu rzeczywistego lub punkty końcowe potoku (dla wnioskowania wsadowego). Obsługiwane cele obliczeniowe to:

| Docelowy zasób obliczeniowy | Szkolenia | wdrażania |
| ---- |:----:|:----:|
| Obliczenia usługi Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Obiekty docelowe obliczeń są dołączone do [obszaru roboczego usługi Azure Machine Learning.](concept-workspace.md) Zarządzasz celami obliczeniowymi w obszarze roboczym w [usłudze Azure Machine Learning Studio (klasycznym).](https://ml.azure.com)

## <a name="deploy"></a>Wdrożenie

Aby przeprowadzić wnioskowanie w czasie rzeczywistym, należy wdrożyć potok jako **punkt końcowy w czasie rzeczywistym.** Punkt końcowy w czasie rzeczywistym tworzy interfejs między aplikacją zewnętrzną a modelem oceniania. Wywołanie punktu końcowego w czasie rzeczywistym zwraca wyniki prognozowania do aplikacji w czasie rzeczywistym. Aby nawiązać wywołanie punktu końcowego w czasie rzeczywistym, należy przekazać klucz interfejsu API, który został utworzony podczas wdrażania punktu końcowego. Punkt końcowy jest oparty na REST, popularnym wyborze architektury dla projektów programowania sieci web.

Punkty końcowe w czasie rzeczywistym muszą zostać wdrożone w klastrze usługi Azure Kubernetes.

Aby dowiedzieć się, jak wdrożyć model, zobacz [Samouczek: Wdrażanie modelu uczenia maszynowego z projektantem.](tutorial-designer-automobile-price-deploy.md)

## <a name="publish"></a>Publikowanie

Można również opublikować potok do **punktu końcowego potoku**. Podobnie jak w przypadku punktu końcowego w czasie rzeczywistym, punkt końcowy potoku umożliwia przesyłanie nowych uruchomień potoku z aplikacji zewnętrznych przy użyciu wywołań REST. Jednak nie można wysyłać ani odbierać danych w czasie rzeczywistym przy użyciu punktu końcowego potoku.

Opublikowane potoki są elastyczne, mogą być używane do szkolenia lub ponownego trenowania modeli, [wykonywania wnioskowania wsadowego,](how-to-run-batch-predictions-designer.md)przetwarzania nowych danych i wielu innych. Można opublikować wiele potoków do jednego punktu końcowego potoku i określić, która wersja potoku do uruchomienia.

Opublikowany potok działa na zasoby obliczeniowe zdefiniowane w wersji roboczej potoku dla każdego modułu.

Projektant tworzy ten sam [obiekt PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) co SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Przejście z interfejsu wizualnego do projektanta

Interfejs wizualny (wersja zapoznawcza) został zaktualizowany i jest teraz projektantem usługi Azure Machine Learning (wersja zapoznawcza). Projektant został prześliczony do użycia zaplecza opartego na potoku, który w pełni integruje się z innymi funkcjami usługi Azure Machine Learning. 

W wyniku tych aktualizacji niektóre pojęcia i terminy dla interfejsu wizualnego zostały zmienione lub zmieniono jego nazwę. Zobacz poniższą tabelę najważniejszych zmian koncepcyjnych. 

| Koncepcja w projektancie | Poprzednio w interfejsie wizualnym |
| ---- |:----:|
| Wersja robocza rurociągu | Experiment |
| Punkt końcowy w czasie rzeczywistym | Usługa sieci Web |

### <a name="migrating-to-the-designer"></a>Migracja do projektanta

Można konwertować istniejące eksperymenty interfejsu wizualnego i usług sieci web do potoków i punktów końcowych w czasie rzeczywistym w projektancie. Aby przeprowadzić migrację zasobów interfejsu wizualnego, należy wykonać następujące czynności:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego dzięki [samouczkowi: Przewidywanie ceny samochodów z projektantem](tutorial-designer-automobile-price-train-score.md)
* Dowiedz się, jak zmodyfikować istniejące [przykłady projektanta,](samples-designer.md) aby dostosować je do swoich potrzeb.

