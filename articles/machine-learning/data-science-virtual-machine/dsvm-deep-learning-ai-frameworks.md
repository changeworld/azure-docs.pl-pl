---
title: Głębokie uczenie & platformy AI
titleSuffix: Azure Data Science Virtual Machines
description: Platformy uczenia głębokiego i narzędzia na platformie Azure Data Science Virtual Machine, w tym TensorFlow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, Łańcucher itd.
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
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: 3c9510b99bbdc7db292ecdc0bf59aadc305cf0c8
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035836"
---
# <a name="deep-learning-and-ai-frameworks"></a>Platformy sztucznej inteligencji i uczenia głębokiego
[Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) obsługuje wiele platform uczenia głębokiego, które ułatwiają tworzenie aplikacji sztucznej inteligencją (AI) z analizą predykcyjną i funkcjami poznawczymi, takimi jak zrozumienie obrazu i języka.

Platformy uczenia głębokiego dostępne na DSVM obejmują:

+ TensorFlow
+ PyTorch
+ Biblioteki Keras
+ Caffe
+ Caffe2
+ Torch
+ Serwer modelu MXNet
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Głębokie limitu górnego
+ PROCESORY GPU NVIDIA CYFR
+ procesory GPU NVIDIA smi
+ Obsługa TensorFlow
+ TensorRT
+ Zestaw narzędzi usług Microsoft Cognitive

