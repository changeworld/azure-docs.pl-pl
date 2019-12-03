---
title: Ciągła integracja i ciągłe wdrażanie
description: Środowisko DevOps Database klasy korporacyjnej dla SQL Data Warehouse z wbudowaną obsługą ciągłej integracji i wdrażania przy użyciu Azure Pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e8d7e7764a01dbd0169efae093bac4d984982108
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708671"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Ciągła integracja i wdrażanie dla Azure SQL Data Warehouse

W tym prostym samouczku przedstawiono sposób integrowania projektu bazy danych SQL Server Data Tools (SSDT) z usługą Azure DevOps i korzystania z Azure Pipelines w celu skonfigurowania ciągłej integracji i wdrażania. Ten samouczek to drugi krok tworzenia potoku ciągłej integracji i wdrażania przy użyciu SQL Data Warehouse. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Zapoznaj się z [samouczkiem dotyczącym integracji kontroli źródła](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Konfigurowanie usługi Azure DevOps i nawiązywanie z nią połączenia


## <a name="continuous-integration-with-visual-studio-build"></a>Ciągła integracja z kompilacją programu Visual Studio

1. Przejdź do Azure Pipelines i Utwórz nowy potok kompilacji

      ![Nowy potok](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nowy potok")

2. Wybierz repozytorium kodu źródłowego (Azure Repos Git) i wybierz szablon aplikacji klasycznej platformy .NET.

      ![Konfiguracja potoku](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Konfiguracja potoku") 

3. Edytuj plik YAML, aby używać odpowiedniej puli agenta. Plik YAML powinien wyglądać następująco:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

W tym momencie istnieje proste środowisko, w którym każde ewidencjonowanie gałęzi głównej repozytorium kontroli źródła powinno automatycznie wyzwolić pomyślną kompilację projektu bazy danych w programie Visual Studio. Sprawdź, czy Automatyzacja działa na zakończenie, wprowadzając zmianę w lokalnym projekcie bazy danych i sprawdzając tę zmianę w gałęzi głównej.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Ciągłe wdrażanie przy użyciu zadania wdrażania Azure SQL Data Warehouse (lub bazy danych)

1. Dodaj nowe zadanie przy użyciu [zadania wdrażania Azure SQL Database](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) i Wypełnij wymagane pola, aby nawiązać połączenie z docelowym magazynem danych. Po uruchomieniu tego zadania DACPAC wygenerowanego przez poprzedni proces kompilacji jest wdrażany w docelowym magazynie danych. Można również użyć [zadania wdrożenia usługi Azure SQL DataWarehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment) 

      ![Zadanie wdrażania](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Zadanie wdrażania")

2. Jeśli używasz agenta samoobsługowego, upewnij się, że ustawisz zmienną środowiskową tak, aby używała poprawnego elementu sqlpackage. exe dla SQL Data Warehouse. Ścieżka powinna wyglądać następująco:

      ![Zmienna środowiskowa](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Zmienna środowiskowa")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Uruchom i sprawdź poprawność potoku. Zmiany można wprowadzać lokalnie i zaewidencjonować zmiany w kontroli źródła, które powinny generować automatyczne Kompilowanie i wdrażanie.

## <a name="next-steps"></a>Następne kroki

- Eksploruj [Azure SQL Data Warehouse architekturę](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Szybkie [tworzenie SQL Data Warehouse][create a SQL Data Warehouse]
- [Ładowanie przykładowych danych][load sample data].
- Eksploruj [wideo](/azure/sql-data-warehouse/sql-data-warehouse-videos)



<!--Image references-->

[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Feature requests]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
