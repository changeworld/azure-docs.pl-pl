---
title: Monitorowanie aplikacji — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować aplikacje w usłudze Azure App Service przy użyciu witryny Azure portal.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a5d4d13d8e60cd7f273363a9bc385098e15cbb71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832594"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorowanie aplikacji w usłudze Azure App Service
[Usługa Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) zapewnia wbudowaną funkcję monitorowania dla aplikacji sieci web, mobilnych zaplecza i aplikacji API apps w [witryny Azure portal](https://portal.azure.com).

W witrynie Azure portal możesz przejrzeć *przydziały* i *metryki* dla aplikacji, przejrzyj plan usługi App Service, a automatyczne konfigurowanie *alerty* i *skalowanie* opartych na metryki.

## <a name="understand-quotas"></a>Omówienie limitów przydziału

Aplikacje, które są hostowane w usłudze App Service są podlegają pewnym ograniczeniom na zasoby, których mogą używać. Limity są definiowane przez plan usługi App Service, który jest skojarzony z aplikacją.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Jeśli aplikacja jest hostowana w *bezpłatna* lub *Shared* limity dotyczące zasobów, które aplikacja może używać planu są definiowane przez limity przydziału.

Jeśli aplikacja jest hostowana w *podstawowe*, *standardowa*, lub *Premium* planu, limity dotyczące zasobów, które mogą używać są ustawiane przez *rozmiar* () Mały, Średni, duży) i *liczba wystąpień* (1, 2, 3 i tak dalej) planu usługi App Service.

Limity przydziału dla aplikacji w warstwie bezpłatna lub współdzielona są:

| Przydział | Opis |
| --- | --- |
| **Procesor (krótki)** | Moc procesora CPU dozwolone dla tej aplikacji w 5 minut. Ten limit przydziału resetuje co pięć minut. |
| **Procesor (dzień)** | Całkowita moc procesora CPU dozwolone dla tej aplikacji w ciągu dnia. Ten limit przydziału resetowany co 24 godziny, o północy czasu UTC. |
| **Pamięć** | Całkowita ilość pamięci, dozwolone dla tej aplikacji. |
| **Bandwidth** | Suma przepustowości wychodzącej dozwolone dla tej aplikacji w ciągu dnia. Ten limit przydziału resetowany co 24 godziny, o północy czasu UTC. |
| **Filesystem** | Całkowita ilość miejsca, dozwolone. |

Tylko limitu przydziału stosowane w aplikacjach, które są hostowane w *podstawowe*, *standardowa*, i *Premium* plany to system plików.

