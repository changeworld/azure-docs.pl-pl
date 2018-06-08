---
title: Sposób użycia procesora GPU dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia graficznego przetwarzania jednostki GPU do uczenia się sieci neuronowej głębokości w Azure Machine Learning Workbench.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: db0cfa1d91681d48a21ac160706dc4e8c72f81c2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830915"
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Sposób użycia procesora GPU w usłudze Azure Machine Learning
Graficzny przetwarzania jednostka GPU jest powszechnie używany do przetworzenia w praktyce znacznym zadań, które zwykle mogą wystąpić w przypadku uczenie niektórych modeli sieci neuronowej głębokość. Przy użyciu GPU, można znacznie skrócić czas szkolenia modeli. W tym dokumencie zostanie przedstawiony sposób konfigurowania środowiska roboczego uczenia Maszynowego Azure do użycia [DSVM (danych maszyny wirtualnej nauki)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) wyposażone GPU jako cel wykonywania. 

## <a name="prerequisites"></a>Wymagania wstępne
- Aby kroków opisanych w tym przewodnik, musisz najpierw [zainstalować Azure ML Workbench](../service/quickstart-installation.md).
- Musisz mieć dostęp do komputerów z NVidia GPU.
    - Skrypty można uruchomić bezpośrednio na komputerze lokalnym (Windows lub macOS) z GPU.
    - Można również uruchamiać skrypty w kontenerze Docker na maszynie z GPU.

## <a name="execute-in-local-environment-with-gpus"></a>Wykonaj w _lokalnego_ środowisko z GPU
Można zainstalować usługi Azure ML Workbench na komputerze wyposażonym GPU i wykonać _lokalnego_ środowiska. Może to być:
- Fizyczny komputer z systemem Windows lub macOS.
- Wirtualna systemu Windows (maszyna wirtualna), takich jak DSVM systemu Windows zainicjowana przy użyciu szablonu Azure NC serii maszyn wirtualnych.

W tym przypadku są wymagane w konsoli usługi Azure ML Workbench specjalnej konfiguracji. Po prostu upewnij się, że masz wszystkie niezbędne sterowniki, zestaw narzędzi i maszyny z obsługą procesora GPU uczenia biblioteki zainstalowane lokalnie. Po prostu wykonać _lokalnego_ środowiska umożliwiający środowiska uruchomieniowego języka Python bezpośrednio dostęp do lokalnych sprzętu procesora GPU.

