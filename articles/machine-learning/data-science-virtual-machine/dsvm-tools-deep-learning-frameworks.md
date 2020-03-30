---
title: Głębokie uczenie & struktury SI
titleSuffix: Azure Data Science Virtual Machine
description: Dostępne struktury i narzędzia uczenia głębokiego na maszynie wirtualnej usługi Azure Data Science.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270071"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Głębokie uczenie i struktury sztucznej inteligencji dla maszyny Wirtualnej do nauki o danych platformy Azure
Struktury uczenia głębokiego w systemie DSVM są wymienione poniżej.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Caffe jest `/opt/caffe`zainstalowany w .   Próbki znajdują `/opt/caffe/examples`się w pliku .|
| Jak go uruchomić      | Użyj X2Go, aby zalogować się do maszyny Wirtualnej, a następnie uruchom nowy terminal i wprowadź następujące elementy:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Zostanie otwarte nowe okno przeglądarki z przykładowymi notesami. Pliki binarne są instalowane w pliku /opt/caffe/build/install/bin.<br/><br/>Zainstalowana wersja Caffe wymaga Pythona 2.7 i nie będzie działać z Pythonem 3.5, który jest domyślnie aktywowany. Aby przełączyć się na Python `source activate root` 2.7, uruchom, aby przełączyć się do środowiska Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Caffe2 jest zainstalowany w środowisku [Python 2.7 (root) conda. |
| Jak go uruchomić      | Terminal: Uruchom Pythona i zaimportuj Caffe2. <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu Caffe2, aby znaleźć przykładowe notesy. Niektóre notesy wymagają, aby katalog główny Caffe2 był ustawiony w kodzie Języka Python; wprowadź /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 5.2 |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Chainer jest zainstalowany w Pythonie 3.5. |
| Jak go uruchomić      | Terminal: Aktywuj środowisko Pythona 3.5, uruchom `python`, a następnie `import chainer`. <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu Chainer, aby znaleźć przykładowe notesy.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[Sterownik CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 10.0.130|
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest skonfigurowany / zainstalowany na DSVM?  |_nvidia-smi_ jest dostępna na ścieżce systemowej.  |
| Jak go uruchomić      | Otwórz wiersz polecenia (w systemie Windows) lub terminal (w systemie Linux), a następnie uruchom _nvidia-smi_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 0.16.1|
| Obsługiwane wersje DSVM      | Linux (Ubuntu)   |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Horovod jest zainstalowany w Pythonie 3.5 |
| Jak go uruchomić      | Uaktywnij odpowiednie środowisko w terminalu, a następnie uruchom pythona. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 2.2.4 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Keras jest zainstalowany w Pythonie 3.6 w systemie Windows i Pythonie 3.5 w Systemie Linux |
| Jak go uruchomić      | Uaktywnij odpowiednie środowisko w terminalu, a następnie uruchom pythona. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Zestaw narzędzi usług Microsoft Cognitive (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 2.5.1 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | CNTK jest zainstalowany w Pythonie 3.6 na [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i Python 3.5 na [Linuksie](./dsvm-tools-languages.md#python-linux-edition)) |
| Jak go uruchomić      | Terminal: Aktywuj odpowiednie środowisko i uruchom Pythona. <br/>Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog CNTK dla przykładów. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.3.0 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | MXNet jest `C:\dsvm\tools\mxnet` zainstalowany w `/dsvm/tools/mxnet` systemie Windows i na Ubuntu. Powiązania Pythona są instalowane w Pythonie 3.6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i Python 3.5 na [Linuksie](./dsvm-tools-languages.md#python-linux-edition)) powiązania R są również zawarte w Ubuntu DSVM. |
| Jak go uruchomić      | Terminal: Aktywuj prawidłowe środowisko conda, a następnie uruchom `import mxnet`. <br/>Jupyter: Połącz się z [Jupyter](provision-vm.md#access-the-dsvm) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz `mxnet` katalog próbek. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.0.1 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | MXNet Model Server jest zainstalowany w Pythonie 3.6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i Python 3.5 na [Linuksie](./dsvm-tools-languages.md#python-linux-edition)) |
| Jak go uruchomić      | Terminal: `sudo systemctl stop jupyterhub` Uruchom najpierw, aby zatrzymać usługę JupyterHub, ponieważ oba nasłuchują na tym samym porcie. Następnie aktywuj prawidłowe środowisko conda i uruchom`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Interfejs zarządzania systemem NVidia (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje |  |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Do czego służy? | Narzędzie NVIDIA do wykonywania zapytań o aktywność gpu |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | `nvidia-smi`znajduje się na ścieżce systemowej. |
| Jak go uruchomić      | Na maszynie wirtualnej **z procesorem graficznym**otwórz wiersz polecenia (w systemie Windows) lub terminal (w systemie Linux), a następnie uruchom program `nvidia-smi`. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Zainstalowany w [Pythonie 3.5](dsvm-tools-languages.md#python-linux-edition). Przykładowe notesy Jupyter są uwzględniane, a próbki znajdują się w /dsvm/samples/pytorch. |
| Jak go uruchomić      | Terminal: Aktywuj odpowiednie środowisko, a następnie uruchom Pythona.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Połącz, a następnie otwórz katalog PyTorch dla przykładów.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.13 |
| Obsługiwane wersje DSVM      | Windows, Linux |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | Zainstalowany w Pythonie 3.5 na [Linuksie](dsvm-tools-languages.md#python-linux-edition) i Pythonie 3.6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Jak go uruchomić      | Terminal: Aktywuj odpowiednie środowisko, a następnie uruchom Pythona. <br/> * Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog TensorFlow dla próbek.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.12 |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest skonfigurowany / zainstalowany na DSVM?  | tensorflow_model_server jest dostępna w terminalu. |
| Jak go uruchomić      |  Próbki są dostępne [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.0.3 |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest skonfigurowany / zainstalowany na DSVM?  |Theano jest zainstalowany w Pythonie 2.7 (_korzeń_) i w pythonie 3.5 (_py35_). |
| Jak go uruchomić      |  Terminal: Aktywuj żądaną wersję Pythona (root lub py35), uruchom Pythona, a następnie zaimportuj Theano.<br/>* Jupyter: Wybierz jądro Python 2.7 lub 3.5, a następnie zaimportuj Theano.  <br/>Aby obejść błąd biblioteki jądra matematycznego (MKL), należy najpierw ustawić warstwę wątków MKL w następujący sposób:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |