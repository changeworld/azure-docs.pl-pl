---
title: Narzędzia nauki uczenie i danych - Azure komputera | Dokumentacja firmy Microsoft
description: Narzędzia nauki maszyny uczenie i danych
keywords: narzędzia do analizy danych, maszyny wirtualnej analizy danych, narzędzia do analizy danych, nauki danych systemu linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: f8d7fff30d5f5289c362d78ad89027b8141bbbe6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="machine-learning-and-data-science-tools"></a>Narzędzia nauki maszyny uczenie i danych
Maszyna wirtualna nauki danych (DSVM) zawiera bogaty zestaw narzędzi i bibliotek do uczenia maszynowego dostępnych w popularnych języków, takich jak Julia Python, R. 

Poniżej przedstawiono niektóre machine learning narzędzia i bibliotek w DSVM. 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co to jest?   |    Szybki, przenośny i rozproszonych gradientu zwiększania (GBDT, GBRT lub GBM) biblioteki dla języka Python, R, Java, Scala, C++ i inne. Działa na jednym komputerze, Hadoop, Spark    |
| DSVM obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne biblioteki ML      |
| Jak jest on skonfigurowany / zainstalowanym DSVM?      |  Zainstalowane z obsługą procesora GPU   |
| Jak używać / uruchom go?      | Jak biblioteka języka Python 2.7 i 3.5 pakietu języka R i na ścieżkę narzędzia wiersza polecenia (`C:\dsvm\tools\xgboost\bin\xgboost.exe` dla systemu Windows, `/dsvm/tools/xgboost/xgboost` dla systemu Linux)    |
| Łącza do próbek      | Przykłady znajdują się na Maszynie wirtualnej, w `/dsvm/tools/xgboost/demo` w systemie Linux i `C:\dsvm\tools\xgboost\demo` w systemie Windows   |
| Pokrewne narzędzia na DSVM      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Vowpal Wabbit (znanej także jako "VW") jest typu open source fast poza z — podstawowe uczenia biblioteka systemu    |
| DSVM obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne biblioteki ML      |
| Jak jest on skonfigurowany / zainstalowanym DSVM?      |  System Windows — Instalator msi, Linux — stanie get |
| Jak używać / uruchom go?      | Jako narzędzie wiersza polecenia na ścieżkę (`C:\Program Files\VowpalWabbit\vw.exe` w systemie Windows, `/usr/bin/vw` w systemie Linux)    |
| Łącza do próbek      | [Przykłady VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Pokrewne narzędzia na DSVM      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Weka jest kolekcją algorytmów uczenia maszynowego dla zadań wyszukiwania danych. Algorytmy można zastosować bezpośrednio do zestawu danych lub wywoływać w kodzie języka Java. Weka zawiera narzędzia służące do wstępnego przetwarzania danych, klasyfikacji, regresji, klaster, reguły kojarzenia i wizualizacji. |
| DSVM obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Narzędzie ML ogólne     |
| Jak używać / uruchom go?      | W systemie Windows wyszukaj Weka w Start Menu. W systemie Linux, zaloguj się za pomocą X2Go, a następnie przejdź do aplikacji -> Programowanie -> Weka. |
| Łącza do próbek      | [Przykłady weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Pokrewne narzędzia na DSVM      |XGBooost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika do wyszukiwania danych przy użyciu języka R   |
| DSVM obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne interfejsu użytkownika narzędzia wyszukiwania danych dla języka R    |
| Jak używać / uruchom go?      | Narzędzia interfejsu użytkownika. W systemie Windows, uruchom wiersz polecenia, uruchom R, a następnie wewnątrz R Uruchom `rattle()`. W systemie Linux, połączenie z X2Go, rozpoczęcie terminal, uruchom R, a następnie wewnątrz R Uruchom `rattle()`. |
| Łącza do próbek      | [Rattle](https://togaware.com/onepager/) |
| Pokrewne narzędzia na DSVM      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Co to jest?   | Gradient szybkie, rozproszonych, wysokiej wydajności zwiększania framework (GBDT GBRT, GBM albo SKŁADNICY) oparte na algorytmów drzewa decyzyjne, używanych do klasyfikacji, klasyfikacji i wiele innych machine learning zadania.    |
| DSVM obsługiwane wersje      | Windows, Linux    |
| Typowe zastosowania      | Ogólnego przeznaczenia framework zwiększania wyniku gradientu      |
| Jak jest on skonfigurowany / zainstalowanym DSVM?      | W systemie Windows LightGBM jest instalowany jako pakiet języka Python. W systemie Linux, plik wykonywalny wiersza polecenia znajduje się w `/opt/LightGBM/lightgbm`zainstalowano pakietu języka R i pakiety języka Python są zainstalowane.     |
| Łącza do próbek      | [Przewodnik LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Pokrewne narzędzia na DSVM      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Co to jest?   | Platformy AI open source do obsługi uczenia maszynowego w pamięci, rozproszonej szybka i skalowalna  |
| DSVM obsługiwane wersje      | Linux   |
| Typowe zastosowania      | Ogólnego przeznaczenia rozproszonych, skalowalna ML   |
| Jak jest on skonfigurowany / zainstalowanym DSVM?      | H2O jest zainstalowany w `/dsvm/tools/h2o`.      |
| Jak używać / uruchom go?      | Połączenie z maszyną Wirtualną za pomocą X2Go. Uruchom nowy terminal i uruchom `java -jar /dsvm/tools/h2o/current/h2o.jar`. Następnie uruchom przeglądarkę sieci web i połącz się `http://localhost:54321`.      |
| Łącza do próbek      | Przykłady są dostępne na Maszynie wirtualnej w oprogramowaniu Jupyter w obszarze `h2o` katalogu.      |
| Pokrewne narzędzia na DSVM      | Platforma Apache Spark, MXNet, XGBoost, musujące wody, głębokie limitu górnego    |

Istnieją kilka bibliotek ML na DSVM, jak popularne `scikit-learn` pakiet, który jest dostarczany jako część dystrybucję oprogramowania Python Anaconda, który jest zainstalowany na DSVM. Należy zapoznać się lista pakietów dostępne w języku Python, R i Julia uruchamiając menedżerów odpowiedniego pakietu. 
