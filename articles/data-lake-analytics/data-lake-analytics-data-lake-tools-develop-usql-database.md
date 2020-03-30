---
title: Tworzenie projektu bazy danych U-SQL — usługa Azure Data Lake
description: Dowiedz się, jak opracować bazę danych U-SQL przy użyciu narzędzia usługi Azure Data Lake Tools for Visual Studio.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309925"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Tworzenie bazy danych U-SQL dla usługi Azure Data Lake za pomocą projektu bazy danych U-SQL

Baza danych U-SQL udostępnia widoki strukturalne nad danymi nieustrukturyzowanymi i zarządzanymi danymi strukturalnymi w tabelach. Zapewnia również ogólny system katalogów metadanych do organizowania danych strukturalnych i kodu niestandardowego. Baza danych jest pojęciem, które grupuje te powiązane obiekty razem.

Dowiedz się więcej o [bazie danych U-SQL i języku DDL (Data Definition Language).](/u-sql/data-definition-language-ddl-statements) 

Projekt bazy danych U-SQL jest typem projektu w programie Visual Studio, który pomaga deweloperom szybko i łatwo wdrażać bazy danych U-SQL.

## <a name="create-a-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Narzędzia usługi Azure Data Lake Tools for Visual Studio dodały nowy szablon projektu o nazwie Projekt bazy danych U-SQL po wersji 2.3.3000.0. Aby utworzyć projekt U-SQL, wybierz **opcję Plik > Nowy projekt >**. Projekt bazy danych U-SQL można znaleźć w obszarze **Usługi Azure Data Lake > węzłem U-SQL**.

