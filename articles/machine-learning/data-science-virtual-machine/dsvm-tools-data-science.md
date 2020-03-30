---
title: Narzędzia do uczenia maszynowego i nauki o danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej o narzędziach uczenia maszynowego i platformach, które są preinstalowane na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282311"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Narzędzia do uczenia maszynowego i nauki o danych na maszynach wirtualnych do nauki o danych platformy Azure
Maszyny wirtualne do nauki o danych platformy Azure (DSVMs) mają bogaty zestaw narzędzi i bibliotek do uczenia maszynowego dostępnych w popularnych językach, takich jak Python, R i Julia.

Oto niektóre z narzędzi uczenia maszynowego i bibliotek na dsvms.

## <a name="azure-machine-learning-sdk-for-python"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python

Zobacz pełne odwołanie do [sdk usługi Azure Machine Learning dla języka Python.](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)

|    |           |
| ------------- | ------------- |
| co to jest?   |   Usługa Azure Machine Learning to usługa w chmurze, której można używać do tworzenia i wdrażania modeli uczenia maszynowego. Można śledzić modele podczas tworzenia, trenowania, skalowania i zarządzania nimi przy użyciu zestawie SDK języka Python. Wdrażaj modele jako kontenery i uruchamiaj je w chmurze, lokalnie lub w usłudze Azure IoT Edge.   |
| Obsługiwane wersje     | Windows (środowisko conda: AzureML), Linux (środowisko conda: py36)    |
| Typowe zastosowania      | Ogólna platforma uczenia maszynowego      |
| Jak jest skonfigurowany lub zainstalowany?      |  Zainstalowany z obsługą GPU   |
| Jak go używać lub uruchamiać      | Jako zestaw SDK języka Python i w interfejsie wiersza polecenia platformy Azure. Aktywuj do środowiska `AzureML` conda w wersji Windows *lub* w `py36` wersji Linux.      |
| Łącze do próbek      | Przykładowe notesy Jupyter są `AzureML` zawarte w katalogu w notesach.  |
| Powiązane narzędzia      | Kod programu Visual Studio, Jupyter   |

## <a name="h2o"></a>H2o

|    |           |
| ------------- | ------------- |
| co to jest?   | Platforma AI typu open source, która obsługuje uczenie maszynowe w pamięci, rozproszone, szybkie i skalowalne.  |
| Obsługiwane wersje      | Linux   |
| Typowe zastosowania      | Rozproszone, skalowalne uczenie maszynowe ogólnego przeznaczenia   |
| Jak jest skonfigurowany lub zainstalowany?      | H2O jest `/dsvm/tools/h2o`zainstalowany w .      |
| Jak go używać lub uruchamiać      | Połącz się z maszyną wirtualną za pomocą X2Go. Uruchom nowy terminal i `java -jar /dsvm/tools/h2o/current/h2o.jar`uruchom program . Następnie uruchom przeglądarkę internetową `http://localhost:54321`i połącz się z plikiem .      |
| Łącze do próbek      | Przykłady są dostępne na maszynie Wirtualnej `h2o` w Jupyter w katalogu.      |
| Powiązane narzędzia      | Apache Spark, MXNet, XGBoost, Woda gazowana, Głęboka woda    |

