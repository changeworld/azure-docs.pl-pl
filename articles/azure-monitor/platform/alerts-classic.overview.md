---
title: Omówienie alertów klasycznych w witrynie Microsoft Azure i monitorze platformy Azure
description: Klasyczne alerty są przestarzałe. Alerty umożliwiają monitorowanie metryk zasobów platformy Azure, zdarzeń lub dzienników i otrzymywać powiadomienia o spełnieniu określonego warunku.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 098efd3075c6b099bdfc925cb4f09163f83532a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668270"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>What are classic alerts in Microsoft Azure? (Czym są alerty klasyczne na platformie Microsoft Azure?)

> [!NOTE]
> W tym artykule opisano sposób tworzenia starszych alertów metryki klasycznej. Usługa Azure Monitor obsługuje teraz [nowsze alerty metryki w czasie zbliżonym do rzeczywistego i nowe środowisko alertów.](../../azure-monitor/platform/alerts-overview.md) Klasyczne alerty są [wycofane,](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)choć nadal w ograniczonym użyciu dla zasobów, które nie obsługują jeszcze nowych alertów. 
>

Alerty umożliwiają konfigurowanie warunków za wiele danych i powiadamianie, gdy warunki są zgodne z najnowszymi danymi monitorowania.

## <a name="old-and-new-alerting-capabilities"></a>Stare i nowe funkcje ostrzegania

W przeszłości usługa Azure Monitor, usługa Application Insights, log analytics i kondycja usługi miały oddzielne funkcje alertów. Nadgodziny, platforma Azure ulepszone i połączone zarówno interfejs użytkownika i różne metody alertów. Konsolidacja jest nadal w toku.

