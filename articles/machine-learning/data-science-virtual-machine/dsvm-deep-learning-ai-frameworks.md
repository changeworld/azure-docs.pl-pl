---
title: Głębokie uczenie & platformy AI
titleSuffix: Azure Data Science Virtual Machine
description: Dostępne platformy i narzędzia uczenia głębokiego na platformie Azure Data Science Virtual Machine, w tym TensorFlow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, Łańcucher itd.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: d8d351659bcfdcdddcc09831b5f95f580bf38f1e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170866"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>Uczenie głębokie i platformy AI dla platformy Azure Data Science VM
[Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) obsługuje wiele struktur głębokiego uczenia, które ułatwiają tworzenie sztucznych aplikacji do analizy predykcyjnej i możliwości poznawczych, takich jak zrozumienie obrazu i języka.

Platformy uczenia głębokiego dostępne za poorednictwem DSVM obejmują:

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

|Narzędzia&nbsp;&nbsp;głębokiegouczeniawwitrynie&nbsp;DSVM|Windows|Linux|Uwagi&nbsp;dotyczące użycia|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | Tak (Windows 2016) | Tak |Zainstalowane w języku Python 3,5 w systemach [Linux i windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Przykładowe notesy Jupyter są zawarte w DSVM.<br/><br/>**Aby go uruchomić**:<br/>Terminal Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> Jupyter Połącz się z usługą [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog TensorFlow, aby zapoznać się z przykładami.  |
|[PyTorch](https://pytorch.org/)| Nie | Tak |Zainstalowane w języku [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Przykładowe notesy Jupyter są dołączone, a przykłady znajdują się w/dsvm/Samples/pytorch.    <br/><br/>**Aby go uruchomić**:<br/>Terminal Aktywuj poprawne środowisko, a następnie uruchom Język Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Połącz się, a następnie otwórz katalog PyTorch na potrzeby przykładów.  |
|[Keras](https://keras.io/)| Tak | Tak |Interfejs API jest instalowany w języku Python 3,5 w systemach [Linux i windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) oraz w języku Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Zobacz przykłady](https://github.com/fchollet/keras/tree/master/examples).<br/><br/>**Aby go uruchomić**:<br/>Terminal Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> Jupyter Pobierz przykłady z lokalizacji GitHub, Połącz się z usługą [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz przykładowy katalog. |
|[Caffe](https://github.com/caffe2/caffe2) | Nie |Tak (Ubuntu)|Caffe jest zainstalowany w `/opt/caffe`.   Przykłady znajdują `/opt/caffe/examples`się w. <br/><br/>**Aby go uruchomić**, użyj x2go, aby zalogować się do maszyny wirtualnej, a następnie uruchom nowy terminal, a następnie wprowadź następujące polecenie:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otwiera nowe okno przeglądarki z notesami próbki. Pliki binarne są instalowane w /opt/caffe/build/install/bin.<br/><br/>Zainstalowana wersja programu Caffe wymaga języka Python 2,7 i nie będzie współdziałać z językiem Python 3,5, który jest domyślnie aktywowany. Aby przełączyć się do języka Python 2,7 `source activate root` , uruchom polecenie, aby przełączyć się do środowiska Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Nie |Tak (Ubuntu)|Caffe2 jest zainstalowany w [środowiska conda środowisko Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Trwa źródłowego `/opt/caffe2`.<br/>Notesy próbki są objęte JupyterHub.<br/><br/>**Aby go uruchomić**:<br/>Terminal Aktywuj [Główne środowisko Python](dsvm-languages.md#python-linux-and-windows-server-2012-edition), uruchom Python i zaimportuj Caffe2. <br/> * JupyterHub: [Połącz](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)się z usługą JupyterHub, a następnie przejdź do katalogu Caffe2, aby znaleźć przykładowe notesy. Niektóre notesów wymagają głównego Caffe2 w kod Python; Wprowadź /opt/caffe2. |
|[Torch](http://torch.ch/) | Nie |Tak (Ubuntu)|Latarka jest zainstalowany w `/dsvm/tools/torch`. PyTorch jest zainstalowany w środowisko Python 2.7 (_głównego_), a także język Python 3.5 (_py35_) środowiska. Próbki Torch znajdują `/dsvm/samples/torch` się w i PyTorch próbki `/dsvm/samples/pytorch`znajdują się w. |
|[MXNet](https://mxnet.io/) | Tak (Windows 2016) | Tak|MXNet jest zainstalowany w `C:\dsvm\tools\mxnet` na Windows i `/dsvm/tools/mxnet` w systemie Linux. Python powiązania są zainstalowane w języku Python 3.5 na [systemów Linux i Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i 3.6 języka Python na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Powiązań języka R są również instalowane w systemie Ubuntu.<br/><br/>Notesy Jupyter próbki są uwzględniane. <br/><br/>**Aby go uruchomić**:<br/>Terminal Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> Jupyter Połącz się z usługą [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog mxnet, aby zapoznać się z przykładami.|
|[Serwer modelu MXNet](https://github.com/awslabs/mxnet-model-server) | Nie | Tak |Serwer służący do tworzenia punktów końcowych HTTP dla modeli MXNet i ONNX. _Mxnet-model-Server_ jest dostępny w terminalu. Przykłady na [stronie serwer modelu MXNet](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Nie | Tak (Ubuntu) |Dystrybuowana struktura głębokiego uczenia dla TensorFlow. Horovod jest zainstalowany w języku Python 3.5 na [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Zobacz przykłady](https://github.com/uber/horovod/tree/master/examples).<br/><br/>**Aby go uruchomić**, Aktywuj odpowiednie środowisko w terminalu, a następnie uruchom Język Python. |
|[Theano](https://github.com/Theano/Theano) | Nie | Tak (Ubuntu) |Theano jest zainstalowany w języku Python 2,7 (_root_) i w środowisku Python 3,5 (_py35_).<br/><br/>**Aby go uruchomić**: <br/>Terminal Aktywuj żądaną wersję języka Python (root lub py35), uruchom środowisko Python, a następnie zaimportuj Theano.<br/>Jupyter Wybierz jądro Python 2,7 lub 3,5, a następnie zaimportuj Theano.  <br/>Aby obejść ostatnią usterkę biblioteki jądra matematycznego (MKL), musisz najpierw ustawić warstwę wątku MKL w następujący sposób:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |Nie | Tak |Chainer jest zainstalowany w [język Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL i ChainerCV są również instalowane. <br/><br/>Notesy próbki są objęte JupyterHub.<br/><br/>**Aby go uruchomić**: <br/>Terminal Aktywuj środowisko [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , uruchom język _Python_, a następnie zaimportuj moduł łańcucha. <br/> * JupyterHub: [Połącz](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)się z usługą JupyterHub, a następnie przejdź do katalogu łańcucha, aby znaleźć przykładowe notesy.|
|[Cyfry NVidia](https://github.com/NVIDIA/DIGITS) | Nie | Tak (Ubuntu) |System uczenia głębokiego firmy NVIDIA do szybkiego szkolenia modeli uczenia głębokiego. Cyfry są instalowane w `/dsvm/tools/DIGITS` programie i są dostępne jako _cyfry_usługi o nazwach.  <br/><br/>**Aby go uruchomić**: <br/>Zaloguj się do maszyny wirtualnej za pomocą X2Go. W terminalu uruchom usługę, uruchamiając ```sudo systemctl start digits```polecenie. <br/><br/>Usługa zajmuje około jednej minuty do uruchomienia. Otwórz przeglądarkę internetową i przejdź do `http://localhost:5000`. Należy pamiętać, że CYFR nie bezpiecznego logowania i nie powinny być udostępniane poza maszyny Wirtualnej.|
|[CUDA, cuDNN, sterownik NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Tak | Tak | |
|procesory GPU NVIDIA smi|Tak | Tak |Narzędzie NVIDIA do wykonywania zapytań dotyczących aktywności procesora GPU; _NVIDIA-SMI_ jest dostępny w ścieżce systemowej. <br/><br/>Otwórz wiersz polecenia (w systemie Windows) lub terminal (na komputerze z systemem Linux), a następnie uruchom polecenie _NVIDIA-SMI_.|
|[TensorFlow obsługujące](https://www.tensorflow.org/serving/) | Nie | Tak |Serwer do wnioskowania w modelu TensorFlow; tensorflow_model_server jest dostępny w terminalu. Przykłady są dostępne [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Nie | Tak (Ubuntu) |Serwer wnioskowania o głębokiej uczeniu od firmy NVIDIA. TensorRT jest instalowany jako _apt_ pakietu. Przykłady są dostępne [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Tak | Tak | Zainstalowane w języku Python 3,5 w systemach [Linux i windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i Python 3,6 w [systemie Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Przykładowe notesy Jupyter są zawarte w DSVM. <br/><br/>**Aby go uruchomić**: <br/>Terminal Aktywuj poprawne środowisko i uruchamiaj Język Python. <br/>Jupyter Połącz się z usługą [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog CNTK, aby zapoznać się z przykładami. |
|Głębokie limitu górnego|Nie | Tak (Ubuntu) |Platforma uczenia głębokiego dla H2O, głębokiej wody jest instalowana w języku [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) i jest `/dsvm/tools/deep_water`również dostępna w. Notesy próbki są objęte JupyterHub. Głębokie wody wymaga CUDA 8 cuDNN 5.1. Domyślnie nie znajduje się to w ścieżce biblioteki, ponieważ inne platformy uczenia głębokiego używają CUDA 9 i cuDNN 7. Aby użyć programu, zainstaluj CUDA 8 + cuDNN 5,1 dla głębokiej wody:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Aby użyć głębokiego limitu górnego:<br/>Terminal Aktywuj środowisko [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , a następnie uruchom język _Python_. <br/>* JupyterHub: [Połącz](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)się z usługą JupyterHub, a następnie przejdź do katalogu deep_water, aby znaleźć przykładowe notesy.|
