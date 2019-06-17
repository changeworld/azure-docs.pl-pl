---
title: Utwórz nowy zasób usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Ręcznie skonfiguruj monitorowanie usługi Application Insights dla nowej aplikacji na żywo.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073290"
---
# <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Usługa Azure Application Insights zawiera dane dotyczące Twojej aplikacji w systemie Microsoft Azure *zasobów*. Tworzenie nowego zasobu znajduje się w związku z tym [konfiguracji usługi Application Insights na potrzeby monitorowania nowej aplikacji][start]. Po utworzeniu nowego zasobu, możesz pobrać jego klucza Instrumentacji i używać go do konfigurowania zestawu SDK usługi Application Insights. Klucz Instrumentacji łączy telemetrii do zasobu.

## <a name="sign-in-to-microsoft-azure"></a>Zaloguj się do platformy Microsoft Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Utwórz zasób usługi Application Insights:

![Kliknij znak "+" w lewym górnym rogu. Wybierz narzędzia dla deweloperów, a następnie przez usługę Application Insights](./media/create-new-resource/new-app-insights.png)

   | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację. |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej lub istniejącej grupy zasobów do przechowywania danych usługi App Insights. |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji. |

Wprowadź odpowiednie wartości w wymaganych polach, a następnie wybierz **przeglądu + Utwórz**.

![Wprowadź wartości w polach wymagane, a następnie wybierz pozycję "Przeglądanie + tworzenie".](./media/create-new-resource/review-create.png)

Po utworzeniu aplikacji, zostanie otwarte nowe okienko. To okienko jest tam, gdzie zobaczysz danych użycia i wydajności dotyczące monitorowanej aplikacji. 

## <a name="copy-the-instrumentation-key"></a>Skopiuj klucz Instrumentacji

Klucz Instrumentacji identyfikuje zasób, który chcesz skojarzyć dane telemetryczne z. Konieczne będzie kopiowania, aby dodać klucz Instrumentacji do kodu aplikacji.

![Kliknij przycisk, a następnie skopiuj klucz Instrumentacji](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Zainstaluj zestaw SDK w aplikacji

Zainstaluj zestaw SDK usługi Application Insights w swojej aplikacji. Ten krok zależy od intensywnie typu aplikacji.

Użyj klucza Instrumentacji do skonfigurowania [zestawu SDK, który można zainstalować w aplikacji][start].

Zestaw SDK zawiera standardowe moduły, które wysyłają dane telemetryczne bez konieczności pisania dodatkowego kodu. Śledzenie działań użytkownika lub diagnozować problemy z bardziej szczegółowo [za pomocą interfejsu API] [ api] do wysyłania własnej telemetrii.

## <a name="creating-a-resource-automatically"></a>Automatyczne tworzenie zasobu
Można napisać [skrypt programu PowerShell](../../azure-monitor/app/powershell.md) automatycznego tworzenia zasobu.

## <a name="next-steps"></a>Kolejne kroki
* [Wyszukiwanie diagnostyczne](../../azure-monitor/app/diagnostic-search.md)
* [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md)
* [Pisanie zapytań analitycznych](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md