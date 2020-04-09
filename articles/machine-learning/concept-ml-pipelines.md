---
title: Co to są rurociągi ML
titleSuffix: Azure Machine Learning
description: W tym artykule poznaj zalety potoków uczenia maszynowego (ML), które można utworzyć za pomocą zestawu SDK usługi Azure Machine Learning dla języka Python. Potoki uczenia maszynowego są używane przez analityków danych do tworzenia, optymalizowanie i zarządzanie ich przepływów pracy uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 0cefa78b6f52cc67df8817f68a9b793ab86b2a7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878582"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Co to są potoki usługi Azure Machine Learning?

Potoki usługi Azure Machine Learning umożliwiają tworzenie przepływów pracy w projektach uczenia maszynowego. Te przepływy pracy mają szereg zalet: 

+ Prostota
+ Szybkość
+ Powtarzalność
+ Elastyczność
+ Przechowywanie wersji i śledzenie
+ Modułowość 
+ Kontrola jakości
+ Kontrola kosztów

Korzyści te stają się znaczące, gdy tylko projekt uczenia maszynowego wykracza poza czystą eksplorację i iterację. Nawet proste potoki jednoetapowe mogą być cenne. Projekty uczenia maszynowego są często w stanie złożonym i może to być ulga, aby dokładne wykonanie pojedynczego przepływu pracy było trywialnym procesem.

Dowiedz się, jak [utworzyć pierwszy potok](how-to-create-your-first-pipeline.md).

![Potoki uczenia maszynowego w usłudze Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Której technologii potoku platformy Azure należy używać?

Chmura platformy Azure udostępnia kilka innych potoków, z których każdy ma inny cel. W poniższej tabeli wymieniono różne potoki i do czego są one używane:

| Scenariusz | Podstawowa persona | Oferta platformy Azure | Oferta OSS | Rura kanoniczna | Mocnych | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Aranżacja modelu (uczenie maszynowe) | Analityk danych | Potoki usługi Azure Machine Learning | Potoki Kubeflow | Data -> Model | Dystrybucja, buforowanie, najpierw kod, ponowne użycie | 
| Aranżacja danych (przygotowanie danych) | Inżynier danych | [Potoki usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Przepływ powietrza | Dane -> Dane | Silnie typowany ruch. Działania zorientowane na dane. |
| Kod & aranżacji aplikacji (CI/CD) | Twórca aplikacji / Ops | [Potoki devops platformy Azure](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kod + Model -> aplikacji/usługi | Najbardziej otwarte i elastyczne wsparcie aktywności, kolejki do zatwierdzania, fazy z gating | 


## <a name="what-can-azure-ml-pipelines-do"></a>Do czego można wykonać potoki usługi Azure ML?

Potok usługi Azure Machine Learning jest niezależnie wykonywalnym przepływem pracy kompletnego zadania uczenia maszynowego. Podzadania są zamknięte jako serie kroków w potoku. Potok usługi Azure Machine Learning może być tak proste, jak ten, który wywołuje skrypt języka Python, więc _może_ zrobić prawie wszystko. Potoki _powinny koncentrować_ się na zadaniach uczenia maszynowego, takich jak:

+ Przygotowywanie danych, takie jak importowanie, weryfikowanie i czyszczenie, zniekształcanie i przekształcanie, normalizacja i przemieszczanie
+ Konfigurowanie trenowania obejmujące parametryzację argumentów, ścieżki plików i konfiguracje rejestrowania/raportowania
+ Szkolenia i walidacji skutecznie i wielokrotnie. Wydajność może wynikać z określania określonych podzbiorów danych, różnych zasobów obliczeniowych sprzętu, przetwarzania rozproszonego i monitorowania postępu
+ Wdrażanie, w tym przechowywanie wersji, skalowanie, aprowizowanie i kontrola dostępu 

Niezależne kroki umożliwiają wielu analitykom danych pracę nad tym samym potokiem w tym samym czasie bez nadmiernego opodatkowania zasobów obliczeniowych. Oddzielne kroki ułatwiają również używanie różnych typów/rozmiarów obliczeń dla każdego kroku.

Po zaprojektowaniu potoku często jest bardziej dostrajania wokół pętli szkoleniowej potoku. Po ponownym uruchomieniu potoku, uruchomienie przeskakuje do kroków, które należy uruchomić ponownie, takich jak zaktualizowany skrypt szkolenia. Kroki, które nie muszą być ponownie uruchomić są pomijane. Ta sama analiza dotyczy niezmienionych skryptów używanych do wykonania kroku. Ta funkcja ponownego użycia pomaga uniknąć uruchamiania kosztownych i czasochłonnych kroków, takich jak pozyskiwania danych i transformacji, jeśli dane podstawowe nie uległy zmianie.

Za pomocą usługi Azure Machine Learning można używać różnych zestawów narzędzi i struktur, takich jak PyTorch lub TensorFlow, dla każdego kroku w potoku. Platforma Azure koordynuje różne [obiekty docelowe obliczeń,](concept-azure-machine-learning-architecture.md) których używasz, dzięki czemu dane pośrednie mogą być udostępniane podrzędnym obiektom docelowym obliczeń.

[Metryki eksperymentów potoku](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) można śledzić bezpośrednio w witrynie Azure portal lub [na stronie docelowej obszaru roboczego (wersja zapoznawcza).](https://ml.azure.com) Po opublikowaniu potoku można skonfigurować punkt końcowy REST, który umożliwia ponowne uruchomienie potoku z dowolnej platformy lub stosu.

Krótko mówiąc, wszystkie złożone zadania cyklu życia uczenia maszynowego mogą być wspomagane rurociągami. Inne technologie potoku platformy Azure mają swoje mocne strony. [Potoki usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) doskonale sprawdzają się w pracy z danymi, a usługa Azure [Pipelines](https://azure.microsoft.com/services/devops/pipelines/) jest właściwym narzędziem do ciągłej integracji i wdrażania. Ale jeśli koncentrujesz się na uczeniu maszynowym, potoki usługi Azure Machine Learning prawdopodobnie będą najlepszym wyborem dla twoich potrzeb związanych z przepływem pracy. 

## <a name="what-are-azure-ml-pipelines"></a>Co to są potoki usługi Azure ML?

Potok usługi Azure ML wykonuje kompletny logiczny przepływ pracy z uporządkowaną sekwencją kroków. Każdy krok jest dyskretną akcją przetwarzania. Potoki są uruchamiane w kontekście [eksperymentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)usługi Azure Machine Learning.

We wczesnych etapach projektu uczenia maszynowego można mieć pojedynczy notes jupyter lub skrypt języka Python, który wykonuje całą pracę z konfiguracją obszaru roboczego i zasobów platformy Azure, przygotowaniem danych, konfiguracją uruchamiania, szkoleniem i sprawdzaniem poprawności. Ale tak jak funkcje i klasy szybko stają się lepsze niż pojedynczy imperatyw kodu, przepływy pracy uczenia maszynowego szybko stają się lepsze niż monolityczny notes lub skrypt. 

Modułowując zadania uczenia maszynowego, potoki obsługują konieczność informatyki, że składnik powinien "dobrze zrobić (tylko) jedną rzecz". Modułowość jest wyraźnie niezbędna do sukcesu projektu podczas programowania w zespołach, ale nawet podczas pracy w pojedynkę, nawet mały projekt ml wymaga oddzielnych zadań, z których każdy ma dużą złożoność. Zadania obejmują: konfigurację obszaru roboczego i dostęp do danych, przygotowanie danych, definicję modelu i konfigurację oraz wdrożenie. Podczas gdy dane wyjściowe jednego lub więcej zadań tworzą dane wejściowe do innego, dokładne szczegóły implementacji jednego zadania są w najlepszym wypadku nieistotne zakłócenia w następnym. W najgorszym stanie obliczeniowym jednego zadania może spowodować błąd w innym. 

### <a name="analyzing-dependencies"></a>Analizowanie zależności

Wiele ekosystemów programowania ma narzędzia, które organizują zależności zasobów, biblioteki lub kompilacji. Ogólnie rzecz biorąc te narzędzia używają sygnatur czasowych plików do obliczania zależności. Po zmianie pliku tylko on i jego osoby pozostające na jego utrzymaniu są aktualizowane (pobierane, ponownie kompilowane lub pakowane). Potoki usługi Azure ML znacznie rozszerzają tę koncepcję. Podobnie jak tradycyjne narzędzia kompilacji potoki obliczają zależności między krokami i wykonują tylko niezbędne ponowne obliczenia. 

Analiza zależności w potokach usługi Azure ML jest bardziej wyrafinowane niż proste sygnatury czasowe choć. Każdy krok może działać w innym środowisku sprzętowym i programowym. Przygotowanie danych może być procesem czasochłonnym, ale nie trzeba uruchamiać na sprzęcie z zaawansowanymi procesorami gpu, niektóre kroki mogą wymagać oprogramowania specyficznego dla systemu operacyjnego, można użyć szkolenia rozproszonego i tak dalej. Podczas gdy oszczędności kosztów optymalizacji zasobów mogą być znaczne, może być przytłaczające, aby ręcznie żonglować wszystkie różne odmiany sprzętu i zasobów oprogramowania. Jeszcze trudniej jest to zrobić, nie popełniając błędu w danych przesyłane między krokami. 

Potoki rozwiązać ten problem. Usługa Azure Machine Learning automatycznie organizuje wszystkie zależności między krokami potoku. Ta aranżacja może obejmować obracanie obrazów platformy Docker w górę i w dół, dołączanie i odłączanie zasobów obliczeniowych oraz przenoszenie danych między krokami w sposób spójny i automatyczny.

### <a name="reusing-results"></a>Ponowne zasiewanie wyników

Ponadto dane wyjściowe kroku może, jeśli wybierzesz, być ponownie. Jeśli określisz ponowne użycie jako możliwość i nie ma żadnych zależności nadrzędnych wyzwalających ponowne obliczanie, usługa potoku użyje buforowanej wersji wyników kroku. Takie ponowne użycie może znacznie skrócić czas opracowywania. Jeśli masz złożone zadanie przygotowania danych, prawdopodobnie ponownie go częściej niż jest to absolutnie konieczne. Rurociągi zwalniają cię z tego zmartwienia: jeśli to konieczne, krok będzie działać, jeśli nie, to nie będzie.

Wszystkie te analizy zależności, aranżacji i aktywacji są obsługiwane przez usługę Azure Machine Learning `Experiment`podczas `submit()`tworzenia wystąpienia obiektu [Potok,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) przekazywania go do programu , i wywołania . 

### <a name="coordinating-the-steps-involved"></a>Koordynacja działań

Podczas tworzenia i `Pipeline` uruchamiania obiektu występują następujące kroki wysokiego poziomu:

+ Dla każdego kroku usługa oblicza wymagania dotyczące:
    + Zasoby obliczeniowe sprzętu
    + Zasoby systemu operacyjnego (obrazy(-y) platformy Docker
    + Zasoby oprogramowania (zależności Conda / virtualenv)
    + Wprowadzanie danych 
+ Usługa określa zależności między krokami, co powoduje dynamiczne wykonanie wykresu
+ Po uruchomieniu każdego węzła na wykresie wykonywania:
    + Usługa konfiguruje niezbędne środowisko sprzętowe i programowe (być może ponowne przywykcie istniejących zasobów)
    + Etap przebiega, dostarczając informacje logowania i `Experiment` monitorowania do jego obiektu zawierającego
    + Po zakończeniu kroku jego wyjścia są przygotowywane jako wejścia do następnego kroku i/lub zapisywane w pamięci masowej
    + Zasoby, które nie są już potrzebne, są finalizowane i odłączane

![Kroki rurociągu](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Tworzenie potoków za pomocą SDK języka Python

W [panelu Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)potok jest `azureml.pipeline.core` obiektem języka Python zdefiniowanym w module. A [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) Obiekt zawiera uporządkowaną sekwencję jednego lub więcej [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) obiektów. Klasa `PipelineStep` jest abstrakcyjna, a rzeczywiste kroki będą nadawać podklasomom, takim jak [EstimatorStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)lub [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). Klasa [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) zawiera wielokrotnego stosowania sekwencji kroków, które mogą być współużytkowane przez potoki. A `Pipeline` działa w `Experiment`ramach .

Potok usługi Azure ML jest skojarzony z obszarem roboczym usługi Azure Machine Learning, a krok potoku jest skojarzony z obiektem docelowym obliczeniowym dostępnym w tym obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Tworzenie obszarów roboczych usługi Azure Machine Learning i zarządzanie nimi w witrynie Azure portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) lub Jakie są obiekty [docelowe obliczeń w usłudze Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

W usłudze Azure Machine Learning obiekt docelowy obliczeń to środowisko, w którym występuje faza uczenia maszynowego. Środowisko oprogramowania może być zdalna maszyna wirtualna, azure machine learning compute, azure databricks, usługa Azure Batch i tak dalej. Środowisko sprzętowe może się znacznie różnić w zależności od obsługi procesora GPU, pamięci, pamięci masowej i tak dalej. Można określić cel obliczeniowy dla każdego kroku, co daje szczegółową kontrolę nad kosztami. Można użyć bardziej lub mniej zaawansowanych zasobów dla określonych działań, ilości danych i wydajności potrzeb projektu. 

## <a name="building-pipelines-with-the-designer"></a>Budowa rurociągów z projektantem

Deweloperzy, którzy wolą powierzchni projektowania wizualnego można użyć projektanta usługi Azure Machine Learning do tworzenia potoków. Dostęp do tego narzędzia można uzyskać z zaznaczenia **projektanta** na stronie głównej obszaru roboczego.  Projektant umożliwia przeciąganie i upuszczanie kroków na powierzchnię projektową. Do szybkiego rozwoju można użyć istniejących modułów w całym spektrum zadań uczenia maszynowego; istniejące moduły obejmują wszystko, od transformacji danych do wyboru algorytmów do szkolenia do wdrożenia. Możesz też utworzyć w pełni niestandardowy potok, łącząc własne kroki zdefiniowane w skryptach Języka Python.

Podczas wizualnego projektowania potoków, dane wejściowe i wyjściowe kroku są wyświetlane w widoczny sposób. Można przeciągać i upuszczać połączenia danych, co pozwala szybko zrozumieć i zmodyfikować przepływ danych potoku.
 
![Przykład projektanta usługi Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Opis wykresu wykonania

Kroki w potoku może mieć zależności w innych krokach. Usługa potoku usługi Azure ML wykonuje pracę analizowania i organizowania tych zależności. Węzły w wynikowym "wykresie wykonywania" są etapy przetwarzania. Każdy krok może obejmować tworzenie lub ponowne stosowanie określonej kombinacji sprzętu i oprogramowania, ponowneużywanie wyników w pamięci podręcznej i tak dalej. Aranżacji usługi i optymalizacji tego wykresu wykonywania może znacznie przyspieszyć fazę ML i zmniejszyć koszty. 

Ponieważ kroki są uruchamiane niezależnie, obiekty do przechowywania danych wejściowych i wyjściowych, które przepływa między krokami muszą być zdefiniowane zewnętrznie. Jest to rola [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)i [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), obiektów. Te obiekty danych są skojarzone z obiektem [magazynu danych,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) który hermetyzuje ich konfiguracji magazynu. Klasa `PipelineStep` podstawowa jest zawsze `name` tworzona `inputs`z ciągiem, `outputs`listą i listą . Zwykle ma również listę `arguments` i często będzie miał `resource_inputs`listę . Podklasy zazwyczaj mają również dodatkowe argumenty `PythonScriptStep` (na przykład wymaga uruchomienia nazwy pliku i ścieżki skryptu). 

Wykres wykonywania jest acykliczny, ale potoki mogą być uruchamiane zgodnie z harmonogramem cyklicznym i mogą uruchamiać skrypty języka Python, które mogą zapisywać informacje o stanie w systemie plików, umożliwiając tworzenie złożonych profili. Jeśli projektujesz potok tak, aby niektóre kroki mogły być uruchamiane równolegle lub asynchronicznie, usługa Azure Machine Learning w sposób przejrzysty obsługuje analizę zależności i koordynację funkcji wyjęciech z wentylatorem i obsługą wentylatora. Zazwyczaj nie trzeba zajmować się szczegóły wykresu wykonywania, ale jest on dostępny za pośrednictwem [pipeline.graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) atrybutu. 


### <a name="a-simple-python-pipeline"></a>Prosty potok Pythona

Ten fragment kodu pokazuje obiekty i wywołania potrzebne `Pipeline`do utworzenia i uruchomienia podstawowego:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Fragment kodu rozpoczyna się od wspólnych obiektów usługi `Workspace`Azure `Datastore`Machine Learning, a , `Experiment`a , [a ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)i . Następnie kod tworzy obiekty do `input_data` `output_data`przechowywania i . Tablica `steps` zawiera jeden element, który `PythonScriptStep` będzie używać obiektów `compute_target`danych i uruchomić na . Następnie kod wystąpienia samego `Pipeline` obiektu, przekazywanie w obszarze roboczym i steps tablicy. Wywołanie `experiment.submit(pipeline)` rozpoczyna uruchamianie potoku usługi Azure ML. Wywołanie `wait_for_completion()` do blokowania, aż potok zostanie zakończony. 

Aby dowiedzieć się więcej na temat łączenia potoku z danymi, zobacz artykuły [Dostęp do danych w usłudze Azure Machine Learning](concept-data.md) i [przenoszenie danych do i między krokami potoku ml (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="best-practices-when-using-pipelines"></a>Najważniejsze wskazówki dotyczące korzystania z potoków

Jak widać, tworzenie potoku usługi Azure ML jest nieco bardziej złożone niż uruchamianie skryptu. Potoki wymagają kilku obiektów Języka Python, które mają być skonfigurowane i utworzone. 

Niektóre sytuacje, które sugerują użycie potoku:

* W środowisku zespołowym: podziel zadania uczenia maszynowego na wiele niezależnych kroków, aby deweloperzy mogli pracować i rozwijać swoje programy niezależnie. 

* Gdy we wdrożeniu lub w pobliżu: przybij konfigurację i użyj zaplanowanych i opartych na zdarzeniach operacji, aby być na bieżąco ze zmieniającymi się danymi.

* We wczesnych etapach projektu uczenia maszynowego lub pracy samodzielnie: użyj potoków do automatyzacji kompilacji. Jeśli zacząłeś martwić się o odtworzenie konfiguracji i stanu obliczeniowego przed wdrożeniem nowego pomysłu, jest to sygnał, który można rozważyć użycie potoku do automatyzacji przepływu pracy. 

Łatwo jest stać się entuzjastycznie nastawiony do ponownego korzystania z wyników w pamięci podręcznej, szczegółowej kontroli nad kosztami obliczeniowymi i izolacji procesu, ale potoki mają koszty. Niektóre anty-wzory obejmują:

* Korzystanie z rurociągów jako jedyny środek do oddzielenia problemów. Wbudowane funkcje, obiekty i moduły Pythona przechodzą długą drogę, aby uniknąć mylącego stanu programowego! Krok potoku jest znacznie droższe niż wywołanie funkcji.

* Duże sprzężenie między etapami rurociągu. Jeśli refaktoryzowanie kroku zależnego często wymaga modyfikowania wyników poprzedniego kroku, jest prawdopodobne, że oddzielne kroki są obecnie bardziej koszt niż korzyści. Inną wskazówką, że kroki są nadmiernie powiązane, są argumenty do kroku, które nie są danymi, ale flagi do kontrolowania przetwarzania. 

* Przedwczesna optymalizacja zasobów obliczeniowych. Na przykład, często istnieje kilka etapów przygotowania danych i często można zobaczyć "Och, oto miejsce, w którym mógłbym użyć do programowania równoległego, `MpiStep` ale oto miejsce, w którym mógłbym użyć `PythonScriptStep` mniej wydajnego celu obliczeniowego" i tak dalej. I być może, na dłuższą metę, tworzenie drobnoziarnistych kroków może okazać się opłacalne, zwłaszcza jeśli istnieje możliwość korzystania z buforowanych wyników, a nie zawsze ponownego obliczania. Ale potoki nie mają być substytutem natywnego `multiprocessing` modułu Pythona. 

Dopóki projekt nie zostanie rozmieszczony lub zbliża się do wdrożenia, potoki powinny być grubsze, a nie drobnoziarniste. Jeśli uważasz, że projekt uczenia maszynowego jako obejmujący _etapy_ i potok jako zapewniający pełny przepływ pracy, aby przenieść cię przez określony etap, jesteś na właściwej ścieżce. 

## <a name="key-advantages"></a>Kluczowe zalety

Najważniejsze zalety korzystania z potoków dla przepływów pracy uczenia maszynowego są:

|Kluczowa zaleta|Opis|
|:-------:|-----------|
|**Przebiegi nienadzorowane&nbsp;**|Zaplanuj kroki do uruchamiania równolegle lub w kolejności w sposób niezawodny i nienadzorowany. Przygotowanie i modelowanie danych może trwać dni lub tygodnie, a potoki umożliwiają skupienie się na innych zadaniach podczas uruchamiania procesu. |
|**Heterogeniczne obliczenia**|Użyj wielu potoków, które są niezawodnie koordynowane w różnych heterogenicznych i skalowalnych zasobach obliczeniowych i lokalizacjach magazynu. Efektywne wykorzystanie dostępnych zasobów obliczeniowych, uruchamiając poszczególne kroki potoku na różnych obiektach docelowych obliczeń, takich jak HDInsight, maszyny wirtualne do nauki o danych gpu i databricks.|
|**Możliwość ponownego użycia**|Tworzenie szablonów potoku dla określonych scenariuszy, takich jak przekwalifikowanie i ocenianie wsadowe. Wyzwalanie opublikowanych potoków z systemów zewnętrznych za pomocą prostych wywołań REST.|
|**Śledzenie i przechowywanie wersji**|Zamiast ręcznie śledzić dane i ścieżki wyników podczas iteracji, użyj zestawu SDK potoków, aby jawnie nazwać i wersją źródeł danych, danych wejściowych i wyjściowych. Można również zarządzać skryptami i danymi oddzielnie, aby zwiększyć produktywność.|
| **Modułowość** | Oddzielenie obszarów problemów i izolowanie zmian pozwala oprogramowaniu ewoluować w szybszym tempie i wyższej jakości. | 
|**Współpraca**|Potoki umożliwiają analitykom danych współpracę we wszystkich obszarach procesu projektowania uczenia maszynowego, jednocześnie mogąc jednocześnie pracować nad krokami potoku.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>Wybór odpowiedniej podklasy PipelineStep

Jest `PythonScriptStep` najbardziej elastyczną podklasą streszczenia `PipelineStep`. Inne podklasy, `EstimatorStep` takie jak `DataTransferStep` podklasy i można wykonać określone zadania z mniej kodu. Na przykład `EstimatorStep` można utworzyć tylko przez przekazanie w nazwie `Estimator`kroku, , i obliczeń docelowych. Można też zastąpić dane wejściowe i wyjściowe, parametry potoku i argumenty. Aby uzyskać więcej informacji, zobacz [Szkolenie modeli z usługą Azure Machine Learning przy użyciu estymatora.](how-to-train-ml-models.md) 

Ułatwia `DataTransferStep` przenoszenie danych między źródłami danych i pochłaniaczami. Kod do wykonania tego transferu ręcznie jest prosty, ale powtarzalny. Zamiast tego można po `DataTransferStep` prostu utworzyć z nazwą, odwołania do źródła danych i ujścia danych oraz miejsce docelowe obliczeń. Potok [usługi Azure Machine Learning notesu z danymi TransferferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) demonstruje tę elastyczność.

## <a name="modules"></a>Moduły

Podczas gdy kroki potoku umożliwiają ponowne użycie wyników poprzedniego uruchomienia, w wielu przypadkach konstrukcja kroku zakłada, że skrypty i pliki zależne muszą być dostępne lokalnie. Jeśli naukowiec danych chce tworzyć na istniejącym kodzie, skrypty i zależności często muszą być klonowane z oddzielnego repozytorium.

Moduły są podobne w użyciu do kroków potoku, ale zapewniają przechowywanie wersji ułatwione za pośrednictwem obszaru roboczego, co umożliwia współpracę i możliwość ponownego użycia na dużą skalę. Moduły są przeznaczone do ponownego użycia w wielu potokach i mogą ewoluować, aby dostosować określone obliczenia do różnych przypadków użycia. Użytkownicy mogą wykonywać następujące zadania za pośrednictwem obszaru roboczego, bez korzystania z zewnętrznych repozytoriów:

* Tworzenie nowych modułów i publikowanie nowych wersji istniejących modułów
* Przestarzałe istniejące wersje
* Oznaczanie wersji wyłączonych, aby uniemożliwić konsumentom korzystanie z tej wersji
* Wyznaczanie wersji domyślnych
* Pobieranie modułów według wersji z obszaru roboczego, aby upewnić się, że zespoły używają tego samego kodu

Zobacz [przykłady](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) kodu notesu dotyczące tworzenia, łączenia i używania modułów w potokach usługi Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

Potoki usługi Azure ML to zaawansowana funkcja, która rozpoczyna dostarczanie wartości na wczesnych etapach rozwoju. Wartość wzrasta wraz ze wzrostem zespołu i projektu. W tym artykule wyjaśniono, jak potoki są określone za pomocą narzędzia Azure Machine Learning Python SDK i zaaranżowane na platformie Azure. Widzieliście kilka podstawowych kodu źródłowego i został `PipelineStep` wprowadzony do kilku klas, które są dostępne. Powinieneś mieć poczucie, kiedy używać potoków usługi Azure ML i jak platforma Azure je uruchamia. 


+ Dowiedz się, jak [utworzyć pierwszy potok](how-to-create-your-first-pipeline.md).

+ Dowiedz się, jak [uruchamiać prognozy partii na dużych danych](tutorial-pipeline-batch-scoring-classification.md ).

+ Zobacz dokumenty referencyjne SDK dla [kroków rdzenia i](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) [potoku potoku.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

+ Wypróbuj przykładowe notesy Jupyter prezentujące [potoki usługi Azure Machine Learning.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) Dowiedz się, jak [uruchomić notesy w celu zapoznania się z tą usługą.](samples-notebooks.md)
