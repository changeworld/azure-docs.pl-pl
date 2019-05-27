---
title: Przegląd alertów klasycznych na platformie Microsoft Azure i usługi Azure Monitor
description: Alertów klasycznych zostaną wycofane. Alerty umożliwiają monitorowanie zdarzeń, dzienniki lub metryk zasobów platformy Azure i otrzymywać powiadomienia, gdy spełniony jest warunek, który określisz.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2018
ms.author: robb
ms.openlocfilehash: 96183e22f0aeafc681a782e7d3d8bd29a6ec9617
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65914708"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co to są alertów klasycznych na platformie Microsoft Azure?

> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze klasycznego alertów dotyczących metryk. Platforma Azure obsługuje teraz Monitor [nowszych alertów metryk niemal w czasie rzeczywistym oraz nowe środowisko alertów](../../azure-monitor/platform/alerts-overview.md). Alerty klasyczne są [zaplanowane wkrótce zostaną wycofane](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).  
>

Alerty pozwalają na konfigurowanie warunków nad danymi i stają się powiadomienie, gdy warunki pasują do monitorowania najnowszych danych.

## <a name="old-and-new-alerting-capabilities"></a>Stare i nowe alerty możliwości

W przeszłości, usługi Azure Monitor, Application Insights, Log Analytics i kondycji usług ma oddzielne alertów. Z czasem platforma Azure ulepszone i interfejsu użytkownika i różnych metod alertów w połączeniu. Konsolidacja jest nadal w toku.

