---
title: Narzędzia programistyczne
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej o narzędziach i zintegrowanych środowiskach programistycznych dostępnych na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282685"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Narzędzia programistyczne na maszynie wirtualnej do nauki o danych platformy Azure

Maszyna wirtualna do nauki o danych (DSVM) zawiera kilka popularnych narzędzi w wysoce wydajnym zintegrowanym środowisku programistycznym (IDE). Oto kilka narzędzi, które są dostępne w dsvm.

## <a name="visual-studio-community-edition"></a>Edycja społeczności programu Visual Studio

|    |           |
| ------------- | ------------- |
| co to jest?   | IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Typowe zastosowania      | Tworzenie oprogramowania    |
| Jak jest skonfigurowany i zainstalowany na DSVM?      | Obciążenie do nauki o danych (narzędzia Python i R), obciążenie platformy Azure (Hadoop, usługa Data Lake), node.js, narzędzia programu SQL Server, [usługa Azure Machine Learning dla programu Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak go używać i uruchamiać      | Skrót pulpitu (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Graficznie otwórz program Visual Studio za pomocą ikony pulpitu lub menu **Start.** Wyszukaj programy (klawisze logo systemu Windows+S), a następnie **program Visual Studio**. W tym miejscu można tworzyć projekty w językach takich jak C#, Python, R i Node.js.   |
| Powiązane narzędzia w systemie DSVM      |     Kod programu Visual Studio, RStudio, Juno  |

> [!NOTE]
> Może pojawić się komunikat, że okres oceny wygasł. Wprowadź poświadczenia konta Microsoft. Możesz też utworzyć nowe bezpłatne konto, aby uzyskać dostęp do społeczności programu Visual Studio.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| co to jest?   | IDE ogólnego przeznaczenia      |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Edytor kodu i integracja Gita   |
| Jak go używać i uruchamiać      | Skrót pulpitu (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) w systemie`code`Windows, skrót pulpitu lub terminal ( ) w systemie Linux    |
| Powiązane narzędzia w systemie DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| co to jest?   | Ide klienta dla języka R   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Rozwój R     |
| Jak go używać i uruchamiać      | Skrót pulpitu (`C:\Program Files\RStudio\bin\rstudio.exe`) w`/usr/bin/rstudio`systemie Windows, skrót pulpitu ( ) w systemie Linux      |
| Powiązane narzędzia w systemie DSVM      |   Visual Studio, kod programu Visual Studio, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| co to jest?   | Ide klienta dla języka R   |
| co to jest?   | Internetowy IDE dla R    |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      |  Rozwój R     |
| Jak go używać i uruchamiać      | Włącz usługę z _systemctl włączyć rstudio-server_, a następnie uruchom usługę z _systemctl uruchomić rstudio-server_. Następnie zaloguj się do serwera RStudio pod adresem http:\//your-vm-ip:8787.       |
| Powiązane narzędzia w systemie DSVM      |   Visual Studio, kod programu Visual Studio, pulpit RStudio      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| co to jest?   | Ide klienta dla języka Julia   |
| Obsługiwane wersje DSVM      | Windows, Linux      |
| Typowe zastosowania      |  Rozwój Julii     |
| Jak go używać i uruchamiać      | Skrót pulpitu (`C:\JuliaPro-0.5.1.1\Juno.bat`) w`/opt/JuliaPro-VERSION/Juno`systemie Windows, skrót pulpitu ( ) w systemie Linux      |
| Powiązane narzędzia w systemie DSVM      |   Visual Studio, kod programu Visual Studio, RStudio      |

## <a name="pycharm"></a>Pycharm (pycharm)

|    |           |
| ------------- | ------------- |
| co to jest?   | Ide klienta dla języka Python    |
| Obsługiwane wersje DSVM      | Windows 2019, Linux      |
| Typowe zastosowania      |  Rozwój Pythona     |
| Jak go używać i uruchamiać      | Skrót pulpitu (`C:\Program Files\tk`) w systemie Windows. Skrót pulpitu (`/usr/bin/pycharm`) w systemie Linux      |
| Powiązane narzędzia w systemie DSVM      |   Visual Studio, kod programu Visual Studio, RStudio      |
