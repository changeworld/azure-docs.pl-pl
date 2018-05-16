---
title: Struktury głębokie uczenie i AI - Azure | Dokumentacja firmy Microsoft
description: Platformy sztucznej inteligencji i uczenia głębokiego
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: cd8579fe29282f3875ecfddf67a34444e3de9ef5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Platformy sztucznej inteligencji i uczenia głębokiego
[Maszyny wirtualnej nauki danych](http://aka.ms/dsvm) (DSVM) i [głębokie wirtualna Learning](http://aka.ms/dsvm/deeplearning) obsługuje wiele platform głębokie learning ułatwia tworzenie aplikacji analizy sztucznego (AI) z analizy predykcyjnej i kognitywnych możliwości, takich jak obraz i zrozumienia języka. 

Poniżej przedstawiono szczegóły na wszystkich bezpośrednich uczenia struktury dostępne na DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Microsoft Toolkit kognitywnych (CNTK) jest zainstalowany w języku Python w wersji 3.5 na [systemu Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane.     |
| Pokrewne narzędzia na DSVM      | Keras      |
| Jak używać / uruchom go?    | * W terminalu: aktywowanie poprawne środowisko, a następnie uruchom Python. <br/>
 * W Jupyter: Nawiązać [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie otwórz katalog CNTK próbek. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | TensorFlow jest zainstalowany w języku Python w wersji 3.5 na [systemu Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane.     |
| Pokrewne narzędzia na DSVM      | Keras      |
| Jak używać / uruchom go?    | * W terminalu: aktywowanie poprawne środowisko, a następnie uruchom Python. <br/>
 * W Jupyter: Nawiązać [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie otwórz katalog TensorFlow próbek.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie uczenia struktury Distribued TensorFlow      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Horovod jest zainstalowany w języku Python w wersji 3.5 na [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Łącza do próbek      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Pokrewne narzędzia na DSVM      | TensorFlow      |
| Jak używać / uruchom go?    | W terminalu: aktywowanie poprawne środowisko, a następnie uruchom Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | TensorFlow jest zainstalowany w języku Python w wersji 3.5 na [systemu Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Łącza do próbek      | https://github.com/fchollet/keras/tree/master/examples      |
| Pokrewne narzędzia na DSVM      | Theano kognitywnych zestawu narzędzi, TensorFlow, Microsoft      |
| Jak używać / uruchom go?    | * W terminalu: aktywowanie poprawne środowisko, a następnie uruchom Python. <br/>
 * W Jupyter: Pobierz przykłady z lokalizacji Github, Połącz [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie otwórz katalog przykładu. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Caffe jest zainstalowany w `/opt/caffe`.    |
| Przełączanie do języka Python 2.7 | Uruchom polecenie `source activate root` |
| Łącza do próbek      | Przykłady znajdują się w `/opt/caffe/examples`.      |
| Pokrewne narzędzia na DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

Użyj X2Go, aby zalogować się do maszyny Wirtualnej, a następnie uruchom nowy terminal i wprowadź

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Otwiera nowe okno przeglądarki z notesów próbki.

Pliki binarne zostały zainstalowane w /opt/caffe/build/install/bin.

Zainstalowana wersja programu Caffe wymaga Python 2.7 i nie będzie działać z 3.5 Python aktywowane domyślnie. Uruchom `source activate root` przełączyć Anaconda środowiska. 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Caffe2 jest zainstalowany w [środowiska conda Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Źródło znajduje się w `/opt/caffe2`. |
| Łącza do próbek      | Przykładowe notesów znajdują się w JupyterHub. |
| Pokrewne narzędzia na DSVM      | Caffe      |
| Jak używać / uruchom go?    | * W terminalu: aktywowanie [środowiska Python głównego](dsvm-languages.md#python-linux-and-windows-server-2012-edition), Python i rozpoczęcie zaimportować caffe2. <br/> * W JupyterHub: [nawiązać JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie przejdź do katalogu Caffe2, aby znaleźć przykład notesów. Niektóre komputery przenośne wymagają głównego Caffe2 można ustawić w kodzie języka Python Wprowadź /opt/caffe2. |
| Uwagi dotyczące kompilacji | Caffe2 składa się z źródła w systemie Linux i obejmuje CUDA, cuDNN i Intel MKL. Bieżące zatwierdzenie jest 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, która została wybrana dla stabilności we wszystkich procesorach GPU i przykłady przetestowane. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Moduł łańcucha jest zainstalowany w [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL i ChainerCV są również instalowane.   |
| Łącza do próbek      | Przykładowe notesów znajdują się w JupyterHub. |
| Pokrewne narzędzia na DSVM      | Caffe      |
| Jak używać / uruchom go?  | * W terminalu: aktywowanie [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) środowiska uruchamiania _python_, następnie zaimportować moduł łańcucha. <br/>
* W JupyterHub: [nawiązać JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie przejdź do katalogu moduł łańcucha, aby znaleźć przykład notesów.


## <a name="deep-water"></a>Głębokie limitu górnego

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie uczenia struktury H2O      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Głębokie limitu górnego jest zainstalowany w [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i jest również dostępna w `/dsvm/tools/deep_water`.   |
| Łącza do próbek      | Przykładowe notesów znajdują się w JupyterHub.      |
| Pokrewne narzędzia na DSVM      | H2O musujących limitu górnego      |

### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

Głębokie limitu górnego wymaga CUDA 8 z cuDNN 5.1. To nie jest w ścieżce biblioteki, domyślnie jako innych platform, głębokie learning Użyj CUDA 9 i cuDNN 7. Aby użyć CUDA 8 + cuDNN 5.1 dla głębokości limitu górnego:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Aby użyć dokładnego limitu górnego:
* W terminalu: aktywowanie [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) środowiska, następnie uruchom _python_. <br/>
* W JupyterHub: [nawiązać JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie przejdź do katalogu deep_water, aby znaleźć przykład notesów.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | MXNet jest zainstalowany w `C:\dsvm\tools\mxnet` w systemie Windows i `/dsvm/tools/mxnet` w systemie Linux. Python powiązania są instalowane w języku Python w wersji 3.5 na [systemu Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Powiązania R są również instalowane na Ubuntu.   |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane.    |
| Pokrewne narzędzia na DSVM      | Keras      |
| Jak używać / uruchom go?    | * W terminalu: aktywowanie poprawne środowisko, a następnie uruchom Python. <br/>
 * W Jupyter: Nawiązać [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie otwórz katalog mxnet próbek.  |
 | Uwagi dotyczące kompilacji | MXNet składa się z źródła w systemie Linux. Ta kompilacja obejmuje CUDA, cuDNN NCCL i MKL. |

## <a name="nvidia-digits"></a>NVIDIA CYFR

|    |           |
| ------------- | ------------- |
| Co to jest?   | Bezpośrednie uczenia system z NVIDIA szybko szkoleniowe modele uczenia bezpośrednich      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Zainstalowano CYFR w `/dsvm/tools/DIGITS` i jest dostępna usługa o nazwie _cyfr_.   |
### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

Zaloguj się do maszyny Wirtualnej z X2Go. W terminalu należy uruchomić usługę:

    sudo systemctl start digits

Start, usługi trwa około jednej minuty. Uruchom przeglądarkę sieci web i przejdź do `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie NVIDIA na potrzeby zapytań o aktywności procesora GPU      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | _NVIDIA smi_ znajduje się na ścieżce systemowej.   |
| Jak używać / uruchom go? | Uruchom wiersz polecenia (w systemie Windows) lub terminal (w systemie Linux), a następnie uruchom _nvidia smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Theano jest zainstalowany w Python 2.7 (_głównego_), a także Python 3.5 (_py35_) środowiska.   |
| Pokrewne narzędzia na DSVM      | Keras      |
| Jak używać / uruchom go?    | * W terminalu aktywować wersji języka Python (głównego lub py35), uruchom python, a następnie zaimportować theano. <br/> 
* W oprogramowaniu Jupyter wybierz środowisko Python 2.7 lub jądra 3.5, a następnie zaimportować theano.  
<br/>
Aby obejść ostatnie usterki MKL, musisz najpierw ustawić MKL wątkowość warstwy:<br/><br/>
_Eksportuj MKL_THREADING_LAYER = GNU_
|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Latarka jest zainstalowany w `/dsvm/tools/torch`. PyTorch jest zainstalowany w Python 2.7 (_głównego_), a także Python 3.5 (_py35_) środowiska.   |
| Łącza do próbek      | Przykłady latarka znajdują się w folderze `/dsvm/samples/torch`. Przykłady PyTorch znajdują się w folderze `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | PyTorch jest zainstalowany w [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane i przykłady można znaleźć w /dsvm/samples/pytorch.      |
| Pokrewne narzędzia na DSVM      | Torch      |
| Jak używać / uruchom go | 
* W terminalu: aktywowanie poprawne środowisko, a następnie uruchom Python. <br/>
 * W Jupyter: Nawiązania połączenia [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), następnie otwórz katalog PyTorch próbek.  |

## <a name="mxnet-model-server"></a>Serwer modelu MXNet

|    |           |
| ------------- | ------------- |
| Co to jest?   | Serwer, aby utworzyć punkty końcowe HTTP dla modeli MXNet i ONNX      |
| DSVM obsługiwane wersje      | Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | _mxnet modelu serwera_ jest dostępna w w terminalu.   |
| Łącza do próbek      | Wyszukaj aktualne próbki [strony serwera modelu MXNet](https://github.com/awslabs/mxnet-model-server).    |
| Pokrewne narzędzia na DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>Obsługa TensorFlow

|    |           |
| ------------- | ------------- |
| Co to jest?   | Serwer do uruchamiania inferencing modelu TensorFlow      |
| DSVM obsługiwane wersje      | Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | _tensorflow_model_server_ jest dostępna w terminalu.   |
| Łącza do próbek      | Przykłady są dostępne [online](https://www.tensorflow.org/serving/).      |
| Pokrewne narzędzia na DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Co to jest?   | Bezpośrednie, learning wnioskowania serwera z NVIDIA. |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | TensorRT jest instalowany jako _stanie_ pakietu.   |
| Łącza do próbek      | Przykłady są dostępne [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Pokrewne narzędzia na DSVM      | TensorFlow obsługująca modelu MXNet serwera  |



