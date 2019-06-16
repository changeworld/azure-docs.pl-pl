---
title: Microsoft Dynamics CRM i usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Pobierz dane telemetryczne Microsoft Dynamics CRM Online przy użyciu usługi Application Insights. Przewodnik po instalacji, pobieranie danych, wizualizacji i eksportu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 6119f1116d255f7cd2a2bfc20e86eeca9e5dfe82
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60523282"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Przewodnik: Włączanie Telemetrii dla usługi Microsoft Dynamics CRM Online przy użyciu usługi Application Insights
W tym artykule pokazano, jak można pobrać danych telemetrycznych z [Microsoft Dynamics CRM Online](https://www.dynamics.com/) przy użyciu [usługi Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Omówimy kompletnego procesu dodawania skrypt usługi Application Insights do aplikacji, przechwytywania danych i wizualizacja danych.

> [!NOTE]
> [Przeglądaj przykładowe rozwiązanie](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Dodaj usługę Application Insights do nowego lub istniejącego wystąpienia CRM Online
Aby monitorować aplikację, należy dodać zestaw SDK usługi Application Insights do aplikacji. Zestaw SDK wysyła dane telemetryczne do [portalu Application Insights](https://portal.azure.com), gdzie można korzystać z naszych zaawansowanych analiz i narzędzia diagnostyczne lub dane są eksportowane do magazynu.

### <a name="create-an-application-insights-resource-in-azure"></a>Tworzenie zasobu usługi Application Insights na platformie Azure
1. Pobierz [konta w systemie Microsoft Azure](https://azure.com/pricing). 
2. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i Dodaj nowy zasób usługi Application Insights. Jest to, gdzie opracowywania i wyświetlania danych.

    ![Kliknij pozycję +, usługi dla deweloperów, Application Insights.](./media/sample-mscrm/01.png)

    Wybierz ASP.NET jako typ aplikacji.
3. Postępuj zgodnie z instrukcjami, aby [Pobierz skrypt zestaw SDK języka JavaScript dla aplikacji](../../azure-monitor/app/javascript.md#set-up-application-insights-for-your-web-page), skopiuj fragment kodu JavaScript i upewnij się, że Zastąp klucz Instrumentacji poprawnej wartości zasobu usługi Application Insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Tworzenie zasobu internetowego języka JavaScript w programie Microsoft Dynamics CRM
1. Otwórz swoje wystąpienie usługi CRM Online i logowania z uprawnieniami administratora.
2. Otwórz Microsoft Dynamics CRM ustawień, dostosowania, dostosowywanie systemu

    ![Ustawienia programu Microsoft Dynamics CRM](./media/sample-mscrm/00001.png)

    ![Ustawienia > dostosowania](./media/sample-mscrm/00002.png)

1. Utwórz zasób języka JavaScript.

    ![Okno dialogowe Nowy zasób sieci Web](./media/sample-mscrm/07.png)

    Nadaj jej nazwę, wybierz opcję **skryptu (JScript)** , a następnie otwórz Edytor tekstu.

    ![Otwórz Edytor tekstu](./media/sample-mscrm/00004.png)
2. Skopiuj kod z aplikacji zestawu SDK języka JavaScript wgląd w którym skonfigurowano klucz Instrumentacji przed. Podczas kopiowania, upewnij się zignorować tagów skryptu. Zapoznaj się z poniższym zrzucie ekranu:

    ![Ustaw klucz Instrumentacji](./media/sample-mscrm/000005.png)

    Kod zawiera klucz instrumentacji, który identyfikuje zasób usługi Application insights.
3. Zapisywanie i publikowanie.

    ![Zapisz i opublikuj](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Formularze Instrumentacji
1. W usłudze Microsoft CRM Online należy otworzyć formularz konta

    ![Formularz konta](./media/sample-mscrm/00007.png)
2. Otwórz formularz właściwości

    ![Właściwości formularza](./media/sample-mscrm/00008.png)
3. Dodawanie zasobu internetowego języka JavaScript, który został utworzony

    ![Menu Dodaj](./media/sample-mscrm/13.png)

4. Zapisz i opublikuj swoje dostosowania formularza.

## <a name="metrics-captured"></a>Metryki przechwycone
Po skonfigurowaniu teraz funkcję przechwytywania danych telemetrycznych dla formularza. Zawsze, gdy jest używane, dane będą wysyłane do zasobu usługi Application Insights.

Poniżej przedstawiono przykłady danych, które zostaną wyświetlone.

#### <a name="application-health"></a>Kondycja aplikacji
![Czas ładowania strony przez przykład](./media/sample-mscrm/15.png)

![Przykładowy wykres wyświetleń strony](./media/sample-mscrm/16.png)

Wyjątki przeglądarki:

![Wykres wyjątki przeglądarki](./media/sample-mscrm/17.png)

Kliknij wykres, aby uzyskać więcej informacji:

![Lista wyjątków](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Sposób użycia
![Użytkownicy, sesje i wyświetlenia stron](./media/sample-mscrm/19.png)

![Wykresy sesji](./media/sample-mscrm/20.png)

![Wersje przeglądarki](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Przeglądarki
![Podział czas ładowania strony](./media/sample-mscrm/22.png)

![Liczba sesji według wersji przeglądarki](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolokalizacja
![Liczba sesji według kraju](./media/sample-mscrm/24.png)

![Sesje i użytkownicy wg krajów](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Żądanie widoku strony do wewnątrz
![Podsumowanie widoku strony](./media/sample-mscrm/26.png)

![Wyszukiwanie zdarzeń widoku strony](./media/sample-mscrm/27.png)

![Podobne wyświetlenia strony](./media/sample-mscrm/28.png)

![Właściwości wyświetlania stron](./media/sample-mscrm/29.png)

![Liczba stron na sesję](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Przykładowy kod
[Przeglądaj w przykładowym kodzie](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Możesz wykonać nawet dokładniejszej analizy, gdy użytkownik [dane są eksportowane do usługi Microsoft Power BI](../../azure-monitor/app/export-power-bi.md ).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Przykładowe Microsoft Dynamics CRM rozwiązanie
[Poniżej przedstawiono przykładowe rozwiązanie zaimplementowany w Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Dowiedz się więcej
* [Co to jest usługa Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Usługa Application Insights dla stron sieci web](../../azure-monitor/app/javascript.md)
* [Więcej przykładów i przewodniki](../../azure-monitor/app/app-insights-overview.md)
