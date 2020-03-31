---
title: Ponowne rozmieszczenie pakietów SSIS do pojedynczej bazy danych SQL
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak migrować lub ponownie wdrożyć pakiety i projekty usług SQL Server Integration Services do pojedynczej bazy danych usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure i Asystenta migracji danych.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648533"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Ponowne wdrożenie pakietów SSIS do bazy danych SQL usługi Azure za pomocą usługi migracji bazy danych Azure

Jeśli używasz usług SQL Server Integration Services (SSIS) i chcesz przeprowadzić migrację projektów/pakietów SSIS ze źródła SSISDB hostowanego przez program SQL Server do docelowej bazy danych SSISDB hostowanego przez usługę Azure SQL Database, można je ponownie wdrożyć za pomocą Kreatora wdrażania usług integracji. Kreatora można uruchomić z poziomu programu SQL Server Management Studio (SSMS).

Jeśli używana wersja SSIS jest wcześniejsza niż 2012, przed przeniesieniem projektów/pakietów SSIS do modelu wdrażania projektu, należy najpierw przekonwertować je za pomocą Integration Services Project Conversion Wizard, który można również uruchamiać z usługi SSMS. Aby uzyskać więcej informacji, zobacz artykuł [Konwertowanie projektów na model wdrażania projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Usługa migracji bazy danych azure (DMS) obecnie nie obsługuje migracji źródła SSISDB do serwera bazy danych SQL azure, ale można ponownie wdrożyć projekty/pakiety SSIS przy użyciu następującego procesu.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Oceń źródła projektów/pakietów SSIS.
> * Migrowanie projektów/pakietów SSIS na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące kroki, potrzebujesz:

* SSMS w wersji 17.2 lub nowszej.
* Wystąpienie docelowego serwera bazy danych do hostowania SSISDB. Jeśli jeszcze go nie masz, utwórz serwer bazy danych SQL Azure (bez bazy danych) przy użyciu portalu Azure, przechodząc do [formularza](https://ms.portal.azure.com/#create/Microsoft.SQLServer)programu SQL Server (tylko serwer logiczny).
* SSIS musi być aprowizowana w usłudze Azure Data Factory (ADF) zawierająca środowisko uruchomieniowe integracji platformy Azure-SSIS (IR) z docelowym usługą SSISDB hostowanym przez wystąpienie serwera bazy danych SQL platformy Azure (zgodnie z opisem w artykule [Aprowizj środowisko wykonawcze integracji platformy Azure-SSIS w usłudze Azure Data Factory).](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

## <a name="assess-source-ssis-projectspackages"></a>Ocena źródłowych projektów/pakietów SSIS

Podczas oceny źródła SSISDB nie jest jeszcze zintegrowany z Asystentem migracji bazy danych (DMA) lub usługi migracji bazy danych Azure Database (DMS), projekty/pakiety SSIS zostaną ocenione/zweryfikowane, ponieważ są one ponownie rozmieszczane do docelowego pliku SSISDB hostowanego na serwerze bazy danych SQL azure.

## <a name="migrate-ssis-projectspackages"></a>Migrowanie projektów/pakietów SSIS

Aby przeprowadzić migrację projektów/pakietów SSIS na serwer bazy danych SQL platformy Azure, wykonaj następujące kroki.

1. Otwórz usługę SSMS, a następnie wybierz pozycję **Opcje,** aby wyświetlić okno dialogowe **Połącz z serwerem.**

2. Na karcie **Logowanie** określ informacje niezbędne do nawiązania połączenia z serwerem bazy danych SQL platformy Azure, który będzie obsługiwał docelową bazę danych SSISDB.

    ![Karta Logowania do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na karcie **Właściwości połączenia** w polu tekstowym **Połącz z bazą danych** zaznacz lub wprowadź pozycję **SSISDB**, a następnie wybierz pozycję **Połącz**.

    ![Karta Właściwości połączenia SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. W Eksploratorze obiektów SSMS rozwiń węzeł **Katalogi usług integracji,** rozwiń węzeł **SSISDB**, a jeśli nie ma istniejących folderów, kliknij prawym przyciskiem myszy **SSISDB** i utwórz nowy folder.

5. W obszarze **SSISDB**rozwiń dowolny folder, kliknij prawym przyciskiem myszy **pozycję Projekty**, a następnie wybierz polecenie **Wdrażanie projektu**.

    ![Węzeł SSIS SSISDB rozwinięty](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. W Kreatorze wdrażania usług integracyjnych na stronie **Wprowadzenie** przejrzyj informacje, a następnie wybierz pozycję **Dalej**.

    ![Strona Wprowadzenie kreatora wdrażania](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na stronie **Wybierz źródło** określ istniejący projekt SSIS, który chcesz wdrożyć.

    Jeśli usługa SSMS jest również połączona z serwerem SQL Host hostującym źródło SSISDB, wybierz **katalog Integration Services**, a następnie wprowadź nazwę serwera i ścieżkę projektu w katalogu, aby bezpośrednio wdrożyć projekt.

    Alternatywnie wybierz **plik wdrożenia projektu**, a następnie określ ścieżkę do istniejącego pliku wdrażania projektu (.ispac), aby wdrożyć projekt.

    ![Kreator wdrażania Wybierz stronę Źródło](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Wybierz **pozycję Dalej**.
9. Na stronie **Wybierz miejsce docelowe** określ miejsce docelowe dla projektu.

    a. W polu tekstowym Nazwa serwera wprowadź w pełni kwalifikowaną nazwę serwera usługi Azure SQL Database (<server_name>.database.windows.net).

    b. Podaj informacje uwierzytelniania, a następnie wybierz pozycję **Połącz**.

    ![Kreator wdrażania Wybierz stronę miejsce docelowe](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    d. Wybierz **pozycję Przeglądaj,** aby określić folder docelowy w SSISDB, a następnie wybierz pozycję **Dalej**.

    > [!NOTE]
    > Przycisk **Dalej** jest włączony dopiero po wybraniu opcji **Połącz**.

10. Na stronie **Sprawdzanie poprawności** wyświetl wszelkie błędy/ostrzeżenia, a następnie w razie potrzeby odpowiednio zmodyfikuj pakiety.

    ![Kreator wdrażania Sprawdź poprawność strony](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Wybierz **pozycję Dalej**.

12. Na stronie **Recenzja** przejrzyj ustawienia wdrażania.

    > [!NOTE]
    > Ustawienia można zmienić, wybierając pozycję **Poprzedni** lub wybierając dowolny z łączy kroków w lewym okienku.

13. Wybierz **pozycję Wdrażanie,** aby rozpocząć proces wdrażania.

14. Po zakończeniu procesu wdrażania można wyświetlić wyniki strony, która wyświetla powodzenie lub niepowodzenie każdej akcji wdrażania.
    a. Jeśli jakakolwiek akcja nie powiodła się, w kolumnie **Wynik** wybierz pozycję **Nie można** wyświetlić objaśnienia błędu.
    b. Opcjonalnie wybierz **pozycję Zapisz raport,** aby zapisać wyniki w pliku XML.

15. Wybierz **zamknij,** aby zakończyć Pracę Wdrażania Usług integracyjnych.

Jeśli wdrożenie projektu powiedzie się bez niepowodzenia, można wybrać wszystkie pakiety, które zawiera do uruchomienia na platformie Azure-SSIS IR.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze wskazówkami dotyczącymi migracji w [Przewodniku migracji bazy danych firmy](https://datamigration.microsoft.com/)Microsoft .
