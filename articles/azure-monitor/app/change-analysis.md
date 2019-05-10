---
title: Aplikacji usługi Azure Monitor zmienić analizy — odnajdywanie zmian, które mogą mieć wpływ na aktywnej witryny problemów/awarii, za pomocą aplikacji usługi Azure Monitor zmienić analizy | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z problemów z aktywną witryną aplikacji w usłudze Azure App Services za pomocą analizy zmiany aplikacji usługi Azure Monitor
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415843"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Usługa Azure Monitor zmiany analiza (publiczna wersja zapoznawcza)

W przypadku awarii lokacji na żywo problem/szybkiego określenia głównej przyczyny ma krytyczne znaczenie. Standardowe rozwiązania do monitorowania mogą pomóc Ci szybko zidentyfikować, że występuje problem, a często nawet określają składnik, kończy się niepowodzeniem. Ale to zawsze nie zaprowadzi do natychmiastowego wyjaśnienie, dlaczego występuje błąd. Witryny pracował pięć minut temu, teraz jest on uszkodzony. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, na które nowej funkcji usługi Azure Monitor zmiany analiza jest przeznaczona do odpowiedzi. Dzięki możliwości [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) zmiany analiza zapewnia wgląd w zmiany aplikacji usługi Azure App Service, aby zwiększyć observability i zmniejszyć MTTR (średniego czasu naprawy).

> [!IMPORTANT]
> Analiza zmiany aplikacji w usłudze Azure Monitor jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Jak używać analiza zmiany?

Analiza zmiany aplikacji w usłudze Azure Monitor jest obecnie wbudowany w samoobsługi **diagnozowanie i rozwiązywanie problemów** środowisko, które są dostępne z **Przegląd** sekcji usługi Azure App Service Aplikacja:

![Zrzut ekranu w usłudze Azure App Service — Omówienie strony czerwone pola wokół przycisku — omówienie i diagnozowanie i rozwiązywanie problemów, przycisk](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Włącz analizę zmiany

1. Wybierz **dostępności i wydajności**

    ![Zrzut ekranu przedstawiający dostępność i wydajność opcje rozwiązywania problemów](./media/change-analysis/availability-and-performance.png)

2. Kliknij przycisk **wystąpiła awaria aplikacji** kafelka.

   ![Zrzut ekranu z kafelkiem awarii aplikacji](./media/change-analysis/application-crashes-tile.png)

3. Aby włączyć **analizy zmiany** wybierz **Włącz teraz**.

   ![Zrzut ekranu przedstawiający dostępność i wydajność opcje rozwiązywania problemów](./media/change-analysis/application-crashes.png)

4. Aby móc korzystać z pełnego zmienić zestaw funkcji analizy **zmienić analizy**, **skanowania w poszukiwaniu zmian w kodzie**, i **zawsze na** do **na** i wybierz **Zapisz**.

    ![Zrzut ekranu przedstawiający interfejs użytkownika analizy zmiany Włączanie usługi Azure App Service](./media/change-analysis/change-analysis-on.png)

    Jeśli **analizy zmiany** jest włączone, można wykryć zmiany na poziomie zasobów. Jeśli **skanowania w poszukiwaniu zmian w kodzie** jest włączone, będziesz również pliki wdrożenia są wyświetlane i zmiany w konfiguracji lokacji. Włączanie **zawsze na** zoptymalizuje zmiany skanowanie wydajność, ale może pociągnąć za sobą dodatkowe koszty z punktu widzenia rozliczeń.

5.  Gdy wszystko jest włączona, wybierając **diagnozowanie i rozwiązywanie problemów** > **dostępność i wydajność** > **wystąpiła awaria aplikacji** Umożliwia dostęp do środowiska analizy zmiany. Wykres będzie summerize typ zmian, które wystąpiły w czasie, wraz z informacjami na temat tych zmian:

     ![Zrzut ekranu przedstawiający zmiany widoku różnic](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unable-to-fetch-change-analysis-information"></a>Nie można pobrać informacji analizy zmiany.

Jest to tymczasowy problem z bieżący proces dołączania (wersja zapoznawcza). Obejście składa się z ustawienie ukrytych tagu dla aplikacji sieci web, a następnie odświeżyć stronę:

   ![Zrzut ekranu przedstawiający ukryte zmiany tagu](./media/change-analysis/hidden-tag.png)

Aby ustawić ukryte tagu przy użyciu programu PowerShell:

1. Otwórz usługę Azure Cloud Shell:

    ![Zrzut ekranu przedstawiający zmian usługi Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

2. Zmień typ powłoki programu PowerShell:

   ![Zrzut ekranu przedstawiający zmian usługi Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

3. Uruchom następujące polecenie:

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> Po dodaniu znacznika ukryte, nadal konieczne może być początkowo czekać nawet 4 godzin najpierw wyświetlać zmiany. Jest to spowodowane freqeuncy 4 godziny, z których korzysta usługa analizy zmiany do skanowania aplikacji sieci web, jednocześnie ograniczając wpływ na wydajność skanowania.

## <a name="next-steps"></a>Kolejne kroki

- Ulepszyć monitorowanie usługi Azure App Services [przez włączenie funkcji usługi Application Insights](azure-web-apps.md) usługi Azure Monitor.
- Pomóc lepiej zrozumieć [wykres zasobów Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) pozwalającemu aplikacja usługi Azure Monitor power zmienić analizy. 