![Narzędzia usługi Data Lake Tools dla programu Visual Studio — tworzenie projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Tworzenie obiektów bazy danych U-SQL przy użyciu projektu bazy danych

Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL. Wybierz pozycję **Dodaj > Nowy element**. Wszystkie nowe obsługiwane typy obiektów można znaleźć w Kreatorze **dodawania nowego elementu.** 

Dla obiektu niezwiązanego z złożeniem (na przykład funkcji wycenianej w tabeli) po dodaniu nowego elementu tworzony jest nowy skrypt U-SQL. Można rozpocząć tworzenie instrukcji DDL dla tego obiektu w edytorze.

Dla obiektu zestawu narzędzie zapewnia przyjazny dla użytkownika edytor interfejsu użytkownika, który pomaga zarejestrować zestaw i wdrożyć pliki DLL i inne dodatkowe pliki. Poniższe kroki pokazują, jak dodać definicję obiektu zestawu do projektu bazy danych U-SQL:

1.  Dodaj odwołania do projektu C#, które zawierają UDO/UDAG/UDF dla projektu bazy danych U-SQL.

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — dodawanie odwołania do projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — dodawanie odwołania do projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  W widoku projektu złożenia wybierz zestaw odniesienia z menu rozwijanego **Utwórz z** menu rozwijanego.

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — tworzenie zestawu na podstawie odwołania](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Dodaj **zależności zarządzane** i dodatkowe **pliki,** jeśli istnieją. Po dodaniu dodatkowych plików narzędzie używa ścieżki względnej, aby upewnić się, że można znaleźć zestawy zarówno na komputerze lokalnym, jak i na komputerze kompilacji później. 

@_DeployTempDirectoryjest wstępnie zdefiniowaną zmienną, która wskazuje narzędzie do folderu wyjściowego kompilacji. W folderze wyjścia kompilacji każdy zestaw ma podfolder o nazwie o nazwie zestawu. Wszystkie biblioteki DLL i dodatkowe pliki znajdują się w tym podfolderze. 
 
## <a name="build-a-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Dane wyjściowe kompilacji dla projektu bazy danych U-SQL to pakiet wdrażania `.usqldbpack`bazy danych U-SQL o nazwie z sufiksem . Pakiet `.usqldbpack` jest plikiem zip, który zawiera wszystkie instrukcje DDL w jednym skrypcie U-SQL w folderze **DDL** oraz wszystkie biblioteki DLL i dodatkowe pliki dla zestawów w folderze **Temp.**

Dowiedz się więcej o [tym, jak utworzyć projekt bazy danych U-SQL za pomocą wiersza polecenia MSBuild i zadania kompilacji usług Azure DevOps](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Wdrażanie bazy danych U-SQL

Pakiet .usqldbpack można wdrożyć na koncie lokalnym lub koncie usługi Azure Data Lake Analytics przy użyciu programu Visual Studio lub zestawu SDK wdrażania. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Bazę danych U-SQL można wdrożyć za pośrednictwem projektu bazy danych U-SQL lub pakietu .usqldbpack w programie Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Wdrażanie za pomocą projektu bazy danych U-SQL

1.  Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz polecenie **Wdrażanie**.
2.  W **Kreatorze wdrażania bazy danych U-SQL**wybierz **konto ADLA,** na którym chcesz wdrożyć bazę danych. Obsługiwane są zarówno konta lokalne, jak i konta ADLA.
3.  **Źródło bazy danych** jest wypełniane automatycznie i wskazuje pakiet .usqldbpack w folderze wyjściowym kompilacji projektu.
4.  Wprowadź nazwę w **nazwie bazy danych,** aby utworzyć bazę danych. Jeśli baza danych o tej samej nazwie już istnieje na docelowym koncie usługi Azure Data Lake Analytics, wszystkie obiekty zdefiniowane w projekcie bazy danych są tworzone bez ponownego tworzenia bazy danych.
5.  Aby wdrożyć bazę danych U-SQL, wybierz pozycję **Prześlij**. Wszystkie zasoby (zestawy i dodatkowe pliki) są przekazywane, a zadanie U-SQL, które zawiera wszystkie instrukcje DDL jest przesyłany.

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — wdrażanie projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — kreator wdrażania projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Wdrażanie za pomocą pakietu wdrażania bazy danych U-SQL

1.  Otwórz **Eksploratora serwera**. Następnie rozwiń **konto usługi Azure Data Lake Analytics,** na którym chcesz wdrożyć bazę danych.
2.  Kliknij prawym przyciskiem myszy **pozycję Bazy danych U-SQL**, a następnie wybierz polecenie **Wdrażanie bazy danych**.
3.  Ustaw **źródło bazy danych** na ścieżkę pakietu wdrażania bazy danych U-SQL (plik usqldbpack).
4.  Wprowadź **nazwę bazy danych,** aby utworzyć bazę danych. Jeśli istnieje baza danych o tej samej nazwie, która już istnieje na docelowym koncie usługi Azure Data Lake Analytics, wszystkie obiekty zdefiniowane w projekcie bazy danych są tworzone bez ponownego tworzenia bazy danych.

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — wdrażanie pakietu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Narzędzia usługi Data Lake Tools dla programu Visual Studio — kreator wdrażania pakietu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Wdrażanie bazy danych U-SQL przy użyciu narzędzia SDK

`PackageDeploymentTool.exe`udostępnia interfejsy programowania i wiersza polecenia, które pomagają wdrożyć bazy danych U-SQL. Pakiet SDK znajduje się w [pakiecie U-SQL SDK Nuget,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)znajdującym się pod adresem `build/runtime/PackageDeploymentTool.exe`.

[Dowiedz się więcej o zestawie SDK i sposobie konfigurowania potoku ciągłej integracji/ciągłego wdrażania bazy danych U-SQL.](data-lake-analytics-cicd-overview.md)

## <a name="reference-a-u-sql-database-project"></a>Odwoływanie się do projektu bazy danych U-SQL

Projekt U-SQL może odwoływać się do projektu bazy danych U-SQL. Odwołanie dotyczy dwóch obciążeń:

- *Kompilacja projektu:* Skonfiguruj środowiska bazy danych, do których istnieje odwołanie, przed utworzeniem skryptów U-SQL. 
- *Uruchom lokalnie przeciwko kontu (projektu lokalnego):* środowiska bazy danych, do których istnieje odwołanie, są wdrażane na koncie (projektu lokalnego) przed wykonaniem skryptu U-SQL. [Dowiedz się więcej o przebiegach lokalnych i różnica między (komputer lokalny) i (projekt lokalny) tutaj](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Jak dodać odwołanie do bazy danych U-SQL

1. Kliknij prawym przyciskiem myszy projekt U-SQL w **Eksploratorze rozwiązań,** a następnie wybierz polecenie **Dodaj odwołanie do bazy danych U-SQL...**.

    ![Narzędzia usługi Data Lake Tools for Visual Studio — dodaj odwołanie do projektu bazy danych](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Skonfiguruj odwołanie do bazy danych z projektu bazy danych U-SQL w bieżącym rozwiązaniu lub w pliku pakietu bazy danych U-SQL.
3. Podaj nazwę bazy danych.

    ![Kreator odwołania do projektu bazy danych usługi Data Lake Tools dla programu Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Uruchamianie skryptu U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
