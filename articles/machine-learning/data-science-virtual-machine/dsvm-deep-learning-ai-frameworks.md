---
title: Głębokie uczenie & platformy AI
titleSuffix: Azure Data Science Virtual Machine
description: Dostępne platformy i narzędzia uczenia głębokiego na platformie Azure Data Science Virtual Machine.
keywords: narzędzia do nauki dotyczące danych, maszyna wirtualna do nauki o danych, narzędzia do nauki o danych, nauka danych systemu Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: fd38bf1f7741c4d610ef43a12d90533d4ac7b703
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802409"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Głębokie uczenie i platformy AI dla Data Science VM platformy Azure
Poniżej wymieniono platformy uczenia głębokiego na DSVM.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Caffe jest zainstalowany w `/opt/caffe`.   Przykłady są w `/opt/caffe/examples`.|
| Jak uruchomić go      | Użyj X2Go, aby zalogować się do maszyny wirtualnej, a następnie uruchomić nowy terminal, a następnie wprowadź następujące polecenie:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Zostanie otwarte nowe okno przeglądarki z przykładowymi notesami. Pliki binarne są instalowane w/opt/Caffe/Build/Install/bin.<br/><br/>Zainstalowana wersja programu Caffe wymaga języka Python 2,7 i nie będzie współdziałać z językiem Python 3,5, który jest domyślnie aktywowany. Aby przełączyć się do języka Python 2,7, uruchom `source activate root` w celu przełączenia się do środowiska Anaconda.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Caffe2 jest zainstalowany w środowisku [Python 2,7 (root) Conda. |
| Jak uruchomić go      | Terminal: uruchamianie języka Python i importowanie Caffe2. <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu Caffe2, aby znaleźć przykładowe notesy. Niektóre notesy wymagają ustawienia elementu głównego Caffe2 w kodzie języka Python; Wprowadź/opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 5,2 |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Moduł łańcucha jest instalowany w języku Python 3,5. |
| Jak uruchomić go      | Terminal: Aktywuj środowisko Python 3,5, uruchom `python`, a następnie `import chainer`. <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu łańcucha, aby znaleźć przykładowe notesy.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, sterownik NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 10.0.130|
| Obsługiwane wersje DSVM      | Systemy Windows i Linux   |
| Jak jest on skonfigurowany/instalowany w DSVM?  |_NVIDIA-SMI_ jest dostępny w ścieżce systemowej.  |
| Jak uruchomić go      | Otwórz wiersz polecenia (w systemie Windows) lub terminal (na komputerze z systemem Linux), a następnie uruchom polecenie _NVIDIA-SMI_. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 0.16.1|
| Obsługiwane wersje DSVM      | Linux (Ubuntu)   |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Horovod jest zainstalowany w języku Python 3,5 |
| Jak uruchomić go      | Aktywuj poprawne środowisko w terminalu, a następnie uruchom Język Python. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 2.2.4 |
| Obsługiwane wersje DSVM      | Systemy Windows i Linux   |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Keras jest zainstalowany w języku Python 3,6 w systemie Windows i w języku Python 3,5 w Linux |
| Jak uruchomić go      | Aktywuj poprawne środowisko w terminalu, a następnie uruchom Język Python. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 2.5.1 |
| Obsługiwane wersje DSVM      | Systemy Windows i Linux   |
| Jak jest on skonfigurowany/instalowany w DSVM?  | CNTK jest zainstalowany w języku Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) i w języku Python 3,5 w systemie [Linux](./dsvm-languages.md#python-linux-edition) |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko i uruchom Język Python. <br/>Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog CNTK dla przykładów. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.2.0 |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Zainstalowane w języku [Python 3,5](dsvm-languages.md#python-linux-edition). Przykładowe notesy Jupyter są dołączone, a przykłady znajdują się w/dsvm/Samples/pytorch. |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Połącz, a następnie otwórz katalog PyTorch dla przykładów.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1,13 |
| Obsługiwane wersje DSVM      | Windows, Linux |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Zainstalowane w języku Python 3,5 w systemach [Linux](dsvm-languages.md#python-linux-edition) i Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> * Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog TensorFlow dla przykładów.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow obsługujące](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1,12 |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest on skonfigurowany/instalowany w DSVM?  | tensorflow_model_server jest dostępny w terminalu. |
| Jak uruchomić go      |  Przykłady są dostępne w [trybie online](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.0.3 |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest on skonfigurowany/instalowany w DSVM?  |Theano jest zainstalowany w języku Python 2,7 (_root_) i w środowisku Python 3,5 (_py35_). |
| Jak uruchomić go      |  Terminal: Aktywuj żądaną wersję języka Python (root lub py35), uruchom środowisko Python, a następnie zaimportuj Theano.<br/>* Jupyter: Wybierz jądro Python 2,7 lub 3,5, a następnie zaimportuj Theano.  <br/>Aby obejść ostatnią usterkę biblioteki jądra matematycznego (MKL), musisz najpierw ustawić warstwę wątku MKL w następujący sposób:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |