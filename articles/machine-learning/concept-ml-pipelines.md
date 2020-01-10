---
title: Co to są potoki ML
titleSuffix: Azure Machine Learning
description: W tym artykule poznasz zalety potoków uczenia maszynowego (ML), które można skompilować za pomocą zestawu Azure Machine Learning SDK dla języka Python. Potoki uczenia maszynowego są używane przez analityków danych do kompilowania i optymalizowania przepływów pracy uczenia maszynowego oraz zarządzania nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: c93c936664f65e7846f6c4ad82d9aead973fa129
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772605"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Co to są Azure Machine Learning potoki?

Potoki Azure Machine Learning umożliwiają tworzenie przepływów pracy w projektach uczenia maszynowego. Te przepływy pracy mają różne zalety: 

+ Prostota
+ Szybkość
+ Powtarzalność
+ Elastyczność
+ Przechowywanie wersji i śledzenie
+ Modułowość 
+ Kontrola jakości
+ Kontrola kosztów

Te korzyści stają się istotne, gdy tylko projekt uczenia maszynowego przejdzie poza czystą eksplorację i iterację. Nawet proste potoki jednoetapowe mogą być cenne. Projekty uczenia maszynowego często są złożone i mogą być niezbędne do precyzyjnego wykonania pojedynczego przepływu pracy.

Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md).