Alerty klasyczne można wyświetlać tylko na ekranie użytkownika alertów klasycznych w witrynie Azure Portal. Ten ekran zostanie wyświetlony za pomocą przycisku **Wyświetl klasyczne alerty** na ekranie alertów. 

 ![Opcje alertów w witrynie Azure portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Nowe środowisko użytkownika alertów ma następujące korzyści w przypadku klasycznych alertów:
-   **Lepszy system powiadomień** — wszystkie nowsze alerty używają grup akcji, które są nazwane grupami powiadomień i akcji, które mogą być ponownie używane w wielu alertach. Alerty klasyczne metryki i starsze alerty usługi Log Analytics nie używają grup akcji.
-   **Ujednolicone środowisko tworzenia** — wszystkie tworzenie alertów dla metryk, dzienników i dziennika aktywności w usłudze Azure Monitor, usługa Log Analytics i usługa Application Insights jest w jednym miejscu.
-   **Wyświetlanie zwolnionych alertów usługi Log Analytics w witrynie Azure portal** — teraz można również zobaczyć również zwolnione alerty usługi Log Analytics w ramach subskrypcji. Wcześniej były one w osobnym portalu.
-   **Oddzielenie uruchomionych alertów i reguł alertów** — reguły alertów (definicja warunku wyzwalającego alert) i Uruchomione alerty (wystąpienie uruchamiania reguły alertu) są zróżnicowane, więc widoki operacyjne i konfiguracyjne są oddzielone.
-   **Lepszy przepływ pracy** — nowe środowisko tworzenia alertów prowadzi użytkownika w procesie konfigurowania reguły alertów, co ułatwia odnajdywanie właściwych rzeczy, na których można uzyskać alert.
-   **Inteligentne alerty konsolidacji** i **ustawiania stanu alertów** — nowsze alerty obejmują funkcje automatycznego grupowania pokazujące podobne alerty razem w celu zmniejszenia przeciążenia w interfejsie użytkownika. 

Nowsze alerty metryki mają następujące korzyści w stosunku do alertów metryki klasycznej:
-   **Poprawiono opóźnienie:** nowsze alerty metryki mogą być uruchamiane tak często, jak co minutę. Starsze alerty metryki zawsze są uruchamiane z częstotliwością 5 minut. Nowsze alerty zwiększają mniejsze opóźnienie od wystąpienia problemu do powiadomienia lub akcji (od 3 do 5 minut). Starsze alerty są 5 do 15 minut w zależności od typu.  Alerty dziennika zazwyczaj mają 10 do 15 minut opóźnienia ze względu na czas potrzebny do pozyskiwania dzienników, ale nowsze metody przetwarzania skracają ten czas. 
-   **Obsługa metryk wielowymiarowych:** można alertować o metrykach wymiarowych, co pozwala monitorować interesujący segment metryki.
-   **Większa kontrola nad warunkami metryki:** Można zdefiniować bogatsze reguły alertów. Nowsze alerty obsługują monitorowanie maksymalnych, minimalnych, średnich i całkowitych wartości metryk.
-   **Połączone monitorowanie wielu metryk:** można monitorować wiele metryk (obecnie maksymalnie dwie metryki) za pomocą jednej reguły. Alert jest wyzwalany, jeśli obie metryki naruszają ich odpowiednie progi dla określonego okresu.
-   **Lepszy system powiadomień:** Wszystkie nowsze alerty używają [grup akcji,](../../azure-monitor/platform/action-groups.md)które są nazwane grupami powiadomień i akcji, które mogą być ponownie używane w wielu alertach.  Alerty klasyczne metryki i starsze alerty usługi Log Analytics nie używają grup akcji. 
-   **Metryki z dzienników** (publiczna wersja zapoznawcza): Rejestrowanie danych przechodzących do usługi Log Analytics można teraz wyodrębnić i przekonwertować na metryki usługi Azure Monitor, a następnie alerty na tak jak inne metryki. Zobacz [Alerty (klasyczne)](alerts-classic.overview.md) dla terminologii specyficzne dla klasycznych alertów. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasyczne alerty dotyczące danych usługi Azure Monitor
Dostępne są dwa typy alertów klasycznych — alerty metryki i alerty dziennika aktywności.

* **Alerty metryki klasycznej** — ten alert wyzwala, gdy wartość określonej metryki przekracza próg, który można przypisać. Alert generuje powiadomienie, gdy ten próg zostanie przekroczony i warunek alertu jest spełniony. W tym momencie alert jest uważany za "Aktywowany". Generuje kolejne powiadomienie, gdy jest "Rozwiązany" - to znaczy, gdy próg zostanie przekroczony ponownie i warunek nie jest już spełniony.

* **Alerty dziennika aktywności klasycznej** — alert dziennika przesyłania strumieniowego, który wyzwala wpis zdarzenia dziennik aktywności, który spełnia kryteria filtrowania. Te alerty mają tylko jeden stan "Aktywowany". Aparat alertów po prostu stosuje kryteria filtrowania do każdego nowego zdarzenia. Nie wyszukuje, aby znaleźć starsze wpisy. Te alerty mogą powiadamiać użytkownika o wystąpieniu nowego zdarzenia kondycji usługi lub gdy użytkownik lub aplikacja wykonuje operację w ramach subskrypcji, na przykład "Usuń maszynę wirtualną".

W przypadku danych dziennika zasobów dostępnych za pośrednictwem usługi Azure Monitor należy kierować dane do usługi Log Analytics i używać alertu kwerendy dziennika. Usługa Log Analytics korzysta teraz z [nowej metody alertów](../../azure-monitor/platform/alerts-overview.md) 

Na poniższym diagramie podsumowano źródła danych w usłudze Azure Monitor i, pod względem koncepcyjnym, jak można alertować o tych danych.

![Wyjaśniono alerty](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taksonomia alertów (klasyczny)
Platforma Azure używa następujących terminów do opisywania klasycznych alertów i ich funkcji:
* **Alert** — definicja kryteriów (co najmniej jedna reguła lub warunki), która staje się aktywowana po spełnieniu.
* **Aktywny** — stan, w którym spełnione są kryteria zdefiniowane przez alert klasyczny.
* **Rozwiązano** — stan, w którym kryteria zdefiniowane przez alert klasyczny nie są już spełniane po spełnieniu wcześniej.
* **Powiadomienie** - akcja podjęta na podstawie klasycznego alertu staje się aktywna.
* **Akcja** — określone wywołanie wysłane do odbiorcy powiadomienia (na przykład wysłanie adresu e-mail lub opublikowanie adresu URL elementu webhook). Powiadomienia mogą zwykle wyzwalać wiele akcji.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak otrzymać powiadomienie z klasycznego alertu usługi Azure Monitor?
Historycznie alerty platformy Azure z różnych usług używane własne wbudowane metody powiadomień. 

Usługa Azure Monitor utworzyła grupę powiadomień wielokrotnegoużytnia o nazwie *grupy akcji*. Grupy akcji określają zestaw odbiorników dla powiadomienia. Za każdym razem, gdy zostanie aktywowany alert, który odwołuje się do grupy akcji, wszyscy odbiorcy otrzymują to powiadomienie. Grupy akcji umożliwiają ponowne użycie grupowania odbiorników (na przykład listy inżynierów dyżurnych) w wielu obiektach alertów. Grupy akcji obsługują powiadomienia, publikując na adresie URL elementu webhook oprócz adresów e-mail, numerów SMS i wielu innych akcji.  Aby uzyskać więcej informacji, zobacz [grupy akcji](../../azure-monitor/platform/action-groups.md). 

Starsze alerty dziennika aktywności klasycznej używają grup akcji.

Jednak starsze alerty metryki nie używają grup akcji. Zamiast tego można skonfigurować następujące akcje: 
- Wysyłaj powiadomienia e-mail do administratora usługi, do współadministratorów lub na dodatkowe adresy e-mail, które określisz.
- Wywołanie elementu webhook, który umożliwia uruchamianie dodatkowych akcji automatyzacji.

Elementy webhooks umożliwia automatyzację i korygowanie, na przykład za pomocą:
- Element Runbook usługi Azure Automation
- Funkcja platformy Azure
- Aplikacja logiki platformy Azure
- usługa innej firmy

## <a name="next-steps"></a>Następne kroki
Uzyskaj informacje o regułach alertów i konfigurowaniu ich przy użyciu:

* Dowiedz się więcej o [metrykach](data-platform.md)
* Konfigurowanie [klasycznych alertów metryk za pośrednictwem witryny Azure portal](alerts-classic-portal.md)
* Konfigurowanie [klasycznych alertów metrycznych programu PowerShell](alerts-classic-portal.md)
* Konfigurowanie [klasycznego interfejsu wiersza polecenia alertów metrycznych (CLI)](alerts-classic-portal.md)
* Konfigurowanie [klasycznego interfejsu API interfejsu API interfejsu REST monitora azure monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Dowiedz się więcej o [Dzienniku aktywności](platform-logs-overview.md)
* Konfigurowanie [alertów dziennika aktywności za pośrednictwem witryny Azure portal](activity-log-alerts.md)
* Konfigurowanie [alertów dziennika aktywności za pośrednictwem Menedżera zasobów](alerts-activity-log.md)
* Przeglądanie [schematu programu webhook alertu dziennika aktywności](activity-log-alerts-webhook.md)
* Dowiedz się więcej o [grupach akcji](action-groups.md)
* Konfigurowanie [nowszych alertów](alerts-metric.md)
