---
title: Zadania debugowania U-SQL — narzędzia usługi Azure Data Lake dla kodu programu Visual Studio
description: Dowiedz się, jak lokalnie uruchamiać i debugować zadania U-SQL za pomocą narzędzi usługi Azure Data Lake Tools for Visual Studio Code.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72030036"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Uruchamianie języka U-SQL i debugowanie lokalnie w programie Visual Studio Code
W tym artykule opisano sposób uruchamiania zadań U-SQL na komputerze deweloperskim lokalnym, aby przyspieszyć wczesne fazy kodowania lub debugować kod lokalnie w programie Visual Studio Code. Aby uzyskać instrukcje dotyczące narzędzia Usługi Azure Data Lake dla kodu programu Visual Studio, zobacz [Korzystanie z narzędzi usługi Azure Data Lake dla kodu programu Visual Studio.](data-lake-analytics-data-lake-tools-for-vscode.md)

Tylko instalacje systemu Windows usługi Azure Data Lake Tools for Visual Studio obsługują akcję uruchamiania U-SQL lokalnie i debugowania U-SQL lokalnie. Instalacje w systemach operacyjnych macOS i Linux nie obsługują tej funkcji.

## <a name="set-up-the-u-sql-local-run-environment"></a>Konfigurowanie lokalnego środowiska uruchamiania U-SQL

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Pobierz pakiet uruchamiania lokalnego,** aby pobrać pakiety.  

   ![Pobierz pakiety zależności ADL LocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Zlokalizuj pakiety zależności ze ścieżki pokazanej w okienku **Dane wyjściowe,** a następnie zainstaluj BuildTools i Win10SDK 10240. Oto przykładowa ścieżka:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Lokalizowanie pakietów zależności](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Aby zainstalować **polecenie BuildTools,** kliknij przycisk visualcppbuildtools_full.exe w folderze LocalRunDependency, a następnie postępuj zgodnie z instrukcjami kreatora.   

    ![Instalowanie buildtools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Aby zainstalować **program Win10SDK 10240,** kliknij przycisk sdksetup.exe w folderze LocalRunDependency/Win10SDK_10.0.10240_2, a następnie postępuj zgodnie z instrukcjami kreatora.  

    ![Instalowanie systemu Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Skonfiguruj zmienną środowiskową. Ustaw **zmienną** środowiskową SCOPE_CPP_SDK na:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Uruchom usługę uruchamiania lokalnego i prześlij zadanie U-SQL do konta lokalnego 
Dla użytkownika po raz pierwszy użyj **ADL: Pobierz pakiet uruchamiania lokalnego,** aby pobrać lokalne pakiety uruchamiania, jeśli nie [skonfigurowałeś lokalnego środowiska uruchamiania U-SQL](#set-up-the-u-sql-local-run-environment).

1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń, a następnie wprowadź **pozycję ADL: Uruchom usługę uruchamiania lokalnego**.   
2. Wybierz **opcję Zaakceptuj,** aby zaakceptować postanowienia licencyjne dotyczące oprogramowania firmy Microsoft po raz pierwszy. 

   ![Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Zostanie otwarta konsola cmd. W przypadku użytkowników po raz pierwszy należy wprowadzić **3**, a następnie zlokalizować ścieżkę folderu lokalnego dla danych wejściowych i wyjściowych. Jeśli nie uda ci się zdefiniować ścieżki za pomocą ukośników odwrotnych, spróbuj ukośników do przodu. W przypadku innych opcji można użyć wartości domyślnych.

   ![Narzędzia usługi Data Lake Tools dla lokalnego uruchomienia kodu programu Visual Studio](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń, wprowadź **ADL: Prześlij zadanie**, a następnie wybierz pozycję **Lokalny,** aby przesłać zadanie do konta lokalnego.

   ![Narzędzia usługi Data Lake Tools dla programu Visual Studio Code wybierz lokalne](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po przesłaniu zadania można wyświetlić szczegóły przesyłania. Aby wyświetlić szczegóły przesyłania, wybierz **jobUrl** w oknie **Dane wyjściowe.** Stan przesyłania zadania można również wyświetlić w konsoli cmd. Wprowadź **7** w konsoli cmd, jeśli chcesz dowiedzieć się więcej szczegółów pracy.

   ![Narzędzia usługi Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![for Visual Studio Code lokalne uruchamianie wyjściowe Data Lake Tools for Visual Studio Code local run cmd status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Uruchamianie lokalnego debugowania zadania U-SQL  
Dla użytkownika po raz pierwszy:

1. Użyj **ADL: Pobierz pakiet uruchamiania lokalnego,** aby pobrać lokalne pakiety uruchamiania, jeśli nie [skonfigurowałeś lokalnego środowiska uruchamiania U-SQL](#set-up-the-u-sql-local-run-environment).
2. Zainstaluj .NET Core SDK 2.0 zgodnie z sugestią w oknie komunikatu, jeśli nie jest zainstalowany.
 
  ![przypomnienie instaluje Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Zainstaluj C# dla programu Visual Studio Kod zgodnie z sugestią w oknie komunikatu, jeśli nie jest zainstalowany.Kliknij **przycisk Zainstaluj,** aby kontynuować, a następnie uruchom ponownie program VSCode.

    ![Przypomnienie o zainstalowaniu C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Wykonaj poniższe czynności, aby wykonać debugowanie lokalne:
  
1. Wybierz klawisze Ctrl+Shift+P, aby otworzyć paletę poleceń, a następnie wprowadź **pozycję ADL: Uruchom usługę uruchamiania lokalnego**. Zostanie otwarta konsola cmd. Upewnij się, że **dataroot** jest ustawiona.
2. Ustaw punkt przerwania w kodzie języka C#.
3. Powrót do edytora skryptów, kliknij prawym przyciskiem myszy i wybierz **polecenie ADL: Debugowanie lokalne**.
    
   ![Narzędzia usługi Data Lake Tools dla kodu programu Visual Studio wynik lokalnego debugowania](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Następne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Tworzenie języka U-SQL za pomocą języka Python, R i CSharp dla usługi Azure Data Lake Analytics w programie VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Używanie narzędzi usługi Data Lake Tools dla programu Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z katalogu usługi Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
