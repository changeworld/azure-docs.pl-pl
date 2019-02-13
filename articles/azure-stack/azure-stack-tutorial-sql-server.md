---
title: Udostępnij baz danych SQL dla użytkowników usługi Azure Stack | Dokumentacja firmy Microsoft
description: Samouczek, aby zainstalować dostawcę zasobów programu SQL Server i utworzyć oferuje, umożliwić użytkownikom usługi Azure Stack, tworzenie baz danych SQL.
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: aba9014e74b8ba0cd1b528cf570d3651950c6d5f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207062"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Samouczek: udostępnić baz danych SQL dla użytkowników usługi Azure Stack

Jako administrator chmury Azure Stack można utworzyć oferty, które pozwalają użytkownikom (dzierżawcy) Tworzenie baz danych SQL, których mogą używać ich natywnych aplikacji w chmurze, witryn sieci Web i obciążeń. Dostarczając użytkownikom tych baz danych niestandardowych, na żądanie, oparte na chmurze, można zapisać ich czas i zasoby. Aby skonfigurować tę funkcjonalność, wykonasz następujące czynności:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów programu SQL Server
> * Tworzenie oferty
> * Testowanie oferty

## <a name="deploy-the-sql-server-resource-provider"></a>Wdrażanie dostawcy zasobów programu SQL Server

Proces wdrażania opisano szczegółowo w temacie [używania baz danych SQL w usłudze Azure Stack artykule](azure-stack-sql-resource-provider-deploy.md)i obejmuje następujące podstawowe kroki:

1. [Wdrażanie dostawcy zasobów bazy danych SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Weryfikacja wdrożenia](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Zapewniają pojemność, nawiązując połączenie do hostowania programu SQL server. Aby uzyskać więcej informacji, zobacz [Dodawanie serwerów hosta](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Tworzenie oferty

1.  [Ustawiono limit przydziału](azure-stack-setting-quotas.md) i nadaj mu nazwę *SQLServerQuota*. Wybierz **Microsoft.SQLAdapter** dla **Namespace** pola.
2.  [Utwórz plan](azure-stack-create-plan.md). Nadaj mu nazwę *TestSQLServerPlan*, wybierz opcję **Microsoft.SQLAdapter** usługi, i **SQLServerQuota** limitu przydziału.

    > [!NOTE]
    > Aby umożliwić użytkownikom tworzenie innych aplikacji, innych usług może być konieczne w planie. Na przykład usługa Azure Functions wymaga **Microsoft.Storage** usługi w planie, podczas gdy Wordpress wymaga **Microsoft.MySQLAdapter**.

3.  [Utwórz ofertę](azure-stack-create-offer.md), nadaj jej nazwę **TestSQLServerOffer** i wybierz **TestSQLServerPlan** planu.

## <a name="test-the-offer"></a>Testowanie oferty

Teraz, udało Ci się wdrożyć dostawcy zasobów programu SQL Server i utworzeniu oferty, możesz zalogować się jako użytkownik, Subskrybuj ofertę i utworzyć bazę danych.

### <a name="subscribe-to-the-offer"></a>Subskrybuj ofertę

1. Zaloguj się do portalu usługi Azure Stack (https://portal.local.azurestack.external) jako dzierżawca.
2. Wybierz **Uzyskaj subskrypcję** , a następnie wprowadź **TestSQLServerSubscription** w obszarze **nazwę wyświetlaną**.
3. Wybierz **wybierz ofertę** > **TestSQLServerOffer** > **Utwórz**.
4. Wybierz **wszystkich usług** > **subskrypcje** > **TestSQLServerSubscription** > **zasobów dostawcy**.
5. Wybierz **zarejestrować** obok **Microsoft.SQLAdapter** dostawcy.

### <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

1. Wybierz **+**  >  **dane + magazyn** > **bazy danych SQL**.
2. Zachowaj wartości domyślne lub przy użyciu tych przykładów dla następujących pól:
    - **Nazwa bazy danych**: SQLdb
    - **Maksymalny rozmiar w Megabajtach**: 100
    - **Subskrypcja**: TestSQLOffer
    - **Grupa zasobów**: SQL-RG
3. Wybierz **ustawienia logowania**, wprowadź poświadczenia dla bazy danych, a następnie wybierz **OK**.
4. Wybierz **jednostki SKU** > Wybierz jednostkę SKU SQL, który został utworzony dla programu SQL Server obsługującego >, a następnie wybierz **OK**.
5. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów programu SQL Server
> * Tworzenie oferty
> * Testowanie oferty

Przejdź do następnego samouczka, aby dowiedzieć się, jak:

> [!div class="nextstepaction"]
> [Udostępnić w sieci web, mobilnych i aplikacji interfejsu API dla użytkowników]( azure-stack-tutorial-app-service.md)