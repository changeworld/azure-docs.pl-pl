---
title: Uruchamianie skryptu U-SQL i debugowania lokalnie w Azure Data Lake Tools dla Visual Studio Code
description: Dowiedz się, jak używać narzędzi Azure Data Lake Tools dla Visual Studio Code by przeprowadzić debugowanie zadań U-SQL lokalnie.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 850b13becb2137c9e881b2d6a657bbd06216e96e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317150"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Uruchamianie skryptu U-SQL i lokalnie debugowania w programie Visual Studio Code
W tym artykule opisano sposób uruchamiania zadań U-SQL na maszynie lokalnej, aby przyspieszyć we wczesnej fazie kodowania lub Aby debugować kod lokalnie w programie Visual Studio Code. Aby uzyskać instrukcje dotyczące usługi Azure Data Lake narzędzia dla programu Visual Studio Code, zobacz [narzędzi użycia usługi Azure Data Lake Tools dla Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md). 

## <a name="set-up-the-u-sql-local-run-environment"></a>Konfigurowanie środowiska lokalnego uruchamiania skryptu U-SQL

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć palety polecenia, a następnie wprowadź **ADL: Pobierz pakiet lokalny Uruchom** pobierania pakietów.  

   ![Pobieranie pakietów ADL LocalRun zależności](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Zlokalizuj pakiety zależności ze ścieżki pokazano **dane wyjściowe** okienku, a następnie zainstaluj BuildTools i Win10SDK 10240. Oto przykład ścieżki:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Zlokalizuj pakiety zależności](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 do zainstalowania **BuildTools**, kliknij przycisk visualcppbuildtools_full.exe w folderze LocalRunDependency, a następnie postępuj zgodnie z instrukcjami kreatora.   

    ![Zainstaluj BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 do zainstalowania **Win10SDK 10240**, kliknij przycisk sdksetup.exe w folderze LocalRunDependency/Win10SDK_10.0.10240_2, a następnie postępuj zgodnie z instrukcjami kreatora.  

    ![Zainstaluj Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Ustawienie zmiennej środowiskowej. Ustaw **SCOPE_CPP_SDK** zmiennej środowiskowej:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Uruchom usługę uruchamiania lokalnego i przesłać zadanie U-SQL do lokalnego konta 
Aby użytkownik po raz pierwszy, należy użyć **ADL: Pobierz pakiet lokalny Uruchom** pobierania lokalnego uruchamiania pakietów, jeśli nie masz [Konfigurowanie lokalnego środowiska uruchamiania skryptu U-SQL](#set-up-the-u-sql-local-run-environment).

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć palety polecenia, a następnie wprowadź **ADL: Uruchom usługę lokalnego uruchamiania**.   
2. Wybierz **Akceptuj** aby zaakceptować postanowienia licencyjne dotyczące oprogramowania firmy Microsoft po raz pierwszy. 

   ![Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Zostanie otwarta konsola cmd. Dla użytkowników po raz pierwszy, musisz wprowadzić **3**, a następnie zlokalizuj ścieżkę do folderu lokalnego dla danych wejściowych i wyjściowych. Inne opcje można użyć wartości domyślnych. 

   ![Narzędzia Data Lake Tools dla Visual Studio Code lokalnego uruchamiania cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę polecenia, wpisz **ADL: Prześlij zadanie**, a następnie wybierz **lokalnego** można przesłać zadania do konta lokalnego.

   ![Narzędzia Data Lake Tools dla Visual Studio Code wybierz lokalnego](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po przesłaniu zadania można wyświetlić szczegóły przesyłania. Zaznacz, aby wyświetlić szczegóły przesyłania **jobUrl** w **dane wyjściowe** okna. Można również wyświetlić stan zadania przesyłania z poziomu konsoli cmd. Wprowadź **7** w konsoli cmd, jeśli chcesz dowiedzieć się więcej szczegółów zadania.

   ![Data Lake Tools dla Visual Studio Code lokalnego uruchamiania wyjścia](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![narzędzi Data Lake Tools dla Visual Studio Code lokalnych cmd stan uruchomienia](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Uruchom lokalny debugowania dla zadania skryptu U-SQL  
Dla pierwszego użytkownika:

1. Użyj **ADL: Pobierz pakiet lokalny Uruchom** pobierania lokalnego uruchamiania pakietów, jeśli nie masz [Konfigurowanie lokalnego środowiska uruchamiania skryptu U-SQL](#set-up-the-u-sql-local-run-environment).
2. Zainstaluj .NET Core SDK 2.0 zgodnie z sugestią podaną w oknie komunikatu, jeśli nie jest zainstalowany.
 
  ![Przypomnienie instaluje Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instalacji C# dla programu Visual Studio Code jako widoczną w oknie komunikatu Jeśli nie jest zainstalowany. Kliknij przycisk **zainstalować** aby kontynuować, a następnie ponownie uruchom VSCode.

    ![Monitu, aby zainstalować C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Wykonaj poniższe kroki, aby przeprowadzić debugowania lokalnego:
  
1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć palety polecenia, a następnie wprowadź **ADL: Uruchom usługę lokalnego uruchamiania**. Zostanie otwarta konsola cmd. Upewnij się, że **DataRoot** jest ustawiona.
2. Ustaw punkt przerwania w Twojej C# związane z kodem.
3. Powrót do edytora skryptów, kliknij prawym przyciskiem myszy i wybierz **ADL: debugowania lokalnego**.
    
   ![Narzędzia Data Lake Tools dla Visual Studio Code wyniku lokalnego debugowania](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Kolejne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Tworzenie skryptu U-SQL, Python, R i CSharp dla usługi Azure Data Lake Analytics w VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu portalu Azure](data-lake-analytics-get-started-portal.md)
* [Użyj narzędzi Data Lake Tools dla programu Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Użyj Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
