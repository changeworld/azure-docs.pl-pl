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
ms.openlocfilehash: a8178e5ff9ff4816ddd422d3c45cfc0e1e0b3d41
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712981"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Ciągła integracja i wdrażanie dla Azure SQL Data Warehouse

W tym prostym samouczku przedstawiono sposób integrowania projektu bazy danych SQL Server Data Tools (SSDT) z usługą Azure DevOps i korzystania z Azure Pipelines w celu skonfigurowania ciągłej integracji i wdrażania. Ten samouczek to drugi krok tworzenia potoku ciągłej integracji i wdrażania przy użyciu SQL Data Warehouse. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Zapoznaj się z [samouczkiem dotyczącym integracji kontroli źródła](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Konfigurowanie usługi Azure DevOps i nawiązywanie z nią połączenia


## <a name="continuous-integration-with-visual-studio-build"></a>Ciągła integracja z kompilacją programu Visual Studio

1. Przejdź do Azure Pipelines i Utwórz nowy potok kompilacji.

      ![Nowy potok](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nowy potok")

2. Wybierz repozytorium kodu źródłowego (Azure Repos Git) i wybierz szablon aplikacji klasycznej platformy .NET.

      ![Konfiguracja potoku](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Konfiguracja potoku") 

3. Edytuj plik YAML, aby używać odpowiedniej puli agenta. Plik YAML powinien wyglądać następująco:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

W tym momencie istnieje proste środowisko, w którym każde ewidencjonowanie gałęzi głównej repozytorium kontroli źródła powinno automatycznie wyzwolić pomyślną kompilację projektu bazy danych w programie Visual Studio. Sprawdź, czy Automatyzacja działa na zakończenie, wprowadzając zmianę w lokalnym projekcie bazy danych i sprawdzając tę zmianę w gałęzi głównej.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Ciągłe wdrażanie przy użyciu zadania wdrażania Azure SQL Data Warehouse (lub bazy danych)

1. Dodaj nowe zadanie przy użyciu [zadania wdrażania Azure SQL Database](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) i Wypełnij wymagane pola, aby nawiązać połączenie z docelowym magazynem danych. Po uruchomieniu tego zadania DACPAC wygenerowanego przez poprzedni proces kompilacji jest wdrażany w docelowym magazynie danych. Można również użyć [zadania wdrażania Azure SQL Data Warehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment). 

      ![Zadanie wdrażania](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Zadanie wdrażania")

2. Jeśli używasz agenta samoobsługowego, upewnij się, że ustawisz zmienną środowiskową tak, aby używała poprawnego elementu sqlpackage. exe dla SQL Data Warehouse. Ścieżka powinna wyglądać następująco:

      ![Zmienna środowiskowa](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Zmienna środowiskowa")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Uruchom i sprawdź poprawność potoku. Zmiany można wprowadzać lokalnie i zaewidencjonować zmiany w kontroli źródła, które powinny generować automatyczne Kompilowanie i wdrażanie.

## <a name="next-steps"></a>Następne kroki

- Eksploruj [Azure SQL Data Warehouse architekturę](massively-parallel-processing-mpp-architecture.md)
- Szybkie [tworzenie SQL Data Warehouse](create-data-warehouse-portal.md)
- [Ładowanie danych przykładowych](sql-data-warehouse-load-sample-databases.md)
- Eksploruj [wideo](/azure/sql-data-warehouse/sql-data-warehouse-videos)
