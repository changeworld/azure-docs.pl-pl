---
title: Debugowanie i rozwiązywanie problemów z ParallelRunStep
titleSuffix: Azure Machine Learning
description: Debuguj i rozwiązywanie problemów z potokami uczenia maszynowego w zestawie Azure Machine Learning SDK dla języka Python. Poznaj typowe pułapek na potrzeby programowania przy użyciu potoków oraz porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: db17dca1a859807f4d92b4953ec0e2785a3a4160
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852330"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Debugowanie i rozwiązywanie problemów przy użyciu ParallelRun
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z klasą [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) z poziomu [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Lokalne testowanie skryptów

Zobacz [sekcję testowanie skryptów lokalnie](how-to-debug-pipelines.md#testing-scripts-locally) dla potoków uczenia maszynowego. ParallelRunStep działa jako krok w potokach ML, dlatego ta sama odpowiedź ma zastosowanie do obu tych metod.

## <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Przejście od debugowania skryptu oceniania lokalnie w celu debugowania skryptu oceniania w rzeczywistym potoku może być trudne. Aby uzyskać informacje na temat wyszukiwania dzienników w portalu, należy zapoznać się z [sekcją potoki uczenia maszynowego w przypadku debugowania skryptów z kontekstu zdalnego](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informacje zawarte w tej sekcji dotyczą również przebiegu wnioskowania partii.

Na przykład plik dziennika `70_driver_log.txt` również zawiera: 

* Wszystkie wydrukowane instrukcje podczas wykonywania skryptu.
* Ślad stosu skryptu.

Ze względu na dystrybuowany charakter zadań wnioskowania partii istnieją dzienniki z kilku różnych źródeł. Jednak tworzone są dwa skonsolidowane pliki, które udostępniają informacje wysokiego poziomu:

- `~/logs/overview.txt`: ten plik zawiera ogólne informacje o liczbie pośrednich partii (nazywanych również zadaniami), które zostały utworzone do tej pory i wiele z nich przetworzono do tej pory. Na tym końcu zostanie wyświetlony wynik zadania. Jeśli zadanie nie powiodło się, zostanie wyświetlony komunikat o błędzie i miejsce, w którym należy rozpocząć rozwiązywanie problemów.

- `~/logs/master.txt`: ten plik zawiera widok węzła głównego (znanego również jako koordynator) uruchomionego zadania. Obejmuje tworzenie zadań, monitorowanie postępu, wynik uruchomienia.

Jeśli potrzebujesz pełnego zrozumieć, jak każdy węzeł wykonał skrypt wynikowy, sprawdź poszczególne dzienniki procesów dla każdego węzła. Dzienniki procesów można znaleźć w folderze `worker` pogrupowanym według węzłów procesu roboczego:

- `~/logs/worker/<ip_address>/Process-*.txt`: ten plik zawiera szczegółowe informacje o każdej z grup minimalnej, które są pobierane lub wykonywane przez proces roboczy. Dla każdej grupy mini-Batch ten plik zawiera:

    - Adres IP i Identyfikator PID procesu roboczego. 
    - Całkowita liczba elementów i liczba pomyślnie przetworzonych elementów. 
    - Godzina rozpoczęcia i zakończenia w zegarze ściany (`start1` i `end1`). 
    - Godzina rozpoczęcia i zakończenia w czasie trwania procesora (`start2` i `end2`). 

Możesz również znaleźć informacje o użyciu zasobów procesów dla każdego pracownika. Te informacje są w formacie CSV i znajdują się w `~/logs/performance/<ip_address>/`. Na przykład podczas sprawdzania wykorzystania zasobów zapoznaj się z następującymi plikami:

- `process_resource_monitor_<ip>_<pid>.csv`: za użycie zasobów procesu roboczego. 
- `sys_resource_monitor_<ip>.csv`: dziennik na węzeł.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Jak mogę dziennika ze skryptu użytkownika ze zdalnego kontekstu?
Można skonfigurować Rejestrator z poniższymi krokami, aby dzienniki były wyświetlane w folderze **Logs/users** w portalu:
1. Zapisz pierwszą sekcję kodu poniżej w pliku entry_script_helper. PR i umieść plik w tym samym folderze co skrypt wejściowy. Ta klasa Pobiera ścieżkę w AmlCompute. W przypadku testu lokalnego można zmienić `get_working_dir()`, aby zwracał folder lokalny.
2. Skonfiguruj rejestrator w metodzie `init()`, a następnie użyj go. Druga sekcja kodu poniżej jest przykładem. 

**entry_script_helper. PR:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Przykładowy skrypt wprowadzania przy użyciu rejestratora:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Następne kroki

* Zobacz odwołanie do zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-contrib-Pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) i [dokumentacji](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) klasy ParallelRunStep.

* Postępuj zgodnie z [zaawansowanym samouczkiem](tutorial-pipeline-batch-scoring-classification.md) dotyczącym tworzenia wsadowego oceniania przy użyciu potoków.
