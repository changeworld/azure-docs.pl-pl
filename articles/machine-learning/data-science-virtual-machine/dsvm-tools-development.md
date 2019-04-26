---
title: Narzędzia programistyczne maszyna wirtualna do analizy danych — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o narzędziach i zintegrowanych środowisk projektowych, które są wstępnie zainstalowanych na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 7983169c2b1123c57a48471e3f4d9ad6f19c84dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502303"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Narzędzia programistyczne na maszynie wirtualnej do nauki o danych

Maszyna wirtualna do nauki o danych (DSVM) oferuje wydajne środowisko dla rozwoju, tworzenie pakietów kilku popularnych narzędzi i środowiska IDE. Poniżej przedstawiono niektóre narzędzia, które znajdują się maszyny DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  

|    |           |
| ------------- | ------------- |
| Co to jest?   | Ogólnego przeznaczenia IDE      |
| Wersje maszyny DSVM obsługiwane      | Windows      |
| Typowe zastosowania      | Tworzenie oprogramowania    |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?      | Obciążenie analizy danych (Narzędzia języka Python i R), obciążenie platformy Azure (Hadoop, Data Lake), Node.js, narzędzia programu SQL Server [usługi Azure Machine Learning dla programu Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak używać / ją uruchomić?      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Pokrewne narzędzia na maszyny DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Ogólnego przeznaczenia IDE      |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Typowe zastosowania      | Edytor kodu i integrację z usługą Git   |
| Jak używać / ją uruchomić?      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) w Windows, skrót na pulpicie lub terminalu (`code`) w systemie Linux    |
| Pokrewne narzędzia na maszyny DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient środowisko IDE dla języka R    |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać / ją uruchomić?      | Skrót na pulpicie (`C:\Program Files\RStudio\bin\rstudio.exe`) na Windows, skrót na pulpicie (`/usr/bin/rstudio`) w systemie Linux      |
| Pokrewne narzędzia na maszyny DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>Programu RStudio Server 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Środowisko IDE oparte na sieci Web dla języka R    |
| Wersje maszyny DSVM obsługiwane      | Linux      |
| Typowe zastosowania      |  Programowanie w języku R     |
| Jak używać / ją uruchomić?      | Aktywuj usługę za pomocą _systemctl Włączanie programu rstudio server_, następnie uruchom usługę za pomocą _systemctl start programu rstudio server_. Możesz następnie zalogować się do programu RStudio Server http:\// z-vm-ip:8787.       |
| Pokrewne narzędzia na maszyny DSVM      |   Program Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient środowiska IDE języka Julia   |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux      |
| Typowe zastosowania      |  Julia rozwoju     |
| Jak używać / ją uruchomić?      | Skrót na pulpicie (`C:\JuliaPro-0.5.1.1\Juno.bat`) na Windows, skrót na pulpicie (`/opt/JuliaPro-VERSION/Juno`) w systemie Linux      |
| Pokrewne narzędzia na maszyny DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Platformy Pycharm

|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient środowisko IDE dla języka Python    |
| Wersje maszyny DSVM obsługiwane      | Linux      |
| Typowe zastosowania      |  Programowanie w języku Python     |
| Jak używać / ją uruchomić?      | Skrót na pulpicie (`/usr/bin/pycharm`) w systemie Linux      |
| Pokrewne narzędzia na maszyny DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 

|    |           |
| ------------- | ------------- |
| Co to jest?   | Interaktywna Wizualizacja danych i narzędzia do analizy Biznesowej    |
| Wersje maszyny DSVM obsługiwane      | Windows  |
| Typowe zastosowania      |  Wizualizacja danych i tworzenie pulpitów nawigacyjnych   |
| Jak używać / ją uruchomić?      | Skrót na pulpicie (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Pokrewne narzędzia na maszyny DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

