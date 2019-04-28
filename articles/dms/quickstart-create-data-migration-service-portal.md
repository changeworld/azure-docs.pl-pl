---
title: 'Szybki start: Tworzenie wystąpienia usługi Azure Database Migration Service przy użyciu witryny Azure Portal | Microsoft Docs'
description: Używanie witryny Azure Portal do tworzenia wystąpienia usługi Azure Database Migration Service
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/12/2019
ms.openlocfilehash: af5ffdb1c1f030c2bbc0616d027c06b59f1a34de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767787"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Szybki start: Tworzenie wystąpienia usługi Azure Database Migration Service przy użyciu witryny Azure Portal
W tym przewodniku Szybki start utworzysz wystąpienie usługi Azure Database Migration Service przy użyciu witryny Azure Portal.  Po utworzeniu usługi możesz jej użyć do migracji danych z lokalnego programu SQL Server do bazy danych Azure SQL Database.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
Otwórz przeglądarkę internetową, przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/), a następnie wprowadź swoje poświadczenia, aby zalogować się w portalu.

Widok domyślny to pulpit nawigacyjny usług.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów
Przed utworzeniem pierwszego wystąpienia usługi Database Migration Service zarejestruj dostawcę zasobów Microsoft.DataMigration.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Tworzenie wystąpienia usługi
1. Wybierz pozycję +**Utwórz zasób**, aby utworzyć wystąpienie usługi Azure Database Migration Service.

2. Wyszukaj w witrynie Marketplace hasło „migration”, wybierz pozycję **Azure Database Migration Service**, a następnie na ekranie **Azure Database Migration Service** wybierz pozycję **Utwórz**.

3. Na ekranie **Tworzenie usługi migracji**: 

    - Wybierz wartość **Nazwa usługi**, która jest łatwa do zapamiętania i unikatowo identyfikuje Twoje wystąpienie usługi Azure Database Migration Service.
    - Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć wystąpienie.
    - Wybierz istniejącą **grupę zasobów** lub utwórz nową.
    - Wybierz pozycję **Lokalizacja** położoną najbliżej Twojego serwera źródłowego lub docelowego.
    - Wybierz istniejącą **sieć wirtualną** lub utwórz nową.

        Sieć wirtualna zapewnia usłudze Azure Database Migration Service dostęp do źródłowej bazy danych i środowiska docelowego.

        Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://aka.ms/vnet).

    - Wybierz ustawienie Podstawowa: 1 rdzeń wirtualny dla pozycji **Warstwa cenowa**.

        ![Tworzenie usługi migracji](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Wybierz pozycję **Utwórz**.

    Po kilku chwilach wystąpienie usługi Azure Database Migration Service zostanie utworzone i będzie gotowe do użycia. Usługa Database Migration Service jest wyświetlana tak jak pokazano na poniższej ilustracji:

    ![Utworzono usługę migracji](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Możesz wyczyścić zasoby utworzone w tym przewodniku Szybki start, usuwając [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md). Aby usunąć grupę zasobów, przejdź do utworzonego wystąpienia usługi Azure Database Migration Service. Wybierz nazwę w obszarze **Grupa zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**. Ta akcja spowoduje usunięcie wszystkich zasobów w grupie zasobów, a także usunięcie samej grupy.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Migrowanie lokalnego programu SQL Server do usługi Azure SQL Database](tutorial-sql-server-to-azure-sql.md)