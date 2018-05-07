---
title: Tworzenie alertów z progami dynamicznych w monitorze Azure | Dokumentacja firmy Microsoft
description: Tworzenie alertów z usługi machine learning na podstawie progów dynamiczne
author: antonfrMSFT
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: antonfr;mbullwin
ms.openlocfilehash: c847052134b1d83cd606e0e2b51b63b580f7917c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Alerty z progami dynamicznych w monitorze Azure (ograniczone publicznej wersji zapoznawczej)

Alerty z progami dynamiczne są ulepszeniem alerty Metryka Azure w monitorze Azure, której korzystać z zaawansowanych możliwości Machine Learning (ML), aby dowiedzieć się więcej metryki historycznych zachowanie można automatycznie obliczyć linii bazowych i wykorzystać je jako progi alertów.

Korzyści wynikające ze stosowania progów dynamiczne są:

- Zapisz proste skojarzone z ustawienia wstępnie zdefiniowanych granic sztywne, ponieważ monitor automatycznie uzyskuje informacje o wydajności historycznych, metryki i stosuje algorytmów uczenia Maszynowego można określić progi alertów.
- Mogą identyfikować okresach zachowanie i alert tylko w odchylenia od oczekiwane zachowanie okresach. Metryki alerty z progami dynamiczne nie powoduje wyzwolenia, jeśli usługa jest regularnie bezczynności w weekendy, a następnie wzrósł w każdy poniedziałek. Obecnie obsługiwane: sezonowości co godzinę, codziennie i co tydzień.
- Stale uzyskuje informacje o metryki wydajności i jest odpowiadanie na zmiany metryki.

Alerty oparte na wartościach progowych dynamiczne są dostępne dla wszystkich Azure monitora na podstawie metryki źródeł wymienione w tym [artykułu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Zarejestruj się, aby uzyskać dostęp w wersji zapoznawczej

Do wykonania tej funkcji dla pokrętła, [Załóż podglądu](http://aka.ms/DynamicThresholdMetricAlerts). Zawsze, chętnie czekamy na Twoją opinię, zachować przesyłanych na [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Jak skonfigurować alerty z progami dynamiczne

Alerty z progami dynamicznej można skonfigurować za pomocą alertów w monitorze Azure

![Podgląd alertów](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Tworzenie reguły alertu z progami dynamiczne

1. W okienku alertów w obszarze monitorowania, wybierz **nową regułę alertu** przycisk, aby utworzyć nowy alert na platformie Azure.

   ![Nowa reguła alertu](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. Sekcji Tworzenie reguły jest wyświetlany z trzech części składające się z: _Zdefiniuj warunek alertu_, _Zdefiniuj szczegóły alertu_, i _grupy akcji Definiuj_. Najpierw zaczynać _Zdefiniuj warunek alertu_ sekcji Użyj **wybierz docelowy** łącze, aby określić obiekt docelowy, wybierając zasobu. Po wybraniu odpowiednich zasobów, kliknij przycisk Gotowe.

   ![Wybierz element docelowy](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. Następnie użyć **Dodaj kryteria** wybierz odpowiedni przycisk, aby wyświetlić listę sygnału opcje dostępne dla zasobu i z listy sygnału **Metryka** opcji. (Na przykład procesora CPU w procentach.)

   ![Dodaj kryteria](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. Na ekranie konfiguracji sygnału logikę w sekcji alertu logiki masz opcja przełączania warunek dla typu dynamicznego, które automatycznie wygeneruje progi dynamicznego (czerwoną linie) obok metryka (linię).

   ![Dynamiczny](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. Na podstawie wartości progowe na wykresie mają być obliczane na ostatnich siedmiu dni dane historyczne utworzony alert dynamiczne progi uzyska dostęp do dodatkowych danych historycznych, który jest dostępny i stale Dowiedz się na podstawie nowych danych bardziej dokładne wartości progowe.

6. Ustawienia dodatkową logikę alertu:
   - Warunek — możesz wybrać alert wyzwolenie na jednym z trzech następujących warunków:
       - Górny próg mniejsza niż niższa niż próg dolny (ustawienie domyślne)
       - Większy niż górny próg
       - Poniżej dolnej wartości progowej.
   - Czas agregacji: średni (domyślnie), sum, min, max.
   - Ważność alertu:
       - Wysoki — więcej alertów, jak na najmniejszą odchylenia zostanie wyzwolony alert.
       - MED — mniej liter niż wysoki, alerty mniej niż Wysoka czułość (ustawienie domyślne)
       - Niskie — próg wskazującą najmniejszą ważność.

    ![Logika ustawienia alertu](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. Obliczane na podstawie:
    -  Jakie czas trwania Alert szukać określony warunek, wybierając z **okresu**.

    ![Oceniono na podstawie](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Obsługiwane wartości okresu: 5 minut, 10 minut, 30 minut i godzinę.

   Do zmniejszenia szumu alertów wygenerowanych przez nagłego przejściowy, zaleca się za pomocą ustawień "Liczba naruszeń wyzwolenia alertu". Ta funkcja umożliwia alert zostanie wygenerowany tylko wtedy, gdy próg zostało naruszone X razy pod rząd lub razy Y poza ostatnich okresów Z. Na przykład:

    Do wyzwolenia alertu, gdy problem ciągłego 15 min, 3 razy pod rząd w danym okresie 5 minut, użyj następujących ustawień:

   ![Oceniono na podstawie](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    Do wyzwolenia alertu, gdy wystąpił naruszenie z dynamicznego próg w ciągu 15 minut z ostatnich 30 minut okres 5 minut, użyj następujących ustawień:

   ![Oceniono na podstawie](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. Obecnie użytkownicy mogą mieć alerty z kryteriami Próg dynamiczny jako pojedynczego kryterium.

   ![Utwórz regułę](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Pytania i odpowiedzi

- Pytanie: Jeśli Metryka zmieni się powoli wraz z upływem czasu, to wyzwolenia alertu z progami dynamiczne?

- Odpowiedź: prawdopodobnie nie. Progi dynamiczne są dobry wykrywanie znaczne odchylenia, a nie wolno zmieniających się problemy.

- Pytanie: czy można skonfigurować progi dynamiczne przy użyciu interfejsu API?

- Odpowiedź: pracujemy nad go.