Istnieje kilka innych bibliotek uczenia maszynowego na dsvms, takich jak popularny `scikit-learn` pakiet, który jest częścią dystrybucji Języka Python Anaconda dla dsvms. Aby sprawdzić listę pakietów dostępnych w Pythonie, R i Julii, uruchom odpowiednie menedżery pakietów.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| co to jest?   | Szybka, rozproszona, wysokowydajna struktura zwiększania gradientu (GBDT, GBRT, GBM lub MART) oparta na algorytmach drzewa decyzyjnego. Jest on używany do rankingu, klasyfikacji i wielu innych zadań uczenia maszynowego.    |
| Obsługiwane wersje      | Windows, Linux    |
| Typowe zastosowania      | Struktura zwiększania gradientów ogólnego przeznaczenia      |
| Jak jest skonfigurowany lub zainstalowany?      | W systemie Windows LightGBM jest zainstalowany jako pakiet Pythona. W systemie Linux plik wykonywalny wiersza polecenia znajduje się w `/opt/LightGBM/lightgbm`programie , zainstalowany jest pakiet R i zainstalowane pakiety Pythona.     |
| Łącze do próbek      | [Przewodnik lightgbm](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Powiązane narzędzia      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| co to jest?   |   Graficzny interfejs użytkownika do wyszukiwania danych przy użyciu języka R.   |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie do wyszukiwania danych interfejsu użytkownika dla R    |
| Jak go używać lub uruchamiać      | Jako narzędzie interfejsu użytkownika. W systemie Windows uruchom wiersz polecenia, uruchom R, `rattle()`a następnie wewnątrz R uruchom polecenie . Na Linuksie, połącz się z X2Go, uruchom `rattle()`terminal, uruchom R, a następnie wewnątrz R, uruchom . |
| Łącze do próbek      | [Rattle](https://togaware.com/onepager/) |
| Powiązane narzędzia      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| co to jest?   |   Szybka, otwarta biblioteka systemów uczenia się poza rdzeniem    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólna biblioteka uczenia maszynowego      |
| Jak jest skonfigurowany lub zainstalowany?      |  Windows: instalator msi<br/>Linux: apt-get |
| Jak go używać lub uruchamiać      | Jako narzędzie wiersza polecenia na`C:\Program Files\VowpalWabbit\vw.exe` ścieżce `/usr/bin/vw` (w systemie Windows, w systemie Linux)    |
| Łącze do próbek      | [Próbki VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Powiązane narzędzia      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| co to jest?   |  Kolekcja algorytmów uczenia maszynowego dla zadań wyszukiwania danych. Algorytmy mogą być stosowane bezpośrednio do zestawu danych lub wywoływane z własnego kodu Java. Weka zawiera narzędzia do wstępnego przetwarzania danych, klasyfikacji, regresji, klastrowania, reguł skojarzenia i wizualizacji. |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie do uczenia maszynowego     |
| Jak go używać lub uruchamiać      | W systemie Windows wyszukaj wekę w menu **Start.** W systemie Linux zaloguj się za pomocą X2Go, a następnie przejdź do **Aplikacji** > **Rozwoju** > **Weka**. |
| Łącze do próbek      | [Próbki Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Powiązane narzędzia      |LightGBM, Grzechotka, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| co to jest?   |   Szybka, przenośna i rozproszona biblioteka zwiększania gradientu (GBDT, GBRT lub GBM) dla języków Python, R, Java, Scala, C++ i innych. Działa na jednej maszynie, a na Apache Hadoop i Spark.    |
| Obsługiwane wersje     | Windows, Linux     |
| Typowe zastosowania      | Ogólna biblioteka uczenia maszynowego      |
| Jak jest skonfigurowany lub zainstalowany?      |  Zainstalowany z obsługą GPU   |
| Jak go używać lub uruchamiać      | Jako biblioteka Języka Python (2.7 i 3.5), pakiet R`C:\dsvm\tools\xgboost\bin\xgboost.exe` i narzędzie `/dsvm/tools/xgboost/xgboost` wiersza polecenia na ścieżce (dla windowsa i linuksa)    |
| Odnośniki do próbek      | Przykłady są zawarte na maszynie `/dsvm/tools/xgboost/demo` Wirtualnej, `C:\dsvm\tools\xgboost\demo` w systemie Linux i Windows.   |
| Powiązane narzędzia      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| co to jest?   | Aparat zapytań SQL typu open source dotyczący dużych zbiorów danych    |
| Obsługiwane wersje DSVM      | Windows 2019, Linux  |
| Jak jest skonfigurowany i zainstalowany na DSVM?      |  Zainstalowany `/dsvm/tools/drill*` tylko w trybie wbudowanym   |
| Typowe zastosowania      |  W przypadku eksploracji danych w miejscu bez konieczności wyodrębniania, przekształcania, ładowania (ETL). Wysyłaj zapytania do różnych źródeł danych i formatów, w tym CSV, JSON, tabel relacyjnych i Hadoop.     |
| Jak go używać i uruchamiać      | Skrót pulpitu  <br/> [Zacznij ciemiężyć w 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Powiązane narzędzia w systemie DSVM      |   Grzechotka, Weka, SQL Server Management Studio      |


