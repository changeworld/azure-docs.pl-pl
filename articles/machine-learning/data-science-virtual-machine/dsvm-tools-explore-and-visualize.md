---
title: Narzędzia do eksploracji i wizualizacji danych
titleSuffix: Azure Data Science Virtual Machine
description: Narzędzia do eksploracji i wizualizacji danych Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330706"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Narzędzia do eksploracji i wizualizacji danych na platformie Azure Data Science Virtual Machine

W nauce danych klucz jest zrozumiały dla danych. Narzędzia do wizualizacji i eksploracji danych przyspieszają zrozumienie danych. Poniższe narzędzia, które są dostępne w Data Science Virtual Machine (DSVM), ułatwiają ten klucz.

## <a name="apache-drill"></a>Funkcja drążenia Apache
|    |           |
| ------------- | ------------- |
| Co to jest?   | Aparat zapytań SQL "open source" dla danych Big Data    |
| Obsługiwane wersje DSVM      | Windows, Linux  |
| Jak została skonfigurowana i zainstalowana na DSVM?      |  Zainstalowane w `/dsvm/tools/drill*` tylko w trybie osadzonym   |
| Typowe zastosowania      |  W przypadku eksploracji danych w miejscu bez konieczności wyodrębniania, przekształcania, ładowania (ETL). Badaj różne źródła danych i ich formaty, w tym pliki CSV, JSON, tabele relacyjne i Hadoop.     |
| Jak używać i uruchamiać      | Skrót na pulpicie  <br/> [Wprowadzenie do przechodzenia do szczegółów za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Narzędzia pokrewne na DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Co to jest?   | Interaktywna wizualizacja danych i Narzędzie analizy biznesowej    |
| Obsługiwane wersje DSVM      | Windows  |
| Typowe zastosowania      |  Wizualizacja danych i tworzenie pulpitów nawigacyjnych   |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`) lub po prostu uruchamiany z menu **Start** .      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

> [!NOTE]
> Do uzyskania dostępu do Power BI wymagane jest konto Microsoft Office 365.


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika (GUI) do wyszukiwania danych przy użyciu języka R   |
| Obsługiwane wersje DSVM     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie wyszukiwania danych interfejsu użytkownika dla języka R    |
| Jak używać i uruchamiać      | Narzędzie interfejsu użytkownika. W systemie Windows otwórz wiersz polecenia, uruchom polecenie R, a następnie w języku R, uruchom `rattle()`. W systemie Linux Połącz się z usługą X2Go, uruchom terminal, uruchom polecenie R, a następnie w obszarze R Uruchom polecenie `rattle()`. |
| Linki do przykładów      | [Rattle](https://togaware.com/onepager/) |
| Narzędzia pokrewne na DSVM      |LightGBM, Weka, Xgboost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Kolekcja algorytmów uczenia maszynowego na potrzeby zadań wyszukiwania danych. Algorytmy te można zastosować bezpośrednio do zestawu danych lub wywołać z własnego kodu Java. Weka zawiera narzędzia do przetwarzania wstępnego, klasyfikacji, regresji, zasad kojarzenia i wizualizacji danych. |
| Obsługiwane wersje DSVM     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie uczenia maszynowego     |
| Jak używać i uruchamiać      | W systemie Windows wyszukaj pozycję Weka w menu Start. W systemie Linux Zaloguj się przy użyciu X2Go, a następnie przejdź do pozycji aplikacje > Development > Weka. |
| Linki do przykładów      | [Przykłady Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Narzędzia pokrewne na DSVM      |LightGBM, Rattle, Xgboost   |