|Narzędzia&nbsp;&nbsp;DLwwitrynieDSVM&nbsp;|Windows|Linux|Uwagi&nbsp;dotyczące użycia|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | Tak (Windows 2016) | Tak |Zainstalowane w języku Python 3,5 w systemach [Linux i windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Przykładowe notesy Jupyter są zawarte w DSVM.<br/><br/>**Aby go uruchomić**:<br/>* Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> Jupyter Połącz się z usługą [Jupyter](provision-vm.md#tools) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz katalog TensorFlow, aby zapoznać się z przykładami.  |
|[PyTorch](https://pytorch.org/)| Nie | Tak |Zainstalowane w języku [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Przykładowe notesy Jupyter są dołączone, a przykłady znajdują się w/dsvm/Samples/pytorch.    <br/><br/>**Aby uruchomić go**<br/>* Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Połącz się, a następnie otwórz katalog PyTorch na potrzeby przykładów.  |
|[Keras](https://keras.io/)| Tak | Tak |Interfejs API jest instalowany w języku Python 3,5 w systemach [Linux i windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Przykłady](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Aby go uruchomić**:<br/>* Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> Jupyter Pobierz przykłady z lokalizacji GitHub, Połącz się z usługą [Jupyter](provision-vm.md#tools) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz przykładowy katalog. |
|[Caffe](https://github.com/caffe2/caffe2) | Nie |Tak (Ubuntu)|Caffe jest zainstalowany w `/opt/caffe`.   Przykłady znajdują `/opt/caffe/examples`się w. <br/><br/>**Aby go uruchomić**, użyj x2go do logowania się do maszyny wirtualnej, a następnie uruchom nowy terminal i wprowadź:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otwiera nowe okno przeglądarki z notesami próbki. Pliki binarne są instalowane w /opt/caffe/build/install/bin.<br/><br/>Zainstalowana wersja Caffe wymaga środowiska Python 2.7 i nie będzie działać z języka Python 3.5 aktywowana domyślnie. Aby przełączyć się do języka Python 2,7 `source activate root` , uruchom polecenie w celu przełączenia środowiska Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Nie |Tak (Ubuntu)|Caffe2 jest zainstalowany w [środowiska conda środowisko Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Trwa źródłowego `/opt/caffe2`.<br/>Notesy próbki są objęte JupyterHub.<br/><br/>**Aby go uruchomić**:<br/>* W terminalu: aktywowanie [środowisko Python głównego](dsvm-languages.md#python-linux-and-windows-server-2012-edition), Python i rozpoczęcie importowania caffe2. <br/> * W JupyterHub: [nawiązać połączenie z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie przejdź do katalogu Caffe2 można znaleźć przykładowe notesy. Niektóre notesów wymagają głównego Caffe2 w kod Python; Wprowadź /opt/caffe2. |
|[Torch](http://torch.ch/) | Nie |Tak (Ubuntu)|Latarka jest zainstalowany w `/dsvm/tools/torch`. PyTorch jest zainstalowany w środowisko Python 2.7 (_głównego_), a także język Python 3.5 (_py35_) środowiska. Próbki Torch znajdują `/dsvm/samples/torch` się w i PyTorch próbki `/dsvm/samples/pytorch`znajdują się w. |
|[MXNet](https://mxnet.io/) | Tak (Windows 2016) | Tak|MXNet jest zainstalowany w `C:\dsvm\tools\mxnet` na Windows i `/dsvm/tools/mxnet` w systemie Linux. Python powiązania są zainstalowane w języku Python 3.5 na [systemów Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i 3.6 języka Python na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Powiązań języka R są również instalowane w systemie Ubuntu.<br/><br/>Notesy Jupyter próbki są uwzględniane. <br/><br/>**Aby go uruchomić**:<br/>Terminal Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> Jupyter Połącz się z usługą [Jupyter](provision-vm.md#tools) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz katalog mxnet, aby zapoznać się z przykładami.|
|[Serwer modelu MXNet](https://github.com/awslabs/mxnet-model-server) | Nie | Tak |Serwer służący do tworzenia punktów końcowych HTTP dla modeli MXNet i ONNX. _mxnet modelu serwera_ jest dostępne w terminalu. Przykłady na [stronie serwer modelu MXNet](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Nie | Tak (Ubuntu) |Dystrybuowana platforma uczenia głębokiego dla TensorFlow. Horovod jest zainstalowany w języku Python 3.5 na [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Zobacz przykłady](https://github.com/uber/horovod/tree/master/examples)<br/><br/>**Aby go uruchomić**, Aktywuj odpowiednie środowisko w terminalu, a następnie uruchom Język Python. |
|[Theano](https://github.com/Theano/Theano) | Nie | Tak (Ubuntu) |Theano jest zainstalowany w środowisko Python 2.7 (_głównego_), a także język Python 3.5 (_py35_) środowiska.<br/><br/>**Aby go uruchomić**: <br/>Terminal Aktywuj żądaną wersję języka Python (root lub py35), uruchom środowisko Python, a następnie zaimportuj Theano.<br/>Jupyter Wybierz jądro Python 2,7 lub 3,5, a następnie Importuj Theano.  <br/>Aby obejść ostatnie usterki MKL, musisz najpierw ustawić MKL wątkowości warstwy:<br/><br/>_Export MKL_THREADING_LAYER = GNU_|
|[Chainer](https://chainer.org/) |Nie | Tak |Chainer jest zainstalowany w [język Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL i ChainerCV są również instalowane. <br/><br/>Notesy próbki są objęte JupyterHub.<br/><br/>**Aby go uruchomić**: <br/>Terminal Aktywuj środowisko [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , uruchom język _Python_, a następnie zaimportuj moduł łańcucha. <br/> * JupyterHub: [Połącz](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)się z usługą JupyterHub, a następnie przejdź do katalogu łańcucha, aby znaleźć przykładowe notesy.|
|[Cyfry NVidia](https://github.com/NVIDIA/DIGITS) | Nie | Tak (Ubuntu) |System uczenia głębokiego firmy NVIDIA do szybkiego szkolenia modeli uczenia głębokiego. CYFRY jest zainstalowany w `/dsvm/tools/DIGITS` i jest dostępna usługa o nazwie _cyfr_.  <br/><br/>**Aby go uruchomić**: <br/>Zaloguj się do tej maszyny Wirtualnej z X2Go. W terminalu uruchom usługę ```sudo systemctl start digits```. <br/><br/>Usługa zajmuje około jednej minuty do uruchomienia. Uruchom przeglądarkę internetową i przejdź do `http://localhost:5000`. Należy pamiętać, że CYFR nie bezpiecznego logowania i nie powinny być udostępniane poza maszyny Wirtualnej.|
|[CUDA, cuDNN, sterownik NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Tak | Tak | |
|procesory GPU NVIDIA smi|Tak | Tak |Narzędzie NVIDIA do wykonywania zapytań dotyczących aktywności procesora GPU. _procesory GPU NVIDIA smi_ jest dostępny w ścieżce systemowej. <br/><br/>Uruchom wiersz polecenia (na Windows) lub terminalu (w systemie Linux), a następnie uruchom _nvidia smi_.|
|[TensorFlow obsługujące](https://www.tensorflow.org/serving/) | Nie | Tak |Serwer do wnioskowania w modelu TensorFlow. _tensorflow_model_server_ jest dostępna w terminalu. Przykłady są dostępne [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Nie | Tak (Ubuntu) |Wnioskowanie serwera NVIDIA do uczenia głębokiego. TensorRT jest instalowany jako _apt_ pakietu. Przykłady są dostępne [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Tak | Tak | Zainstalowane w języku Python 3,5 w systemach [Linux i windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Przykładowe notesy Jupyter są zawarte w DSVM. <br/><br/>**Aby go uruchomić**: <br/>Terminal Aktywuj poprawne środowisko i uruchamiaj Język Python. <br/>Jupyter Połącz się z usługą [Jupyter](provision-vm.md#tools) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie otwórz katalog CNTK, aby zapoznać się z przykładami. |
|Głębokie limitu górnego|Nie | Tak (Ubuntu) |Platforma uczenia głębokiego dla H2O, głębokiej wody jest instalowana w języku [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i `/dsvm/tools/deep_water`jest również dostępna w. Notesy próbki są objęte JupyterHub. Głębokie wody wymaga CUDA 8 cuDNN 5.1. To nie jest w ścieżce biblioteki, domyślnie CUDA 9 i cuDNN 7 używania w innych platform uczenia głębokiego. Na potrzeby architektury CUDA 8 + cuDNN 5.1 głębokiego limitu górnego:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Aby użyć głębokiego limitu górnego:<br/>* Terminal: Aktywuj środowisko [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , a następnie uruchom język _Python_. <br/>* JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), a następnie przejdź do katalogu deep_water, aby znaleźć przykładowe notesy.|
