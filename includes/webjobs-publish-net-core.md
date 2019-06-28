---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183390"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W **Publikuj** okno dialogowe, wybierz opcję **Microsoft Azure App Service**, wybierz **Utwórz nowy**, a następnie wybierz pozycję **Publikuj**.

   ![Pobranie publikowania docelowej](./media/webjobs-publish-netcore/pick-publish-target.png)

1. W **Tworzenie usługi App Service** okno dialogowe, użyj ustawień hostingu określonych w tabeli znajdującej się poniżej obrazu:

    ![Okno dialogowe Tworzenie usługi App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. |
    | **Subskrypcja** | Wybierz subskrypcję | Subskrypcja platformy Azure, która ma być używana. |
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nazwa grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz pozycję **Nowa**, aby utworzyć nową grupę zasobów.|
    | **[Hosting Plan](../articles/app-service/overview-hosting-plans.md)** | Plan usługi App Service | [Plan usługi App Service](../articles/app-service/overview-hosting-plans.md) określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która jest hostem aplikacji. Hostując wiele aplikacji, można zmniejszyć koszty przez skonfigurowanie aplikacji internetowych do korzystania z jednego planu usługi App Service. Plany usługi App Service definiują regionu, rozmiaru wystąpienia, wartość licznika skali i jednostki SKU (bezpłatna, współdzielona, podstawowa, standardowa lub Premium). Wybierz **New** Aby utworzyć nowy plan usługi App Service. |

1. Kliknij przycisk **Utwórz** Tworzenie zadania WebJob i powiązane zasoby na platformie Azure przy użyciu tych ustawień i wdrożyć kod projektu.