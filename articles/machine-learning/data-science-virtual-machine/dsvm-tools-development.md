---
title: Narzędzia programistyczne
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej na temat narzędzi i zintegrowanych środowisk programistycznych dostępnych na Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 6ff4d92cb3730716c532332bf426132fcbb8e122
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191950"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Narzędzia programistyczne na platformie Azure Data Science Virtual Machine

Data Science Virtual Machine (DSVM) udostępnia kilka popularnych narzędzi w wysoce wydajnym zintegrowanym środowisku programistycznym (IDE). Poniżej przedstawiono niektóre narzędzia, które znajdują się maszyny DSVM.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Programowanie oprogramowania    |
| Jak została skonfigurowana i zainstalowana na DSVM?      | Obciążenie analizy danych (Narzędzia języka Python i R), obciążenie platformy Azure (Hadoop, Data Lake), Node.js, narzędzia programu SQL Server [usługi Azure Machine Learning dla programu Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak używać i uruchamiać      | Skrót na pulpicie`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`()    |
| Narzędzia pokrewne na DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Edytor kodu i integrację z usługą Git   |
| Jak używać i uruchamiać      | Skrót na pulpicie`C:\Program Files (x86)\Microsoft VS Code\Code.exe`() w systemie Windows, skrót do pulpitu`code`lub terminal () na platformie Linux    |
| Narzędzia pokrewne na DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE klienta dla języka R   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać i uruchamiać      | Skrót na pulpicie`C:\Program Files\RStudio\bin\rstudio.exe`() w systemie Windows, skrót`/usr/bin/rstudio`na pulpicie () na komputerze z systemem Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>Programu RStudio Server 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE klienta dla języka R   |
| Co to jest?   | Środowisko IDE oparte na sieci Web dla języka R    |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać i uruchamiać      | Włącz usługę z _systemctl Enable RStudio-Server_, a następnie uruchom usługę przy użyciu _systemctl Start RStudio-Server_. Następnie zaloguj się do serwera RStudio przy użyciu protokołu\/http:/Your-VM-IP: 8787.       |
| Narzędzia pokrewne na DSVM      |   Program Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient środowiska IDE języka Julia   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Julia rozwoju     |
| Jak używać i uruchamiać      | Skrót na pulpicie`C:\JuliaPro-0.5.1.1\Juno.bat`() w systemie Windows, skrót`/opt/JuliaPro-VERSION/Juno`na pulpicie () na komputerze z systemem Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Platformy Pycharm

|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient środowisko IDE dla języka Python    |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      |  Programowanie w języku Python     |
| Jak używać i uruchamiać      | Skrót na pulpicie`/usr/bin/pycharm`() w systemie Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Interaktywna wizualizacja danych i Narzędzie analizy biznesowej    |
| Obsługiwane wersje DSVM      | Windows  |
| Typowe zastosowania      |  Wizualizacja danych i tworzenie pulpitów nawigacyjnych   |
| Jak używać i uruchamiać      | Skrót na pulpicie`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Narzędzia pokrewne na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

