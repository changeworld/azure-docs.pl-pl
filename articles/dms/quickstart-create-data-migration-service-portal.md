---
title: 'Szybki Start: Tworzenie wystąpienia Azure Database Migration Service przy użyciu Azure Portal | Microsoft Docs'
description: Użyj Azure Portal, aby utworzyć wystąpienie Azure Database Migration Service
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/05/2019
ms.openlocfilehash: 6427de61ebf30a5e4a62070becd1cc1f750f6c1b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571603"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Szybki start: tworzenie wystąpienia usługi Azure Database Migration Service przy użyciu witryny Azure Portal

W tym przewodniku szybki start utworzysz wystąpienie Azure Database Migration Service za pomocą Azure Portal.  Po utworzeniu usługi można jej użyć do migrowania danych z SQL Server lokalnych do Azure SQL Database.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową, przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/), a następnie wprowadź swoje poświadczenia, aby zalogować się w portalu.

Widok domyślny to pulpit nawigacyjny usług.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Przed utworzeniem pierwszego wystąpienia usługi Database Migration Service zarejestruj dostawcę zasobów Microsoft.DataMigration.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Tworzenie wystąpienia usługi

1. Wybierz pozycję +**Utwórz zasób** , aby utworzyć wystąpienie Azure Database Migration Service.

2. Wyszukaj w witrynie Marketplace hasło „migration”, wybierz pozycję **Azure Database Migration Service**, a następnie na ekranie **Azure Database Migration Service** wybierz pozycję **Utwórz**.

3. Na ekranie **Tworzenie usługi migracji**:

    - Wybierz **nazwę usługi** , która jest dopamiętana i unikatowa, aby zidentyfikować wystąpienie Azure Database Migration Service.
    - Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć wystąpienie.
    - Wybierz istniejącą **grupę zasobów** lub utwórz nową.
    - Wybierz pozycję **Lokalizacja** położoną najbliżej Twojego serwera źródłowego lub docelowego.
    - Wybierz istniejącą **sieć wirtualną** lub utwórz ją.

        Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowej bazy danych i środowiska docelowego.

        Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu Azure Portal](https://aka.ms/vnet).

    - Wybierz opcję Podstawowa: 1 rdzeń wirtualny dla pozycji **Warstwa cenowa**.

        ![Tworzenie usługi migracji](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Wybierz pozycję **Utwórz**.

    Po kilku chwilach wystąpienie usługi Azure Database Migration Service jest tworzone i gotowe do użycia. Azure Database Migration Service wyświetlane jak pokazano na poniższej ilustracji:

    ![Utworzono usługę migracji](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz wyczyścić zasoby utworzone w tym przewodniku Szybki start, usuwając [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md). Aby usunąć grupę zasobów, przejdź do utworzonego wystąpienia usługi Azure Database Migration Service. Wybierz nazwę w obszarze **Grupa zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**. Ta akcja spowoduje usunięcie wszystkich zasobów w grupie zasobów, a także usunięcie samej grupy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie lokalnego programu SQL Server do usługi Azure SQL Database](tutorial-sql-server-to-azure-sql.md)