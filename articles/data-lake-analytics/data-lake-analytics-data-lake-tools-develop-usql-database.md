---
title: Użyj projektu bazy danych U-SQL do tworzenia bazy danych U-SQL dla usługi Azure Data Lake
description: Dowiedz się, jak tworzyć bazy danych U-SQL przy użyciu narzędzi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 47235fa5676acd8de8a7cc0d969b813837faf0af
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469713"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Użyj projektu bazy danych U-SQL do tworzenia bazy danych U-SQL dla usługi Azure Data Lake

Bazy danych U-SQL zapewnia widoki ze strukturą za pośrednictwem danych bez struktury oraz zarządzanych ze strukturą danych w tabelach. Umożliwia także system katalogu metadanych ogólny służący do organizowania danych ze strukturą i kod niestandardowy. Baza danych jest koncepcji, który grupuje obiekty powiązane ze sobą.

Dowiedz się więcej o [bazy danych U-SQL i języka definicji danych (DDL)](/u-sql/data-definition-language-ddl-statements). 

Projekt bazy danych U-SQL jest typ projektu w programie Visual Studio, która ułatwia deweloperom tworzenie, zarządzanie i wdrażanie ich bazy danych U-SQL, szybko i łatwo.

## <a name="create-a-u-sql-database-project"></a>Utwórz projekt bazy danych U-SQL

Usługa Azure Data Lake Tools for Visual Studio dodano nowy szablon projektu o nazwie projekt bazy danych U-SQL po wersji 2.3.3000.0. Aby utworzyć projekt U-SQL, wybierz **Plik > Nowy > Projekt**. Projekt bazy danych U-SQL można znaleźć w obszarze **usługi Azure Data Lake > U-SQL węzła**.

