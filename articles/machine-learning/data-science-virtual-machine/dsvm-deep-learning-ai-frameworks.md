---
title: Platformy uczenia głębokiego i sztucznej Inteligencji — Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat głębokiego uczenia, strukturami i narzędziami obsługiwanych na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 08a740ec898f1ac46ff66de9002e4241d918adc0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558180"
---
# <a name="deep-learning-and-ai-frameworks"></a>Platformy sztucznej inteligencji i uczenia głębokiego
[Maszyny wirtualnej do nauki o danych](https://aka.ms/dsvm) (DSVM) i [VM uczenia głębokiego](https://aka.ms/dsvm/deeplearning) obsługuje wiele platform uczenia głębokiego, aby ułatwić tworzenie aplikacji sztucznej inteligencji (AI) przy użyciu analizy predykcyjnej i możliwości poznawcze, takich jak obraz i language understanding.

Poniżej przedstawiono szczegółowe informacje na wszystkie niedostępne w maszyny DSVM platform uczenia głębokiego.

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Microsoft Cognitive Toolkit (CNTK) jest zainstalowany w języku Python 3.5 na [systemów Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i 3.6 języka Python na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Zawiera linki do przykładów      | Notesy Jupyter próbki są uwzględniane.     |
| Pokrewne narzędzia na maszyny DSVM      | Biblioteki Keras      |
| Jak używać / ją uruchomić?    | * W terminalu: aktywowanie odpowiednie środowisko, a następnie uruchom języka Python. <br/> * W Jupyter: Połącz się z usługą [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz katalog CNTK, aby zapoznać się z przykładami. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | TensorFlow jest zainstalowany w języku Python 3.5 na [systemów Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i 3.6 języka Python na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Zawiera linki do przykładów      | Notesy Jupyter próbki są uwzględniane.     |
| Pokrewne narzędzia na maszyny DSVM      | Biblioteki Keras      |
| Jak używać / ją uruchomić?    | * W terminalu: aktywowanie odpowiednie środowisko, a następnie uruchom języka Python. <br/> * W Jupyter: Połącz się z usługą [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz katalog TensorFlow, aby zapoznać się z przykładami.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Co to jest?   | Distribued platforma do uczenia głębokiego TensorFlow      |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Horovod jest zainstalowany w języku Python 3.5 na [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Zawiera linki do przykładów      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Pokrewne narzędzia na maszyny DSVM      | TensorFlow      |
| Jak używać / ją uruchomić?    | W terminalu: aktywowanie odpowiednie środowisko, a następnie uruchom języka Python. |

## <a name="keras"></a>Biblioteki Keras

|    |           |
| ------------- | ------------- |
| Co to jest?   | Interfejs API wysokiego poziomu uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | TensorFlow jest zainstalowany w języku Python 3.5 na [systemów Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i 3.6 języka Python na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Zawiera linki do przykładów      | https://github.com/fchollet/keras/tree/master/examples      |
| Pokrewne narzędzia na maszyny DSVM      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Jak używać / ją uruchomić?    | * W terminalu: aktywowanie odpowiednie środowisko, a następnie uruchom języka Python. <br/> * W Jupyter: Pobierz przykłady z lokalizacji GitHub, Połącz się z usługą [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz przykładowy katalog. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Caffe jest zainstalowany w `/opt/caffe`.    |
| Przełączanie do środowiska Python 2.7 | Uruchom polecenie `source activate root` |
| Zawiera linki do przykładów      | Przykłady są objęte `/opt/caffe/examples`.      |
| Pokrewne narzędzia na maszyny DSVM      | Caffe2      |

### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?

Użyj X2Go, aby zalogować się do maszyny Wirtualnej, a następnie uruchom nowy terminal i wprowadź

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Otwiera nowe okno przeglądarki z notesami próbki.

Pliki binarne są instalowane w /opt/caffe/build/install/bin.

Zainstalowana wersja Caffe wymaga środowiska Python 2.7 i nie będzie działać z języka Python 3.5 aktywowana domyślnie. Uruchom `source activate root` przełączyć środowisko Anaconda.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Caffe2 jest zainstalowany w [środowiska conda środowisko Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Trwa źródłowego `/opt/caffe2`. |
| Zawiera linki do przykładów      | Notesy próbki są objęte JupyterHub. |
| Pokrewne narzędzia na maszyny DSVM      | Caffe      |
| Jak używać / ją uruchomić?    | * W terminalu: aktywowanie [środowisko Python głównego](dsvm-languages.md#python-linux-and-windows-server-2012-edition), Python i rozpoczęcie importowania caffe2. <br/> * W JupyterHub: [nawiązać połączenie z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie przejdź do katalogu Caffe2 można znaleźć przykładowe notesy. Niektóre notesów wymagają głównego Caffe2 w kod Python; Wprowadź /opt/caffe2. |
| Tworzenie notatki | Caffe2 została stworzona od źródła w systemie Linux i obejmuje CUDA, cuDNN i Intel MKL. Bieżące zatwierdzenie jest 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, która została wybrana dla stabilności dla wszystkich procesorów GPU i przykłady przetestowane. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Chainer jest zainstalowany w [język Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL i ChainerCV są również instalowane.   |
| Zawiera linki do przykładów      | Notesy próbki są objęte JupyterHub. |
| Pokrewne narzędzia na maszyny DSVM      | Caffe      |
| Jak używać / ją uruchomić?  | * W terminalu: aktywowanie [język Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) środowiska, uruchom _python_, następnie zaimportuj moduł łańcucha. <br/> * W JupyterHub: [Połącz z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie przejdź do katalogu łańcucha, aby znaleźć przykładowe notesy.


## <a name="deep-water"></a>Głębokie limitu górnego

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego dla H2O      |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Głębokie limitu górnego jest zainstalowany w [język Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i jest również dostępna w `/dsvm/tools/deep_water`.   |
| Zawiera linki do przykładów      | Notesy próbki są objęte JupyterHub.      |
| Pokrewne narzędzia na maszyny DSVM      | H2O Sparkling Water      |

### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?

Głębokie wody wymaga CUDA 8 cuDNN 5.1. To nie jest w ścieżce biblioteki, domyślnie CUDA 9 i cuDNN 7 używania w innych platform uczenia głębokiego. Na potrzeby architektury CUDA 8 + cuDNN 5.1 głębokiego limitu górnego:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Aby użyć głębokiego limitu górnego:
* W terminalu: aktywowanie [język Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) środowiska, uruchom _python_. <br/>
* W JupyterHub: [nawiązać połączenie z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie przejdź do katalogu deep_water można znaleźć przykładowe notesy.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | MXNet jest zainstalowany w `C:\dsvm\tools\mxnet` na Windows i `/dsvm/tools/mxnet` w systemie Linux. Python powiązania są zainstalowane w języku Python 3.5 na [systemów Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i 3.6 języka Python na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Powiązań języka R są również instalowane w systemie Ubuntu.   |
| Zawiera linki do przykładów      | Notesy Jupyter próbki są uwzględniane.    |
| Pokrewne narzędzia na maszyny DSVM      | Biblioteki Keras      |
| Jak używać / ją uruchomić?    | * W terminalu: aktywowanie odpowiednie środowisko, a następnie uruchom języka Python. <br/> * W Jupyter: Połącz się z usługą [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz katalog mxnet, aby zapoznać się z przykładami.  |
 | Tworzenie notatki | MXNet składa się z źródła w systemie Linux. Ta kompilacja zawiera CUDA, cuDNN, NCCL i MKL. |

## <a name="nvidia-digits"></a>PROCESORY GPU NVIDIA CYFR

|    |           |
| ------------- | ------------- |
| Co to jest?   | Uczenie głębokie system z NVIDIA dla szybko szkolenie modeli uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | CYFRY jest zainstalowany w `/dsvm/tools/DIGITS` i jest dostępna usługa o nazwie _cyfr_.   |

### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?

Zaloguj się do tej maszyny Wirtualnej z X2Go. W terminalu Uruchom usługę:

    sudo systemctl start digits

Usługa zajmuje około jednej minuty do uruchomienia. Uruchom przeglądarkę internetową i przejdź do `http://localhost:5000`. Należy pamiętać, że CYFR nie bezpiecznego logowania i nie powinny być udostępniane poza maszyny Wirtualnej.



## <a name="nvidia-smi"></a>procesory GPU NVIDIA smi

|    |           |
| ------------- | ------------- |
| Co to jest?   | Procesory GPU NVIDIA narzędzie do wykonywania zapytań aktywność procesora GPU      |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | _procesory GPU NVIDIA smi_ jest dostępny w ścieżce systemowej.   |
| Jak używać / ją uruchomić? | Uruchom wiersz polecenia (na Windows) lub terminalu (w systemie Linux), a następnie uruchom _nvidia smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Theano jest zainstalowany w środowisko Python 2.7 (_głównego_), a także język Python 3.5 (_py35_) środowiska.   |
| Pokrewne narzędzia na maszyny DSVM      | Biblioteki Keras      |
| Jak używać / ją uruchomić?    | * W terminalu Aktywuj wersję języka Python (głównego lub py35), uruchom języka python, a następnie zaimportować theano. <br/> * W Jupyter wybierz jądro Python 2,7 lub 3,5, a następnie zaimportuj Theano.  <br/>Aby obejść ostatnie usterki MKL, musisz najpierw ustawić MKL wątkowości warstwy:<br/><br/>_Export MKL_THREADING_LAYER = GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Latarka jest zainstalowany w `/dsvm/tools/torch`. PyTorch jest zainstalowany w środowisko Python 2.7 (_głównego_), a także język Python 3.5 (_py35_) środowiska.   |
| Zawiera linki do przykładów      | Przykłady latarka znajdują się w folderze `/dsvm/samples/torch`. Przykłady PyTorch znajdują się w folderze `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma do uczenia głębokiego      |
| Wersje maszyny DSVM obsługiwane      | Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | PyTorch jest zainstalowany w [język Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Zawiera linki do przykładów      | Znajdują się przykładowe notesów Jupyter i przykłady znajdują się również w /dsvm/samples/pytorch.      |
| Pokrewne narzędzia na maszyny DSVM      | Torch      |
| Jak używanie i uruchom go |* W terminalu: aktywowanie odpowiednie środowisko, a następnie uruchom języka Python. <br/> * W Jupyter: Połącz się z usługą [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz katalog PyTorch dla przykładów.  |

## <a name="mxnet-model-server"></a>Serwer modelu MXNet

|    |           |
| ------------- | ------------- |
| Co to jest?   | Serwer w celu utworzenia punktów końcowych HTTP dla modeli MXNet i ONNX      |
| Wersje maszyny DSVM obsługiwane      | Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | _mxnet modelu serwera_ jest dostępne w terminalu.   |
| Zawiera linki do przykładów      | Wyszukaj aktualne próbki [strony serwera modelu MXNet](https://github.com/awslabs/mxnet-model-server).    |
| Pokrewne narzędzia na maszyny DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>Obsługa TensorFlow

|    |           |
| ------------- | ------------- |
| Co to jest?   | Serwer, aby był uruchamiany wnioskowania modelu TensorFlow      |
| Wersje maszyny DSVM obsługiwane      | Linux     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | _tensorflow_model_server_ jest dostępna w terminalu.   |
| Zawiera linki do przykładów      | Przykłady są dostępne [online](https://www.tensorflow.org/serving/).      |
| Pokrewne narzędzia na maszyny DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Co to jest?   | Wnioskowanie serwera NVIDIA do uczenia głębokiego. |
| Wersje maszyny DSVM obsługiwane      | Ubuntu     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | TensorRT jest instalowany jako _apt_ pakietu.   |
| Zawiera linki do przykładów      | Przykłady są dostępne [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Pokrewne narzędzia na maszyny DSVM      | TensorFlow obsługująca modelu MXNet serwera  |



