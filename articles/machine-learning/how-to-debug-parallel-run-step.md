---
title: Debugowanie i rozwiązywanie problemów z parallelrunstepem
titleSuffix: Azure Machine Learning
description: Debugowanie i rozwiązywanie problemów z parallelrunstep w potokach uczenia maszynowego w usłudze Azure Machine Learning SDK dla języka Python. Dowiedz się typowe pułapki do tworzenia z potoków i porady, które pomogą Ci debugować skrypty przed i podczas zdalnego wykonywania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122966"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Debugowanie i rozwiązywanie problemów z parallelrunstepem
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z klasą [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) z [sdk usługi Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

## <a name="testing-scripts-locally"></a>Testowanie skryptów lokalnie

Zobacz [testowanie skryptów lokalnie sekcji](how-to-debug-pipelines.md#testing-scripts-locally) dla potoków uczenia maszynowego. Twój ParallelRunStep działa jako krok w potokach ml, więc ta sama odpowiedź dotyczy obu.

## <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Przejście od debugowania skryptu oceniania lokalnie do debugowania skryptu oceniania w rzeczywistym potoku może być trudnym skokiem. Aby uzyskać informacje na temat znajdowania dzienników w portalu, [w sekcji potoki uczenia maszynowego na temat debugowania skryptów z kontekstu zdalnego](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informacje w tej sekcji dotyczy również równoległego przebiegu kroku.

Na przykład plik `70_driver_log.txt` dziennika zawiera informacje z kontrolera, który uruchamia kod kroku uruchamiania równoległego uruchamiania.

Ze względu na rozproszony charakter zadań uruchamiania równoległego istnieją dzienniki z kilku różnych źródeł. Tworzone są jednak dwa skonsolidowane pliki, które dostarczają informacji wysokiego poziomu:

- `~/logs/overview.txt`: Ten plik zawiera informacje wysokiego poziomu o liczbie mini-partii (znanych również jako zadania) utworzonych do tej pory i liczbie przetwarzanych do tej pory mini-partii. Na tym końcu pokazuje wynik zadania. Jeśli zadanie nie powiodło się, wyświetli komunikat o błędzie i miejsce rozpoczęcia rozwiązywania problemów.

- `~/logs/sys/master.txt`: Ten plik udostępnia widok węzła głównego (znanego również jako koordynator) uruchomionego zadania. Obejmuje tworzenie zadań, monitorowanie postępu, wynik uruchomienia.

Dzienniki wygenerowane na podstawie skryptu wejścia przy użyciu pliku EntryScript.logger i instrukcji drukowania zostaną znalezione w następujących plikach:

- `~/logs/user/<ip_address>/Process-*.txt`: Ten plik zawiera dzienniki napisane z entry_script przy użyciu pliku EntryScript.logger. Zawiera również instrukcję drukowania (stdout) z entry_script.

Jeśli potrzebujesz pełnego zrozumienia, jak każdy węzeł wykonywane skrypt wynik, spójrz na dzienniki poszczególnych procesów dla każdego węzła. Dzienniki procesów można znaleźć `sys/worker` w folderze, pogrupowanym według węzłów procesu:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Ten plik zawiera szczegółowe informacje o każdej mini-partii, ponieważ jest ona pobierana lub uzupełniana przez pracownika. Dla każdej mini-partii ten plik zawiera:

    - Adres IP i identyfikator PID procesu roboczego. 
    - Całkowita liczba elementów, pomyślnie przetworzone elementy są liczone i liczba elementów, które nie powiodły się.
    - Czas rozpoczęcia, czas trwania, czas procesu i czas uruchamiania metody.

Można również znaleźć informacje na temat użycia zasobów procesów dla każdego procesu roboczego. Te informacje są w formacie CSV i znajdują się pod adresem `~/logs/sys/perf/<ip_address>/`. W przypadku jednego węzła pliki `~logs/sys/perf`zadań będą dostępne w obszarze . Na przykład podczas sprawdzania wykorzystania zasobów, spójrz na następujące pliki:

- `Process-*.csv`: Użycie zasobów procesu na pracownika. 
- `sys.csv`: Na dziennik węzła.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Jak zalogować się ze skryptu użytkownika z kontekstu zdalnego?
Rejestrator można pobrać z języka EntryScript, jak pokazano w poniższym przykładowym kodzie, aby dzienniki były wyświetlane w **dziennikach/folderze użytkownika** w portalu.

**Przykładowy skrypt wprowadzania przy użyciu rejestratora:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Jak mogę przekazać wszystkim moim pracownikom dane wejściowe boczne, takie jak plik lub pliki zawierające tabelę odnośników?

Skonstruuj obiekt [zestawu danych](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) zawierający dane wejściowe boczne i zarejestruj się w obszarze roboczym. Następnie można uzyskać do niego dostęp w skrypcie wnioskowania (na przykład w metodzie init() w następujący sposób:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Następne kroki

* Zobacz odwołanie zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) i [dokumentacji](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) dla klasy ParallelRunStep.

* Postępuj zgodnie z [zaawansowanym samouczkiem](tutorial-pipeline-batch-scoring-classification.md) na temat korzystania z potoków z równoległym krokiem uruchamiania.
