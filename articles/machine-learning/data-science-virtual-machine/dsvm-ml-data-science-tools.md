---
title: Narzędzia do analizy nauki i dane maszyny
description: Dowiedz się więcej o narzędziach i strukturach uczenia maszynowego, które są preinstalowane na Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: dec1589898cf6cc239dbe737519e078f1c4bc7b1
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744922"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Uczenie maszynowe i narzędzia do nauki o danych w usłudze Azure Data Learning Virtual Machines
Usługa Azure Data Learning Virtual Machines (DSVMs) oferuje bogaty zestaw narzędzi i bibliotek do uczenia maszynowego dostępnych w popularnych językach, takich jak Python, R i Julia.

Poniżej przedstawiono niektóre narzędzia i biblioteki uczenia maszynowego w systemie DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>Zestaw SDK Azure Machine Learning dla języka Python
Zobacz pełne informacje dotyczące [zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Co to jest?   |   Azure Machine Learning to usługa w chmurze, za pomocą której można tworzyć i wdrażać modele uczenia maszynowego. Możesz śledzić swoje modele, jak tworzenie, szkolenie, skalowanie i zarządzać nimi za pomocą zestawu SDK języka Python. Wdrażanie modeli jako kontenery i uruchamiać je w chmurze, lokalnie lub w usłudze Azure IoT Edge.   |
| Obsługiwane wersje     | Windows (środowisko Conda: Azure), Linux (Conda Environment: py36)    |
| Typowe zastosowania      | Ogólna platforma uczenia maszynowego      |
| Jak jest ona skonfigurowana lub zainstalowane?      |  Zainstalowano z obsługą procesorów GPU   |
| Jak za pomocą lub uruchomić go      | Jako zestaw SDK języka Python i w interfejsie wiersza polecenia platformy Azure. Aktywuj, aby środowiska conda `AzureML` w wersji Windows *lub* do `py36` w wersji systemu Linux.      |
| Łącza do przykładów      | Przykładowy program Jupyter notebooks znajdują się w `AzureML` katalogu w ramach notesów.  |
| Pokrewnych narzędzi      | Program Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Szybka, przenośna i rozproszona Biblioteka do zwiększania gradientu (GBDT, GBRT lub GBM) dla języków Python, R, Java, Scala C++, i innych. Działa na jednej maszynie oraz na Apache Hadoop i Spark.    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólna Biblioteka uczenia maszynowego      |
| Jak jest ona skonfigurowana lub zainstalowane?      |  Zainstalowano z obsługą procesorów GPU   |
| Jak za pomocą lub uruchomić go      | Jako biblioteka Python (2,7 i 3,5), pakiet języka R i narzędzie wiersza polecenia on-Path (`C:\dsvm\tools\xgboost\bin\xgboost.exe` dla systemów Windows i `/dsvm/tools/xgboost/xgboost` Linux)    |
| Zawiera linki do przykładów      | Przykłady znajdują się na maszynie Wirtualnej, w `/dsvm/tools/xgboost/demo` w systemie Linux i `C:\dsvm\tools\xgboost\demo` na Windows.   |
| Pokrewnych narzędzi      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Szybka, niepodstawowa Biblioteka systemu szkoleniowego typu "open source"    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólna Biblioteka uczenia maszynowego      |
| Jak jest ona skonfigurowana lub zainstalowane?      |  Windows: instalator MSI<br/>Linux: apt — Pobierz |
| Jak za pomocą lub uruchomić go      | Jako narzędzie wiersza polecenia na ścieżce (`C:\Program Files\VowpalWabbit\vw.exe` na Windows, `/usr/bin/vw` w systemie Linux)    |
| Łącza do przykładów      | [Przykłady VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Pokrewnych narzędzi      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Kolekcja algorytmów uczenia maszynowego na potrzeby zadań wyszukiwania danych. Te algorytmy można stosować bezpośrednio do zestawu danych lub wywoływana z kodu Java. Weka zawiera narzędzia do wstępnego przetwarzania danych, klasyfikacji, regresji, klastrowanie, reguły kojarzenia i wizualizacji. |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie uczenia maszynowego     |
| Jak za pomocą lub uruchomić go      | W systemie Windows wyszukaj pozycję Weka w menu **Start** . W systemie Linux, zaloguj się przy użyciu X2Go, a następnie przejdź do **aplikacje** > **rozwoju** > **Weka**. |
| Łącza do przykładów      | [Przykłady weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Pokrewnych narzędzi      |XGBoost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika służący do wyszukiwania danych przy użyciu języka R.   |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie wyszukiwania danych interfejsu użytkownika dla języka R    |
| Jak za pomocą lub uruchomić go      | Jako narzędzie interfejsu użytkownika. W systemie Windows Uruchom wiersz polecenia, uruchom polecenie R, a następnie w języku R, `rattle()`Uruchom polecenie. W systemie Linux Połącz się z usługą X2Go, uruchom terminal, uruchom polecenie R, a następnie w obszarze `rattle()`r Uruchom polecenie. |
| Łącza do przykładów      | [Rattle](https://togaware.com/onepager/) |
| Pokrewnych narzędzi      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Co to jest?   | Szybkie, rozproszone i wysoce wydajne środowisko do zwiększania gradientu (GBDT, GBRT, GBM lub SKŁADNIe) w oparciu o algorytmy drzewa decyzyjnego. Jest on używany do klasyfikowania, klasyfikowania i wielu innych zadań uczenia maszynowego.    |
| Obsługiwane wersje      | Windows, Linux    |
| Typowe zastosowania      | Środowisko zwiększania gradientu ogólnego przeznaczenia      |
| Jak jest ona skonfigurowana lub zainstalowane?      | W Windows LightGBM jest instalowany jako pakiet języka Python. W systemie Linux, pliku wykonywalnego wiersza polecenia znajduje się w `/opt/LightGBM/lightgbm`pakietu języka R jest zainstalowany i zainstalowanych pakietów języka Python.     |
| Łącza do przykładów      | [Przewodnik LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Pokrewnych narzędzi      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma AI Open Source, która obsługuje funkcję uczenia maszynowego w pamięci, rozproszonej, szybkiej i skalowalnej.  |
| Obsługiwane wersje      | Linux   |
| Typowe zastosowania      | Rozdystrybuowane ogólnego przeznaczenia, skalowalne Uczenie maszynowe   |
| Jak jest ona skonfigurowana lub zainstalowane?      | H2O jest zainstalowany w `/dsvm/tools/h2o`.      |
| Jak za pomocą lub uruchomić go      | Łączenie z maszyną Wirtualną za pomocą X2Go. Uruchom nowy terminal i uruchom `java -jar /dsvm/tools/h2o/current/h2o.jar`. Następnie uruchom przeglądarkę internetową i połącz się z `http://localhost:54321`.      |
| Łącza do przykładów      | Przykłady są dostępne na maszynie Wirtualnej w programie Jupyter w obszarze `h2o` katalogu.      |
| Pokrewnych narzędzi      | Platforma Apache Spark, MXNet, XGBoost, musujące wody, wody głębokiego    |

Istnieje kilka innych bibliotek uczenia maszynowego w systemie DSVMs, takich jak popularny `scikit-learn` pakiet, który jest częścią dystrybucji języka Python Anaconda dla DSVMs. W celu zapoznania się z listy pakietów, które są dostępne w języku Python, R i Julia, uruchom menedżerów odpowiednich pakietów.
