---
title: Udostępnić użytkownikom Azure stosu baz danych SQL | Dokumentacja firmy Microsoft
description: Samouczek, aby zainstalować dostawcę zasobów programu SQL Server i utworzyć oferuje które zezwala użytkownikom Azure stosu na tworzenie baz danych.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: b9ba2bb89bb0d7e16a28a165cf14530a7a10f71b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234754"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Samouczek: udostępnić baz danych użytkowników Azure stosu

Jako administrator chmury Azure stosu tworzenia oferty, które pozwalają użytkownikom (dzierżawcami) Utwórz bazy danych SQL, które mogą używać z ich chmury natywnych aplikacji, witryn sieci Web i obciążeń. Zapewniając użytkownikom tych baz danych niestandardowych, na żądanie, oparte na chmurze, można je zapisać czasu i zasobów. Aby to skonfigurować, obejmują:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów programu SQL Server
> * Tworzenie oferty
> * Testowanie oferty

## <a name="deploy-the-sql-server-resource-provider"></a>Wdrażanie dostawcy zasobów programu SQL Server

Proces wdrażania opisano szczegółowo w [użycia baz danych w artykule stosu Azure](azure-stack-sql-resource-provider-deploy.md)i zawiera następujące podstawowe kroki:

1. [Wdrażanie dostawcy zasobów SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Weryfikacja wdrożenia](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Zapewniają pojemność, łącząc się z serwerem SQL hostingu. Aby uzyskać więcej informacji, zobacz [Dodaj serwerów hosta](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Tworzenie oferty

1.  [Ustaw limit przydziału](azure-stack-setting-quotas.md) i nadaj mu nazwę *SQLServerQuota*. Wybierz **Microsoft.SQLAdapter** dla **Namespace** pola.
2.  [Tworzenie planu](azure-stack-create-plan.md). Nadaj mu nazwę *TestSQLServerPlan*, wybierz pozycję **Microsoft.SQLAdapter** usługi, i **SQLServerQuota** przydziału.

    > [!NOTE]
    > Aby zezwolić użytkownikom na tworzenie inne aplikacje, innych usług mogą być wymagane w planie. Na przykład wymaga usługi Azure Functions **Microsoft.Storage** usługi w planie, podczas gdy Wordpress wymaga **Microsoft.MySQLAdapter**.

3.  [Utwórz ofertę](azure-stack-create-offer.md), nadaj jej nazwę **TestSQLServerOffer** i wybierz **TestSQLServerPlan** planu.

## <a name="test-the-offer"></a>Testowanie oferty

Wdrożeniu dostawcy zasobów programu SQL Server, a następnie utworzyć ofertę, może zalogować się jako użytkownik, subskrybować ofertę i utworzyć bazę danych.

### <a name="subscribe-to-the-offer"></a>Subskrybuj oferty

1. Zaloguj się do portalu Azure stosu (https://portal.local.azurestack.external) dzierżawcy.
2. Wybierz **uzyskania subskrypcji** , a następnie wprowadź **TestSQLServerSubscription** w obszarze **Nazwa wyświetlana**.
3. Wybierz **wybierz ofertę** > **TestSQLServerOffer** > **Utwórz**.
4. Wybierz **więcej usług** > **subskrypcje** > **TestSQLServerSubscription** > **zasobów dostawcy**.
5. Wybierz **zarejestrować** obok **Microsoft.SQLAdapter** dostawcy.

### <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

1. Wybierz **+**  >  **dane i magazyn** > **bazy danych SQL**.
2. Zachowaj wartości domyślne lub użyć tych przykładów dla następujących pól:
    - **Nazwa bazy danych**: SQLdb
    - **Maksymalny rozmiar w MB**: 100
    - **Subskrypcja**: TestSQLOffer
    - **Grupa zasobów**: zarządcy zasobów SQL
3. Wybierz **ustawienia logowania**, wprowadź poświadczenia dla bazy danych, a następnie wybierz **OK**.
4. Wybierz **SKU** > Wybierz jednostki SKU SQL utworzony dla programu SQL Server obsługującego >, a następnie wybierz **OK**.
5. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów programu SQL Server
> * Tworzenie oferty
> * Testowanie oferty

ADVANCE do samouczka dalej, aby dowiedzieć się, jak:

> [!div class="nextstepaction"]
> [Udostępnić aplikacje interfejsu API, mobilne i sieci web dla użytkowników]( azure-stack-tutorial-app-service.md)