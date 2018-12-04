---
title: Planowanie środowiska usługi Azure Time Series Insights (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Planowanie środowiska usługi Azure Time Series Insights (wersja zapoznawcza)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 438d71997d2c92e377cd068615d274af6b8b5edb
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856482"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planowanie środowiska usługi Azure Time Series Insights (wersja zapoznawcza)

W tym artykule opisano najlepsze rozwiązania do planowania i rozpocząć pracę, szybko za pomocą usługi Azure Time Series Insights (wersja zapoznawcza).

## <a name="best-practices-for-planning-and-preparation"></a>Najlepsze rozwiązania dotyczące planowania i przygotowania

Należy dysponować następującymi elementami gotowe przed rozpoczęciem:

* Zidentyfikowaniu swoje **identyfikatory serii czasu**
* Masz usługi **sygnatura czasowa** właściwość gotowe
* Gdy masz utworzoną swoje **modelu szeregów czasowych**
* Możesz dowiedzieć się, jak wysyłać zdarzenia, które są wydajnie nieznormalizowany w formacie JSON

O tych elementów gotowe pomaga uprościć planowania i przygotowywania. Ponadto dobrze jest Planuj z wyprzedzeniem i ustalić biznesowej odzyskiwania po awarii (BCDR) wymaga przed utworzeniem wystąpienia (a nie później). Sposób wcześniejsze pomaga upewnić się, że wystąpienie przechowywany jest gotowa.

> [!TIP]
> Skonfiguruj środowisko do swoich potrzeb BCDR wcześniej, a nie w przypadku, po utworzeniu wystąpienia usługi.

Azure TSI (wersja zapoznawcza) wykorzystuje model biznesowy zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat opłat i pojemności, zobacz [cennik usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights).

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Konfigurowanie właściwości identyfikatorów serii czasu i znacznik czasu:

Aby utworzyć nowe środowisko TSI, wybierz **identyfikator serii czasu**. Spowoduje to więc działa jako partycji logicznej dla swoich danych. Jak wspomniano, upewnij się, że masz swoje **identyfikatory serii czasu** gotowe.

> [!IMPORTANT]
> **Czas serii identyfikatory** są **niezmienialnych** i **nie można później zmienić**. Sprawdź każdy przed ostatecznego wyboru, a pierwszym użyciu.

Możesz wybrać maksymalnie **trzy** klucze (3), aby jednoznacznie odróżnienia Twoich zasobów. Odczyt [najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md) artykuł, aby uzyskać więcej informacji.

Każde źródło zdarzenia ma opcjonalny **sygnatura czasowa** właściwość, która jest używana do źródła śledzenia zdarzeń, wraz z upływem czasu. **Sygnatura czasowa** wartości jest rozróżniana wielkość liter i musi być sformatowany do poszczególnych specyfikacji każdego źródła zdarzeń.

> [!TIP]
> Sprawdź wymagania dotyczące formatowania i analizowania źródeł zdarzeń.

Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** zdarzenia źródłowego jest używana jako zdarzenie **sygnatura czasowa**. W przypadku wysyłania danych historycznych lub wsadowej zdarzeń, prawdopodobnie zauważysz dostosowywanie **sygnatura czasowa** właściwość, aby być bardziej przydatne niż domyślna **czas umieścić w kolejce zdarzenia**. Aby uzyskać więcej informacji, przeczytaj o [sposób dodawania źródła zdarzeń w usłudze IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).  

## <a name="understand-the-time-series-model"></a>Zrozumienie szeregów czasowych modelu

Teraz możesz skonfigurować środowisko usługi TSI **modelu szeregów czasowych**. Nowy model sprawia, że można łatwo znaleźć i analizowanie danych IoT. Nie tak, należy włączyć nadzorowaną, konserwacja i Wzbogacanie danych szeregów czasowych i pomaga przygotować przygotowane zestawów danych. Model wykorzystuje **identyfikatory serii czasu**, które mapowania na wystąpienie kojarzenie unikatowy zasób z zmienne (znanych jako typy) i hierarchii. Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

Model jest dynamiczny, dzięki czemu mogą wbudowane w dowolnym momencie. Można jednak szybciej rozpocząć pracę, jeśli jego została skompilowana i przekazana przed po raz pierwszy do wypychania danych do usługi TSI. Aby utworzyć model, zapoznaj się z [modelu szeregów czasowych](./time-series-insights-update-tsm.md) artykułu.

Dla wielu klientów, oczekujemy, że **modelu szeregów czasowych** do mapowania na istniejący model zasobu lub system ERP i przeniesieniu jej już w miejscu. Dla tych klientów, którzy nie mają istniejącego modelu, jest gotowych komfortu [podane](https://github.com/Microsoft/tsiclient) uzyskać szybko uruchamiać wdrożenia.

## <a name="shaping-your-events"></a>Kształtowanie zdarzeń

Jest to ważne, aby sprawdzić sposób wysyłania zdarzeń do usługi TSI. W idealnym przypadku zdarzenia będą nieznormalizowane również i wydajne.

Regułą:

* Metadane powinny być przechowywane w swojej **modelu szeregów czasowych**
* **Tryb serii czasu;**  wystąpienia pola i zdarzenia powinny tylko niezbędne informacje takie jak:
  * **Identyfikator serii czasu**
  * **Znacznik czasu:**

Przegląd [jak zdarzenia kształtu](./time-series-insights-update-how-to-shape-events.md) artykułu bardziej szczegółowo.

## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii biznesowych

Usługi platformy Azure jest usługi wysokiej dostępności (HA) przy użyciu nadmiarowość na poziomie regionu świadczenia usługi Azure TSI. Aby użyć tych funkcje związane jest wymagana żadna konfiguracja. Platforma Microsoft Azure zawiera funkcje ułatwiające tworzenie rozwiązań za pomocą funkcji odzyskiwania po awarii lub dostępności między regionami. Jeśli chcesz zapewnić globalnego, między regionami HA dla urządzeń lub użytkowników, korzystać z tych funkcji odzyskiwania po awarii z platformy Azure. Artykuł [wskazówek technicznych ciągłości biznesowej Azure](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance) opis wbudowanych funkcji na platformie Azure zapewniającej ciągłość biznesową i odzyskiwanie po awarii. [Po awarii](https://docs.microsoft.com/azure/architecture/resiliency/index) odzyskiwania i wysoka dostępność dla aplikacji platformy Azure, dokument zawiera wskazówki dotyczące architektury, strategii dla aplikacji platformy Azure w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii.

> [!NOTE]
> Usługa Azure Time Series Insights nie ma wbudowanych BCDR.
> Domyślnie usługi Azure Storage, Azure IoT Hub i Event Hubs mają wbudowane odzyskiwania.

Aby dowiedzieć się więcej:

* Przeczytaj o [nadmiarowości magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* Przeczytaj o [odzyskiwania po awarii usługi IoT Hub HA](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).
* Przeczytaj o [zasad Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

Niemniej jednak klienci, którzy wymagają strategii BCDR nadal można zaimplementować strategię odzyskiwania przez utworzenie drugiej środowiska TSI w regionie świadczenia usługi Azure backup. Klienci wysyłanie zdarzeń do tej dodatkowej środowiska ze źródła zdarzeń podstawowy drugiego dedykowanej grupy klientów i wskazówki dotyczące strategii BCDR tego źródła zdarzeń.

Określone kroki, aby osiągnąć ten cel, są następujące:

1. Należy utworzyć środowisko, w drugim regionie. Przeczytaj o [środowisk TSI](./time-series-insights-get-started.md).
1. Utwórz drugi dedykowanej grupy klientów dla źródła zdarzeń i łączenie źródła zdarzeń do nowego środowiska. Pamiętaj wyznaczyć grupy odbiorców drugi, dedykowanych. Dowiedz się więcej z [dokumentacja usługi IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) lub [dokumentacja Centrum zdarzeń](./time-series-insights-data-access.md).
1. Jeśli swój region podstawowy został zmieniony podczas zdarzenia po awarii, należy przekierować operacje tworzenia kopii zapasowej środowiska TSI.

> [!IMPORTANT]
> * Należy zauważyć, że opóźnienie mogą się pojawić w przypadku pracy awaryjnej.
> * Tryb failover mogą również spowodować wzrost mometary podczas przetwarzania komunikatu, ponieważ operacje są przekierowane.
> * Aby uzyskać więcej informacji, zobacz [łagodzenia opóźnienia w usłudze TSI](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Kolejne kroki

Odczyt [Azure TSI (wersja zapoznawcza), magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).