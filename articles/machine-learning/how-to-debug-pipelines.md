---
title: Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego w module SDK usługi Azure Machine Learning dla języka Python. Poznaj typowe pułapki dotyczące tworzenia potoków i porady ułatwiające debugowanie skryptów przed i podczas zdalnego wykonywania. Dowiedz się, jak używać programu Visual Studio Code do interaktywnego debugowania potoków uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 578e935ee742ad476aeafb53670f0a92035249e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064082"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z [potokami uczenia maszynowego](concept-ml-pipelines.md) w narzędziu [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) i [projektancie usługi Azure Machine Learning (wersja zapoznawcza).](https://docs.microsoft.com/azure/machine-learning/concept-designer) Podano informacje na temat:

* Debugowanie przy użyciu zestawu SDK usługi Azure Machine Learning
* Debugowanie przy użyciu projektanta usługi Azure Machine Learning
* Debugowanie przy użyciu usługi Application Insights
* Debugowanie interaktywnie przy użyciu kodu programu Visual Studio (VS Code) i narzędzia Python dla programu Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Debugowanie i rozwiązywanie problemów w sdk usługi Azure Machine Learning
Poniższe sekcje zawierają omówienie typowych pułapek podczas tworzenia potoków i różnych strategii debugowania kodu, który jest uruchomiony w potoku. Skorzystaj z poniższych wskazówek, gdy masz problemy z uruchomieniem potoku zgodnie z oczekiwaniami.

### <a name="testing-scripts-locally"></a>Testowanie skryptów lokalnie

Jednym z najczęstszych błędów w potoku jest to, że dołączony skrypt (skrypt oczyszczania danych, skrypt oceniający itp.) nie działa zgodnie z przeznaczeniem lub zawiera błędy środowiska uruchomieniowego w kontekście zdalnego obliczeń, które są trudne do debugowania w obszarze roboczym na komputerze platformy Azure Studio nauki. 

Same potoki nie mogą być uruchamiane lokalnie, ale uruchamianie skryptów w izolacji na komputerze lokalnym umożliwia szybsze debugowanie, ponieważ nie trzeba czekać na proces kompilacji obliczeń i środowiska. Niektóre prace rozwojowe są wymagane, aby to zrobić:

* Jeśli dane są w magazynie danych w chmurze, należy pobrać dane i udostępnić je do skryptu. Korzystanie z małej próbki danych jest dobrym sposobem na ograniczenie czasu wykonywania i szybkie uzyskiwanie opinii na temat zachowania skryptu
* Jeśli próbujesz symulować krok pośredniego potoku, może być konieczne ręczne tworzenie typów obiektów oczekiwanych przez dany skrypt od poprzedniego kroku
* Konieczne będzie również zdefiniowanie własnego środowiska i replikowanie zależności zdefiniowanych w zdalnym środowisku obliczeniowym

Po skonfigurowaniu skryptu do uruchomienia w środowisku lokalnym znacznie łatwiej jest wykonywać zadania debugowania, takie jak:

* Dołączanie niestandardowej konfiguracji debugowania
* Wstrzymywanie wykonywania i sprawdzanie stanu obiektu
* Przechwytywanie typu lub błędów logicznych, które nie będą widoczne do czasu środowiska wykonawczego

> [!TIP] 
> Po sprawdzeniu, że skrypt jest uruchomiony zgodnie z oczekiwaniami, dobry następny krok jest uruchomienie skryptu w potoku jednoetapowym przed podjęciem próby uruchomienia go w potoku z wielu kroków.

### <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Testowanie skryptów lokalnie jest doskonałym sposobem debugowania głównych fragmentów kodu i złożonej logiki przed rozpoczęciem tworzenia potoku, ale w pewnym momencie prawdopodobnie trzeba będzie debugować skrypty podczas rzeczywistego uruchomienia potoku, zwłaszcza podczas diagnozowania zachowania, które występuje podczas interakcji między etapami rurociągu. Zaleca się liberalne użycie `print()` instrukcji w skryptach kroków, dzięki czemu można zobaczyć stan obiektu i oczekiwane wartości podczas zdalnego wykonywania, podobnie jak debugować kod JavaScript.

Plik `70_driver_log.txt` dziennika zawiera: 

* Wszystkie drukowane instrukcje podczas wykonywania skryptu
* Śledzenie stosu dla skryptu 

Aby znaleźć te i inne pliki dziennika w portalu, najpierw kliknij potok uruchomiony w obszarze roboczym.

![Strona listy uruchamiania potoku](./media/how-to-debug-pipelines/pipelinerun-01.png)

