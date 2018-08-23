---
title: Sposób użycia procesora GPU dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób używania graficzny przetwarzania jednostek (GPU) to w opracowywaniu głębokich sieciach neuronowych w aplikacji Azure Machine Learning Workbench.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: f3b6c4f6af14615511400650662fe7a350c172ba
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056079"
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Sposób użycia procesora GPU w usłudze Azure Machine Learning
Graficzny przetwarzania jednostki (GPU) powszechnie używanych przetwarzania zadań wymaga dużej mocy obliczeniowej, które zazwyczaj mogą wystąpić podczas uczenia modeli niektórych sieci neuronowej. Za pomocą procesorów GPU, można znacznie zmniejszyć czas szkolenie modeli. W tym dokumencie, dowiesz się, jak skonfigurować aplikację Azure ML Workbench, aby użyć [DSVM (maszyna wirtualna do nauki o danych)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) wyposażone w procesory GPU jako element docelowy wykonywania. 

## <a name="prerequisites"></a>Wymagania wstępne
- Do wykonania kroków w tym przewodniku, trzeba wcześniej [zainstalować aplikację Azure ML Workbench](../service/quickstart-installation.md).
- Musisz mieć dostęp do komputerów wyposażonych w procesory GPU NVidia.
    - Można uruchamiać skrypty bezpośrednio na komputerze lokalnym (Windows lub macOS) za pomocą procesorów GPU.
    - Można również uruchamiać skrypty w kontenerze platformy Docker na maszynie z systemem Linux za pomocą procesorów GPU...

## <a name="execute-in-local-environment-with-gpus"></a>Wykonaj w _lokalnego_ środowiska za pomocą procesorów GPU
Można zainstalować aplikację Azure ML Workbench na komputerze wyposażonym za pomocą procesorów GPU i wykonywania względem _lokalnego_ środowiska. Może to być:
- Fizyczny komputer Windows lub macOS.
- VM Windows (maszyna wirtualna), takie jak Windows DSVM aprowizowane przy użyciu szablonu maszyny wirtualne z serii NC platformy Azure.

W tym przypadku są wymagane w aplikacji Azure ML Workbench specjalnej konfiguracji. Po prostu upewnij się, że masz wszystkie niezbędne sterowniki, zestaw narzędzi i obsługujące procesor GPU usługi machine learning biblioteki zainstalowane lokalnie. Po prostu Działaj _lokalnego_ środowisko, w której środowisko uruchomieniowe języka Python można uzyskać dostęp do lokalnego sprzęcie procesora GPU.

1. Otwórz AML aplikacji Workbench. Przejdź do **pliku** i **Otwórz wiersz polecenia**. 
2. Z wiersza polecenia należy zainstalować uczenia głębokiego z włączonymi procesorami GPU struktury, takiej jak Microsoft Cognitive Toolkit, TensorFlow i itp. Na przykład:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.5 with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

