---
title: "Jak skonfigurować usługi Azure Machine Learning Workbench do pracy z IDE?  | Microsoft Docs"
description: "Przewodnik konfigurowania usługi Azure Machine Learning Workbench pracę ze środowiskiem IDE."
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 8a4bb8d825f89b08e9b7514d67ae2d2512cceec9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Konfigurowanie usługi Azure Machine Learning Workbench do pracy z IDE 

Azure Machine Learning Workbench można skonfigurować do pracy z popularnych IDEs języka Python (zintegrowane środowisko programistyczne). Umożliwia sprawne dane środowisko programistyczne nauki przechodzenia między Przygotowanie danych, tworzenia kodu, uruchom śledzenie i operationalization. Obecnie są obsługiwane IDEs:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Konfigurowanie środowiska roboczego
1. Polecenie **pliku** menu w górnym lewym rogu aplikacji. 
2. Wybierz **Konfigurowanie projektu IDE** opcję z funkcją wysuwania 
3. Wpisz w `VS Code` lub `PyCharm` w **nazwa** pola (nazwa to dowolne)
4. Wprowadź lokalizację do środowiska IDE pliku wykonywalnego (wraz z nazwą pliku wykonywalnego i rozszerzenie) w **ścieżka wykonywania**

### <a name="default-install-path-for-visual-studio-code"></a>Domyślnej ścieżki instalacji programu Visual Studio Code  

* 32-bitowego systemu Windows `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* 64-bitowego systemu Windows `C:\Program Files\Microsoft VS Code\Code.exe`
* System macOS — na przykład wybierz ścieżkę .app `/Applications/Visual Studio Code.app`, i aplikacja dołącza pozostałą część ścieżki dla Ciebie. Pełna ścieżka do pliku wykonywalnego, domyślnie jest `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Jeśli zostały wykonane `Shell Command: Install 'code' command in PATH` poleceń w kodzie VS, a następnie można także odwoływać skrypt kodzie VS `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Domyślna ścieżka instalacji dla PyCharm 

* 32-bitowego systemu Windows `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64-bit — `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* System macOS — wybierz ścieżkę .app, na przykład "/ Applications/PyCharm CE.app" i aplikacji dołącza pozostałą część ścieżki dla Ciebie. Pełna ścieżka do pliku wykonywalnego, domyślnie jest `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Można również znaleźć PyCharm na bin folder `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Otwórz projekt w środowisku IDE 
Po zakończeniu konfiguracji można otworzyć projektu usługi Azure Machine Learning, otwierając **pliku** menu w konsoli usługi Azure Machine Learning Workbench, następnie kliknij przycisk **Otwórz projekt (< IDE_Name >)**. Ta akcja powoduje otwarcie bieżącego aktywnego projektu w skonfigurowanym IDE. _Uwaga: Jeśli nie znajdujesz się w projekcie, **Otwórz projekt (< IDE_Name >)** zostanie wyłączone._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Konfigurowanie terminal zintegrowane w programie Visual Studio Code

### <a name="windows"></a>Windows 
Firma Microsoft ma zastąpić domyślną powłokę za cmd zamiast programu PowerShell. Po kliknięciu przycisku na **Otwórz projekt (< IDE_Name >)**, zostanie wyświetlony monit: 

_Musisz zezwolić powłoki: `C:\windows\System32\cmd.exe` (zdefiniowany jako ustawienia obszaru roboczego) ma być uruchamiana w terminalu?_

Odpowiedzi `yes` umożliwia konfigurowanie powłoki współpracuje z interfejsu wiersza polecenia Azure ML Workbench.

### <a name="mac"></a>Mac
Aby uruchomić `az` polecenia przy użyciu w programie Visual Studio Code zintegrowane terminali dla komputerów Mac, należy ręcznie ustawić `PATH` się taką samą wartość jak `PATH` w projekcie `.vscode/settings.json` pliku w kluczu `terminal.integrated.env.osx`. Można to zrobić, uruchamiając następujące polecenie z poziomu terminala: `PATH=<PATH in .vscode/settings>`
