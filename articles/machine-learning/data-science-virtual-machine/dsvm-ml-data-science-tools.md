---
title: Uczenie maszynowe & narzędzia do nauki o danych
titleSuffix: Azure Data Science Virtual Machine
description: Więcej informacji na temat narzędzi i platform wstępnie zainstalowanych na maszynie wirtualnej do nauki o danych uczenia maszynowego.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f97412bc3f8d8b046df1324c7e1a0ad568f5db52
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278777"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Uczenie maszynowe i narzędzia do nauki o danych w usłudze Azure Data Learning Virtual Machines
Maszyn wirtualnych do nauki o danych zawiera bogaty zestaw narzędzi i biblioteki dla usługi machine learning (ML) dostępna w popularnych języków, takich jak Python, R i Julia. 

Poniżej przedstawiono niektóre narzędzia ML i bibliotek na maszynach wirtualnych do nauki o danych. 

## <a name="azure-machine-learning-sdk-for-python"></a>Zestaw SDK Azure Machine Learning dla języka Python
Zobacz pełne informacje dotyczące [zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Co to jest? |   Usługa Azure Machine Learning to usługa w chmurze, który służy do opracowywania i wdrażania modeli uczenia Maszynowego. Możesz śledzić swoje modele, jak tworzenie, szkolenie, skalowanie i zarządzać nimi za pomocą zestawu SDK języka Python. Wdrażanie modeli jako kontenery i uruchamiać je w chmurze, lokalnie lub w usłudze Azure IoT Edge. |
| Obsługiwane wersje  | Windows (środowisko Conda: Azure), Linux (Conda Environment: py36) |
| Typowe zastosowania      | Ogólne platformy uczenia Maszynowego  |
| Jak jest ona skonfigurowana lub zainstalowane? |  Zainstalowano z obsługą procesorów GPU |
| Jak za pomocą lub uruchomić go | Jak dla języka Python SDK i platformą Azure. Aktywuj, aby środowiska conda `AzureML` w wersji Windows *lub* do `py36` w wersji systemu Linux.  |
| Łącza do przykładów | Przykładowy program Jupyter notebooks znajdują się w `AzureML` katalogu w ramach notesów.  |
| Pokrewnych narzędzi  | Program Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co to jest?   |    XGBoost jest szybkie, przenośne i rozproszonych gradientu zwiększania wyniku biblioteki (GBDT, GBRT lub GBM) dla języka Python, R, Java, Scala, C++ i więcej. Działa na jednej maszynie, Hadoop i Spark.    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne biblioteki uczenia Maszynowego      |
| Jak jest ona skonfigurowana lub zainstalowane?      |  Zainstalowano z obsługą procesorów GPU   |
| Jak za pomocą lub uruchomić go      | Jako Python biblioteki (w wersji 2.7 i 3.5), pakietu języka R, a na ścieżce, narzędzie wiersza polecenia (`C:\dsvm\tools\xgboost\bin\xgboost.exe` dla Windows, `/dsvm/tools/xgboost/xgboost` dla systemu Linux)    |
| Zawiera linki do przykładów      | Przykłady znajdują się na maszynie Wirtualnej, w `/dsvm/tools/xgboost/demo` w systemie Linux i `C:\dsvm\tools\xgboost\demo` na Windows.   |
| Pokrewnych narzędzi      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Vowpal Wabbit (znany także jako "VW") jest typu open source, szybkie uczenie biblioteka systemu poza programu core.    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne biblioteki uczenia Maszynowego      |
| Jak jest ona skonfigurowana lub zainstalowane?      |  Windows — Instalator msi, systemu Linux — polecenia apt-get |
| Jak za pomocą lub uruchomić go      | Jako narzędzie wiersza polecenia na ścieżce (`C:\Program Files\VowpalWabbit\vw.exe` na Windows, `/usr/bin/vw` w systemie Linux)    |
| Łącza do przykładów      | [Przykłady VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Pokrewnych narzędzi      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Weka jest kolekcją algorytmów uczenia Maszynowego dla danych wyszukiwania zadań. Te algorytmy można stosować bezpośrednio do zestawu danych lub wywoływana z kodu Java. Weka zawiera narzędzia do wstępnego przetwarzania danych, klasyfikacji, regresji, klastrowanie, reguły kojarzenia i wizualizacji. |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Narzędzie uczenia Maszynowego ogólnego     |
| Jak za pomocą lub uruchomić go      | W Windows wyszukaj Weka, w Start menu. W systemie Linux, zaloguj się przy użyciu X2Go, a następnie przejdź do **aplikacje** > **rozwoju** > **Weka**. |
| Łącza do przykładów      | [Przykłady weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Pokrewnych narzędzi      |XGBoost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Rattle jest graficzny interfejs użytkownika do wyszukiwania danych przy użyciu języka R.   |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Narzędzie do wyszukiwania danych ogólne interfejsu użytkownika dla języka R    |
| Jak za pomocą lub uruchomić go      | Narzędzia interfejsu użytkownika. Na Windows, uruchom wiersz polecenia, uruchamiaj język R, a następnie wewnątrz języka R Uruchom `rattle()`. W systemie Linux, połącz się z X2Go, uruchom w terminalu, uruchamiaj język R, a następnie wewnątrz języka R Uruchom `rattle()`. |
| Łącza do przykładów      | [Rattle](https://togaware.com/onepager/) |
| Pokrewnych narzędzi      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Co to jest?   | LightGBM jest gradientem, szybka i rozproszonej o wysokiej wydajności, ulepszanie framework (GBDT, GBRT, GBM lub SKŁADNICY) oparte na algorytmy drzewa decyzyjnego. Służy do oceniania, klasyfikacji i wiele innych zadań uczenia Maszynowego.    |
| Obsługiwane wersje      | Windows, Linux    |
| Typowe zastosowania      | Ogólnego przeznaczenia framework zwiększenie gradientu      |
| Jak jest ona skonfigurowana lub zainstalowane?      | W Windows LightGBM jest instalowany jako pakiet języka Python. W systemie Linux, pliku wykonywalnego wiersza polecenia znajduje się w `/opt/LightGBM/lightgbm`pakietu języka R jest zainstalowany i zainstalowanych pakietów języka Python.     |
| Łącza do przykładów      | [Przewodnik LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Pokrewnych narzędzi      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Co to jest?   | H2O to platforma sztucznej Inteligencji typu open source, która obsługuje uczenia Maszynowego w pamięci, rozproszonej, szybkie i skalowalne.  |
| Obsługiwane wersje      | Linux   |
| Typowe zastosowania      | Ogólnego przeznaczenia rozproszonych, skalowalnych ML   |
| Jak jest ona skonfigurowana lub zainstalowane?      | H2O jest zainstalowany w `/dsvm/tools/h2o`.      |
| Jak za pomocą lub uruchomić go      | Łączenie z maszyną Wirtualną za pomocą X2Go. Uruchom nowy terminal i uruchom `java -jar /dsvm/tools/h2o/current/h2o.jar`. Następnie uruchom przeglądarkę internetową i połącz się z `http://localhost:54321`.      |
| Łącza do przykładów      | Przykłady są dostępne na maszynie Wirtualnej w programie Jupyter w obszarze `h2o` katalogu.      |
| Pokrewnych narzędzi      | Platforma Apache Spark, MXNet, XGBoost, musujące wody, wody głębokiego    |

Istnieje kilka innych bibliotek ML na maszynach wirtualnych do nauki o danych takich jak popularnej `scikit-learn` pakiet, który jest dostarczany jako część dystrybucji Anaconda Python, który jest instalowany na maszynach wirtualnych do nauki o danych. W celu zapoznania się z listy pakietów, które są dostępne w języku Python, R i Julia, uruchom menedżerów odpowiednich pakietów.
