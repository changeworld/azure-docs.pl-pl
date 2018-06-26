---
title: Planowanie skali środowiska Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób należy stosować najlepsze rozwiązania w przypadku planowania środowiska Azure Insights serii czasu, w tym pojemność magazynu, przechowywanie danych wejściowych pojemności, monitorowania i działalności odzyskiwania po awarii (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: jasonh
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: f0f414e43231fc6d873d639902fd4f71e48f1002
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751173"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planowanie środowiska Azure czas serii Insights

W tym artykule opisano sposób planowania środowiska Azure czas serii Insights na podstawie szybkość oczekiwanego wejściowych i wymagań dotyczących przechowywania danych.

## <a name="best-practices"></a>Najlepsze praktyki

Aby rozpocząć pracę z informacjami dotyczącymi czasu serii, najlepiej Jeśli wiadomo, ile danych spodziewasz się do dystrybuowania za minutę również, jak długo ma być do przechowywania danych.  

Aby uzyskać więcej informacji o pojemności i przechowywania dla obu SKU czasu serii szczegółowe informacje, zobacz [Insights serii czasu cennik](https://azure.microsoft.com/pricing/details/time-series-insights/).

Weź pod uwagę następujące atrybuty do planu najlepsze środowisko dla długoterminowym sukcesie: 
- Pojemność magazynu
- Okres przechowywania danych
- Ruch przychodzący wydajność 
- Kształtowania zdarzeń
- Sprawdzając, czy jest odwołanie do danych w miejscu

## <a name="understand-storage-capacity"></a>Zrozumienie pojemność magazynu
Domyślnie czas serii Insights zachowuje danych, w oparciu o ilość miejsca w magazynie, które zostały udostępnione (jednostek raza ilość pamięci masowej na jednostkę) i transfer danych przychodzących.

## <a name="understand-data-retention"></a>Omówienie przechowywania danych
Można skonfigurować środowisko czasu serii Insights **czas przechowywania danych** ustawienie umożliwiające maksymalnie 400 dni przechowywania.  Czas serii wgląd w dwóch trybach, który optymalizuje zapewniających środowiska zawiera najbardziej aktualne dane (na domyślnie), a innego, który jest zoptymalizowany do zapewnienia przechowywania limitów, gdy transfer danych przychodzących jest wstrzymana, jeśli ogólną pojemność środowisko zostaje trafiony.  Można dostosować, przechowywania i przełączania między dwa tryby na stronie konfiguracji w środowisku, w portalu Azure.

Można skonfigurować maksymalnie 400 dni przechowywania danych w środowisku Insights serii czasu.

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. W [portalu Azure](https://portal.azure.com), wybierz środowisko Insights serii czasu.

2. Na **strony środowiska czasu serii Insights**w obszarze **ustawienia** nagłówek, wybierz **Konfiguruj**. 

3. W **czas przechowywania danych (w dniach)** wprowadź wartość z zakresu od 1 do 400.

   ![Skonfiguruj przechowywanie](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Zrozumienie ruch przychodzący wydajność

Obszar innych skupić się na potrzeby planowania jest pojemność ruch przychodzący, która jest pochodną alokacji na minutę. 

Z punktu widzenia ograniczania przepustowości ingressed danych o rozmiarze pakiecie 32 KB jest traktowany jako 32 zdarzenia, każdy o rozmiarze 1 KB. Maksymalnego dozwolonego rozmiaru zdarzenia wynosi 32 KB; pakiety danych jest większy niż 32 KB są obcinane.

Poniższa tabela zawiera podsumowanie pojemności wejściowych dla każdej jednostki SKU:

|SKU  |Liczba zdarzeń na miesiąc na jednostkę  |Zdarzenia rozmiar na miesiąc na jednostkę  |Liczba zdarzeń na minutę na jednostkę  | Rozmiar na minutę na jednostkę   |
|---------|---------|---------|---------|---------|
|S1     |   30 milionów     |  30 GB     |  700    |  700 KB   |
|S2     |   300 milionów    |   300 GB   | 7,000   | 7000 KB  |

Można zwiększyć pojemność S1 lub S2 SKU do 10 jednostek w jednym środowisku. Nie można migrować z środowisku S1 S2 lub środowiska S2 S1. 

Ruch przychodzący pojemności najpierw należy określić całkowita wymagane na podstawie-miesięczny transfer danych przychodzących. Następnie należy określić, jakie użytkownika na minutę wymaga są, jak jest to, gdzie ograniczania przepustowości i opóźnień pełnić rolę.

Jeśli kolekcji znajdują się w sieci przychodzący danych trwających mniej niż 24 godziny, czasu serii wgląd można "przechwycenie" szybkością wejściowych 2 x stawki wymienionych powyżej. 

Na przykład jeśli masz jednej jednostki SKU S1 i danych wejściowych z szybkością 700 zdarzenia na minutę, kolekcji poniżej 1 godziny szybkością 1400 zdarzeń lub mniej, nie byłoby bez zauważalnego opóźnienia dla danego środowiska. Jednak po przekroczeniu 1400 zdarzenia na minutę przez więcej niż jedną godzinę, będzie prawdopodobnie mają opóźnienia do danych, które wizualizowanego i jest dostępny dla zapytań w danym środowisku. 

Może nie wiedzieć z wyprzedzeniem ilość danych można było się spodziewać push. W takim przypadku można znaleźć dane telemetryczne dla [Centrum IoT Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) i [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) w portalu Azure. Ten telemetrii ułatwia określenie sposobu udostępnienia środowiska. Użyj **metryki** strony w portalu Azure źródła odpowiednie zdarzenie wyświetlić jego telemetrii. Jeśli znasz Twoje metryki źródła zdarzeń można efektywniej planowanie i udostępnić środowiska Insights serii czasu.

### <a name="calculate-ingress-requirements"></a>Oblicz wymagania wejściowych

- Upewnij się, czy pojemność transfer danych przychodzących jest powyżej średnia szybkość na minutę oraz czy środowisko jest wystarczająco duża do obsługi sieci przychodzący przewidywanego odpowiednikiem 2 x możliwości poniżej 1 godziny.

- Po wystąpieniu nagłego transfer danych przychodzących który ostatnio przez czas dłuższy niż 1 godzina; Użyj szybkość, z kolekcji jako Twoja średnia i środowisko o pojemności do obsługi kolekcji częstotliwość udostępnić.
 
### <a name="mitigate-throttling-and-latency"></a>Ograniczenia przepustowości i opóźnień

Aby dowiedzieć się, jak zapobiec ograniczania przepustowości i opóźnień, zobacz [zmniejszyć opóźnienia i ograniczania przepustowości](time-series-insights-environment-mitigate-latency.md). 

## <a name="shaping-your-events"></a>Kształtowania zdarzeń
Należy koniecznie upewnij się, jak wysyłać zdarzenia do TSI obsługuje rozmiar środowiska w przypadku udostępniania (z drugiej strony, możesz mapować rozmiar środowiska do liczby zdarzeń odczytuje TSI, a rozmiar każdego zdarzenia).  Podobnie warto pomyśleć o atrybuty, które może zajść potrzeba wycinka i Filtruj według podczas wykonywania zapytań dotyczących danych.  Pamiętając o tym, zaleca się przegląd JSON kształtowania sekcji naszych *wysyłać zdarzenia* dokumentacji [dokumentacji] (https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  Jest w dolnej części strony.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Sprawdzając, czy jest odwołanie do danych w miejscu
Odwołanie do zestawu danych to kolekcja elementów, które rozszerzyć zdarzenia ze źródła zdarzeń. Czas Insights serii wejściowych aparat łączy każdego zdarzenia ze źródła zdarzeń z odpowiednich wiersza danych w zestawie danych odwołania. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. Tego sprzężenia jest oparta na kolumny klucza podstawowego zdefiniowany w zestawie danych odwołania.

Uwaga: odwołanie do danych nie jest dołączony Wstecz. Oznacza to, że tylko danych wejściowych aktualnych i przyszłych jest zgodny i dołączony do zestawu odwołania data po jej skonfigurowaniu i przekazać.  Jeśli planujesz do wysyłania dużej ilości danych historycznych do TSI i nie przekazać lub tworzenie danych referencyjnych w TSI pierwszy, konieczne może być ponownie pracy (wskazówki, nie fun).  

Aby dowiedzieć się więcej na temat sposobu tworzenia, przekazywanie i zarządzać danymi odwołania w TSI, przejdź do naszego *danych referencyjnych* dokumentacji [dokumentacji](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii biznesowa
Jako usługi Azure czas serii Insights udostępnia wysokiej dostępności (HA), za pomocą zwolnienia na poziomie region platformy Azure, bez konieczności wykonywania dodatkowych działań wymaganych przez to rozwiązanie. Platforma Microsoft Azure obejmuje również funkcje ułatwiające tworzenie rozwiązań z możliwości odzyskiwanie po awarii lub dostępności między regionu. Jeśli chcesz zapewnić globalnych, region między wysoką dostępność dla urządzeń lub użytkowników, korzystać z tych funkcji odzyskiwania po awarii usługi Azure. Artykuł [wskazówki techniczne ciągłości biznesowej Azure](../resiliency/resiliency-technical-guidance.md) opisuje wbudowane funkcje w systemie Azure ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii. [Odzyskiwania po awarii i wysoką dostępność aplikacji Azure] [odzyskiwania po awarii i wysoką dostępność aplikacji Azure] dokument zawiera wskazówki architektura strategie aplikacjami platformy Azure w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii.

Czas serii Insights nie ma firmowych wbudowanych odzyskiwania po awarii (BCDR).  Jednak klientów, które wymagają BCDR, nadal można zaimplementować strategię odzyskiwania. Utworzyć drugi środowisko czasu serii wgląd w kopii zapasowej region platformy Azure i wysyłania zdarzeń do tego środowiska dodatkowej ze źródła zdarzeń podstawowego, wykorzystaniu drugi dedykowanej grupy klientów oraz wskazówki BCDR źródła zdarzeń.  

1.  Utwórz środowisko w regionie drugiego.  Więcej informacji na temat tworzenia środowiska czasu serii Insights [tutaj](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-get-started).
2.  Tworzenie drugiej grupy konsumentów dedykowanych źródła zdarzeń i połącz źródła zdarzeń do nowego środowiska.  Pamiętaj wyznaczyć grupy odbiorców drugiego, dedykowanego.  Więcej informacji na ten temat można znaleźć, wykonując jedną [dokumentacji Centrum IoT](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) lub [dokumentacji Centrum zdarzeń](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access).
3.  Jeżeli regionu podstawowego przestaną działać podczas zdarzenia po awarii, należy przełączyć operacje tworzenia kopii zapasowej środowiska Insights serii czasu.  

Aby dowiedzieć się więcej o zasadach BCDR Centrum IoT, head [tutaj](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-ha-dr).  Aby dowiedzieć się więcej o zasadach BCDR Centrum zdarzeń, head [tutaj](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-geo-dr).  

## <a name="next-steps"></a>Kolejne kroki
- [Jak dodać źródła zdarzenia Centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Jak dodać źródła zdarzenia Centrum IoT](time-series-insights-how-to-add-an-event-source-iothub.md)