Można wyświetlić alertów klasycznych tylko na ekranie użytkownika alertów klasycznych w witrynie Azure Portal. Pobierz ten ekran z **wyświetlanie alertów klasycznych** przycisk na ekranie alertów. 

 ![Opcje alertów w witrynie Azure portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Nowe środowisko użytkownika alertów ma następujące zalety nad środowiskiem alertów klasycznych:
-   **Lepsze systemu powiadomień** -wszystkich nowszych alertów korzystanie z grup akcji, które noszą nazwy grup powiadomienia i akcje, które mogą być ponownie używane w wielu alertów. Klasyczne alertów metryk i alertów usługi Log Analytics starsze, nie należy używać grup akcji.
-   **A ujednolicone środowisko tworzenia** — wszystkich alertów tworzenia dla metryk, dzienników i działań logowania w usłudze Azure Monitor usługi Log Analytics i usługa Application Insights to w jednym miejscu.
-   **Wyświetl wyzwolone alerty usługi Log Analytics w witrynie Azure portal** — możesz teraz również znaleźć wyzwolone alerty usługi Log Analytics w ramach subskrypcji. Wcześniej były one osobnego portalu.
-   **Oddzielanie wyzwolone alerty i reguł alertów** — reguły alertów (definicja Warunek wyzwalający alert), a także alerty wyzwolone (wystąpienia uruchomieniem reguły alertu) są zróżnicowane, więc są rozdzielone widoków operacyjnych i konfiguracji.
-   **Lepszy przepływ pracy** — nowe alerty tworzenia przewodniki środowiska użytkownika wzdłuż proces konfigurowania reguły alertu, który upraszcza odnajdywanie z odpowiednimi kwestiami, aby po otrzymaniu na.
-   **Blokada Smart Konsolidacja alertów** i **ustawiania stanu alertu** -nowszych alertów obejmują automatyczne funkcja grupowania przedstawiający podobnych alertów ze sobą, aby ograniczyć przeciążenia w interfejsie użytkownika. 

Nowszych alertów metryk zapewnia następujące korzyści za pośrednictwem klasycznego alerty metryki:
-   **Ulepszone opóźnienie**: Nowszych alertów metryk, można uruchomić tak często, jak co minutę. Starsze alertów dotyczących metryk są zawsze uruchamiane z częstotliwością wynoszącą 5 minut. Nowszych alertów ma, zwiększając mniejsze opóźnienia z wystąpienia problemu, aby powiadomienia lub akcję (3 – 5 minut). Starsze alerty są 5 do 15 minut w zależności od typu.  Alerty dzienników zazwyczaj mają opóźnienia 10 do 15 - minutowy ze względu na czas potrzebny do odbierania dzienników, ale nowszej metody przetwarzania są skrócenie tego czasu. 
-   **Obsługa metryk wielowymiarowych**: Może generować alerty na metryki jednowymiarowe, dzięki czemu można monitorować segmentu interesujące metryki.
-   **Większa kontrola nad metryki warunki**: Można zdefiniować bardziej rozbudowane reguły alertu. Nowszych alertów, obsługują monitorowanie maksymalna, minimalna, średnia i łączna liczba wartości metryk.
-   **Monitorowanie wielu metryk w połączeniu**: Można monitorować wiele metryk (obecnie maksymalnie dwie metryki) przy użyciu jednej reguły. Alert jest wyzwalany, jeśli oba wskaźniki naruszenia ich odpowiednich progów w określonym przedziale czasu.
-   **Lepsze systemu powiadomień**: Użyj wszystkich nowszych alertów [grup akcji](../../azure-monitor/platform/action-groups.md), które są nazwane grupy powiadomienia i akcje, które mogą być ponownie używane w wielu alertów.  Klasyczne alertów metryk i alertów usługi Log Analytics starsze, nie należy używać grup akcji. 
-   **Metryki z dzienników** (publiczna wersja zapoznawcza): Dane dziennika przesyłane do usługi Log Analytics można teraz wyodrębnione i przekształcone w metryki usługi Azure Monitor i następnie alerty o podobnie jak inne metryki. Zobacz [alerty (klasyczne)](alerts-classic.overview.md) przeznaczonego dla terminologii specyficzne dla alertów klasycznych. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Alertów klasycznych na danych usługi Azure Monitor
Istnieją dwa typy alertów klasycznych dostępne - alertów dotyczących metryk i alertów dziennika aktywności.

* **Klasyczne alertów dotyczących metryk** — uruchamia ten alert, gdy wartość określonej metryki przekracza wartość progową, który przypiszesz. Ten alert generuje powiadomienie, po przekroczeniu progu i spełnieniu warunku alertu. W tym momencie alertu jest traktowany jako "Aktywny". Generuje on kolejne powiadomienie, gdy jej jest "rozwiązany" — oznacza to, po przekroczeniu progu ponownie i warunek nie jest już spełniany.

* **Alerty dziennika aktywności klasycznego** -przesyłania strumieniowego alert dziennika, który wyzwala przy uruchamianiu zdarzenia dziennika aktywności, odpowiadający kryteria filtru. Te alerty mają tylko jeden stan "Aktywowano". Aparat alertów po prostu dotyczy kryteria filtrowania wszelkie nowe zdarzenie. Wyszukiwanie nie jest do wyszukiwania starszych wpisów. Te alerty może generować powiadomienia po wystąpieniu nowego zdarzenia usługi Service Health, lub gdy użytkownik lub aplikacja wykonuje operację w ramach subskrypcji, na przykład, "usunięcie maszyny wirtualnej".

W przypadku dzienników diagnostycznych danych dostępne za pośrednictwem usługi Azure Monitor kierować dane do usługi Log Analytics (dawniej OMS) i użyj alertu zapytań usługi Log Analytics. Zaloguj korzysta teraz Analytics [nowych alertów — metoda](../../azure-monitor/platform/alerts-overview.md) 

Poniższy diagram przedstawia źródeł danych w usłudze Azure Monitor i, pod względem koncepcyjnym, jak może generować alerty zniżki w stosunku do tych danych.

![Wyjaśniono alertów](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taksonomia usługi alerty (klasyczne)
Platforma Azure stosuje następujące warunki do opisania alertów klasycznych i ich funkcje:
* **Alert** -definicję kryteria (jednego lub więcej reguł, jak i warunki), która staje się aktywowany, gdy spełnione.
* **Aktywne** — stan, gdy są spełnione kryteria zdefiniowane przez klasyczny alert.
* **Rozwiązane** — stan, gdy kryteria zdefiniowane przez klasyczny alert nie jest już spełniany po wcześniej spełnione.
* **Powiadomienie** — akcję wykonywaną na podstawie zniżki w stosunku do klasycznego alertu, staje się aktywny.
* **Akcja** -wywołań wysyłanych do odbiorcy powiadomienia (na przykład wysyłanie wiadomości e-mail adres lub publikowanie do adresu URL elementu webhook). Powiadomienia zwykle może wyzwolić wiele akcji.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak otrzymać powiadomienie z poziomu alertu klasycznej usługi Azure Monitor?
W przeszłości alertów platformy Azure z różnymi usługami używać własnych metod wbudowanych powiadomień. 

Usługa Azure Monitor utworzone powiadomienie wielokrotnego użytku, grupowanie o nazwie *grup akcji*. Grupy akcji określić zbiór odbiorców dla powiadomienia. Zawsze, gdy aktywowano alert odwołujący się do grupy akcji wszystkich odbiorców otrzymywać powiadomienia. Grupy akcji umożliwiają wielokrotne użycie Grupa odbiorców (na przykład listy odtwarzania na wywołanie) w odniesieniu do obiektów wiele alertów. Grupy akcji powiadomienia są obsługiwane przez publikowanie do adresu URL elementu webhook, oprócz adresy e-mail, SMS liczb i wiele innych działań.  Aby uzyskać więcej informacji, zobacz [grup akcji](../../azure-monitor/platform/action-groups.md). 

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

* Dowiedz się więcej o [metryki](data-platform.md)
* Konfigurowanie [alertów klasycznych metryki w witrynie Azure portal](alerts-classic-portal.md)
* Konfigurowanie [klasyczny program PowerShell alerty metryki](alerts-classic-portal.md)
* Konfigurowanie [klasycznego interfejsu wiersza polecenia alerty metryki (CLI)](alerts-classic-portal.md)
* Konfigurowanie [klasycznego metryki alerty usługi Azure Monitor interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Dowiedz się więcej o [dziennika aktywności](activity-logs-overview.md)
* Konfigurowanie [alertów dziennika aktywności w witrynie Azure portal](activity-log-alerts.md)
* Konfigurowanie [alertów dziennika aktywności przy użyciu usługi Resource Manager](alerts-activity-log.md)
* Przegląd [schemat elementów webhook alertu dziennika aktywności](activity-log-alerts-webhook.md)
* Dowiedz się więcej o [grupy akcji](action-groups.md)
* Konfigurowanie [nowszych alertów](alerts-metric.md)