Przejdź do strony szczegółów przebiegu potoku.

![Strona szczegółów przebiegu potoku](./media/how-to-debug-pipelines/pipelinerun-02.png)

Kliknij moduł dla określonego kroku. Przejdź do karty **Dzienniki.** Inne dzienniki zawierają informacje o procesie kompilacji obrazu środowiska i skrypty przygotowania kroku.

![Karta Dziennika szczegółów strony uruchamiania potoku](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Uruchomienia *opublikowanych potoków* można znaleźć na karcie **Punkty końcowe** w obszarze roboczym. Przebiegi dla *nieopublikowanych potoków* można znaleźć w **eksperymentach** lub **potokach**.

### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Poniższa tabela zawiera typowe problemy podczas opracowywania potoku z potencjalnymi rozwiązaniami.

| Problem | Możliwe rozwiązanie |
|--|--|
| Nie można przekazać `PipelineData` danych do katalogu | Upewnij się, że utworzono katalog w skrypcie, który odpowiada, gdzie potok oczekuje danych wyjściowych kroku. W większości przypadków argument wejściowy zdefiniuje katalog wyjściowy, a następnie utworzysz katalog jawnie. Służy `os.makedirs(args.output_dir, exist_ok=True)` do tworzenia katalogu wyjściowego. Zobacz [samouczek](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) przykład skryptu oceniania, który pokazuje ten wzorzec projektu. |
| Błędy zależności | Jeśli skrypty zostały opracowane i przetestowane lokalnie, ale znaleźć problemy z zależnością podczas uruchamiania na zdalnym obliczeniu w potoku, upewnij się, że zależności i wersje środowiska obliczeniowego są zgodne ze środowiskiem testowym. (Patrz [Budowanie, buforowanie i ponowne wykorzystanie środowiska](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Niejednoznaczne błędy z celami obliczeniowymi | Usuwanie i ponowne tworzenie obiektów docelowych obliczeń może rozwiązać pewne problemy z celami obliczeniowymi. |
| Potok nie jest ponownie zaaukany | Ponowne użycie kroku jest domyślnie włączone, ale upewnij się, że nie został wyłączony w kroku potoku. Jeśli ponowne użycie jest `allow_reuse` wyłączone, parametr w `False`kroku zostanie ustawiony na . |
| Rurociąg jest ponownie nienamnie | Aby upewnić się, że kroki są powtarzane tylko wtedy, gdy ich podstawowe dane lub skrypty ulegną zmianie, oddziel katalogi dla każdego kroku. Jeśli używasz tego samego katalogu źródłowego dla wielu kroków, mogą wystąpić niepotrzebne ponowne uruchomienia. Użyj `source_directory` parametru w obiekcie kroku potoku, aby wskazać izolowany katalog dla `source_directory` tego kroku i upewnij się, że nie używasz tej samej ścieżki dla wielu kroków. |

### <a name="logging-options-and-behavior"></a>Opcje rejestrowania i zachowanie

Poniższa tabela zawiera informacje dotyczące różnych opcji debugowania dla potoków. Nie jest to wyczerpująca lista, ponieważ istnieją inne opcje oprócz tylko usługi Azure Machine Learning, Python i OpenCensus pokazano tutaj.

| Biblioteka                    | Typ   | Przykład                                                          | Element docelowy                                  | Resources                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metryka | `run.log(name, val)`                                             | Interfejs użytkownika portalu usługi Azure Machine Learning             | [Jak śledzić eksperymenty](how-to-track-experiments.md#available-metrics-to-track)<br>[klasa azureml.core.Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Drukowanie/rejestrowanie języka Python    | Log    | `print(val)`<br>`logging.info(message)`                          | Dzienniki sterowników, projektant usługi Azure Machine Learning | [Jak śledzić eksperymenty](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Rejestrowanie języka Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Usługa Application Insights — ślady                | [Debugowanie potoków w usłudze Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Eksporterzy biblioteki OpenCensus usługi Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Książka kucharska rejestrowania Pythona](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Przykład opcji rejestrowania

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Debugowanie i rozwiązywanie problemów w projektancie usługi Azure Machine Learning (wersja zapoznawcza)

W tej sekcji przedstawiono omówienie sposobu rozwiązywania problemów z potokami w projektancie.
W przypadku potoków utworzonych w projektancie można znaleźć **pliki dziennika** na stronie tworzenia lub na stronie szczegółów uruchamiania potoku.

### <a name="access-logs-from-the-authoring-page"></a>Uzyskiwanie dostępu do dzienników ze strony tworzenia

Po przesłaniu przebiegu potoku i pozostania na stronie tworzenia można znaleźć pliki dziennika wygenerowane dla każdego modułu.

1. Wybierz dowolny moduł na kanwie tworzenia.
1. W prawym okienku modułu przejdź do **danych Wyjściowo-ogs.**
1. Zaznaczanie pliku dziennika`70_driver_log.txt`

    ![Tworzenie dzienników modułów stron](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Dzienniki dostępu z uruchomień potoku

Można również znaleźć pliki dziennika określonych przebiegów na stronie szczegółów przebiegu potoku w sekcji **Potoki** lub **Eksperymenty.**

1. Wybierz przebieg potoku utworzony w projektancie.
    ![Strona uruchomienia potoku](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Wybierz dowolny moduł w okienku podglądu.
1. W prawym okienku modułu przejdź do **danych Wyjściowo-ogs.**
1. Zaznaczanie pliku dziennika`70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Debugowanie i rozwiązywanie problemów w usłudze Application Insights
Aby uzyskać więcej informacji na temat korzystania z biblioteki Języka Python OpenCensus w ten sposób, zobacz ten przewodnik: [Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego w usłudze Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Debugowanie i rozwiązywanie problemów w programie Visual Studio Code

W niektórych przypadkach może być konieczne interaktywne debugowanie kodu języka Python używanego w potoku ml. Za pomocą programu Visual Studio Code (VS Code) i Narzędzia Python dla programu Visual Studio (PTVSD), można dołączyć do kodu, jak działa w środowisku szkoleniowym.

### <a name="prerequisites"></a>Wymagania wstępne

* __Obszar roboczy usługi Azure Machine Learning__ skonfigurowany do korzystania z sieci __wirtualnej platformy Azure__.
* __Potok usługi Azure Machine Learning,__ który używa skryptów języka Python w ramach kroków potoku. Na przykład PythonScriptStep.
* Klaster obliczeniowy usługi Azure Machine Learning, który znajduje się __w sieci wirtualnej__ i jest __używany przez potok do szkolenia.__
* __Środowisko programistyczne,__ które znajduje __się w sieci wirtualnej__. Środowisko programistyczne może być jedną z następujących czynności:

    * Maszyna wirtualna platformy Azure w sieci wirtualnej
    * Wystąpienie obliczeniowe maszyny Wirtualnej notesu w sieci wirtualnej
    * Komputer kliencki połączony z siecią wirtualną za pomocą wirtualnej sieci prywatnej (VPN).

Aby uzyskać więcej informacji na temat korzystania z sieci wirtualnej platformy Azure z usługą Azure Machine Learning, zobacz [Bezpieczne zadania eksperymentowania i wnioskowania usługi Azure ML w ramach sieci wirtualnej platformy Azure.](how-to-enable-virtual-network.md)

### <a name="how-it-works"></a>Jak to działa

Kroki potoku ml uruchamiają skrypty języka Python. Skrypty te są modyfikowane w celu wykonania następujących akcji:
    
1. Zarejestruj adres IP hosta, na który są uruchomione. Adres IP służy do łączenia debugera ze skryptem.

2. Uruchom składnik debugowania PTVSD i poczekaj na połączenie debugera.

3. Ze środowiska programistycznego można monitorować dzienniki utworzone przez proces szkolenia, aby znaleźć adres IP, w którym jest uruchomiony skrypt.

4. Powiedz vs kod adres IP, aby połączyć debugera przy użyciu `launch.json` pliku.

5. Należy dołączyć debugera i interaktywnie krok po kroku przez skrypt.

### <a name="configure-python-scripts"></a>Konfigurowanie skryptów języka Python

Aby włączyć debugowanie, należy wprowadzić następujące zmiany w skryptach języka Python używanych przez kroki w potoku uczenia maszynowego:

1. Dodaj następujące instrukcje importu:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Dodaj następujące argumenty. Te argumenty umożliwiają włączenie debugera w razie potrzeby i ustawienie limitu czasu do podłączenia debugera:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Dodaj następujące instrukcje. Te instrukcje załadować bieżącego kontekstu uruchamiania, dzięki czemu można rejestrować adres IP węzła, na który jest uruchomiony kod:

    ```python
    global run
    run = Run.get_context()
    ```

1. Dodaj `if` instrukcję, która uruchamia PTVSD i czeka na debugera do dołączenia. Jeśli żaden debuger nie zostanie dołączony przed limitem czasu, skrypt będzie kontynuowany w normalny sposób.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

W poniższym przykładzie `train.py` języka Python przedstawiono podstawowy plik, który umożliwia debugowanie:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurowanie potoku ml

Aby zapewnić pakiety Języka Python potrzebne do uruchomienia PTVSD i `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`uzyskania kontekstu uruchomienia, utwórz środowisko i ustaw . Zmień wersję SDK, aby dopasować się do używanej wersji. Poniższy fragment kodu pokazuje, jak utworzyć środowisko:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

W sekcji [Konfigurowanie skryptów języka Python](#configure-python-scripts) dwa nowe argumenty zostały dodane do skryptów używanych w krokach potoku ml. Poniższy fragment kodu pokazuje, jak używać tych argumentów, aby włączyć debugowanie dla składnika i ustawić limit czasu. Pokazuje również, jak korzystać ze środowiska `runconfig=run_config`utworzonego wcześniej przez ustawienie:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Po uruchomieniu potoku każdy krok tworzy uruchomienie podrzędne. Jeśli debugowanie jest włączone, zmodyfikowany skrypt rejestruje informacje podobne `70_driver_log.txt` do następującego tekstu w dla uruchomienia podrzędnego:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Zapisz `ip_address` wartość. Jest on używany w następnej sekcji.

> [!TIP]
> Można również znaleźć adres IP z dzienników uruchamiania dla uruchomienia podrzędnego dla tego kroku potoku. Aby uzyskać więcej informacji na temat wyświetlania tych informacji, zobacz [Monitorowanie przebiegów eksperymentu usługi Azure ML i metryk](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować narzędzia Języka Python dla programu Visual Studio (PTVSD) w środowisku programistycznym VS Code, należy użyć następującego polecenia:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Aby uzyskać więcej informacji na temat korzystania z PTVSD z kodem VS, zobacz [Zdalne debugowanie](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Aby skonfigurować program VS Code do komunikowania się z obliczeniami usługi Azure Machine Learning, w których jest uruchomiony debuger, należy utworzyć nową konfigurację debugowania:

    1. W obszarze Kod programu VS wybierz menu __Debugowanie,__ a następnie wybierz polecenie __Otwórz konfiguracje__. Zostanie otwarty plik o nazwie __launch.json.__

    1. W pliku __launch.json__ znajdź wiersz `"configurations": [`zawierający program i wstaw następujący tekst. Zmień `"host": "10.3.0.5"` wpis na adres IP zwrócony w dziennikach z poprzedniej sekcji. Zmień `"localRoot": "${workspaceFolder}/code/step"` wpis na katalog lokalny zawierający kopię debugowanego skryptu:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Jeśli istnieją już inne wpisy w sekcji konfiguracje, dodać przecinek (,) po wstawionym kodzie.

        > [!TIP]
        > Najlepszym rozwiązaniem jest przechowywanie zasobów dla skryptów w oddzielnych `localRoot` katalogach, `/code/step1`dlatego odwołania do przykładowej wartości .
        >
        > Jeśli debugujesz wiele skryptów, w różnych katalogach utwórz oddzielną sekcję konfiguracji dla każdego skryptu.

    1. Zapisz plik __launch.json.__

### <a name="connect-the-debugger"></a>Łączenie debugera

1. Otwórz program VS Code i otwórz lokalną kopię skryptu.
2. Ustaw punkty przerwania w miejscu, w którym skrypt ma zostać zatrzymany po dołączeniu.
3. Gdy proces podrzędny jest uruchomiony `Timeout for debug connection` skrypt, a jest wyświetlany w dziennikach, użyj klawisza F5 lub wybierz __debug__. Po wyświetleniu monitu wybierz konfigurację __zdalnego debugowania usługi Azure Machine Learning Compute: remote debug.__ Można również wybrać ikonę debugowania z paska bocznego, __azure machine learning: zdalnego debugowania__ wpis z menu rozwijanego debugowania, a następnie użyć zielonej strzałki, aby dołączyć debugera.

    W tym momencie vs code łączy się z PTVSD w węźle obliczeniowym i zatrzymuje się w punkcie przerwania, który został ustawiony wcześniej. Możesz teraz przejść przez kod podczas jego pracy, przeglądać zmienne itp.

    > [!NOTE]
    > Jeśli w dzienniku zostanie `Debugger attached = False`wyświetlony wpis z informacją , a następnie upłynął limit czasu i skrypt kontynuowany bez debugera. Prześlij potok ponownie i połącz debuger po `Timeout for debug connection` wiadomości i przed upływem limitu czasu.

## <a name="next-steps"></a>Następne kroki

* Zobacz odwołanie zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) i pakietu [azureml-pipelines-steps.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Zobacz listę [wyjątków projektanta i kodów błędów](algorithm-module-reference/designer-error-codes.md).
