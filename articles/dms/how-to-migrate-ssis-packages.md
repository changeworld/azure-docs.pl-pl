---
title: Ponowne wdrażanie pakietów usług SQL Server Integration Services do usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację pakietów usług SQL Server Integration Services do usługi Azure SQL Database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080634"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>Ponowne wdrażanie pakietów usług SQL Server Integration Services do usługi Azure SQL Database

Jeśli używasz programu SQL Server Integration Services (SSIS) i chcesz przeprowadzić migrację projektów SSIS/pakietów ze źródła danych SSISDB hostowaną przez program SQL Server do miejsca docelowego danych SSISDB hostowaną przez usługę Azure SQL Database, możesz ponownie wdrożyć je przy użyciu wdrożenia usług integracji Kreator. Można uruchomić kreatora z w ramach programu SQL Server Management Studio (SSMS).

Jeśli wersja korzystasz z usług SSIS jest wcześniejszą niż 2012, przed ponowne wdrożenie usługi SSIS projektów/pakietów do modelu wdrażania przy użyciu projektu, najpierw musisz konwertować je za pomocą integracji usług projektu konwersji kreatora, w którym można także uruchomić z programu SSMS. Aby uzyskać więcej informacji, zobacz artykuł [konwertowanie projektów do modelu wdrażania projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) aktualnie nie obsługuje migracji źródła SSISDB do serwera usługi Azure SQL Database, ale można ponownie wdrożyć swoje projektów SSIS/pakietów przy użyciu poniższego procesu.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Oceń źródła projektów SSIS/pakietów.
> * Migrowanie projektów SSIS/pakietów na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* SSMS wersji 17.2 lub nowszej.
* Wystąpienie bazy danych serwera docelowego do hostowania bazy SSISDB. Jeśli nie masz jeszcze jeden, tworzenie serwera usługi Azure SQL Database (bez bazy danych) w witrynie Azure portal, przechodząc do programu SQL Server (ltylko serwer logiczny) [formularza](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
* SSIS musi być obsługiwana w usłudze Azure Data Factory (ADF) zawierający środowiska Azure-SSIS Integration Runtime (IR) z lokalizacją docelową bazą danych SSISDB hostowaną przez wystąpienie serwera Azure SQL Database (zgodnie z opisem w artykule [obsługi administracyjnej integracji usług SSIS na platformie Azure Środowisko uruchomieniowe w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Ocena projektów SSIS/pakietów źródła

Podczas oceny źródłowej bazy danych SSISDB nie jest jeszcze zintegrowana narzędzia Database Migration Assistant (DMA) lub usługi Azure Database Migration Service (DMS), projektów SSIS/pakietów zostanie ocenione/zweryfikowana, ponieważ są one ponownie wdrażana do docelowej bazy danych SSISDB w serwisie Serwer bazy danych SQL Azure.

## <a name="migrate-ssis-projectspackages"></a>Migrowanie projektów SSIS/pakietów

Aby przeprowadzić migrację projektów SSIS/pakietów do serwera Azure SQL Database, wykonaj następujące czynności.

1. Otwórz program SSMS, a następnie wybierz **opcje** do wyświetlenia **Połącz z serwerem** okno dialogowe.

2. Na **logowania** karcie, określ informacje niezbędne do łączenia się z serwerem usługi Azure SQL Database, który będzie hostował docelowej bazy danych SSISDB.

    ![Karta logowania usług SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na **właściwości połączenia** na karcie **Połącz z bazą danych** pola tekstowego, wybierz lub wprowadź **SSISDB**, a następnie wybierz pozycję **Connect**.

    ![Karta właściwości połączenia usług SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. W Eksploratorze obiektów programu SSMS, rozwiń **Integracja usług katalogów** węzła, rozwiń węzeł **SSISDB**i jeśli nie ma żadnych istniejących folderów, kliknij prawym przyciskiem myszy **SSISDB** i Utwórz nowy folder.

5. W obszarze **SSISDB**rozwiń dowolnego folderu, kliknij prawym przyciskiem myszy **projektów**, a następnie wybierz pozycję **wdrażanie projektu**.

    ![Węzeł bazy danych SSISDB SSIS rozwinięte](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. W Kreatorze wdrażania usług integracji na **wprowadzenie** strony, przejrzyj informacje, a następnie wybierz pozycję **dalej**.

    ![Strona wprowadzenia Kreatora wdrażania](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na **wybierz źródło** Określ istniejący projekt usług SSIS, który chcesz wdrożyć.

    Jeśli program SSMS również jest podłączony do serwera SQL hostującego źródłowej bazy danych SSISDB, wybierz opcję **wykaz usług Integration Services**, a następnie wprowadź ścieżkę serwera nazwę i Projekt w katalogu wdrażania projektu bezpośrednio.

    Alternatywnie zaznacz **pliku wdrażania projektu**, a następnie określ ścieżkę do istniejącego projektu wdrażania pliku (.ispac) do wdrażania projektu.

    ![Strona kreatora wybierz źródło wdrożenia](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Wybierz opcję **Dalej**.
9. Na **wybierz lokalizację docelową** Określ lokalizację docelową dla projektu.

    a. W polu tekstowym Nazwa serwera, wpisz w pełni kwalifikowana nazwa serwera usługi Azure SQL Database (< nazwa_serwera >. database.windows.net).

    b. Podaj informacje dotyczące uwierzytelniania, a następnie wybierz pozycję **Connect**.

    ![Strona kreatora wybierz lokalizację docelową wdrożenia](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Wybierz **Przeglądaj** do określania folderu docelowego w SSISDB, a następnie wybierz **dalej**.

    > [!NOTE]
    > **Dalej** przycisk jest aktywny tylko wtedy, gdy wybrano **Connect**.

10. Na **weryfikacji** się wyświetlić wszelkie błędy/ostrzeżenia, a następnie w razie potrzeby pakietów odpowiednio zmodyfikować.

    ![Strona kreatora sprawdzania poprawności wdrożenia](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Wybierz opcję **Dalej**.

12. Na **Przejrzyj** Przejrzyj ustawienia wdrożenia.

    > [!NOTE]
    > Ustawienia można zmienić, wybierając **Wstecz** lub wybierając dowolny z linków krok w okienku po lewej stronie.

13. Wybierz **Wdróż** do rozpoczęcia procesu wdrażania.

14. Po ukończeniu procesu wdrażania można wyświetlić strony wyników Wyświetla powodzenie lub niepowodzenie akcji każdego wdrożenia.
    a. Jeśli wszystkie akcja nie powiodła się, w **wynik** wybierz opcję **nie powiodło się** Aby wyświetlić wyjaśnienie błędu.
    b. Opcjonalnie można zaznaczyć **Zapisz raport** zapisać wyniki w pliku XML.

15. Wybierz **Zamknij** aby zakończyć działanie Kreatora wdrażania usługi integracyjnego.

Jeśli wdrożenie projektu zakończy się pomyślnie bez błędów, można wybrać dowolnego pakietu, zawartych w nim do uruchamiania na usługi platformy Azure-SSIS IR.

## <a name="next-steps"></a>Kolejne kroki

* Przejrzyj wskazówki dotyczące migracji w programie Microsoft [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/).
