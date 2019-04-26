---
title: Najlepsze rozwiązania dotyczące zarządzania zestawów języka U-SQL w potoku ciągłej integracji/ciągłego wdrażania dla usługi Azure Data Lake
description: Poznaj najlepsze rozwiązania dotyczące zarządzania U-SQL C# zestawów w potoku ciągłej integracji/ciągłego wdrażania za pomocą DevOps platformy Azure.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 27a873fac8bf2b53ee06780b8a348eaaa5c94e97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334270"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Najlepsze rozwiązania dotyczące zarządzania zestawów języka U-SQL w potoku ciągłej integracji/ciągłego wdrażania

W tym artykule dowiesz się, jak zarządzać kodem źródłowym zestawu U-SQL przy użyciu nowo wprowadzonych projekt U-SQL w bazie danych. Poznasz również sposób konfigurowania ciągłej integracji i potoku wdrażania (CI/CD) na potrzeby rejestracji zestawów przy użyciu DevOps platformy Azure.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Użyj projektu bazy danych U-SQL do zarządzania kodem źródłowym zestawu

[Projekt bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) jest typ projektu w programie Visual Studio, która ułatwia deweloperom tworzenie, zarządzanie i wdrażanie ich bazy danych U-SQL, szybko i łatwo. Możesz zarządzać wszystkie obiekty bazy danych U-SQL (z wyjątkiem poświadczeń) z projektem bazy danych U-SQL. 

Aby zarządzać C# kod źródłowy zestawu i skrypty języka DDL U-SQL rejestracji zestawów, należy użyć:

* Projekt bazy danych U-SQL do zarządzania skryptami języka U-SQL rejestracji zestawu.
* Klasy biblioteki (dla języka U-SQL aplikacji) do zarządzania C# w kodzie źródłowym oraz zależności dla operatorów zdefiniowanych przez użytkownika, funkcje i agregatorów (udo, funkcje zdefiniowane przez użytkownika i UDAGs).
* Projekt bazy danych U-SQL, aby odwoływać się do projektu biblioteki klas. 

Projekt bazy danych U-SQL można odwoływać się do projektu biblioteki klas (dla aplikacji języka U-SQL). Możesz utworzyć zestawy zarejestrowana w bazie danych U-SQL przy użyciu, do których odwołuje się C# źródła kodu z tego projektu biblioteki klas (dla aplikacji języka U-SQL).

