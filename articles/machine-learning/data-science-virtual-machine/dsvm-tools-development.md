---
title: Narzędzia programistyczne
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej na temat narzędzi i zintegrowanych środowisk programistycznych dostępnych na Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283760"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Narzędzia programistyczne na platformie Azure Data Science Virtual Machine

Data Science Virtual Machine (DSVM) udostępnia kilka popularnych narzędzi w wysoce wydajnym zintegrowanym środowisku programistycznym (IDE). Poniżej przedstawiono niektóre narzędzia, które znajdują się maszyny DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows: Visual Studio 2017, Windows 2019 (wersja zapoznawcza): Visual Studio 2019      |
| Typowe zastosowania      | Programowanie oprogramowania    |
| Jak została skonfigurowana i zainstalowana na DSVM?      | Obciążenie analizy danych (narzędzia Python i R), obciążenie platformy Azure (Hadoop, Data Lake), Node. js, SQL Server Tools, [Azure Machine Learning dla Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Graficznie Otwórz program Visual Studio, korzystając z ikony pulpitu lub menu **Start** . Wyszukaj programy (logo systemu Windows + S), a następnie **program Visual Studio**. W tym miejscu możesz tworzyć projekty w językach takich jak C#, Python, R i Node. js.   |
| Narzędzia pokrewne na DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Możesz otrzymać komunikat, okres próbny wygasł. Wprowadź poświadczenia konta Microsoft. Lub Utwórz nowe bezpłatne konto, aby uzyskać dostęp do programu Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Edytor kodu i integrację z usługą Git   |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) w systemie Windows, skrót do pulpitu lub terminal (`code`) na platformie Linux    |
| Narzędzia pokrewne na DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE klienta dla języka R   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files\RStudio\bin\rstudio.exe`) w systemie Windows, skrót na pulpicie (`/usr/bin/rstudio`) w systemie Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>Serwer RStudio

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE klienta dla języka R   |
| Co to jest?   | Środowisko IDE oparte na sieci Web dla języka R    |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać i uruchamiać      | Włącz usługę z _systemctl Enable RStudio-Server_, a następnie uruchom usługę przy użyciu _systemctl Start RStudio-Server_. Następnie zaloguj się do serwera RStudio przy użyciu protokołu http:\//Your-VM-IP: 8787.       |
| Narzędzia pokrewne na DSVM      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient środowiska IDE języka Julia   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Julia rozwoju     |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\JuliaPro-0.5.1.1\Juno.bat`) w systemie Windows, skrót na pulpicie (`/opt/JuliaPro-VERSION/Juno`) w systemie Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Platformy Pycharm

|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient środowisko IDE dla języka Python    |
| Obsługiwane wersje DSVM      | Windows 2019 (wersja zapoznawcza), Linux      |
| Typowe zastosowania      |  Programowanie w języku Python     |
| Jak używać i uruchamiać      | Skrót na pulpicie (`C:\Program Files\tk`) w systemie Windows. Skrót na pulpicie (`/usr/bin/pycharm`) w systemie Linux      |
| Narzędzia pokrewne na DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
