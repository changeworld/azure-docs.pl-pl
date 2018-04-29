---
title: Microsoft Dynamics CRM i usługa Azure Application Insights | Dokumentacja firmy Microsoft
description: Pobierz dane telemetryczne z programu Microsoft Dynamics CRM Online przy użyciu usługi Application Insights. Pobieranie danych, wizualizacji i eksportowanie Przewodnik instalacji.
services: application-insights
documentationcenter: ''
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: mbullwin
ms.openlocfilehash: ced4f62b4f27ede28c61e8d2670432ddd7b76f9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Wskazówki: Włączanie dane telemetryczne dla programu Microsoft Dynamics CRM Online przy użyciu usługi Application Insights
W tym artykule przedstawiono sposób pobierać dane telemetryczne [Microsoft Dynamics CRM Online](https://www.dynamics.com/) przy użyciu [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Zostanie omówiony Zakończ proces dodawania skryptów usługi Application Insights do aplikacji, przechwytywania danych i wizualizacji danych.

> [!NOTE]
> [Przeglądaj przykładowe rozwiązanie](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Dodawanie usługi Application Insights do nowego lub istniejącego wystąpienia CRM Online
Do monitorowania aplikacji, należy dodać zestaw SDK usługi Application Insights do aplikacji. Zestaw SDK wysyła dane telemetryczne do [portalu Application Insights](https://portal.azure.com), których można użyć naszych zaawansowane analizy i narzędzia diagnostyczne lub Eksportuj dane do magazynu.

### <a name="create-an-application-insights-resource-in-azure"></a>Tworzenie zasobu usługi Application Insights na platformie Azure
1. Pobierz [konta na platformie Microsoft Azure](http://azure.com/pricing). 
2. Zaloguj się do [portalu Azure](https://portal.azure.com) i Dodaj nowy zasób usługi Application Insights. Jest to, gdzie opracowywania i wyświetlania danych.

    ![Kliknij pozycję +, usług deweloperskich, usługi Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Wybierz ASP.NET jako typ aplikacji.
3. Otwórz stronę wprowadzenie i otworzyć "Monitor i diagnozowanie po stronie klienta".

    ![Fragment kodu dotyczący wstawiania na stronie sieci web](./media/app-insights-sample-mscrm/03.png)

**Nie zamykaj strony kodowej** przy wykonywaniu do następnego kroku w innym oknie przeglądarki. Będziesz potrzebować kod wkrótce. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Utwórz zasób sieci web JavaScript w programie Microsoft Dynamics CRM
1. Otwórz wystąpienie CRM Online, a logowanie z uprawnieniami administratora.
2. Otwórz aplikację Microsoft Dynamics CRM ustawienia, dostosowania, dostosowywanie systemu

    ![Ustawienia programu Microsoft Dynamics CRM](./media/app-insights-sample-mscrm/00001.png)

    ![Ustawienia > dostosowania](./media/app-insights-sample-mscrm/00002.png)

1. Utwórz zasób języka JavaScript.

    ![Okno dialogowe nowego zasobu sieci Web](./media/app-insights-sample-mscrm/07.png)

    Nadaj mu nazwę, wybierz opcję **skryptu (JScript)** , a następnie otwórz Edytor tekstu.

    ![Otwórz Edytor tekstu](./media/app-insights-sample-mscrm/00004.png)
2. Skopiuj kod z usługi Application Insights. Podczas kopiowania, upewnij się, że zignorowanie tagów skryptu. Zapoznaj się poniżej zrzut ekranu:

    ![Ustaw klucz Instrumentacji](./media/app-insights-sample-mscrm/000005.png)

    Kod zawiera klucz instrumentacji, który identyfikuje z zasobu usługi Application insights.
3. Zapisz i opublikuj.

    ![Zapisz i opublikuj](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Formularze dokumentu
1. W programie Microsoft CRM Online Otwórz formularz konta

    ![Formularz konta](./media/app-insights-sample-mscrm/00007.png)
2. Otwórz formularz właściwości

    ![Właściwości formularza](./media/app-insights-sample-mscrm/00008.png)
3. Dodaj zasób sieci web JavaScript, który został utworzony

    ![Dodawanie menu](./media/app-insights-sample-mscrm/13.png)

4. Zapisz i opublikuj swoje dostosowania formularza.

## <a name="metrics-captured"></a>Metryki przechwycić
Po skonfigurowaniu teraz przechwytywania danych telemetrycznych dla formularza. Zawsze, gdy jest on używany, dane zostaną wysłane do zasobu usługi Application Insights.

Poniżej przedstawiono przykłady, danych, który zostanie wyświetlony.

#### <a name="application-health"></a>Kondycja aplikacji
![Czas ładowania strony przykład](./media/app-insights-sample-mscrm/15.png)

![Przykład strony widoki wykresu](./media/app-insights-sample-mscrm/16.png)

Wyjątki przeglądarki:

![Wykres wyjątków przeglądarki](./media/app-insights-sample-mscrm/17.png)

Kliknij na wykresie, aby uzyskać więcej szczegółów:

![Listy wyjątków](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Sposób użycia
![Użytkownikami, sesjami i wyświetleń strony](./media/app-insights-sample-mscrm/19.png)

![Wykresy sesji](./media/app-insights-sample-mscrm/20.png)

![Wersji przeglądarki](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Przeglądarki
![Podział czas ładowania strony](./media/app-insights-sample-mscrm/22.png)

![Liczba sesji według wersji przeglądarki](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Używanie funkcji Geolokalizacji
![Liczba sesji według kraju](./media/app-insights-sample-mscrm/24.png)

![Sesje i użytkowników według kraju](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Żądanie dostępu do strony w widoku
![Podsumowanie widoku strony](./media/app-insights-sample-mscrm/26.png)

![Wyszukaj zdarzenia widoku strony](./media/app-insights-sample-mscrm/27.png)

![Podobne wyświetleń strony](./media/app-insights-sample-mscrm/28.png)

![Właściwości wyświetlania stron](./media/app-insights-sample-mscrm/29.png)

![Strony na sesję](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Przykładowy kod
[Przeglądaj w przykładowym kodzie](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Nawet dokładniejszej analizy można zrobić, jeśli użytkownik [eksportowanie danych do usługi Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Przykładowe Microsoft Dynamics CRM rozwiązanie
[Oto przykładowe rozwiązanie zaimplementowane w programie Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Dowiedz się więcej
* [Co to jest Application Insights?](app-insights-overview.md)
* [Application Insights dla stron sieci web](app-insights-javascript.md)
* [Więcej przykłady i wskazówki](app-insights-code-samples.md)
