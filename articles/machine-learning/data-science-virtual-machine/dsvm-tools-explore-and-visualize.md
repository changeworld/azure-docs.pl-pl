---
title: Narzędzia do eksploracji i wizualizacji danych
titleSuffix: Azure Data Science Virtual Machine
description: Narzędzia do eksploracji i wizualizacji danych Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 14c34a13440d50fcf42c2207f9933dc1de014a14
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191962"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Narzędzia do eksploracji i wizualizacji danych na platformie Azure Data Science Virtual Machine

W nauce danych klucz jest zrozumiały dla danych. Narzędzia do wizualizacji i eksploracji danych przyspieszają zrozumienie danych. Poniższe narzędzia, które są dostępne w Data Science Virtual Machine (DSVM), ułatwiają ten klucz.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co to jest?   | Aparat zapytań SQL "open source" dla danych Big Data    |
| Obsługiwane wersje DSVM      | Windows, Linux  |
| Jak została skonfigurowana i zainstalowana na DSVM?      |  `/dsvm/tools/drill*` Zainstalowane tylko w trybie osadzonym   |
| Typowe zastosowania      |  W przypadku eksploracji danych w miejscu bez konieczności wyodrębniania, przekształcania, ładowania (ETL). Badaj różne źródła danych i ich formaty, w tym pliki CSV, JSON, tabele relacyjne i Hadoop.     |
| Jak używać i uruchamiać      | Skrót na pulpicie  <br/> [Wprowadzenie do przechodzenia do szczegółów za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Narzędzia pokrewne na DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co to jest?   |  Kolekcja algorytmów uczenia maszynowego na potrzeby zadań wyszukiwania danych. Algorytmy te można zastosować bezpośrednio do zestawu danych lub wywołać z własnego kodu Java. Weka zawiera narzędzia do przetwarzania wstępnego, klasyfikacji, regresji, zasad kojarzenia i wizualizacji danych. |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie uczenia maszynowego     |
| Jak używać i uruchamiać      | W Windows wyszukaj Weka, w Start menu. W systemie Linux Zaloguj się przy użyciu X2Go, a następnie przejdź do pozycji aplikacje > Development > Weka. |
| Zawiera linki do przykładów      | [Przykłady weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Narzędzia pokrewne na DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co to jest?   |   Graficzny interfejs użytkownika (GUI) do wyszukiwania danych przy użyciu języka R   |
| Wersje maszyny DSVM obsługiwane     | Windows, Linux     |
| Typowe zastosowania      | Ogólne narzędzie wyszukiwania danych interfejsu użytkownika dla języka R    |
| Jak używać i uruchamiać      | Narzędzia interfejsu użytkownika. W systemie Windows otwórz wiersz polecenia, uruchom polecenie R, a następnie w języku R, `rattle()`Uruchom polecenie. W systemie Linux Połącz się z usługą X2Go, uruchom terminal, uruchom polecenie R, a następnie w obszarze `rattle()`r Uruchom polecenie. |
| Zawiera linki do przykładów      | [Rattle](https://togaware.com/onepager/) |
| Narzędzia pokrewne na DSVM      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Co to jest?   | Interaktywna wizualizacja danych i Narzędzie analizy biznesowej    |
| Obsługiwane wersje DSVM      | Windows  |
| Typowe zastosowania      |  Wizualizacja danych i tworzenie pulpitów nawigacyjnych   |
| Jak używać i uruchamiać      | Skrót na pulpicie`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

