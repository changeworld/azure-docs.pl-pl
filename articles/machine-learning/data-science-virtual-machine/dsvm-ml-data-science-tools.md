---
title: Machine narzędzia do analizy nauki i dane — Azure | Dokumentacja firmy Microsoft
description: Narzędzia do analizy nauki i dane maszyny
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
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 9a9dc868c4f22f95ca5027e3c95513d176c69eac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392406"
---
# <a name="machine-learning-and-data-science-tools"></a>Narzędzia do analizy nauki i dane maszyny
Maszyna wirtualna do nauki o danych (DSVM) zawiera bogaty zestaw narzędzi i bibliotek do uczenia maszynowego dostępnych w popularnych języków, takich jak Python, R, Julia. 

Poniżej przedstawiono niektóre narzędzia i biblioteki na maszyny DSVM uczenia maszynowego. 

## <a name="azure-machine-learning-servicehttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) zestawu SDK
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Usługa Azure Machine Learning to usługa w chmurze, który służy do tworzenia i wdrażania modeli uczenia maszynowego.  Możesz śledzić swoje modele, podczas tworzenia, uczenia, skalowania i zarządzania nimi przy użyciu zestawu SDK języka Python. Wdrażanie modeli jako kontenery i uruchamiaj je w chmurze, lokalnego lub usługi IoT Edge.   |
| Wersje maszyny DSVM obsługiwane     | Windows (środowiska Conda: usługi Azure ml), systemu Linux (środowiska Conda: py36)    |
| Typowe zastosowania      | Platformy uczenia Maszynowego ogólnego      |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      |  Zainstalowano z obsługą procesorów GPU   |
| Jak używać / ją uruchomić?      | Zestaw SDK języka Python i narzędzia wiersza polecenia platformy Azure (AZ interfejs wiersza polecenia). Aktywuj, aby środowiska conda `AzureML` w wersji Windows lub do `py36` w wersji systemu Linux.      |
| Zawiera linki do przykładów      | Przykłady aplikacji Jupyter notebooks znajdują się w `AzureML` katalogu w ramach notesów  |
| Pokrewne narzędzia na maszyny DSVM      | Program Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co to jest?   |    Szybkie, przenośne i rozproszonych gradientu zwiększania wyniku (GBDT, GBRT lub GBM) biblioteki dla języka Python, R, Java, Scala, C++ i inne. Jest uruchamiany na jednym komputerze, Hadoop, Spark    |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Ogólne biblioteki uczenia Maszynowego      |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      |  Zainstalowano z obsługą procesorów GPU   |
| Jak używać / ją uruchomić?      | Jak biblioteka języka Python 2.7 i 3.5 pakietu języka R i narzędzia wiersza polecenia ścieżki (`C:\dsvm\tools\xgboost\bin\xgboost.exe` dla Windows, `/dsvm/tools/xgboost/xgboost` dla systemu Linux)    |
| Zawiera linki do przykładów      | Przykłady znajdują się na maszynie Wirtualnej, w `/dsvm/tools/xgboost/demo` w systemie Linux i `C:\dsvm\tools\xgboost\demo` na Windows   |
| Pokrewne narzędzia na maszyny DSVM      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Vowpal Wabbit (znany także jako "VW") jest typu open source szybko out z core uczenia biblioteka systemowa    |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Ogólne biblioteki uczenia Maszynowego      |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      |  Windows — Instalatora msi systemu Linux — polecenia apt-get |
| Jak używać / ją uruchomić?      | Narzędzie wiersza polecenia na ścieżce (`C:\Program Files\VowpalWabbit\vw.exe` na Windows, `/usr/bin/vw` w systemie Linux)    |
| Zawiera linki do przykładów      | [Przykłady VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Pokrewne narzędzia na maszyny DSVM      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Weka jest kolekcją algorytmów uczenia maszynowego dla danych wyszukiwania zadań. Te algorytmy może być stosowane bezpośrednio do zestawu danych lub wywoływana z kodu Java. Weka zawiera narzędzia do wstępnego przetwarzania danych, klasyfikacji, regresji, klastrowanie, reguły kojarzenia i wizualizacji. |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Narzędzia ML ogólne     |
| Jak używać / ją uruchomić?      | W Windows wyszukaj Weka w Start Menu. W systemie Linux, zaloguj się przy użyciu X2Go, a następnie przejdź do aplikacji -> programowania -> Weka. |
| Zawiera linki do przykładów      | [Przykłady weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Pokrewne narzędzia na maszyny DSVM      |XGBooost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika do wyszukiwania danych przy użyciu języka R   |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Narzędzie do wyszukiwania danych ogólne interfejsu użytkownika dla języka R    |
| Jak używać / ją uruchomić?      | Narzędzia interfejsu użytkownika. W Windows, należy uruchomić wiersz polecenia, uruchom R, a następnie wewnątrz języka R Uruchom `rattle()`. W systemie Linux, połącz się z X2Go, uruchom terminal, uruchamiaj język R, następnie wewnątrz języka R Uruchom `rattle()`. |
| Zawiera linki do przykładów      | [Rattle](https://togaware.com/onepager/) |
| Pokrewne narzędzia na maszyny DSVM      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Co to jest?   | Gradientem, szybka i rozproszonej o wysokiej wydajności, ulepszanie framework (GBDT, GBRT, GBM lub SKŁADNICY) oparte na algorytmy drzewa decyzyjnego, używane do klasyfikacji, klasyfikacji i wiele innych zadań uczenia maszynowego.    |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux    |
| Typowe zastosowania      | Ogólnego przeznaczenia framework zwiększenie gradientu      |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      | W Windows LightGBM jest instalowany jako pakiet języka Python. W systemie Linux, pliku wykonywalnego wiersza polecenia znajduje się w `/opt/LightGBM/lightgbm`pakietu języka R jest zainstalowany i zainstalowanych pakietów języka Python.     |
| Zawiera linki do przykładów      | [Przewodnik LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Pokrewne narzędzia na maszyny DSVM      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Co to jest?   | Platforma sztucznej Inteligencji typu open-source obsługujące uczenia maszynowego w pamięci, rozproszonej, szybka i skalowalna  |
| Wersje maszyny DSVM obsługiwane      | Linux   |
| Typowe zastosowania      | Ogólnego przeznaczenia rozproszone, skalowalne ML   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      | H2O jest zainstalowany w `/dsvm/tools/h2o`.      |
| Jak używać / ją uruchomić?      | Łączenie z maszyną wirtualną przy użyciu X2Go. Uruchom nowy terminal i uruchom `java -jar /dsvm/tools/h2o/current/h2o.jar`. Następnie uruchom przeglądarkę internetową i połącz się z `http://localhost:54321`.      |
| Zawiera linki do przykładów      | Przykłady są dostępne na maszynie Wirtualnej w programie Jupyter w obszarze `h2o` katalogu.      |
| Pokrewne narzędzia na maszyny DSVM      | Platforma Apache Spark, MXNet, XGBoost, musujące wody, wody głębokiego    |

Na nauki, takich jak popularnej ma kilka innych bibliotek ML `scikit-learn` pakiet, który jest dostarczany jako część dystrybucji Anaconda Python, która jest zainstalowana na maszyny DSVM. Należy koniecznie zapoznaj się z listy pakietów, które są dostępne w języku Python, R i Julia, uruchamiając menedżerów odpowiednich pakietów. 
