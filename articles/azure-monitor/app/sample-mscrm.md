---
title: Microsoft Dynamics CRM i Azure Application Insights | Microsoft Docs
description: Pobierz dane telemetryczne z usługi Microsoft Dynamics CRM Online przy użyciu Application Insights. Przewodnik instalacji, pobieranie danych, Wizualizacja i eksportowanie.
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
ms.openlocfilehash: 470f723782ca29409549e0df8e900edf86cd446e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534282"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Przewodnik: Włączanie telemetrii dla usługi Microsoft Dynamics CRM Online przy użyciu Application Insights
W tym artykule opisano sposób pobierania danych telemetrycznych z usługi [Microsoft Dynamics CRM Online](https://www.dynamics.com/) przy użyciu [Application Insights platformy Azure](https://azure.microsoft.com/services/application-insights/). Przeprowadzimy przez cały proces dodawania skryptu Application Insights do aplikacji, przechwytywania danych i wizualizacji danych.

> [!NOTE]
> [Przejrzyj przykładowe rozwiązanie](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Dodawanie Application Insights do nowego lub istniejącego wystąpienia usługi CRM Online
Aby monitorować aplikację, należy dodać do aplikacji zestaw Application Insights SDK. Zestaw SDK wysyła dane telemetryczne do [portalu Application Insights](https://portal.azure.com), w którym można korzystać z zaawansowanych narzędzi analitycznych i diagnostycznych albo eksportować je do magazynu.

### <a name="create-an-application-insights-resource-in-azure"></a>Tworzenie zasobu Application Insights na platformie Azure
1. Pobierz [konto w Microsoft Azure](https://azure.com/pricing). 
2. Zaloguj się do [Azure Portal](https://portal.azure.com) i Dodaj nowy zasób Application Insights. Jest to miejsce, w którym dane będą przetwarzane i wyświetlane.

    ![Kliknij pozycję +, usługi dla deweloperów, Application Insights.](./media/sample-mscrm/01.png)

    Wybierz ASP.NET jako typ aplikacji.
3. Postępuj zgodnie z instrukcjami, aby [uzyskać skrypt zestawu SDK języka JavaScript dla aplikacji](../../azure-monitor/app/javascript.md), skopiuj fragment kodu JavaScript i Zastąp klucz Instrumentacji poprawną wartością dla zasobu Application Insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Tworzenie zasobu sieci Web JavaScript w programie Microsoft Dynamics CRM
1. Otwórz wystąpienie usługi CRM Online i zaloguj się przy użyciu uprawnień administratora.
2. Otwórz ustawienia programu Microsoft Dynamics CRM, dostosowania, Dostosuj system

    ![Ustawienia programu Microsoft Dynamics CRM](./media/sample-mscrm/00001.png)

    ![Ustawienia > dostosowania](./media/sample-mscrm/00002.png)

1. Utwórz zasób JavaScript.

    ![Nowe okno dialogowe zasobów sieci Web](./media/sample-mscrm/07.png)

    Nadaj jej nazwę, wybierz pozycję **skrypt (JScript)** i Otwórz Edytor tekstu.

    ![Otwieranie edytora tekstu](./media/sample-mscrm/00004.png)
2. Skopiuj kod z Application Insights zestawu SDK języka JavaScript, w którym wcześniej skonfigurowano klucz Instrumentacji. Podczas kopiowania, pamiętaj, aby zignorować Tagi skryptu. Skorzystaj z poniższego zrzutu ekranu:

    ![Ustaw klucz Instrumentacji](./media/sample-mscrm/000005.png)

    Kod zawiera klucz instrumentacji, który identyfikuje zasób usługi Application Insights.
3. Zapisz i Opublikuj.

    ![Zapisz i opublikuj](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Formularze instrumentów
1. W usłudze Microsoft CRM Online Otwórz formularz konta

    ![Formularz konta](./media/sample-mscrm/00007.png)
2. Otwórz właściwości formularza

    ![Właściwości formularza](./media/sample-mscrm/00008.png)
3. Dodawanie utworzonego zasobu sieci Web języka JavaScript

    ![Menu Dodaj](./media/sample-mscrm/13.png)

4. Zapisz i Opublikuj dostosowania formularza.

## <a name="metrics-captured"></a>Przechwycone metryki
Skonfigurowano przechwytywanie danych telemetrycznych dla formularza. Za każdym razem, gdy jest używany, dane zostaną wysłane do zasobu Application Insights.

Poniżej przedstawiono przykłady danych, które zobaczysz.

#### <a name="application-health"></a>Kondycja aplikacji
![Przykładowy czas ładowania strony](./media/sample-mscrm/15.png)

![Przykładowy wykres wyświetlania strony](./media/sample-mscrm/16.png)

Wyjątki przeglądarki:

![Wykres wyjątków przeglądarki](./media/sample-mscrm/17.png)

Kliknij wykres, aby uzyskać więcej szczegółów:

![Lista wyjątków](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Użycie
![Użytkownicy, sesje i wyświetlenia stron](./media/sample-mscrm/19.png)

![Wykresy sesji](./media/sample-mscrm/20.png)

![Wersje przeglądarki](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Przeglądarki
![Podział czasu ładowania strony](./media/sample-mscrm/22.png)

![Liczba sesji według wersji przeglądarki](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolokalizacja
![Liczba sesji według kraju](./media/sample-mscrm/24.png)

![Sesje i użytkownicy według kraju](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Wewnątrz żądania wyświetlenia strony
![Podsumowanie widoku strony](./media/sample-mscrm/26.png)

![Wyszukaj zdarzenia w widoku strony](./media/sample-mscrm/27.png)

![Podobne wyświetlenia strony](./media/sample-mscrm/28.png)

![Właściwości wyświetlania stron](./media/sample-mscrm/29.png)

![Strony na sesję](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Przykładowy kod
[Przeglądaj przykładowy kod](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
[Aby wyeksportować dane do programu Microsoft Power BI](../../azure-monitor/app/export-power-bi.md ), można jeszcze bardziej szczegółowo przeprowadzić analizę.

## <a name="sample-microsoft-dynamics-crm-solution"></a>Przykładowe rozwiązanie Microsoft Dynamics CRM
[Oto przykładowe rozwiązanie zaimplementowane w programie Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Dowiedz się więcej
* [Co to jest usługa Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights stron sieci Web](../../azure-monitor/app/javascript.md)
* [Więcej przykładów i instruktaży](../../azure-monitor/app/app-insights-overview.md)