Wykonaj następujące kroki, aby tworzyć projekty i dodaj odwołania do.
1. Utwórz projekt biblioteki klas (dla aplikacji języka U-SQL), wybierając **pliku** > **New** > **projektu**. Projekt jest pod **usługi Azure Data Lake > U-SQL** węzła.

   ![Usługa Data Lake Tools for Visual Studio — Utwórz C# projekt biblioteki klas](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Dodawanie sieci zdefiniowanej przez użytkownika C# kod w projekcie biblioteki klas (dla aplikacji języka U-SQL).

1. Utwórz projekt U-SQL, wybierając **pliku** > **New** > **projektu**. Projekt jest pod **usługi Azure Data Lake** > **U-SQL** węzła.

   ![Narzędzia Data Lake Tools for Visual Studio — Utwórz U-SQL projektu bazy danych](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Dodaj odwołanie do C# projekt biblioteki klas dla projektu bazy danych U-SQL.

    ![Narzędzia Data Lake Tools for Visual Studio — Dodawanie języka U-SQL bazy danych odwołania do projektu](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Narzędzia Data Lake Tools for Visual Studio — Dodawanie języka U-SQL bazy danych odwołania do projektu](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Utwórz skrypt zestawu w projekcie bazy danych U-SQL, kliknij prawym przyciskiem myszy projekt i wybierając **Dodaj nowy element**.

   ![Usługa Data Lake Tools for Visual Studio — Dodaj skrypt zestawu](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Otwórz skrypt zestawu w widoku Projekt zestawu. Wybierz zestaw z odwołania z **tworzenia zestawu z odwołania** menu rozwijanego.

    ![Narzędzia Data Lake Tools for Visual Studio — Tworzenie zestawu z odwołania](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Dodaj **zarządzanych zależności** i **dodatkowe pliki**, jeśli istnieją. Po dodaniu dodatkowych plików, to narzędzie używa ścieżki względnej do upewnij się, że można odnaleźć zestawów na komputerze lokalnym i na komputerze kompilacji później.

**\@_DeployTempDirectory** w edytorze u dołu okna jest wstępnie zdefiniowanej zmiennej, która wskazuje narzędziu do folderu wyjściowego kompilacji. W folderze danych wyjściowych kompilacji każdy zestaw miał podfolder o nazwie zestawu. Wszystkie biblioteki dll i dodatkowe pliki znajdują się w tym podfolderze.

## <a name="build-a-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Dane wyjściowe kompilacji dla projektu bazy danych U-SQL jest pakiet wdrażania bazy danych U-SQL. Jest on nazwany z sufiksem `.usqldbpack`. `.usqldbpack` Pakiet jest plik .zip zawierający wszystkie instrukcje języka DDL w jednego skryptu U-SQL w folderze DDL. Wszystkie pliki .dll skompilowane i dodatkowe pliki zestawów znajdują się w folderze Temp.

## <a name="deploy-a-u-sql-database"></a>Wdrażanie bazy danych U-SQL

`.usqldbpack` Można wdrożyć pakietu dla konta lokalnego lub konta usługi Azure Data Lake Analytics. Użyj programu Visual Studio lub wdrożenie zestawu SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Bazy danych U-SQL można wdrożyć za pomocą projektu bazy danych U-SQL lub `.usqldbpack` pakietów w programie Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Wdrażanie przy użyciu projektu bazy danych U-SQL

1.  Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz **Wdróż**.
2.  W **wdrażania bazy danych U-SQL** kreatora wybierz **konta ADLA** do której chcesz wdrożyć bazy danych. Obsługiwane są zarówno kont lokalnych, jak i kont ADLA.
3.  **Bazy danych źródła** jest wypełniane automatycznie. Wskazuje on na pakiet .usqldbpack w folderze danych wyjściowych kompilacji projektu.
4.  Wprowadź nazwę w **Nazwa bazy danych** utworzyć bazę danych. Jeśli baza danych o tej samej nazwie już istnieje w miejscu docelowym kontem usługi Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych są tworzone bez potrzeby ponownego tworzenia bazy danych.
5.  Aby wdrożyć bazę danych U-SQL, wybierz **przesyłania**. Wszystkie zasoby, takie jak zestawy i dodatkowe pliki zostaną przekazane. Przesłaniu zadania U-SQL, który zawiera wszystkie instrukcje języka DDL.

    ![Narzędzia Data Lake Tools for Visual Studio — wdrażanie U-SQL projektu bazy danych](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Kreator projektu bazy danych narzędzi Data Lake Tools for Visual Studio — wdrażanie U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Wdrażanie bazy danych U-SQL w DevOps platformy Azure

`PackageDeploymentTool.exe` udostępnia programowania i interfejsów z wierszem polecenia, które ułatwiają wdrażanie bazy danych U-SQL. Zestaw SDK jest uwzględniony w [pakietu Nuget zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)znajdujący się w folderze `build/runtime/PackageDeploymentTool.exe`.

W metodyce DevOps platformy Azure można użyć zadania wiersza polecenia i ten zestaw SDK do konfigurowania potoku automatyzacji do odświeżania bazy danych U-SQL. [Dowiedz się więcej o zestawie SDK oraz sposób konfigurowania potoku ciągłej integracji/ciągłego Dostarczania do wdrożenia bazy danych U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Kolejne kroki

* [Skonfiguruj potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Testowanie kodu Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
