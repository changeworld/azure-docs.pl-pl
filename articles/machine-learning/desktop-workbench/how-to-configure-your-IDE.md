---
title: Jak skonfigurować program Azure Machine Learning Workbench do pracy ze środowiskiem IDE?  | Microsoft Docs
description: Przewodnik konfigurowania usługi Azure Machine Learning Workbench do pracy z używanego środowiska IDE.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 18692fe631a7e1349ead6bc68a87934e6d030913
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649255"
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Jak skonfigurować program Azure Machine Learning Workbench do pracy ze środowiskiem IDE 

Usługa Azure Machine Learning Workbench można skonfigurować do pracy z popularnymi środowiskami IDE języka Python (zintegrowane środowisko programistyczne). Dzięki temu w środowisku projektowym do nauki o danych smooth przenoszenia między przygotowywania danych, tworzenie kodu, śledzenie wykonywania i operacjonalizacji. Obecnie są obsługiwane środowiskami IDE:
- Microsoft Visual Studio Code 
- Platformy JetBrain PyCharm 

## <a name="configure-workbench"></a>Konfigurowanie aplikacji workbench
1. Kliknij pozycję **pliku** menu w górnym lewym rogu aplikacji. 
2. Wybierz **Konfigurowanie IDE projektu** opcję z menu wysuwane 
3. Wpisz `VS Code` lub `PyCharm` w **nazwa** pole (dowolne jest nazwa)
4. Wprowadź lokalizację do IDE pliku wykonywalnego (Ukończono z nazwą pliku wykonywalnego i rozszerzenia) w **ścieżki wykonywania**

### <a name="default-install-path-for-visual-studio-code"></a>Domyślna ścieżka instalacji programu Visual Studio Code  

* 32-bitowe — Windows `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64-bitowych `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS — na przykład wybierz ścieżkę .app `/Applications/Visual Studio Code.app`, a aplikacja dołącza pozostałą część ścieżki dla Ciebie. Pełna ścieżka do pliku wykonywalnego, który domyślnie jest `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Jeśli został wykonany `Shell Command: Install 'code' command in PATH` polecenia w programie VS Code, a następnie możesz też przywołać skrypt programu VS Code `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Domyślne ścieżki instalacji dla platformy PyCharm 

* Windows 32-bitowych `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64-bit — `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS — wybierz ścieżkę .app, na przykład "/ Applications/CE.app platformy PyCharm", a aplikacja dołącza pozostałą część ścieżki dla Ciebie. Pełna ścieżka do pliku wykonywalnego, który domyślnie jest `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Możesz również stwierdzić, platformy PyCharm na bin folder `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Otwórz projekt w środowisku IDE 
Po zakończeniu konfiguracji można otworzyć projektu usługi Azure Machine Learning, otwierając **pliku** menu w aplikacji Azure Machine Learning Workbench, następnie kliknij przycisk **Otwórz projekt (< IDE_Name >)**. Ta akcja powoduje otwarcie bieżącego aktywnego projektu w IDE skonfigurowany. _Uwaga: Jeśli nie jesteś w projekcie, **Otwórz projekt (< IDE_Name >)** zostaną wyłączone._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Konfigurowanie zintegrowany terminal programu Visual Studio Code

### <a name="windows"></a>Windows 
Firma Microsoft ma zastąpić domyślnej powłoki jako cmd, zamiast programu PowerShell. Po kliknięciu przycisku na **Otwórz projekt (< IDE_Name >)**, zostanie wyświetlony monit: 

_Możesz umożliwić powłoki: `C:\windows\System32\cmd.exe` (zdefiniowane w ustawieniach obszaru roboczego) do uruchomienia w terminalu?_

Odpowiedź `yes` umożliwia konfigurowanie powłoki o bezproblemowej współpracy z interfejsu wiersza polecenia aplikacji Azure ML Workbench.

### <a name="mac"></a>Mac
Aby uruchomić `az` polecenia przy użyciu programu Visual Studio Code w zintegrowany terminal na komputerze Mac, należy ręcznie ustawić `PATH` się taką samą wartość jak `PATH` w projekcie `.vscode/settings.json` pliku w kluczu `terminal.integrated.env.osx`. Możesz to zrobić, uruchamiając następujące polecenie w terminalu: `PATH=<PATH in .vscode/settings>`
