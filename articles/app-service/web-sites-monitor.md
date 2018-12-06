---
title: Monitorowanie aplikacji w usłudze Azure App Service | Dokumentacja firmy Microsoft
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
ms.date: 11/28/2017
ms.author: byvinyal
ms.openlocfilehash: 6334b4cc50bfa6dca709fdc9d65938f0fec3ad1c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956774"
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Porady: monitorowanie aplikacji w usłudze Azure App Service
[Usługa App Service](https://go.microsoft.com/fwlink/?LinkId=529714) udostępnia wbudowane funkcje monitorowania w [witryny Azure portal](https://portal.azure.com).
Witryny Azure portal umożliwia przeglądanie **przydziały** i **metryki** dla aplikacji, a także plan usługi App Service, konfigurowanie **alerty** i nawet **skalowania**  automatycznie w oparciu o te metryki.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Opis limitów przydziału i metryki
### <a name="quotas"></a>Przydziały
Aplikacje hostowane w usłudze App Service jest zależna od pewnych *limity* zasobów mogą używać. Limity są definiowane przez **planu usługi App Service** skojarzone z aplikacją.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Jeśli aplikacja jest hostowana w **bezpłatna** lub **Shared** planu, a następnie limitów zasobów aplikacja może używać są definiowane przez **przydziały**.

Jeśli aplikacja jest hostowana w **podstawowe**, **standardowa** lub **Premium** planu, a następnie limitów zasobów mogą używać są ustawiane przez **rozmiar**(Mały, Średni, duży) i **liczba wystąpień** (1, 2, 3,...) z **planu usługi App Service**.

**Przydziały** dla **bezpłatna** lub **Shared** aplikacje są:

* **CPU(Short)**
  * Moc procesora CPU dozwolone dla tej aplikacji w 5 minut. Ten limit przydziału resetuje co pięć minut.
* **CPU(Day)**
  * Całkowita moc procesora CPU dozwolone dla tej aplikacji w ciągu dnia. Ten limit przydziału resetowany co 24 godziny, o północy czasu UTC.
* **Pamięć**
  * Całkowita ilość pamięci, dozwolone dla tej aplikacji.
* **Bandwidth**
  * Suma przepustowości wychodzącej dozwolone dla tej aplikacji w ciągu dnia.
    Ten limit przydziału resetowany co 24 godziny, o północy czasu UTC.
* **Filesystem**
  * Łączna liczba dozwoloną ilość pamięci masowej.

Tylko limitu przydziału stosowane w aplikacjach hostowanych w **podstawowe**, **standardowa**, i **Premium** jest plany **systemu plików**.

Więcej informacji na temat określonych przydziały, limity i dostęp do różnych jednostek SKU usługi aplikacji funkcji można znaleźć tutaj: [limity usług subskrypcji platformy Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Wymuszanie przydziałów
Jeśli aplikacja przekracza **Procesora (krótki)**, **Procesora (dzień)**, lub **przepustowości** przydziału następnie aplikacji zostanie zatrzymana, dopóki nie powoduje zresetowanie limitu przydziału. W tym czasie wszystkie żądania przychodzące spowodować **HTTP 403**.
![][http403]

Jeśli aplikacja **pamięci** limit przydziału zostanie przekroczony, a następnie ponownym uruchomieniu aplikacji.

Jeśli **Filesystem** limit przydziału zostanie przekroczony, a następnie wszelkie zapisu operacji zakończy się niepowodzeniem, który zawiera wszelkie operacje zapisu w dziennikach.

Limity przydziału można zwiększyć lub usunięty z aplikacji, uaktualniając plan usługi App Service.

### <a name="metrics"></a>Metryki
**Metryki** zawierają informacje o aplikacji lub zachowanie planu usługi App Service.

Aby uzyskać **aplikacji**, są dostępne metryki:

* **Średni czas odpowiedzi**
  * Średni czas dla aplikacji do obsługi żądań w ms.
* **Średni zestaw roboczy pamięci**
  * Średnia ilość pamięci w bazach MIB używanych przez aplikację.
* **Czas procesora CPU**
  * Moc procesora CPU w ciągu kilku sekund, używane przez aplikację. Aby uzyskać więcej informacji na temat tej metryki, zobacz: [procent vs Procesor czas procesora CPU](#cpu-time-vs-cpu-percentage)
* **Dane w**
  * Ilość użytej przez aplikację w bazach MIB przychodzących przepustowości.
* **Dane wyjściowe**
  * Ilość użytej przez aplikację w bazach MIB wychodzących przepustowości.
* **Http 2xx**
  * Liczba żądań, co kod stanu HTTP > = 200, lecz < 300.
* **Http 3xx**
  * Liczba żądań, co kod stanu HTTP > = 300, lecz < 400.
* **HTTP 401**
  * Liczba żądań, co kod stanu HTTP 401.
* **HTTP 403**
  * Liczba żądań, co kod stanu HTTP 403.
* **HTTP 404**
  * Liczba żądań, co kod stanu HTTP 404.
* **HTTP 406**
  * Liczba żądań, co kod stanu HTTP 406.
* **Http 4xx**
  * Liczba żądań, co kod stanu HTTP > = 400, lecz < 500.
* **Błędy serwera HTTP**
  * Liczba żądań, co kod stanu HTTP > = 500, ale < 600.
* **Zestaw roboczy pamięci**
  * Bieżąca ilość pamięci używanej przez aplikację w bazach MIB.
* **Żądania**
  * Całkowita liczba żądań, niezależnie od ich wynikowy kod stanu HTTP.

Aby uzyskać **planu usługi App Service**, są dostępne metryki:

> [!NOTE]
> Metryki planu usługi App Service są dostępne tylko dla planów w ramach **podstawowe**, **standardowa**, i **Premium** warstw.
> 
> 

* **Procent użycia procesora CPU**
  * Średnie wykorzystanie Procesora, używane we wszystkich wystąpieniach planu.
* **Procent pamięci**
  * Średnia pamięć używana we wszystkich wystąpieniach planu.
* **Dane w**
  * Średnia przepustowość przychodzące używane we wszystkich wystąpieniach planu.
* **Dane wyjściowe**
  * Średnia wychodzące przepustowość we wszystkich wystąpieniach planu.
* **Długość kolejki dysku**
  * Średnia liczba zarówno żądania odczytu i zapisu umieszczonych w kolejce magazynu. Długość kolejki dysku jest wskazanie aplikację, która może spowalniać z powodu nadmiernego We/Wy dysku.
* **Długość kolejki http**
  * Średnia liczba żądań HTTP, które musiały znajdują się w kolejce przed są spełnione. Długość kolejki HTTP dużej lub rosnącej ilości jest objaw programu z dużym obciążeniem.

### <a name="cpu-time-vs-cpu-percentage"></a>Procent procesora CPU w programie vs czasu procesora CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Istnieją dwie metryki, które odzwierciedlają użycie procesora CPU. **Czas procesora CPU** i **procent użycia procesora CPU**

**Czas procesora CPU** jest przydatne w przypadku aplikacji hostowanej w **bezpłatna** lub **Shared** plany, ponieważ ich przydziałów jest zdefiniowana w ciągu kilku minut Procesora używanych przez aplikację.

**Procent użycia procesora CPU** jest przydatne w przypadku aplikacji hostowanej w **podstawowe**, **standardowa**, i **premium** plany, ponieważ mogą one być skalowana w poziomie. Procent użycia procesora CPU jest dobrym wskaźnikiem ogólne użycie we wszystkich wystąpieniach.

## <a name="metrics-granularity-and-retention-policy"></a>Poziom szczegółowości metryki i zasad przechowywania
Metryki dla aplikacji i plan usługi app service są rejestrowane, a następnie agregowane przez usługę za pomocą następujących stopniach szczegółowości i zasad przechowywania:

* **Minuty** stopień szczegółowości metryki są przechowywane przez **30 godzin**
* **Godzina** stopień szczegółowości metryki są przechowywane przez **30 dni**
* **Dzień** stopień szczegółowości metryki są przechowywane przez **30 dni**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorowanie przydziałów i metryki w witrynie Azure portal.
Można sprawdzić stan różnych **przydziały** i **metryki** wpływu na aplikację w [witryny Azure portal](https://portal.azure.com).

![][quotas]
**Przydziały** można znaleźć w obszarze Ustawienia >**przydziały**. Środowiska użytkownika umożliwia dokonanie przeglądu: (1) nazwę limity przydziału, (2) jej interwał resetowania (3) jego bieżący limit i (4) bieżącą wartość.

![][metrics]
**Metryki** możliwy bezpośrednio ze strony zasobu. Można również dostosować wykres przez: (1) **kliknij** i (2) wybierz **edytowanie wykresu**.
W tym miejscu możesz zmienić (3) **zakres czasu**, (4) **typ wykresu**i 5 **metryki** do wyświetlenia.  

Dowiedz się więcej o metrykach w tym miejscu: [monitorowania metryk usług](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alerty i automatyczne skalowanie
Metryki dla planu usługi aplikacji lub usługi App Service można podłączonymi do alertów. Aby uzyskać więcej informacji, zobacz [otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-alerts-portal.md).

Aplikacje usługi App Service, hostowane w podstawowa, standardowa lub premium obsługi planów usługi App Service **skalowania automatycznego**. Funkcja automatycznego skalowania umożliwia skonfigurowanie reguł, które monitorują metryki planu usługi App Service. Zasady można zwiększyć lub zmniejszyć liczbę wystąpień, zapewniając dodatkowe zasoby, zgodnie z potrzebami. Reguły może również pomóc zaoszczędzić pieniądze, gdy aplikacja jest nadmiernie aprowizowane. Dowiedz się więcej na temat automatycznego skalowania: [jak skalować](../monitoring-and-diagnostics/insights-how-to-scale.md) i tutaj [najlepsze rozwiązania dotyczące skalowania automatycznego usługi Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://azure.microsoft.com/try/app-service/) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
