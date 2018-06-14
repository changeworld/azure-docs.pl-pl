---
title: Monitorowanie aplikacji w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować aplikacje w usłudze Azure App Service przy użyciu portalu Azure.
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
ms.openlocfilehash: fdc4329806d416811352d0d4dbc8dd3bce25aa0b
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29573807"
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Porady: monitorować aplikacje w usłudze aplikacji Azure
[Usługi aplikacji](http://go.microsoft.com/fwlink/?LinkId=529714) oferuje wbudowane funkcje monitorowania w [portalu Azure](https://portal.azure.com).
Azure portal zawiera przegląd możliwości **przydziały** i **metryki** dla aplikacji, a także plan usługi aplikacji, ustawianie **alerty** i nawet **skalowania**  automatycznie w oparciu o te metryki.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Opis przydziałów i metryki
### <a name="quotas"></a>Przydziały
Aplikacje hostowane w usłudze App Service podlegają niektórych *limity* z użyciem zasobów. Wynika z limitów **planu usługi aplikacji** skojarzone z aplikacją.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Jeśli aplikacja jest hostowana w **wolne** lub **Shared** planowanie, a następnie wynika z ograniczenia zasobów, aplikacja może używać **przydziały**.

Jeśli aplikacja jest hostowana w **podstawowe**, **standardowe** lub **Premium** planowanie, a następnie limity mogą używać zasoby są ustawiane przez **rozmiar**(Mały, Średni, duża) i **wystąpienia licznika** (1, 2, 3,...) z **planu usługi aplikacji**.

**Przydziały** dla **wolne** lub **Shared** aplikacje są:

* **CPU(Short)**
  * Ilość Procesora dozwolone dla tej aplikacji w 5 minut. Ten limit przydziału resetuje co pięć minut.
* **CPU(Day)**
  * Całkowita ilość Procesora dozwolone dla tej aplikacji w ciągu jednego dnia. Ten limit przydziału resetuje co 24 godziny, o północy czasu UTC.
* **Pamięci**
  * Całkowita ilość pamięci dozwolone dla tej aplikacji.
* **Bandwidth**
  * Całkowita ilość przepustowości wychodzącej dozwolone dla tej aplikacji w ciągu jednego dnia.
    Ten limit przydziału resetuje co 24 godziny, o północy czasu UTC.
* **Filesystem**
  * Całkowita liczba dozwoloną ilość pamięci masowej.

Tylko przydział stosowane w aplikacjach hostowanych na **podstawowe**, **standardowe**, i **Premium** jest planów **Filesystem**.

Więcej informacji na temat określonych przydziałów, ograniczenia i funkcje dostępne dla różnych jednostki SKU usługi aplikacji można znaleźć tutaj: [ograniczenia usługi subskrypcji platformy Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Wymuszanie przydziałów
Jeśli aplikacja przekracza **procesora CPU (short)**, **procesora CPU (dzień)**, lub **przepustowości** przydziału następnie aplikacja jest zatrzymana, aż do limitu przydziału resetuje. W tym czasie wszystkie żądania przychodzące spowodować **HTTP 403**.
![][http403]

Jeśli aplikacja **pamięci** przekroczony przydział, a następnie ponownym uruchomieniu aplikacji.

Jeśli **Filesystem** przekroczony przydział, a następnie żadnego zapisu operacja kończy się niepowodzeniem, która obejmuje wszystkie zapisy w dziennikach.

Przydziały można zwiększyć lub usunięte z aplikacji, uaktualniając plan usługi aplikacji.

### <a name="metrics"></a>Metryki
**Metryki** zawierają informacje o aplikacji, lub zachowanie planu usługi aplikacji.

Aby uzyskać **aplikacji**, są dostępne metryki:

* **Średni czas odpowiedzi**
  * Średni czas dla aplikacji do obsługi żądań w ms.
* **Pamięć średni zestaw roboczy**
  * Średnia ilość pamięci w bazach MIB używany przez aplikację.
* **Czas procesora CPU**
  * Ilość Procesora w sekundach używane przez aplikację. Aby uzyskać więcej informacji dotyczących tej metryki, zobacz: [procent vs Procesora czas procesora CPU](#cpu-time-vs-cpu-percentage)
* **Dane w**
  * Ilość przychodzącego przepustowości przez aplikację w bazach MIB.
* **Dla danych wychodzących**
  * Liczba wychodzących przepustowości przez aplikację w bazach MIB.
* **Http 2xx**
  * Liczba żądań, co powoduje kod stanu HTTP > = 200, ale < 300.
* **Http 3xx**
  * Liczba żądań, co powoduje kod stanu HTTP > = 300, ale < 400.
* **HTTP 401**
  * Liczba żądań, co powoduje kod stanu HTTP 401.
* **HTTP 403**
  * Liczba żądań, co powoduje kod stanu HTTP 403.
* **HTTP 404**
  * Liczba żądań, co powoduje kod stanu HTTP 404.
* **HTTP 406**
  * Liczba żądań, co powoduje kod stanu HTTP 406.
* **Http 4xx**
  * Liczba żądań, co powoduje kod stanu HTTP > = 400, ale < 500.
* **Błędy HTTP serwera**
  * Liczba żądań, co powoduje kod stanu HTTP > = 500, ale < 600.
* **Zestaw roboczy pamięci**
  * Bieżąca ilość pamięci użytej przez aplikację w bazach MIB.
* **żądania**
  * Całkowita liczba żądań, niezależnie od ich wynikowy kod stanu HTTP.

Aby uzyskać **planu usługi aplikacji**, są dostępne metryki:

> [!NOTE]
> Metryki planu usługi aplikacji są dostępne tylko dla planów w **podstawowe**, **standardowe**, i **Premium** warstw.
> 
> 

* **Procent użycia procesora CPU**
  * Średnie wykorzystanie Procesora, używana we wszystkich wystąpieniach planu.
* **Procent pamięci**
  * Średnia pamięć używana we wszystkich wystąpieniach planu.
* **Dane w**
  * Średnia przepustowość przychodzące używane we wszystkich wystąpieniach planu.
* **Dla danych wychodzących**
  * Średnia przepustowość we wszystkich wystąpieniach planu wychodzące.
* **Długość kolejki dysku**
  * Średnia liczba zarówno żądania odczytu i zapisu umieszczonych w kolejce w magazynie. Długość kolejki dysku będzie wskazywać aplikacji, która może być spowolnieniem z powodu nadmiernego We/Wy dysku.
* **Długość kolejki http**
  * Średnia liczba żądań HTTP, które musiały znajdują się w kolejce przed są spełnione. Wysoki lub zwiększa długość kolejki HTTP jest symptomem planu obciążona.

### <a name="cpu-time-vs-cpu-percentage"></a>Wartość procentowa vs Procesora czas Procesora
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Istnieją dwa metryk, które odzwierciedlają użycie procesora CPU. **Czas procesora CPU** i **procent użycia procesora CPU**

**Czas procesora CPU** jest przydatne dla aplikacji hostowanej w **wolne** lub **Shared** plany, ponieważ ich przydziałów jest zdefiniowana w minutach procesora CPU używanych przez aplikację.

**Procent użycia procesora CPU** jest przydatne dla aplikacji hostowanej w **podstawowe**, **standardowe**, i **premium** plany, ponieważ mogą one być skalowana w poziomie. Procent użycia procesora CPU jest dobrym wskaźnikiem ogólne wykorzystanie we wszystkich wystąpieniach.

## <a name="metrics-granularity-and-retention-policy"></a>Poziom szczegółowości metryki i zasady przechowywania
Metryki dla aplikacji i plan usługi aplikacji są rejestrowane i agregowane przez usługę za pomocą zasad przechowywania i szczegółowości następujące:

* **Minuta** metryki szczegółowości są zachowywane dla **30 godzin**
* **Godzina** metryki szczegółowości są zachowywane dla **30 dni**
* **Dzień** metryki szczegółowości są zachowywane dla **30 dni**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorowanie przydziałów i metryki w portalu Azure.
Można sprawdzić stan różnych **przydziały** i **metryki** mające wpływ na aplikację w [portalu Azure](https://portal.azure.com).

![][quotas]
**Przydziały** znajduje się w obszarze Ustawienia >**przydziały**. Środowiska użytkownika umożliwia przeglądanie: (1) nazwa przydziałów, (2) jego interwał resetowania, (3) jego bieżący limit i (4) bieżącą wartość.

![][metrics]
**Metryki** mogą uzyskiwać bezpośrednio na stronie zasobów. Można również dostosować wykres przez: (1) **kliknij** i wybierz (2) **Edytuj wykres**.
W tym miejscu można zmienić (3) **zakres czasu**4 **typ wykresu**i 5 **metryki** do wyświetlenia.  

Dowiedz się więcej o metryki tutaj: [monitorować metryki usługi](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alerty i skalowania automatycznego
Metryki planu aplikacji lub usługi aplikacji może podłączonymi do alertów. Aby uzyskać więcej informacji, zobacz [otrzymywać powiadomienia o alertach](../monitoring-and-diagnostics/insights-alerts-portal.md).

Aplikacje usługi aplikacji hostowanej w podstawowa, standardowa lub premium Obsługa planów usługi aplikacji **skalowania automatycznego**. Funkcja automatycznego skalowania umożliwia konfigurowanie reguł, które monitorują metryki planu usługi aplikacji. Zasady można zwiększyć lub zmniejszyć liczbę wystąpień, zapewniając dodatkowe zasoby, zgodnie z potrzebami. Reguły może również pomóc oszczędności, gdy aplikacja jest też obsługiwana. Dowiedz się więcej o automatyczne skalowanie: [sposobu skalowania](../monitoring-and-diagnostics/insights-how-to-scale.md) i tutaj [najlepszych rozwiązań dotyczących skalowania automatycznego Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://azure.microsoft.com/try/app-service/) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację sieci Web o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
