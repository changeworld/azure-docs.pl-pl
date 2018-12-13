---
title: Tworzenie alertów z dynamicznymi progami w usłudze Azure Monitor
description: Tworzenie alertów za pomocą usługi machine learning na podstawie dynamicznymi progami
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: Yaniv.Lavi
ms.reviewer: mbullwin
ms.openlocfilehash: b9b56145c13cb83b1686004ce215b1960caced14
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182999"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-private-preview"></a>Alerty z dynamicznymi progami w usłudze Azure Monitor (ograniczony prywatna wersja zapoznawcza)

Alerty z dynamicznymi progami stanowią rozszerzenie alerty metryki platformy Azure w usłudze Azure Monitor, który korzystać z zaawansowanych możliwości Machine Learning (ML), aby dowiedzieć się więcej metryki historycznych zachowanie, aby automatycznie obliczyć linii bazowych i używać ich jako progi alertów.

Korzyści z używania dynamicznymi progami są:

- Zapisz trudności związanych ze skomplikowanymi skojarzone z ustawienia wstępnie zdefiniowanych granic sztywne, ponieważ monitor uczy się wydajność historycznych metryki i automatycznie stosuje algorytmów uczenia Maszynowego, aby określić progi alertów.
- Identyfikują one sezonowych zachowanie i alertu tylko o odchyleniach od oczekiwanego zachowania sezonowym. Alerty metryk z dynamicznymi progami nie spowodują uruchomienia, jeśli usługa jest regularnie bezczynności w weekendy, a następnie gwałtowne wzrosty w każdy poniedziałek. Obecnie obsługiwane: sezonowości co godzinę, codziennie i co tydzień.
- Ciągle uczy się metryki wydajności i jest adaptacyjne metryki zmian.

Alerty oparte na wartościach progowych dynamicznych dostępnych na potrzeby wszystkich monitora platformy Azure na podstawie metryki wymienione w tym źródła [artykułu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Utwórz konto, aby uzyskać dostęp (wersja zapoznawcza)

Do wykonania tych funkcji pokrętła [Zamów wersję zapoznawczą](https://aka.ms/DynamicThresholdMetricAlerts). Zawsze, chętnie poznamy Twoją opinię, zachować, dostępne w [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Jak skonfigurować alerty z dynamicznymi progami

Alerty z dynamicznymi progami można skonfigurować za pomocą alertów w usłudze Azure Monitor

![Alerty (wersja zapoznawcza)](media/alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Tworzenie reguły alertu z dynamicznymi progami

1. W okienku alertów w obszarze monitorowanie wybierz **nową regułę alertu** przycisk, aby utworzyć nowego alertu na platformie Azure.

   ![Nowa reguła alertu](media/alerts-dynamic-thresholds/002.png)

2. Pokazano sekcji Tworzenie reguły z trzech części, składający się z: _Zdefiniuj warunek alertu_, _Zdefiniuj szczegóły alertu_, i _zdefiniuj grupę akcji_. Najpierw zaczynają się od _Zdefiniuj warunek alertu_ sekcji użyto **Select Target** link, aby określić docelową, wybierając zasobu. Po wybraniu odpowiedni zasób, kliknij przycisk Gotowe.

   ![Wybieranie obiektu docelowego](media/alerts-dynamic-thresholds/0003.png)

3. Dalej używać **Dodaj kryteria** przycisk, aby wyświetlić listę sygnałów dostępnymi dla zasobu, a z listy sygnału wybierz odpowiednią **metryki** opcji. (Na przykład procentowe użycie procesora CPU.)

   ![Dodaj kryteria](media/alerts-dynamic-thresholds/004.png)

4. Na ekranie Konfigurowanie logiki sygnału w sekcji Alert logic masz opcję przełączania warunku dla typu dynamicznego, które automatycznie wygeneruje dynamiczne progi (czerwone linie) obok metryki (niebieska linia).

   ![Dynamiczny](media/alerts-dynamic-thresholds/005.png)

5. Wartości progowe na wykresie są obliczane na podstawie na ostatnich siedmiu dni dla danych historycznych, po utworzeniu alertu dynamicznymi progami uzyska dostęp do dodatkowych danych historycznych, który jest dostępny i stale dowiesz się, na podstawie nowych danych bardziej precyzyjne progów.

6. Ustawienia dodatkową logikę alertów:
   - Warunek — możesz wybrać alertu dla jednej z trzech następujących warunków:
       - Większy niż próg górny lub mniejsza niż niższa wartość progową (ustawienie domyślne)
       - Większa niż górny próg
       - Mniejsza niż niższa wartość progową.
   - Agregacja czasu: Średni (domyślnie), sum, min, max.
   - Czułość alertów:
       - Wysoki — więcej alertów, ponieważ alert zostanie wyzwolony na najmniejszą odchylenia.
       - MED — mniej wrażliwych niż wysoki, alerty mniej niż Wysoka czułość (ustawienie domyślne)
       - Niska — próg wskazującą najmniejszą ważność.

    ![Ustawienia logika alertu](media/alerts-dynamic-thresholds/00007.png)

7. Obliczane na podstawie:
    -  Jakie czas trwania alertu należy szukać określony warunek, wybierając z **okres**.

    ![Oceniono na podstawie](media/alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Obsługiwane wartości okresu: 5 minut, 10 minut, 30 minut do 1 godziny.

   Do ograniczenia liczby niepotrzebnych alertów generowanych przez przejściowe skoki, zaleca się za pomocą ustawień "Liczba naruszeń wyzwalających alert". Ta funkcja umożliwia alert zostanie wygenerowany tylko wtedy, gdy próg został naruszony, zwiększyłaby X razy pod rząd lub razy Y z ostatnich okresów Z. Na przykład:

    Aby wyzwolić alert, gdy problem jest ciągły przez 15 minut, 3 razy pod rząd w danym okresie 5 minut, użyj następujących ustawień:

   ![Oceniono na podstawie](media/alerts-dynamic-thresholds/0008.png)

    Aby wyzwolić alert podczas naruszenie od progu dynamicznego w ciągu 15 minut z ostatnich 30 minut od okresu 5 minut, użyj następujących ustawień:

   ![Oceniono na podstawie](media/alerts-dynamic-thresholds/0009.png)

8. Obecnie użytkownicy mogą mieć alerty z kryteriami progu dynamicznego jako pojedyncze kryterium.

   ![Utwórz regułę](media/alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Pytania i odpowiedzi

- PYT.: Jeśli Metryka powoli zmienia się wraz z upływem czasu, to wyzwoli alert z dynamicznymi progami?

- ODP.: Prawdopodobnie nie. Dynamicznymi progami dla zastosowań dobre są wykrywanie znaczące odchylenia, a nie wolno zmieniających się problemy.

- PYT.: Można skonfigurować dynamicznymi progami przy użyciu interfejsu API?

- ODP.: Pracujemy nad na nim.
