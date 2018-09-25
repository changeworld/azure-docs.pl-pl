---
title: Przegląd alertów klasycznych na platformie Microsoft Azure i usługi Azure Monitor
description: Alertów klasycznych zostaną wycofane. Alerty umożliwiają monitorowanie zdarzeń, dzienniki lub metryk zasobów platformy Azure i otrzymywać powiadomienia, gdy spełniony jest warunek, który określisz.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 7046a0c6ac84ad5f156098a26dcef2b8accd50af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987649"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co to są alertów klasycznych na platformie Microsoft Azure?

> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze klasycznego alertów dotyczących metryk. Platforma Azure obsługuje teraz Monitor [nowszych alertów metryk niemal w czasie rzeczywistym oraz nowe środowisko alertów](monitoring-overview-alerts.md). 
>

Alerty pozwalają na konfigurowanie warunków nad danymi i stają się powiadomienie, gdy warunki pasują do monitorowania najnowszych danych.

## <a name="old-and-new-alerting-capabilities"></a>Stare i nowe alerty możliwości

W ciągu ostatnich usługi Azure Monitor usługi Application Insights, Log Analytics i kondycji usług ma oddzielne możliwości alertów. Z czasem platforma Azure ulepszone i interfejsu użytkownika i różnych metod alertów w połączeniu. Konsolidacja jest nadal w toku. Alerty

Można wyświetlić alertów klasycznych tylko na ekranie użytkownika alertów klasycznych w witrynie Azure Portal. Pobierz ten ekran z **wyświetlanie alertów klasycznych** przycisk na ekranie alertów. 

 ![Opcje alertów w witrynie Azure portal](./media/monitoring-overview-alerts-classic/monitor-alert-screen2.png) 

Nowe środowisko użytkownika alertów ma następujące zalety nad środowiskiem alertów klasycznych:
-   **Lepsze systemu powiadomień** -wszystkich nowszych alertów korzystanie z grup akcji, które noszą nazwy grup powiadomienia i akcje, które mogą być ponownie używane w wielu alertów. Klasyczne alertów metryk i alertów usługi Log Analytics starsze, nie należy używać grup akcji.
-   **A ujednolicone środowisko tworzenia** — wszystkich alertów tworzenia dla metryk, dzienników i działań logowania w usłudze Azure Monitor usługi Log Analytics i usługa Application Insights to w jednym miejscu.
-   **Wyświetl wyzwolone alerty usługi Log Analytics w witrynie Azure portal** — możesz teraz również znaleźć wyzwolone alerty usługi Log Analytics w ramach subskrypcji. Wcześniej były one osobnego portalu.
-   **Oddzielanie wyzwolone alerty i reguł alertów** — reguły alertów (definicja Warunek wyzwalający alert), a także alerty wyzwolone (wystąpienia uruchomieniem reguły alertu) są zróżnicowane, więc są rozdzielone widoków operacyjnych i konfiguracji.
-   **Lepszy przepływ pracy** — nowe alerty tworzenia przewodniki środowiska użytkownika wzdłuż proces konfigurowania reguły alertu, który upraszcza odnajdywanie z odpowiednimi kwestiami, aby po otrzymaniu na.
-   **Blokada Smart Konsolidacja alertów** i **ustawiania stanu alertu** -nowszych alertów obejmują automatyczne funkcja grupowania przedstawiający podobnych alertów ze sobą, aby ograniczyć przeciążenia w interfejsie użytkownika. 

Nowszych alertów metryk zapewnia następujące korzyści za pośrednictwem klasycznego alerty metryki:
-   **Ulepszone opóźnienie**: nowszych alertów metryk, można uruchomić tak często, jak co minutę. Starsze alertów dotyczących metryk są zawsze uruchamiane z częstotliwością wynoszącą 5 minut. Nowszych alertów ma, zwiększając mniejsze opóźnienia z wystąpienia problemu, aby powiadomienia lub akcję (3 – 5 minut). Starsze alerty są 5 do 15 minut w zależności od typu.  Alerty dzienników zazwyczaj mają 10 do 15 minut opóźnienia z powodu czasu jest potrzebnych do pozyskiwania w dziennikach, ale nowszych metody przetwarzania jest zmniejszenie czasu. 
-   **Obsługa metryk wielowymiarowych**: może generować alerty na metryki jednowymiarowe, dzięki czemu można monitorować segmentu interesujące metryki.
-   **Większa kontrola nad metryki warunki**: można zdefiniować bardziej rozbudowane reguły alertu. Nowszych alertów, obsługują monitorowanie maksymalna, minimalna, średnia i łączna liczba wartości metryk.
-   **Monitorowanie wielu metryk w połączeniu**: można monitorować wiele metryk (obecnie maksymalnie dwie metryki) przy użyciu jednej reguły. Alert jest wyzwalany, jeśli oba wskaźniki naruszenia ich odpowiednich progów w określonym przedziale czasu.
-   **Lepsze systemu powiadomień**: Użyj wszystkich nowszych alertów [grup akcji](../monitoring-and-diagnostics/monitoring-action-groups.md), które są nazwane grupy powiadomienia i akcje, które mogą być ponownie używane w wielu alertów.  Klasyczne alertów metryk i alertów usługi Log Analytics starsze, nie należy używać grup akcji. 
-   **Metryki z dzienników** (publiczna wersja zapoznawcza): dziennika dane przesyłane do usługi Log Analytics mogą teraz być wyodrębnione i przekształcone w metryki usługi Azure Monitor i następnie alerty na podobnie jak inne metryki. Zobacz [alerty (klasyczne)](monitoring-overview-alerts-classic.md) przeznaczonego dla terminologii specyficzne dla alertów klasycznych. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Alertów klasycznych na danych usługi Azure Monitor
Istnieją dwa typy alertów klasycznych dostępne - alertów dotyczących metryk i alertów dziennika aktywności.

