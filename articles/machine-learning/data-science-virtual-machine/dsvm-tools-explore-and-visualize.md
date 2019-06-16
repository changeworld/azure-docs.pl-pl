---
title: Eksploracja i wizualizacja narzędzia danych — Azure | Dokumentacja firmy Microsoft
description: Eksploracja narzędzia i wizualizacji danych dla maszyny wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 693be80e493a0ba259d147f432dc9d6c07ba876d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427516"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Eksploracja i wizualizacja narzędzia danych na maszynie wirtualnej do nauki o danych

Krok klucza do nauki o danych jest zrozumieć dane. Wizualizacja i narzędzi eksploracji danych pomagają w szybszym interpretacji danych. Poniżej przedstawiono niektóre narzędzia na maszyny DSVM ułatwić ten krok klucza. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co to jest?   | Aparat kwerend SQL typu open source, danych Big Data    |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux  |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      |  Zainstalowane w `/dsvm/tools/drill*` osadzone tylko w trybie   |
| Typowe zastosowania      |  Eksplorowanie danych w miejscu bez konieczności ETL. Zapytania różnych źródeł danych i formatach, łącznie z pliku CSV, JSON, tabelach relacyjnych, Hadoop     |
| Jak używać / ją uruchomić?      | Skrót na pulpicie  <br/> [Wprowadzenie do testowania odzyskiwania po awarii w ciągu 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Pokrewne narzędzia na maszyny DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Weka jest kolekcją algorytmów uczenia maszynowego dla danych wyszukiwania zadań. Te algorytmy może być stosowane bezpośrednio do zestawu danych lub wywoływana z kodu Java. Weka zawiera narzędzia do wstępnego przetwarzania danych, klasyfikacji, regresji, klastrowanie, reguły kojarzenia i wizualizacji. |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Narzędzia ML ogólne     |
| Jak używać / ją uruchomić?      | W Windows wyszukaj Weka w Start Menu. W systemie Linux, zaloguj się przy użyciu X2Go, a następnie przejdź do aplikacji -> programowania -> Weka. |
| Zawiera linki do przykładów      | [Przykłady weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Pokrewne narzędzia na maszyny DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika do wyszukiwania danych przy użyciu języka R   |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Narzędzie do wyszukiwania danych ogólne interfejsu użytkownika dla języka R    |
| Jak używać / ją uruchomić?      | Narzędzia interfejsu użytkownika. W Windows, należy uruchomić wiersz polecenia, uruchom R, a następnie wewnątrz języka R Uruchom `rattle()`. W systemie Linux, połącz się z X2Go, uruchom terminal, uruchamiaj język R, następnie wewnątrz języka R Uruchom `rattle()`. |
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

