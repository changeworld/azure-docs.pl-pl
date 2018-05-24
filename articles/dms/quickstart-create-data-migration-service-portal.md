---
title: Tworzenie wystąpienia usługi Azure Database Migration Service przy użyciu witryny Azure Portal | Microsoft Docs
description: Używanie witryny Azure Portal do tworzenia wystąpienia usługi Azure Database Migration Service
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/01/2018
ms.openlocfilehash: 7669ee678f4049c938c6e249c26997d993fd7c6a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Tworzenie wystąpienia usługi Azure Database Migration Service przy użyciu witryny Azure Portal
W tym przewodniku Szybki start utworzysz wystąpienie usługi Azure Database Migration Service przy użyciu witryny Azure Portal.  Po utworzeniu usługi możesz jej użyć do migracji danych z lokalnego programu SQL Server do bazy danych Azure SQL.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
Otwórz przeglądarkę internetową, przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/), a następnie wprowadź swoje poświadczenia, aby zalogować się w portalu.

Widok domyślny to pulpit nawigacyjny usług.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów
Przed utworzeniem pierwszego wystąpienia usługi Database Migration Service zarejestruj dostawcę zasobów Microsoft.DataMigration.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

3. Wyszukaj pozycję dotyczącą migracji, a następnie po prawej stronie pozycji Microsoft.DataMigration wybierz pozycję **Zarejestruj**.

![Rejestrowanie dostawcy zasobów](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Tworzenie wystąpienia usługi
1. Kliknij pozycję **+ Utwórz zasób**, aby utworzyć wystąpienie usługi Azure Database Migration Service.

2. Wyszukaj w witrynie Marketplace hasło „migration”, wybierz pozycję **Azure Database Migration Service**, a następnie na ekranie **Azure Database Migration Service** kliknij pozycję **Utwórz**.

3. Na ekranie **Database Migration Service**: 

    - Wybierz wartość **Nazwa usługi**, która jest łatwa do zapamiętania i unikatowo identyfikuje Twoje wystąpienie usługi Azure Database Migration Service.
    - Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć wystąpienie.
    - Utwórz nową pozycję **Sieć** o unikatowej nazwie.
    - Wybierz pozycję **Lokalizacja** położoną najbliżej Twojego serwera źródłowego lub docelowego.
    - Wybierz opcję Podstawowa: 1 rdzeń wirtualny dla pozycji **Warstwa cenowa**.

    ![Tworzenie usługi migracji](media/quickstart-create-data-migration-service-portal/dms-create-service.png)
4. Wybierz pozycję **Utwórz**.

Po kilku chwilach wystąpienie usługi Azure Database Migration Service zostanie utworzone i będzie gotowe do użycia. Usługa Database Migration Service jest wyświetlana tak jak pokazano na poniższej ilustracji:

![Utworzono usługę migracji](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Możesz wyczyścić zasoby utworzone w tym przewodniku Szybki start, usuwając [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).  Aby usunąć grupę zasobów, przejdź do utworzonego wystąpienia usługi Azure Database Migration Service. Wybierz nazwę w obszarze **Grupa zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**.  Ta akcja spowoduje usunięcie wszystkich zasobów w grupie zasobów, a także usunięcie samej grupy.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie lokalnego programu SQL Server do usługi Azure SQL Database](tutorial-sql-server-to-azure-sql.md)