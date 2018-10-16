---
title: Udostępnij sieci web i aplikacji interfejsu API użytkowników usługi Azure Stack | Dokumentacja firmy Microsoft
description: Samouczek, aby zainstalować dostawcę zasobów usługi App Service i utworzyć oferuje, które powodują użytkowników usługi Azure Stack zdolność do tworzenia sieci web i aplikacji API apps.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0a9b87ccfd49ba04a8dff8ef48bea023ff94b222
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340733"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Samouczek: udostępnić w sieci web i aplikacje interfejsu API użytkowników usługi Azure Stack

Jako administrator chmury Azure Stack można utworzyć oferty, które pozwalają użytkownikom (dzierżawcy) tworzenie aplikacji usługi Azure Functions i sieci web i interfejsów API. Przy zapewnieniu użytkownikom dostępu do tych aplikacji na żądanie, oparte na chmurze, można je oszczędność czasu i zasobów.

Aby skonfigurować tę funkcjonalność, wykonasz następujące czynności:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów usługi App Service
> * Tworzenie oferty
> * Testowanie oferty

## <a name="deploy-the-app-service-resource-provider"></a>Wdrażanie dostawcy zasobów usługi App Service

1. [Przygotowanie hosta usługi Azure Stack Development Kit](azure-stack-app-service-before-you-get-started.md). W tym wdrażanie dostawcy zasobów programu SQL Server, który jest wymagany do tworzenia niektórych aplikacji.
2. [Pobieranie skryptów Instalatora i Pomocnika](azure-stack-app-service-deploy.md).
3. [Uruchom skrypt pomocnika, aby utworzyć wymagane certyfikaty](azure-stack-app-service-deploy.md).
4. [Zainstaluj dostawcę zasobów usługi App Service](azure-stack-app-service-deploy.md) (potrwa kilka godzin, aby zainstalować i dla wszystkich ról procesów roboczych się pojawić.)
5. [Weryfikowania instalacji](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Tworzenie oferty

Na przykład można utworzyć ofertę, która pozwala użytkownikom na tworzenie systemów zarządzania zawartością sieci web DNN. Wymaga usługi SQL Server, który jest już włączona po zainstalowaniu dostawcy zasobów programu SQL Server.

1.  [Ustawiono limit przydziału](azure-stack-setting-quotas.md) i nadaj mu nazwę *AppServiceQuota*. Wybierz **Microsoft.Web** dla **Namespace** pola.
2.  [Utwórz plan](azure-stack-create-plan.md). Nadaj mu nazwę *TestAppServicePlan*, wybierz opcję **Microsoft.SQL** usługi i **limitu przydziału usługi App Service** limitu przydziału.

    > [!NOTE]
    > Aby umożliwić użytkownikom tworzenie innych aplikacji, innych usług może być konieczne w planie. Na przykład usługa Azure Functions wymaga **Microsoft.Storage** usługi w planie, podczas gdy Wordpress wymaga **Microsoft.MySQL**.

3.  [Utwórz ofertę](azure-stack-create-offer.md), nadaj jej nazwę **TestAppServiceOffer** i wybierz **TestAppServicePlan** planu.

## <a name="test-the-offer"></a>Testowanie oferty

Teraz, udało Ci się wdrożyć dostawcy zasobów usługi App Service i utworzeniu oferty, możesz zalogować się jako użytkownik, Subskrybuj ofertę i utworzyć aplikację.

W tym przykładzie utworzymy system zarządzania zawartością witryny platformy DNN. Najpierw utwórz bazę danych SQL, a następnie DNN aplikacji sieci web.

### <a name="subscribe-to-the-offer"></a>Subskrybuj ofertę

1. Zaloguj się do portalu usługi Azure Stack (https://portal.local.azurestack.external) jako dzierżawca.
2. Wybierz **Uzyskaj subskrypcję** >, wprowadź **TestAppServiceSubscription** w obszarze **nazwę wyświetlaną** > **wybierz ofertę**  >  **TestAppServiceOffer** > **tworzenie**.

### <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

1. Wybierz **+**  >  **dane + magazyn** > **bazy danych SQL**.
2. Zachowaj wartości domyślne, z wyjątkiem następujących pól:

    - **Nazwa bazy danych**: DNNdb
    - **Maksymalny rozmiar w Megabajtach**: 100
    - **Subskrypcja**: TestAppServiceOffer
    - **Grupa zasobów**: DNN-RG

3. Wybierz **ustawienia logowania**, wprowadź poświadczenia dla bazy danych, a następnie wybierz **OK**. Te poświadczenia będą używane w dalszej części tego samouczka.
4. W obszarze **jednostki SKU** > Wybierz jednostkę SKU SQL, który został utworzony dla programu SQL Server obsługującego >, a następnie wybierz **OK**.
5. Wybierz pozycję **Utwórz**.

### <a name="create-a-dnn-app"></a>Tworzenie aplikacji DNN

1. Wybierz **+**  >  **holograficznych** > **witryny platformy DNN w wersji zapoznawczej** > **Utwórz** .
2. Wprowadź *DNNapp* w obszarze **nazwy aplikacji** i wybierz **TestAppServiceOffer** w obszarze **subskrypcji**.
3. Wybierz **Skonfiguruj wymagane ustawienia** > **Utwórz nowy** > Wprowadzenie **planu usługi App Service** nazwy.
4. Wybierz **warstwa cenowa** > **bezpłatna F1** > **wybierz** > **OK**.
5. Wybierz **bazy danych** i wprowadź poświadczenia dla bazy danych SQL została utworzona wcześniej.
6. Wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie dostawcy zasobów usługi App Service
> * Tworzenie oferty
> * Testowanie oferty

Przejdź do następnego samouczka, aby dowiedzieć się, jak:

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji na platformie Azure i usługi Azure Stack](user/azure-stack-solution-pipeline.md)
