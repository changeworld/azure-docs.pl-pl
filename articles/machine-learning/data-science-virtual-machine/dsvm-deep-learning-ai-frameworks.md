---
title: Struktury głębokie uczenie i AI - Azure | Dokumentacja firmy Microsoft
description: Platformy sztucznej inteligencji i uczenia głębokiego
keywords: narzędzia do analizy danych, maszyny wirtualnej analizy danych, narzędzia do analizy danych, nauki danych systemu linux
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
ms.openlocfilehash: e2a0bdaf0d12a93dcee5a8a2657bb012095bb283
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Platformy sztucznej inteligencji i uczenia głębokiego
[Maszyny wirtualnej nauki danych](http://aka.ms/dsvm) (DSVM) i [głębokie wirtualna Learning](http://aka.ms/dsvm/deeplearning) obsługuje wiele platform głębokie learning ułatwia tworzenie aplikacji analizy sztucznego (AI) z analizy predykcyjnej i kognitywnych możliwości, takich jak obraz i zrozumienia języka. 

Poniżej przedstawiono szczegóły na wszystkich bezpośrednich uczenia struktury dostępne na DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Microsoft Toolkit kognitywnych (CNTK) jest zainstalowana w Python 2.7 w _głównego_ środowiska, a także Python 3.5 w _py35_ środowiska.   |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane.     |
| Pokrewne narzędzia na DSVM      | Keras      |
| Jak używać / uruchom go?    | Otwórz Jupyter, a następnie wyszukaj CNTK folder  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | W systemie Linux TensorFlow jest zainstalowany w Python 2.7 (_głównego_), a także Python 3.5 (_py35_) środowiska. W systemie Windows, Tensorflow jest zainstalowany w języku Python w wersji 3.5 (_py35_) środowiska.  |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane.     |
| Pokrewne narzędzia na DSVM      | Keras      |
| Jak używać / uruchom go?    | Otwórz Jupyter, a następnie wyszukaj TensorFlow folder.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Keras jest zainstalowany w Python 2.7 (_głównego_), a także Python 3.5 (_py35_) środowiska.   |
| Łącza do próbek      | https://github.com/fchollet/keras/tree/master/examples      |
| Pokrewne narzędzia na DSVM      | Theano kognitywnych zestawu narzędzi, TensorFlow, Microsoft      |
| Jak używać / uruchom go?    | Pobierz przykłady z lokalizacji Github, skopiuj go do katalogu, w obszarze ~/notebooks i otwórz go w aplikacji Jupyter   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Caffe jest zainstalowany w `/opt/caffe`.    |
| Łącza do próbek      | Przykłady znajdują się w `/opt/caffe/examples`.      |
| Pokrewne narzędzia na DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

Użyj X2Go, aby zalogować się do maszyny Wirtualnej, a następnie uruchom nowy terminal i wprowadź

```
cd /opt/caffe/examples
jupyter notebook
```

Otwiera nowe okno przeglądarki z notesów próbki.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Caffe2 jest zainstalowany w `/opt/caffe2`. Jest również dostępny do języka Python 2.7 (_głównego_) conda środowiska.     |
| Łącza do próbek      | Uwzględniono notesów Jupyter próbki     |
| Pokrewne narzędzia na DSVM      | Caffe      |
| Jak używać / uruchom go?    | Otwórz Jupyter, a następnie przejdź do katalogu Caffe2, aby znaleźć przykład notesów. Niektóre komputery przenośne wymagają głównego Caffe2 można ustawić w kodzie języka Python Wprowadź /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Moduł łańcucha jest zainstalowany w Python 2.7 (_głównego_), a także Python 3.5 (_py35_) środowiska. ChainerRL i ChainerCV są również instalowane.   |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane.      |
| Pokrewne narzędzia na DSVM      | Caffe      |

### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

W terminalu, aktywować odpowiednią wersję języka Python (_głównego_ lub _py35_) uruchom _python_, następnie zaimportować moduł łańcucha. W oprogramowaniu Jupyter wybierz środowisko Python 2.7 lub jądra 3.5, a następnie zaimportować moduł łańcucha.


## <a name="deep-water"></a>Głębokie limitu górnego

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie uczenia struktury H2O      |
| DSVM obsługiwane wersje      | Ubuntu     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | Głębokie limitu górnego jest zainstalowany w `/dsvm/tools/deep_water`.   |
| Łącza do próbek      | Przykłady są dostępne za pośrednictwem serwera głębokie limitu górnego.      |
| Pokrewne narzędzia na DSVM      | H2O musujących limitu górnego      |

### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

Połączenie z maszyną Wirtualną za pomocą X2Go. W terminalu uruchom serwer głębokie limitu górnego:

    java -jar /dsvm/tools/deep_water/h2o.jar

Następnie otwórz przeglądarkę i połącz się `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Co to jest?   | Głębokie learning framework      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | MXNet jest zainstalowany w `C:\dsvm\tools\mxnet` w systemie Windows i `/dsvm/tools/mxnet` w systemie Linux. Python powiązania są instalowane w języku Python 2.7 (_głównego_), a także Python 3.5 (_py35_) środowiska. Powiązania R są również instalowane.   |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane.    |
| Pokrewne narzędzia na DSVM      | Keras      |
| Jak używać / uruchom go?    | Otwórz Jupyter, a następnie wyszukaj mxnet folder  |

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
| Jak używać / uruchom go?    | W terminalu aktywować wersji języka Python (głównego lub py35), uruchom python, a następnie zaimportować theano. W oprogramowaniu Jupyter wybierz środowisko Python 2.7 lub jądra 3.5, a następnie zaimportować theano.  |



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
| Jak jest on skonfigurowany / zainstalowanym DSVM?  | PyTorch jest zainstalowany w wersji 3.5 języka Python (_py35_) środowiska.   |
| Łącza do próbek      | Notesów Jupyter próbki są uwzględniane i przykłady można znaleźć w /dsvm/samples/pytorch.      |
| Pokrewne narzędzia na DSVM      | Torch      |

### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

W terminalu, uruchom _python_, następnie zaimportować latarka. W oprogramowaniu Jupyter wybierz jądra Python 3.5, a następnie zaimportować latarka.


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