![Narzędzia Data Lake Tools for Visual Studio — Utwórz projekt bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Tworzenie obiektów bazy danych U-SQL przy użyciu projektu bazy danych

Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL. Wybieranie **Dodaj > Nowy element**. Możesz znaleźć wszystkie nowe obsługiwane typy obiektów **Dodaj nowy element** kreatora. 

Dla obiektu inne niż zestawu (na przykład funkcji zwracającej tabelę) po dodaniu nowego elementu tworzony jest nowy skrypt U-SQL. Możesz rozpocząć tworzenie instrukcji DDL dla tego obiektu w edytorze.

Dla obiektu zestawu to narzędzie zawiera przyjazny dla użytkownika Edytor interfejsu użytkownika, który pomoże Ci zarejestrować zestaw i wdrażanie plików DLL i inne dodatkowe pliki. Poniższe kroki pokazują, jak dodać zestaw definicji obiektu do projektu bazy danych U-SQL:

1.  Dodaj odwołania do projektu C#, zawierających operatory zdefiniowane przez użytkownika/UDAG/UDF dla projektu bazy danych U-SQL.

    ![Narzędzia Data Lake Tools for Visual Studio — Dodawanie języka U-SQL bazy danych odwołania do projektu](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Narzędzia Data Lake Tools for Visual Studio — Dodawanie języka U-SQL bazy danych odwołania do projektu](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  W widoku Projekt zestawu, wybierz przywoływanego zestawu z **tworzenia zestawu z odwołania** menu rozwijanego.

    ![Narzędzia Data Lake Tools for Visual Studio — Tworzenie zestawu z odwołania](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Dodaj **zarządzanych zależności** i **dodatkowe pliki** ile jakieś istnieją. Po dodaniu dodatkowych plików, to narzędzie używa ścieżki względnej do upewnij się, że można odnaleźć zestawów na komputerze lokalnym i na maszynie kompilacji później. 

@_DeployTempDirectory jest wstępnie zdefiniowanej zmiennej, która wskazuje narzędziu do folderu wyjściowego kompilacji. W folderze danych wyjściowych kompilacji każdy zestaw miał podfolder o nazwie zestawu. Wszystkie biblioteki dll i dodatkowe pliki znajdują się w tym podfolderze. 
 
## <a name="build-a-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Dane wyjściowe projektu bazy danych U-SQL jest pakiet wdrażania bazy danych U-SQL, nazwę z sufiksem kompilacji `.usqldbpack`. `.usqldbpack` Pakiet jest plik .zip zawierający wszystkie instrukcje języka DDL w jednego skryptu U-SQL w **DDL** folderu oraz wszystkie biblioteki dll i dodatkowe pliki dla zestawów w **Temp** folderu.

Dowiedz się więcej o [sposób do utworzenia projektu bazy danych U-SQL za pomocą narzędzia MSBuild wiersz polecenia i usługom DevOps platformy Azure zadania kompilacji](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Wdrażanie bazy danych U-SQL

Pakiet .usqldbpack można wdrożyć do konta lokalnego lub konta usługi Azure Data Lake Analytics przy użyciu programu Visual Studio lub wdrożenie zestawu SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Możesz wdrożyć bazę danych U-SQL za pomocą projektu bazy danych U-SQL lub pakietu .usqldbpack w programie Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Wdrażanie za pośrednictwem projektu bazy danych U-SQL

1.  Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz **Wdróż**.
2.  W **Kreatora wdrażania bazy danych U-SQL**, wybierz opcję **konta ADLA** do której chcesz wdrożyć bazy danych. Obsługiwane są zarówno kont lokalnych, jak i kont ADLA.
3.  **Bazy danych źródła** jest wypełniane automatycznie, i wskazuje pakietu .usqldbpack w projekcie folderze danych wyjściowych kompilacji.
4.  Wprowadź nazwę w **Nazwa bazy danych** utworzyć bazę danych. Jeśli baza danych o tej samej nazwie już istnieje w miejscu docelowym kontem usługi Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych są tworzone bez konieczności ponownego tworzenia bazy danych.
5.  Aby wdrożyć bazę danych U-SQL, wybierz **przesyłania**. Wszystkie zasoby (zestawy i dodatkowe pliki) są przekazywane i przesłaniu zadania U-SQL, który zawiera wszystkie instrukcje języka DDL.

    ![Narzędzia Data Lake Tools for Visual Studio — wdrażanie U-SQL projektu bazy danych](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Kreator projektu bazy danych narzędzi Data Lake Tools for Visual Studio — wdrażanie U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Wdrażanie za pośrednictwem pakietu wdrażania bazy danych U-SQL

1.  Otwórz **Eksploratora serwera**. Następnie rozwiń **konta usługi Azure Data Lake Analytics** do której chcesz wdrożyć bazy danych.
2.  Kliknij prawym przyciskiem myszy **bazy danych U-SQL**, a następnie wybierz **wdrażania bazy danych**.
3.  Ustaw **źródła bazy danych** ścieżkę pakiet (plik .usqldbpack) wdrożenia bazy danych U-SQL.
4.  Wprowadź **Nazwa bazy danych** utworzyć bazę danych. W przypadku bazy danych o takiej samej nazwie, która już istnieje w docelowym kontem usługi Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych są tworzone bez konieczności ponownego tworzenia bazy danych.

    ![Narzędzia Data Lake Tools for Visual Studio — wdrażanie U-SQL bazy danych pakietu](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Kreator pakietu bazy danych narzędzi Data Lake Tools for Visual Studio — wdrażanie U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Wdrażanie bazy danych U-SQL przy użyciu zestawu SDK

`PackageDeploymentTool.exe` udostępnia programowania i interfejsów z wierszem polecenia, które ułatwiają wdrażanie bazy danych U-SQL. Zestaw SDK jest uwzględniony w [pakietu Nuget zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)znajdujący się w folderze `build/runtime/PackageDeploymentTool.exe`.

[Dowiedz się więcej na temat zestawu SDK oraz sposób konfigurowania potoku ciągłej integracji/ciągłego wdrażania na potrzeby wdrażania bazy danych U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Odwoływać się do projektu bazy danych U-SQL

Projekt U-SQL można odwoływać się do projektu bazy danych U-SQL. Odwołanie ma wpływ na dwóch obciążeń:

- *Projekt kompilacji*: Konfigurowanie środowiska, w której istnieje odwołanie przed tworzenie skryptów U-SQL. 
- *Konto wykonywania (lokalnego projekt)*: Środowiska, w której istnieje odwołanie są wdrażane do (lokalnego projektu) konta przed wykonaniem skryptu U-SQL. [Dowiedz się więcej o lokalnych przebiegów i różnica między (lokalnego maszyna) oraz (lokalnego projekt) konta w tym miejscu](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Jak dodać odwołanie do bazy danych U-SQL

1. Kliknij prawym przyciskiem myszy projekt U-SQL w **Eksploratora rozwiązań**, a następnie wybierz **Dodaj odwołanie do bazy danych U-SQL...** .

    ![Narzędzia Data Lake Tools for Visual Studio — Dodaj odwołanie do projektu bazy danych](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfiguruj odwołanie do bazy danych z projektu bazy danych U-SQL w bieżącym rozwiązaniu lub plik pakietu bazy danych U-SQL.
3. Podaj nazwę dla bazy danych.

    ![Narzędzia Data Lake Tools for Visual Studio Dodaj kreatora odwołanie do projektu bazy danych](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Kolejne kroki

- [Jak skonfigurować potok CI/CD dla usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
