---
title: Najlepszym rozwiązaniem jest zarządzania zestawów języka U-SQL w potoku CI/CD dla usługi Azure Data Lake
description: Dowiedz się, najlepszym rozwiązaniem jest zarządzania U-SQL C# zestawów w ciągłej integracji/ciągłego wdrażania potoku przy użyciu infrastruktury DevOps platformy Azure.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634247"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Najlepszym rozwiązaniem jest zarządzania zestawów języka U-SQL w potoku ciągłej integracji/ciągłego wdrażania

W tym artykule dowiesz się, jak zarządzać kodem źródłowym zestawu U-SQL przy użyciu nowego wprowadzono projektu bazy danych U-SQL. Możesz także dowiedzieć się, jak do skonfigurowania ciągłej integracji i potoku wdrażania (CI/CD) na potrzeby rejestracji zestawów przy użyciu DevOps platformy Azure.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Użyj projektu bazy danych U-SQL do zarządzania kodem źródłowym zestawu

[Projekt bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) jest typ projektu w programie Visual Studio, która ułatwia deweloperom tworzenie, zarządzanie i wdrażanie ich bazy danych U-SQL, szybko i łatwo. Wszystkich obiektów bazy danych U-SQL (z wyjątkiem poświadczeń) można zarządzać za pomocą projektu bazy danych U-SQL. 

Zalecanym sposobem zarządzania C# jest zestaw źródłowy kod i zestawu rejestracji DDL skryptów U-SQL:

* Użyj **projekt bazy danych U-SQL** do zarządzania skryptami języka U-SQL rejestracji zestawu.
* Użyj **biblioteki klas (dla aplikacji języka U-SQL)** zarządzanie C# w kodzie źródłowym oraz zależności dla operatorów zdefiniowanych przez użytkownika, funkcje i agregatorów (udo/UDF/UDAGs).
* Aby odwołać projekt biblioteki klas, należy użyć projektu bazy danych U-SQL. 

Projekt bazy danych U-SQL można odwoływać się do projektu biblioteki klas (dla aplikacji języka U-SQL). Zestawy zarejestrowanej w bazie danych U-SQL można tworzyć za pomocą odwołania C# źródła kodu z tego projektu biblioteki klas (dla aplikacji języka U-SQL).

Możesz wykonać poniższe kroki tworzenia projektów i dodanie odwołania do:
1. Utwórz projekt biblioteki klas (dla aplikacji języka U-SQL) za pośrednictwem **Plik > Nowy > Projekt**. Projekt jest pod **usługi Azure Data Lake > U-SQL** węzła.
   ![Usługa Data Lake Tools for Visual Studio — Utwórz C# projekt biblioteki klas](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Dodawanie sieci zdefiniowanej przez użytkownika C# kod w projekcie biblioteki klas (dla aplikacji języka U-SQL). 
3. Utwórz projekt U-SQL przy użyciu **Plik > Nowy > Projekt**. Projekt jest pod **usługi Azure Data Lake > U-SQL** węzła.
   ![Narzędzia Data Lake Tools for Visual Studio — Utwórz U-SQL projektu bazy danych](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Dodaj odwołanie do C# projekt biblioteki klas dla projektu bazy danych U-SQL.

    ![Narzędzia Data Lake Tools for Visual Studio — Dodawanie języka U-SQL bazy danych odwołania do projektu](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Narzędzia Data Lake Tools for Visual Studio — Dodawanie języka U-SQL bazy danych odwołania do projektu](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Utwórz skrypt zestawu w projekcie bazy danych U-SQL przy użyciu **kliknij prawym przyciskiem myszy Projekt > Dodaj nowy element**.
   ![Usługa Data Lake Tools for Visual Studio — Dodaj skrypt zestawu](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Otwórz skrypt zestawu w widoku Projekt zestawu, wybierz opcję przywoływanego zestawu z **tworzenia zestawu z odwołania** menu rozwijanego.

    ![Narzędzia Data Lake Tools for Visual Studio — Tworzenie zestawu z odwołania](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Dodaj **zarządzanych zależności** i **dodatkowe pliki** ile jakieś istnieją. Po dodaniu dodatkowych plików, to narzędzie używa ścieżki względnej do upewnij się, że można odnaleźć zestawów na komputerze lokalnym i na maszynie kompilacji później. 

**@_DeployTempDirectory** w edytorze u dołu okna jest wstępnie zdefiniowanej zmiennej, która wskazuje narzędziu do folderu wyjściowego kompilacji. W folderze danych wyjściowych kompilacji każdy zestaw miał podfolder o nazwie zestawu. Wszystkie biblioteki dll i dodatkowe pliki znajdują się w tym podfolderze. 

## <a name="build-a-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Dane wyjściowe projektu bazy danych U-SQL jest pakiet wdrażania bazy danych U-SQL, nazwę z sufiksem kompilacji `.usqldbpack`. `.usqldbpack` Pakiet jest plik .zip zawierający wszystkie instrukcje języka DDL w jednego skryptu U-SQL w **DDL** folder, a wszystkie utworzone biblioteki dll i dodatkowe pliki dla zestawów w **Temp** folderu.

## <a name="deploy-a-u-sql-database"></a>Wdrażanie bazy danych U-SQL

`.usqldbpack` Pakietu można wdrożyć do konta lokalnego lub konta usługi Azure Data Lake Analytics przy użyciu programu Visual Studio lub wdrożenie zestawu SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Można wdrożyć bazy danych U-SQL za pomocą projektu bazy danych U-SQL lub `.usqldbpack` pakietów w programie Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Wdrażanie za pośrednictwem projektu bazy danych U-SQL

1.  Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz **Wdróż**.
2.  W **Kreatora wdrażania bazy danych U-SQL**, wybierz opcję **konta ADLA** do której chcesz wdrożyć bazy danych. Obsługiwane są zarówno kont lokalnych, jak i kont ADLA.
3.  **Bazy danych źródła** jest wypełniane automatycznie, i wskazuje pakietu .usqldbpack w projekcie folderze danych wyjściowych kompilacji.
4.  Wprowadź nazwę w **Nazwa bazy danych** utworzyć bazę danych. Jeśli baza danych o tej samej nazwie już istnieje w miejscu docelowym kontem usługi Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych są tworzone bez konieczności ponownego tworzenia bazy danych.
5.  Aby wdrożyć bazę danych U-SQL, wybierz **przesyłania**. Wszystkie zasoby (zestawy i dodatkowe pliki) są przekazywane i przesłaniu zadania U-SQL, który zawiera wszystkie instrukcje języka DDL.

    ![Narzędzia Data Lake Tools for Visual Studio — wdrażanie U-SQL projektu bazy danych](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Kreator projektu bazy danych narzędzi Data Lake Tools for Visual Studio — wdrażanie U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Wdrażanie bazy danych U-SQL w DevOps platformy Azure

`PackageDeploymentTool.exe` udostępnia programowania i interfejsów z wierszem polecenia, które ułatwiają wdrażanie bazy danych U-SQL. Zestaw SDK jest uwzględniony w [pakietu Nuget zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)znajdujący się w folderze `build/runtime/PackageDeploymentTool.exe`.

W metodyce DevOps platformy Azure można użyć zadania wiersza polecenia i ten zestaw SDK do konfigurowania potoku automatyzacji do odświeżania bazy danych U-SQL. [Dowiedz się więcej na temat zestawu SDK oraz sposób konfigurowania potoku ciągłej integracji/ciągłego wdrażania na potrzeby wdrażania bazy danych U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Kolejne kroki

* [Jak skonfigurować potok CI/CD dla usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