![Potoki uczenia maszynowego w Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Której technologii potoku platformy Azure należy używać?

Chmura systemu Azure udostępnia kilka innych potoków, z których każdy ma inny cel. W poniższej tabeli wymieniono różne potoki i ich zastosowania:

| Potok | Wyniki działania | Rura kanoniczna |
| ---- | ---- | ---- |
| Potoki Azure Machine Learning | Definiuje przepływy pracy uczenia maszynowego wielokrotnego użytku, które mogą być używane jako szablon scenariuszy uczenia maszynowego. | Model > danych |
| [Potoki usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Grupuje działania związane z przenoszeniem, przekształcaniem i kontrolą danych, które są konieczne do wykonania zadania.  | Dane > danych |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Ciągła integracja i dostarczanie aplikacji na dowolną platformę/dowolną chmurę  | Usługa Code-> App/Service |

## <a name="what-can-azure-ml-pipelines-do"></a>Do czego służą potoki Azure ML?

Potok Azure Machine Learning to niezależnie wykonywalny przepływ pracy kompletnego zadania uczenia maszynowego. Podzadania są hermetyzowane jako serie kroków w potoku. Potok Azure Machine Learning może być taki sam jak taki, który wywołuje skrypt w języku Python, więc _może_ dochodzić do wszystkiego. Potoki _powinny_ skupić się na zadaniach uczenia maszynowego, takich jak:

+ Przygotowywanie danych, takie jak importowanie, sprawdzanie poprawności i czyszczenie, munging i przekształcanie, normalizacja i przemieszczanie
+ Konfiguracja szkoleń obejmująca argumenty parametryzacja, ścieżki i rejestrowanie/konfiguracje raportowania
+ Wydajnie i wielokrotnie sprawdzaj, które mogą obejmować określanie określonych podzestawów danych, różnych zasobów obliczeniowych sprzętu, przetwarzania rozproszonego i monitorowania postępu
+ Wdrażanie, w tym przechowywanie wersji, skalowanie, Inicjowanie obsługi i kontrola dostępu 

Niezależne kroki umożliwiają wielu analitykom danych jednoczesne działanie w tym samym potoku bez zasobów obliczeniowych. Osobne kroki ułatwiają również korzystanie z różnych typów i rozmiarów obliczeniowych dla każdego kroku.

Po zaprojektowaniu potoku jest często bardziej precyzyjne dostosowywanie pętli szkoleniowej potoku. Po ponownym uruchomieniu potoku przebieg jest przeskakuje do kroków, które należy uruchomić ponownie, takich jak zaktualizowany skrypt szkoleniowy. Kroki, które nie wymagają ponownego uruchomienia, są pomijane. Ta sama analiza odnosi się do niezmienionych skryptów używanych do osiągnięcia kroku. Ta funkcja ponownego wykorzystania pozwala uniknąć kosztownego i intensywnego wykonywania kroków, takich jak pozyskiwanie i Przekształcanie danych, jeśli dane podstawowe nie uległy zmianie.

Za pomocą Azure Machine Learning można używać różnych zestawów narzędzi i platform, takich jak PyTorch lub TensorFlow, dla każdego etapu w potoku. Platforma Azure koordynuje różne używane [cele obliczeń](concept-azure-machine-learning-architecture.md) , dzięki czemu dane pośrednie mogą być współużytkowane z podrzędnymi obiektami docelowymi obliczeń.

[Metryki dla eksperymentów potoku można śledzić](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) bezpośrednio w Azure Portal lub na [stronie docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com). Po opublikowaniu potoku można skonfigurować punkt końcowy REST, który umożliwia ponowne uruchomienie potoku z poziomu dowolnej platformy lub stosu.

Krótko mówiąc, wszystkie złożone zadania cyklu życia uczenia maszynowego mogą być przydatne w przypadku potoków. Inne technologie potoku platformy Azure mają własne siły, takie jak [potoki Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) do pracy z danymi i [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) w celu zapewnienia ciągłej integracji i wdrażania. Ale jeśli fokus jest uczeniem maszynowym, Azure Machine Learning potoki mogą być najlepszym wyborem dla potrzeb przepływu pracy. 

## <a name="what-are-azure-ml-pipelines"></a>Co to są potoki Azure ML?

Potok Azure ML wykonuje pełny logiczny przepływ pracy z uporządkowaną sekwencją kroków. Każdy krok to dyskretna akcja przetwarzania. Potoki są uruchamiane w kontekście Azure Machine Learning [eksperymentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py).

W bardzo wczesnych etapach projektu ML warto mieć jeden Jupyter Notes lub skrypt języka Python, który wykonuje wszystkie zadania związane z obszarem roboczym platformy Azure i konfiguracją zasobów, przygotowywaniem danych, konfiguracją, szkoleniem i sprawdzaniem poprawności. Jednak podobnie jak funkcje i klasy szybko stają się preferowane jako jeden niezależny blok kodu, przepływy pracy w ML szybko stają się preferowane w postaci monolitycznego notesu lub skryptu. 

Za pomocą zadań modularizing ML, potoki obsługują naukę komputera, ponieważ składnik powinien mieć tylko jeden z nich. Modularność jest jasno niezbędna do osiągnięcia sukcesu w przypadku programowania w zespołach, ale nawet w przypadku pracy samego, nawet w przypadku niewielkiej części projektu, z których każdy ma dobrą złożoność. Zadania obejmują: Konfiguracja obszaru roboczego i dostęp do danych, przygotowywanie danych, definiowanie modelu i konfiguracja oraz wdrażanie. Podczas gdy dane wyjściowe jednego lub większej liczby zadań tworzą dane wejściowe, dokładne szczegóły implementacji każdego z nich są, co jest najlepszym, nieistotnymi odwracaniem w następnym. W najgorszym stanie obliczeniowym jednego zadania może spowodować błąd w innym. 

### <a name="analyzing-dependencies"></a>Analizowanie zależności

Wiele ekosystemów programistycznych ma narzędzia, które organizują zależności zasobów, bibliotek lub kompilacji. Ogólnie rzecz biorąc, te narzędzia używają sygnatur czasowych plików do obliczenia zależności. Gdy plik zostanie zmieniony, tylko jego elementy zależne są aktualizowane (pobrane, ponownie skompilowane lub spakowane). Potoki usługi Azure ML znacznie poszerzają tę koncepcję. Podobnie jak w przypadku tradycyjnych narzędzi do kompilowania, potoki obliczają zależności między krokami i wykonują wymagane ponowne obliczenia. 

Analiza zależności w potokach Azure ML jest bardziej zaawansowana niż proste sygnatury czasowe. Każdy krok może działać w innym środowisku sprzętu i oprogramowania. Przygotowanie danych może być czasochłonnym procesem, ale nie musi być uruchamiane na sprzęcie z zaawansowanymi procesorami GPU, niektóre kroki mogą wymagać oprogramowania specyficznego dla systemu operacyjnego, dlatego warto używać szkolenia rozproszonego i tak dalej. Oszczędności wynikające z optymalizacji zasobów mogą być znaczne, dlatego można je przeciążyć, aby ręcznie Juggle wszystkie różne odmiany zasobów sprzętowych i programowych. Jest to jeszcze trudniejsze, aby wykonać wszystkie czynności, które nie wykonują żadnego błędu w danych przesyłanych między krokami. 

Potoki rozwiązują ten problem. Azure Machine Learning automatycznie organizuje wszystkie zależności między krokami potoku. Ta aranżacja może obejmować nadzielenie i zmniejszenie obrazów platformy Docker, dołączanie i odłączanie zasobów obliczeniowych oraz przeniesienie danych między krokami w sposób spójny i automatyczny.

### <a name="reusing-results"></a>Używanie wyników

Ponadto dane wyjściowe kroku mogą być ponownie używane. Jeśli określisz ponowne użycie jako możliwe i nie zostaną wyzwolone żadne zależności nadrzędne, usługa potoku będzie korzystać z zbuforowanej wersji wyników kroku. Takie ponowne użycie może znacznie skrócić czas projektowania. Jeśli masz złożone zadanie przygotowania danych, prawdopodobnie będzie ono często uruchamiane ponownie, niż jest to absolutnie konieczne. Potoku zwalniają Cię z tego powodu: w razie potrzeby krok zostanie uruchomiony, jeśli nie.

Wszystkie te analizy zależności, aranżacja i aktywacja są obsługiwane przez Azure Machine Learning podczas tworzenia wystąpienia obiektu [potoku](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) , przekazywania go do `Experiment`i wywoływania `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Koordynacja pozostałych kroków

Podczas tworzenia i uruchamiania obiektu `Pipeline` należy wykonać następujące czynności wysokiego poziomu:

+ Dla każdego kroku usługa oblicza wymagania dla:
    + Zasoby obliczeniowe sprzętu
    + Zasoby systemu operacyjnego (obrazy platformy Docker)
    + Zasoby oprogramowania (zależności Conda/virtualenv)
    + Dane wejściowe 
+ Usługa określa zależności między krokami, co prowadzi do dynamicznego wykresu wykonawczego
+ Po uruchomieniu każdego węzła w grafie wykonywania:
    + Usługa konfiguruje niezbędne środowisko sprzętowe i programowe (prawdopodobnie przy użyciu istniejących zasobów)
    + Ten krok jest uruchamiany, dostarczając informacje o rejestrowaniu i monitorowaniu do zawierającego go obiektu `Experiment`
    + Po zakończeniu kroku jego wyniki są przygotowywane jako dane wejściowe do następnego kroku i/lub zapisywane w magazynie
    + Zasoby, które nie są już potrzebne, są finalizowane i odłączone

![Kroki potoku](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Kompilowanie potoków przy użyciu zestawu SDK języka Python

W [zestawie Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)potok jest obiektem języka Python zdefiniowanym w module `azureml.pipeline.core`. Obiekt [potoku](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) zawiera uporządkowaną sekwencję co najmniej jednego obiektu [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) . Klasa `PipelineStep` jest abstrakcyjna i rzeczywiste kroki będą podklasami, takimi jak [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)lub [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). Klasa [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) przechowuje sekwencję kroków wielokrotnego użytku, które mogą być współużytkowane między potokami. `Pipeline` jest uruchamiany jako część `Experiment`.

Potok Azure ML jest skojarzony z obszarem roboczym Azure Machine Learning, a krok potoku jest skojarzony z elementem docelowym obliczeń dostępnym w tym obszarze roboczym. Aby uzyskać więcej informacji, zobacz temat [Tworzenie obszarów roboczych Azure Machine Learning i zarządzanie nimi w Azure Portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) lub [co to są cele obliczeniowe w Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).

W Azure Machine Learning obiektem docelowym obliczeń jest środowisko, w którym występuje faza ML. Środowisko oprogramowania może być zdalną maszyną wirtualną, Azure Machine Learning COMPUTE, Azure Databricks, Azure Batch itd. Środowisko sprzętowe może być również różne, w zależności od obsługi procesora GPU, pamięci, magazynu i tak dalej. Można określić element docelowy obliczeń dla każdego kroku, który zapewnia szczegółową kontrolę nad kosztami. Możesz użyć bardziej zaawansowanych zasobów dla konkretnej akcji, ilości danych i potrzeb związanych z wydajnością projektu. 

## <a name="building-pipelines-with-the-designer"></a>Kompilowanie potoków przy użyciu narzędzia Projektant

Deweloperzy, którzy preferują powierzchnię projektowania wizualizacji, mogą używać projektanta Azure Machine Learning do tworzenia potoków. Możesz uzyskać dostęp do tego narzędzia z poziomu wybranego **projektanta** na stronie głównej obszaru roboczego.  Projektant umożliwia przeciąganie i upuszczanie kroków na powierzchnię projektu. W celu szybkiego rozwoju można korzystać z istniejących modułów w całym spektrum zadań w ML. istniejące moduły obejmują wszystko, od przekształcania danych do wybranego algorytmu, do szkolenia do wdrożenia. Można też utworzyć w pełni niestandardowy potok, łącząc własne kroki zdefiniowane w skryptach języka Python.

Podczas wizualnego projektowania potoków dane wejściowe i wyjściowe kroku są wyświetlane w sposób widoczny. Możesz przeciągać i upuszczać dane, co pozwala szybko zrozumieć i zmodyfikować przepływu danych potoku.
 
![Przykład projektanta Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Informacje na temat grafu wykonywania

Kroki w potoku mogą mieć zależności od innych kroków. Usługa potoku Azure ML wykonuje analizę i organizowanie tych zależności. Węzły na "grafie wykonywania" są krokami przetwarzania. Każdy krok może polegać na utworzeniu lub ponownej użyciu określonej kombinacji sprzętu i oprogramowania, ponownego użycia zbuforowanych wyników i tak dalej. Dzięki aranżacji i optymalizacji usługi w tym grafie wykonywania można znacznie przyspieszyć fazę ML i obniżyć koszty. 

Ponieważ kroki są uruchamiane niezależnie, obiekty do przechowywania danych wejściowych i wyjściowych, które przepływ między krokami muszą być zdefiniowane zewnętrznie. Jest to rola elementów [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)i skojarzonych klas. Te obiekty danych są skojarzone z obiektem [magazynu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) obiektów, który hermetyzuje konfigurację magazynu. Klasa bazowa `PipelineStep` jest zawsze tworzona z ciągiem `name`, listą `inputs`oraz listą `outputs`. Zazwyczaj ma także listę `arguments` i często zawiera listę `resource_inputs`. Podklasy zazwyczaj mają również dodatkowe argumenty (na przykład `PythonScriptStep` wymaga, aby nazwa pliku i ścieżka do uruchomienia skryptu). 

Wykres wykonawczy jest niespójny, ale potoki można uruchamiać w cyklicznym harmonogramie i uruchamiać skrypty języka Python, które mogą zapisywać informacje o stanie w systemie plików, dzięki czemu można tworzyć złożone profile. W przypadku zaprojektowania potoku w taki sposób, aby pewne czynności mogły działać równolegle lub asynchronicznie, Azure Machine Learning niewidocznym w sposób przezroczysty przeanalizować zależności i koordynacja wentylatorów oraz wentylatorów. Zazwyczaj nie trzeba niczego zachodzić na szczegóły wykresu wykonawczego, ale jest on dostępny za pośrednictwem atrybutu [Pipeline. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) . 


### <a name="a-simple-python-pipeline"></a>Prosty potok Python

Ten fragment kodu przedstawia obiekty i wywołania, które są konieczne do utworzenia i uruchomienia podstawowego `Pipeline`:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Fragment kodu rozpoczyna się od typowego Azure Machine Learning obiektów, `Workspace`, `Datastore`, [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)i `Experiment`. Następnie kod tworzy obiekty do przechowania `input_data` i `output_data`. Tablica `steps` zawiera pojedynczy element, `PythonScriptStep`, który będzie używać obiektów danych i działa w `compute_target`. Następnie kod tworzy wystąpienie obiektu `Pipeline`, przekazując w obszarze roboczym i w tablicy kroków. Wywołanie `experiment.submit(pipeline)` rozpoczyna uruchomienie potoku usługi Azure ML. Wywołanie `wait_for_completion()` bloków do momentu zakończenia potoku. 

Aby dowiedzieć się więcej na temat łączenia potoku z danymi, zobacz artykuły, [jak uzyskać dostęp do danych](how-to-access-data.md) i [jak rejestrować zbiory](how-to-create-register-datasets.md). 

## <a name="best-practices-when-using-pipelines"></a>Najlepsze rozwiązania w przypadku korzystania z potoków

Jak widać, tworzenie potoku Azure ML jest nieco bardziej skomplikowane niż uruchamianie skryptu. Potoki wymagają skonfigurowania i utworzenia kilku obiektów w języku Python. 

Niektóre sytuacje, które sugerują użycie potoku:

* W środowisku zespołu: Podziel zadania ML na wiele niezależnych kroków, aby deweloperzy mogli działać i rozwijać programy niezależnie od siebie. 

* W przypadku wdrożenia w programie lub niemal: Nail konfigurację i użycie operacji zaplanowanych i opartych na zdarzeniach, aby zachować zmiany danych.

* Na wczesnych etapach projektu ML lub samej pracy: Użyj potoków, aby zautomatyzować kompilację. Jeśli przed wdrożeniem nowego pomysłu zachodzi konieczność ponownego utworzenia konfiguracji i stanu obliczeniowego, to sygnał, który można rozważyć przy użyciu potoku do automatyzacji przepływu pracy. 

Można łatwo stać r o ponowne używanie buforowanych wyników, szczegółową kontrolę nad kosztami obliczeniowymi i izolacją procesów, ale potoki mają koszty. Niektóre z tych wzorców obejmują:

* Korzystanie z potoków jako jedynego środka do oddzielenia obaw. Wbudowane funkcje, obiekty i moduły w języku Python umożliwiają długotrwałe uniknięcie niemylącego stanu. Krok potoku jest znacznie bardziej kosztowny niż wywołanie funkcji.

* Duże sprzęganie między krokami potoku. Jeśli Refaktoryzacja krok zależny często wymaga modyfikacji danych wyjściowych poprzedniego kroku, istnieje duże ryzyko, że poszczególne kroki są obecnie bardziej kosztowne niż korzyść. Kolejną wskazówki, że kroki są zbyt powiązane z argumentami, które nie są danymi, ale flagi sterujące przetwarzaniem. 

* Przedwcześnie Optymalizacja zasobów obliczeniowych. Na przykład istnieje często kilka etapów przygotowywania danych, a jedna z nich często widzi "Niestety, to miejsce, w którym można użyć `MpiStep` do programowania równoległego, ale jest to miejsce, w którym można użyć `PythonScriptStep` z mniej zaawansowanym celem obliczeń" i tak dalej. I może w długim czasie tworzyć szczegółowe kroki, takie jak to, które mogą okazać się wartościowa, zwłaszcza jeśli istnieje możliwość użycia zbuforowanych wyników zamiast zawsze ponownego obliczania. Ale potoki nie są przeznaczone do podstawiania dla modułu `multiprocessing`. 

Dopóki projekt nie osiągnie dużego lub niemal wdrożenia, potoki powinny być grubsze, a nie szczegółowe. Jeśli myślisz o projekcie ML jako obejmującej _etapy_ i potoku, co zapewnia kompletny przepływ pracy w celu przechodzenia przez określony etap, jesteś w odpowiedniej ścieżce. 

## <a name="key-advantages"></a>Najważniejsze zalety

Najważniejsze zalety używania potoków dla przepływów pracy usługi Machine Learning to:

|Kluczową zaletą|Opis|
|:-------:|-----------|
|**Nienadzorowane&nbsp;działa**|Zaplanuj wykonywanie kroków równolegle lub w kolejności w sposób niezawodny i nienadzorowany. Przygotowanie i modelowanie danych może trwać ostatni dzień lub tygodnie, a potoki umożliwiają skoncentrowanie się na innych zadaniach w trakcie działania procesu. |
|**Obliczenia heterogenicznych**|Używaj wielu potoków, które są niezawodne, skoordynowane w heterogenicznych i skalowalnych zasobach obliczeniowych i lokalizacjach magazynu. Wydajnie korzystaj z dostępnych zasobów obliczeniowych, uruchamiając poszczególne etapy potoku w różnych obiektach docelowych obliczeń, takich jak HDInsight, maszyny wirtualne do nauki o danych procesora GPU i kostki datacegły.|
|**Możliwość ponownego wykorzystania**|Tworzenie szablonów potoku dla konkretnych scenariuszy, takich jak ponowne szkolenie i ocenianie partii. Wyzwalaj opublikowane potoki z systemów zewnętrznych za pośrednictwem prostych wywołań REST.|
|**Śledzenie i przechowywania wersji**|Zamiast ręcznego śledzenia danych i ścieżek wyników podczas iteracji Użyj zestawu SDK potoków, aby jawnie nazwać i w wersji źródła danych, dane wejściowe i wyjściowe. Możesz również zarządzać skryptami i danymi osobno w celu zwiększenia produktywności.|
| **Modułowość** | Rozdzielenie obszarów problemów i izolacja zmian pozwala na szybsze rozwijanie się oprogramowania o wyższej jakości. | 
|**Współpraca**|Potoki umożliwiają analitykom danych współpracę we wszystkich obszarach procesu projektowania uczenia maszynowego, jednocześnie umożliwiając jednocześnie pracę nad krokami potoku.|

## <a name="next-steps"></a>Następne kroki

Potoki Azure ML to zaawansowana funkcja, która rozpoczyna dostarczanie wartości na wczesnych etapach tworzenia oprogramowania. Wartość zwiększa się wraz ze wzrostem zespołu i projektu. W tym artykule wyjaśniono, jak potoki są określone za pomocą zestawu SDK języka Python Azure Machine Learning i zorganizowane na platformie Azure. Zaobserwowano jakiś podstawowy kod źródłowy i wprowadzono kilka `PipelineStep` dostępnych klas. W przypadku korzystania z potoków Azure ML i sposobu ich uruchamiania na platformie Azure należy mieć sens. 


+ Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md).

+ Dowiedz się, jak [uruchamiać przewidywania wsadowe w przypadku dużych ilości danych](tutorial-pipeline-batch-scoring-classification.md ).

+ Zapoznaj się z dokumentacją zestawu SDK dotyczącej głównych [kroków i etapów](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) [potoku](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) .

+ Wypróbuj przykładowe notesy Jupyter, które przedstawiają [potoki Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Dowiedz się, jak [uruchamiać notesy w celu eksplorowania tej usługi](samples-notebooks.md).