3. Pisanie kodu w języku Python, który korzysta z bibliotek do uczenia głębokiego.
4. Wybierz _lokalnego_ jako środowisko obliczeniowe i wykonywanie kodu w języku Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Wykonaj w _docker_ środowiska na maszynie Wirtualnej z systemem Linux za pomocą procesorów GPU
Usługa Azure ML Workbench również obsługiwać wykonywania na platformie Docker na maszynie Wirtualnej systemu Linux platformy Azure. W tym miejscu masz świetnym rozwiązaniem umożliwiającym uruchamianie zadań wymaga dużej mocy obliczeniowej na elemencie silny sprzęt wirtualny i płać tylko za użycie przez wyłączenie, po zakończeniu. Zasadniczo jest możliwe użycie procesorów GPU na dowolnej maszynie wirtualnej systemu Linux, maszyny wirtualnej DSVM oparta na systemie Ubuntu dołączono wymagane sterowniki CUDA i wstępnie zainstalowanymi bibliotekami dzięki czemu konfiguracji. Wykonaj poniższe kroki:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Utwórz maszynę wirtualną do nauki o danych opartych na systemie Ubuntu Linux na platformie Azure
1. Otwórz przeglądarkę internetową i przejdź do [witryny Azure portal](https://portal.azure.com)

2. Wybierz **+ nowy** po lewej stronie portalu.

3. Wyszukaj "Maszyny wirtualnej analizy danych dla systemu Linux (Ubuntu)" w witrynie marketplace.

4. Kliknij przycisk **Utwórz** do utworzenia maszyny wirtualnej DSVM z systemem Ubuntu.

5. Wypełnij **podstawy** formularz, podając wymagane informacje.
Podczas wybierania lokalizacji dla maszyny Wirtualnej, należy pamiętać, że procesora GPU maszyny wirtualne są dostępne tylko w niektórych regionach platformy Azure, na przykład **południowo-środkowe stany USA**. Zobacz [obliczenia dostępność produktów według regionów](https://azure.microsoft.com/regions/services/).
Kliknij przycisk OK, aby zapisać **podstawy** informacji.

6. Wybierz rozmiar maszyny wirtualnej. Wybierz jeden z rozmiary z prefiksem przez kontroler sieci maszyn wirtualnych są wyposażone w procesor GPU NVidia mikroukłady.  Kliknij przycisk **Wyświetl wszystko** aby zobaczyć pełną listę, stosownie do potrzeb. Dowiedz się więcej o [wyposażone w procesor GPU maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Zakończ pozostałych ustawień i przejrzyj informacje o zakupie. Kliknij opcję zakupu, aby utworzyć maszynę Wirtualną. Zwróć uwagę na adres IP z przydzielonej do maszyny wirtualnej. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Utwórz nowy projekt w aplikacji Azure ML Workbench 
Możesz użyć _klasyfikowania mnist ręcznie ZAPISANYCH przy użyciu TensorFlow_ przykład lub _zestawu klasyfikowania mnist ręcznie ZAPISANYCH danych za pomocą CNTK_ przykład.

### <a name="create-a-new-compute-target"></a>Utwórz nowy obiekt docelowy obliczeń
Uruchom wiersz polecenia z aplikacji Azure ML Workbench. Wprowadź następujące polecenie. Przy użyciu własnych wartości, aby uzyskać nazwę, adres IP, nazwy użytkownika i hasło, należy zastąpić tekst symbolu zastępczego w poniższym przykładzie. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Konfigurowanie usługi Azure ML Workbench dostęp do procesora GPU
Wróć do projektu i Otwórz **widok pliku**i kliknij przycisk **Odśwież** przycisku. Teraz możesz zobaczyć dwa nowe pliki konfiguracji `my_dsvm.compute` i `my_dsvm.runconfig`.
 
Otwórz `my_dsvm.compute`. Zmiana `baseDockerImage` do `microsoft/mmlspark:plus-gpu-0.7.9` i Dodaj nowy wiersz `nvidiaDocker: true`. Więc ten plik powinien zawierać następujące dwa wiersze:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Teraz Otwórz `my_dsvm.runconfig`, zmień `Framework` wartość z `PySpark` do `Python`. Jeśli nie widzisz ten wiersz ją dodać, ponieważ wartość domyślna będzie `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Platforma do uczenia głębokiego odwołania 
Otwórz `conda_dependencies.yml` pliku i upewnij się, używają procesorów GPU wersję framework pakiety języka Python do uczenia głębokiego. Przykładowe projekty listę wersji procesora CPU, więc trzeba wprowadzić tę zmianę.

Przykład dotyczący TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Przykład dotyczący Microsoft Cognitive Toolkit:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.5 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

### <a name="execute"></a>Realizacja
Teraz można przystąpić do uruchamiania skryptów w języku Python. Można je też uruchamiać w obrębie za pomocą aplikacji Azure ML Workbench `my_dsvm` kontekstu lub uruchomić go z wiersza polecenia:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Aby sprawdzić, czy procesor GPU jest używany, sprawdź dane wyjściowe wykonywania będzie podobny do poniższego:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Gratulacje! Skrypt po prostu zaprzęgnięte możliwości procesora GPU przez kontener platformy Docker!

## <a name="next-steps"></a>Kolejne kroki
Zobacz przykład użycia procesora GPU, aby przyspieszyć sieci neuronowej szkolenia w galerii usługi Azure ML.
