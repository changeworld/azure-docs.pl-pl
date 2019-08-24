---
title: Integracja kontroli źródła | Microsoft Docs
description: Środowisko DevOps Database klasy korporacyjnej dla SQL Data Warehouse z integracją natywnej kontroli źródła przy użyciu Azure Repos (git i GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: develop
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ec2c65e33e5c07988b01c40d735f2d34c2196087
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014209"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Integracja kontroli źródła dla Azure SQL Data Warehouse

W tym samouczku opisano sposób integrowania projektu bazy danych SQL Server Data Tools (SSDT) z kontrolą źródła.  Integracja kontroli źródła to pierwszy etap tworzenia potoku ciągłej integracji i wdrażania przy użyciu SQL Data Warehouse. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Utwórz konto w [organizacji usługi Azure DevOps](https://azure.microsoft.com/services/devops/)
- Przejdź do samouczka [Tworzenie i łączenie](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Zainstaluj program Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurowanie usługi Azure DevOps i nawiązywanie z nią połączenia

1. W organizacji usługi Azure DevOps Utwórz projekt, który będzie hostować projekt bazy danych SSDT za pośrednictwem repozytorium repozytorium Azure

   ![Utwórz projekt](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Utwórz projekt")

2. Otwórz program Visual Studio i Połącz się z organizacją usługi Azure DevOps i projektem w kroku 1, wybierając pozycję "Zarządzaj połączeniami".

   ![Zarządzanie połączeniami](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Zarządzanie połączeniami")

   ![Połącz](media/sql-data-warehouse-source-control-integration/3-connect.png "Połącz")

3. Klonowanie repozytorium repozytorium platformy Azure z projektu na komputerze lokalnym

   ![Klonowanie repozytorium](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klonowanie repozytorium")

## <a name="create-and-connect-your-project"></a>Tworzenie i łączenie projektu

1. W programie Visual Studio Utwórz nowy projekt SQL Server bazy danych z katalogiem i lokalnym repozytorium Git w **lokalnym sklonowanym repozytorium** .

   ![Utwórz nowy projekt](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Utwórz nowy projekt")  

2. Kliknij prawym przyciskiem myszy pusty program sqlproject i zaimportuj magazyn danych do projektu bazy danych

   ![Importuj projekt](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importuj projekt")  

3. W programie Team Explorer w programie Visual Studio Zatwierdź wszystkie zmiany w lokalnym repozytorium git 

   ![Zatwierdzenie](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Zatwierdzenie")  

4. Teraz, gdy zmiany zostały zatwierdzone lokalnie w sklonowanym repozytorium, zsynchronizuj i wypchnij zmiany do repozytorium repozytorium platformy Azure w projekcie usługi Azure DevOps.

   ![Synchronizacja i wypychanie](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronizacja i wypychanie")

   ![Synchronizuj i wypchnij](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronizuj i wypchnij")  

## <a name="validation"></a>Weryfikacja

1. Sprawdź, czy zmiany zostały wypchnięte do repozytorium platformy Azure przez zaktualizowanie kolumny tabeli w projekcie bazy danych za pomocą programu Visual Studio SQL Server Data Tools (SSDT)

   ![Sprawdź poprawność kolumny aktualizacji](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Sprawdź poprawność kolumny aktualizacji")

2. Zatwierdź i wypchnij zmianę z repozytorium lokalnego do repozytorium Azure

   ![Wypychanie zmian](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wypychanie zmian")

3. Sprawdź, czy zmiana została wypchnięcia w repozytorium repozytorium platformy Azure

   ![Sprawdź](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Weryfikuj zmiany")

4. (**Opcjonalnie**) Użyj porównania schematów i zaktualizuj zmiany w docelowym hurtowni danych przy użyciu SSDT, aby upewnić się, że definicje obiektów w repozytorium Azure Repository i repozytorium lokalnym odzwierciedlają magazyn danych

## <a name="next-steps"></a>Następne kroki

- [Programowanie dla Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

