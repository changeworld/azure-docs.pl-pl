---
title: Uczenie maszynowe i narzędzia do nauki o danych
description: Dowiedz się więcej o narzędziach i strukturach uczenia maszynowego, które są preinstalowane na Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 8eae1501973d24dc468056229e8a762910f2f181
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330758"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Uczenie maszynowe i narzędzia do nauki o danych w usłudze Azure Data Learning Virtual Machines
Usługa Azure Data Learning Virtual Machines (DSVMs) oferuje bogaty zestaw narzędzi i bibliotek do uczenia maszynowego dostępnych w popularnych językach, takich jak Python, R i Julia.

Poniżej przedstawiono niektóre narzędzia i biblioteki uczenia maszynowego w systemie DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>Zestaw SDK Azure Machine Learning dla języka Python

Zobacz pełne informacje dotyczące [zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Co to jest?   |   Azure Machine Learning to usługa w chmurze, za pomocą której można tworzyć i wdrażać modele uczenia maszynowego. Modele można śledzić w miarę kompilowania, uczenia i skalowania oraz zarządzania nimi przy użyciu zestawu SDK języka Python. Wdrażaj modele jako kontenery i uruchamiaj je w chmurze, lokalnie lub na Azure IoT Edge.   |
| Obsługiwane wersje     | Windows (środowisko Conda: Azure), Linux (Conda Environment: py36)    |
| Typowe zastosowania      | Ogólna platforma uczenia maszynowego      |
| Jak jest on skonfigurowany lub instalowany?      |  Zainstalowana z obsługą procesora GPU   |
| Jak używać lub uruchamiać      | Jako zestaw SDK języka Python i w interfejsie wiersza polecenia platformy Azure. Aktywuj środowisko Conda `AzureML` w wersji Windows *lub* `py36` w systemie Linux Edition.      |
| Link do przykładów      | Przykładowe notesy Jupyter są dołączone do katalogu `AzureML` w obszarze notesy.  |
| Narzędzia pokrewne      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma AI Open Source, która obsługuje funkcję uczenia maszynowego w pamięci, rozproszonej, szybkiej i skalowalnej.  |
| Obsługiwane wersje      | Linux   |
| Typowe zastosowania      | Rozdystrybuowane ogólnego przeznaczenia, skalowalne Uczenie maszynowe   |
| Jak jest on skonfigurowany lub instalowany?      | H2O jest zainstalowany w `/dsvm/tools/h2o`.      |
| Jak używać lub uruchamiać      | Połącz się z maszyną wirtualną za pomocą X2Go. Rozpocznij nowy terminal i uruchom `java -jar /dsvm/tools/h2o/current/h2o.jar`. Następnie uruchom przeglądarkę sieci Web i Połącz się z `http://localhost:54321`.      |
| Link do przykładów      | Przykłady są dostępne na maszynie wirtualnej w Jupyter w katalogu `h2o`.      |
| Narzędzia pokrewne      | Apache Spark, MXNet, XGBoost, wody musujące, głębokie wody    |

Istnieje kilka innych bibliotek uczenia maszynowego w systemie DSVMs, takich jak popularny pakiet `scikit-learn`, który jest częścią dystrybucji języka Python Anaconda dla DSVMs. Aby zapoznać się z listą pakietów dostępnych w języku Python, R i Julia, należy uruchomić odpowiednich menedżerów pakietów.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Co to jest?   | Szybkie, rozproszone i wysoce wydajne środowisko do zwiększania gradientu (GBDT, GBRT, GBM lub SKŁADNIe) w oparciu o algorytmy drzewa decyzyjnego. Jest on używany do klasyfikowania, klasyfikowania i wielu innych zadań uczenia maszynowego.    |
| Obsługiwane wersje      | Windows, Linux    |
| Typowe zastosowania      | Środowisko zwiększania gradientu ogólnego przeznaczenia      |
| Jak jest on skonfigurowany lub instalowany?      | W systemie Windows LightGBM jest instalowany jako pakiet języka Python. W systemie Linux plik wykonywalny wiersza polecenia znajduje się w `/opt/LightGBM/lightgbm`, pakiet języka R jest zainstalowany, a pakiety w języku Python są zainstalowane.     |
| Link do przykładów      | [Przewodnik LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Narzędzia pokrewne      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika służący do wyszukiwania danych przy użyciu języka R.   |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie wyszukiwania danych interfejsu użytkownika dla języka R    |
| Jak używać lub uruchamiać      | Jako narzędzie interfejsu użytkownika. W systemie Windows, Uruchom wiersz polecenia, uruchom polecenie R, a następnie w języku R, uruchom `rattle()`. W systemie Linux Połącz się z usługą X2Go, uruchom terminal, uruchom polecenie R, a następnie w obszarze R Uruchom polecenie `rattle()`. |
| Link do przykładów      | [Rattle](https://togaware.com/onepager/) |
| Narzędzia pokrewne      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Szybka, niepodstawowa Biblioteka systemu szkoleniowego typu "open source"    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólna Biblioteka uczenia maszynowego      |
| Jak jest on skonfigurowany lub instalowany?      |  Windows: instalator MSI<br/>Linux: apt — Pobierz |
| Jak używać lub uruchamiać      | Jako narzędzie wiersza polecenia na ścieżce (`C:\Program Files\VowpalWabbit\vw.exe` w systemie Windows, `/usr/bin/vw` w systemie Linux)    |
| Link do przykładów      | [Przykłady VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Narzędzia pokrewne      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Kolekcja algorytmów uczenia maszynowego na potrzeby zadań wyszukiwania danych. Algorytmy mogą być stosowane bezpośrednio do zestawu danych lub wywoływane z własnego kodu Java. Weka zawiera narzędzia do wstępnego przetwarzania danych, klasyfikacji, regresji, klastrowania, reguł kojarzenia i wizualizacji. |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie uczenia maszynowego     |
| Jak używać lub uruchamiać      | W systemie Windows wyszukaj pozycję Weka w menu **Start** . W systemie Linux Zaloguj się przy użyciu X2Go, a następnie przejdź do pozycji **aplikacje** > **programowanie** > **Weka**. |
| Link do przykładów      | [Przykłady Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Narzędzia pokrewne      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Szybka, przenośna i rozproszona Biblioteka do zwiększania gradientu (GBDT, GBRT lub GBM) dla języków Python, R, Java, Scala C++, i innych. Działa na jednej maszynie oraz na Apache Hadoop i Spark.    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólna Biblioteka uczenia maszynowego      |
| Jak jest on skonfigurowany lub instalowany?      |  Zainstalowana z obsługą procesora GPU   |
| Jak używać lub uruchamiać      | Jako biblioteka Python (2,7 i 3,5), pakiet języka R i narzędzie wiersza polecenia on-Path (`C:\dsvm\tools\xgboost\bin\xgboost.exe` dla systemu Windows i `/dsvm/tools/xgboost/xgboost` dla Linux)    |
| Linki do przykładów      | Przykłady znajdują się na maszynie wirtualnej, w `/dsvm/tools/xgboost/demo` w systemie Linux i `C:\dsvm\tools\xgboost\demo` w systemie Windows.   |
| Narzędzia pokrewne      | LightGBM, MXNet   |


