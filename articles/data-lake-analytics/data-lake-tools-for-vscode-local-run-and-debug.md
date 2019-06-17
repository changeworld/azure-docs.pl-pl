---
title: Uruchomienie języka U-SQL i Debuguj lokalnie w usłudze Azure Data Lake Tools for Visual Studio Code
description: Dowiedz się, jak uruchamianie i debugowanie zadań U-SQL lokalnie za pomocą usługi Azure Data Lake Tools for Visual Studio Code.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 765bcaab0f91e097be827bfa6e8f505ef5330d57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814396"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Uruchomienie języka U-SQL i Debuguj lokalnie w programie Visual Studio Code
W tym artykule opisano sposób uruchamiania zadań U-SQL na lokalnej maszynie do programowania w celu przyspieszenia wczesnych faz kodowania lub do debugowania kodu lokalnie w programie Visual Studio Code. Aby uzyskać instrukcje dotyczące usługi Azure Data Lake narzędzia dla programu Visual Studio Code, zobacz [użycia Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Tylko Windows urządzenia Azure Data Lake Tools for Visual Studio obsługuje akcji lokalne uruchomienie języka U-SQL i Debuguj lokalnie U-SQL. Instalacje w systemach macOS i systemami operacyjnymi opartymi na systemie Linux nie obsługują tej funkcji.

## <a name="set-up-the-u-sql-local-run-environment"></a>Skonfiguruj lokalne środowisko uruchomieniowe języka U-SQL

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Pobierz pakiet lokalny Uruchom** pobierania pakietów.  

   ![Pobierz pakiety zależności LocalRun usługi ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Zlokalizuj pakiety zależności z ścieżkę wyświetloną w **dane wyjściowe** okienka, a następnie zainstaluj BuildTools i Win10SDK 10240. Oto przykładowa ścieżka:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Zlokalizuj pakiety zależności](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 można zainstalować **BuildTools**kliknij visualcppbuildtools_full.exe w folderze LocalRunDependency, a następnie postępuj zgodnie z instrukcjami kreatora.   

    ![Zainstaluj BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 zainstalował **Win10SDK 10240**kliknij sdksetup.exe w folderze LocalRunDependency/Win10SDK_10.0.10240_2, a następnie postępuj zgodnie z instrukcjami kreatora.  

    ![Zainstaluj Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Ustawienie zmiennej środowiskowej. Ustaw **SCOPE_CPP_SDK** zmiennej środowiskowej, aby:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Uruchom usługę uruchamiania lokalnego i przesyłać zadania U-SQL do konta lokalnego 
Aby użytkownik po raz pierwszy, użyj **ADL: Pobierz pakiet lokalny Uruchom** do pobierania lokalnego uruchamiania pakietów, jeśli nie masz [skonfigurować lokalne środowisko uruchomieniowe języka U-SQL](#set-up-the-u-sql-local-run-environment).

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Uruchom usługę uruchamiania lokalnego**.   
2. Wybierz **Akceptuj** zaakceptować postanowienia licencyjne dotyczące oprogramowania firmy Microsoft po raz pierwszy. 

   ![Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Zostanie otwarta konsola cmd. Dla użytkowników po raz pierwszy, musisz wprowadzić **3**, a następnie zlokalizuj ścieżka folderu lokalnego dla danych wejściowych i danych wyjściowych. Inne opcje można użyć wartości domyślnych. 

   ![Narzędzia Data Lake Tools dla programu Visual Studio Code lokalnego uruchamiania polecenia](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, wpisz **ADL: Prześlij zadanie**, a następnie wybierz pozycję **lokalnego** można przesłać zadania do konta lokalnego.

   ![Narzędzia Data Lake Tools for Visual Studio Code wybierz lokalnych](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po przesłaniu zadania możesz wyświetlić szczegóły przesyłania. Zaznacz, aby wyświetlić szczegóły przesyłania **jobUrl** w **dane wyjściowe** okna. Można również wyświetlić stan przesyłania zadania z poziomu polecenia konsoli. Wprowadź **7** w konsoli cmd, jeśli chcesz wiedzieć więcej szczegółów zadania.

   ![Data Lake Tools for Visual Studio Code lokalne dane wyjściowe przebiegu](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![narzędzi Data Lake Tools dla programu Visual Studio Code lokalnego cmd stan uruchomienia](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Rozpocznij debugowania lokalnego dla zadania U-SQL  
Dla użytkownika po raz pierwszy:

1. Use **ADL: Pobierz pakiet lokalny Uruchom** do pobierania lokalnego uruchamiania pakietów, jeśli nie masz [skonfigurować lokalne środowisko uruchomieniowe języka U-SQL](#set-up-the-u-sql-local-run-environment).
2. Zainstaluj .NET Core SDK 2.0 zgodnie z sugestią podaną w oknie komunikatu, jeśli nie jest zainstalowany.
 
  ![Przypomnienie o zainstalowaniu Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Zainstaluj C# programu Visual Studio Code zgodnie z sugestią podaną w oknie komunikatu, jeśli nie jest zainstalowany. Kliknij przycisk **zainstalować** aby kontynuować, a następnie ponownie uruchom VSCode.

    ![Przypomnienie o zainstalowaniu języka C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Wykonaj poniższe kroki, aby wykonać debugowania lokalnego:
  
1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Uruchom usługę uruchamiania lokalnego**. Zostanie otwarta konsola cmd. Upewnij się, że **DataRoot** jest ustawiona.
2. Ustaw punkt przerwania w swojej C# związanym z kodem.
3. Powrót do edytora skryptów, kliknij prawym przyciskiem myszy i wybierz **ADL: Debugowanie lokalne**.
    
   ![Narzędzia Data Lake Tools dla wyniku lokalnego debugowania programu Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Kolejne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Tworzenie języka U-SQL przy użyciu języka Python, R i CSharp usługi Azure Data Lake Analytics w VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Użyj narzędzi Data Lake Tools for Visual Studio do tworzenia aplikacji w języku U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
