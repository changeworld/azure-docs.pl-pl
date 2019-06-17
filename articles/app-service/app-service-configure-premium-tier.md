---
title: Konfigurowanie warstwy PremiumV2 — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak lepsza wydajność sieci web, mobilnych i aplikacji interfejsu API w usłudze Azure App Service dzięki skalowaniu do nowej warstwy cenowej PremiumV2.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c85644e3cab39f9e0864af91722ee54aab6d59f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66139748"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurowanie warstwy PremiumV2 dla usługi Azure App Service

Nowy **PremiumV2** warstwa cenowa zapewnia szybszymi procesorami, magazynem dysków SSD i stosunek wartości podwójnej precyzji pamięć rdzeń istniejących warstw cenowych. Za pomocą korzyści wydajności można zapisać pieniądze, po uruchomieniu aplikacji na mniejszej liczby wystąpień. W tym artykule dowiesz się, jak utworzyć aplikację w **PremiumV2** warstwy lub skalować aplikację, aby **PremiumV2** warstwy.

## <a name="prerequisites"></a>Wymagania wstępne

Aplikację, aby skalować w górę **PremiumV2**, musisz mieć aplikacja usługi Azure App Service, która działa w warstwie cenowej niższa niż **PremiumV2**, i aplikacja muszą działać w wdrożenie usługi App Service, która obsługuje PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Dostępność PremiumV2

**PremiumV2** warstwa jest dostępna dla usługi App Service, zarówno _Windows_ także _Linux_.

**PremiumV2** jest dostępna w większości regionów platformy Azure. Aby zobaczyć, jeśli nie jest dostępna w Twoim regionie, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure [usługi Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Tworzenie aplikacji na platformie warstwy PremiumV2

Warstwa cenowa aplikację usługi App Service jest zdefiniowany w [planu usługi App Service](overview-hosting-plans.md) uruchamianego na. Można utworzyć plan usługi App Service, samodzielnie lub jako część tworzenia aplikacji.

Podczas konfigurowania planu usługi App Service w <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, wybierz opcję **warstwa cenowa**. 

Wybierz **produkcji**, a następnie wybierz **P1V2**, **P2V2**, lub **P3V2**, następnie kliknij przycisk **Zastosuj**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Jeśli nie widzisz **P1V2**, **P2V2**, i **P3V2** jako opcje, lub jeśli opcje są wyszarzone out następnie **PremiumV2** prawdopodobnie nie jest dostępna w podstawowe wdrożenie usługi App Service zawiera plan usługi App Service. Zobacz [skalowanie w górę z kombinacji grupy i regionu nieobsługiwanych](#unsupported) Aby uzyskać więcej informacji.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Skalowanie w górę do warstwy PremiumV2 istniejącej aplikacji

Przed skalowaniem istniejącą aplikację w celu **PremiumV2** warstwy, upewnij się, że **PremiumV2** jest dostępna. Aby uzyskać informacje, zobacz [dostępności PremiumV2](#availability). Jeśli nie jest dostępna, zobacz [skalowanie w górę z kombinacji grupy i regionu nieobsługiwanych](#unsupported).

W zależności od środowiska hostingu skalowanie w górę może wymagać wykonania dodatkowych czynności. 

W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, otwórz stronę aplikacji usługi App Service.

W lewym obszarze nawigacji strony aplikacji usługi App Service wybierz **skalowanie w górę (plan usługi App Service)** .

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Wybierz **produkcji**, a następnie wybierz **P1V2**, **P2V2**, lub **P3V2**, następnie kliknij przycisk **Zastosuj**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Jeśli operacja zakończy się pomyślnie, strona Omówienie aplikacji pokazuje, że jest on teraz **PremiumV2** warstwy.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Jeśli zostanie wyświetlony komunikat o błędzie

Niektóre plany usługi App Service nie można skalować w górę do warstwy PremiumV2 Jeśli podstawowego wdrożenia usługi App Service nie obsługuje PremiumV2.  Zobacz [skalowanie w górę z kombinacji grupy i regionu nieobsługiwanych](#unsupported) Aby uzyskać więcej informacji.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skalowanie w górę z kombinacji nieobsługiwanych grupy i region

Jeśli aplikacja działa we wdrożeniu usługi App Service gdzie **PremiumV2** nie jest dostępny lub jeśli aplikacja działa w regionie, który aktualnie nie obsługuje **PremiumV2**, należy ponownie wdrożyć aplikację do wykonania Zaletą **PremiumV2**.  Dostępne są dwie opcje:

- Tworzenie **nowe** zasobów grupy, a następnie utwórz **nowe** aplikacji i usługi App Service plan w **nowe** grupy zasobów, wybierając odpowiedni region platformy Azure w trakcie procesu tworzenia.  Możesz **musi** wybierz **PremiumV2** planu w momencie jest tworzony nowy plan usługi app service.  Dzięki temu kombinacji grupy zasobów, plan usługi App Service i regionu platformy Azure spowoduje planu usługi App Service, tworzonych w wdrożenie usługi App Service, która obsługuje **PremiumV2**.  Następnie należy ponownie wdrożyć kod aplikacji do nowo utworzoną aplikację i plan usługi app service. W razie potrzeby, plan usługi App Service można następnie Skaluj w dół, od **PremiumV2** Zapisz koszty i nadal będzie można pomyślnie skalowanie kopii ponownie w przyszłości za pomocą **PremiumV2**.
- Jeśli aplikacja już działa w istniejącym **Premium** warstwy, a następnie aplikację można sklonować przy użyciu ustawień aplikacji, parametry połączenia i konfiguracji wdrażania w nowy plan usługi aplikacji, która używa **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    W **aplikację** strony, można utworzyć planu usługi App Service przy użyciu **PremiumV2** w regionie oraz określić ustawienia aplikacji i konfiguracji, które chcesz sklonować.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Można zautomatyzować tworzenie aplikacji w **PremiumV2** warstwy za pomocą skryptów, za pomocą [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie tworzy plan usługi App Service w _P1V2_. Możesz uruchomić go w usłudze Cloud Shell. Opcje `--sku` są P1V2, _P2V2_, i _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie tworzy plan usługi App Service w _P1V2_. Opcje `-WorkerSize` są _małych_, _średni_, i _duże_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Więcej zasobów

[Skalowanie aplikacji na platformie Azure](web-sites-scale.md)  
[Ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md)