Aby uzyskać więcej informacji o określonych przydziały, limity i dostęp do różnych jednostek SKU usługi aplikacji funkcji, zobacz [limity usług subskrypcji platformy Azure](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Wymuszanie przydziałów

Jeśli aplikacja przekracza *Procesora (krótki)*, *Procesora (dzień)*, lub *przepustowości* limit przydziału, aplikacja zostanie zatrzymana, dopóki nie powoduje zresetowanie limitu przydziału. W tym czasie wszystkie żądania przychodzące powodować wystąpienie błędu HTTP 403.

![komunikat o błędzie 403][http403]

Jeśli zostanie przekroczony przydział pamięci aplikacji, ponownym uruchomieniu aplikacji.

Jeśli zostanie przekroczony limit przydziału systemu plików, wszelkie zapisu operacji zakończy się niepowodzeniem. Zapisu niepowodzeń operacji obejmują wszelkie operacje zapisu w dziennikach.

Można zwiększyć lub usunąć limity przydziału z aplikacji, uaktualniając plan usługi App Service.

## <a name="understand-metrics"></a>Omówienie metryk

Metryki zawierają informacje o aplikacji lub usługi App Service zachowanie tego planu.

Dla aplikacji są dostępne metryki:

| Metryka | Opis |
| --- | --- |
| **Średni czas odpowiedzi** | Średni czas dla aplikacji do obsługi żądań, w milisekundach. |
| **Średni zestaw roboczy pamięci** | Średnia ilość pamięci używanej przez aplikację, w megabajtach (MiB). |
| **Połączenia** | Liczba powiązanych sockets istniejących w piaskownicy (w3wp.exe i jego procesów podrzędnych).  Gniazdo powiązanej jest tworzony przez wywołanie bind()/connect() interfejsów API i pozostaje do czasu zamknięcia wymienionego gniazda z CloseHandle()/closesocket(). |
| **Czas procesora CPU** | Moc procesora CPU, używane przez aplikację, w ciągu kilku sekund. Aby uzyskać więcej informacji na temat tej metryki, zobacz [procent vs Procesor czas procesora CPU](#cpu-time-vs-cpu-percentage). |
| **Aktualna liczba zestawów** | Bieżąca liczba zestawów są ładowane we wszystkich domen aplikacji w tej aplikacji. |
| **Dane w** | Ilość przychodzącego przepustowości aplikacji MiB. |
| **Dane wyjściowe** | Ilość przepustowości wychodzącej przez aplikację, w MiB. |
| **Velikost haldy 0 wyrzucania elementów bezużytecznych** | Liczba przypadków, gdy obiekty generacji 0 są pamięci zbierane od momentu rozpoczęcia procesu aplikacji. Wyższe generowania wykazów globalnych obejmują wszystkie wykazów globalnych z niższych generacji.|
| **Gen 1 wyrzucania elementów bezużytecznych** | Liczba przypadków, gdy obiekty generacji 1 są pamięci zbierane od momentu rozpoczęcia procesu aplikacji. Wyższe generowania wykazów globalnych obejmują wszystkie wykazów globalnych z niższych generacji.|
| **Odzyskiwanie obiektów pokolenia 2** | Liczba przypadków, gdy obiekty generacji 2 są pamięci zbierane od momentu rozpoczęcia procesu aplikacji.|
| **Liczba dojść** | Całkowita liczba dojść otwartych przez proces aplikacji.|
| **Http 2xx** | Liczba żądań skutkuje ≥ kod stanu HTTP 200, ale < 300. |
| **Http 3xx** | Liczba żądań, co kod stanu HTTP ≥ 300, ale < 400. |
| **Http 401** | Liczba żądań, co kod stanu HTTP 401. |
| **Http 403** | Liczba żądań, co kod stanu HTTP 403. |
| **Http 404** | Liczba żądań, co kod stanu HTTP 404. |
| **HTTP 406** | Liczba żądań, co kod stanu HTTP 406. |
| **Http 4xx** | Liczba żądań skutkuje ≥ kod stanu HTTP 400, ale < 500. |
| **Błędy serwera HTTP** | Liczba żądań skutkuje ≥ kod stanu HTTP 500, ale < 600. |
| **We/Wy inne bajty na sekundę** | Szybkość, w którym proces aplikacji wysyła bajty operacji We/Wy, które nie wymagają dane, takie jak operacje kontroli.|
| **Operacje We/Wy innych na sekundę** | Szybkość, w którym proces aplikacji jest wykonywane operacje We/Wy, które nie są do odczytu ani zapisu.|
| **Bajty odczytu We/Wy na sekundę** | Szybkość, w którym proces aplikacji odczytywanie bajtów z operacji We/Wy.|
| **Operacje odczytu We/Wy na sekundę** | Szybkość, z jaką proces aplikacji wysyła odczytać operacji We/Wy.|
| **We/Wy zapisu bajtów na sekundę** | Szybkość, w którym proces aplikacji zapisuje bajty w operacji We/Wy.|
| **We/Wy zapisu na sekundę** | Szybkość, w którym proces aplikacji jest wykonywane operacje We/Wy zapisu.|
| **Zestaw roboczy pamięci** | Bieżąca ilość pamięci używanej przez aplikację, w MiB. |
| **Bajty prywatne** | Licznik Bajty prywatne wskazuje bieżący rozmiar w bajtach pamięci przydzielonej przez proces aplikacji, nie może być współużytkowana z innymi procesami.|
| **Żądania** | Całkowita liczba żądań, niezależnie od ich wynikowy kod stanu HTTP. |
| **Żądania w kolejce aplikacji** | Liczba żądań w kolejce żądań aplikacji.|
| **Liczba wątków** | Liczba aktywnych wątków w procesie aplikacji.|
| **Łączna liczba domen aplikacji** | Bieżąca liczba domen aplikacji są ładowane w tej aplikacji.|
| **Łączna liczba domen aplikacji zwolnione** | Całkowita liczba domen aplikacji usunięty od czasu uruchomienia aplikacji.|


Plan usługi App Service są dostępne metryki:

> [!NOTE]
> Metryki planu usługi App Service są dostępne tylko w przypadku planów w ramach *podstawowe*, *standardowa*, i *Premium* warstw.
> 

| Metryka | Opis |
| --- | --- |
| **Procent użycia procesora CPU** | Średnie wykorzystanie Procesora, używane we wszystkich wystąpieniach planu. |
| **Procent pamięci** | Średnia pamięć używana we wszystkich wystąpieniach planu. |
| **Dane w** | Średnia przepustowość przychodzące używane we wszystkich wystąpieniach planu. |
| **Dane wyjściowe** | Średnia wychodzące przepustowość we wszystkich wystąpieniach planu. |
| **Długość kolejki dysku** | Średnia liczba zarówno żądania odczytu i zapisu umieszczonych w kolejce magazynu. Długość kolejki dysku jest wskazanie aplikację, która może spowalniać z powodu nadmiernego We/Wy dysku. |
| **Długość kolejki http** | Średnia liczba żądań HTTP, które musiały znajdują się w kolejce przed są spełnione. Długość kolejki HTTP dużej lub rosnącej ilości jest objaw programu z dużym obciążeniem. |

### <a name="cpu-time-vs-cpu-percentage"></a>Procent procesora CPU w programie vs czasu procesora CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Istnieją dwie metryki, które odzwierciedlają użycie procesora CPU:

**Czas procesora CPU**: Przydatne w przypadku aplikacji hostowanej w bezpłatnej lub udostępniony w przypadku planów, ponieważ ich przydziałów jest zdefiniowana w ciągu kilku minut Procesora używanych przez aplikację.

**Procent użycia procesora CPU**: Przydatne dla aplikacji hostowanych w planach podstawowa, standardowa i Premium, ponieważ mogą one być skalowana w poziomie. Procent użycia procesora CPU jest dobrym wskaźnikiem ogólne użycie we wszystkich wystąpieniach.

## <a name="metrics-granularity-and-retention-policy"></a>Metryki stopień szczegółowości i zasady przechowywania
Metryki dla aplikacji, a plan usługi app service są rejestrowane, a następnie agregowane przez usługę, za pomocą następujących stopniach szczegółowości i zasad przechowywania:

* **Minuty** stopień szczegółowości metryki są przechowywane przez 30 godzin.
* **Godzina** stopień szczegółowości metryki są przechowywane przez 30 dni.
* **Dzień** stopień szczegółowości metryki są przechowywane przez 30 dni.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorowanie przydziałów i metryki w witrynie Azure portal
Aby przejrzeć stan różnych przydziałów i metryki, które mają wpływ na aplikację, przejdź do [witryny Azure portal](https://portal.azure.com).

![Wykres przydziały w witrynie Azure portal][quotas]

Aby znaleźć przydziały, wybierz pozycję **ustawienia** > **przydziały**. Na wykresie możesz przejrzeć: 
1. Nazwa limitu przydziału.
1. Jego interwał resetowania.
1. Bieżący limit.
1. Jej bieżącą wartość.

![Wykresu metryki w witrynie Azure portal][metrics] dostęp można uzyskać metryki bezpośrednio z **zasobów** strony. Aby dostosować wykres: 
1. Wybierz wykres.
1. Wybierz **Edytuj wykres**.
1. Edytuj **zakres czasu**.
1. Edytuj **typ wykresu**.
1. Edytuj metryk, które mają być wyświetlane.  

Aby dowiedzieć się więcej o wskaźnikach, zobacz [monitorowania metryk usług](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alerty i automatyczne skalowanie
Metryki dla aplikacji lub plan usługi App Service można podłączonymi do alertów. Aby uzyskać więcej informacji, zobacz [Receive alert notifications](../monitoring-and-diagnostics/insights-alerts-portal.md) (Otrzymywanie powiadomień o alertach).

Aplikacje usługi App Service, hostowane w skalowania automatycznego w pomocy technicznej plany podstawowa, standardowa lub Premium usługi App Service. Z funkcją automatycznego skalowania można skonfigurować zasady, które monitorują metryki planu usługi App Service. Zasady można zwiększyć lub zmniejszyć liczbę wystąpień, co może zapewnić dodatkowe zasoby, zgodnie z potrzebami. Reguły może również pomóc zaoszczędzić pieniądze, gdy aplikacja jest nadmiernie aprowizowane.

Aby uzyskać więcej informacji na temat automatycznego skalowania, zobacz [sposób skalowania](../monitoring-and-diagnostics/insights-how-to-scale.md) i [najlepsze rozwiązania dotyczące usługi Azure Monitor autoscaling](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png