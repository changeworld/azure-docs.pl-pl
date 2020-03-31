---
title: Zarządzanie zestawami U-SQL w potoku ciągłej integracji/ciągłego wdrażania — usługa Azure Data Lake
description: Poznaj najlepsze rozwiązania dotyczące zarządzania zestawami języka C# u-SQL w potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315848"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Najważniejsze wskazówki dotyczące zarządzania zestawami U-SQL w potoku ciągłej integracji/ciągłego wdrażania

W tym artykule dowiesz się, jak zarządzać kodem źródłowym zestawu U-SQL za pomocą nowo wprowadzonego projektu bazy danych U-SQL. Dowiesz się również, jak skonfigurować potok ciągłej integracji i wdrażania (CI/CD) do rejestracji zestawu przy użyciu usługi Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Zarządzanie kodem źródłowym zestawu za pomocą projektu bazy danych U-SQL

[Projekt bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) jest typem projektu w programie Visual Studio, który pomaga deweloperom szybko i łatwo wdrażać bazy danych U-SQL. Za pomocą projektu bazy danych U-SQL można zarządzać wszystkimi obiektami bazy danych U-SQL (z wyjątkiem poświadczeń). 

Aby zarządzać kodem źródłowym zestawu C# i skryptami DDL U-SQL rejestracji zestawu, należy użyć:

* Projekt bazy danych U-SQL do zarządzania skryptami U-SQL rejestracji zestawu.
* Biblioteka klas (dla aplikacji U-SQL) do zarządzania kodem źródłowym języka C# i zależności dla operatorów zdefiniowanych przez użytkownika, funkcji i agregatorów (UDOs, UDFs i UDAGs).
* Projekt bazy danych U-SQL do odwołania się do projektu biblioteki klas. 

Projekt bazy danych U-SQL może odwoływać się do projektu biblioteki klas (dla aplikacji U-SQL). Można utworzyć zestawy zarejestrowane w bazie danych U-SQL przy użyciu kodu źródłowego odwołania C# z tego projektu biblioteki klas (dla aplikacji U-SQL).

Wykonaj następujące kroki, aby utworzyć projekty i dodać odwołania.
1. Utwórz projekt biblioteki klas (dla aplikacji U-SQL), wybierając **pozycję Plik** > **nowego** > **projektu**. Projekt znajduje się w obszarze usługi Azure Data Lake > węźle **U-SQL.**

   ![Narzędzia usługi Data Lake tools dla programu Visual Studio — tworzenie projektu biblioteki klas języka C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Dodaj zdefiniowany przez użytkownika kod C# w projekcie biblioteki klas (dla aplikacji U-SQL).

1. Utwórz projekt U-SQL, wybierając **pozycję Plik** > **nowego** > **projektu**. Projekt znajduje się w węźle **Azure Data Lake** > **U-SQL.**

   ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — tworzenie projektu bazy danych U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Dodaj odwołanie do projektu biblioteki klas C# dla projektu bazy danych U-SQL.

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — dodawanie odwołania do projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — dodawanie odwołania do projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Utwórz skrypt złożenia w projekcie bazy danych U-SQL, klikając prawym przyciskiem myszy projekt i wybierając pozycję **Dodaj nowy element**.

   ![Narzędzia usługi Data Lake tools dla programu Visual Studio — dodawanie skryptu zestawu](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Otwórz skrypt złożenia w widoku projektu złożenia. Wybierz zestaw, do którego istnieje odwołanie, z menu rozwijanego **Utwórz z** menu rozwijanego Odwołanie.

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — tworzenie zestawu na podstawie odwołania](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Dodaj **zależności zarządzane** i dodatkowe **pliki**, jeśli istnieją. Po dodaniu dodatkowych plików narzędzie używa ścieżki względnej, aby upewnić się, że można znaleźć zestawy na komputerze lokalnym i na komputerze kompilacji później.

_DeployTempDirectory w oknie edytora u dołu jest wstępnie zdefiniowaną zmienną, która wskazuje narzędzie do folderu wyjściowego kompilacji. ** \@** W folderze wyjścia kompilacji każdy zestaw ma podfolder o nazwie o nazwie zestawu. Wszystkie biblioteki DLL i dodatkowe pliki znajdują się w tym podfolderze.

## <a name="build-a-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Dane wyjściowe kompilacji dla projektu bazy danych U-SQL jest pakiet wdrażania bazy danych U-SQL. Jego nazwa została nazwana `.usqldbpack`przyrostkiem . Pakiet `.usqldbpack` jest plikiem zip, który zawiera wszystkie instrukcje DDL w jednym skrypcie U-SQL w folderze DDL. Wszystkie zbudowane pliki dll i dodatkowe pliki dla zestawów znajdują się w folderze Temp.

## <a name="deploy-a-u-sql-database"></a>Wdrażanie bazy danych U-SQL

Pakiet `.usqldbpack` można wdrożyć na koncie lokalnym lub koncie usługi Azure Data Lake Analytics. Użyj programu Visual Studio lub wdrożenia SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Bazę danych U-SQL można wdrożyć przy użyciu `.usqldbpack` projektu bazy danych U-SQL lub pakietu w programie Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Wdrażanie przy użyciu projektu bazy danych U-SQL

1.  Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz polecenie **Wdrażanie**.
2.  W Kreatorze **wdrażania bazy danych U-SQL** wybierz **konto ADLA,** na którym chcesz wdrożyć bazę danych. Obsługiwane są zarówno konta lokalne, jak i konta ADLA.
3.  **Źródło bazy danych** jest wypełniane automatycznie. Wskazuje na pakiet .usqldbpack w folderze wyjściowym kompilacji projektu.
4.  Wprowadź nazwę w **nazwie bazy danych,** aby utworzyć bazę danych. Jeśli baza danych o tej samej nazwie już istnieje na docelowym koncie usługi Azure Data Lake Analytics, wszystkie obiekty zdefiniowane w projekcie bazy danych są tworzone bez ponownego tworzenia bazy danych.
5.  Aby wdrożyć bazę danych U-SQL, wybierz pozycję **Prześlij**. Wszystkie zasoby, takie jak zestawy i dodatkowe pliki, są przekazywane. Zadanie U-SQL, które zawiera wszystkie instrukcje DDL jest przesyłany.

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — wdrażanie projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — kreator wdrażania projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Wdrażanie bazy danych U-SQL w usłudze Azure DevOps

`PackageDeploymentTool.exe`udostępnia interfejsy programowania i wiersza polecenia, które pomagają wdrożyć bazy danych U-SQL. Pakiet SDK znajduje się w [pakiecie U-SQL SDK Nuget,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)znajdującym się pod adresem `build/runtime/PackageDeploymentTool.exe`.

W usłudze Azure DevOps można użyć zadania wiersza polecenia i tego zestawu SDK, aby skonfigurować potok automatyzacji do odświeżania bazy danych U-SQL. [Dowiedz się więcej o zestawie SDK i sposobie konfigurowania potoku ciągłej integracji/ciągłego wdrażania bazy danych U-SQL.](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie potoku ciągłej integracji/ciągłego wdrażania dla usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Testowanie kodu usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Uruchamianie skryptu U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