1. Otwórz AML Workbench. Przejdź do **pliku** i **Otwórz okno wiersza polecenia**. 
2. Z wiersza polecenia Zainstaluj strukturę włączone GPU głębokie learning jak kognitywnych zestaw narzędzi firmy Microsoft, TensorFlow i itp. Na przykład:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Pisanie kodu języka Python, który wykorzystuje dokładnego uczenia bibliotek.
4. Wybierz _lokalnego_ środowiska obliczeniowe i wykonanie kodu języka Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Wykonaj w _docker_ środowiska na maszynie Wirtualnej systemu Linux z GPU
Azure ML Workbench obsługują także wykonanie w Docker w maszynie Wirtualnej systemu Linux platformy Azure. W tym miejscu masz doskonałe rozwiązanie do uruchomienia zadań praktyce znacznym na element zaawansowanych sprzęt wirtualny i zapłać tylko za użycie przez wyłączenie, po zakończeniu. Zasadniczo jest możliwe użycie procesorów graficznych maszyny wirtualnej systemu Linux, DSVM systemem Ubuntu zawiera wymagane sterowniki CUDA i wstępnie zainstalowanymi bibliotekami znacznie ułatwiając konfiguracji. Wykonaj następujące czynności:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Utwórz maszynę wirtualną nauki danych opartych na Ubuntu Linux na platformie Azure
1. Otwórz przeglądarkę sieci web i przejdź do [portalu Azure](https://portal.azure.com)

2. Wybierz **+ nowy** po lewej stronie portalu.

3. Wyszukaj "Data nauki maszyny wirtualnej dla systemu Linux (Ubuntu)" w witrynie marketplace.

4. Kliknij przycisk **Utwórz** utworzyć DSVM Ubuntu.

5. Wypełnij **podstawy** formularza z wymaganych informacji.
Podczas wybierania lokalizacji dla maszyny Wirtualnej, należy pamiętać, że procesor GPU maszyny wirtualne są dostępne tylko w określonych regionach platformy Azure, na przykład **południowo-środkowe stany**. Zobacz [obliczeniowe produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/).
Kliknij przycisk OK, aby zapisać **podstawy** informacji.

6. Wybierz rozmiar maszyny wirtualnej. Wybierz jeden z rozmiarów z prefiksem NC maszyn wirtualnych z mikroukłady NVidia GPU.  Kliknij przycisk **Wyświetl wszystko** Aby zapoznać się z pełną listą zgodnie z potrzebami. Dowiedz się więcej o [wyposażone w procesor GPU maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Zakończ pozostałe ustawienia i przejrzyj informacje o zakupie. Kliknij przycisk zakupu, aby utworzyć maszynę Wirtualną. Zanotuj adres IP przydzielone do maszyny wirtualnej. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Utwórz nowy projekt w Workbench uczenia Maszynowego Azure 
Można użyć _MNIST klasyfikacji za pomocą TensorFlow_ przykład lub _MNIST klasyfikowania dataset z CNTK_ przykład.

### <a name="create-a-new-compute-target"></a>Utwórz nowy obiekt docelowy obliczeniowe
Uruchom wiersz polecenia z Workbench uczenia Maszynowego Azure. Wprowadź następujące polecenie. Zastąp tekst zastępczy w poniższym przykładzie własne wartości Nazwa, adres IP, nazwę użytkownika i hasło. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Konfigurowanie usługi Azure ML Workbench do dostępu do procesora GPU
Wróć do projektu i Otwórz **widoku pliku**i trafień **Odśwież** przycisku. Teraz można zobaczyć dwa nowe pliki konfiguracji `my_dsvm.compute` i `my_dsvm.runconfig`.
 
Otwórz `my_dsvm.compute`. Zmień `baseDockerImage` do `microsoft/mmlspark:plus-gpu-0.7.9` i Dodaj nowy wiersz `nvidiaDocker: true`. Dlatego ten plik powinien mieć następujące dwa wiersze:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Teraz otworzyć `my_dsvm.runconfig`, zmień `Framework` wartość z `PySpark` do `Python`. Jeśli nie widzisz ten wiersz, dodaj go, ponieważ wartość domyślna będzie `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Odwołanie głębokie Learning Framework 
Otwórz `conda_dependencies.yml` plików i upewnij się, że używasz wersji procesora GPU dokładnego learning framework pakietów języka Python. Przykładowych projektach listy wersji procesora CPU, więc należy wprowadzić tę zmianę.

Przykład TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Przykład kognitywnych zestaw narzędzi firmy Microsoft:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

Umożliwia także 1 wersja bit SGD Toolkit kognitywnych firmy Microsoft, co zapewnia poprawy wydajności na maszynach wirtualnych z wieloma procesorami GPU. Należy pamiętać, [wymaganie licencji 1 bit SGD](https://docs.microsoft.com/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Realizacja
Teraz można przystąpić do uruchamiania skryptów języka Python. Mogły być uruchamiane w ramach przy użyciu usługi Azure ML Workbench `my_dsvm` kontekstu, lub uruchomić go z wiersza polecenia:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Aby sprawdzić, czy jest używany procesora GPU, sprawdź, czy wyjście wykonywania wyświetlić postać zbliżoną do następującej:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Gratulacje! Skrypt po prostu wykorzystana mocy procesora GPU za pomocą kontenera Docker!

## <a name="next-steps"></a>Kolejne kroki
Zobacz przykład użycia procesora GPU w celu przyspieszenia sieci neuronowej głębokość szkolenia w galerii Azure ML.
