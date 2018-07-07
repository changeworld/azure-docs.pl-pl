---
title: Użyj języka U-SQL bazy danych projektu do tworzenia bazy danych U-SQL dla usługi Azure Data Lake | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć bazy danych U-SQL przy użyciu narzędzi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889597"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Użyj projektu bazy danych U-SQL do tworzenia bazy danych U-SQL dla usługi Azure Data Lake

Bazy danych U-SQL zapewnia widoki ze strukturą względem danych bez struktury, zarządzanie ustrukturyzowanych danych w tabelach i zapewnia system katalogu ogólne metadanych do organizowania danych strukturalnych i niestandardowy kod. Baza danych jest koncepcji, który grupuje obiekty powiązane ze sobą.

Dowiedz się więcej o [bazy danych U-SQL i języka definicji danych (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

Projekt bazy danych U-SQL jest typ projektu w programie Visual Studio, która ułatwia deweloperom tworzenie, zarządzanie i wdrażanie ich bazy danych U-SQL, szybko i łatwo.

## <a name="create-a-u-sql-database-project"></a>Utwórz projekt bazy danych U-SQL

Usługa Azure Data Lake Tools for Visual Studio dodano nowy szablon projektu o nazwie projekt bazy danych U-SQL po wersji 2.3.3000.0. Aby utworzyć projekt U-SQL, przejdź **Plik > Nowy > Projekt**, projekt bazy danych U-SQL można znaleźć w obszarze **usługi Azure Data Lake > węzła U-SQL**.

![Narzędzia Data Lake Tools for Visual Studio Utwórz projekt bazy danych u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>Tworzenie obiektów bazy danych U-SQL przy użyciu projektu bazy danych

Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, kliknij przycisk **Dodaj > Nowy element**, wszystkie obsługiwane typy obiektów można znaleźć w Kreatorze dodawania nowego elementu. 

1.  Dla obiektu, inny niż zestawu na przykład wartościami przechowywanymi w tabeli, nowy skrypt U-SQL zostanie utworzona funkcja po dodaniu nowego elementu. Możesz rozpocząć tworzenie instrukcji DDL dla tego obiektu w edytorze.
2.  Dla obiektu zestawu to narzędzie zawiera przyjazny dla użytkownika Edytor interfejsu użytkownika, który pomoże Ci zarejestrować zestaw i wdrażaj .dll i dodatkowe pliki. Wykonaj poniższe kroki, aby dodać zestaw definicji obiektu do projektu bazy danych U-SQL:

1.  Dodaj odwołania do projektu C#, która zawiera operatora zdefiniowanego przez użytkownika/UDAG/funkcji zdefiniowanej przez użytkownika do projektu bazy danych U-SQL.

    ![Narzędzia Data Lake Tools for Visual Studio Dodaj odwołanie do projektu bazy danych u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Narzędzia Data Lake Tools for Visual Studio Dodaj odwołanie do projektu bazy danych u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  W widoku Projekt zestawu, wybierz przywoływanego zestawu z **tworzenia zestawu z odwołania** listy rozwijanej.

    ![Narzędzia Data Lake Tools for Visual Studio utworzyć zestawu z odwołania](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Dodaj **zarządzanych zależności** i **dodatkowe pliki** ewentualne. Podczas dodawania dodatkowych plików, narzędzie użyje ścieżki względnej do upewnij się, że jej później znaleźć zestawy zarówno na komputerze lokalnym i na komputerze kompilacji. @_DeployTempDirectory to wstępnie zdefiniowane zmienna, która wskazuje narzędziu do folderu wyjściowego kompilacji. W obszarze dane wyjściowe kompilacji każdy zestaw miał podfolder o nazwie jak nazwa zestawu, wszystkie biblioteki dll i dodatkowych plików można znaleźć dostępne w podfolderze. 
 
## <a name="build-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Dane wyjściowe projektu bazy danych U-SQL jest pakiet wdrożeniowy bazy danych U-SQL, nazwę z sufiksem kompilacji `.usqldbpack`. `.usqldbpack` Pakiet jest plik zip zawiera wszystkich instrukcji DDL w jednego skryptu U-SQL w **DDL** folderu oraz wszystkie biblioteki dll i dodatkowe pliki dla zestawów w **Temp** folderu.

Dowiedz się więcej o [sposób budowania projektu bazy danych U-SQL przy użyciu wiersza polecenia programu MSBuild i zadania kompilacji usługi Visual Studio Team](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>Wdrażanie bazy danych U-SQL

Można wdrożyć pakietu .usqldbpack konta lokalnego lub na koncie usługi Azure Data Lake Analytics przy użyciu programu Visual Studio lub wdrożenie zestawu SDK. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Możesz wdrożyć bazę danych U-SQL za pomocą projektu bazy danych U-SQL lub pakietu .usqldbpack w programie Visual Studio.

#### <a name="deploy-through-u-sql-database-project"></a>Wdrażanie za pośrednictwem projektu bazy danych U-SQL

1.  Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz **Wdróż**.
2.  W Kreatorze wdrażania bazy danych U-SQL zaznaczyć **konta ADLA** chcesz wdrożyć bazę danych do. Obsługiwane są zarówno konta (Local), jak i konto ADLA.
3.  **Bazy danych źródła** są wypełniane automatycznie wskazuje pakietu .usqldbpack w folderze danych wyjściowych kompilacji projektu
4.  Wprowadź **Nazwa bazy danych** do tworzenia bazy danych. W przypadku bazy danych przy użyciu tego samego istniejących w miejscu docelowym kontem usługi Azure Data Lake Analytics, wszystkie obiekty zdefiniowane w projekcie bazy danych są tworzone bez konieczności ponownego tworzenia bazy danych.
5.  Kliknij przycisk **przesyłania** do wdrożenia bazy danych U-SQL. Wszystkie zasoby (zestawy i dodatkowe pliki) są przekazywane i zadania U-SQL zawiera wszystkie są przesyłane instrukcji DDL.

    ![Narzędzia Data Lake Tools for Visual Studio wdrożyć projekt bazy danych u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Narzędzia Data Lake Tools for Visual Studio wdrożyć Kreator projektu bazy danych u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Wdrażanie za pośrednictwem pakietu wdrożeniowego bazy danych U-SQL

1.  Otwórz **Eksploratora serwera**, rozwiń węzeł **konta usługi Azure Data Lake Analytics** chcesz wdrożyć bazę danych do.
2.  Kliknij prawym przyciskiem myszy bazy danych U-SQL, a następnie wybierz **wdrażania bazy danych**.
3.  Ustaw **źródła bazy danych** ścieżkę pakiet (plik .usqldbpack) wdrożenia bazy danych U-SQL.
4.  Wprowadź **Nazwa bazy danych** do tworzenia bazy danych. W przypadku bazy danych przy użyciu tego samego istniejących w miejscu docelowym kontem usługi Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych są tworzone bez konieczności ponownego tworzenia bazy danych.

    ![Narzędzia Data Lake Tools for Visual Studio wdrożyć pakiet bazy danych u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Narzędzia Data Lake Tools for Visual Studio wdrożyć Kreator pakietu bazy danych u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>Wdrażanie bazy danych U-SQL przy użyciu zestawu SDK

`PackageDeploymentTool.exe` udostępnia programowania i interfejsów z wierszem polecenia, które ułatwiają wdrażanie bazy danych U-SQL. Zestaw SDK jest uwzględniony w [pakietu Nuget zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), lokalizowania na `build/runtime/PackageDeploymentTool.exe`.

[Dowiedz się więcej na temat zestawu SDK oraz sposób konfigurowania potoku ciągłej integracji/ciągłego wdrażania na potrzeby wdrażania bazy danych U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Odwoływać się do projektu bazy danych U-SQL

Projekt U-SQL można odwoływać się do projektu bazy danych U-SQL. Odwołanie ma wpływ na dwóch obciążeń:

- Projekt kompilacji: środowisk bazy danych skonfigurowano pod kątem przed tworzenie skryptów U-SQL. 
- Lokalne uruchamianie względem konta (lokalny projekt): wdrożonych środowiska, w której istnieje odwołanie do konta (lokalny projekt), przed wykonaniem skryptu U-SQL. [Dowiedz się więcej na temat uruchamiania lokalnego i różnica między (Local-machine) i konto (lokalny projekt), w tym miejscu](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>Jak dodać odwołanie do bazy danych U-SQL

1. Kliknij prawym przyciskiem myszy projekt U-SQL w **Eksploratora rozwiązań**i wybierz polecenie **Dodaj odwołanie do bazy danych U-SQL...** .

    ![Narzędzia Data Lake Tools for Visual Studio Dodaj odwołanie do projektu bazy danych](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfiguruj odwołanie do bazy danych z projektu bazy danych U-SQL w bieżącym rozwiązaniu lub plik pakietu bazy danych U-SQL.
3. Podaj nazwę dla bazy danych.

    ![Narzędzia Data Lake Tools for Visual Studio Dodaj kreatora odwołanie do projektu bazy danych](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
