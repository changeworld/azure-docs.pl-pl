---
title: Udostępnić w sieci web i aplikacji API apps użytkownikom stosu Azure | Dokumentacja firmy Microsoft
description: Samouczek, aby zainstalować dostawcę zasobów usługi aplikacji i utworzyć oferuje który zapewnić użytkownikom stosu Azure możliwość tworzenia sieci web i aplikacji API apps.
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
ms.openlocfilehash: 0171dba639e480a04cdd1c7f23d546d01121fb42
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247402"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Samouczek: udostępnić w sieci web i aplikacji API apps użytkownikom Azure stosu

Jako administrator chmury Azure stosu tworzenia oferty, które pozwalają użytkownikom (dzierżawcami), tworzenie aplikacji usługi Azure Functions i sieci web i interfejsu API. Przez przyznawanie użytkownikom dostępu do tych aplikacji na żądanie, oparte na chmurze, można je zapisać czasu i zasobów.

Aby to skonfigurować, obejmują:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów usługi aplikacji
> * Tworzenie oferty
> * Testowanie oferty

## <a name="deploy-the-app-service-resource-provider"></a>Wdrażanie dostawcy zasobów usługi aplikacji

1. [Przygotowywanie hostów Azure stosu Development Kit](azure-stack-app-service-before-you-get-started.md). W tym wdrażanie dostawcy zasobów programu SQL Server, który jest wymagany do tworzenia niektórych aplikacji.
2. [Pobierz skrypty Instalatora i Pomocnika](azure-stack-app-service-deploy.md).
3. [Uruchom skrypt pomocnika do tworzenia wymaganych certyfikatów](azure-stack-app-service-deploy.md).
4. [Zainstaluj dostawcę zasobów usługi aplikacji](azure-stack-app-service-deploy.md) (potrwa kilka godzin, aby zainstalować i dla wszystkich ról procesu roboczego się pojawić.)
5. [Sprawdź poprawność instalacji](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Tworzenie oferty

Na przykład można utworzyć ofertę, która umożliwia użytkownikom tworzenie systemów zarządzania zawartością sieci web DNN. Wymaga usługi SQL Server, który jest już włączony, instalując dostawcy zasobów programu SQL Server.

1.  [Ustaw limit przydziału](azure-stack-setting-quotas.md) i nadaj mu nazwę *AppServiceQuota*. Wybierz **Microsoft.Web** dla **Namespace** pola.
2.  [Tworzenie planu](azure-stack-create-plan.md). Nadaj mu nazwę *TestAppServicePlan*, wybierz pozycję **Microsoft.SQL** usługi i **przydziału AppService** przydziału.

    > [!NOTE]
    > Aby zezwolić użytkownikom na tworzenie inne aplikacje, innych usług mogą być wymagane w planie. Na przykład wymaga usługi Azure Functions **Microsoft.Storage** usługi w planie, podczas gdy Wordpress wymaga **Microsoft.MySQL**.

3.  [Utwórz ofertę](azure-stack-create-offer.md), nadaj jej nazwę **TestAppServiceOffer** i wybierz **TestAppServicePlan** planu.

## <a name="test-the-offer"></a>Testowanie oferty

Po wdrożeniu dostawcy zasobów usługi aplikacji i utworzyć ofertę, może zalogować się jako użytkownik, subskrybować oferty i tworzenie aplikacji.

Na przykład utworzymy system zarządzania zawartością DNN platformy. Najpierw należy utworzyć bazę danych SQL, a następnie DNN aplikacji sieci web.

### <a name="subscribe-to-the-offer"></a>Subskrybuj oferty

1. Zaloguj się do portalu Azure stosu (https://portal.local.azurestack.external) dzierżawcy.
2. Wybierz **uzyskania subskrypcji** >, wprowadź **TestAppServiceSubscription** w obszarze **Nazwa wyświetlana** > **wybierz ofertę**  >  **TestAppServiceOffer** > **utworzyć**.

### <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

1. Wybierz **+**  >  **dane i magazyn** > **bazy danych SQL**.
2. Zachowaj wartości domyślne, z wyjątkiem następujących pól:

    - **Nazwa bazy danych**: DNNdb
    - **Maksymalny rozmiar w MB**: 100
    - **Subskrypcja**: TestAppServiceOffer
    - **Grupa zasobów**: zarządcy zasobów DNN

3. Wybierz **ustawienia logowania**, wprowadź poświadczenia dla bazy danych, a następnie wybierz **OK**. Te poświadczenia będą używane w dalszej części tego samouczka.
4. W obszarze **SKU** > Wybierz jednostki SKU SQL utworzony dla programu SQL Server obsługującego >, a następnie wybierz **OK**.
5. Wybierz pozycję **Utwórz**.

### <a name="create-a-dnn-app"></a>Tworzenie aplikacji DNN

1. Wybierz **+**  >  **zobaczyć wszystkie** > **DNN platformy w wersji zapoznawczej** > **Utwórz** .
2. Wprowadź *DNNapp* w obszarze **Nazwa aplikacji** i wybierz **TestAppServiceOffer** w obszarze **subskrypcji**.
3. Wybierz **Skonfiguruj wymagane ustawienia** > **Utwórz nowy** > Wprowadzenie **planu usługi aplikacji** nazwy.
4. Wybierz **warstwa cenowa** > **F1 bezpłatna** > **wybierz** > **OK**.
5. Wybierz **bazy danych** , a następnie wprowadź poświadczenia dla bazy danych SQL utworzony wcześniej.
6. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów usługi aplikacji
> * Tworzenie oferty
> * Testowanie oferty

ADVANCE do samouczka dalej, aby dowiedzieć się, jak:

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji na platformie Azure oraz Azure stosu](user/azure-stack-solution-pipeline.md)
