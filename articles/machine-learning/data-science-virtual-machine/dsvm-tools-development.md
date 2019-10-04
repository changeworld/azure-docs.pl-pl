---
title: Narzędzia programistyczne
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej na temat narzędzi i zintegrowanych środowisk programistycznych dostępnych na Data Science Virtual Machine.
keywords: narzędzia do nauki dotyczące danych, maszyna wirtualna do nauki o danych, narzędzia do nauki o danych, nauka danych systemu Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 62eb5f72d4b4395602b2665c0d1b3da4f6bb459b
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950199"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Narzędzia programistyczne na platformie Azure Data Science Virtual Machine

Data Science Virtual Machine (DSVM) udostępnia kilka popularnych narzędzi w wysoce wydajnym zintegrowanym środowisku programistycznym (IDE). Oto kilka narzędzi, które są dostępne w DSVM.

## <a name="visual-studio-2017"></a>Visual Studio 2017

|    |           |
| ------------- | ------------- |
| Co to?   | Środowisko IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Programowanie oprogramowania    |
| Jak została skonfigurowana i zainstalowana na DSVM?      | Obciążenie analizy danych (narzędzia Python i R), obciążenie platformy Azure (Hadoop, Data Lake), Node. js, SQL Server Tools, [Azure Machine Learning dla Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Narzędzia pokrewne na DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co to?   | Środowisko IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Edytor kodu i integracja z usługą git   |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) w systemie Windows, skrót do pulpitu lub terminal (`code`) na komputerze z systemem Linux    |
| Narzędzia pokrewne na DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio 

|    |           |
| ------------- | ------------- |
| Co to?   | Środowisko IDE klienta dla języka R   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files\RStudio\bin\rstudio.exe`) w systemie Windows, skrót na pulpicie (`/usr/bin/rstudio`) w systemie Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>Serwer RStudio 

|    |           |
| ------------- | ------------- |
| Co to?   | Środowisko IDE klienta dla języka R   |
| Co to?   | Środowisko IDE oparte na sieci Web dla języka R    |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać i uruchamiać      | Włącz usługę z _systemctl Enable RStudio-Server_, a następnie uruchom usługę przy użyciu _systemctl Start RStudio-Server_. Następnie zaloguj się do serwera RStudio przy użyciu protokołu http: \//The-VM-IP: 8787.       |
| Narzędzia pokrewne na DSVM      |   Program Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co to?   | Środowisko IDE klienta dla języka Julia   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Programowanie Julia     |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\JuliaPro-0.5.1.1\Juno.bat`) w systemie Windows, skrót na pulpicie (`/opt/JuliaPro-VERSION/Juno`) w systemie Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Platformy PyCharm itd

|    |           |
| ------------- | ------------- |
| Co to?   | Środowisko IDE klienta dla języka Python    |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      |  Programowanie w języku Python     |
| Jak używać i uruchamiać      | Skrót na pulpicie (`/usr/bin/pycharm`) w systemie Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Co to?   | Interaktywna wizualizacja danych i Narzędzie analizy biznesowej    |
| Obsługiwane wersje DSVM      | Windows  |
| Typowe zastosowania      |  Wizualizacja danych i tworzenie pulpitów nawigacyjnych   |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

