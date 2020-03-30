---
title: Konfigurowanie warstwy PremiumV2
description: Dowiedz się, jak uzyskać lepszą wydajność aplikacji sieci Web, urządzeń mobilnych i interfejsu API w usłudze Azure App Service, skalując do nowej warstwy cenowej PremiumV2.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672212"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurowanie warstwy PremiumV2 dla usługi Azure App Service

Nowa warstwa cenowa **PremiumV2** zapewnia szybsze procesory, pamięć masową SSD i podwaja stosunek pamięci do rdzenia istniejących warstw cenowych. Dzięki korzyści wydajności można zaoszczędzić pieniądze, uruchamiając aplikacje na mniejszej liczbie wystąpień. W tym artykule dowiesz się, jak utworzyć aplikację w warstwie **PremiumV2** lub skalować aplikację do warstwy **PremiumV2.**

## <a name="prerequisites"></a>Wymagania wstępne

Aby skalować aplikację do **wersji PremiumV2,** musisz mieć aplikację usługi Azure App Service, która działa w warstwie cenowej niższej niż **PremiumV2,** a aplikacja musi być uruchomiona we wdrożeniu usługi app service, która obsługuje wersję PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Dostępność premiumv2

Warstwa **PremiumV2** jest dostępna dla usługi App Service zarówno w _systemie Windows,_ jak i _linux._

**Usługa PremiumV2** jest dostępna w większości regionów platformy Azure. Aby sprawdzić, czy jest on dostępny w Twoim regionie, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure w [usłudze Azure Cloud Shell:](../cloud-shell/overview.md)

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Tworzenie aplikacji w warstwie PremiumV2

Warstwa cenowa aplikacji usługi App Service jest zdefiniowana w [planie usługi app service,](overview-hosting-plans.md) na który działa. Możesz utworzyć plan usługi app service samodzielnie lub jako część tworzenia aplikacji.

Podczas konfigurowania planu usługi app service w <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>wybierz **warstwę cenową**. 

Wybierz **opcję Produkcja**, a następnie wybierz opcję **P1V2**, **P2V2**lub **P3V2**, a następnie kliknij przycisk **Zastosuj**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Jeśli nie widzisz **P1V2**, **P2V2**i **P3V2** jako opcje lub jeśli opcje są wyszarzone, a następnie **PremiumV2** prawdopodobnie nie jest dostępny w podstawowym wdrożenia usługi App Service, który zawiera plan usługi app service. Aby uzyskać więcej informacji, zobacz [Skalowanie w górę z nieobsługiconej grupy zasobów i kombinacji regionów.](#unsupported)

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Skalowanie w górę istniejącej aplikacji do warstwy PremiumV2

Przed skalowaniem istniejącej aplikacji do warstwy **PremiumV2** upewnij się, że **usługa PremiumV2** jest dostępna. Aby uzyskać więcej informacji, zobacz [Dostępność PremiumV2](#availability). Jeśli opcja ta nie jest dostępna, zobacz [Skalowanie w górę z nieobsługiconej grupy zasobów i kombinacji regionów](#unsupported).

W zależności od środowiska hostingowego skalowanie w górę może wymagać dodatkowych kroków. 

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>otwórz stronę aplikacji usługi App Service.

W lewej części strony aplikacji Usługi App Service wybierz pozycję **Skaluj w górę (plan usługi app service).**

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Wybierz **opcję Produkcja**, a następnie wybierz opcję **P1V2**, **P2V2**lub **P3V2**, a następnie kliknij przycisk **Zastosuj**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Jeśli operacja zakończy się pomyślnie, strona przeglądu aplikacji pokazuje, że jest teraz w warstwie **PremiumV2.**

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Jeśli zostanie wyświetlony błąd

Niektóre plany usługi app service nie można skalować do warstwy PremiumV2, jeśli podstawowe wdrożenie usługi App Service nie obsługuje PremiumV2.  Aby uzyskać więcej informacji, zobacz [Skalowanie w górę z nieobsługiconej grupy zasobów i kombinacji regionów.](#unsupported)

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skalowanie w górę z nieobsługiconej kombinacji grupy zasobów i regionu

Jeśli aplikacja jest uruchamiana we wdrożeniu usługi App Service, gdzie **usługa PremiumV2** nie jest dostępna lub jeśli aplikacja działa w regionie, który obecnie nie obsługuje **wersji PremiumV2,** należy ponownie wdrożyć aplikację, aby skorzystać z **wersji PremiumV2.**  Dostępne są dwie opcje:

- Utwórz **nową** grupę zasobów, a następnie utwórz **nową** aplikację i plan usługi App Service w **nowej** grupie zasobów, wybierając żądany region platformy Azure podczas procesu tworzenia.  **Należy** wybrać plan **PremiumV2** w momencie utworzenia nowego planu usługi aplikacji.  Dzięki temu połączenie grupy zasobów, planu usługi app service i regionu platformy Azure spowoduje, że plan usługi App Service zostanie utworzony we wdrożeniu usługi App Service, który obsługuje **wersję PremiumV2.**  Następnie ponownie wdrożyć kod aplikacji do nowo utworzonego planu usługi aplikacji i aplikacji. W razie potrzeby można następnie skalować plan usługi app service w dół z **PremiumV2,** aby zaoszczędzić koszty, a w przyszłości nadal będzie można pomyślnie skalować z powrotem w przyszłości za pomocą **PremiumV2**.
- Jeśli aplikacja jest już uruchamiana w istniejącej warstwie **Premium,** możesz sklonować aplikację ze wszystkimi ustawieniami aplikacji, ciągami połączeń i konfiguracją wdrożenia w nowy plan usługi aplikacji korzystający z **programu PremiumV2.**

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na stronie **Klonuj aplikację** możesz utworzyć plan usługi app service przy użyciu **usługi PremiumV2** w żądanym regionie i określić ustawienia aplikacji i konfigurację, które mają zostać sklonować.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Tworzenie aplikacji można zautomatyzować w warstwie **PremiumV2** za pomocą skryptów przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub programu Azure [PowerShell.](/powershell/azure/overview)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie tworzy plan usługi app service w _P1V2_. Można go uruchomić w Cloud Shell. Dostępne opcje `--sku` to P1V2, _P2V2_i _P3V2._

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie tworzy plan usługi app service w _P1V2_. `-WorkerSize` Dostępne opcje to _Małe,_ _Średnie_i _Duże_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Więcej zasobów

[Skalowanie aplikacji w górę na platformie Azure](manage-scale-up.md)  
[Ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md)
