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
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021288"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W oknie dialogowym **Publikowanie** wybierz pozycję **Microsoft Azure App Service**, wybierz pozycję **Utwórz nowy**, a następnie wybierz pozycję **Publikuj**.

   ![Wybierz miejsce docelowe publikowania](./media/webjobs-publish-netcore/pick-publish-target.png)

1. W oknie dialogowym **Tworzenie usługi app service** użyj ustawień hostingu określonych w tabeli poniżej obrazu:

    ![Okno dialogowe Tworzenie usługi App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. |
    | **Subskrypcja** | Wybierz subskrypcję | Subskrypcja platformy Azure, która ma być używana. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nazwa grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz pozycję **Nowa**, aby utworzyć nową grupę zasobów.|
    | **[Plan hostingowy](../articles/app-service/overview-hosting-plans.md)** | Plan usługi App Service | [Plan usługi App Service](../articles/app-service/overview-hosting-plans.md) określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która jest hostem aplikacji. Hostując wiele aplikacji, można zmniejszyć koszty przez skonfigurowanie aplikacji internetowych do korzystania z jednego planu usługi App Service. Plany usługi app service definiują region, rozmiar wystąpienia, liczbę skalowania i jednostkę SKU (bezpłatna, udostępniona, podstawowa, standardowa lub premium). Wybierz **pozycję Nowy,** aby utworzyć nowy plan usługi aplikacji. |

1. Kliknij **przycisk Utwórz,** aby utworzyć usługę WebJob i powiązane zasoby na platformie Azure z tymi ustawieniami i wdrożyć kod projektu.