* **Klasyczne alertów dotyczących metryk** — uruchamia ten alert, gdy wartość określonej metryki przekracza wartość progową, który przypiszesz. Ten alert generuje powiadomienie, gdy alert jest "aktywny" (po przekroczeniu progu, i jest spełniony warunek alertu). Generuje on kolejne powiadomienie, gdy jej jest "rozwiązany" (po przekroczeniu progu ponownie i warunek przestaje być spełniany).

* **Alerty dziennika aktywności klasycznego** -przesyłania strumieniowego alertu dziennika, która wyzwala po wygenerowaniu zdarzenia dziennika aktywności, że dopasowania filtrowania kryteria, które zostały przypisane. Te alerty mają tylko jeden stan "Aktywowano", ponieważ aparat alertów po prostu dotyczy kryteria filtrowania wszelkie nowe zdarzenie. Te alerty mogą służyć do otrzymywania powiadomień w przypadku nowego zdarzenia usługi Service Health, lub gdy użytkownik lub aplikacja wykonuje operację w ramach subskrypcji, na przykład "Usuń maszynę wirtualną".

W przypadku dzienników diagnostycznych danych dostępne za pośrednictwem usługi Azure Monitor kierować dane do usługi Log Analytics (dawniej OMS) i użyj alertu zapytań usługi Log Analytics. Zaloguj korzysta teraz Analytics [nowych alertów — metoda](monitoring-overview-unified-alerts.md) 

Poniższy diagram przedstawia źródeł danych w usłudze Azure Monitor i, pod względem koncepcyjnym, jak może generować alerty zniżki w stosunku do tych danych.

![Wyjaśniono alertów](./media/monitoring-overview-alerts-classic/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taksonomia usługi alerty (klasyczne)
Platforma Azure stosuje następujące warunki do opisania alertów klasycznych i ich funkcje:
* **Alert** -definicję kryteria (jednego lub więcej reguł, jak i warunki), która staje się aktywowany, gdy spełnione.
* **Aktywne** — stan, gdy są spełnione kryteria zdefiniowane przez klasyczny alert.
* **Rozwiązane** — stan, gdy kryteria zdefiniowane przez klasyczny alert nie jest już spełniany po wcześniej spełnione.
* **Powiadomienie** — akcję wykonywaną na podstawie zniżki w stosunku do klasycznego alertu, staje się aktywny.
* **Akcja** -wywołań wysyłanych do odbiorcy powiadomienia (na przykład wysyłanie wiadomości e-mail adres lub publikowanie do adresu URL elementu webhook). Powiadomienia zwykle może wyzwolić wiele akcji.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak otrzymać powiadomienie z poziomu alertu klasycznej usługi Azure Monitor?
W przeszłości alertów platformy Azure z różnymi usługami używać własnych metod wbudowanych powiadomień. 

Usługa Azure Monitor utworzone powiadomienie wielokrotnego użytku, grupowanie o nazwie *grup akcji*. Grupy akcji określić zbiór odbiorców dla powiadomienia i każdym razem, gdy aktywowano alert odwołujący się do grupy akcji wszystkich odbiorniki odbierania powiadomienia. Grupy akcji pozwala na ponowne użycie Grupa odbiorców (na przykład listy odtwarzania na wywołanie) w odniesieniu do obiektów wiele alertów. Grupy akcji powiadomienia są obsługiwane przez publikowanie do adresu URL elementu webhook, oprócz adresy e-mail, SMS liczb i wiele innych działań.  Aby uzyskać więcej informacji, zobacz [grup akcji](monitoring-action-groups.md). 

Starsze alertów dziennika aktywności klasycznych korzystanie z grup akcji.

Jednak starsze alertów metryk, nie należy używać grup akcji. Zamiast tego można skonfigurować następujące akcje: 
- Wysyłanie powiadomień e-mail do administratora usługi, współadministratorów lub adresy e-mail dodatkowych, które określisz.
- Wywołaj element webhook, co pozwala na uruchamianie automatyzacji dodatkowej akcji.

Elementy Webhook umożliwia automatyzację i korygowania, na przykład przy użyciu:
    - Element Runbook usługi Azure Automation
    - Funkcja platformy Azure
    - Aplikacji logiki platformy Azure
    - Usługa innej firmy

## <a name="next-steps"></a>Kolejne kroki
Uzyskać informacje na temat reguł alertów i konfigurowanie ich za pomocą:

* Dowiedz się więcej o [metryki](monitoring-overview-metrics.md)
* Konfigurowanie [alertów klasycznych metryki w witrynie Azure portal](insights-alerts-portal.md)
* Konfigurowanie [klasyczny program PowerShell alerty metryki](insights-alerts-powershell.md)
* Konfigurowanie [klasycznego interfejsu wiersza polecenia alerty metryki (CLI)](insights-alerts-command-line-interface.md)
* Konfigurowanie [klasycznego metryki alerty usługi Azure Monitor interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Dowiedz się więcej o [dziennika aktywności](monitoring-overview-activity-logs.md)
* Konfigurowanie [alertów dziennika aktywności w witrynie Azure portal](monitoring-activity-log-alerts.md)
* Konfigurowanie [alertów dziennika aktywności przy użyciu usługi Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Przegląd [schemat elementów webhook alertu dziennika aktywności](monitoring-activity-log-alerts-webhook.md)
* Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md)
* Konfigurowanie [nowszych alertów](monitor-alerts-unified-usage.md)