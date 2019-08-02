---
title: Narzędzia do eksploracji i wizualizacji danych — Azure | Microsoft Docs
description: Narzędzia do eksploracji i wizualizacji danych Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 31b05ec4fa68c3d4804000caee94b62432bdaed9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557770"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Narzędzia do eksploracji i wizualizacji danych na Data Science Virtual Machine

Najważniejszym krokiem w nauce danych jest zrozumienie danych. Narzędzia do wizualizacji i eksploracji danych przyspieszają zrozumienie danych. Oto kilka narzędzi dostępnych w DSVM, które ułatwiają ten klucz. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co to jest?   | Aparat zapytań SQL Open Source dla danych Big Data    |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux  |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      |  `/dsvm/tools/drill*` Zainstalowane tylko w trybie osadzonym   |
| Typowe zastosowania      |  Eksploracja danych in situ bez konieczności używania ETL. Wykonywanie zapytań dotyczących różnych źródeł danych i formatów, w tym woluminów CSV, JSON, tabel relacyjnych, usługi Hadoop     |
| Jak używać / ją uruchomić?      | Skrót na pulpicie  <br/> [Wprowadzenie do przechodzenia do szczegółów za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Pokrewne narzędzia na maszyny DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Weka to kolekcja algorytmów uczenia maszynowego na potrzeby zadań wyszukiwania danych. Algorytmy mogą być stosowane bezpośrednio do zestawu danych lub wywoływane z własnego kodu Java. Weka zawiera narzędzia do wstępnego przetwarzania danych, klasyfikacji, regresji, klastrowanie, reguły kojarzenia i wizualizacji. |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Narzędzie ogólne ML     |
| Jak używać / ją uruchomić?      | W systemie Windows wyszukaj pozycję Weka w menu Start. W systemie Linux Zaloguj się przy użyciu X2Go, a następnie przejdź do aplikacji — > Development — > Weka. |
| Zawiera linki do przykładów      | [Przykłady weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Pokrewne narzędzia na maszyny DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika dla wyszukiwania danych przy użyciu języka R   |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie wyszukiwania danych interfejsu użytkownika dla języka R    |
| Jak używać / ją uruchomić?      | Narzędzia interfejsu użytkownika. W systemie Windows Uruchom wiersz polecenia, uruchom polecenie R, a następnie wewnątrz uruchomienia `rattle()`języka r. W systemie Linux Połącz się z usługą X2Go, uruchom terminal, uruchom polecenie R, a następnie `rattle()`wewnątrz uruchomienia języka r. |
| Zawiera linki do przykładów      | [Rattle](https://togaware.com/onepager/) |
| Pokrewne narzędzia na maszyny DSVM      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Co to jest?   | Interaktywna Wizualizacja danych i narzędzia do analizy Biznesowej    |
| Wersje maszyny DSVM obsługiwane      | Windows  |
| Typowe zastosowania      |  Wizualizacja danych i tworzenie pulpitów nawigacyjnych   |
| Jak używać / ją uruchomić?      | Skrót na pulpicie (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Pokrewne narzędzia na maszyny DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

